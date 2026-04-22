---
title: Munge Authentication
description: Munge keeps nodes validated for Slurm
published: true
date: 2026-04-22T16:30:15.989Z
tags: slurm, munge, auth, validate, credentials
editor: markdown
dateCreated: 2026-04-22T13:57:32.283Z
---

# The Munge Service
Munge can be used to create and validate credentials. It allows Slurm to authenticate the UID and GID of a request from another host that has matching users and groups. It is therefore indispensible for Slurm.

## Installing Munge and Munge Libraries
All nodes must have `munge` configured. They must have the `munge` user with uniform UID and GID. By default, `munge` user is created on installation. You just need to verify if the UID and GID is synced with the cluster.
To install `munge` on all nodes,
```bash
sudo apt install munge libmunge-dev -y
```

## Sync Munge IDs
Stop the services for a while. We'll be syncing the GIDs and UIDs first.
```bash
sudo systemctl stop munge
```
We'll set the GID and UID to be 1101.
```bash
sudo groupmod -g 1101 munge
sudo usermod -u 1101 -g 1101 munge
```
Set ownerships,
```bash
sudo chown -R munge:munge /etc/munge
sudo chown -R munge:munge /var/lib/munge
sudo chown -R munge:munge /var/log/munge
sudo chown -R munge:munge /run/munge
```
You can start munge now,
```bash
sudo systemctl start munge
```
## Generate Munge Key
On raven01 generate the key,
```bash
dd if=/dev/random of=/etc/munge/munge.key bs=1024 count=1
```
Set ownerships,
```bash
chown munge:munge /etc/munge/munge.key
chmod 400 /etc/munge/munge.key
```
Copy the munge key to the home directory of admin_ultron,
```bash
sudo cp /etc/munge/munge.key ~/munge.key
```

## Distributing the Munge Key
All nodes must have the same munge key we generated on raven01. Run the following on raven01,
```bash
scp ~/munge.key admin_ultron@raven02:~/
```
Repeat the above step for argon01, and so on for all nodes.

Next, on all of the nodes (expect raven01),
```bash
sudo mv ~/munge.key /etc/munge/munge.key
```
Set ownerships (again on all nodes) like how we did on raven01,
```bash
chown munge:munge /etc/munge/munge.key
chmod 400 /etc/munge/munge.key
```
On all nodes, enable and start `munge`,
```bash
systemctl enable munge
systemctl start munge
```
To verify the functionality of munge,
```bash
munge -n | unmunge
```
You must delete the key from admin_ultron's home directory in raven01.
```bash
rm ~/munge.key
```

## Summing Up
The authentication Slurm requires is all up and running now. Manually doing all this can be a bit tedious. Try using Ansible Playbook.

Next: [Synchronizing Users And Groups](/PrerequisitesForSlurm/SynchronizingUsersAndGroups)