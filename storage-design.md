---

copyright:
  years: 2023
lastupdated: "2023-12-13"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Storage design
{: #storage-design}

The web app cross-region resiliency pattern uses highly available cloud storage options for the application, backup, and log data.

-   Block Storage for VPC is used for the database tier. Block Storage for VPC is [highly available and durable](https://cloud.ibm.com/docs/vpc?topic=vpc-storageavailability), provides built-in high availability within a single availability zone and the best IOPs and latency. Provision VPC block storage in multiple zones to protect data from zone outages.

-   Object Storage is used to store static web content as well as for logs and backups. Object Storage (COS) is highly available, durable, and secure and provides region and cross-region [resiliency options](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints). Use region COS buckets for web content and logs and cross-region COS buckets for backups.
