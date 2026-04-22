---
title: Synchronizing Users and Groups
description: UIDs and GIDs must be same across the cluster
published: true
date: 2026-04-22T16:55:19.948Z
tags: sync, gid, uid, users
editor: markdown
dateCreated: 2026-04-22T13:58:40.123Z
---

# UIDs and GIDs
User IDs and Group IDs is what identifies a user on Linux. If your IDs are same, you can replicate your action on a foreign machine. Containers such as Docker are based on this concept.

## The Important Users
There are a lot of users on your computer. Daemons have their own users that have certain file permissions. To peek into who lives on your computer, run,
```bash
cat /etc/passwd
```
Not everyone is to be synced for Slurm to work. The following users are important,
1. Slurm user (not created yet)
2. Munge user (created and synced)
3. admin_ultron (created but sync status to be checked)
4. All Ultron's users (to be created)

Let's run checks for admin_ultron.

## Syncing IDs
On all nodes, run,
```bash 
id admin_ultron
```
Compare the UID and GID across the cluster. Are they same? If yes, you are good to go. If not, you must change it.

Let's settle on a GID and UID of 1000. Run the following on all nodes,
```bash
sudo groupmod -g 1000 admin_ultron
sudo usermod -u 1000 -g 1000 admin_ultron
```

## Summing Up
In the future, the slurm user shall also be synced and so will all the end-users to this HPC. If you find it tiring running the same commands on all nodes separately, try Ansible Playbook.

Next: [Configuring Hosts](/PrerequisitesForSlurm/ConfiguringHosts)