---
author: "Avnish"
title: "Helm Charts"
date: "2023-05-16"
description: "Helm is a package manager for Kubernetes"
tags: ["openshift", "kubernetes", "helm", "helm-charts", "go", "artifacthub"]
categories: ["Microservices", "Cloud Native"]
series: ["Kubernetes"]
aliases: ["helm-charts", "helm"]
ShowToc: true
TocOpen: false
comments: false
---

Package managers like `dnf` and `apt` increase the convenience of installing, updating, and maintaining applications on operating systems. For developers, a package manager provides a standardized way of packaging and distributing their applications.

**Helm** is a package manager for Kubernetes. It is implemented in Go and installed as a binary `helm`. It interacts with the Kubernetes cluster using Kubernetes API.

# Charts
Helm distributes Kubernetes-based applications in a format called **Chart**.   
Charts can deploy all kinds of Kubernetes resources such as Deployments, Pods, Services, Persistent Volumes, etc.

## Structure of a Helm Chart
Chart's directory contains the following files and sub-directories:
* `Chart.yaml` contains the chart's metadata
* `charts/` directory contains dependencies (other charts)
* `crds/` directory contains the custom resources required by the chart
* `templates/` stores the templates for Kubernetes resources
* `values.yaml` stores the configuration values for templates

### `Chart.yaml`
The chart's metadata includes
* Chart's name
* Application's version
* Chart's type (`application` or `library`)
	* `application` charts package Kubernetes applications that could be installed directly on the cluster.
	* `library` charts are used by other charts to extend their functionality.
* Compatible Kubernetes versions
* Keywords related to the chart, its homepage, and link to the chart's source code
* Dependencies
* Contact information of chart's maintainers

### `templates` and `values`
Resource templates are based on the *Go template* convention. Example of a Deployment resource template
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: {{ .Values.replicaNum }}
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: {{ .Values.containerName }}
        image: {{ .Values.containerImage }}
        ports:
        - containerPort: {{ .Values.containerPort }}
```

The values to be substituted in this template will be provided from `values.yaml` file in the chart's root directory. Example of a `values.yaml` file for the template
```yaml
replicaNum: "3"
containerName: "nginx-test"
containerImage: "docker.io/library/nginx:1.14.2"
containerPort: "80"
```

The structure of the `values.yaml` file is defined in `values.schema.json`. Chart developers can also define the datatype of each value. Example of a `values.schema.json` file
```json
{
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "replicaNum": {
      "type": "string"
    },
    "containerName": {
      "type": "string"
    },
    "containerImage": {
      "type": "string"
    },
    "containerPort": {
      "type": "string"
    }
  },
  "required": [
    "replicaNum",
    "containerName",
    "containerImage",
    "containerPort",
  ],
  "title": "Values",
  "type": "object"
}
```

### `crds`
Custom Resources (CRs) are installed before any other resource is created by the helm chart. Some limitations imposed on the CRs created by the helm charts:
* YAML manifests of CRs could not be templated for values.
* CRs would not be reinstalled with a chart.
* Upgrades and rollbacks do not impact the CRs installed by the chart.
* Helm doesn't delete CRs upon uninstallation.

# Chart Repository
Helm charts are distributed through HTTP servers called **Repositories**. The metadata of available charts in the repository is stored in `index.yaml`.

A repository could be added using
```bash
helm repo add jenkins-x https://storage.googleapis.com/chartmuseum.jenkins-x.io 
```

Here `jenkins-x` is the name of the helm chart repository and `https://storage.googleapis.com/chartmuseum.jenkins-x.io` is the link to the repository.

Once a repository is added, it is recommended to update the information of available charts in the local cache.
```bash
helm repo update
```

The `repo` command also provides `list` and `remove` for listing and removing repositories respectively.

# Searching Helm Charts in Repositories and ArtifactHub
To search a helm chart within the added repositories the `search` command could be used
```bash
helm search repo tekton
```

<a href="https://artifacthub.io/" target="_blank">ArtifactHub</a> is a web platform that provides access to multiple Kubernetes-based applications including helm charts. `helm search` can also be used for searching packages in ArtifactHub
```bash
helm search hub tekton
```

# Installing Helm Charts
Each instance of a helm chart installation is called a **Release**. 

A release will be created after executing the following command.
```bash
helm install tekton-pipelines jenkins-x/tekton
```

Here, `tekton-pipelines` is the name of the release and `jenkins-x/tekton` is the name of the helm chart to be installed.

To install a local helm chart, the chart name will be substituted with the path to the chart's directory or archive
```bash
helm install test-helm-chart ./test-helm-chart
```

Configuration values are passed from a file using the `--values` flag.

During development, if developers want to inspect the manifests created during release and avoid installing the chart on a cluster then they can run the `install` command with `--debug` and `--dry-run` flags.

# Upgrading and Rolling Back Releases
Helm performs the least invasive upgrades on chart releases i.e. updates only the changed resources. The `upgrade` command is used to upgrade a chart release

```bash
helm upgrade -f newValues.yaml test-helm-chart ./test-helm-chart
```

Release `test-helm-chart` will be updated with values in `newValues.yaml`.

On every upgrade, the chart release's **revision number** will be incremented by 1. To view the history of releases with their respective revision numbers

```bash
helm history test-helm-chart
```

For some reason, if the chart upgrade doesn't go as planned it could be rolled back to a previous revision number.

```bash
helm rollback test-helm-chart 1
```

will rollback the `test-helm-chart` release to its first revision.

# Uninstalling Helm Chart
The `uninstall` command will remove all the resources created by the helm chart release from the Kubernetes cluster (except CRs). It will also wipe the history of the release, to retain history the `--keep-history` flag could be used with the command.

```bash
helm uninstall test-helm-chart
```

# Example of a Helm Chart: Grafana Helm Chart
Grafana is an analytics tool that provides utilities for creating dashboards with graphs and alerts. Using Grafana developers can create dashboards for monitoring services, presenting business metrics, etc.

<a href="https://artifacthub.io/packages/helm/grafana/grafana" target="_blank">Grafana's Helm Chart</a> will perform the following changes to the Kubernetes cluster
* Create pods and other workload resources required by Grafana
* Create services and security policies
* Create ConfigMaps and Secrets for pods
* Create PersistentVolumes
* Create RBAC resources

# Creating Helm Charts
The boilerplate code for helm charts is generated using `create` command. 

```bash
helm create test-helm-chart
```

will create a directory `test-helm-chart` containing basic chart resources (`Chart.yaml`, `charts/`, `templates/`, `values.yaml`, etc.).


# Testing a Helm Chart
Developers can define tests for their helm charts in the `templates` directory (or `templates/tests` for better organization). 

Tests are defined as Kubernetes Pods with the following annotation.
```yaml
  annotations:
    "helm.sh/hook": test
```

Test pods should finish with exit code `0` to be marked successful.

To execute tests defined with a release
```bash
helm test test-helm-chart
```

# Packaging Helm Charts
Charts are packaged as GZIP compressed archives with extension `tgz`. The `package` subcommand will create the archive from the chart's directory

```bash
helm package ./test-helm-chart
```

The archive could be signed (`--sign`) by its developer using a key passed with a `--key` flag. 

# Distributing Helm Charts

## Chart Repositories
The chart repository server contains
* an `index.yaml` containing chart's metadata
* charts packaged as archives (for example `test-helm-chart-0.0.1.tgz`)
* chart's provenance files (`test-helm-chart-0.0.1.tgz.prov`) containing integrity and validation data

A publicly available helm chart repository could be created using GitHub pages. Public helm chart repositories could also be added to ArtifactHub.

## OCI-based registries
Some OCI-based registries like DockerHub support helm chart distribution. To distribute a chart through DockerHub the user has to login to the registry through `helm`
```bash
helm registry login -u bovem docker.io
```

Here `bovem` is the DockerHub username and `docker.io` is the link to the DockerHub registry. To push the chart to DockerHub it has to be packaged as an archive
```bash
helm push test-helm-chart-0.0.1.tgz oci://docker.io/bovem
```

Similarly, a specific version of the chart could be pulled from DockerHub using the `pull` command.
```bash
helm pull oci://docker.io/bovem/test-helm-chart --version 0.0.1
```


<hr>
Thank you for taking the time to read this blog post! If you found this content valuable and would like to stay updated with my latest posts consider subscribing to my <a href="https://www.avni.sh/index.xml" target="_blank">RSS Feed</a>.

# Resources
<a href="https://docs.fedoraproject.org/en-US/quick-docs/dnf/" target="_blank">Using the DNF software package manager</a>  
<a href="https://helm.sh/" target="_blank">Helm</a>  
<a href="https://helm.sh/docs/topics/architecture/" target="_blank">Helm Architecture</a>  
<a href="https://helm.sh/docs/intro/install/" target="_blank">Installing Helm</a>  
<a href="https://helm.sh/docs/topics/charts/" target="_blank">Charts</a>  
<a href="https://helm.sh/docs/topics/chart_repository/" target="_blank">The Chart Repository Guide</a>  
<a href="https://artifacthub.io/packages/helm/jenkins-x/tekton" target="_blank">Tekton's Helm Chart</a>  
<a href="https://artifacthub.io/" target="_blank">ArtifactHub</a>  
<a href="https://artifacthub.io/packages/helm/grafana/grafana" target="_blank">Grafana's Helm Chart</a>  