---
title: Resources and Architecture
description: Understanding available resources and conceptualizing the architecture
published: true
date: 2026-04-26T09:58:54.185Z
tags: resources, architecture, concept, atlas
editor: markdown
dateCreated: 2026-04-17T05:14:44.074Z
---

# Ultron - A top-down
This page talks sketches the hard lines of Ultron: identifying available resources and grouping them most efficiently to form a cluster. Let's first have a closer look at the generic HPC schematic given on the main page.

<div align="center">
  <br>
  <img src="/introducing-ultron/hpc-schematic.jpg" width="500px">
  <br>
</div>

You can clearly identify two categories of computers:
1. Login Nodes: *Meant to handle incoming traffic and schedule jobs*
2. Compute Nodes: *Meant for the heavy-lifting only*

> ***Think:*** Do you believe the login nodes should contribute their fair share of computation?

Yes! I mean if they don't, are we not practically wasting their abilities? That defeats the purpose of the cluster! But if they are computing at the same time as they are scheduling, their scheduling abilities might suffer. There might be lag at the user end. What could be the solution?

> ***Rule:*** Place the weakest of your computers at the head/login node position.
{.is-info}

The rest of them go for the position of the compute nodes. Sweet.

## Identified Compute Resources

Given below is summary of the two categories of computers available for Ultron. 

### Head Nodes: Raven-1 to Raven-3
| Specification | Details |
| :--- | :--- |
| **Role** | RSP Training Station |
| **Serial/ID** | RSP-TS-X20 |
| **Processor** | Core i9-12900 (16 Cores) |
| **RAM** | 32 GB (16GB x 2) |
| **Storage** | 2TB HDD, 512 GB SSD |
| **GPU** | NVIDIA TRX A4000 16GB |

---

### Compute Nodes: Argon-1 to Argon-10
| Specification | Details |
| :--- | :--- |
| **Role** | Avionics Integration Simulation Station |
| **Serial/ID** | 6625PK0217321 |
| **Processor** | Core i9 13900K (24 Cores) |
| **RAM** | 128 GB |
| **Storage** | 2TB HDD, 2TB SSD |
| **GPU** | NVIDIA GeForce RTX 4090 24GB |

The choice of head/login and compute nodes is obvious - ***Argons*** having better processor, memory and GPU are chosen for computation. ***Ravens'*** GPU might go unused in its job as a scheduler but in relative retrospect, Ravens are obviously to be the head nodes.

## Storage
This aspect of Ultron is what baffled us the most. Scroll above to the generic HPC schematic. Nodes read from a common storage over the network. Large and professional HPCs use NAS (Network Attached Storage) or PFS (Parallel File System) to achieve this. These are difficult to setup and maintain but recommended for a scalable and longer-lasting cluster. What was our solution?

> ***Rule:*** Use SSDs as your OS disk.
{.is-info}

Look at the specifications of the systems above. While the SSDs sit around holding the OS, the HDDs are practically unused. With roughly 80-120 MB/s, this is to be expected. But what if we used these to hold the more permanent data of our users?

Here is the solution we came up with,
- Configure the HDD of raven01 (head01) as the larger, longer-lasting location for data storage.
- Configure the HDD of raven02 (head02 - backup head node) as the network RAID disk for the raven01's HDD. It is its exact copy. For redundancy and backup.

What about the speed? It is a HUGE bottleneck. Well, the users don't necessarily have to always use this HDDs. They can,
1. Use the space in their `/home/user` folder. This is very limited (look at 512GB Ravens!).
2. Use the space on the compute nodes SSD (a sweet 2TB). They call it 'scratch space'. You copy it, when you are done you delete it. Or we do. How do you copy if you can't access the compute nodes? Well, you write to HDD first and then copy to the local SSD (using slurm batch files - more on that later).
3. Keep the larger files on the Hard Disks.

This seemed like a decent solution. We are using our resources properly and avoiding bottlenecks where we can. Only end-user testing and post-deployment performance reports remain to validate this engineering choice.

## Networking
The nodes need to communicate (naturally). A simple network would be connecting them all by plugging them into a Gigabit switch in a star topology. This is exactly what we did,
- Use Huawei's S5735-l24P4X-A1 switch. It has 24 1-Gbps links. We'll be using those.
- Use Cat-6 cables. We don't want bad copper being a choking hazard.

Each node in the cluster has a static IP. This is for a two main reasons,
1. The nodes need to talk to each other. We can't have DHCP changing addresses as it wills - the cluster will break down!
2. We requested this block of IPs to be relieved of *restrictions* and *login requirements* at CAE.

Given below is a summary of the network details,
| Specification | Details |
| :--- | :--- |
| **IP Block** | 192.168.24.x-192.168.24.y (13 addreses - *Confidential*) |
| **Gateway** | 192.168.24.254 |
| **Subnet** | 255.255.255.0 |
| **DNS** | 8.8.8.8, 8.8.4.4 |

> ***Think:*** Who is 8.8.8.8 and 8.8.4.4?

We assign IPs in order. ***raven01*** holds the first IP (being the primary head); the second IP is given to ***raven02*** (backup head); the rest (in order) are assigned to ***argon01**-**argon10***.

## Backup Power
Ultron is an HPC and HPCs stay online 24/7. We need power (ofcourse) but more importantly we need backup power. Better options exist out there but for now we settled on ***2kW UPS providing backup to 2 nodes***. This gives about ***10 minutes*** of backup power (we need more). 

Here are some notes on the power consumption of our compute resources,
- Ravens consume a maximum of 800W.
- Argons consume a maximum of 1.4kW.

We need better batteries and better backup power. Backup power is also arranged for the network switch. In the event the computers do lose power, they are configured in their BIOS settings to restart.

## Cooling
Current cooling arrangements are as follows,
1. GPUs' inherent cooling arrangements on computers.
2. A 2-ton standing chiller Air Conditioner.

We hope these arrangements will suffice for our current requirements.

## Summing Up
You must now have a thorough idea of what is available to us and how we plan to use it in our goal of building an HPC. Let's move on to the practical stuff.

Next: [Installing Base OS](/BuildingUltron/InstallingBaseOS)






