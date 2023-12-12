---

copyright:
  years: 2023
lastupdated: "2023-12-12"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# Security design
{: #security-design}

It is important to use encryption to protect data from unauthorized disclosure. All application data, including configuration and meta data, as well as all security data, including logs and credentials to access application or cloud resources must be protected.

IBM Cloud data services encrypt data by default using randomly generated keys and support encryption with customer-managed keys using a Key Management Service (KMS). The following table provides an overview of data protection services in IBM Cloud.

| **Services**                  | **Secret Types**                                                     | **Usage**                               | **Tenancy**   | **HSM Backed**         |
|-------------------------------|----------------------------------------------------------------------|-----------------------------------------|---------------|------------------------|
| Secrets Manager               | Arbitrary secrets IAM credentials Key-value secrets User Credentials | Access to resources                     | Single tenant | N/A                    |
|                               | SSL/TLS certificates                                                 | Data in-transit encryption              |               |                        |
| Key Protect                   | Symmetric encryption keys                                            | Data at-rest encryption TLS/SSL Offload | Multi tenant  | FIPS 140-2 Level 3 HSM |
| Hyper Protect Crypto Services | Symmetric encryption keys                                            | Data at-rest-encryption TLS/SSL Offload | Single tenant | FIPS 140-2 Level 4 HSM |
{: caption="Table 1. Key features of IBM Cloud data protection services" caption-side="bottom"}

Secret Management and Key Management Solutions in IBM Cloud are regional services deployed across multiple availability zones in a region for resiliency. For optimal performance and security, keep the root keys in the same region as the encrypted resources and follow disaster recovery recommendations for each data protection service, as follows:

-   To ensure cross-region availability of the secrets, provision a Secrets Manager instance in an alternate region and copy the secrets in the alternate region following the instructions provided in [this document](https://cloud.ibm.com/docs/secrets-manager?topic=secrets-manager-ha-dr&interface=ui#manual-backup).

-   If using Key Protect, configure the service in the primary region with failover to DR region to enable automatic re-routing of Key Protect requests in the event of a regional service outage. If using HPCS, configure failover crypto unit in DR region, and initialize and configure failover crypto units the same as the operational crypto units before the disaster happens, so they are available in the event of a regional outage. See [Restoring your data by using failover crypto units](https://cloud.ibm.com/docs/hs-crypto?topic=hs-crypto-restore-data#restore-data-failover-crypto-units) for more information about how to use failover crypto units to restore data.

-   "Disaster recovery" does not include the ability to recover from user-initiated accidental deletions. IBM Cloud provides a [30-day grace period to restore a deleted key](https://cloud.ibm.com/docs/key-protect?topic=key-protect-delete-purge-keys). To protect a key from accidental deletion after the 30-day grace period, the user must create and maintain up-to-date backups of the key.
