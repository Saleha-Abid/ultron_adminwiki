---
title: Configuring Slurm DB
description: We need to keep record of all jobs.
published: true
date: 2026-04-26T10:16:50.512Z
tags: slurm, slurmdbd, config, database
editor: markdown
dateCreated: 2026-04-23T03:30:05.109Z
---

# The Slurm Database
Slurm developers took this awesome initiative to allow Slurm to keep track of its jobs. This feature is invaluable for debugging and troubleshooting. It also allows Slurm to be fairer in resource allocation and allows us to set rules for computation as well.

## Setting up SlurmDB
To enable the slurmdb daemon, we first must have an active database that the daemon will be reporting to. This shall be MariaDB.
```bash
sudo apt install mariadb-server mariadb-client -y
sudo systemctl enable --now mariadb
```
Make sure `slurmdbd.conf` has the right ownerships and permissions,
```bash
sudo chown root:slurm /etc/slurm/slurmdbd.conf
sudo chmod 640 /etc/slurm/slurmdbd.conf

#Some additional directories
sudo mkdir -p /var/run/slurm
sudo chown slurm:slurm /var/run/slurm
```
Now, run:
```bash
sudo mysql -u root
```
Inside MySQL prompt,
```bash
CREATE DATABASE IF NOT EXISTS slurm_acct_db;
CREATE USER IF NOT EXISTS 'slurm'@'localhost' IDENTIFIED BY 'placeholder-passwd';
GRANT ALL PRIVILEGES ON slurm_acct_db.* TO 'slurm'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
The password should be correct and the same as one configured in slurmdbd.conf.

To check if the backend database is happy,
```bash
sudo -u slurm /usr/sbin/slurmdbd -Dvvv
```
This should eventually return `connection established`. Next, start the `slurmdbd` daemon.
```bash
sudo systemctl enable slurmdbd
sudo systemctl restart slurmdbd
```

## Summing Up
Great job! This completes the task of configuring the brains of this HPC - Slurm. Atlas is practically a being now.

Next: 