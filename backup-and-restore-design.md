---

copyright:
  years: 2023
lastupdated: "2023-12-12"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# Backup and restore design
{: #backup-and-restore-design}

The backup and restore solution for an enterprise application deployed in the cloud must protect the application, configuration data, and application data. Ideally, configuration should be managed and controlled in Git as part of the DevOps approach.

Restoring data from a backup might take a long time (hours to days) depending on the amount of data to be restored. Therefore, backup and restore must not be considered as a mechanism for achieving high availability.

Backup and restore can be considered as a Disaster Recovery strategy to recover non-production workloads or production workloads that can tolerate recovery time objectives (RTO) and recovery point objectives (RPO) in the range of days.

## Backup and Restore Options on IBM Cloud
{: #backup-and-restore-options}

[IBM Cloud Backup for VPC](https://cloud.ibm.com/docs/vpc?topic=vpc-backup-service-about&interface=ui) is a cloud service that supports the creation and management of boot and data storage volume snapshots. Use IBM Cloud Backup for VPC and IBM Storage Protect to backup and restore applications deployed in VPC Virtual Servers as follows:

-   Virtual Server backup: create a custom image from the boot volume of the VPC Virtual Servers hosting the application and use them as the golden image for the application and configuration. Use the custom image to quickly recover the application instances in another availability zone or region. See [Creating an image from a volume](https://cloud.ibm.com/docs/vpc?topic=vpc-image-from-volume-vpc&interface=ui) for details. Note that the virtual server must be stopped to create an image from the boot volume.

-   Boot Volume backup: create snapshots of the boot volume for the VPC Virtual Servers hosting the application. [Restore the bootable snapshots](https://cloud.ibm.com/docs/vpc?topic=vpc-baas-vpc-restore&interface=ui#baas-restore-concept-boot) and use them to re-create the application virtual server instances. Note that bootable snapshots are not fully hydrated which will impact initial performance.

-   Data Volume backup: create snapshots of the data volumes attached to the application. The snapshots replicate the data to other availability zones and regions to support the recovery of configuration or any other critical files. See [Creating Snapshots](https://cloud.ibm.com/docs/vpc?topic=vpc-snapshots-vpc-create&interface=ui) for details.

[IBM Storage Protect](https://cloud.ibm.com/catalog/content/SPonIBMCloud-20c54034-d319-48c0-beb6-0b4adc54265c-global) is an enterprise-level backup solution for virtual, physical, cloud, and software-defined environments, as well as core applications. Use IBM Storage Protect to create application-consistent backups for database applications deployed on VPC Virtual Servers and to create file or folder level backups.

| **Backup Feature**            | **IBM Storage Protect**                                                 | **IBM Cloud Backup for VPC**                                                                 |
|-------------------------------|-------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| **Backup Capabilities**       | Agent based \n Scheduled backups \n Backups Management                        | Scheduled backups \n Backups Management \n Fast restore clone \n Cross-regional copies                |
| **Backup Scope**              | Selected VSIs, selected volumes or files in VSIs                        | Selected volumes (boot or data) attached to any VSIs                                         |
| **File/Folder level support** | Yes                                                                     | No                                                                                           |
| **Backup Storage**            | VPC Block Storage or IBM COS                                            | IBM COS                                                                                      |
| **Database Protection**       | Application Consistent Backup (Oracle, IBM Db2, MongoDB, MS SQL Server) | Not supported                                                                                |
| **Encryption**                | In-transit and at rest                                                  | In-transit and at rest                                                                       |
| **Recommendation**            | DB or folder level backup for multiple VSIs                             | Complex backup operations for multiple VSIs that do not require application data consistency |
{: caption="Table 1. Compariosn of backup options for VPC VSIs" caption-side="bottom"}
