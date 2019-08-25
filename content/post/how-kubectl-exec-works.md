+++
categories = ["Software","k8s"]
date = "2019-08-25T19:00:00+03:00"
title = "How does kubectl work?"
keywords = ["Software", "k8s", "Kubernetes"]
weight = 1
+++

Last Friday, one of my colleagues approached me and asked a question about how to exec a command in a pod with client-go. I didn't know the answer and I noticed that I had never thought about the mechanism in "kubectl exec". I had some ideas about how it should be, but I wasn't 100% sure. I noted the topic to check again and I have learnt a lot after reading some blogs, docs and source codes. In this blog post, I am going to share my understanding and findings. 

> Please ping me if there is something wrong. https://twitter.com/erkan_erol_

<!--more-->
<br><br>
## Setup

I cloned https://github.com/ecomm-integration-ballerina/kubernetes-cluster in order to create a k8s cluster in my MacBook. I fixed IP addresses of the nodes in kubelet configurations since the default configuration didn't let me run `kubectl exec`. You can find the root cause [here](https://medium.com/@joatmon08/playing-with-kubeadm-in-vagrant-machines-part-2-bac431095706).

* Any machine = my MacBook
* IP of master node = 192.168.205.10
* IP of worker node = 192.168.205.11
* API server port = 6443


<br><br>

## Components

<img src="/img/kubectl-exec/components.png" title="Components"/>

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
```
// any machine
$ kubectl run exec-test-nginx --image=nginx
```

- Then run an exec command and `sleep 5000` to make observation
```
// any machine
$ kubectl exec -it exec-test-nginx-6558988d5-fgxgg -- sh
# sleep 5000
```

- We can observe the kubectl process (pid=8507 in this case)
```
// any machine
$ ps -ef |grep kubectl
501  8507  8409   0  7:19PM ttys000    0:00.13 kubectl exec -it exec-test-nginx-6558988d5-fgxgg -- sh
```

- When we check network activities of the process, we can see that it has some connections to api-server (192.168.205.10.6443)
```
// any machine
$ netstat -atnv |grep 8507
tcp4       0      0  192.168.205.1.51673    192.168.205.10.6443    ESTABLISHED 131072 131768   8507      0 0x0102 0x00000020
tcp4       0      0  192.168.205.1.51672    192.168.205.10.6443    ESTABLISHED 131072 131768   8507      0 0x0102 0x00000028
```

- Let's check the code. kubectl creates a POST request with subresource `exec` and sends a rest request.
   <script src="https://gist-it.appspot.com/http://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/staging/src/k8s.io/kubectl/pkg/cmd/exec/exec.go?slice=344:359"></script>

<img src="/img/kubectl-exec/rest-request.png"  title="rest-request"/>

<br>
### 2. Activities in Master Node

- We can observe the request in api-server side as well.
```
handler.go:143] kube-apiserver: POST "/api/v1/namespaces/default/pods/exec-test-nginx-6558988d5-fgxgg/exec" satisfied by gorestful with webservice /api/v1
upgradeaware.go:261] Connecting to backend proxy (intercepting redirects) https://192.168.205.11:10250/exec/default/exec-test-nginx-6558988d5-fgxgg/exec-test-nginx?command=sh&input=1&output=1&tty=1
Headers: map[Connection:[Upgrade] Content-Length:[0] Upgrade:[SPDY/3.1] User-Agent:[kubectl/v1.12.10 (darwin/amd64) kubernetes/e3c1340] X-Forwarded-For:[192.168.205.1] X-Stream-Protocol-Version:[v4.channel.k8s.io v3.channel.k8s.io v2.channel.k8s.io channel.k8s.io]]
```
    > Notice that the http request includes a protocol upgrade request. [SPDY](https://www.wikiwand.com/en/SPDY) allows for separate stdin/stdout/stderr/spdy-error "streams" to be multiplexed over a single TCP connection.

- Api server receives the request and binds it into a `PodExecOptions`
<script src="https://gist-it.appspot.com/http://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/apis/core/types.go?slice=4124:4147"></script>

- To be able to take necessary actions, api-server needs to know which location it should contact. 
    <script src="https://gist-it.appspot.com/http://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/registry/core/pod/strategy.go?slice=455:467"></script><br>

    Of course the endpoint is derived from node info.
    <script src="https://gist-it.appspot.com/http://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/registry/core/pod/strategy.go?slice=504:510"></script><br>

    GOTCHA! KUBELET HAS A PORT (`node.Status.DaemonEndpoints.KubeletEndpoint.Port`) TO WHICH API-SERVER CAN CONNECT.
    <script src="https://gist-it.appspot.com/http://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/kubelet/client/kubelet_client.go?slice=180:206"></script><br>

    > [Master-Node Communication > Master to Cluster > apiserver to kubelet](https://kubernetes.io/docs/concepts/architecture/master-node-communication/#apiserver-to-kubelet)

    > These connections terminate at the kubelet’s HTTPS endpoint. By default, the apiserver does not verify the kubelet’s serving certificate, which makes the connection subject to man-in-the-middle attacks, and ***unsafe*** to run over untrusted and/or public networks. 

- Now, api server knows the endpoint and it opens a connections. 
    <script src="https://gist-it.appspot.com/http://github.com/kubernetes/kubernetes/blob/a1f1f0b599e961a5c59b02c349c0ed818b1851a5/pkg/registry/core/pod/rest/subresources.go?slice=132:144"></script><br>

- Let's check what is going on the master node.

First, learn the ip of the worker node. It is `192.168.205.11` in this case.
```
// any machine
$ kubectl get nodes k8s-node-1 -o wide
NAME         STATUS   ROLES    AGE   VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
k8s-node-1   Ready    <none>   9h    v1.15.3   192.168.205.11   <none>        Ubuntu 16.04.6 LTS   4.4.0-159-generic   docker://17.3.3
```

Then get the kubelet port. It is `10250` in this case.
```
// any machine
$ kubectl get nodes k8s-node-1 -o jsonpath='{.status.daemonEndpoints.kubeletEndpoint}'
map[Port:10250]
```

Then check the network. Is there a connection to worker node(192.168.205.11)? THE CONNECTİON IS THERE. When I kill the exec process, it disappears so I know it is set by api-server because of my exec command.
```
// master node
$ netstat -atn |grep 192.168.205.11
tcp        0      0 192.168.205.10:37870    192.168.205.11:10250    ESTABLISHED
...
```

<img src="/img/kubectl-exec/api-server-to-kubelet.png"  title="api-server-to-kubelet"/>

- Now the connection between kubectl and api-server is still open and there is another connection between api-server and kubelet. 

<br><br>
### 3. Activities in Worker Node

- Let's continue by connecting to the worker node and checking what is going on the worker node.

  First, we can observe the connection here as well. The second line. `192.168.205.10` is the IP of master node.
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
   <script src="https://gist-it.appspot.com/https://github.com/kubernetes/kubernetes/blob/master/pkg/kubelet/server/streaming/server.go?slice=41:60"></script><br>

- kubelet computes a response endpoint for exec requests.
   <script src="https://gist-it.appspot.com/https://github.com/kubernetes/kubernetes/blob/master/pkg/kubelet/server/streaming/server.go?slice=177:190"></script><br>

  Don't confuse. It doesn't return the result of the command. It returns an endpoint for communication. 
<script src="https://gist-it.appspot.com/https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go?slice=4533:4540"></script><br>
  
  kubelet implements `RuntimeServiceClient` interface which is part of Container Runtime Interface.
  <script src="https://gist-it.appspot.com/https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go?slice=7157:7229"></script><br>

  It just uses gRPC to invoke a method through Container Runtime Interface.
  <script src="https://gist-it.appspot.com/https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go?slice=7229:7233"></script><br>

  <script src="https://gist-it.appspot.com/https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go?slice=7372:7381"></script><br>

  Container Runtime is responsible to implement `RuntimeServiceServer`
  <script src="https://gist-it.appspot.com/https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/cri-api/pkg/apis/runtime/v1alpha2/api.pb.go?slice=7436:7508"></script><br>


<img src="/img/kubectl-exec/kubelet-to-container-runtime.png"  title="kubelet-to-container-runtime"/>


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
  <script src="https://gist-it.appspot.com/https://github.com/cri-o/cri-o/blob/master/server/server.go?slice=60:82"></script><br>

  <script src="https://gist-it.appspot.com/https://github.com/cri-o/cri-o/blob/master/server/container_exec.go?slice=13:28"></script><br>

  At the end of the chain, container runtime executes the command in the worker node.
  <script src="https://gist-it.appspot.com/https://github.com/cri-o/cri-o/blob/master/internal/oci/runtime_oci.go?slice=292:342"></script><br>

<img src="/img/kubectl-exec/container-runtime-to-kernel.png"  title="container-runtime-to-kernel"/>

Finally, kernel executes commands.
<img src="/img/kubectl-exec/kernel-puts.png"  title="kernel-puts"/>


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