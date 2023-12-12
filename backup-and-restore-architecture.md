---

copyright:
  years: 2023
lastupdated: "2023-12-12"

subcollection: whitepaper-vpc-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Backup and restore architecture decisions
{: #backup-and-restore-architecture}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| App server backup | Backup App server to enable re-deployment of application in the event of unplanned outages | * IBM Cloud Backup \n * IBM Storage Protect | IBM Cloud Backup | Use Cloud Backup to create volume snapshots of App servers when application-consistent backups are not required. |
| DB server backup  | Backup DB server images to enable recovery of databases in the event of unplanned outages | * IBM Cloud Backup \n * IBM Storage Protect | IBM Storage Protect | Use Storage Protect to backup DB servers in DB tier since this is also the recommended tool for transaction consistent backups for the data volumes. |
| DB backup | Create transaction consistent database backups to enable recovery of database tier in the event of unplanned outages | * IBM Storage Protect \n * DB Backup Tool \n * BYO Tool | IBM Storage Protect | Use Storage Protect for application consistent backups of the database. \n Storage Protect supports Oracle, IBM Db2, MongoDB, Microsoft SQL Server, SAP HANA |
| File Backup | Create file system backups for according to business processes | * IBM Storage Protect \n * BYO Backup Tool | IBM Storage Protect | Use Storage Protect if there is a business need to backup or recover specific files. |
| Backup Automation | Schedule regular database backups based on RPO requirements to enable data recovery in the event of unplanned outages | * IBM Cloud Backup \n * IBM Storage Protect \n * BYO Backup Tool | IBM Storage Protect | Use Storage Protect to schedule regular backups for the DB tier and define backup policies to manage the creation and deletion of backups. |
{: caption="Table 1. Backup and restore architecture decisions" caption-side="bottom"}
