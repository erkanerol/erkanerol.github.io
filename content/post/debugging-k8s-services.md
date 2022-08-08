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

![Setup](/img/k8s-services/Setup.png)

We have 3 services. <b>service-front</b> is exposed to the public via an ingress. <b>service-front</b> depends on <b>service-middle</b> and <b>service-middle</b> depends on <b>service-back</b>. The communications are done through k8s services. 

To install this setup, here are the necessary commands:
```bash
kubectl create ns service-debug
kubectl -n service-debug run service-back --image=erkanerol/service-back:v1 --port=8080 --expose=true --labels="app=back"
kubectl -n service-debug run service-middle --image=erkanerol/service-middle:v1 --port=8081 --expose=true --labels="app=middle"
kubectl -n service-debug run service-front --image=erkanerol/service-front:v1 --port=8082 --expose=true --labels="app=front"
```

Here is the source code of these services: https://github.com/erkanerol/service-examples-for-blog

<br>

## Tool 1: <b>kubectl port-forward</b>

#### Scenario:
As a developer, I want to send some requests to <b>service-back</b> directly and see the result without touching the other services. 

#### Problem:
<b>service-back</b> is not exposed to the public and you cannot send requests to it directly. 

#### Solution:
With <b>kubectl port-forward</b>, it is possible to open a tunnel from your local machine to the <b>service-back</b> in the cluster. See https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

#### Steps:

- Run the command below in a terminal.
    ```bash
    $ kubectl -n service-debug port-forward service/service-back 8080:8080
    Forwarding from 127.0.0.1:8080 -> 8080
    Forwarding from [::1]:8080 -> 8080
    ```

- Then run the curl command below in another terminal to see that you are able to access <b>service-back</b>

    ```bash
    $ curl localhost:8080
    Timestamp from back:1614508193
    ```
<br>

#### How does it work?

![Level1](/img/k8s-services/Level1.png)

<b>kubectl</b> starts a process which binds <b>localhost:8080</b>. It listens that port and establishes a connection to api-server, which forwards the requests to <b>service-back</b>.


<br><br>

## Tool 2: <b>kubefwd</b>


#### Scenario:
As a developer, I want to run <b>service-front</b> in my local machine so that I can put breakpoints in my IDE to debug my application.

#### Problem:
<b>service-front</b> is designed to run in Kubernetes and it accesses <b>service-middle</b> via the k8s service. The service name is hardcoded or hard to be configurable or you are too lazy to mock the dependencies in your local machine.

#### Solution:
<b>kubefwd</b> is a useful tool for this problem. It does bulk port-forwarding and manages dns entries in your local machine. See https://github.com/txn2/kubefwd

#### Steps:

- Run the command below in a terminal
    ```bash
    $ sudo KUBECONFIG=$KUBECONFIG kubefwd svc -n service-debug -l app=middle
    ```

    > Note that <b>kubefwd</b> requires root privileges and it has to be run with <b>sudo</b>. Set <b>KUBECONFIG</b> variable without any home folder reference beforehand. 

<br>

- In another terminal, run <b>front</b> application in your local machine. Note that you can run it in debug mode as well and put breakpoints.

    ```bash
    $ cd /tmp
    $ git clone https://github.com/erkanerol/service-examples-for-blog.git
    $ cd service-examples-for-blog/front
    $ go run main.go
    ```

<br>

- In another terminal, Send a request to <b>front</b> app to see that your front app serves locally and it accesses <b>service-middle</b> in the cluster.
    ```
    $ curl localhost:8082
    Response from service middle:'Response from service back:'Timestamp from back:1614513901''
    ```

<br>

#### How does it work?


![Level2](/img/k8s-services/Level2.png)

- As you can see from the logs of <b>kubefwd</b>
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

- It starts a process that binds <b>127.1.27.1:8081</b> and manipulates the <b>/etc/hosts</b> for <b>service-middle</b>

    ```
    $ cat /etc/hosts |grep service-middle
    127.1.27.1       service-middle.default service-middle.default.svc service-middle.default.svc.cluster.local service-middle.default.minikube service-middle.default.svc.minikube service-middle.default.svc.cluster.minikube service-middle service-middle.service-debug service-middle.service-debug.svc service-middle.service-debug.svc.cluster.local service-middle.service-debug.minikube service-middle.service-debug.svc.minikube service-middle.service-debug.svc.cluster.minikube
    ```


Then your local <b>front</b> app can access the <b>service-middle</b> like in k8s cluster without any extra effort.

<br>

## Tool 3: <b>telepresence</b>

#### Scenario:
As a developer, I want to run <b>service-middle</b> in my local machine so that I can put breakpoints in my IDE to debug my application.

#### Problem:
<b>service-middle</b> is designed to run in Kubernetes. It accesses <b>service-back</b> via k8s services. Also, its consumer <b>service-front</b> is running on k8s. The services are not available in your local machine and it is hard to mock all these environments in your local machine.

#### Solution:
<b>telepresence</b> is a useful tool for this problem. See https://www.telepresence.io/


#### Steps:

- Delete <b>service-middle</b> from your k8s cluster at first. We will run it locally.
    ```bash
    kubectl -n service-debug delete service service-middle --ignore-not-found=true
    kubectl -n service-debug delete pod service-middle --ignore-not-found=true
    ```

<br>

- Run telepresence for <b>service-middle</b>

    ```bash
    telepresence --namespace service-debug --new-deployment service-middle --expose 8081
    ```

<br>

- In another terminal, run <b>middle</b> application in your local machine. Note that you can run it in debug mode as well and put breakpoints.

    ```
    $ cd /tmp
    $ git clone https://github.com/erkanerol/service-examples-for-blog.git
    $ cd service-examples-for-blog/middle
    $ go run main.go
    ```

<br>

- In another terminal, run the command below to send a request to <b>service-front</b> via a temporary pod in the cluster. 

    ```bash
    $ kubectl -n service-debug run curl -it  --rm=true --image=curlimages/curl --restart=Never -- http://service-front:8082 
    Response from service middle:'Response from service back:'Timestamp from back:1614517363''pod "curl" deleted
    ```

Note that your request goes to <b>service-front</b> in k8s, which sends a request to <b>service-middle</b> in your local machine, which sends a request to <b>service-back</b> in the cluster.

<br>

#### How does it work?

![Level3](/img/k8s-services/Level3.png)


Basically, <b>telepresence</b> deploys a proxy/fake agent into cluster and opens a two-way tunnel between your local environment and the cluster via that agent. Then you are able to run the <b>middle</b> service in your local machine without adapting the consumers/dependent services.

A detailed explanation about how telepresence works is available here: https://www.telepresence.io/discussion/how-it-works


<br>

## Summary

- If you need to access a service without exposing it to public, <b>kubectl port-forward</b> is enough.
- If you need to run a service locally for debugging and your service needs to access other services on k8s, <b>kubefwd</b> is enough. It manages DNS entries in your local machine and opens a one-way tunnel from your machine to cluster for the dependencies of your service.
- If you need to run a service locally for debugging and your application has some consumers in the cluster, <b>telepresence</b> is your tool. It opens two-way network channel and it forwards requests from cluster to your local instance as well.

<br><br>


p.s. Admission webhooks in Kubernetes are similar to <b>service-middle</b>. They receive some requests from api-server and they may send some requests to other services in the cluster. Therefore, <b>telepresence</b> is a useful tool for debugging admission webhooks.