---
title: Resources and Architecture
description: Understanding available resources and conceptualizing the architecture
published: true
date: 2026-04-17T05:50:11.043Z
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


> ***Think:*** do you believe the login nodes should contribute their fair share of computation?
{.is-question}



Yes! I mean if they don't, are we not practically wasting their abilities? That defeats the purpose of the cluster! But if they are computing at the same time as they are scheduling, their scheduling abilities might suffer. There might be lag at the user end. What could be the solution.