---
title: Configuring Hosts
description: Make the nodes known by name, not IP
published: true
date: 2026-04-23T05:11:07.170Z
tags: hosts, ip
editor: markdown
dateCreated: 2026-04-22T13:59:56.318Z
---

# Naming Nodes
We already christened our nodes during [Base OS Installation](/BuildingUltron/InstallingBaseOS). But the nodes only know each other by their IPs. It is immensely helpful if they'd know each other by their names.

## Resolving IPs
This step is very straight-forward. On all nodes, edit the `/etc/hosts` file using,
```bash
sudo nano /etc/hosts
```
Add the following lines at the end,
```bash
192.168.24.x  raven01
192.168.24.x  raven02
192.168.24.x  argon01
192.168.24.x  argon02
```
And that's it.

## Summing Up
We have officially completed all Slurm prerequisites. Slurm is ready to be configured now.

Next: [Bringing Up Slurm](/BringingUpSlurm/InstallingTheRightSlurmDaemons)
