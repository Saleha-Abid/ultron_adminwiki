---
title: Managing Storage: Setting Up DRBD
description: Configure a network RAID - DRBD (Distributed Replicated Block Device)
published: true
date: 2026-04-22T05:38:22.025Z
tags: storage, managing storage, drbd, file systems
editor: markdown
dateCreated: 2026-04-17T18:54:14.049Z
---

# The Permanent Disk
For heavy AI and ML tasks, heavy databases need a more permanent and bulkier home. This location can never be safe solitary - it has to have a replica, a back-up. **DRBD** (Distributed Replicated Block Device) helps us accomplish this. We can ensure redundancy using two physically isolated disks that are connected over a network.

## Choosing Disks
As established earlier, the head nodes main disks (SSDs) are not spacious enough to fulfill this purpose. So we use the **two HDDs on each of the two head nodes**. One shall be primary (*raven01*) and the other shall be secondary (*raven02*). They should be synced at all times. When the primary fails, the secondary should take charge and later, when restored, should compensate any changes.

## Setting up DRBD
Setting up DRBD is relatively simple. What is difficult is making sure that the fail-safe logic works. As it stands, we cannot implement that logic through DRBD alone. We need an additional tool called **Keepalived** (more on that later).

To set up DRBD, follow the manual [here](https://ubuntu.com/server/docs/how-to/high-availability/install-drbd/).

1. On both head nodes (raven01 and raven02), install DRBD.
```bash
sudo apt install drbd-utils
```
2. On both, edit the `/etc/drbd.conf`. You can obtain it through [this Github repository](https://github.com/Saleha-Abid/ultron.git).
```bash
sudo vim /etc/drbd.conf
```
3. On both, run:
```bash
sudo drbdadm create-md r0
sudo systemctl start drbd.service
```
4. On *raven02*, the head node which is to have the primary role in DRBD.
```bash
sudo drbdadm -- --overwrite-data-of-peer primary all
```
5. The above command starts syncing the data. To view the sync status on *raven02*.
```bash
watch -n1 cat /proc/drbd
```
6. On raven01,
```bash
sudo mkfs.ext4 /dev/drbd0
sudo mount /dev/drbd0 /data
```

## Some Pointers
DRBD is super fussy. Here is why we say that,
> ***Rule:*** Only one of the two heads, can mount their disks at a time. Moreover, it has to be the one with the **Primary** role.
{.is-info}

You must first demote primary to secondary, the unmount then promote the second head from secondary to primary and mount its drive. This is the fail-safe logic's tripping point that we must be cautious of later.

##


