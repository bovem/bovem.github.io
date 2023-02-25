---
author: "Avnish"
title: "Container Images"
date: "2023-02-17"
description: "Container Images contain all the necessary executable code for the creation of containers."
tags: ["container","podman","docker","skopeo","buildah","openshift","kubernetes","container-image","container-registry"]
categories: ["Microservices", "Cloud Computing"]
series: ["OpenShift"]
aliases: ["container-images"]
ShowToc: true
TocOpen: false
cover:
  image: "container_image.drawio.svg"
  linkFullImages: true
  alt: "Building container images from containerfile"
  caption: "Building container images from containerfile"
  relative: false
draft: true
---

A container image is a static file that contains all the necessary resources (packages, configuration, other dependencies) required to provision a container. A container image consists of one or multiple layered filesystems and a *Manifest* file that contains the metadata for container image.

# Open Container Intiative (OCI) Image Specification
Open Container Initiative was started by The Linux Foundation in 2015 to provide
* Runtime specification
* Image specification
* Distribution specification
for container images.

A container image created from OCI Image specification should have
* Layered Filesystem: Packaged contents of the container.
* Image Index and Manifest: Contains a manifest list that stores the metadata for container images for multiple platforms.
* Image Configuration: Arguments and environment variables for the application running inside the container.

The images created from Docker, Podman and buildah follow OCI Image Specification.

## Layered filesystems
A container image is built upon multiple layered filesystems. Each only stores the new changes from the previous filesystem layer.

Every container image has a **base layer** that contains the basic utilities required to execute the container. It could be based upon an OS like Fedora, Kali, etc. Upon this base layer the developer can add more utilities, perform configuration changes, install dependencies, etc. and that will create additional layers on top of base layer, they will be called **image layers**. These layers are differentiated from each other using their a SHA256 checksum.

The base and image layers are read only. This helps with storage space consumption of container images as the layers could be reused among similar container images. It also helps with build time for the container image as during the build only the changed layers are recreated instead of all layers.

When a container is created a data state of image layers is generated and a wriable **container layer** is added on top of image layers. This layer stores all the changes at runtime (file creation, configuration changes, etc.) and deleted with the container.

## Image Index and Manifest
The **manifest** of a container image contains the metadata related to the filesystem layers, the command to be executed once the container is provisioned and other information required by the Container Runtime.

To view the manifest of a container image from Podman you can use `manifest` subcommand with `inspect` option.

```bash
podman manifest inspect httpd:latest
```

The output of the command will be a JSON document similar to this
```json
{
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
    "manifests": [
        {
            "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
            "size": 1366,
            "digest": "sha256:d866e5c91f31fc6a122aaf37149cc67ba2ca0de68ae73ab206747a190937967e",
            "platform": {
                "architecture": "amd64",
                "os": "linux"
            }
        },
        {
            "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
            "size": 1366,
            "digest": "sha256:32588e5c7552750100ad3110a64d163b1881ce92216d67e828c42d3322c439d1",
            "platform": {
                "architecture": "arm",
                "os": "linux",
                "variant": "v5"
            }
        },
        {
            "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
            "size": 1366,
            "digest": "sha256:67586a7e127abd9b362884172b575a43b3342725ae310c339f4f7d5e5bdba918",
            "platform": {
                "architecture": "arm",
                "os": "linux",
                "variant": "v7"
            }
        },
        {
            "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
            "size": 1366,
            "digest": "sha256:bc5f484630b50cec12a50035d22ed717d980c52c9871105e91e276ebcbee69a2",
            "platform": {
                "architecture": "arm64",
                "os": "linux",
                "variant": "v8"
            }
        }
    ]
}
```

## Configuration

# Building container images
## Containerfile
<!-- Creating containerfile and details of containerfiles -->
<!-- About buildah and how podman uses buildah in background -->
<!-- Write about FROM scratch image` -->
<!-- Write about what happens when RUN is exeucted i.e. a new container is created, command is executed and the container layer is added back -->
## Docker
## Buildah

# Exporting container images
## Tar.gz
## Pushing to registry

# Creating images from existing containers
## Commit and Push

# Copying container images between registries

# External Resources
<a href="https://opencontainers.org/about/overview/" target="_blank">About the Open Container Initiative</a>  
<a href="https://github.com/opencontainers/image-spec/blob/main/manifest.md" target="_blank">OCI Image Manifest Specification</a>  
<a href="https://github.com/opencontainers/image-spec/blob/main/layer.md" target="_blank">Image Layer Filesystem Changeset</a>  
<a href="https://github.com/opencontainers/image-spec/blob/main/config.md" target="_blank">OCI Image Configuration</a>  
<a href="https://docs.docker.com/storage/storagedriver/" target="_blank">About storage drivers</a>  