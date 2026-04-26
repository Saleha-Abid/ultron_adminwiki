---
title: Exporting Data from Nodes
description: Starting services on all nodes to be sent to the dashboard
published: true
date: 2026-04-26T10:02:49.105Z
tags: prometheus, grafana, dashboards, reports, monitoring, export data, sensors, gpu, slurm
editor: markdown
dateCreated: 2026-04-20T18:25:16.577Z
---

# Collecting Data
Now that your containers are up, let's start collecting data. We need three categories of data,
1. General node reports.
2. Slurm reports.
3. GPU reports.

This page guides you to exporting each one, one by one.

## General Node Reports
We need a service running on each node. This service is called `node_exporter`. On each node, run:
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
tar xvf node_exporter-1.8.2.linux-amd64.tar.gz
sudo mv node_exporter-1.8.2.linux-amd64/node_exporter /usr/local/bin/
```

Next, you must edit the config file. 
```bash
sudo nano /etc/systemd/system/node_exporter.service
```
Copy the `node_exporter.service` from [this Github Repo](https://github.com/Saleha-Abid/ultron.git). 

Then start the service,
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now node_exporter
```
> ***Note:*** `node_exporter` exports metrics on the port 9100.
{.is-success}

To check if it works, on each node

```bash
curl http://localhost:9100/metrics
```

## Slurm Reports
To collect data from slurm, simply edit the `slurm.conf` file on each node. Add these two lines,

```bash
SlurmctldParameters=enable_prometheus
MetricsType=metrics/openmetrics
```

Then restart `slurmctld` service on the head nodes. Remember, while the `node_exporter` collected data from each node separately, we'll only be ask the primary head node for slurm reports.

```bash
sudo systemctl restart slurmctld
```

> ***Note:*** Slurm exports data on port 6817
{.is-success}

To chek if data is being exported,
```bash
curl http://localhost:6817/metrics/nodes
```

## GPU Reports
As opposed to config files that we used to collect data for the two categories above, we'll be using containerized to collect data for GPUs. As head nodes do not use their GPUs in the cluster's computation, their logs are unnecessary. On the compute nodes, install the necessary toolkits,

```bash
sudo apt install -y nvidia-container-toolkit
sudo nvidia-ctk runtime configure --runtime=docker
```

Then, start the containers,
```bash
sudo systemctl restart docker
sudo docker run -d   --name gpu-exporter   --restart always   --gpus all   -p 9400:9400   nvidia/dcgm-exporter:latest
```

To check if the containers are up,
```bash
sudo docker ps
```
`nvidia/dcgm-exporter` should be listed. 

> ***Note:*** `nvidia/dcgm-exporter` exports data on port 9400
{.is-success}


To check the metrics being exported, run on the compute nodes,
```bash
curl http://localhost:9400/metrics
```

## Summing Up
All our sensors are working perfectly! Now that we're done with Prometheus, we must work with Grafana to build dashboards.

Next: [Importing And Configuring Dashboards](/MonitoringUltron/ImportingAndConfiguringDashboards)

