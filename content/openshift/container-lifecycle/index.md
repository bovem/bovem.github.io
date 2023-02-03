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

After login is successful, Podman stores the encrypted user information in file `${XDG_RUNTIME_DIR}/containers/auth.json`. `skopeo` also uses this same file for container registry authentication. `XDG_RUNTIME_DIR` is an environment variable set by `systemd` that stores path of the directory containing user-specific runtime files.

## Pull Container Images
<p align="center"><img src="/openshift/container-lifecycle/image_pull.png" alt="Pulling container images from registries"></p>
<p align="center"><small><i>Pulling container images from registries</i></small></p>

You can search the container image to use by either visiting the Container Registry's website like <a href="https://hub.docker.com/" target="_blank">DockerHub</a>/<a href="https://quay.io/search" target="_blank">Quay.io</a> or you can use `podman` CLI.

```bash
podman search httpd
```

Once you have found the image you can pull using CLI or Podman Desktop app. For this article we will be using image: `docker.io/library/httpd`

```bash
podman pull docker.io/library/httpd 
```

or in Podman Desktop app you can go into **Images->Pull an Image** and enter the image name.
<p align="center"><img src="/openshift/container-lifecycle/podman_desktop_image_pull.png" alt="Pulling images using podman desktop"></p>
<p align="center"><small><i>Pulling images using podman desktop</i></small></p>

By default podman adds tag `latest` to the container image being pulled. You can view the list of available tags on the image's webpage or use `skopeo`. You have to append the *transport* (which is `docker` in our case) to the container image.

```bash
skopeo list-tags docker://docker.io/library/httpd
```

# Managing Container Lifecycle
Once you have access to the container image you can easily create multiple containers from it.

## Create a Container
<p align="center"><img src="/openshift/container-lifecycle/create_containers.png" alt="Creating containers from container images"></p>
<p align="center"><small><i>Creating containers from container images</i></small></p>

You can use the following CLI command for creating a container from the image. If the image is not present on your machine then it will be pulled from the registry.

```bash
podman run --name httpd-test docker.io/library/httpd
```

A container named `httpd-test` will created on your system and output from its terminal will be attached to your terminal. To exit you can use shortcut `Ctrl+C`. If you don't want the output of the container to be attached to your terminal you can use `--detach` flag.

On Podman Desktop the **Containers** section provides all the utilites for creating containers and changing their states.
<p align="center"><img src="/openshift/container-lifecycle/podman_desktop_create_containers.png" alt="Creating containers using podman desktop"></p>
<p align="center"><small><i>Creating containers using podman desktop</i></small></p>

You can use command `podman ps` to list all the running containers on your system.

## Execute Commands inside a Container
If you want to run the container and access its shell you can use options `--interactive` & `--tty`.

Entrypoint is a command that is executed when the container starts. It could be defined for the container by default but you can override with `--entrypoint` flag.

```bash
podman run -it --name httpd-test \
               --entrypoint="/bin/bash" \
               docker.io/library/httpd
```

If you want to execute a command such as a shell command inside a container or execute a script that's present inside the container you can use podman's `exec` subcommand

```bash
podman exec httpd-test echo "Hello, world"
```

## Attach Volumes
You can mount directories on the host to a directory inside the container. This is very useful for cases when you want to execute the code inside container but you can edit it from your host on your IDE/Code Editor.

To mount a directory to container you can use flag `--volume`. The values will be passed in form `<HOSTDIR>:<CONTAINERDIR>`.

```bash
podman run -it --name python-test -d \
               --entrypoint="/bin/bash" \
               -v $(pwd):/app:Z \
               docker.io/library/python:3.10.6-slim-bullseye
```

In the example above we ran a python container with present working directory (fetched from `pwd` command) mounted on container's `/app` directory. The path from host has to be an absolute path and not a relative path.

To apply the proper <a href="/openshift/container-architecture/#security-enhanced-linux-selinux" target="_blank">SELinux</a> context (`container_file_t`) on the files being mounted you have to append with either `:z` (if multiple containers need read-write access on the mounted files) or `:Z` (if only the current running container needs read-write access).

<p align="center"><img src="/openshift/container-lifecycle/podman_desktop_volumes.png" alt="Podman Desktop provides Volumes section to view container volumes on system"></p>
<p align="center"><small><i>Podman Desktop provides <b>Volumes</b> section to view container volumes on system</i></small></p>

## Port Forwarding
You can forward the port of the container to its host using `-publish` flag of `run` subcommand.
By doing this the you can communicate with a process running inside containers from host.

```bash
podman run --name httpd-test -d \
           --publish 8080:80 \
           docker.io/library/httpd           
```

The command above forwards the output from container's port `80` to host's port `8080`. You can view the output by visiting <a href="http://localhost:8080" target="_blank">localhost:8080</a> from your host.

Although you can forward all of the container's ports to the host using `--publish-all` command but it is advisable to forward only those ports which are used by application running inside the container.

## Pause an Executing Container
<p align="center"><img src="/openshift/container-lifecycle/pause.png" alt="Pausing running containers"></p>
<p align="center"><small><i>Pausing running containers</i></small></p>

To demonstrate the pausing container we can execute the following script

```python
# This script runs an infinite loop 
# with time interval of 2 seconds to slow down the output

import time
i=0
while True:
    print("Value of i: {}".format(i))
    time.sleep(2)
    i+=1
```

We can create this python script inside current directory with name `counting.py`.
Then we run a container created from image `docker.io/library/python:3.10.6-slim-bullseye` in detached mode.

```bash
podman run -it --name python-test -d \
               --entrypoint="/bin/bash" \
               -v $(pwd):/app:Z \
               docker.io/library/python:3.10.6-slim-bullseye
```

Then we execute the script inside the container in interactive mode

```bash
podman exec -it python-test python3 /app/counting.py
```

The infinite loop should start and output will be on your terminal. Now you can open a new terminal window for pausing this container.

```bash
podman pause python-test
```

If you go back to first terminal you will notice that the script execution is paused. If we unpause the container the script will start executing again right from where we left.

```bash
podman unpause python-test
```

## Stop an Executing Container
<p align="center"><img src="/openshift/container-lifecycle/stop.png" alt="Stop running containers"></p>
<p align="center"><small><i>Stop running containers</i></small></p>

While pausing a container stops its execution in current state. Stopping a container kills the currently running process inside the it.
```bash
podman stop python-test
```

Restarting the container will start the container with its entrypoint command, starting a new process.
```bash
podman start python-test
```

## Deleting containers
<p align="center"><img src="/openshift/container-lifecycle/delete.png" alt="Deleting containers"></p>
<p align="center"><small><i>Deleting containers</i></small></p>

Before deleting a running container you have to stop it or you can use `--force` flag alongwith the command.
```bash
podman rm python-test
```

# Metrics and Logging
## List of containers
By default the `ps` subcommand fetches the list of running containers but to view all the containers regardless of their states you can use `-a` flag.
```bash
podman ps --all
```

## Resource Utilization Statistics
`stats` subcommand provides live statistics of host's resource utilization by containers.
```bash
podman stats
```

## Accessing Logs from a Container
To access the logs of a running/finished container you can use `logs` subcommand. If you want the live output from a running container you can use flag `--follow`
```bash
podman logs --follow httpd-test
```

# External Resources
<a href="https://docs.podman.io/en/latest/markdown/podman-login.1.html" target="_blank">podman-login</a>  
<a href="https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html#variables" target="_blank">XDG Base Directory Specification</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-search.1.html" target="_blank">podman-search</a>  