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

<!-- We are going to be using podman in the following article but commands are mostly interopable with docker -->
<!-- Docker installation documentation -->

# Accessing Container Images
## Login to Container Image Registry
<!-- About container registries and repositories -->
```bash
podman login
```

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
<a href="https://www.youtube.com/watch?v=sK5i-N34im8" target="_blank">Cgroups, namespaces, and beyond: what are containers made from?</a>  