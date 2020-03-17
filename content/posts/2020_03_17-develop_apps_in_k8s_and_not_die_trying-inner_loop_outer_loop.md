---
title: Developing applications on Kubernetes - Inner Loop vs Outer Loop
date: 2020-03-17
publishDate: 2020-03-17
tags: [kubernetes,appdev,cloudnative,devexp]
author: jorgemoralespou
---
Finally we get to the last concept which is critical to understand development of applications on Kubernetes. If you're a developer, you might have already guessed what I'm talking about, because this is where you spend most of your day.

![Inner Loop - Outer Loop](/images/posts/develop_apps_in_k8s/inner_loop_outer_loop.png)

**Inner loop** is the cycle an application developer goes through before he can share his application with anyone. He will typically code, run, test, debug, code more, run again, test more, maybe debug again, and on and on. The feedback loop needs to be very **fast**. A developer will go through this iterations several tenths of times a day, and he doesn’t want to have much wait time in any of the steps in order to be productive and not desperate on the process. 

Once the code is ready to be shared, the application developer will push it into any type of version control system for the outer loop to be triggered. 

The **outer loop** is everything that can (and will) happen with that code after has been pushed into the version control system. From checking it out, building the runtime artifact (whatever that is), run continuous integration process, quality assurance, all the tests in possibly different environments, releases, promotions, deployments to non development environments. For these, the interaction of the application developer is minimal. Everything should be automated as much as possible. The developer will most likely be happy to get “asynchronous” feedback of the steps in the process. If something goes wrong he’ll probably be notified, so he can go back to the starting box in the inner loop.

![Inner Loop - Outer Loop](/images/posts/develop_apps_in_k8s/inner_loop_3.png)

As described before, a developer spends most of his time working on the inner loop. The faster inner loop is the one that happens locally, on your laptop, without using containers, where you minimise the steps present in the cycle, and you take advantage of performance improvements that some tools can provide, like background building in the IDE or auto-reload of your application when rebuilt, so that the time it takes for you to switch contexts (e.g. when switching from the IDE to the Browser to test) everything has already happened.

When working with containers and on Kubernetes, we are forced into additional phases/tasks. Building a container image, pushing that image to a registry where the cluster can locate it, pull that image into the cluster.

![Container development lead times](/images/posts/develop_apps_in_k8s/container_development_times.png)

These are tasks that add a big lead time, which is not really what a developer would expect. A developer wants to be as productive as he's now. If he's forced into a workflow that doesn't work for him, the reality is that he will either not adopt the workflow or be frustrated. He's used to see changes inmediately. He's used to what we call the "Control+Save" workflow.

**Developers want a "Control+Save" workflow on Kubernetes.**

![Control+S to Kubernetes](/images/posts/develop_apps_in_k8s/ctrl_save_to_k8s.png)

There are some tools that are becoming interesting when developing the inner loop of applications, because they implement the "Control+Save" workflow in a meaningful way, which means, removing all the additional steps that would make this workflow not viable. Those steps that containers add but that while in the inner-loop should be suppressed.
Some examples of these tools are [okteto](https://github.com/okteto/okteto), [tilt](https://tilt.dev/), [garden](https://garden.io/), [devspace](https://devspace.cloud/) and [odo](https://github.com/openshift/odo) if you’re using an OpenShift cluster.

## Final thoughts

One remaining question would be: **Are the tools that will be used throughout both loops the same?**

The answer is that probably not, although it's up to the tooling authors to consider inner and outer loop as two different workflows that developers could go through.

One thing is very clear to me, as a developer on Kubernetes, and it's that I would prefer to have **“consistency as a feature”** as much as possible, so that most of the tools I use they look alike or are perfect complement to each other, so that I don't need to learn way too many tools. 

To me, this means that I want the tools used for Inner loop to internally use the tools for the Outer loop, so that me (or my organization) can easily transition the applications I develop to production without too much burden.

Now that the series of articles on what matters when developing applications on Kubernetes is finished, I will take some time, in later posts, to share my thoughts on some of these tools.

**Happy coding in the meantime!!!**