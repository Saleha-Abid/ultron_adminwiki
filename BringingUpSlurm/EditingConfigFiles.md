---
title: Editing Config Files
description: .conf files are what make up Slurm.
published: true
date: 2026-04-23T04:44:49.675Z
tags: slurm, config, slurm.conf
editor: markdown
dateCreated: 2026-04-23T03:26:05.244Z
---

# Slurm Configurations
To get Slurm up and running, it is vital we have te configuration files in the right locations. 

## Writing the .conf
You get the configuration files from [this Github repository](https://github.com/Saleha-Abid/ultron.git). These are to be copied to the `/etc/slurm/` directory. Follow the instructions below,
- `gres.conf` only goes to the compute nodes.
- `slurmdbd.conf` only goes to raven01.
- The rest of the files are to exist uniformaly everywhere.

> ***Look Out!***
There are some line in `slurm.conf` that are to be commented because we haven't configured slurmdb and Prometheus yet.
{.is-warning}

Once you have done this, restart the daemons,

```bash
#On the head nodes
sudo systemctl enable slurmctld
sudo systemctl restart slurmctld
```
```bash
#On the compute nodes
sudo systemctl enable slurmd
sudo systemctl restart slurmd
```
If everything goes well, this will throw no errors. You can check their status by running `sudo systemctl status slurmctld/slurmd`.

To check if Slurm is really up,
```bash
sinfo
```
This should return the partition *compute* and the compute nodes.

> ***Note:*** You can generate the slurm.conf using [Slurm's Configuration Generator](https://slurm.schedmd.com/configurator.html). 
{.is-success}


## Summing Up
This wraps it for bringing up Slurm. However we are still only doing everything in the present. To keep record, we must setup a database.

Next: [Configuring Slurm DB](/BringingUpSlurm/ConfiguringSlurmDB)

