---
author: "Avnish"
title: "Container Architecture"
date: "2023-01-27"
description: "Creation and execution of container is enabled using features like namespaces, Cgroups, seccomp, and SELinux"
tags: ["container", "podman", "docker","openshift", "kubernetes", "linux", "namespaces", "cgroups", "seccomp", "selinux"]
categories: ["Microservices", "Operating Systems"]
series: ["Kubernetes"]
aliases: ["container-architecture"]
ShowToc: true
TocOpen: false
cover:
  image: "container_arch.drawio.png"
  linkFullImages: true
  alt: "Linux features essestial for the containers"
  caption: "Linux features essential for the containers"
  relative: false
---

To isolate the processes running inside a <a href="/posts/kubernetes/containers/" target="_blank">container</a> from its host system, container engine uses the following four features:
* Namespaces
* Control Groups
* Secure Computing
* Security-Enhanced Linux

## Namespaces
**Namespaces** are created to limit the reach of a container to its host's resources. It helps with security and well as limits resources available to the container.

Linux command `lsns` could be used for listing details of namespaces.

The namespaces essential for containers are User, Mount, Unix Timesharing System, Process ID, Network, and Inter-Process Communication.

### User
The users and groups created inside a container are different from its host. Processes running inside the container as a `root` user could be mapped to a non-root user on the host.

Using the `id` command you can verify that the containers are present on a different user namespace than other processes on your host.

Running `id` on host:
```bash
$ id
uid=1000(username) gid=1000(username) groups=1000(username),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

Running `id` inside a container created from `docker.io/library/httpd:latest` container image:
```
root@14ed72afd62e:/usr/local/apache2# id
uid=0(root) gid=0(root) groups=0(root)
```

### Process ID (`PID`)
Each process running on the host has a unique Process ID (PID) assigned to it. The PIDs of processes running inside container are separate from PIDs assigned by the host. Due to process ID isolation, a container can't access the details of processes running on its host.   

To fetch the list of PID namespaces you can use the command:

```bash
$ lsns -t pid
        NS TYPE NPROCS   PID USER  COMMAND
4026531836 pid     128  4996 user /usr/lib/systemd/systemd --user
4026533251 pid      16  4525 user nginx: worker process
...
```

Just like other processes, containers also have PIDs assigned to them by the host. You can fetch the PID of a running container using the following command: 

```bash
$  docker inspect -f '{{.State.Pid}}' deploy-hugo-server-1
7172
```

`ps aux` command could be used to list the running processes on the system along with their details.

```bash
$ ps aux | grep 7172
root        7172  0.0  0.4 759596 68860 ?        Ssl  Jan22   0:32 /usr/lib/hugo/hugo server --buildFuture --bind=0.0.0.0
```

### Mount (`mnt`)
By using different mount namespaces for different processes we can ensure that they won't be able to access each other's files. 

You can use `df` command to view the filesystems mounted on your system.

```bash
$ df
Filesystem     1K-blocks      Used Available Use% Mounted on
devtmpfs            4096         0      4096   0% /dev
tmpfs            7849324     42384   7806940   1% /dev/shm
tmpfs            3139732      2444   3137288   1% /run
...
```

A container has its separate file system hierarchy which could be viewed by using `df` command on its shell.
```bash
root@14ed72afd62e:/usr/local/apache2# df
Filesystem     1K-blocks      Used Available Use% Mounted on
overlay        498443264 308159584 185270656  63% /
tmpfs              65536         0     65536   0% /dev
tmpfs            1569860       276   1569584   1% /etc/hosts
...
```

It could also be viewed by the host in the file `/proc/<CONTAINER_PID>/mounts`.
```bash
$ docker inspect -f '{{.State.Pid}}' deploy-hugo-server-1
7172

$ cat /proc/7172/mounts
proc /proc proc rw,nosuid,nodev,noexec,relatime 0 0
tmpfs /dev tmpfs rw,seclabel,nosuid,size=65536k,mode=755,inode64 0 0
devpts /dev/pts devpts rw,seclabel,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=666 0 0
...
```

### Unix Timesharing System (UTS)
Unix Time System (UTS) namespace allows containers to have hostnames. We can verify this with the `hostname` command.

On the host:
```bash
[username@host-1 ~]$ hostname
host-1
```

Inside a container:
```bash
root@14ed72afd62e:/usr/local/apache2# hostname
14ed72afd62e
```

### Network
Each container has a IP address and network ports assigned to it by its network namespace. It allows the developer to run multiple processes inside the container and expose them over different network ports.

To access or communicate with a process inside the container, port forwarding should be established from the host.

### Inter-Process Communication (IPC)
Processes in the same IPC namespace can share the resources such as memory, semaphores, and message queues. Keeping separate IPC namespaces ensures that the processes inside a container cannot access the resources used by the host's processes.

### Time
Time namespaces are available since the release of Linux Kernel 5.6.  
Maybe in the future containers can have a different time than their host.

## Control Groups (Cgroups)
A **control group** is created to effectively allocate resources of host its processes. These Cgroups are hierarchical i.e. a child Cgroup could be spawned from the parent and it will inherit its certain attributes.

By creating a Cgroup a process in it could be prioritized, paused, removed, or resumed based on the resources allocated to it. It also helps in monitoring the resources used by particular processes.

If you are using an OS with `systemd` init system (to verify this you can use the command `ps -p 1 -o comm=`) then you can use the command `systemctl list-units` to list all the Cgroups. It will open a table containing the Cgroup name, state, and description. The names of the Cgroup will be in the form `<parent-cgroup>.<child-cgroup>` like `sys-devices-platform-serial8250-tty-ttyS0.device`. 

To view the hierarchy of Cgroups you can use the command `systemd-cgls`. It presents cgroups as a tree structure.

```bash
Control group /:
-.slice
├─user.slice (#1309)
│ └─user-1000.slice (#10010)
│   ├─user@1000.service (#10106)
│   │ ├─session.slice (#10394)
│   │ │ ├─dbus-broker.service (#10442)
│   │ │ │ ├─ 5088 /usr/bin/dbus-broker-launch --scope user
...
``` 

## Secure Computing (Seccomp)
Using **Secure Computing** (or seccomp) you can disable the system calls your process can make to the host's kernel. 

A *seccomp profile* is a definition with a set of restricted and allowed system calls stored in a file. Default seccomp profile used by Docker: <a href="https://github.com/moby/moby/blob/master/profiles/seccomp/default.json" target="_blank">default.json</a>.

Docker allows you to define your seccomp profile for a container in JSON format. 
```bash
docker run --rm -it --security-opt seccomp=/path/to/seccomp/profile.json hello-world
```

## Security-Enhanced Linux (SELinux)
**SELinux** is a security architecture for GNU/Linux-based OS that defines access to files and processes. It is enforced on users or processes to restrict their access to the resources.

SELinux checks the *SELinux context* of the file or process to make decisions related to its access control. To view the SELinux context of a file use command `ls -Z <FILENAME>` and to view it for a process using the command `ps -eZ | grep <PROCESS_NAME>`. 



# Resources Referenced in this Article
<a href="https://www.youtube.com/watch?v=sK5i-N34im8" target="_blank">Cgroups, namespaces, and beyond: what are containers made from?</a>  
<a href="https://opensource.com/article/21/8/container-linux-technology" target="_blank">4 Linux technologies fundamental to containers</a>  
<a href="https://man7.org/linux/man-pages/man1/init.1.html" target="_blank">systemd(1) — Linux manual page</a>  
<a href="https://www.redhat.com/sysadmin/7-linux-namespaces" target="_blank">The 7 most used Linux namespaces</a>  
<a href="https://opensource.com/article/19/4/interprocess-communication-linux-storage" target="_blank">Inter-process communication in Linux: Shared storage</a>  
<a href="https://www.tenable.com/audits/items/DISA_STIG_Docker_Enterprise_2.x_Linux_Unix_v1r1.audit:f0aaa2cd4bcb66461902f47c69bf323b" target="_blank">DKER-EE-001250 - The Docker Enterprise hosts IPC namespace must not be shared.</a>  
<a href="https://www.phoronix.com/news/Time-Namespace-In-Linux-5.6" target="_blank">It's Finally Time: The Time Namespace Support Has Been Added To The Linux 5.6 Kernel</a>  
<a href="https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/resource_management_guide/sec-obtaining_information_about_control_groups" target="_blank">Obtaining Information about Control Groups</a>  
<a href="https://man7.org/linux/man-pages/man2/seccomp.2.html" target="_blank">seccomp(2) — Linux manual page</a>  
<a href="https://www.redhat.com/en/topics/linux/what-is-selinux" target="_blank">What is SELinux?</a>  
<a href="https://blog.christophersmart.com/2021/01/31/podman-volumes-and-selinux/" target="_blank">Podman volumes and SELinux</a>  