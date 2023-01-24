---
author: "Avnish"
title: "Container Architecture"
date: "2023-01-27"
description: "Creation and execution of container is enabled using features such as namespaces, Cgroups, seccomp and SELinux"
tags: ["container", "podman", "docker","openshift", "kubernetes", "linux", "namespaces", "cgroups", "seccomp", "selinux"]
categories: ["Microservices", "Operating Systems"]
series: ["OpenShift"]
aliases: ["container-architecture"]
ShowToc: true
TocOpen: false
cover:
  image: "container_arch.drawio.svg"
  linkFullImages: true
  alt: "The features essestial for containers"
  caption: "The features essential for containers"
  relative: false
---

To isolate process in <a href="/openshift/containers" target="_blank">containers</a> from their host, the container engines uses following four features provided by the OS:
* Namespaces
* Control Groups
* Secure Computing
* SELinux

## Namespaces
**Namespaces** are used to limit the reach of container to its host's resources. This helps with security and well as limiting the resources available to the container.

On GNU/LINUX based systems you can use command `lsns` for listing details of all the namespaces.

The namespaces essential for the container creation are: User, Mount, Unix Timesharing System, Process ID, Network, and Inter-Process Communication.

### User
The users and groups created inside the container are completely different from its host, they have their own UID and GID. Processes running inside the container as a `root` user could be mapped to a non-root user on the host.

Using the `id` command you can verify that the containers are using different user namespace than other processses on your host.

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
Each process running on the OS has a unique Process ID (PID) assigned to it. The processes running inside the container have their own PIDs which is separate from the host. Due to process ID isolation a container can't access the details of processes running on its host.   

To fetch the list of PID namespaces you can use the command:

```bash
$ lsns -t pid
        NS TYPE NPROCS   PID USER  COMMAND
4026531836 pid     128  4996 user /usr/lib/systemd/systemd --user
4026533251 pid      16  4525 user nginx: worker process
...
```

Just like other processes, containers also have PIDs assigned to them by host. You can fetch the PID of a running container using following command: 

```bash
$  docker inspect -f '{{.State.Pid}}' deploy-hugo-server-1
7172
```

`ps aux` command could be used list the running processes on the system along with their details.

```bash
$ ps aux | grep 7172
root        7172  0.0  0.4 759596 68860 ?        Ssl  Jan22   0:32 /usr/lib/hugo/hugo server --buildFuture --bind=0.0.0.0
```

### Mount (`mnt`)
By using different mount namespaces for different process the user can ensure that they can't access each other's files. To view the filesystems mounted on your system, you can use the `df` command.

```bash
$ df
Filesystem     1K-blocks      Used Available Use% Mounted on
devtmpfs            4096         0      4096   0% /dev
tmpfs            7849324     42384   7806940   1% /dev/shm
tmpfs            3139732      2444   3137288   1% /run
...
```

The container has its own file system hierarchy which could be viewed if we use `df` command insider container.
```bash
root@14ed72afd62e:/usr/local/apache2# df
Filesystem     1K-blocks      Used Available Use% Mounted on
overlay        498443264 308159584 185270656  63% /
tmpfs              65536         0     65536   0% /dev
tmpfs            1569860       276   1569584   1% /etc/hosts
...
```

but it could be viewed by the host in file `/proc/<CONTAINER_PID>/mounts`.
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
Container have their own hostnames assigned to them due to UTS namespace. We can verify this with `hostname` command.

On host:
```bash
[username@host-1 ~]$ hostname
host-1
```

Inside container:
```bash
root@14ed72afd62e:/usr/local/apache2# hostname
14ed72afd62e
```

### Network
Each container has its own IP address and network ports assigned to it. This is achieved using network namespaces. It allows the developer to run multiple processes with their own IP address and network ports.

To access or communicate with a process inside container the a network port has to be exposed from the container and port forwarding should be established from the host.

### Inter-Process Communication (IPC)
Processes in same IPC namespace can share the resources such as memory, semaphores, and message queues. Keeping containers in a separate IPC namespace ensures that the processes inside container cannot access the resources being used by host's processes.

### Time
Time namespaces are available since the release of Linux Kernel 5.6.  
Maybe sometime in future containers can have different time than their host.

## Control Groups (Cgroups)
A **control group** is created to effectively allocate resources of the OS to the processes residing in it. These Cgroups are hierarchial i.e. a child Cgroup could be spawned from the parent and it will inherit its certain attributes.

By creating a Cgroup a process in it could be prioritized, paused, removed or resumed based on the resources allocated to it. This also helps in monitoring the resources used by a particular processes.

If you are using an OS with `systemd` init system (to verify this you can use command `ps -p 1 -o comm=`) then you can use the command `systemctl list-units` to list all the cgroups. It will open a table containing....

The names of the Cgroup will be in form `<parent-cgroup>.<child-cgroup>` like `sys-devices-platform-serial8250-tty-ttyS0.device`. To view the hierarchy of cgroups you can use the command `systemd-cgls`. It presents cgroups in a tree structure like:

```bash
Control group /:
-.slice
├─user.slice (#1309)
│ └─user-1000.slice (#10010)
│   ├─user@1000.service (#10106)
│   │ ├─session.slice (#10394)
│   │ │ ├─dbus-broker.service (#10442)
│   │ │ │ ├─ 5088 /usr/bin/dbus-broker-launch --scope user
``` 

## Secure Computing (Seccomp)
Using **Secure Computing** or seccomp you can disable the system calls your process can make. This restricts the container to make any changes to the host that are not necessary.

## Security-Enhanced Linux (SELinux)
**SELinux** is a security architecture for Linux based OS that defines the access to files and processes. This is enforced on users or processes to restrict their access to the resources.

SELinux checks the *SELinux context* of the file or process to make decisions related to access control.

To view the SELinux context of a file use command `ls -Z <FILENAME>` and to view it for a process use command `ps -eZ | grep <PROCESS_NAME>`.

# External Resources

<a href="https://www.youtube.com/watch?v=sK5i-N34im8" target="_blank">Cgroups, namespaces, and beyond: what are containers made from?</a>  
<a href="https://opensource.com/article/21/8/container-linux-technology" target="_blank">4 Linux technologies fundamental to containers</a>  
<a href="https://man7.org/linux/man-pages/man1/init.1.html" target="_blank">systemd(1) — Linux manual page</a>  
<a href="https://www.redhat.com/sysadmin/7-linux-namespaces" target="_blank">The 7 most used Linux namespaces</a>  
<a href="https://www.phoronix.com/news/Time-Namespace-In-Linux-5.6" target="_blank">It's Finally Time: The Time Namespace Support Has Been Added To The Linux 5.6 Kernel</a>  
<a href="https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/resource_management_guide/sec-obtaining_information_about_control_groups" target="_blank">Obtaining Information about Control Groups</a>  
<a href="https://man7.org/linux/man-pages/man2/seccomp.2.html" target="_blank">seccomp(2) — Linux manual page</a>  
<a href="https://www.redhat.com/en/topics/linux/what-is-selinux" target="_blank">What is SELinux?</a>  