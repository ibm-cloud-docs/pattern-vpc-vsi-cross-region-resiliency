---

copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Storage design
{: #storage-design}

The web app cross-region resiliency pattern uses highly available cloud storage options for the application, backup, and log data.

- Block Storage for VPC is used for the database tier. Block Storage for VPC is [highly available and durable](/docs/vpc?topic=vpc-storageavailability), provides built-in high availability within a single availability zone and the best IOPs and latency. Provision Block Storage for VPC in multiple zones to protect data from zone outages.

- Cloud Object Storage is used to store static web content as well as for logs and backups. Cloud Object Storage is highly available, durable, and secure and provides region and cross-region [resiliency options](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints). Use region Cloud Object Storage buckets for web content and logs and cross-region Cloud Object Storage buckets for backups.
