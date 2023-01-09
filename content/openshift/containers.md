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

# Virtual Machines
Add an image of virtual machine and normal machine deployment
Expalination of virtual machines

## Advantages of using Virtual Machines for deploying applications
How using virtual machines could solve some of the problems of application development and deployment.

# Containers
Explaination of containers

## Advantages of using Containers over Virtual Machines
How containers solved leftover problems in virtual machine deployment
Virtual Machines or container based development is microservice application development

# Container Architecture
High level architecture of containers
Pictures from any open platform?

# Open Container Initiative (OCI)
Explaination of OCI and their mission
Link to OCI website

# Podman
What is podman and how it used?
Developed by Red Hat

# Docker
Even more popular container platform

# Skopeo

# Buildah