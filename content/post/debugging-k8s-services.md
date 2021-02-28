+++
categories = ["Software","k8s","EN"]
date = "2021-02-28T16:00:00+03:00"
title = "Debugging k8s services: 3 tools for 3 scenarios"
keywords = [ "kubectl", "kubefwd", "telepresence"]
weight = 1
+++

While developing/debugging applications that serve services on k8s in production, you need some tools/commands. This blog post explains three different scenarios+tools for you.

> Please ping me if there is something wrong. https://twitter.com/erkan_erol_

<!--more-->
<br>
## Setup

Here is our basic setup to explain the scenarios.

<img src="/img/k8s-services/Setup.png" title="Setup"/>

We have 3 services. `service-front` is exposed to the public via an ingress. `service-front` depends on `service-middle` and `service-middle` depends on `service-back`. The communications are done through k8s services. 

To install this setup, here are the necessary commands:
```
kubectl create ns service-debug
kubectl -n service-debug run service-back --image=erkanerol/service-back:v1 --port=8080 --expose=true --labels="app=back"
kubectl -n service-debug run service-middle --image=erkanerol/service-middle:v1 --port=8081 --expose=true --labels="app=middle"
kubectl -n service-debug run service-front --image=erkanerol/service-front:v1 --port=8082 --expose=true --labels="app=front"
```

Here is the source code of these services: https://github.com/erkanerol/service-examples-for-blog

<br><br>

## Level 1: `kubectl port-forward`

#### Scenario:
As a developer, I want to send some requests to `service-back` directly and see the result without touching the other services. 

#### Problem:
`service-back` is not exposed to the public and you cannot send requests to it directly. 

#### Solution:
With `kubectl port-forward`, it is possible to open a tunnel from your local machine to the `service-back` in the cluster. See https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

#### Steps:

Run the command below in a terminal.

```
$ kubectl -n service-debug port-forward service/service-back 8080:8080
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
```

<br>
Then run the curl command below in another terminal to see that you are able to access `service-back`

```
$ curl localhost:8080
Timestamp from back:1614508193
```
<br>

#### How does it work?

<img src="/img/k8s-services/Level1.png" title="Level1"/>

`kubectl` starts a process which binds `localhost:8080`. It listens that port and establishes a connection to api-server, which forwards the requests to `service-back`.


<br><br>

## Level 2: `kubefwd`


#### Scenario:
As a developer, I want to run `service-front` in my local machine so that I can put breakpoints in my IDE to debug my application.

#### Problem:
`service-front` is designed to run in Kubernetes and it accesses `service-middle` via the k8s service. The service name is hardcoded or hard to be configurable or you are too lazy to mock the dependencies in your local machine.

#### Solution:
`kubefwd` is a useful tool for this problem. It does bulk port-forwarding and manages dns entries in your local machine. See https://github.com/txn2/kubefwd

#### Steps:

Run the command below in a terminal
```
$ sudo KUBECONFIG=$KUBECONFIG kubefwd svc -n service-debug -l app=middle
```

> Note that `kubefwd` requires root privileges and it has to be run with `sudo`. Set `KUBECONFIG` variable without any home folder reference beforehand. 

<br>

In another terminal, run `front` application in your local machine. Note that you can run it in debug mode as well and put breakpoints.

```
$ cd /tmp
$ git clone https://github.com/erkanerol/service-examples-for-blog.git
$ cd service-examples-for-blog/front
$ go run main.go
```

<br>

In another terminal, Send a request to `front` app to see that your front app serves locally and it accesses `service-middle` in the cluster.
```
$ curl localhost:8082
Response from service middle:'Response from service back:'Timestamp from back:1614513901''
```

<br>

#### How does it work?


<img src="/img/k8s-services/Level2.png" title="Level2"/>

As you can see from the logs of `kubefwd`
```
...
INFO[14:07:38] 'cat /etc/hosts' to see all host entries.    
INFO[14:07:38] Loaded hosts file /etc/hosts                 
INFO[14:07:38] HostFile management: Original hosts backup already exists at /root/hosts.original 
...
INFO[14:07:38] Port-Forward: 127.1.27.1 service-middle:8081 to pod service-middle:8081 
...
```
<br>

It starts a process that binds `127.1.27.1:8081` and manipulates the `/etc/hosts` for `service-middle`

```
$ cat /etc/hosts |grep service-middle
127.1.27.1       service-middle.default service-middle.default.svc service-middle.default.svc.cluster.local service-middle.default.minikube service-middle.default.svc.minikube service-middle.default.svc.cluster.minikube service-middle service-middle.service-debug service-middle.service-debug.svc service-middle.service-debug.svc.cluster.local service-middle.service-debug.minikube service-middle.service-debug.svc.minikube service-middle.service-debug.svc.cluster.minikube
```


Then your local `front` app can access the `service-middle` like in k8s cluster without any extra effort.
<br><br>


## Level 3: `telepresence`

#### Scenario:
As a developer, I want to run `service-middle` in my local machine so that I can put breakpoints in my IDE to debug my application.

#### Problem:
`service-middle` is designed to run in Kubernetes. It accesses `service-back` via k8s services. Also, its consumer `service-front` is running on k8s. The services are not available in your local machine and it is hard to mock all these environments in your local machine.

#### Solution:
`telepresence` is a useful tool for this problem. See https://www.telepresence.io/


#### Steps:

Delete `service-middle` from your k8s cluster at first. We will run it locally.
```
kubectl -n service-debug delete service service-middle --ignore-not-found=true
kubectl -n service-debug delete pod service-middle --ignore-not-found=true
```

<br>
Run telepresence for `service-middle`
```
telepresence --namespace service-debug --new-deployment service-middle --expose 8081
```

<br>
In another terminal, run `middle` application in your local machine. Note that you can run it in debug mode as well and put breakpoints.

```
$ cd /tmp
$ git clone https://github.com/erkanerol/service-examples-for-blog.git
$ cd service-examples-for-blog/middle
$ go run main.go
```

<br>
In another terminal, run the command below to send a request to `service-front` via a temporary pod in the cluster. 
```
$ kubectl -n service-debug run curl -it  --rm=true --image=curlimages/curl --restart=Never -- http://service-front:8082 
Response from service middle:'Response from service back:'Timestamp from back:1614517363''pod "curl" deleted
```

Note that your request goes to `service-front` in k8s, which sends a request to `service-middle` in your local machine, which sends a request to `service-back` in the cluster.

<br>

#### How does it work?

<img src="/img/k8s-services/Level3.png" title="Level3"/>


Basically, `telepresence` deploys a proxy/fake agent into cluster and opens a two-way tunnel between your local environment and the cluster via that agent. Then you are able to run the `middle` service in your local machine without adapting the consumers/dependent services.

A detailed explanation about how telepresence works is available here: https://www.telepresence.io/discussion/how-it-works


<br>

## Summary

- If you need to access a service without exposing it to public, `kubectl port-forward` is enough.
- If you need to run a service locally for debugging and your service needs to access other services on k8s, `kubefwd` is enough. It manages DNS entries in your local machine and opens a one-way tunnel from your machine to cluster for the dependencies of your service.
- If you need to run a service locally for debugging and your application has some consumers in the cluster, `telepresence` is your tool. It opens two-way network channel and it forwards requests from cluster to your local instance as well.

<br><br>


p.s. Admission webhooks in Kubernetes are similar to `service-middle`. They receive some requests from api-server and they may send some requests to other services in the cluster. Therefore, `telepresence` is a useful tool for debugging admission webhooks. In the next blog post, I am going to explain how to debug validating webhooks.