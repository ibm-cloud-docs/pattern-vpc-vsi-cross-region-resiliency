---

copyright:
  years: 2023
lastupdated: "2023-12-13"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Resiliency architecture decisions
{: #resiliency-architecture}

The following sections summarize the resiliency architecture decisions for workloads deployed on IBM Cloud VPC infrastructure.

## High Availability Architecture Decisions
{: #high-availability}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| High Availability Deployment | * Ensure availability of resources in the event of outages. \n * Support SLA targets for application availability. | Single zone, single region \n Multi zone, single region \n Multi-zone, multi region | Single zone, single region  | **Recommended approach for  low to medium priority applications or non-production workloads** \n * Provides protection from host failures \n  * Supports 99.9% availability |
| | | | Multi-zone, single region | **Recommended deployment for Multi-Zone Resiliency Pattern** \n - Provides protection from zone outage \n * Supports 99.99% availability for active-active application architecture with virtual servers deployed across 3 zones \n Recommended approach for: \n - Core business applications \n - Production level workloads with stringent resiliency requirements\ n  - Business continuity policies with country boundaries or geo data residence constraints |
| | | | Multi-zone, multi-region | **Recommended deployment for Cross-Region Resiliency Pattern** \n - Provides protection from region outage \n * Supports \>99.99% availability for active-active application architecture with application-aware data replication across regions \n  Recommended approach for: \n * Mission critical applications with continuous or near continuous availability requirements \n * Business continuity policies with cross geo or distance requirements \n * Disaster Recovery |
{: caption="Table 1. High availability architecture decisions" caption-side="bottom"}

## Backup and Restore Architecture Decisions
{: #backup-and-restore}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| App server backup | Backup App server to enable re-deployment of application in the event of unplanned outages | * IBM Cloud Backup \n * IBM Storage Protect | IBM Cloud Backup | Use Cloud Backup to create volume snapshots of App servers when application-consistent backups are not required. |
| DB server backup  | Backup DB server images to enable recovery of databases in the event of unplanned outages | * IBM Cloud Backup \n * IBM Storage Protect | IBM Storage Protect | Use Storage Protect to backup DB servers in DB tier since this is also the recommended tool for transaction consistent backups for the data volumes. |
| DB backup | Create transaction consistent database backups to enable recovery of database tier in the event of unplanned outages | * IBM Storage Protect \n * DB Backup Tool \n * BYO Tool | IBM Storage Protect | Use Storage Protect for application consistent backups of the database. \n * Storage Protect supports Oracle, IBM Db2, MongoDB, Microsoft SQL Server, SAP HANA |
| File Backup | Create file system backups for according to business processes | * IBM Storage Protect \n * BYO Backup Tool | IBM Storage Protect | Use Storage Protect if there is a business need to backup or recover specific files. |
| Backup Automation | Schedule regular database backups based on RPO requirements to enable data recovery in the event of unplanned outages | * IBM Cloud Backup \n * IBM Storage Protect \n * BYO Backup Tool | IBM Storage Protect | Use Storage Protect to schedule regular backups for the DB tier and define backup policies to manage the creation and deletion of backups. |
{: caption="Table 2. Backup and restore architecture decisions" caption-side="bottom"}

## Disaster Recovery Architecture Decisions
{: #disaster recovery}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| DR Approach   | * Ensure availability of resources in the event of unplanned outages \n * Establish alternate site for failover of workloads in the event of failure in the primary site \n * Support business targets for RPO/RTO \n * Support Business Continuity and Disaster Recovery plans | * Active-Active \n * Active-Standby / Hot DR Site \n * Active-Standby / Warm DR Site \n * Backup & Restore (Cold DR Site)   | Active-Active | Recommended approach for mission critical applications with continuous availability requirements and near zero RPO/RTO |
| | | | Active-Standby / Hot DR Site | Recommended approach for core business applications with \n  * High availability requirements \n  * RPO \< 15 mins \n * RTO \< 1 hour |
| | | | Active-Standby / Warm DR Site | Recommended approach for core business applications with \n * medium to high availability requirements \n * RPO \< 1 hour \n * RTO \< 4 hours |
| | | | Backup & Restore (Cold DR Site) | Recommended approach for: \n * Dev/test environments \n * Low priority workloads |
| DR Automation | Automate recovery tasks to minimize down time | * IBM Schematics \n * Terraform \n * Ansible \n * BYO tool | IBM Schematics | IBM Schematics provides Terraform-as-a-Service and Ansible-as-a-Service to automate the provisioning and configuration management of the web, app, and DB tiers in the DR site |
{: caption="Table 3. Disaster recovery architecture decisions" caption-side="bottom"}
