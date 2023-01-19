---
author: "Avnish"
title: "Containers"
date: "2023-01-19"
description: "A container is a set of one or more isolated processes running on an Operating System"
tags: ["container", "podman", "docker","openshift", "kubernetes", "skopeo", "buildah", "vm"]
categories: ["Microservices", "Cloud Computing"]
# series: ["OpenShift"]
aliases: ["containers"]
ShowToc: true
TocOpen: false
---

While creating an application in any programming language or framework you have to install its dependencies on your system.
This could include the compiler for the programming language, libraries, and frameworks. 

If the environment for the deployment of this application is different then the whole process of dependency installation has to repeat. You might have to some other changes, like setting up environment variables and making changes to the configuration files.

This problem becomes more challenging once you start collaborating with other people. The complete installation process has to be repeated on every development machine. Even if complete documentation of the setup process is available the developers could have a different environment. It is also possible that the developers want to work on two different projects simultaneously and there could be a conflict between their configurations (like different versions of the framework).

When an application is deployed directly on the OS running on the physical hardware it is called **baremetal deployment**.

<p align="center"><img src="/openshift/containers/baremetal_deployment.png" alt="Deployment of application directly on physical hardware"></p>
<p align="center"><small><i>Baremetal Deployment</i></small></p>

# Virtual Machines (VMs)
A **virtual machine** as the name suggests is a machine running upon the OS installed on the physical hardware. It has its virtual CPU, memory, networking interfaces, and other resources that are provided by a *hypervisor*.

A **hypervisor** is a program that is used to create and manage virtual machines. Some common hypervisors are:
* Oracle VM VirtualBox
* Hyper-V

## Advantages of using VMs for deploying applications

<p align="center"><img src="/openshift/containers/virtual_machine_deployment.png" alt="Deployment of application using Virtualization"></p>
<p align="center"><small><i>Virtualized Deployment</i></small></p>

So, what advantage does development over VMs provide over development on the OS deployed on the physical hardware?

1. The environment is persistent across the machines.  
If you create your application's development environment inside a VM, then you can share it with anyone else who is interested to run it themselves.  
The environment will be the same irrespective of the OS installed on the physical hardware.
2. Load balancing is easier in the production environment.  
You can run multiple instances of the same application such that the load could be distributed effectively. If one of the instances is down (due to updates or outage) the traffic to it could be re-routed to another instance.
3. Effective distribution of physical hardware resources available.  
The hypervisor creates a pool of resources available to it from the OS. Then these resources could be effectively distributed among the VMs created. A deployment VM could use fewer resources compared to a development VM.
4. Provides an option to run multiple OS on a single hardware.


# Containers
A **container** is a set of one or more isolated processes running on an OS, it could be on a VM or directly on the physical hardware. Usually, it requires fewer resources than a virtual machine and provides similar performance and benefits depending on your use case.  
  
The workloads running inside a container have very limited exposure to the resources of its host i.e its CPUs, memory, network, and storage. It uses the same kernel as its host, that's why you can't spin up a Linux container on a machine running Windows (without provisioning the VM through Windows Subsystem for Linux or any other hypervisor).

## Advantages of using Containers over Virtual Machines
<p align="center"><img src="/openshift/containers/containerized_deployment.png" alt="Deployment of applicatino using containerization"></p>
<p align="center"><small><i>Containerized Deployment</i></small></p>

1. Relatively lighter compared to VMs  
  Containers don't need virtual resources. The size of the container could be shrunk down to the point where only the dependencies required for the application are present on it.
2. Faster deployment  
  Due to their smaller size compared to VMs they could be deployed faster.
3. More scalable  
  It is possible to execute multiple containers in the same amount of resources required by a VM for an application.

Depending on your use case the deployment could be a mix of both virtualization and containerization. Like a service comprising of multiple containers could be deployed on a VM.

# Managing Containers
## Container Runtime
The container is executed through a **container runtime**. It sets up the namespaces for the container.

Examples of container runtimes:
* containerd: Developed by Docker and donated to Cloud Native Computing Foundation (CNCF). Used in Docker Engine.
* cri-o: Container Runtime commonly used in Kubernetes.

## Container Images
A **container image** is a file that contains all the dependencies and executable code for the container. It is a way of storing and sharing containers.

## Container Image Registries
A **container image registry** is a service that handles the storage and distribution of container images.

Some of the common container registries are: DockerHub, Quay.io, etc.

## Container Engine
When a container runtime is expanded with CLI/GUI utility for
* Creation of containers
* Changing stage of the container (starting, stopping, resuming, deleting)
* Managing container images
* APIs for developers to create layered products on top of it

it becomes a **container engine**. Some examples of container engines are:
* Docker
* Podman

## Open Container Initiative (OCI)
Open Container Initiative was established in 2015 for providing open specifications for:
* Container Runtime
* Container Image
* Container Image Distribution

The objective is to establish a standardized format for the containers such that an OCI container image could be used between different container engines without any issues.

## Docker
One of the most commonly used container engines. It is developed by Docker, Inc.
It uses a daemon (`dockerd`) that provides all the management services for the container. This daemon runs with root privileges.

## Podman
The acronym for *Pod Manager tool* is a daemonless container engine originally developed by Red Hat.  

Unlike docker, it doesn't use any daemon for interactions with containers.
It also has other security-focused features like the containers don't run as root by default. This reduces the attack surface as the host system cannot be accessed from the inside of the container.

### Skopeo
`skopeo` is a CLI utility used alongside `podman` for the management, inspection, and transfer of container images.

### Buildah
`buildah` is another CLI utility used alongside `podman` and `skopeo` for building OCI container images.

# External Resources
<a href="https://www.virtualbox.org/" target="_blank">Oracle VM VirtualBox</a>  
<a href="https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/about/" target="_blank">Hyper-V</a>  
<a href="https://containerd.io/" target="_blank">containerd</a>  
<a href="https://cri-o.io/" target="_blank">cri-o</a>  
<a href="https://hub.docker.com/" target="_blank">DockerHub</a>  
<a href="https://quay.io/" target="_blank">Quay.io</a>  
<a href="https://opencontainers.org/" target="_blank">Open Container Initiative</a>  
<a href="https://www.docker.com/" target="_blank">Docker</a>  
<a href="https://podman.io/" target="_blank">Podman</a>  
<a href="https://github.com/containers/skopeo" target="_blank">Skopeo</a>  
<a href="https://buildah.io/" target="_blank">Buildah</a>  