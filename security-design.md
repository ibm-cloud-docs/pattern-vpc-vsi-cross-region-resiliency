---

copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Security design
{: #security-design}

The web app cross-region resiliency pattern uses IBM Cloud Data Protection Services to protect all application data, including configuration and metadata, as well as all security data, including logs and credentials to access application or cloud resources, from unauthorized disclosure, as follows:

- Application, databases, backup, and log data are encrypted at rest that uses storage encryption with customer-managed keys through the integration of VPC Block and Cloud Object Storage services with [IBM Cloud Key Management Services (KMS)](https://cloud.ibm.com/docs/secrets-manager?topic=secrets-manager-mng-data&interface=ui#about-encryption).

- The web app encrypts data in transit by using TLS encryption. The Secrets Manager cloud service is used to store and manage secrets and credentials to access applications and cloud resources, as well as SSL/TLS certificates and private keys.

- Key Protect is used to support data encryption with customer-provided keys to meet regulatory compliance requirements. Key Protect uses a shared FIPS 140-2 level 3 certified hardware security module (HSM) to store keys that are used by storage services for envelope encryption and is also used to offload TLS/SSL keys.

   You could also use Hyper Protect Crypto Services (HPCS) as the Key Management Service. HPCS uses a dedicated HSM FIPS 140-2 Level 4 certified (highest level) and supports customer-managed master keys, giving the customer exclusive control of the entire key hierarchy. HPCS is recommended for financial services and other highly regulated industry applications.
   {: note}

- Follow the disaster recovery recommendations provided in [Virtual private cloud resiliency: Data security design](#_Data_Security).
