---

copyright:
  years: 2023
lastupdated: "2023-11-28"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Storage design
{: #storage-design}

The Cross-Region Resiliency pattern for Web Apps includes highly available cloud storage options for the application, backup, and log data.

The following are the IBM Cloud storage recommendations for Web Apps:

-   Use VPC Block Storage for the database tier. VPC Block storage is [highly available and durable](https://cloud.ibm.com/docs/vpc?topic=vpc-storageavailability) and provides built-in high availability within a single availability zone and provides the best IOPs and latency. Provision VPC block storage in multiple zones to protect data from zone outages.

-   Use Cloud Object Storage to store static web content as well as for logs and backups. Cloud Object Storage (COS) is highly available, durable, and secure and provides region and cross-region [resiliency options](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints). Use region COS buckets for web content and logs and cross-region COS buckets for backups.
