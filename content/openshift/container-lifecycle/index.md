---
author: "Avnish"
title: "Container Lifecycle"
date: "2023-02-03"
description: "The lifecycle of a container consists of different states such as running, paused, stopped and deleted. They are managed using the utilities provided by container engine."
tags: ["container","podman","docker","skopeo","buildah","openshift","kubernetes","container-image","container-registry"]
categories: ["Microservices", "Cloud Computing"]
series: ["OpenShift"]
aliases: ["container-lifecycle"]
ShowToc: true
TocOpen: false
cover:
  image: "container_lifecycle.drawio.svg"
  linkFullImages: true
  alt: "Commonly used commands for the management of containers"
  caption: "Commonly used commands for the management of containers"
  relative: false
---

<a href="/openshift/containers#container-engine" target="_blank">Container Engines</a> such as Podman and Docker provide GUI and CLI utilities for managing the state of containers. They also provide features such as container image management, metrics, logging and debugging tools for containers.

For the examples in this article I'll be using Podman container engine with skopeo and buildah utilities but the CLI commands are mostly interopable with Docker CLI. 

To install podman on your system you can follow: <a href="https://podman.io/getting-started/installation" target="_blank">Podman Installation Instructions</a>. You can also install <a href="https://podman-desktop.io/" target="_blank">Podman Desktop</a> which provides a graphical interface for managing containers, images and other resources created by podman.

# Accessing Container Images
To create a container first you should have its container image on your system. A *container image* is a file that contains all the dependencies and executable code for the container. You can fetch images from container registries such as DockerHub or Quay.io.

## Login to Container Image Registry
To access private container images or bypassing the rate limit enforced by the registry you can login to container registry from your container engine.

```bash
podman login docker.io
```

The prompt will ask for username and password for the container registry so you have to first create an account on it by visiting its website.

After login is successful, Podman stores the encrypted user information in file `${XDG_RUNTIME_DIR}/containers/auth.json`.

## Pull Container Images
```bash
podman pull
```

# Managing Container Lifecycle
## Create a Container
```bash
podman run
```

## Pause an Executing Container
```bash
podman pause
```
```bash
podman unpause
```

## Stop an Executing Container
```bash
podman stop
```
```bash
podman start
```

### Difference between stopping and pausing a container

## Execute Commands inside a Container
```bash
podman exec
```

## Attach Volumes
```bash
podman run -v ::Z
```
<!-- Something about selinux context change of file and Z -->

## Port Forwarding
```bash
podman run -p ::
```

# Managing Container Images
## Build Container Images
<!-- Creating containerfile and details of containerfiles -->
<!-- About buildah and how podman uses buildah in background -->
```bash
podman build
```

## Tag Container Images
```bash
podman tag 
```

## Push Container Images
```bash
podman push
```

## Copy Container Images between Registries
<!-- About skopeo tool and copying images between registries or repositories -->

## Deleting containers and container images
```bash
podman rm
```

```bash
podman rmi
```

# Metrics and Logging
## Statistics for Containers
```bash
podman stats
```

## Accessing Logs from a Container
```bash
podman logs
```

# External Resources
<a href="https://docs.podman.io/en/latest/markdown/podman-login.1.html" target="_blank">podman-login</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-login.1.html" target="_blank">podman-login</a>  