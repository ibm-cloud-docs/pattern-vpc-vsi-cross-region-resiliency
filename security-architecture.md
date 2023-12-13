---

copyright:
  years: 2023
lastupdated: "2023-11-28"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Security architecture decisions
{: #security-architecture}

The following are security architecture decisions for the Web App cross-region resiliency pattern.

## Data encryption architecture decisions
{: #data-encryption}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Encryption Approach | Encrypt all data to protect from unauthorized disclosure | - Encryption with provider keys \n - Encryption with customer-managed keys | Encryption with customer-managed keys | Encrypting data with customer-managed keys is recommended since it provides added security and customer control needed to meet regulatory compliance requirements. |
| Data Encryption at rest of Application | Encrypt all application data to protect from unauthorized disclosure | - Storage encryption with customer-managed keys \n - App-level encryption with customer-managed keys | Storage Encryption with customer-managed keys | Application data can be stored on VPC File, VPC Block, or Cloud Object Storage (COS) which support encryption with customer-managed keys by selecting a Key Management Service (KMS) for the respective storage service. |
| Data Encryption at rest of Backups | Encrypt all backup data to protect from unauthorized disclosure | Storage encryption with customer-managed keys App-level encryption | Storage encryption with customer-managed keys | Backup data can be stored in COS or VPC Block storage which support encryption with customer-managed keys by selecting a KMS. |
| Data Encryption at rest of Logs | Encrypt all operational and audit logs at rest to protect from unauthorized disclosure | Storage encryption with customer-managed keys App-level encryption | Storage encryption with customer-managed keys | All operational and audit logs are stored in COS which supports encryption with customer-managed keys by selecting a KMS. |
| Data encryption in transit of Web and App Tiers | - Encrypt all application data in transit to protect from unauthorized disclosure                                                                                                                                    | Application-level encryption with TLS                                                              | Application-level encryption with TLS                        | Web app uses HTTPS protocol to encrypt data transmissions.                                                                                                                                                                                                                           |
| Data encryption in transit of DB Tier                             |                                                                                                                                                                                                                      | Application-level encryption with TLS                                                              | Application-level encryption with TLS                        | The database application uses TLS to encrypt data in transit.                                                                                                                                                                                                                        |
{: caption="Table 1. Data encryption architecture decisions" caption-side="bottom"}

## Key management architecture decisions
{: #kms}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Key Lifecycle Management & HSM | Encrypt data at rest and in transit using customer managed keys to protect from unauthorized access  | Key Protect \n Hyper Protect Crypto Services (HPCS) | Key Protect | Key Protect is recommended for applications that need to comply with regulations requiring encryption of data with customer-managed keys. Key Protect provides key management services using a shared (multi-tenant) FIPS 140-2 Level 3 certified hardware security modules (HSMs). |
| | | | Hyper Protect Crypto Services (HPCS) | HPCS is recommended for financial services and highly regulated industry applications. HPCS provides Key Management Services with the highest level of security and control offered by any cloud provider in the industry. It uses a dedicated (single-tenant) FIPS 140-2 Level 4 certified Hardware Security Module and supports customer-managed master keys, giving the customer exclusive control of the entire key hierarchy. |
| Certificate Management | Protect secrets through their entire lifecycle and secure them using access control measures | Secrets Manager \n BYO Certificate Manager | Secrets Manager | IBM Secrets Manager creates, leases, and centrally manages secrets used by IBM Cloud Services or customer applications. Secrets are stored in a dedicated instance of Secrets Manager and can be encrypted using any of IBM Cloud Key Management Services. |
{: caption="Table 2. Key management architecture decisions" caption-side="bottom"}

## Identity and Access management architecture decisions
{: #iam}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Privileged Access Management        | - Ensure all operator actions are executed securely through a bastion host \n - Implement session recording to track all activities and note any potential threats \n - Manage access to resources & track commands issued | - BYO Bastion Host \n - BYO Bastion Host with Privileged Access Management (PAM) SW                  | BYO Bastion Host with Privileged Access Management (PAM) SW  | The Bastion Host is a Virtual Server instance provisioned via SSH over private network to securely access resources within IBM Cloud’s private network. Using PAM software is recommended to perform session recording and to help track and manage all access.                      |
{: caption="Table 3. Identity and access management architecture decisions" caption-side="bottom"}

## Application security architecture decisions
{: #app-security}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| DDoS                                | - Enforce information flow policies and protect the boundaries of the application. \n - Protect against or limit the effects of denial-of-service attacks.                                                              | - IBM Cloud Internet Services (CIS)                                                                | IBM Cloud Internet Services (CIS)                            | IBM Cloud Internet Services provides Distributed Denial of Service (DDoS) and Web Application Firewall security features to protect applications exposed to the public network                                                                                                       |
| Web Application Firewall            | - Protect web applications from application layer attacks.                                                                                                                                                           | - IBM Cloud Internet Services (CIS) \n - BYO Firewall on VPC VSI                                      | IBM Cloud Internet Services (CIS)                            |                                                                                                                                                                                                                                                                                      |
{: caption="Table 4. Application security architecture decisions" caption-side="bottom"}
