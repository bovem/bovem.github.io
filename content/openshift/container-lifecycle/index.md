---
author: "Avnish"
title: "Container Lifecycle"
date: "2023-02-03"
description: "The state of a container is managed using the utilities provided by container engines."
tags: ["container","podman","docker","skopeo","buildah","openshift","kubernetes","container-image","container-registry"]
categories: ["Microservices", "Cloud Computing"]
series: ["OpenShift"]
aliases: ["container-lifecycle"]
ShowToc: true
TocOpen: false
cover:
  image: "container_lifecycle.drawio.svg"
  linkFullImages: true
  alt: "Lifecycle of a container"
  caption: "Lifecycle of a container"
  relative: false
---

Container Engines like Podman and Docker provide GUI and CLI utilities for managing the state of containers. They also provide features such as container image management, metrics, logging and debugging tools. The examples in this article use Podman but CLI commands are mostly interopable with Docker. 

We can install Podman on your system by following the steps in: <a href="https://podman.io/getting-started/installation" target="_blank">Podman Installation Instructions</a>. Podman also provides a graphical interface for managing containers, images and other resources called <a href="https://podman-desktop.io/" target="_blank">Podman Desktop</a> .

# Accessing Container Images
To create a container, first we have to download its image from a registry. A **container image** is a file that contains all the dependencies and executable code for the container. 

Images could be fetched from container registries such as  <a href="https://hub.docker.com/" target="_blank">DockerHub</a> or <a href="https://quay.io/search" target="_blank">Quay.io</a>.

## Login to Container Image Registry
A **container image registry** is a service that handles the storage and distribution of container images. It is necessary to login on container registry to access private images.

```bash
podman login docker.io
```

After executing this command a prompt will ask for your username and password, for that we need to have a container registry account. Images used in this article are public so we don't need to create an account or login.

Once login is successful, Podman stores the encrypted user information in file `${XDG_RUNTIME_DIR}/containers/auth.json`. `XDG_RUNTIME_DIR` is an environment variable set by `systemd` that stores path of the directory containing user-specific runtime files.

## Pull Container Images
<p align="center"><img src="/openshift/container-lifecycle/image_pull.png" alt="Pulling container image from registry"></p>
<p align="center"><small><i>Pulling container image from registries</i></small></p>

Public registries provide interface for searching images or Podman's subcommand `search` could also be used.

```bash
podman search httpd
```

Once we have the image name we can pull it on our machine using `pull` subcommand

```bash
podman pull docker.io/library/httpd 
```

or using Podman Desktop app you can go to **Images** -> **Pull an Image**.
<p align="center"><img src="/openshift/container-lifecycle/podman_desktop_image_pull.png" alt="Pulling container image using Podman Desktop"></p>
<p align="center"><small><i>Pulling container image using Podman Desktop</i></small></p>

Different versions/variations of container images are managed by **Tags**. The image tag `latest` is pulled by default. You can view the list of available tags on the registry's webpage or use `skopeo list-tags`. Skopeo requires the *transport* (`docker` in our case) to be mentioned explictily with image name.

```bash
skopeo list-tags docker://docker.io/library/httpd
```

# Managing Container Lifecycle
Once we have access to the container image we can easily create one or more containers.

## Create a Container
<p align="center"><img src="/openshift/container-lifecycle/create_containers.png" alt="Creating container from container image"></p>
<p align="center"><small><i>Creating container from container image</i></small></p>

We use `run` subcommand for creating containers. If the image specified in command is not present the system then Podman will attempt to pull it from the registry.

```bash
podman run --name httpd-test docker.io/library/httpd
```

After executing this command a container named `httpd-test` will be created on your system and its output will be attached to your terminal. To exit you can use the shortcut **Ctrl+C**. To run the container in background `--detach` flag could be used. 

`podman ps` command will list running containers on system.

On Podman Desktop, **Containers** section provides utilities for managing the state of containers.
<p align="center"><img src="/openshift/container-lifecycle/podman_desktop_create_containers.png" alt="Creating container using Podman Desktop"></p>
<p align="center"><small><i>Creating container using Podman Desktop</i></small></p>


## Execute Commands inside a Container
To run a container and access its shell we have to use options `--interactive` & `--tty`. 

`--interactive` will allow us to provide inputs to the process running inside container and `--tty` will attach a psuedo-terminal to container.

**Entrypoint** specifies a command that is executed during container intialization. It could be defined by default in image, but you can override it with `--entrypoint` flag.

```bash
podman run -it --name httpd-test \
               --entrypoint="/bin/bash" \
               docker.io/library/httpd
```

To execute a command or script inside container we use Podman's `exec` subcommand.

```bash
podman exec httpd-test echo "Hello, world"
```

## Attach Volumes
Directories from host could be mounted on a container. It is useful when scripts are edited outside container (an IDE/Code Editor on host) or the files have to shared between multiple containers.

To mount a directory from host to container flag `--volume` could be used with `run` subcommand and values are passed as `<HOSTDIR>:<CONTAINERDIR>`.

```bash
podman run -it --name python-test --detach \
               --entrypoint="/bin/bash" \
               --volume $(pwd):/app:Z \
               docker.io/library/python:3.10.6-slim-bullseye
```

This command starts a python container with the contents of present working directory (fetched from `pwd` command) mounted on `/app` directory inside container. Both directory paths have to be absolute.

Before mounting the host directory we have to change its <a href="/openshift/container-architecture/#security-enhanced-linux-selinux" target="_blank">SELinux</a> context to `container_file_t` otherwise we could encounter permission issues while accessing directory contents.To change SELinux context you can append either `:z` (if multiple containers need read-write access) or `:Z` (if only the current container needs read-write access).

<p align="center"><img src="/openshift/container-lifecycle/podman_desktop_volumes.png" alt="Accessing volumes on Podman Desktop"></p>
<p align="center"><small>Accessing volumes on Podman Desktop</small></p>

## Port Forwarding
In order to communicate with the services running inside container, port forwarding has to be established from the host using `--publish` flag in `run` subcommand. The port specified for host and container could be different for example the following command forwards the output from `httpd-test` container's port `80` to port `8080` on host. 

```bash
podman run --name httpd-test --detach \
           --publish 8080:80 \
           docker.io/library/httpd           
```
To view the output we can visit <a href="http://localhost:8080" target="_blank">localhost:8080</a> in our browser.
It is adviced that only the necessary network ports should be forwarded from container to host.

## Pause an Executing Container
<p align="center"><img src="/openshift/container-lifecycle/pause.png" alt="Pausing running container"></p>
<p align="center"><small><i>Pausing running container</i></small></p>

To demonstrate a paused container we will take help of the following Python script

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

Assuming that this script is saved in current directory with name `counting.py`, we run a container created from image `docker.io/library/python:3.10.6-slim-bullseye` in detached mode.

```bash
podman run -it --name python-test --detach \
               --entrypoint="/bin/bash" \
               -v $(pwd):/app:Z \
               docker.io/library/python:3.10.6-slim-bullseye
```

Then we execute the script inside the container in interactive mode

```bash
podman exec -it python-test python3 /app/counting.py
```

An infinite loop will be initiated and the output should be visible on terminal. Now we open a new terminal window and pause this container.

```bash
podman pause python-test
```

If we go back to first terminal we can observe that the script execution is paused. When unpaused the container will restart right where it left.

```bash
podman unpause python-test
```

## Stop an Executing Container
<p align="center"><img src="/openshift/container-lifecycle/stop.png" alt="Stop running containers"></p>
<p align="center"><small><i>Stop running containers</i></small></p>

While pausing a container stops its execution in current state, *stopping* a container kills the executing process.
```bash
podman stop python-test
```

Upon restarting container will run the entrypoint command, starting a new process.
```bash
podman start python-test
```

## Deleting containers
<p align="center"><img src="/openshift/container-lifecycle/delete.png" alt="Deleting containers"></p>
<p align="center"><small><i>Deleting containers</i></small></p>

Before we delete a running container we have to stop it or we can use `--force` flag alongwith the `rm` subcommand.
```bash
podman rm python-test
```

# Metrics and Logging
## List of containers
By default, `ps` subcommand fetches the list of running containers but to view all the containers regardless of their state we can use `--all` flag.
```bash
podman ps --all
```

## Resource Utilization Statistics
`stats` subcommand provides live statistics of resource utilization by containers.
```bash
podman stats
```

## Accessing Logs from a Container
To access the logs of a running/completed container we use `logs` subcommand. To view the live output from a running container `--follow` flag is added.

```bash
podman logs --follow httpd-test
```

To access the container logs from Podman Desktop we can click on container name in **Containers** section.

<p align="center"><img src="/openshift/container-lifecycle/podman_desktop_container_logs.png" alt="Accessing container logs from Podman Desktop"></p>
<p align="center"><small><i>Accessing container logs from Podman Desktop</i></small></p>

We can also attach a terminal to the container from **Terminal** tab.

# External Resources
<a href="https://podman.io/getting-started/installation" target="_blank">Podman Installation Instructions</a>  
<a href="https://podman-desktop.io/" target="_blank">Podman Desktop</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-login.1.html" target="_blank">podman-login</a>  
<a href="https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html#variables" target="_blank">XDG Base Directory Specification</a>  
<a href="https://hub.docker.com/_/httpd" target="_blank">httpd container image</a>  
<a href="https://hub.docker.com/_/python" target="_blank">python container image</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-search.1.html" target="_blank">podman-search</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-pull.1.html" target="_blank">podman-pull</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-run.1.html" target="_blank">podman-run</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-exec.1.html" target="_blank">podman-exec</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-pause.1.html" target="_blank">podman-pause</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-stop.1.html" target="_blank">podman-stop</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-stats.1.html" target="_blank">podman-stats</a>  
<a href="https://docs.podman.io/en/latest/markdown/podman-logs.1.html" target="_blank">podman-logs</a>  