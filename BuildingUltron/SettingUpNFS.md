---
title: Managing Storage: Setting Up NFS
description: Broadcast your filesystem across the cluster.
published: true
date: 2026-04-26T10:00:25.924Z
tags: storage, drbd, file systems, nfs, network
editor: markdown
dateCreated: 2026-04-22T07:04:45.478Z
---

# Network File System
NFS is a tool that allows you to broadcast your disk over network. This way, anyone who is able to reach you can also reach for the disk and mount it as if it were its own. The read/write bottleneck is obviously the network speed - especially if its an NVME SSD mounted on an M2. Unfortunately for us the bottle is the read/write speed of the hard disk.
> ***Note:*** We shall be putting up our DRBD replicated HDD on the NFS.
{.is-success}

## Why NFS
This is straight-forward - we can't always be copying back and forth over the network. Sounds contradictory? Simply put, without NFS we'd have a haphazard system with users copying data on the home directory of the head node. The space is limited there. And since it is the OS disk, we can't have it ballooning up. It is always helpful to have a warehouse storage where you keep your bulkier data - but this warehouse doesn't belong to anyone in particular. It is accessible by everyone.

## Setting up NFS
On the two heads, install `nfs-kernel-server`
```bash
sudo apt install nfs-kernel-server -y
```
Then edit `/etc/exports` and this line at the bottom,
```bash
/data 192.168.24.0/24(rw,sync,no_subtree_check,no_root_squash,fsid=1)
```
Next run, on both nodes.
```bash
sudo exportfs -ra
```
On raven01, run
```bash
sudo systemctl disable nfs-kernel-server
sudo systemctl restart nfs-kernel-server
```
On raven02, run
```bash
sudo systemctl disable nfs-kernel-server
sudo systemctl stop nfs-kernel-server
```
On the compute nodes, install `nfs-common`
```bash
sudo apt install nfs-common -y
```
On the compute nodes, make the directory where you'll be mounting the NFS,
```bash
sudo mkdir /data
```
Then edit `/etc/fstab` on the compute nodes and add this line,
```bash
192.168.24.100:/data  /data  nfs  rw,hard,proto=tcp,mountproto=tcp,retry=10,_netdev  0  0
```
To refresh the settings, run on the compute nodes,
```bash
sudo mount -a
```
On the compute nodes, verify if the NFS mount is present,
```bash
df -h
```
> ***Note:*** After setting up NFS, comment out the lines involving NFS in keepalived scripts (remember commenting them in the last page). The refresh by running `sudo systemctl restart keepalived`. Do this for both heads. Keep the comments on the lines with `lsyncd`.
{.is-success}

## Summing Up
Our filesystem is now live. Check by creating files and viewing them from other nodes. If everything went smoothly, there should be no problems.

Next: [Prerequisites for Slurm](/PrerequisitesForSlurm/TimeSynchronizationUsingChrony)

