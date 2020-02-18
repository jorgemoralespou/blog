---
title: Care and Feeding of Minishift Development Environments
date: 2018-07-12
tags: [openshift,development,local,devexp,minishift]
author: jorgemoralespou
---

If you’ve heard of minishift, the OpenShift environment for your laptop, or if you’re using the Container Development Kit (CDK) at work, you’re probably building applications on the OpenShift Container Platform. This post is for you. If you’re new to OpenShift and these names aren’t familiar yet, check out the [this other minishift blog](https://blog.openshift.com/minishift-enterprise-installation/) first to get the most value from the content below.

## Upgrades: The Wisdom of Impermanence

Developers often ask how minishift installations should be upgraded. This question is especially relevant with minishift v1.19.0, because the operating system running in minishift’s virtual machine changed from Boot2Docker to CentOS. Devs usually worry about upgrading a minishift instance where they have their working application code deployed.

The first answer to that worry is very simple: Don’t upgrade the virtual machine. It’s possible to upgrade just the minishift binary to the latest version, without `start`ing a new instance or upgrading existing ones. Existing, older virtual machines will provide a perfectly suitable kernel environment for a long enough time to simply not worry about it in the short term.

## Minishift Instances as Ephemeral Workspaces
The second answer is more of a philosophy that helps me develop applications that are easy to test and move into production in any OpenShift environment, and it’s what lead to this article about minishift environment care. Treat minishift instances as ephemeral workspaces. Don’t expect them to run forever. Instead, concentrate on making your work easy to deploy on any instance. This not only uses your laptop’s resources more efficiently, but also helps foster good development practices.

I’m a developer advocate, which means that I develop applications on OpenShift to show other developers how to take advantage of OpenShift features and adjust application architectures to get the most out of the platform. I need to create demos that will execute repeatedly at many different times, and, most importantly, that are meant to run by others as well. This means even demo apps need to be ready to move between environments so they will run both “in development” on minishift on my own laptop, and “in production” on the laptops of curious programmers.

I usually find myself working on different projects and applications at different times throughout the day. Making such multitasking easier leads to a suggestion that makes our “ephemeral” philosophy a real-world practice.

## Have a separate minishift profile for each application you’re working on.
Minishift provides a concept called profiles. A profile is a named minishift instance that can be created, configured, started, used, stopped, and deleted, independent of those actions being taken on any other profile.

[How do you create a profile](https://docs.openshift.org/latest/minishift/using/profiles.html#creating-profiles)? It’s as easy as naming the profile you want to use. If the profile already exists, it will become the active profile, and minishift commands will be executed in it. If a profile of that name doesn’t exist, it will be created and made active.

```bash
minishift profile set careandfeeding
```

This command creates a directory named for the profile beneath your minishift profiles directory, in this case `$HOME/.minishift/profiles/careandfeeding`. The profile’s VM disk file and configuration are stored here.

You can list the known profiles:

```bash
minishift profile list
```

Now that you know what a profile is, I recommend creating a profile for every application or project you work on.

Here is an example workflow, showing how to switch between two profiles for two different applications, appA and appB:

```bash
# Create profile for application A
$ minishift profile set appA

# Start working on development of application A
$ minishift start

<code>
# oc new-app . --name appA</code>

# Finish work on application A
$ minishift stop

# Let’s work on application B
$ minishift profile set appB
$ minishift start

<code>
# oc new-app . --name appB</code>

# Finish work on application B
$ minishift stop
```

This workflow can be repeated as many times as needed. You might keep working on an application for a long time, which means that your daily workflow will be just starting minishift, working on your application, and then stopping minishift at the end of the day. The following day you can repeat this shortened workflow, until it’s time to work on another app. Then, you just need to activate that app’s profile and start its instance. While you can have more than one profile’s VM running at the same time, I don’t recommend it. It uses more computing resources on your laptop, and you can lose track of which profile is active and currently receiving your minishift commands. My habit is to run only the active profile and to stop minishift before switching to another profile and doing a `minishift start` there.

There are many advantages to this way of working, but the most important is that the amount of memory and CPU that your minishift instance will require will be kept to a minimum, which can be critical for many developers constrained by the limits of today’s laptop computers.

What are the drawbacks? In my opinion, the biggest drawback to the practice of multiple profiles is that each profile will use disk space. If you have many profiles, they’ll eventually consume a lot of disk space. This brings me to another recommendation.

## Delete long-inactive VMs – but not their Profiles

Notice we are talking about the actual VM disk image, and not the profile that contains it. A profile is more than just the VM. A profile maintains an instance’s configuration. You want to keep that.

```bash
# Use a profile to work as shown before
# When this app is complete or otherwise inactive for a long time
$ minishift delete
```

What kind of madness is this? We know the profile maintains the instance’s configuration, but deleting the VM usually means that the next time you need to work in this profile, minishift must bootstrap OpenShift all over again, pulling megabytes of OpenShift images from the internet. This leads to my third recommendation.

## Use minishift image caching to save container images on the host

Minishift provides a handy feature for [caching container images](https://docs.openshift.org/latest/minishift/using/image-caching.html) on a local filesystem. With image caching correctly configured, you will not need to download images again when a profile’s VM is recreated. Instead, images are copied from the filesystem cache to the active profile.

Image caching requires some configuration. We need to tell minishift which images it should cache. You can do that beforehand, when you know all the images needed for work in the profile, or after you have bootstrapped the VM in a profile for the first time.
Let’s walk through the process.

```bash
# Create profile for application A
$ minishift profile set appA

# Let’s activate image caching functionality for this profile
$ minishift config set image-caching true

# If we know we need to persist some images, let’s configure them before hand
$ minishift image cache-config add

# Start minishift and work
$ minishift start

# As we work, we might want to cache some other images.
# We can list the images contained in the VM
$ minishift images list --vm
MYIMAGE3
# And those stored locally in the cache
$ minishift images list

# Let’s cache another image added after starting the profile’s VM af
$ minishift image cache-config add

# cache-config view will list the
$ minishift image cache-config view

# Once its project is complete, we can garbage-collect the profile’s VM
$ minishift delete

# But with a new feature idea, we want to work on appA again
$ minishift profile set appA
$ minishift start
# This will fetch the profile’s images from the local cache without downloading them again.
```

## Minishift addons help manage additional dependencies
So far things are great. My VM is running, and it has everything I need to work on the next great feature for appA. Except for one additional dependency! Managing ancillary requirements and configurations like this is exactly what minishift addons are designed to do.

A [minishift addon](https://docs.openshift.org/latest/minishift/using/addons.html) is a script that extends the default minishift start behavior, so that we can provide additional bootstrap steps, deploy arbitrary software (like appA’s additional dependency!), or adjust the configuration of the OpenShift instance.

Add-ons can be shared between multiple users and multiple profiles, which makes them even more useful. A set of [default addons](https://docs.openshift.org/latest/minishift/using/addons.html#default-addons) are provided with minishift, and there is a [repository of community contributed addons](https://github.com/minishift/minishift-addons), too.

There are add-ons I consider essential and add to nearly every profile I create. Beyond those basics, I like to script any bootstrapping an application requires as an addon as well so I can manage it alongside the app’s source code in a version control system.

```bash
# Create a profile for appA
$ minishift profile set appA

# The admin-user addon goes into nearly every profile I create
$ minishift addon enable admin-user

# List the profile’s addons
$ minishift addon list
Default add-ons 'anyuid, admin-user, xpaas, registry-route' installed
- admin-user : enabled P(0)
- anyuid : disabled P(0)
- registry-route : disabled P(0)
- xpaas : disabled P(0)

# Install the addon for bootstrapping appA’s dependencies. This copies the addon to the profile folder and enables it.
$ minishift addon install addon/appA-deps --enable

# List the profile’s addons again
$ minishift addon list
Default add-ons 'anyuid, admin-user, xpaas, registry-route' installed
- admin-user : enabled P(0)
- anyuid : disabled P(0)
- registry-route : disabled P(0)
- xpaas : disabled P(0)
- appA-deps : enable P(0)
```

Now, every time I recreate the VM in this profile, these add-ons will be active, bootstrapping the environment I need. I have to create my application, everything else is ready to go when I start minishift in the appA profile.

What is great about this workflow is that the source control and add-ons for the app’s dependencies are stored in the same version control repository with its source code, making it easy to manage changes over time, and making the app repo a one-stop-shop for a future developer to get everything needed to get right to work.

Using profiles helps me keep my minishift environments up to date and my application code ready for deployment in any environment. Combining them with minishift addon scripts and the other practices I’ve outlined here makes recreating a set of dependencies for each instance easy, too. There are a few more things that I’d like to automate or manage more efficiently. While minishift doesn’t fully support this short list of improvements yet, you can see in the linked GitHub issues that work on them is in progress:

* Enable managing minishift configurations as source code, in a version control system, making it easier to replicate, manage, and share profiles ([#2531](https://github.com/minishift/minishift/issues/2531))
Create a profile from a template ([#1517](https://github.com/minishift/minishift/issues/1517)) and [#1649](https://github.com/minishift/minishift/issues/1649))
Improve cache speed and reusability of layers/images ([#2532](https://github.com/minishift/minishift/issues/2532))

Minishift helps me be more productive, and I try to model not just demo apps, but also processes, that mirror and advance how front-line application programmers work in the real world. With any luck, maybe you’ll find some of the tips from my experience useful. Drop me a like, comment or idea [@jorgemoralespou](https://twitter.com/jorgemoralespou) on Twitter.