---

copyright:
  years: 2023
lastupdated: "2023-12-12"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# Disaster recovery architecture decisions
{: #disaster-recovery-architecture}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| DR Approach   | * Ensure availability of resources in the event of unplanned outages \n * Establish alternate site for failover of workloads in the event of failure in the primary site \n * Support business targets for RPO/RTO \n * Support Business Continuity and Disaster Recovery plans | * Active-Active \n * Active-Standby / Hot DR Site \n * Active-Standby / Warm DR Site \n * Backup & Restore (Cold DR Site)   | Active-Active | Recommended approach for mission critical applications with continuous availability requirements and near zero RPO/RTO |
| | | | Active-Standby / Hot DR Site | Recommended approach for core business applications with \n  * High availability requirements \n  * RPO \< 15 mins \n * RTO \< 1 hour |
| | | | Active-Standby / Warm DR Site | Recommended approach for core business applications with \n * medium to high availability requirements \n * RPO \< 1 hour \n * RTO \< 4 hours |
| | | | Backup & Restore (Cold DR Site) | Recommended approach for: \n * Dev/test environments \n * Low priority workloads |
| DR Automation | Automate recovery tasks to minimize down time | * IBM Schematics \n * Terraform \n * Ansible \n * BYO tool | IBM Schematics | IBM Schematics provides Terraform-as-a-Service and Ansible-as-a-Service to automate the provisioning and configuration management of the web, app, and DB tiers in the DR site |
{: caption="Table 1. Disaster recovery architecture decisions" caption-side="bottom"}
