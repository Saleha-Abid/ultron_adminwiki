---
title: Prometheus and Grafana
description: Understanding popular monitoring tools
published: true
date: 2026-04-22T06:57:11.002Z
tags: prometheus, grafana, dashboards, reports, monitoring, monitor
editor: markdown
dateCreated: 2026-04-20T18:21:27.793Z
---

# About P&G
Prometheus and Grafana are two popular industrial grade tools used for tracking infrastucture, microservices and applicaton performances. **Prometheus** is the metrics collector, building a time-series database. **Grafana** is the visualization layer, creating interactive and actionable dashboards.

## The Need to Monitor
There is no doubt that monitoring is an absolutely un-negotiable need of supercomputers. Naturally, Ultron needs one too. Before the development of this dashboard, sysadmins had to go through the pain of SSH-ing and running commands like `htop` and `drbdadm status`. And then they'd lose their sanity everytime the compute nodes unmounted the NFS disks. A dashboard - a pretty one, is exactly what was needed.

## Identifying Metrics to Monitor
We identified three broad categories of metrics we believed were needed. These are enlisted,
1. A general **health report** of each node. This would include the CPU load, memory utilization, disk full, etc. It would also include NFS mount status and DRBD metrics (such as connection and sync status).
2. A **Slurm report**. While we can very well `sinfo`, frankly it is a pain. This dashboard will give display what slurm has to report.
3. A **GPU report**. Just like the standard `htop` does not include GPU stats, the standard *node_exporter* service also ignores it. We need different services up and running to monitor GPU load and health.

That amounts to 3 dashboards.

## Summing Up
This is it for introducing the monitoring tools. Let's bring them to reality.

Next: [Spinning The Containers](/MonitoringUltron/SpinningTheContainers)