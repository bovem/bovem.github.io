---
author: "Avnish"
title: "Network Functions"
date: "2023-06-07"
description: "Network Functions (like routers, firewalls, and DHCP servers) are an integral part of the massive network deployments by telcos"
tags: ["openshift", "kubernetes", "network-functions", "pnf", "vnf", "cnf", "networking", "containers"]
categories: ["Microservices", "Cloud Native", "Computer Networking"]
series: ["Kubernetes"]
aliases: ["network-functions"]
ShowToc: true
TocOpen: false
comments: false
---

**Telcos (Telecommunication companies)** deploy networks that are highly available, scalable, and resilient covering the entire country. Components like routers, firewalls, and DHCP servers (called **Network Functions**) are the building blocks of such large deployments. 

Traditionally, the network functions were deployed on proprietary hardware with application-specific integrated circuits and installed on the telco's premise (baremetal deployment). Such network functions are called Platform Network Functions (PNFs). 

PNFs present the following challenges:
- Hardware for network functions could not be moved between premises and redeployed easily.
- The deployment of PNFs required knowledge of proprietary hardware.
- Services deployed on top of PNFs are tightly coupled with the hardware. Customization, scaling, and configuration of services could require change/upgrade of hardware components.
- Access to PNFs is limited to the private cloud setup by the telco.

# Virtualized Network Functions (VNFs)
The concept of **Network Function Virtualization (NFV)** was introduced in 2012 by a group of leading telcos. It proposed the transition of network functions from proprietary hardware to virtual machines. Individual network functions could be provisioned as a Virtual Machine on top of a hypervisor (<a href="/posts/kubernetes/containers/#virtual-machines-vms" target="_blank">Virtualized Deployment</a>), such network functions are called **Virtualized Network Functions (VNF)**. 

Benefits of VNFs over PNFs
* VNFs could be deployed on any generic hardware as long as they are supported by the hypervisor. This eliminates the need for proprietary hardware for deployment.
* VNFs could be provisioned and moved easily between premises.
* The scalability of the network is improved.
* Overall reduction in power and cooling requirements.

Other than VNFs the NFV architecture comprises MANO (Management, Automation, and Network Orchestration) and NFVi (Network Function Virtualization infrastructure). 

MANO is a framework for the management and orchestration of all the resources and their lifecycle in NFV architecture. This includes the compute, storage, and network resources. NFVi refers to the infrastructure that provides the resources to VNFs an example could be OpenStack.

Software Defined Network (SDN) could be created on top of VNFs to further ease the management and configurability of the network.

## Example of a VNF: Infoblox
Infoblox provides cloud networking solutions through products that provide a unified network view, global load balancing, reporting, analytics, etc. 

The NFV provided by Infoblox has the following features:
- DNS, DHCP, and IP Address Management (IPAM) services.
- DNS Cache Acceleration Services (vDCA) improves the performance of DNS resolution across the network.
- Advanced DNS Protection (vADP) for networks deployed on OpenStack.
- Integration with other Infoblox products like Infoblox IPAM and Infoblox vDiscovery.

# Cloud-Native Network Functions (CNFs)
Now, network functions are transitioning from virtual machines to containers (<a href="/posts/kubernetes/containers/#containers" target="_blank">Containerized Deployment</a>). This will provide further benefits over PNFs like

- Improved scalability and efficient usage of resources.
- Ability to use a mix of private, public, and hybrid cloud as containers could be deployed and migrated easily on any cloud environment.
- Edge devices could be included in the network to improve its reach and latency to the end users.

Although <a href="/posts/kubernetes/container-network-interfaces/" target="_blank">CNIs</a> could be used to create and configure network interfaces between containers, for telco's use case a network function container could require network interfaces that could interact with the specialized hardware directly. In such cases, an <a href="/posts/kubernetes/kubernetes-operators/" target="_blank">operator</a> could be defined with custom resources that could procure the hardware directly to the workloads (containers) as network interfaces. CNFs themselves could also be packaged as Kubernetes operators.

## Example of a CNF: Nokia Cloud Mobility Manager
Nokia's Cloud Mobility Manager is a control plane network function for packet networks following the 3GPP (3rd Generation Partnership Project) standard.

It is designed to be deployed in a cloud-native environment as VNF (in OpenStack KVM or VMware vCloud environments) or as CNF (in Kubernetes using Red Hat OpenShift or standalone containers using Docker). It provides the following benefits

- Performance and scalability depending on the workloads
- Reliability and resiliency with fault monitoring, recovery, and overload control protection
- Multi-generation access (2G/3G/4G/5G)
 
# Transition from VNFs to CNFs
The journey from VNFs to CNFs is not easy due to the following challenges:
* Virtualization of a workload is relatively easier compared to its containerization.
* A container isolates the process running inside it using <a href="/posts/kubernetes/container-architecture/#namespaces" target="_blank">namespaces</a>. Some of the network function's workloads might not run as expected inside an isolated namespace.
* Containers share the kernel with their host and traditionally the deployment of network functions requires kernel hacks.
* The architecture of telco networks is complex. Migrating it to a container-based environment would not be easy.

In addition to this telcos work in a highly regulated environment where they are expected to have:
* Service Level Agreements (SLAs) for the availability of the network. Usually, this is 99.999% (~5.26 minutes in a year).
* Maintain the backward compatibility of the network with up to 50 years of legacy requirements.
* Tight regulation from governments and authorities.

To ease this transition we have CNF Test Suite and CNF Testbed

## CNF Test Suite
The **CNF Test Suite** is defined by the Telecom User Group (TUG) and Cloud Native Network Function Working Group (CNF WG) for telcos to test their network functions deployed in a cloud-native environment for cloud-native principles:

- Configuration: The configuration for CNF should be defined in a declarative manner in ConfigMaps, Operators, or any other resource.
- Compatibility, Installability & Upgradability: CNFs should work with Certified Kubernetes Products. CNFs should be distributed & deployed as Containers, Operators, or <a href="/posts/kubernetes/helm-charts/" target="_blank">Helm Charts</a>.
- Microservice: CNFs should be built as microservices.
- State: The state of CNFs should be stored in a custom resource or a database like `etcd`.
- Reliability, Resilience & Availability: Failures are inevitable in a non-carrier grade cloud environment. So a CNF should be reliable, resilient, and available during such failures.
- Observability & Diagnostics: CNFs should provide endpoints and data to establish the observability stack (metrics, tracing, and logging).
- Security: The containers associated with CNFs should be isolated from their host and other containers. They should also be verified against common CVE and other vulnerabilities.

## CNF Testbed
**CNF Testbed** provides reference code and test suites for benchmarking the performance and resiliency between network functions deployed as VNFs and CNFs.

It provisions the hardware for a Kubernetes and OpenStack cluster. After that, it deploys similar workloads on both environments to benchmark their performance using testing tools such as NFVbench.

<iframe src="https://bovem.substack.com/embed" width="100%" height="320" style="border:2px solid #EEE; background:white;" frameborder="0" scrolling="no"></iframe>

# Resources
<a href="https://www.redhat.com/en/topics/cloud-computing/what-is-telco-cloud" target="_blank">What is telco cloud?</a>  
<a href="https://www.redhat.com/en/topics/virtualization/what-is-nfv" target="_blank">What is NFV?</a>  
<a href="https://www.sdxcentral.com/networking/nfv/definitions/whats-network-functions-virtualization-nfv/nfv-elements-overview/nfv-mano/" target="_blank">What is NFV MANO?</a>  
<a href="https://www.techtarget.com/searchnetworking/definition/virtual-network-functions-VNF" target="_blank">What are Virtual Network Functions (VNFs)?</a>  
<a href="https://www.vmware.com/topics/glossary/content/network-functions-virtualization-nfv.html" target="_blank">What is Network Functions Virtualization (NFV)? | VMware Glossary</a>  
<a href="https://cloud.ibm.com/docs/vpc?topic=vpc-about-vnf" target="_blank">About virtual network functions over VPC</a>  
<a href="https://blogs.infoblox.com/community/how-infoblox-supports-network-functions-virtualization/" target="_blank">Infoblox Network Function Virtualization</a>  
<a href="https://www.redhat.com/en/topics/cloud-native-apps/vnf-and-cnf-whats-the-difference" target="_blank">VNF and CNF, what’s the difference?</a>  
<a href="https://www.nokia.com/networks/core-networks/cloud-mobility-manager/" target="_blank">Nokia Cloud Mobility Manager</a>  
<a href="https://www.youtube.com/watch?v=FvDJg2hZr0I&pp=ygUQbmV0d29yayBmdW5jdGlvbg%3D%3D" target="_blank">Lessons Learnt Developing and Deploying a Cloud Native Network Function - Paul Graham</a>  
<a href="https://github.com/cncf/cnf-testsuite/" target="_blank">cncf/cnf-testsuite</a>  
<a href="https://www.cncf.io/blog/2022/03/24/testing-cloud-native-best-practices-with-the-cnf-test-suite/" target="_blank">Testing cloud native best practices with the CNF Test Suite</a>  
<a href="https://github.com/cncf/cnf-testbed" target="_blank">cncf/cnf-testbed</a>  
<a href="https://www.youtube.com/watch?v=_fD_4FuU_jg&pp=ygUeY2xvdWQgbmF0aXZlIG5ldG93b3JrIGZ1bmN0aW9u" target="_blank">Cloud Native Network Function (CNF) Testbed - Taylor Carpenter & Denver Williams</a>  
<a href="https://www.youtube.com/watch?v=ZF5UJD7YJIw&pp=ygUIY25mIGNuY2Y%3D" target="_blank">K8s Best Practices for Telco Apps - Taylor Carpenter & Bill Mulligan</a>  
<a href="https://cloud.redhat.com/blog/building-cnf-applications-with-openshift-pipelines" target="_blank">Building CNF applications with OpenShift Pipelines</a>  
<a href="https://www.youtube.com/watch?v=IL4nxbmUIX8&pp=ygUeY2xvdWQgbmF0aXZlIG5ldG93b3JrIGZ1bmN0aW9u" target="_blank">Keynote: E2E 5G Cloud Native Network - Heather Kirksey, Azhar Sayeed & Fu Qiao</a>  
<a href="https://www.redhat.com/en/topics/edge-computing/telecommunications" target="_blank">Understanding edge computing for telecommunications</a>  