+++
categories = ["Software","k8s","EN"]
date = "2022-04-12T22:00:00+03:00"
title = "Is Kubernetes simple or complex?"
keywords = ["Software", "k8s", "Kubernetes"]
weight = 1
+++

> Disclaimer: I am working for [Giant Swarm](https://www.giantswarm.io/product) as a Platform Engineer. My day-to-day work might have influenced my perspective on this subject since it is pretty related to why/how we help our customers.

Over the years, I faced two different reactions from the people who are new to Kubernetes: 

1. "Is it genuinely that simple?"
2. "Why is it so complex?"

In this blog post, I am going to explain how it can happen at the same time by digging deeper into the problem domain.
<!--more-->

---

## The nature of the problem

All companies want to run their applications on top of a cutting-edge infrastructure. Let's think about this problem.

1. ***Big commons:*** Infrastructures have some aspects like networking, security, storage, deployment, 
   observability etc. They are very big fields and common for all companies. 

2. ***Small variables:*** In the big common fields, there are numerous subproblems. For example, dual stacks in 
   networking, single sign-on in security, gitops in deployment, continuous profiling in observability etc. 

3. ***Unique cases:*** Every company has to solve a subset of subproblems and has its own unique case.

![image](/img/is-kubernetes-simple-or-complex/nature.png)

--- 

## Different approaches to the problem

1. ***Monolith and specific solution per case***

2. ***Monolith and generic solution***

3. ***




