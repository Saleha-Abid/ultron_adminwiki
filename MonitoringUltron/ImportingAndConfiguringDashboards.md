---
title: Importing and Configuring Dashboards
description: Monitor Ultron's status through customized dashboards
published: true
date: 2026-04-21T16:42:13.740Z
tags: 
editor: markdown
dateCreated: 2026-04-20T18:26:46.709Z
---

# Building Dashboards
The final step in this section is to build the visual - beautiful dashboards that shall display metrics. It is very important that these dashboards are clean and friendly - a messy panel is no better than your black-and-white shell. For this purpose, we use pre-built dashboards will feq customizations.

## Import Ultron's Dashboards
Since we have already built on the existing panels to best suit the needs of monitoring Ultron, you don't need to work much. Just import the JSON file and your dashboard is up!

1. Go to [this Github Repository](https://github.com/Saleha-Abid/Grafana-Dashboards.git).You'll find the JSON files. Clone the repo or download them.
2. On Grafana (go to `raven02-ip:3000` and login as admin), go to the **Dashboards** section. Top-right, you'll find **New**. Select **Import** from the drop-down list.
3. Upload your JSON file. Then **Load** it. Name it appropriately and settle **UID Conflicts** if any. 
4. Do the above three steps for each of the three dashboards. Customize them if needed.

## Import and Customize Standard Dashboards
If you want to start afresh, you can check out **node_exporter's** and **slurm's** standard dashboards. For this,

1. Login to Grafana as the admin. In the **Dashboard** section, click **New** and select **Import** from the drop-down.
2. There is an input box titled **Find and import dashboards for common applications**. Under this add the ID `1860` for node_exporter, `24979` for Slurm and `12239` for nvidia/dcgm-exporter.
3. **Load** the dashboards. Name them appropriated and fix **UID conflicts** if any.
4. Happy customizing!

## Summing Up
The dashboards are up! Now you can monitor Ultron in peace and troubleshoot easily.
