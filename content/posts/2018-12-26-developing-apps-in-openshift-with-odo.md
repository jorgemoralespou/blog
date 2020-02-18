---
title: Developing applications on OpenShift in an easier way
date: 2018-12-26
tags: [openshift,development,local,devexp,odo]
author: jorgemoralespou
---
Have you ever developed applications on a platform like Red Hat OpenShift?

I’m a Java developer with more than 15 years of coding experience, and while I’ve been working with OpenShift for over three years now, I never found it easy to use or compelling as a day to day development platform. Why? There are many reasons to this question, but the key ones are, complexity and speed. Before you call me a troll, allow me to explain.

As a Java developer, one of the things that I’ve done for a long time has been to build using maven (I never got to gradle myself). Maven has provided me the ability to do almost anything I wanted with a simple command line tool. Almost anything meant that I typically had my runtimes installed and available on my local machine, and maven only had to deploy (or copy) the generated artifact into the appropriate location. Sometimes this even triggered a restart of my runtime, or just a reload if the integration with the runtime allowed for such fanciness.

It took me a long time to get used to maven. I bet that it was the same for most of you. But once I knew (more or less) how to use it, it was pretty cool. I could share my source code, with a pom.xml file to any of my colleagues and they could build and test the application, just the same way I did.

The bigger challenge back then was not creating the application, but ensuring that my fellow developers were running it the same way. The issue was that many of us installed our runtimes and databases locally, on heterogeneous hardware, probably with different operating systems, or at least different versions of the same OS.

The solution was to standardize our runtime environments. I was a fervent user of Vagrant. I created development environments using Vagrant, so all of us developers could use the same runtimes, with the same configuration and the same application that maven built.

Fast-forwarding some years: containers took the place of these standardized runtime environments, and Kubernetes environments took the place of orchestrating all the pieces required for an application to run. You could now use Kubernetes locally, on your laptop, using minikube or Minishift. But now, we needed to create containers rather than applications, as these are the new artifacts. This posed a new challenge to me, and probably to many of you.

Building containers with your application in it can be a simple or complex process. There are a variety of tools that makes the process simpler. Tools that take your source code, build it and incorporate the generated artifact into a container that already had the desired runtime your application would use.

OpenShift s2i is one of these mechanisms. Heroku and Cloud Foundry Buildpacks is another approach. As a Java developer, I feel that any of these opinionated approaches works much better than dealing with a Dockerfile yourself.

Now, the platform you use to run your application can also build your application, as long as it can access the source code for it. But the process is slower than what you’re used to. And while slower can work sometimes –typically those times where you’re not waiting for it to complete– there are other times when you need the build and deployment to finish promptly to start your validation and testing, or just to continue your development work.

How can we make this process as fast as possible but also have your applications running on these wonderful environments (OpenShift or Kubernetes)?

We’ve been pondering this problem ourselves for a long time. We need a fast inner development loop, that is, code, build, deploy, test, code, build, deploy, test, again and again. We don’t want to push our code to a git server for the platform to build it, as this is a longer process. Also, should I commit code to my git server if I’m not sure whether it works as it should?

With this premise in mind, we are building a command line tool that we call `odo`, which stands for `OpenShift Do`.

The logic behind this tool is simple. We deploy onto the platform specialized containers that know how to build an application, but that also have the runtime for the application included and already running. We then only need to push the application to this container. For this, we have the option of pushing the source code and the container will create the artifact for us, or we can just push the artifact if we have already built it locally on our local machine. The tool used to push the application will then instruct the container to reload the runtime with the new application in it. This will only reload the runtime and not the whole container/pod, making the process as fast as it would be if I host that runtime locally on my machine.

This is a development pod, which knows how to build and/or run my application. What is convenient though, is that we can use a regular s2i container –one that OpenShift uses– for the process. We don’t need any special or additional capabilities.

As an example is worth more than a thousand words:

![Example](/images/posts/develop-apps-with-odo/example.gif)

As you would have probably seen in the previous example, the process we went through is recognizable by you, even if you’re not an OpenShift or Kubernetes expert. How is that possible?

We took the decision to make the tool as user (a.k.a. developer) friendly as we could, which means, that we are aware many users don’t fully understand all the complex constructs a platform like OpenShift or Kubernetes provides. We wanted to make it simple, and immediately familiar. So we decided the language the tool was going to provide should be natural to developers.

Another important observation we embodied in the tool is that developers usually work on a single component (part of an application) at a time. So, while a developer can have a quite complex application (consisting of multiple deployed components or services), he or she is only going to work through the inner development loop of one of these components at a time. That means our CLI was built with the concept of a component at its center.

To create a component corresponding to the type of runtime of your application, you will select from one of the available component types in the catalog. Once you have the component created, you only need to push your code (or pre-built artifact) to it, the tool will do the rest. If you want to access your component, you create a URL. If you want to provide persistence for your component, you create storage for the component. If you want to configure your application, you create a config for your component.

All of these constructs have been designed with a developer experience in mind, and should be straightforward to use for most (if not all) developers, regardless of the programming language. They no longer need to know the specifics of the platform where their application will be deployed and running.

This is just an introduction to a project we’re developing. It’s currently under heavy development and only community supported. It’s already in a working state, although not complete as yet. We want to hear your feedback. If you want to try it, head to the project’s page and read the [installation instructions](https://github.com/redhat-developer/odo#installing-odo). If you have feedback, please, open an issue on the [project’s GitHub repository](https://github.com/openshift/odo).

In the coming weeks, we’ll be talking much more about this project. The current state, how specific features work, and what we have in mind for the future. Stay tuned!!!

Develop in your own style, forget about the platform!