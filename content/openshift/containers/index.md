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
cover:
  image: ""
  # can also paste direct link from external site
  # ex. https://i.ibb.co/K0HVPBd/paper-mod-profilemode.png
  alt: "<alt text>"
  caption: "<text>"
  relative: false # To use relative path for cover image, used in hugo Page-bundles
ShowShareButtons: false
---

While creating an application in any programming language or framework you have to install its dependencies on your system.
This could include the compiler for the programming language, libraries and frameworks. If the environment for the deployment of this application is different then the whole process of dependency installation has to repeated.

You might have to change some configurations as well like setting up environment variables, making changes to the files.

This problem becomes more challenging once you start collaborating with other people. The complete installation process has to be repeated on every development machine. Even if a complete documentation of setup process is available the developers could have different environment. It is also possible that the developers want to work on two different projects simultaneously and there could be a conflict between their configurations (like different versions of framework).

<!-- Problem Statement: Deploying an application, The challenges
It works on my machine. Some xkcd comic strip?
This is called monolith application development.... -->

<!-- To solve this problem we can create an our development environment inside a virtual machine.
This virtual machine could be saved as a template and shared between developers to deploy on their own machines.
With some minor changes the same template could be used for the deployment of the application. -->

When an application deployed directly on the OS running on the physical hardware it is called **baremetal deployment**.

<p align="center"><img src="/openshift/containers/baremetal_deployment.png"></p>
<p align="center"><small><i>Baremetal Deployment</i></small></p>

# Virtual Machines (VMs)
A **virtual machine** like the name suggests is a machine running upon the OS installed on the physical hardware. It has its own virtual CPU, memory, networking interfaces, and other resources that is provided by a *hypervisor*.

A **hypervisor** is a program that is used to create and manage virtual machines. Some common hypervisors are:
* Oracle VM VirtualBox
* Hyper-V

## Advantages of using VMs for deploying applications

<p align="center"><img src="/openshift/containers/virtual_machine_deployment.png"></p>
<p align="center"><small><i>Virtualized Deployment</i></small></p>

So, what advantage does development over VMs provide over development on the OS deployed on the physical hardware?

1. Environment is persistent across the machines.  
If you create your application's development environment inside a VM, then you can share it with anyone else who is interested to run it themselves.
The environment will be same irrespective of the OS installed on physical hardware.
2. Load balancing is easier in production environment.
3. Effective distribution of physical hardware resources available.
4. Provides option to run multiple OS on single hardware.

# Containers
A **container** is a set of one or more isolated processes running on an OS, it could be on a VM or directly on the physical hardware. Usually, it requires less resources than a virtual machine and provides similar performance and benefits depending on your use case.  
  
The workloads running inside a container have a very limited exposure to the resources of its host i.e its CPUs, memory, network and storage. It uses the same kernel as its host, that's why you can't spin up a linux container on a machine running Windows (without priovisioning VM throught WSL or any other hypervisor).

## Advantages of using Containers over Virtual Machines
<p align="center"><img src="/openshift/containers/containerized_deployment.png"></p>
<p align="center"><small><i>Containerized Deployment</i></small></p>

* Relatively lighter compared to VMs
* Faster deployment
* More scalable

# Managing Containers
## Container Runtime
The container is executed through a **container runtime**. It sets up the namespaces for the container.

Examples of container runtimes:
* containerd: Developed by Docker and donated to Cloud Native Computing Foundation (CNCF). Used in Docker Engine.
* cri-o: Container Runtime commonly used in Kubernetes.

## Container Images
**Container image** is a file that contains all the dependencies and executable code for the container. It is a way of storing and sharing containers.

## Container Image Registries
A **container image registry** is a service that handles storage and distribution of container images.

Some of the common container registries are:
* DockerHub
* Quay.io

## Container Engine
When a container runtime is expanded with CLI/GUI utilites for
* Creation of containers
* Changing stage of the container (starting, stopping, resuming, deleting)
* Managing container images
* APIs for developers to create layered products on top of it

it becomes a **container engine**. Some examples of container engines are:
* docker
* podman

## Open Container Initiative (OCI)
Open Container Intiative was established in 2015 for providing open specifications for:
* Container Runtime
* Container Image
* Container Image Distribution

The objective is to establish a standardized format for the containers such that an OCI container image could be used between different container engines without any issues.

## Docker
One of the most commonly used container engine. It is developed by Docker, Inc.
It uses a daemon (`dockerd`) that provides all the management services for the container. This daemon runs with root privileges.

## Podman
Acronym for *Pod Manager tool* is a daemonless container engine originally developed by Red Hat.  

Unlike docker it dosen't use any daemon for the interactions with container.
It also has other security focused features like the containers don't run as root by default. This reduces the attack surface as the host system cannot be accessed from the inside of the container.

### Skopeo
`skopeo` is a CLI utility used alongside `podman` for management, inspection and transfer of container images.

### Buildah
`buildah` is another CLI utility used alongside `podman` and `skopeo` for building OCI container images.

# External Resources
* [Oracle VM VirtualBox](https://www.virtualbox.org/)
* [Hyper-V](https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/about/)
* [containerd](https://containerd.io/)
* [cri-o](https://cri-o.io/)
* [Open Container Initiative](https://opencontainers.org/)
* [docker](https://www.docker.com/)
* [podman](https://podman.io/)
* [skopeo](https://github.com/containers/skopeo)
* [buildah](https://buildah.io/)