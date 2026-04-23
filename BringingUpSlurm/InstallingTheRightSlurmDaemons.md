---
title: Installing The Right Slurm Daemons
description: Each node on the HPC has special daemons based on their role.
published: true
date: 2026-04-23T04:04:32.871Z
tags: slurm, daemons, slurmctld, slurmd, slurmdbd, installation
editor: markdown
dateCreated: 2026-04-23T03:21:18.660Z
---

# About Slurm
Slurm (Simple Linux Utility for Resource Management) is an open source, fault-tolerant, and highly scalable cluster management and job scheduling system for large and small Linux clusters. Slurm requires no kernel modifications for its operation and is relatively self-contained. We'll be using Slurm as the brain to our HPC.

## The Slurm Architecture
Slurm is very neatly built. At the very base, each node in the cluster has a special daemon (background process) running that defines its role. Have a look at the concept diagram below,

<div align="center">
  <br>
  <img src="/bringing-up-slurm/architecture.png" width="500px">
  <br>
</div>

Having two roles in out cluster - *head node* and *compute node*, the daemon allocation as explained by the diagram is as follows,
1. **Head Nodes**: slurmctld, slurmdbd (if configuring database which we will).
2. **Compute Nodes**: slurmd

We will now proceed with the installation of these daemons. This entire section has been referenced from [Slurm Documentation](https://slurm.schedmd.com/quickstart_admin.html).

## Installing the Daemons
On raven01, run
```bash
cd /tmp
wget https://download.schedmd.com/slurm/slurm-25.11.4.tar.bz2
tar -xaf slurm-25.11.4.tar.bz2
cd slurm-25.11.4
sudo mk-build-deps -i debian/control
debuild -b -uc -us
```
This will take a while. After it is done, you'll see all of slurm's daemons ready to be unpacked in the `/tmp` directory of raven01.

Copy the correct daemons to the nodes. For raven02,
```bash
scp /tmp/slurm-smd_*.deb admin_ultron@raven02:/tmp/
scp /tmp/slurm-smd-client_*.deb admin_ultron@raven02:/tmp/
scp /tmp/slurm-smd-slurmctld_*.deb admin_ultron@raven02:/tmp/
```
For the compute nodes,
```bash
scp /tmp/slurm-smd_*.deb admin_ultron@argon01:/tmp/
scp /tmp/slurm-smd-client_*.deb admin_ultron@argon01:/tmp/
scp /tmp/slurm-smd-slurmd_*.deb admin_ultron@argon01:/tmp/
```
Repeat this for argon02, and so on.

On each node, navigate to the `/tmp` directory and run,
```bash
#FOR raven01
cd /tmp
sudo apt install slurm-smd-slurmctld_*.deb slurm-smd-client_*.deb slurm-smd_*.deb slurm-smd-slurmdbd_*.deb
```
```bash
#FOR raven02
cd /tmp
sudo apt install slurm-smd-slurmctld_*.deb slurm-smd-client_*.deb slurm-smd_*.deb 
```
```bash
#FOR all argons
cd /tmp
sudo apt install slurm-smd-slurmd_*.deb slurm-smd-client_*.deb slurm-smd_*.deb 
```
This completes the installation. Once installed, the .deb files will disappear from the `/tmp` directory. If errors are thrown, they are most likely due to .conf files having not been set.

## Creating the Slurm User
