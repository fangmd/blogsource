---
title: Centos install docker
date: 2016-01-26 11:38:14
tags: [docker, Centos]
categories: Linux

---


## docker: Cannot connect to the Docker daemon. Is the docker daemon running on this host?.

```
docker daemon
```
显示
```
WARN[0000] Your Linux kernel version 2.6.32-042stab116.1 can be unstable running docker. Please upgrade your kernel to 3.10.0.
ERRO[0000] 'overlay' not found as a supported filesystem on this host. Please ensure kernel is new enough and has overlay support loaded.
INFO[0000] Graph migration to content-addressability took 0.00 seconds
WARN[0000] Running modprobe bridge br_netfilter failed with message: , error: exit status 1
WARN[0000] Running modprobe nf_nat failed with message: ``, error: exit status 1
INFO[0000] Firewalld running: false
INFO[0000] Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP address
FATA[0000] Error starting daemon: Error initializing network controller: Error creating default "bridge" network: package not installed
```

需要升级内核