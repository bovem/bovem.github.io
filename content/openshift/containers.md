---
author: "Avnish"
title: "Containers"
date: "2023-01-03"
description: "Isolated processes for deploying applications"
tags: ["container", "podman", "docker","openshift", "kubernetes"]
categories: ["Microservices", "Cloud Computing"]
series: ["OpenShift"]
aliases: ["containers"]
ShowToc: true
TocOpen: true
---

While creating an application in any programming language or framework you have to install its dependencies on your system.
This could include the compiler for the programming language, libraries and frameworks. If the environment for the deployment of this application is different then the whole process of dependency installation has to repeated.

You might have to change some configurations as well like setting up environment variables, making changes to the files.

This problem becomes more challenging once you start collaborating with other people. The complete installation process has to be repeated on every development machine. Even if a complete documentation of setup process is available the developers could have different environment. It is also possible that the developers want to work on two different projects simultaneously and there could be a conflict between their configurations (like different versions of framework).

<!-- Problem Statement: Deploying an application, The challenges
It works on my machine. Some xkcd comic strip?
This is called monolith application development.... -->

To solve this problem we can create an our development environment inside a virtual machine.
This virtual machine could be saved as a template and shared between developers to deploy on their own machines.
With some minor changes the same template could be used for the deployment of the application.

# Virtual Machines (VMs)
A **virtual machine** like the name suggests is a machine running upon the OS installed on the physical hardware. It has its own CPU, memory, networking interfaces, and other resources that is provided by a *hypervisor*.

A **hypervisor** is a program that is used to create and manage virtual machines. Some common hypervisors are:
* Oracle VM VirtualBox
* Hyper-V

## Advantages of using VMs for deploying applications
So, what advantage does development over VMs provide over development on the OS deployed on the physical hardware?

1. Environment is persistent across the machines.  
If you create your application's development environment inside a VM, then you can share it with anyone else who is interested to run it themselves.
The environment will be same irrespective of the OS installed on physical hardware.
2. Load balancing is easier in production environment.
3. Effective distribution of physical hardware resources available.
4. Provides option to run multiple OS on single hardware.

# Containers
A **container** is an isolated process running on an OS, it could be on a VM or directly on the physical hardware. Usually, it requires less resources than a virtual machine and provides similar performance and benefits depending on your use case.  
The workloads running inside a container have a very limited exposure to the resources of its host i.e its CPUs, memory, network and storage. It uses the same kernel as its host, that's why you can't spin up a linux container on a machine running Windows.

## Advantages of using Containers over Virtual Machines
* Relatively lighter compared to VMs
* Faster deployment
* More scalable

# Container Architecture
High level architecture of containers
Pictures from any open platform?

## Cgroups
## Namespaces
## Container Engine
## Container Runtime

# Managing Containers
## Podman
What is podman and how it used?
Developed by Red Hat

## Docker
Even more popular container platform

## Skopeo

## Buildah

# External Resources
* [Oracle VM VirtualBox](https://www.virtualbox.org/)
* [Hyper-V](https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/about/)
* [Cgroups, namespaces, and beyond: what are containers made from?](https://www.youtube.com/watch?v=sK5i-N34im8)