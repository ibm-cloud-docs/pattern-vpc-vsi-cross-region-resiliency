---

copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Architecture decisions for resiliency
{: #resiliency-architecture}

The following sections summarize the resiliency architecture decisions for workloads deployed on IBM Cloud VPC infrastructure.

## Architecture decisions for high availability
{: #high-availability}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| High Availability Deployment | * Ensure availability of resources if outages occur. \n * Support SLA targets for application availability. | - Single zone, single region \n - Multi zone, single region \n - Multi-zone, multi region | Multi-zone, multi-region | Recommended approach for mission-critical applications with continuous or near continuous availability and disaster recovery requirements \n - Provides protection from region outage \n * Supports \>=99.99% availability \n * Supports business continuity policies with cross geo or distance requirements |
{: caption="Table 1. Architecture decisions for high availability" caption-side="bottom"}

## Architecture decisions for backup and restore
{: #backup-and-restore}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| DB content backup | Create transaction-consistent database backups to enable recovery of database tier if unplanned outages occur | * IBM Storage Protect \n * DB Backup Tool \n * BYO Tool | IBM Storage Protect | IBM Storage Protect can create application consistent backups of the database. It supports Oracle, IBM Db2, MongoDB, Microsoft SQL Server, SAP HANA. |
| File Backup | Create file system backups for according to business processes | * IBM Storage Protect \n * BYO Backup Tool | IBM Storage Protect supports backup and recovery of specific files. |
| Backup Automation | Schedule regular database backups based on RPO requirements to enable data recovery if unplanned outages occur | * IBM Cloud Backup \n * IBM Storage Protect \n * BYO Backup Tool | IBM Storage Protect | IBM Storage Protect supports scheduling regular backups and defining backup policies to manage the creation and deletion of backups. |
{: caption="Table 2. Architecture decisions for backup and restore" caption-side="bottom"}

## Architecture decisions for disaster recovery
{: #disaster recovery}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| DR Approach   | * Ensure availability of resources if unplanned outages occur \n * Establish alternative site for failover of workloads if failure occurs in the primary site \n * Support business targets for RPO/RTO \n * Support Business Continuity and Disaster Recovery plans | * Active-Active \n * Active-Standby / Hot DR Site \n * Active-Standby / Warm DR Site \n * Backup & Restore (Cold DR Site)   | Active-Standby / Hot DR Site | Recommended approach for core business applications with \n  * High availability requirements \n  * RPO \< 15 mins \n * RTO \< 1 hour |
{: caption="Table 3. Architecture decisions for disaster recovery" caption-side="bottom"}
