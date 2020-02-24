---
title: Developing applications on Kubernetes - Concepts and Definitions
date: 2020-02-24
publishDate: 2020-02-24
tags: [kubernetes,appdev,cloudnative,devexp]
author: jorgemoralespou
---
As discussed in the [intro article](2020_02_21-develop_apps_in_k8s_and_not_die_trying-intro.md) I'm going to talk about Developing applications that will run on a Kubernetes platform. The first thing we need to do before going deeper into the topic is to go over some basic concepts so that everything I talk about later has the proper context.

## What is an application?
If you ask the question of `what is an application` to different people you will get different answers, almost one different answer per respondant. So here, I'm going to try to define what I understand as an application.

An application can be one single process/binary deployed in isolation, that has all the functionality it requires built-in. We have seen this pattern in the monolithic designs.

![Monolith single-component](/images/posts/develop_apps_in_k8s/app_1.png)

But we have also seen that sometimes this monolith is decomposed into smaller monolithic components, but that still are considered as monoliths in the classical monolithic designs.

![Monolith frontend-backend](/images/posts/develop_apps_in_k8s/app_2.png)

Sometimes they often use a storage solution. Would the database be considered part of an application? Would the rest of the application work without the database?

![Monolith frontend-backend-db](/images/posts/develop_apps_in_k8s/app_3.png)

Sometimes there’s more than just one database. Sometimes we have multiple databases or external services, like a message queue. Would we consider now that this is part of an application?

![Destructured monolith](/images/posts/develop_apps_in_k8s/app_4.png)

And what if we have a huge amount of composing services? This is what sometimes we call a microservices type of design. What would be the application in this case?

![Microservices](/images/posts/develop_apps_in_k8s/app_5.png)

And what if we also add to the picture functions? An application can have all of these constituent services.

![Microservices and functions](/images/posts/develop_apps_in_k8s/app_6.png)

For this reason, what I define as an application is:

> “any software component or group of components that is designed for the end user”

And the key identifiers that prove that an application has been designed for the end user are:

* **Complete and fully functional**: The application needs to work and not require any additional component that is nnot bundled/identified as part of the application.
* **Does have a version number representing it**: I can identify the complete application by a version number, even if the constituent components also have a version number which can be different for any of it.
* **Multiple instances with different configuration can exist**: Multiple instances of the application can exist and every application will have it's own configuration. Two instances with the same identification and configuration will, most likely, be the same application.
* **Can be easily installed**: The process required to install the application should be simple enough that any end user can easily go through it, and obviously, will need to install the complete application (and constituent components).


## Logical view versus physical view
When we’re creating applications, as developers, we often care about how the components of our application will interact with each other. This is the **logical view** of our application. On the other hand, operations people would focus more on how these components are deployed and running. This is the **physical view**. It is important for us to understand these two views as different people will care about each view more. 

The **logical view** often affects the application source code as how components will communicate with each other, or how to obtain the credentials to access a specific component are codified at the application source code. This is basically why developers need to care about the logical view of an application.

The **physical view** will determine the k8s resource definitions that will be used, whether a secret will be materialized as an Environment variable or as a file. If the application will have multiple replicas, what containers will be used, what ports will be used to listen,...  This is basically why operators or appsOps need to care about the physical view of an application.


## k8s application physical view

![Application physical view](/images/posts/develop_apps_in_k8s/app_physical_view.png)

In Kubernetes, when we look into the physical view of an application, we see that it consists of a set of `containers images`, containing runnable code, there’s also a set of `resource descriptors` that describe every component of the application, and for each instance of our application there will be a set of `environmental configuration` that gives our application different characteristics at runtime, these could be from just the name of the instance to any possible configuration. In order to have an application we need to have these three things defined, and hopefully under control.

Although not so relevant for the developers, as I mentioned previously, the physical view of an application as here described is still important, mostly because Kubernetes doesn't provide application abstractions that can make developers forget about this. But this will be the topic of the next article in the series.