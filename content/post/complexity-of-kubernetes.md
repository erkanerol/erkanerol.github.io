+++
date = "2022-07-04T10:00:00+03:00"
title = "Thinking about the complexity of the Kubernetes ecosystem"
tags = ["English", "Software", "Kubernetes", "CNCF"]
+++

> Disclaimer: I am working for [Giant Swarm](https://www.giantswarm.io/product) as a Platform Engineer. My day-to-day work might have influenced my perspective on this subject since it is pretty related to why/how we help our customers.

Over the years, I faced two different reactions from people who are new to Kubernetes: 

1. "Is Kubernetes genuinely that simple? Why doesn't it have X as a built-in feature?"
2. "Why is Kubernetes so complex? Why do I have to learn so many things to do just simple Y?"

Before learning Kubernetes in detail, they are under the impression that Kubernetes
- has all the necessary features of modern infrastructure
- provides what they need perfectly
- is easy to use
- is free

In this blog post, I am going to explain why it is so hard to achieve these goals at the same time by analyzing the anatomy of Kubernetes ecosystem.

<!--more-->

---

## Basics of the problem domain

All companies want to run their applications on top of cutting-edge infrastructure. Let's think about this problem.

1. ***Big commons:*** Infrastructures have some aspects like networking, security, storage, deployment, 
   observability, etc. They are very big fields and common for all companies. 

2. ***Small variables:*** In the big common fields, there are numerous subproblems. For example, dual stacks in 
   networking, single sign-on in security, GitOps in deployment, continuous profiling in observability, etc. 

3. ***Different solutions:*** For every subproblem, there is more than one solution. All solutions have pros/cons according to the use case.

4. ***Unique cases:*** Every company has to solve a subset of subproblems by implementing the right solutions according to its case. This builds its unique case in the problem domain.

![image](/img/complexity-of-kubernetes/nature.png)

--- 

## Who is going to solve this problem?


1. ***The owner of the problem:*** Every company can build a solution for its unique case.
2. ***Third-party companies:*** Some companies can build solutions for other companies.

> Here we are talking about implementing solutions from scratch like "We need to monitor X, Y, Z. Let's build a monitoring tool for our case."

Option 1 is costly in two ways:
- For our industry: When a problem is solved by different companies again, and again, we waste time and money.
- For the company: When a company tries to solve every subproblem in its unique case, it requires an incredible amount of domain+tech knowledge, which means an incredible amount of money and time. It is simply not affordable for most companies.

So option 2 makes more sense: Some companies will build reusable solutions for others.

## One company vs multiple companies?

1. ***One company:*** A company can implement end-to-end solutions completely by solving a subset of subproblems. The company can sell those full solutions to others.
2. ***Multiple companies:*** Different companies can implement separate solutions for specific subproblems and we can integrate them at the end.

One company cannot deliver the best solutions for every subproblem in this huge area. Do you think SAP/Oracle/Red Hat can be better than Grafana Labs in monitoring, Sysdig in runtime security, Jetstack in certificate management, Kong in connectivity? Can they implement all the features of other products in the same quality? Can they be innovative in all the areas at the same time? 

Some big companies can try and deliver some solutions for some cases but it is clear that it is not the optimal solution. Many big enterprise companies try to sell giant packages for millions of dollars and those products are full of unnecessary features. They say their product has multiple features in a a unified experience but I think the obsession with unified experience just makes everything worse.

Shortly, option 2 is much better than option 1: Solutions for specific subproblems should be solved by specialized companies.

## Integration base: Kubernetes

When different companies build independent solutions for subproblems, we have to integrate those solutions. To optimize the integration effort, we need a common base.

Basically, we expect two things from the common integration layer:
1. It should not be so fat. We will use it everywhere and it should be as light as possible by default.
2. It should be extendable. It should allow us to customize everything so that we can put our custom solutions on top of it.


 The common base is <b>Kubernetes</b> these days. It was not the main goal of Kubernetes in the beginning. It was yet another container orchestration tool. However, it is the de facto standard to run cloud-native workloads now.

The first item explains why Kubernetes doesn't have so many built-in features by default. It doesn't promise to solve your all problems. 

Regarding the second item, Kubernetes were not extendable so much in the beginning but it has been improving with new [extension points](https://kubernetes.io/docs/concepts/extend-kubernetes/) like plugins and operators. It would be much better if it was designed for this goal from the beginning but it is what it is now. 

## Integrable solutions: CNCF projects

[Cloud Native Computing Foundation(CNCF)](https://www.cncf.io/) is orchestrating the cloud-native world with support of many companies and there are hundreds of projects in its landscape as of today.

![image](/img/complexity-of-kubernetes/cncf-tools.png)

As we talked about above, specialized companies build state of art solutions for the cloud-native world. Some of the projects are donated to CNCF, and some of them are not. However, the common property of all is that they can be integrated with Kubernetes easily. It explains why you have to learn yet another tool from this picture when you need to do something. It is how the ecosystem is designed. Does it make sense after thinking about the things above? For me, it does. 

## Integration cost is inevitable

OK, we have Kubernetes. We have a huge app catalog. Let's think about how to build e2e solutions.

It is really simple to try an app on Kubernetes. Go to the "Getting started" page, copy one command (kubectl or helm),and run it against your cluster. Bam! You have it! 

When you want to configure it for your use case, it is not so hard. Check the doc, learn the possible configurations, and provide the necessary parameters. Bam! It is fine.

When you want to run multiple applications that are interacting with each other, it can be harder. You need to know which versions of applications are compatible and which configurations are necessary to run them in harmony.

When you want to run lots of applications including kubernetes itself stably in the long run, the integration cost is a lot. Literally a lot.  Every new item in the stack increases the complexity more and more. The complexity increases exponentially.  

Most probably you think: "Wait wait wait. Didn't you say that this mechanism was the most optimal solution? Why do you say the cost is a lot here?"

My answer: "Well. This is a different cost from the earlier one. We talked about the development cost of solutions. Now, we are talking about the cost of consumption of those solutions. Since the solutions are developed independently, we have to pay a cost to integrate them."

## How to pay the inevitable cost

1. ***The owner of the problem:*** A company can try to integrate available solutions for its unique case.
2. ***Third party companies:*** Some companies can support others.

Nowadays, some companies prefer option 1. They pay the cost by hiring more people to be able to do the integration. They follow available tools in the market. They learn how to use them. They try to run multiple third-party apps on top of Kubernetes in a stable and secure way. They upgrade the tools one by one. They open bugs against upstream repositories when there is something wrong. Some competent them open PRs for their problems. They have many people for on-call rotations. And more and more...

Option 1 makes sense for some cases especially if you are a big tech company. However, for some others, especially the ones whose main business is not tech and the ones who want to keep the focus on something else, it makes sense to delegate the problem to another company.

## How can companies help others with integration?

Let's imagine you want to start a business by solving others' integration problems. Then, you will have two things you need to decide:

1. How many apps from the picture above will be ready in your solution in advance?
2. How opinionated will your solution be?

And your decisions will shape your business model, your product/service, and your user base. As in all matters, there is no perfect decision. There are some trade-offs. It is up to you where you locate your solution in the market.

> There are lots of companies, products, and services in the Kubernetes ecosystem. I won't mention any of them here intentionally to not offend anyone.


![image](/img/complexity-of-kubernetes/solution-diagram.png)


***Journey of a company founder***
- You want to build a product company and solve all problems of all people with your great product. Then the product has to have all the necessary features for modern infrastructure and provide the perfect solution for each customer's unique case. Then you have to put so many apps from the picture above to your product and make the product customizable and allow users to add/remove/configure components. Unfortunately, the customizability of a product is directly proportional to the size of the configuration interface. When the size of the configuration interface expands, it requires more knowledge for the right usage. It means your customers will have to pay an additional price to be able to use your product. They will have to hire experts for your product or buy consultancy from your company. Not great. Right?
   
- Then you decide to make the product easy to use and reduce the configuration complexity. You will have to make the product more opinionated. Then you will enforce users to have unnecessary components(fat solutions) and to use hacky ways when they want to do something different. They will start complaining about it. Still not great.

- Then you accept that you cannot solve all problems of all people in a perfect way and you decide to make the product lighter. You still want to provide some features in an opinionated way so that users can easily use your product. Some customers will satisfy with it but some will not. It means your target user base is smaller now. Are you OK with it?

- Then you decide "OK. I will provide nothing upfront. Tell me your problem and I will build the perfect solution by customizing everything for you and I will keep the configuration interface as small as possible by only caring about your unique case. Then you can easily use it." Congratulations! You have a service company and your solution is much more expensive 
as of now. Are your customers happy with it? 


## Epilogue

Yes. No solution in the market can realize all of your dreams. You have to pay the integration cost. By hiring more people or getting a premium consultancy service from another company or living with the products available in the market. Some of the products will restrict you a lot. Some will require another BS degree to be able to use. Some will handle only a portion of your problems and leave the remaining ones to you.

![image](https://thumbs.gfycat.com/RaggedFrequentKusimanse-size_restricted.gif)

## Feedback

You can contact me via the addresses below if there is something you want to tell me.
- https://twitter.com/erkan_erol_
- https://www.linkedin.com/in/erkanerol/
- erkanerol92 AT gmail DOT com

