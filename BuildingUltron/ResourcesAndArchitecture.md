---
title: Resources and Architecture
description: Understanding available resources and conceptualizing the architecture
published: true
date: 2026-04-17T06:18:56.138Z
tags: ultron, resources, architecture, concept
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

