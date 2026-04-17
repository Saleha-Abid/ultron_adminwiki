---
title: Installing Base OS
description: Laying the OS groundwork for the cluster
published: true
date: 2026-04-17T17:19:35.889Z
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
![networking.png](/building-ultron/networking.png)
3. Recall the networking settings from before. Fill them as follows:
