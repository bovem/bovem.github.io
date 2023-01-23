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
Processes in same IPC namespace can share the resources such as memory, semaphores, and message queues. Keeping containers in a separate namespace than the host also ensures that the process inside the container cannot access the resources being used by host's processes.

### Time
Time namespaces are available since the release of Linux Kernel 5.6.  
Maybe sometime in future containers can have different time than their host.

## Control Groups (Cgroups)
A **control group** is created to effectively allocate resources of the OS to the processes residing in it. These Cgroups are hierarchial i.e. a child Cgroup could be spawned from the parent and it will inherit its certain attributes.

By creating a Cgroup a process in it could be prioritized, paused, removed or resumed based on the resources allocated to it. This also helps in monitoring the resources used by a particular processes.

If you are using an OS with `systemd` init system (to verify this you can ....) then you can use the following command to list all the cgroups: 

```bash
systemctl list-units
```
Its output will be something like:
```bash
  UNIT                                                                                          LOAD   ACTIVE SUB       DESCRIPTION                                                         >
  proc-sys-fs-binfmt_misc.automount                                                             loaded active running   Arbitrary Executable File Formats File System Automount Point       >
  sys-devices-pci0000:00-0000:00:02.2-0000:01:00.0-net-wlo1.device                              loaded active plugged   /sys/devices/pci0000:00/0000:00:02.2/0000:01:00.0/net/wlo1
  sys-devices-pci0000:00-0000:00:02.4-0000:02:00.0-nvme-nvme0-nvme0n1-nvme0n1p1.device          loaded active plugged   SAMSUNG MZVLQ512HBLU-00BH1 EFI\x20System\x20Partition
  sys-devices-pci0000:00-0000:00:02.4-0000:02:00.0-nvme-nvme0-nvme0n1-nvme0n1p2.device          loaded active plugged   SAMSUNG MZVLQ512HBLU-00BH1 2
  sys-devices-pci0000:00-0000:00:02.4-0000:02:00.0-nvme-nvme0-nvme0n1-nvme0n1p3.device          loaded active plugged   SAMSUNG MZVLQ512HBLU-00BH1 fedora_localhost-live
  sys-devices-pci0000:00-0000:00:02.4-0000:02:00.0-nvme-nvme0-nvme0n1.device                    loaded active plugged   SAMSUNG MZVLQ512HBLU-00BH1
  sys-devices-pci0000:00-0000:00:08.1-0000:03:00.0-backlight-amdgpu_bl0.device                  loaded active plugged   /sys/devices/pci0000:00/0000:00:08.1/0000:03:00.0/backlight/amdgpu_b>
  sys-devices-pci0000:00-0000:00:08.1-0000:03:00.1-sound-card0-controlC0.device                 loaded active plugged   /sys/devices/pci0000:00/0000:00:08.1/0000:03:00.1/sound/card0/contro>
  sys-devices-pci0000:00-0000:00:08.1-0000:03:00.3-usb1-1\x2d4-1\x2d4:1.0-bluetooth-hci0.device loaded active plugged   /sys/devices/pci0000:00/0000:00:08.1/0000:03:00.3/usb1/1-4/1-4:1.0/b>
  sys-devices-pci0000:00-0000:00:08.1-0000:03:00.5-acp_pdm_mach.0-sound-card2-controlC2.device  loaded active plugged   /sys/devices/pci0000:00/0000:00:08.1/0000:03:00.5/acp_pdm_mach.0/sou>
  sys-devices-pci0000:00-0000:00:08.1-0000:03:00.6-sound-card1-controlC1.device                 loaded active plugged   /sys/devices/pci0000:00/0000:00:08.1/0000:03:00.6/sound/card1/contro>
  sys-devices-platform-serial8250-tty-ttyS0.device                                              
```

The names of the Cgroup will be in form `<parent-cgroup>.<child-cgroup>` like `sys-devices-platform-serial8250-tty-ttyS0.device`. To view the hierarchy of cgroups you can use the following command: 

```bash
systemd-cgls
```

Output from the command above would be similar to:
```bash
Control group /:
-.slice
├─user.slice (#1309)
│ └─user-1000.slice (#10135)
│   ├─user@1000.service (#10231)
│   │ ├─session.slice (#10519)
│   │ │ ├─dbus-broker.service (#10567)
│   │ │ │ ├─ 5980 /usr/bin/dbus-broker-launch --scope user
│   │ │ │ └─ 5981 dbus-broker --log 4 --controller 10 --machine-id 5de0a982c5304076a443a0974cc96359 --max-bytes 100000000000000 --max-fds 25000000000000 --max-matches 5000000000
│   │ │ ├─xdg-document-portal.service (#11136)
│   │ │ │ ├─ 6342 /usr/libexec/xdg-document-portal
│   │ │ │ └─ 6371 fusermount -o rw,nosuid,nodev,fsname=portal,auto_unmount,subtype=portal -- /run/user/1000/doc
│   │ │ ├─xdg-desktop-portal.service (#11025)
│   │ │ │ └─ 6326 /usr/libexec/xdg-desktop-portal
│   │ │ ├─plasma-ksmserver.service (#11269)
│   │ │ │ └─ 6391 /usr/bin/ksmserver
│   │ │ ├─pipewire-pulse.service (#13828)
│   │ │ │ └─ 7564 /usr/bin/pipewire-pulse
│   │ │ ├─wireplumber.service (#11099)
│   │ │ │ └─ 6341 /usr/bin/wireplumber
│   │ │ ├─gvfs-daemon.service (#10652)
│   │ │ │ ├─   5992 /usr/libexec/gvfsd
│   │ │ │ ├─ 494643 /usr/libexec/gvfsd-trash --spawner :1.5 /org/gtk/gvfs/exec_spaw/0
│   │ │ │ ├─ 494660 /usr/libexec/gvfsd-network --spawner :1.5 /org/gtk/gvfs/exec_spaw/1
│   │ │ │ └─ 494665 /usr/libexec/gvfsd-dnssd --spawner :1.5 /org/gtk/gvfs/exec_spaw/2
│   │ │ ├─plasma-kwin_x11.service (#11380)
│   │ │ │ └─ 6397 /usr/bin/kwin_x11 --replace
│   │ │ ├─plasma-kded.service (#11343)
│   │ │ │ ├─ 6393 /usr/bin/kded5
│   │ │ │ └─ 6801 /usr/bin/xsettingsd
│   │ │ ├─gvfs-udisks2-volume-monitor.service (#137993)
│   │ │ │ └─ 494638 /usr/libexec/gvfs-udisks2-volume-monitor
│   │ │ ├─plasma-xdg-desktop-portal-kde.service (#11676)
│   │ │ │ └─ 6603 /usr/libexec/xdg-desktop-portal-kde
│   │ │ ├─plasma-plasmashell.service (#11491)
│   │ │ │ ├─ 6563 /usr/bin/plasmashell --no-respawn
│   │ │ │ └─ 7251 /usr/bin/ksysguardd
│   │ │ ├─at-spi-dbus-bus.service (#12237)
lines 1-38
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