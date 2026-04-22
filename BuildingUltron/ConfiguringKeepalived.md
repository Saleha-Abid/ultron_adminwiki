---
title: Managing Storage: Configuring Keepalived
description: Keepalived keeps track of your head nodes dying.
published: true
date: 2026-04-22T13:43:50.462Z
tags: storage, drbd, file systems, keepalived, fail-safe, backup
editor: markdown
dateCreated: 2026-04-22T07:03:19.171Z
---

# Keepalived For Failover
Keepalived is a failover and monitoring daemon for Linux clusters that provides high availability functionality and load balancing. Ultron's users login to the one head, the compute nodes mount one filesystem, only one of the two heads can be *Primary* in DRBD: all these need on address - **one IP**. Keepalived floats a *virtual IP* that is owned by raven01 but is snatched by raven02 as soon as raven01 goes MIA.

## Configuring Keepalived
This is simple. First install the service
```bash
sudo apt update && sudo apt install keepalived -y
```
Next, copy the configuration files from [this Github repository](https://github.com/Saleha-Abid/ultron.git). Go to `/etc/keepalived` and copy the `keepalived.conf` there for both nodes respectively.
```bash
sudo vim /etc/keepalived/keepalived.conf
```
Then create a folder `scripts` in `/etc/keepalived`.
```bash
sudo mkdir /etc/keepalived/scripts
```
From the Github repository linked above, check out the scripts folder under each node. Copy `go_primary.sh` and `go_secondary.sh` to their respective nodes.
```bash
sudo vim /etc/keepalived/scripts/go_primary.sh
sudo vim /etc/keepalived/scripts/go_secondary.sh
```
> ***Look Out!*** 
Check the scripts. There are some lines at the end of which is a comment '*comment these out for now*'. Comment them because we haven't configured NFS and lsyncd yet.
{.is-warning}

On both nodes make the scripts executable by running,
```bash
sudo chmod +x /etc/keepalived/scripts/*.sh
```
Restart keepalived to be safe (both nodes),
```bash
sudo systemctl enable keepalived
sudo systemctl restart keepalived
```
To check the IP being bagged by raven01, run:
```bash
ip addr
```
Under the relevant ethernet link, you'll see the default IP and then another IP. This is the one supplied by keepalived. If you stop the service on raven01,
```bash
sudo systemctl stop keepalived
```
And then run `ip addr` on raven02, you'll see that it claims the IP as soon.

## Summing Up
Keepalived is not just a fight for IP, it is doing a lot more. If you are interested, you can study the scripts. To summarize,
- It tracks when raven01 goes down. When it does, it promotes DRBD on raven02 to primary and mounts the filesystem.
- When raven01 comes back up, it waits for a while to sync it to raven02 before promoting it to primary and mounting the disk. It also carefully avoids race conditions.
- Starts the NFS server on the right node.
- Starts lsyncd on the right node.
 
Essentially, it assigns duties to the active head node.

Next: [Setting Up NFS](/BuildingUltron/SettingUpNFS)
