---

copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Architecture decisions for storage
{: #storage-architecture}

The following are storage architecture decisions for the web app cross-region resiliency pattern.

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Main Storage for Web and App Tier         | Provide highly available storage that meets the application performance requirements. | - Block storage for VPC \n - File storage for VPC \n - Cloud Object storage  | Cloud Object Storage                | Cloud Object Storage is recommended to store large volumes of static content or unstructured data. |
| Main Storage for Database Tier         | Provide highly available storage that meets the database performance requirements.    | - Block storage for VPC \n - Managed DB (DBaaS)                        | Block Storage for VPC | Block storage provides the best performance for databases. |
| Logs Storage (Audit, Operational) | Provide highly available storage for logs                                             | - Block Storage for VPC \n - Cloud Object Storage                      | Cloud Object Storage                | Cloud Object Storage provides high available storage and is integrated with operational and audit logging cloud services. |
| Backup Storage                     | Provide highly available storage for backups                                          | - Block Storage for VPC \n - Cloud Object Storage                      | Cloud Object Storage                | Cloud Object Storage provides high available storage for data backups. |
{: caption="Architecture decisions for storage" caption-side="bottom"}
