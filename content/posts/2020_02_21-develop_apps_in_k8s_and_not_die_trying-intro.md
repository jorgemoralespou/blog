---
title: Developing applications on Kubernetes - Intro
date: 2020-02-21
publishDate: 2020-02-21
tags: [kubernetes,appdev,cloudnative,devexp]
author: jorgemoralespou
---
Not too long ago I read [a blog](http://bit.ly/k8s-boring) and found this quote: 

>“It's almost become boring to say that Kubernetes has become boring”

[Maciej Szulik](https://twitter.com/soltysh), the author, is a Kubernetes engineer working for Red Hat and SIG CLI lead amongst other things.
I’ve seen this quote being said more times than I would think, and all of the times it’s being said by a Kubernetes ecosystem engineer.

What do I think they mean when they say Kubernetes has become boring?

To better understand the context, I always find important to know who's talking. For that, I try to fit the person into one of the 3 different users I see on the Kubernetes world.

![Kubernetes users](/images/posts/develop_apps_in_k8s/users.png)

`“Application developers”` are the people that works at Banks, Retailers, Consulting firms, almost everywhere and that create applications for the company they work for that will run on the platform.

`"Operations"` are the people that will make sure that the application is running and that it does under certain conditions/SLAs/SLOs. They are responsible for the application even if they don't really neccesarily need to know anything about it. And yes, I fit SREs under this category as well.

`"Kubernetes ecosystem engineers"` are the people that creates the platform and any  tooling around the platform. From Kubernetes, Istio, KNative, Tekton to things like Helm, Krew, k9s, any software that develops anything with any sort of dependency on Kubernetes APIs I categorize it as Kubernetes ecosystem engineer.

To me, it's very important to draw this distinction as each one of these categories, and possible subcategories, have different skills, expertise and obviously requirements. They do totally different things and they use the tools and the platform in a totally different way. 

Kubernetes was designed to do one thing, “Production-Grade Container Orchestration”. After 4 years of the project, most of the basic required constructs are there and the goals of the project are met. It does container orchestration really well. That’s why, for someone like Maciej, a Kubernetes engineer, it is boring.

But obviously, not everyone feel the same.

For “Application developers”, Kubernetes is just where their applications will be deployed and will run. If their applications were to be deployed and run on Virtualization, probably their life would be the same.

So, what do “application developers” care about? 

They do care about being productive creating, updating, maintaining, fixing and evolving business applications. And they do care about using tools that are easy to learn and understand.

Hence, I can confidently say that Kubernetes despite being boring, it’s also hard. And for “application developers” it is `“really, really hard”`.
 
This one is the first article of a series where I will describe some of my thoughts around `“developing applications for Kubernetes”` and will continue presenting some of the tools I find useful.

To ilustrate my thoughts, I will use what I call the `"Kubernetes application development triangle"`. 

![Kubernetes application development trinagle](/images/posts/develop_apps_in_k8s/k8s_app_dev_triangle.png)

But if you want to know more about this, you'll have to wait for the next article.
