---
title: Time Synchronization Using Chrony
description: All nodes must be very closed synced in time.
published: true
date: 2026-04-22T17:02:20.839Z
tags: slurm, chrony, time, sync, ntp
editor: markdown
dateCreated: 2026-04-22T13:53:05.916Z
---

# The Chrony Daemon
Chrony is a very basic NTP client-server example - you set up a source and have all other devices syncing their time to that source. The source could or could not be correcting its time. Slurm is not concerned with the global accuracy of the cluster's time - the nodes should just be agreeing with one another.

## Why Not NTP?
This allows you to automatically correct your time when you go online. Unfortunately, this service faces certain problems on the CAE network. So here is our work-around:
- Set the time of raven01 manually one.
- Sync all nodes to raven01's time, including raven02.
- All compute nodes have two sources of time, raven01 and raven02. When raven01 goes down, they just follow raven02. 

Let's get Chrony up and going.

## Fixing the Time on raven01
First, turn off NTP. Without this, you can't manually set your time.
```bash
sudo timedatectl set-ntp false
```
Next, set your time,
```bash
sudo timedatectl set-time 'yyyy-mm-dd hh:mm:ss'
```
Remember: this is only to be done on raven01.

## Setting Up Chrony
On all nodes, run,
```bash
sudo apt install chrony -y
```
Next, we must edit `/etc/chrony/chrony.conf`. This is different for different nodes.
On raven01, add the following to the config file.
```bash
allow 192.168.24.0/24
local stratum 10
```
On raven02,
```bash
server 192.168.24.x iburst #include raven01's ip

allow 192.168.24.0/24
local stratum 11
```
On the compute nodes,
```bash
server 192.168.24.x iburst #include raven01's ip
server 192.168.24.x iburst #include raven02's ip
```
Now on all of them run,
```bash
sudo systemctl enable chrony
sudo systemctl restart chrony
sudo chronyc makestep
```
To see whether the compute nodes are indeed referring to raven01 and raven02, run,
```bash
chronyc sources -v
```
You'll see the two heads listed. The '+' icon with both heads indicates that the compute nodes sync their time with a weighted average of time from the two sources. If one goes down, the syncing still happens.

## Summing Up
Time syncing is very crucial for smooth Slurm operations. With chrony, we achieved this. You can see the nodes synced with each other if you check the time on each using `timedatectl`.

Next: [Munge Authentication](/PrerequisitesForSlurm/MungeAuthentication)