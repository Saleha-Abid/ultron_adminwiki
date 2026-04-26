---
title: Introducing Atlas
description: A short and formal getting-to-know Atlas
published: true
date: 2026-04-26T09:58:26.594Z
tags: intro, introduction, basics, hpc, atlas
editor: markdown
dateCreated: 2026-04-16T17:52:00.837Z
---

# About Ultron
Ultron is the name of the High Performance Computation (HPC) Cluster at the College of Aeronautical Engineering (CAE), NUST. It facilitates multiple users (mainly students) connected across the campus network to run their jobs remotely. The 'jobs' here mainly target AI and Machine Learning. 

## High Performace Computing
High-Performance Computing (HPC) refers to the use of powerful, networked computer clusters to solve complex computational problems or process massive datasets in parallel, far exceeding the speed of conventional computers. It is essentially "big compute," often employing supercomputers to achieve high-speed performance. 

Below is an illustration of a common HPC,

<div align="center">
  <br>
  <img src="/introducing-ultron/hpc-schematic.jpg" width="500px">
  <br>
</div>

As the illustration explains, the HPC facility is accessed on the user end through *login nodes*. The *compute nodes* do all the heavy lifting. Storage is common across the cluster and accessed over the network.

Here is what we would like our facility to look like (conceptually),

<div align="center">
  <br>
  <img src="/introducing-ultron/hpc_concept.png" width="500px">
  <br>
</div>

## Why an HPC?
It is very true that you have probably only had about five percent of your tasks that required using over 80% resources of your machine. If you have a GPU you know you rarely ever give it a tough time. While some would argue that usage of resources in moderation is in fact better - the makers of Ultron believe that better use can be made out by efficiently using these resources. For jobs that can be parallelized a cluster is your best option. Group resources and divide tasks. No unit sweats and you get heavy jobs done with satisfactory performance.

The next time you think about training an AI model locally on your consumer-grade laptop, think again. You can remotely access a cluster of literal compute beasts ready only a command away.

## HPC Usage
- Currently, we are targeting AI and ML related heavy-lifting. Relevant packages and libraries are to be pre-installed. 
- Students will be able to register for accounts on Ultron and remotely transfer relevant files for the training.
- Ultron supports requests for usage of the HPC in other dimensions. In this light, requests for custom software can be raised.

## Building Ultron
This site is meant for sysadmins and sysadmins-in-making. We provide detailed documentation from base OS installation to all sorts of configurations. [Ready to Start?](/BuildingUltron/ResourcesAndArchitecture)


