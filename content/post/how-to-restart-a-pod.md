+++
categories = ["Software","k8s","EN"]
date = "2020-04-14T19:00:00+03:00"
title = "Pods are not restartable"
keywords = ["Software", "k8s", "Kubernetes"]
weight = 1
+++

>+Hi Erkan. Pod-X in my cluster is in CrashLoopBackOff. How can I solve this issue?

>-Ohh:/ Most probably it is because of a known EKS issue. Just restart the pod. 

>+How?

>-Just delete the pod.

>+Really? May I delete any pod in my cluster? 

>-In theory, you can delete any pod of our application. Nevertheless, be careful :D 


I don't remember how many times I had this conversation but I have never thought about whether pods are really restartable or not until recently. When I thought about it deeply, **I noticed that pods are not restartable, they are "re-creatable".**

Here are my understanding and my comments.

> Please ping me if there is something wrong. https://twitter.com/erkan_erol_

<!--more-->
<br><br>

## Basics

<img src="/img/pods_simplified.png"  title="pods-simplified"/>

Quotes from official doc:

- "A Pod is a group of one or more containers (such as Docker containers), with shared storage/network, and a specification for how to run the containers.[1]"

- "The shared context of a Pod is a set of Linux namespaces, cgroups, and potentially other facets of isolation...[1]"

- "In Kubernetes, controllers are control loops that watch the state of your cluster, then make or request changes where needed[2]"


<br>


## Lifecycle and Flow (Simplified)

- A pod is created
- Scheduler schedules it into a pod
- Kubelet notices it and prepares the context
- Kubelet triggers Container Runtime
- Init containers [Restartable]
- Containers [Restartable]

## Scenario 1: Problematic Storage Provisioner

EKS fails and pods stuck in CrashLoopBackOff.

Solution: Force re-scheduling

## Scenario 2: Problematic Containers

Container 2 deletes something created by init container and container restart doesn't solve the issue.

Solution: Delete the pod

## Wishes

I wish there was a way to restart the pod from scratch.

<br><br>

## Resources

1. https://kubernetes.io/docs/concepts/workloads/pods/pod/
2. https://kubernetes.io/docs/concepts/architecture/controller/
3. https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
4.  