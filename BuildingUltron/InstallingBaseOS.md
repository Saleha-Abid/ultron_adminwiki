---
title: Installing Base OS
description: Laying the OS groundwork for the cluster
published: true
date: 2026-04-17T19:03:08.720Z
tags: ubuntu, os, base os, os installation
editor: markdown
dateCreated: 2026-04-17T12:12:14.893Z
---

# Base OS - The Groundwork
Every computer needs an operating system - so does this cluster. Our choice for a base OS should be one that is light and most friendly with hardware. One could not have a better choice than OSes built on the Linux Kernel. 

We choose **Ubunutu Server 24.04 LTS**. It is stable and well-documented with a decent online community. You can troubleshoot any native linux problems easily. 

For installation follow [this tutorial](https://ubuntu.com/tutorials/install-ubuntu-server#1-overview) on Ubuntu's official site. Some settings are custom for Ultron. The rest of this page covers them.

## Tutorial Steps 1-6: The Basics
Follow them as they are. Make sure you know your keyboard before you select the layout. The ones in the lab are **English US (en-US)**.

Choose **Ubuntu Server** as your install. (Do not choose the minimalized version). 

## Tutorial Step 7: Networking
As established previously, we want our nodes to have static IPs. To configure this,
1. Choose your ethernet interface (the one that connects to the switch). It might be something like ***enp031sf6***. Select it using your keyboard keys.
2. You'll be presented with the following setting when you select the IPv4 method to be *Manual*.
<div align="center">
  <br>
  <img src="/building-ultron/networking.png" width="500px">
  <br>
</div>
3. Recall the networking settings from before. Fill them as follows:

| Specification | Details |
| :--- | :--- |
| **Subnet** | 192.168.24.0/24 |
| **Address** | 192.168.24.(*confidential*) - Whichever is relevant to your node |
| **Gateway** | 192.168.24.254 |
| **Name Server** | 8.8.8.8, 8.8.4.4 |

4. *Save* the settings. Then select *Done*.
5. No need to add a proxy address. Select *Done*
6. The installation will check connection to the default mirrors. If you are on the *unrestricted CAE internet*, this will pass. Select *Done*.

## Tutorial Step 8-11: Configure Storage
We will be configuring storage manually. The steps are as follows,
1. Select *Custom Storage Layout*. Select *Done*.
2. You'll be presented with all the disks in your system. Identify the SSD. This is usually listed at the top. Any existing OS will be living there (you can catch that from the mount points `/boot`, etc. 
3. Navigate to the name of the disk (all of them - one by one), select it and choose *Reformat* from the menu. If presented with warning for deletion of existing data, proceed.
4. After doing the above for all disks (SSDs and HDDs), navigate to your SSD. Select it and from the menu select *Select as boot device*. The `/boot/efi` partition will be automatically created.
5. *Free Space* will appear before the name of the SSD after performing the step above. Select it and choose *Create Partition*. Configure it as follows:

| Specification | Details |
| :--- | :--- |
| **Size** | 100G |
| **Format** | ext4 |
| **Mount** | `/` |
6. Next select *Free Space* again under the SSD. Select *Create Partition*. Configure as follows,

| Specification | Details |
| :--- | :--- |
| **Size** | *Add Nothing* |
| **Format** | ext4 |
| **Mount** | `/home` |
7. SSD is configured successfully. Now for the HDD - ***if you are configuring the Ravens (head nodes) otherwise skip this step***: Select the disk, select *Create Partition*, configure as below,

| Specification | Details |
| :--- | :--- |
| **Size** | *Add Nothing* |
| **Format** | ext4 |
| **Mount** | `/data` |

8. Select *Done*.

## Tutorial Step 12: Set up a Profile
You'll be presented with the following,
<div align="center">
  <br>
  <img src="/building-ultron/profile.png" width="500px">
  <br>
</div>
Configure as follows,

| Specification | Details |
| :--- | :--- |
| **Your name** | ultron |
| **Your server's name** | raven01 (whatever your node is named) |
| **Pick a username** | admin_ultron |
| **Choose a password** | *confidential - ask sysadmins*  |

As it stands ***admin_ultron***, is the administrator account on Ultron. It has no password set on sudo. It is junior only to the root user. It is the first user account we create on Ultron. This account has remote shell (SSH) access even on compute nodes. It is most powerful.

After this,
- Do not upgrade to *Ubuntu Pro*.
- Install OpenSSH server, but don't configure anything else.
- Skip selecting *Featured server snaps*.

## Tutorial Step 13-14: Installing Software
Let Ubuntu download basic packages. After it is done, it'll ask you to reboot. Then it'll ask you to remove your installation media and press *Enter*. Do that. 

## Follow-up Steps
After you have rebooted, wait for the kernel to process. It'll then ask you to login. Use the configured credentials to login. Then run the following commands

```bash
sudo apt update
sudo apt install -y build-essential git vim munge chrony ssh nvidia-cuda-toolkit python3-pip python3-dev libopencv-dev net-tools iputils-ping curl traceroute mtr
```
This will suffice for our basic requirements install. Next, we must update our drivers. Run,

```bash
sudo ubuntu-drivers install
````
Cool, now to be able to remotely access your nodes, we must configure it for ssh. First enable ssh (so it starts automatically at boot), then start it.

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```
To check its status, run:

```bash
sudo systemctl status ssh
```
Now, on your laptop generate a key (make sure you have openssh-client installed):

```bash
#if you haven't installed openssh-client
sudo apt install -y openssh-client
```
```bash
#to generate key
ssh-keygen -t ed25519
```
We configured a passphrase for security of the admin_ultron account. Next, copy your id to the node:

```bash
ssh-copy-id admin_ultron@ip
```
Now you will be able to SSH without needing the password to admin_ultron user. Try,
```bash
ssh admin_ultron@ip
```

Anyone on the network can do the above if they know admin_ultron's password right? Heck, normally, you can even SSH without a key if you know the password. We need to prevent that.

On your node, edit your `/etc/ssh/sshd_config` by
```bash
sudo vim /etc/ssh/sshd_config
```

Edit the following lines,
```bash
PasswordAuthentication no
KbdInteractiveAuthentication no
UsePAM yes
TCPKeepAlive yes
ClientAliveInterval 60
```

After you have configured SSH (and no longer need the user password to admin_ultron), disable its use by editing the `/etc/ssh/sshd_config.d/50-cloud-init.conf`,
```bash
sudo vim /etc/ssh/sshd_config.d/50-cloud-init.conf
```
Set the one-liner to, (this setting is obviously only for admin_ultron - more on user accounts later)
```bash
PasswordAuthentication no
```
## Summing Up
This page walked you through the steps of base OS installation and a couple follow-up steps involving installing basic packages and configuring SSH. If you are building a cluster from scratch, you must be performing this step multiple times. Cut down your work by using **Ansible Playbook**.

SSH, as a sysadmin should be so that you can remotely access all nodes of your cluster through the admin_ultron account. What about the intra-cluster remote access?
- Sysadmin able to SSH into all nodes (as admin_ultron through keys)
- End-users able to SSH into login/head nodes (as user accounts through passwords)
- Login/head nodes able to SSH into compute nodes and each other (as admin_ultron through keys)

Now that the groundwork is laid, let's configure our planned architecture for storage.

Next: [Managing Storage: Setting up DRBD](/BuildingUltron/SettingUpDRBD)





