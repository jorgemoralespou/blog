---
title: Developing applications on Kubernetes - Application definition
date: 2020-02-27
publishDate: 2020-02-27
tags: [kubernetes,appdev,cloudnative,devexp]
author: jorgemoralespou
---
Now that we have all our [required concepts]({{< ref "2020_02_24-develop_apps_in_k8s_and_not_die_trying-definitions.md" >}}) clear, it is time to consider how Kubernetes defines an **application**. Or to be more precise, the **lack** of a standard for **“Kubernetes application”**.

![Application definition](/images/posts/develop_apps_in_k8s/app_def.png)

Kubernetes is a project that exists for more than 5 years now and in all this time, we (application developers) have seen that there's no support for what we call [application]({{<ref "2020_02_24-develop_apps_in_k8s_and_not_die_trying-definitions.md">}}), but there are some efforts that are worth talking about.

## Kubernetes labels

The first idea that the Kubernetes community had was to standardize on a set of labels that would be set on every resource that are part of an [application](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/#applications-and-instances-of-applications). This is a basic concept embedded in the core of Kubernetes, **label selection**.
But, the biggest drawback I see to this approach is that, as a note on the Kubernetes doc say: 

> “Note: These are recommended labels. They make it easier to manage applications but aren’t required for any core tooling”

And if they are only a recommendation, they can not be really taken seriously. When we look into any of the latest Helm Charts that are published by companies like Bitnami, we can find that these labels [are set](https://github.com/bitnami/charts/blob/master/bitnami/fluentd/templates/_helpers.tpl#L34-L42), but if we do look at others, we find that are [missing](https://github.com/openshift/library/blob/master/official/eap/templates/eap72-basic-s2i.json#L32). Also when we look at applications deployed by Operators, these labels are [missing](https://github.com/openshift/etcd-operator/blob/master/doc/user/resource_labels.md). This seems to be the norm rather than the exception. For this reason, using labels is just a helper.

## SIG-APPS Application

Expanding on this approach, Kubernetes SIG-APPS created a [CRD](https://github.com/kubernetes-sigs/application) to define what an application is. Now that Kubernetes is extensible through the definition of Custom Resource Definitions (a.k.a CRD), we can define a new Kubernetes resource (object) that will define an application. This is basically what the Application CRD and Custom Controller meant to do. This CRD relies heavily on the application labels, which is consistent with the recommendation that the Kubernetes community has made, but the CRD doesn't seem to have gained much traction, although it seems to be back to life with some recent commits.

My opinion about this approach is that it's adding an additional resource definition to the already existing kubernetes resource definitions required (e.g. Deployment, Service, Ingress, PersistenceVolumeClaim) which really doesn't provide any simplification for the application developer. It also treats applications in a generic way, which can be fine as long as anything that [defines an application]({{<ref "2020_02_24-develop_apps_in_k8s_and_not_die_trying-definitions.md#what-is-an-application">}}) can be properly dealt with. When looked from an "ops" or "kubernetes ecosystem engineer" perspective, this CRD can provide some application management benefits, but definitely not for the application author and the application authoring process.

What I just said, in my humble opinion is key:

* **Who is defining an application definition?**
* **What's the goal for an application definition?**

As I described in the [intro article of the series]({{<ref "2020_02_21-develop_apps_in_k8s_and_not_die_trying-intro.md">}}) there's different type of users for Kubernetes, and the solutions that we're seeing seems fit for one type of users (or two) but not for the other, the application developer/application author.

Let's see what additional innitiatives do exist out there before jumping into conclusions.

## COTS - Commercial Off-The-Shelf applications

Some Commercial Off-The-Shelf software (and ISV provided software) uses a specific CRD to define their application. A dedicated Custom Resource for the specific application is quite handy from a consumer point of view (developer) because it defines a new resource type that the developer will most likely understand. These custom resources doesn’t help one understand what is the relation it has with all the constituent resources, but it gives us an easy way to create a specific applications. As an example, we can create a Couchbase cluster just by creating a new Kubernetes object of **kind: CouchbaseCluster**. The downside to this approach is that many vendors will create the definition of their application in different ways, but at least it's better than nothing. Also, an administrator might have difficulties to track down all the resources the application comprises, but this depends on the quality of the application author. Obviously, this is a really difficult solution to any developer as it involves the creation of resources that need to be deployed into the cluster by administrators only, so not any user can use these.

## CNAB

Docker, Microsoft and Pivotal proposed that the easiest way to have an application defined is to package everything together in what they defined as “[Cloud Native Application Bundle](https://cnab.io/)” (CNAB in short). CNAB proposes to facilitate the bundling, installing and managing of container-native applications and their coupled services. A CNAB bundle is a set of all the applications and deployment descriptors, plus the possible configuration definitions packaged in one single “transportable” artifact, that happens to be an “OCI image”. What this means is that CNAB is a solution for application distribution rather than for application definition or authoring. Still, developers need to create all the resources that will define the [physical view]({{<ref "2020_02_24-develop_apps_in_k8s_and_not_die_trying-definitions.md#k8s-application-physical-view">}}), plus they will need to code the logic of how the application will be deployed. The good thing is that users can now manage (install/configure/delete) applications through a dedicated CLI.

## OAM
There is yet another new definition being created: “[Open Application Model](https://oam.dev/)”. It is being defined mostly by Microsoft and Alibaba. This specification tries to separate concerns on application deployment responsibilities by defining 3 main actors, **“app developer”**, **“app ops”** and  **“infra ops”**. The first two focus on how the application will be deployed and run while the third should focus on the platform, Kubernetes. 
[This definition is still very immature](https://www.alibabacloud.com/blog/the-open-application-model-from-alibabas-perspective_595692) and is not backed by many Kubernetes contributors, so we will still need to wait to see if (and how) it progresses. The separation of concerns is something that IMHO makes sense but there's many other things in the implementation that are still very vague.


## Summary
In this article I've tried to summarize some of the ongoing efforts to define what is an application in Kubernetes. I haven't tried to go deep in any of the alternatives as I will be doing that over time in followup articles, where I will be giving a more thorough opinion on them. What I tried to raise in this article is the fact that in Kubernetes there's no concept of an application what makes the task of developing applications difficult. If one decides to favor one approach it might get into a rough spot when trying to make his/her application universal, as the options here described are not standard and one can not expect them to work everywhere (maybe except using labels).

In the next article of the series I will touch the application development stages and some of the existing tools in the ecosystem. Stay tuned!