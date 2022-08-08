+++
categories = ["Software","k8s","EN"]
date = "2019-08-25T19:00:00+03:00"
title = "How does 'kubectl exec' work?"
keywords = ["Software", "k8s", "Kubernetes"]
weight = 1
+++

Last Friday, one of my colleagues approached me and asked a question about how to exec a command in a pod with client-go. I didn't know the answer and I noticed that I had never thought about the mechanism in "kubectl exec". I had some ideas about how it should be, but I wasn't 100% sure. I noted the topic to check again and I have learnt a lot after reading some blogs, docs and source codes. In this blog post, I am going to share my understanding and findings. 

> Please ping me if there is something wrong. https://twitter.com/erkan_erol_

<!--more-->

## Setup

I cloned https://github.com/ecomm-integration-ballerina/kubernetes-cluster in order to create a k8s cluster in my MacBook. I fixed IP addresses of the nodes in kubelet configurations since the default configuration didn't let me run <b>kubectl exec</b>. You can find the root cause [here](https://medium.com/@joatmon08/playing-with-kubeadm-in-vagrant-machines-part-2-bac431095706).

* Any machine = my MacBook
* IP of master node = 192.168.205.10
* IP of worker node = 192.168.205.11
* API server port = 6443


<br>

## Components

![Components](/img/kubectl-exec/components.png)

- ***kubectl exec process:*** When we run "kubectl exec ..." in a machine, a process starts. You can run it in any machine which has an access to k8s api server.

- ***[api server](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver):*** Component on the master that exposes the Kubernetes API. It is the front-end for the Kubernetes control plane.

- ***[kubelet](https://kubernetes.io/docs/concepts/overview/components/#kubelet):*** An agent that runs on each node in the cluster. It makes sure that containers are running in a pod.

- ***[container runtime](https://kubernetes.io/docs/concepts/overview/components/#container-runtime):*** The software that is responsible for running containers. Examples: docker, cri-o, containerd...

- ***kernel:*** kernel of the OS in the worker node which is responsible to manage processes.
 
- ***target container:*** A container which is a part of a pod and which is running on one of the worker nodes.

<br><br>

## Findings

### 1. Activities in Client Side

- Create a pod in default namespace
```bash
// any machine
$ kubectl run exec-test-nginx --image=nginx
```

- Then run an exec command and <b>sleep 5000</b> to make observation
```bash
// any machine
$ kubectl exec -it exec-test-nginx-6558988d5-fgxgg -- sh
# sleep 5000
```

- We can observe the kubectl process (pid=8507 in this case)
```bash
// any machine
$ ps -ef |grep kubectl
501  8507  8409   0  7:19PM ttys000    0:00.13 kubectl exec -it exec-test-nginx-6558988d5-fgxgg -- sh
```

- When we check network activities of the process, we can see that it has some connections to api-server (192.168.205.10.6443)
```bash
// any machine
$ netstat -atnv |grep 8507
tcp4       0      0  192.168.205.1.51673    192.168.205.10.6443    ESTABLISHED 131072 131768   8507      0 0x0102 0x00000020
tcp4       0      0  192.168.205.1.51672    192.168.205.10.6443    ESTABLISHED 131072 131768   8507      0 0x0102 0x00000028
```

- Let's check the code. kubectl creates a POST request with subresource <b>exec</b> and sends a rest request.
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/staging/src/k8s.io/kubectl/pkg/cmd/exec/exec.go#L344-L359
  req := restClient.Post().
    Resource("pods").
    Name(pod.Name).
    Namespace(pod.Namespace).
    SubResource("exec")
  req.VersionedParams(&corev1.PodExecOptions{
    Container: containerName,
    Command:   p.Command,
    Stdin:     p.Stdin,
    Stdout:    p.Out != nil,
    Stderr:    p.ErrOut != nil,
    TTY:       t.Raw,
  }, scheme.ParameterCodec)

  return p.Executor.Execute("POST", req.URL(), p.Config, p.In, p.Out, p.ErrOut, t.Raw, sizeQueue)
  ```
![rest-request](/img/kubectl-exec/rest-request.png)


<br>

### 2. Activities in Master Node

- We can observe the request in api-server side as well.

  ```
  handler.go:143] kube-apiserver: POST "/api/v1/namespaces/default/pods/exec-test-nginx-6558988d5-fgxgg/exec" satisfied by gorestful with webservice /api/v1
  upgradeaware.go:261] Connecting to backend proxy (intercepting redirects) https://192.168.205.11:10250/exec/default/exec-test-nginx-6558988d5-fgxgg/exec-test-nginx?command=sh&input=1&output=1&tty=1
  Headers: map[Connection:[Upgrade] Content-Length:[0] Upgrade:[SPDY/3.1] User-Agent:[kubectl/v1.12.10 (darwin/amd64) kubernetes/e3c1340] X-Forwarded-For:[192.168.205.1] X-Stream-Protocol-Version:[v4.channel.k8s.io v3.channel.k8s.io v2.channel.k8s.io channel.k8s.io]]
  ```
  > Notice that the http request includes a protocol upgrade request. [SPDY](https://www.wikiwand.com/en/SPDY) allows for separate stdin/stdout/stderr/spdy-error "streams" to be multiplexed over a single TCP connection.

- Api server receives the request and binds it into a <b>PodExecOptions</b>
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/apis/core/types.go#L4124-L4147
  // PodExecOptions is the query options to a Pod's remote exec call
  type PodExecOptions struct {
    metav1.TypeMeta
    // Stdin if true indicates that stdin is to be redirected for the exec call
    Stdin bool
    // Stdout if true indicates that stdout is to be redirected for the exec call
    Stdout bool
    // Stderr if true indicates that stderr is to be redirected for the exec call
    Stderr bool
    // TTY if true indicates that a tty will be allocated for the exec call
    TTY bool
    // Container in which to execute the command.
    Container string
    // Command is the remote command to execute; argv array; not executed within a shell.
    Command []string
  }
  ```

- To be able to take necessary actions, api-server needs to know which location it should contact. 
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/registry/core/pod/strategy.go#L455-L467
  // ExecLocation returns the exec URL for a pod container. If opts.Container is blank
  // and only one container is present in the pod, that container is used.
  func ExecLocation(
    getter ResourceGetter,
    connInfo client.ConnectionInfoGetter,
    ctx context.Context,
    name string,
    opts *api.PodExecOptions,
  ) (*url.URL, http.RoundTripper, error) {
    return streamLocation(getter, connInfo, ctx, name, opts, opts.Container, "exec")
  }
  ```

  Of course the endpoint is derived from node info.
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/registry/core/pod/strategy.go#L504-L510
  nodeName := types.NodeName(pod.Spec.NodeName)
	if len(nodeName) == 0 {
		// If pod has not been assigned a host, return an empty location
		return nil, nil, errors.NewBadRequest(fmt.Sprintf("pod %s does not have a host assigned", name))
	}
	nodeInfo, err := connInfo.GetConnectionInfo(ctx, nodeName)
  ```

  GOTCHA! KUBELET HAS A PORT (<b>node.Status.DaemonEndpoints.KubeletEndpoint.Port</b>) TO WHICH API-SERVER CAN CONNECT.
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/kubelet/client/kubelet_client.go#L180-L206
  // GetConnectionInfo retrieves connection info from the status of a Node API object.
  func (k *NodeConnectionInfoGetter) GetConnectionInfo(ctx context.Context, nodeName types.NodeName) (*ConnectionInfo, error) {
    node, err := k.nodes.Get(ctx, string(nodeName), metav1.GetOptions{})
    if err != nil {
      return nil, err
    }

    // Find a kubelet-reported address, using preferred address type
    host, err := nodeutil.GetPreferredNodeAddress(node, k.preferredAddressTypes)
    if err != nil {
      return nil, err
    }

    // Use the kubelet-reported port, if present
    port := int(node.Status.DaemonEndpoints.KubeletEndpoint.Port)
    if port <= 0 {
      port = k.defaultPort
    }

    return &ConnectionInfo{
      Scheme:    k.scheme,
      Hostname:  host,
      Port:      strconv.Itoa(port),
      Transport: k.transport,
    }, nil
  }
  ```


    > [Communication between Nodes and the Control Plane > API server to kubelet](https://kubernetes.io/docs/concepts/architecture/control-plane-node-communication/#api-server-to-kubelet): *"These connections terminate at the kubelet’s HTTPS endpoint. By default, the apiserver does not verify the kubelet’s serving certificate, which makes the connection subject to man-in-the-middle attacks, and <b>unsafe</b> to run over untrusted and/or public networks."*

- Now, api server knows the endpoint and it opens a connections. 
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/registry/core/pod/rest/subresources.go#L132-L144
  // Connect returns a handler for the pod exec proxy
  func (r *ExecREST) Connect(ctx context.Context, name string, opts runtime.Object, responder rest.Responder) (http.Handler, error) {
    execOpts, ok := opts.(*api.PodExecOptions)
    if !ok {
      return nil, fmt.Errorf("invalid options object: %#v", opts)
    }
    location, transport, err := pod.ExecLocation(r.Store, r.KubeletConn, ctx, name, execOpts)
    if err != nil {
      return nil, err
    }
    return newThrottledUpgradeAwareProxyHandler(location, transport, false, true, true, responder), nil
  }
  ```
- Let's check what is going on the master node.

  First, learn the ip of the worker node. It is <b>192.168.205.11</b> in this case.
  ```bash
  // any machine
  $ kubectl get nodes k8s-node-1 -o wide
  NAME         STATUS   ROLES    AGE   VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
  k8s-node-1   Ready    <none>   9h    v1.15.3   192.168.205.11   <none>        Ubuntu 16.04.6 LTS   4.4.0-159-generic   docker://17.3.3
  ```

  Then get the kubelet port. It is <b>10250</b> in this case.
  ```bash
  // any machine
  $ kubectl get nodes k8s-node-1 -o jsonpath='{.status.daemonEndpoints.kubeletEndpoint}'
  map[Port:10250]
  ```

  Then check the network. Is there a connection to worker node(192.168.205.11)? THE CONNECTİON IS THERE. When I kill the exec process, it disappears so I know it is set by api-server because of my exec command.
  ```bash
  // master node
  $ netstat -atn |grep 192.168.205.11
  tcp        0      0 192.168.205.10:37870    192.168.205.11:10250    ESTABLISHED
  ...
  ```

![api-server-to-kubelet](/img/kubectl-exec/api-server-to-kubelet.png)

- Now the connection between kubectl and api-server is still open and there is another connection between api-server and kubelet. 

<br><br>

### 3. Activities in Worker Node

- Let's continue by connecting to the worker node and checking what is going on the worker node.

  First, we can observe the connection here as well. The second line. <b>192.168.205.10</b> is the IP of master node.
  ```
  // worker node
  $ netstat -atn |grep 10250
  tcp6       0      0 :::10250                :::*                    LISTEN
  tcp6       0      0 192.168.205.11:10250    192.168.205.10:37870    ESTABLISHED
  ```

  What about our sleep command? HOORAYYYY!! OUR COMMAND IS THERE!!!!
  ```
  // worker node
  $ ps -afx
  ...
  31463 ?        Sl     0:00      \_ docker-containerd-shim 7d974065bbb3107074ce31c51f5ef40aea8dcd535ae11a7b8f2dd180b8ed583a /var/run/docker/libcontainerd/7d974065bbb3107074ce31c51
  31478 pts/0    Ss     0:00          \_ sh
  31485 pts/0    S+     0:00              \_ sleep 5000
  ...
  ```
  
- Wait! How did kubelet do it?
  
- kubelet has a daemon which serves an api over a port for api-server requests. 
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/33081c1f07be128a89441a39c467b7ea2221b23d/pkg/kubelet/server/streaming/server.go#L41-L60
  // Server is the library interface to serve the stream requests.
  type Server interface {
    http.Handler

    // Get the serving URL for the requests.
    // Requests must not be nil. Responses may be nil iff an error is returned.
    GetExec(*runtimeapi.ExecRequest) (*runtimeapi.ExecResponse, error)
    GetAttach(req *runtimeapi.AttachRequest) (*runtimeapi.AttachResponse, error)
    GetPortForward(*runtimeapi.PortForwardRequest) (*runtimeapi.PortForwardResponse, error)

    // Start the server.
    // addr is the address to serve on (address:port) stayUp indicates whether the server should
    // listen until Stop() is called, or automatically stop after all expected connections are
    // closed. Calling Get{Exec,Attach,PortForward} increments the expected connection count.
    // Function does not return until the server is stopped.
    Start(stayUp bool) error
    // Stop the server, and terminate any open connections.
    Stop() error
  }
  ```


- kubelet computes a response endpoint for exec requests.
  ```go
  //source: https://github.com/kubernetes/kubernetes/blob/33081c1f07be128a89441a39c467b7ea2221b23d/pkg/kubelet/server/streaming/server.go#L178-L190
  func (s *server) GetExec(req *runtimeapi.ExecRequest) (*runtimeapi.ExecResponse, error) {
    if err := validateExecRequest(req); err != nil {
      return nil, err
    }
    token, err := s.cache.Insert(req)
    if err != nil {
      return nil, err
    }
    return &runtimeapi.ExecResponse{
      Url: s.buildURL("exec", token),
    }, nil
  }
  ```
  
  Don't confuse. It doesn't return the result of the command. It returns an endpoint for communication. 
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/6568325ca2bef519e5c8228cd33887660b5ed7b0/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go#L4534-L4540
  type ExecResponse struct {
    // Fully qualified URL of the exec streaming server.
    Url                  string   `protobuf:"bytes,1,opt,name=url,proto3" json:"url,omitempty"`
    XXX_NoUnkeyedLiteral struct{} `json:"-"`
    XXX_sizecache        int32    `json:"-"`
  }
  ```
  
  kubelet implements <b>RuntimeServiceClient</b> interface which is part of Container Runtime Interface.
  ```go
  //source: https://github.com/kubernetes/kubernetes/blob/6568325ca2bef519e5c8228cd33887660b5ed7b0/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go#L7158-L7229
  type RuntimeServiceClient interface {
    // Version returns the runtime name, runtime version, and runtime API version.
    Version(ctx context.Context, in *VersionRequest, opts ...grpc.CallOption) (*VersionResponse, error)
    // RunPodSandbox creates and starts a pod-level sandbox. Runtimes must ensure
    // the sandbox is in the ready state on success.
    RunPodSandbox(ctx context.Context, in *RunPodSandboxRequest, opts ...grpc.CallOption) (*RunPodSandboxResponse, error)
    // StopPodSandbox stops any running process that is part of the sandbox and
    // reclaims network resources (e.g., IP addresses) allocated to the sandbox.
    // If there are any running containers in the sandbox, they must be forcibly
    // terminated.
    // This call is idempotent, and must not return an error if all relevant
    // resources have already been reclaimed. kubelet will call StopPodSandbox
    // at least once before calling RemovePodSandbox. It will also attempt to
    // reclaim resources eagerly, as soon as a sandbox is not needed. Hence,
    // multiple StopPodSandbox calls are expected.
    StopPodSandbox(ctx context.Context, in *StopPodSandboxRequest, opts ...grpc.CallOption) (*StopPodSandboxResponse, error)
    // RemovePodSandbox removes the sandbox. If there are any running containers
    // in the sandbox, they must be forcibly terminated and removed.
    // This call is idempotent, and must not return an error if the sandbox has
    // already been removed.
    RemovePodSandbox(ctx context.Context, in *RemovePodSandboxRequest, opts ...grpc.CallOption) (*RemovePodSandboxResponse, error)
    // PodSandboxStatus returns the status of the PodSandbox. If the PodSandbox is not
    // present, returns an error.
    PodSandboxStatus(ctx context.Context, in *PodSandboxStatusRequest, opts ...grpc.CallOption) (*PodSandboxStatusResponse, error)
    // ListPodSandbox returns a list of PodSandboxes.
    ListPodSandbox(ctx context.Context, in *ListPodSandboxRequest, opts ...grpc.CallOption) (*ListPodSandboxResponse, error)
    // CreateContainer creates a new container in specified PodSandbox
    CreateContainer(ctx context.Context, in *CreateContainerRequest, opts ...grpc.CallOption) (*CreateContainerResponse, error)
    // StartContainer starts the container.
    StartContainer(ctx context.Context, in *StartContainerRequest, opts ...grpc.CallOption) (*StartContainerResponse, error)
    // StopContainer stops a running container with a grace period (i.e., timeout).
    // This call is idempotent, and must not return an error if the container has
    // already been stopped.
    // TODO: what must the runtime do after the grace period is reached?
    StopContainer(ctx context.Context, in *StopContainerRequest, opts ...grpc.CallOption) (*StopContainerResponse, error)
    // RemoveContainer removes the container. If the container is running, the
    // container must be forcibly removed.
    // This call is idempotent, and must not return an error if the container has
    // already been removed.
    RemoveContainer(ctx context.Context, in *RemoveContainerRequest, opts ...grpc.CallOption) (*RemoveContainerResponse, error)
    // ListContainers lists all containers by filters.
    ListContainers(ctx context.Context, in *ListContainersRequest, opts ...grpc.CallOption) (*ListContainersResponse, error)
    // ContainerStatus returns status of the container. If the container is not
    // present, returns an error.
    ContainerStatus(ctx context.Context, in *ContainerStatusRequest, opts ...grpc.CallOption) (*ContainerStatusResponse, error)
    // UpdateContainerResources updates ContainerConfig of the container.
    UpdateContainerResources(ctx context.Context, in *UpdateContainerResourcesRequest, opts ...grpc.CallOption) (*UpdateContainerResourcesResponse, error)
    // ReopenContainerLog asks runtime to reopen the stdout/stderr log file
    // for the container. This is often called after the log file has been
    // rotated. If the container is not running, container runtime can choose
    // to either create a new log file and return nil, or return an error.
    // Once it returns error, new container log file MUST NOT be created.
    ReopenContainerLog(ctx context.Context, in *ReopenContainerLogRequest, opts ...grpc.CallOption) (*ReopenContainerLogResponse, error)
    // ExecSync runs a command in a container synchronously.
    ExecSync(ctx context.Context, in *ExecSyncRequest, opts ...grpc.CallOption) (*ExecSyncResponse, error)
    // Exec prepares a streaming endpoint to execute a command in the container.
    Exec(ctx context.Context, in *ExecRequest, opts ...grpc.CallOption) (*ExecResponse, error)
    // Attach prepares a streaming endpoint to attach to a running container.
    Attach(ctx context.Context, in *AttachRequest, opts ...grpc.CallOption) (*AttachResponse, error)
    // PortForward prepares a streaming endpoint to forward ports from a PodSandbox.
    PortForward(ctx context.Context, in *PortForwardRequest, opts ...grpc.CallOption) (*PortForwardResponse, error)
    // ContainerStats returns stats of the container. If the container does not
    // exist, the call returns an error.
    ContainerStats(ctx context.Context, in *ContainerStatsRequest, opts ...grpc.CallOption) (*ContainerStatsResponse, error)
    // ListContainerStats returns stats of all running containers.
    ListContainerStats(ctx context.Context, in *ListContainerStatsRequest, opts ...grpc.CallOption) (*ListContainerStatsResponse, error)
    // UpdateRuntimeConfig updates the runtime configuration based on the given request.
    UpdateRuntimeConfig(ctx context.Context, in *UpdateRuntimeConfigRequest, opts ...grpc.CallOption) (*UpdateRuntimeConfigResponse, error)
    // Status returns the status of the runtime.
    Status(ctx context.Context, in *StatusRequest, opts ...grpc.CallOption) (*StatusResponse, error)
  } 
  ```

  It just uses gRPC to invoke a method through Container Runtime Interface.
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/6568325ca2bef519e5c8228cd33887660b5ed7b0/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go#L7230-L7233
  type runtimeServiceClient struct {
    cc *grpc.ClientConn
  }
  ```

  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/6568325ca2bef519e5c8228cd33887660b5ed7b0/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go#L7374-L7381
  func (c *runtimeServiceClient) Exec(ctx context.Context, in *ExecRequest, opts ...grpc.CallOption) (*ExecResponse, error) {
    out := new(ExecResponse)
    err := c.cc.Invoke(ctx, "/runtime.v1alpha2.RuntimeService/Exec", in, out, opts...)
    if err != nil {
      return nil, err
    }
    return out, nil
  }
  ```

  Container Runtime is responsible to implement <b>RuntimeServiceServer</b>
  ```go
  // source: https://github.com/kubernetes/kubernetes/blob/6568325ca2bef519e5c8228cd33887660b5ed7b0/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go#L7436-L7508
  // RuntimeServiceServer is the server API for RuntimeService service.
  type RuntimeServiceServer interface {
    // Version returns the runtime name, runtime version, and runtime API version.
    Version(context.Context, *VersionRequest) (*VersionResponse, error)
    // RunPodSandbox creates and starts a pod-level sandbox. Runtimes must ensure
    // the sandbox is in the ready state on success.
    RunPodSandbox(context.Context, *RunPodSandboxRequest) (*RunPodSandboxResponse, error)
    // StopPodSandbox stops any running process that is part of the sandbox and
    // reclaims network resources (e.g., IP addresses) allocated to the sandbox.
    // If there are any running containers in the sandbox, they must be forcibly
    // terminated.
    // This call is idempotent, and must not return an error if all relevant
    // resources have already been reclaimed. kubelet will call StopPodSandbox
    // at least once before calling RemovePodSandbox. It will also attempt to
    // reclaim resources eagerly, as soon as a sandbox is not needed. Hence,
    // multiple StopPodSandbox calls are expected.
    StopPodSandbox(context.Context, *StopPodSandboxRequest) (*StopPodSandboxResponse, error)
    // RemovePodSandbox removes the sandbox. If there are any running containers
    // in the sandbox, they must be forcibly terminated and removed.
    // This call is idempotent, and must not return an error if the sandbox has
    // already been removed.
    RemovePodSandbox(context.Context, *RemovePodSandboxRequest) (*RemovePodSandboxResponse, error)
    // PodSandboxStatus returns the status of the PodSandbox. If the PodSandbox is not
    // present, returns an error.
    PodSandboxStatus(context.Context, *PodSandboxStatusRequest) (*PodSandboxStatusResponse, error)
    // ListPodSandbox returns a list of PodSandboxes.
    ListPodSandbox(context.Context, *ListPodSandboxRequest) (*ListPodSandboxResponse, error)
    // CreateContainer creates a new container in specified PodSandbox
    CreateContainer(context.Context, *CreateContainerRequest) (*CreateContainerResponse, error)
    // StartContainer starts the container.
    StartContainer(context.Context, *StartContainerRequest) (*StartContainerResponse, error)
    // StopContainer stops a running container with a grace period (i.e., timeout).
    // This call is idempotent, and must not return an error if the container has
    // already been stopped.
    // TODO: what must the runtime do after the grace period is reached?
    StopContainer(context.Context, *StopContainerRequest) (*StopContainerResponse, error)
    // RemoveContainer removes the container. If the container is running, the
    // container must be forcibly removed.
    // This call is idempotent, and must not return an error if the container has
    // already been removed.
    RemoveContainer(context.Context, *RemoveContainerRequest) (*RemoveContainerResponse, error)
    // ListContainers lists all containers by filters.
    ListContainers(context.Context, *ListContainersRequest) (*ListContainersResponse, error)
    // ContainerStatus returns status of the container. If the container is not
    // present, returns an error.
    ContainerStatus(context.Context, *ContainerStatusRequest) (*ContainerStatusResponse, error)
    // UpdateContainerResources updates ContainerConfig of the container.
    UpdateContainerResources(context.Context, *UpdateContainerResourcesRequest) (*UpdateContainerResourcesResponse, error)
    // ReopenContainerLog asks runtime to reopen the stdout/stderr log file
    // for the container. This is often called after the log file has been
    // rotated. If the container is not running, container runtime can choose
    // to either create a new log file and return nil, or return an error.
    // Once it returns error, new container log file MUST NOT be created.
    ReopenContainerLog(context.Context, *ReopenContainerLogRequest) (*ReopenContainerLogResponse, error)
    // ExecSync runs a command in a container synchronously.
    ExecSync(context.Context, *ExecSyncRequest) (*ExecSyncResponse, error)
    // Exec prepares a streaming endpoint to execute a command in the container.
    Exec(context.Context, *ExecRequest) (*ExecResponse, error)
    // Attach prepares a streaming endpoint to attach to a running container.
    Attach(context.Context, *AttachRequest) (*AttachResponse, error)
    // PortForward prepares a streaming endpoint to forward ports from a PodSandbox.
    PortForward(context.Context, *PortForwardRequest) (*PortForwardResponse, error)
    // ContainerStats returns stats of the container. If the container does not
    // exist, the call returns an error.
    ContainerStats(context.Context, *ContainerStatsRequest) (*ContainerStatsResponse, error)
    // ListContainerStats returns stats of all running containers.
    ListContainerStats(context.Context, *ListContainerStatsRequest) (*ListContainerStatsResponse, error)
    // UpdateRuntimeConfig updates the runtime configuration based on the given request.
    UpdateRuntimeConfig(context.Context, *UpdateRuntimeConfigRequest) (*UpdateRuntimeConfigResponse, error)
    // Status returns the status of the runtime.
    Status(context.Context, *StatusRequest) (*StatusResponse, error)
  }
  ```

![kubelet-to-container-runtime](/img/kubectl-exec/kubelet-to-container-runtime.png)


- If it is so, we need to observe a connection between kubelet and container runtime. Right? Let's check.

Run this command before and after running exec command and check the diff. This one is the diff in my case.
```
// worker node
$ ss -a -p |grep kubelet
...
u_str  ESTAB      0      0       * 157937                * 157387                users:(("kubelet",pid=5714,fd=33))
...
```

Hımmm. There is a new connection via unix sockets between kubelet(pid=5714) and something. Who can be? YES. IT IS DOCKER(pid=1186).
```
// worker node
$ ss -a -p |grep 157387
...
u_str  ESTAB      0      0       * 157937                * 157387                users:(("kubelet",pid=5714,fd=33))
u_str  ESTAB      0      0      /var/run/docker.sock 157387                * 157937                users:(("dockerd",pid=1186,fd=14))
...
```

Remember. This is the docker daemon process(pid=1186) which runs our command.
```
// worker node.
$ ps -afx
...
 1186 ?        Ssl    0:55 /usr/bin/dockerd -H fd://
17784 ?        Sl     0:00      \_ docker-containerd-shim 53a0a08547b2f95986402d7f3b3e78702516244df049ba6c5aa012e81264aa3c /var/run/docker/libcontainerd/53a0a08547b2f95986402d7f3
17801 pts/2    Ss     0:00          \_ sh
17827 pts/2    S+     0:00              \_ sleep 5000
...
```


### 4. Activities in Container Runtime 
   
- Let's check cri-o's source code to understand how it can happen. The logic is similar in docker.

  It has a server which implements RuntimeServiceServer.
  ```go
  // source: https://github.com/cri-o/cri-o/blob/b3c67952115bee6f44405f547fef0b65a9134c60/server/server.go#L62-L83
  // Server implements the RuntimeService and ImageService
  type Server struct {
    config          libconfig.Config
    seccompProfile  *seccomp.Seccomp
    stream          StreamService
    netPlugin       ocicni.CNIPlugin
    hostportManager hostport.HostPortManager

    appArmorProfile string
    hostIP          string
    bindAddress     string

    *lib.ContainerServer
    monitorsChan      chan struct{}
    defaultIDMappings *idtools.IDMappings
    systemContext     *types.SystemContext // Never nil

    updateLock sync.RWMutex

    seccompEnabled  bool
    appArmorEnabled bool
  }
  ```

  ```go
  // source: https://github.com/cri-o/cri-o/blob/b3c67952115bee6f44405f547fef0b65a9134c60/server/container_exec.go#L14-L28
  // Exec prepares a streaming endpoint to execute a command in the container.
  func (s *Server) Exec(ctx context.Context, req *pb.ExecRequest) (resp *pb.ExecResponse, err error) {
    const operation = "exec"
    defer func() {
      recordOperation(operation, time.Now())
      recordError(operation, err)
    }()

    resp, err = s.getExec(req)
    if err != nil {
      return nil, fmt.Errorf("unable to prepare exec endpoint: %v", err)
    }

    return resp, nil
  }
  ```

  At the end of the chain, container runtime executes the command in the worker node.
  ```go
  // source: https://github.com/cri-o/cri-o/blob/b3c67952115bee6f44405f547fef0b65a9134c60/internal/oci/runtime_oci.go#L293-L342
  // ExecContainer prepares a streaming endpoint to execute a command in the container.
  func (r *runtimeOCI) ExecContainer(c *Container, cmd []string, stdin io.Reader, stdout, stderr io.WriteCloser, tty bool, resize <-chan remotecommand.TerminalSize) error {
    processFile, err := prepareProcessExec(c, cmd, tty)
    if err != nil {
      return err
    }
    defer os.RemoveAll(processFile.Name())

    args := []string{rootFlag, r.root, "exec"}
    args = append(args, "--process", processFile.Name(), c.ID())
    execCmd := exec.Command(r.path, args...)
    if v, found := os.LookupEnv("XDG_RUNTIME_DIR"); found {
      execCmd.Env = append(execCmd.Env, fmt.Sprintf("XDG_RUNTIME_DIR=%s", v))
    }
    var cmdErr, copyError error
    if tty {
      cmdErr = ttyCmd(execCmd, stdin, stdout, resize)
    } else {
      if stdin != nil {
        // Use an os.Pipe here as it returns true *os.File objects.
        // This way, if you run 'kubectl exec <pod> -i bash' (no tty) and type 'exit',
        // the call below to execCmd.Run() can unblock because its Stdin is the read half
        // of the pipe.
        r, w, err := os.Pipe()
        if err != nil {
          return err
        }
        go func() { _, copyError = pools.Copy(w, stdin) }()

        execCmd.Stdin = r
      }
      if stdout != nil {
        execCmd.Stdout = stdout
      }
      if stderr != nil {
        execCmd.Stderr = stderr
      }

      cmdErr = execCmd.Run()
    }

    if copyError != nil {
      return copyError
    }
    if exitErr, ok := cmdErr.(*exec.ExitError); ok {
      return &utilexec.ExitErrorWrapper{ExitError: exitErr}
    }
    return cmdErr
  }
  ```

![container-runtime-to-kernel](/img/kubectl-exec/container-runtime-to-kernel.png)

Finally, kernel executes commands.

![kernel-puts](/img/kubectl-exec/kernel-puts.png)


<br><br>

## Reminders
- api-server can also initialize a connection to kubelet.
- These connections persist until the interactive exec ends.
  - Connection between kubectl and api-server
  - Connection between api-server and kubelet
  - Connection between kubelet and container runtime
- kubectl or api-server cannot run anything in the worker nodes. kubelet can run but it also interacts with container runtime for this kind of actions.

<br><br>
## Resources

-  https://groups.google.com/forum/#!topic/kubernetes-dev/Cjia36v39vM
-  https://medium.com/@joatmon08/playing-with-kubeadm-in-vagrant-machines-part-2-bac431095706
-  https://serverfault.com/questions/252723/how-to-find-other-end-of-unix-socket-connection