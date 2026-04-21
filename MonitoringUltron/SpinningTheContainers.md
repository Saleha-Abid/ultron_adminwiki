---
title: Spinning the Containers
description: We use docker to spin up Grafana and Prometheus
published: true
date: 2026-04-21T05:02:54.008Z
tags: 
editor: markdown
dateCreated: 2026-04-20T18:23:55.091Z
---

# About Containerized Services
As the name explains, we have two services that we must run: Prometheus and Grafana. A good practice is to start these services in containers. Not only is this method easier, it is super scalable and indtustrially recongized. It also keeps all services mutually isolated so there are no conflicts.

## Starting the Containers
All you need is a simple `docker-compose.yml` file. This will pull all foreign containers from over the internet and start your services. Get this file from [this Github repository](https://github.com/Saleha-Abid/ultron.git). All you need for monitoring is compiled within the `monitoring` diectory.

On the backup head (raven02), make a directory `monitoring`. Create a `docker-compose.yml` and `prometheus.yml`. These are included in the Github Repository linked above. To start the containers run,

```bash
sudo docker-compose up -d
```

Check if Prometheus and Grafana successfully came up,

```bash
sudo docker ps
```

In your browser, go to `raven02-ip:3000` and `raven02-ip:9090` to view Grafana and Prometheus respectively. Login to Grafana using admin credentials.

## Summing Up
Congratualations, your containers are up! Metrics must be exported from each node now to be displayed on the dashboard.

Next: [Exporting Data From Nodes](/MonitoringUltron/ExportingDataFromNodes)