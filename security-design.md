---

copyright:
  years: 2023
lastupdated: "2023-12-13"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Security design
{: #security-design}

The web app cross-region resiliency pattern leverages IBM Cloud Data Protection Services to protect all application data, including configuration and meta data, as well as all security data, including logs and credentials to access application or cloud resources, from unauthorized disclosure, as follows:

-   Application, Databases, backup, and log data are encrypted at rest using storage encryption with customer managed keys through the integration of VPC Block and Cloud Object Storage services with [IBM Cloud Key Management Services (KMS)](https://cloud.ibm.com/docs/secrets-manager?topic=secrets-manager-mng-data&interface=ui#about-encryption).

-   The Web App encrypts data in transit using TLS encryption. The Secrets Manager cloud service is used to store and manage secrets and credentials to access applications and cloud resources, as well as SSL/TLS certificates and private keys.

-   Key Protect is used to support data encryption with customer provided keys to meet regulatory compliance requirements. Key Protect uses a shared FIPS 140-2 level 3 certified hardware security module (HSM) to store keys used by storage services for envelope encryption and is also used to offload TLS/SSL keys.

    -   Note that you could also use Hyper Protect Crypto Services (HPCS) as the Key Management Service. HPCS uses a dedicated HSM FIPS 140-2 Level 4 certified (highest level) and supports customer-managed master keys, giving the customer exclusive control of the entire key hierarchy. HPCS is recommended for financial services and other highly regulated industry applications.

-   Follow the disaster recovery recommendations provided in [Resiliency on IBM Cloud: Data Security Design Considerations](#_Data_Security).
