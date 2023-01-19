---
author: "Avnish"
title: "Container Architecture"
date: "2023-01-27"
description: "Isolated processes for deploying applications"
tags: ["container", "podman", "docker","openshift", "kubernetes", "linux", "namespaces", "cgroups", "seccomp", "selinux"]
categories: ["Microservices", "Cloud Computing", "Operating Systems"]
series: ["OpenShift"]
aliases: ["container-architecture"]
ShowToc: true
# TocOpen: true
cover:
  image: "container_arch.drawio.svg"
  linkFullImages: true
  # can also paste direct link from external site
  # ex. https://i.ibb.co/K0HVPBd/paper-mod-profilemode.png
  alt: "Features for containers"
  caption: "Features for containers"
  relative: false # To use relative path for cover image, used in hugo Page-bundles
---

Features that facilitate the creation and execution of containers:

## Namespaces
**Namespaces** are used to limit the reach of container to its host's resources. This helps with security and well as limiting the resources available to the container.

Namespaces necessary for the creation of containers:
### User
The users and groups created inside the container are completely different from its host, they have their own UID and GID. Processes running inside the container as a `root` user could be mapped to a non-root user on the host.

### mnt
The container has its own file system hierarchy but it could be viewed by the host in directory `/proc/<CONTAINER_PID>/mounts`.

### Unix Timesharing System (UTS)
Container have their own hostnames assigned to them due to UTS namespace.

### Process ID (PID)
Each process running on the OS has a unique Process ID or PID assigned to it. The processes running inside the container have their own PIDs which is separate from the host. 
To view all the running processes you can use the command `ps`.  

### Network
Containers communicate with the host and internet through a virtual network interface provided by the Container's Runtime.

### Inter-Process Communication (IPC)
Processes in same IPCs can access the resources of each other. So two containers on same IPC namespace can communicate with each other.

### Time
Since Linux Kernel 5.6 and addition of Time namespace now a container can have a different view of time than its host

## Control Groups (Cgroups)
A **control group** is created to effectively allocate resources of the OS to the processes residing in it. These Cgroups are hierarchial i.e. a child Cgroup could be spawned from the parent and it will inherit its certain attributes.

By creating a Cgroup a process in it could be prioritized, paused, removed or resumed based on the resources allocated to it. This also helps in monitoring the resources used by a particular processes.

If you are using an OS with `systemd` init system. Then you can use the following command to list all the cgroups: 

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
* [Cgroups, namespaces, and beyond: what are containers made from?](https://www.youtube.com/watch?v=sK5i-N34im8)
* [4 Linux technologies fundamental to containers](https://opensource.com/article/21/8/container-linux-technology)
* [systemd(1) — Linux manual page](https://man7.org/linux/man-pages/man1/init.1.html)
* [Obtaining Information about Control Groups](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/resource_management_guide/sec-obtaining_information_about_control_groups)
* [It's Finally Time: The Time Namespace Support Has Been Added To The Linux 5.6 Kernel](https://www.phoronix.com/news/Time-Namespace-In-Linux-5.6)
* [seccomp(2) — Linux manual page](https://man7.org/linux/man-pages/man2/seccomp.2.html)
* [What is SELinux?](https://www.redhat.com/en/topics/linux/what-is-selinux)