[Title]

Carol Hernandez

Doyoung Im

[Company]

December 1, 2023

Table of Contents

[Executive Summary](#executive-summary)

[1. Pattern Objectives](#1-pattern-objectives)

[2. Pattern Overview](#2-pattern-overview)

[3. Pattern Requirements](#3-pattern-requirements)

[4. Solution Architecture](#4-solution-architecture)

[4.1 Solution Architecture Diagram](#41-solution-architecture-diagram)

[4.2 Solution Components](#42-solution-components)

[5. Compute Design Considerations](#5-compute-design-considerations)

[6. Storage Design Considerations](#6-storage-design-considerations)

[7. Networking Design Considerations](#7-networking-design-considerations)

[8. Security Design Considerations](#8-security-design-considerations)

[9. Resiliency Design Considerations](#9-resiliency-design-considerations)

[9.1 High Availability Design](#_Toc151028096)

[9.2 Disaster Recovery Design](#_Toc151028097)

[9.3 Backup and Restore Design](#93-backup-and-restore-design)

[10. Service Management Design Considerations](#10-service-management-design-considerations)

[10.1 Monitoring Design](#_Toc151028100)

[10.2 Logging Design](#_Toc151028101)

[10.3 Auditing Design](#_Toc151028102)

[10.4 Alerting Design](#_Toc151028103)

[12. Architecture Decisions](#12-architecture-decisions)

[12.1 Compute Architecture Decisions](#121-compute-architecture-decisions)

[12.2 Storage Architecture Decisions](#122-storage-architecture-decisions)

[12.3 Networking Architecture Decisions](#123-networking-architecture-decisions)

[12.4 Security Architecture Decisions](#124-security-architecture-decisions)

[12.4 Resiliency Architecture Decisions](#124-resiliency-architecture-decisions)

[12.5 Service Management Architecture Decisions](#125-service-management-architecture-decisions)

[13. References](#13-references)

[14. Glossary of Terms/Abbreviations](#14-glossary-of-termsabbreviations)

[**No index entries found.**](#no-index-entries-found)

# Executive Summary

\<Note to author\> \<Leave this section blank – AI will be used to create\>

# 1. Pattern Objectives

The objective of this pattern is to provide a solution design for a 3-tier web architecture deployment on [IBM Cloud VPC Virtual Servers](https://cloud.ibm.com/docs/vpc?topic=vpc-getting-started&interface=ui) that meets disaster recovery requirements for enterprise workloads. This pattern is intended to:

-   Accelerate and simplify solution design by providing a standard IBM Cloud deployment architecture reference following the [IBM Architecture Framework](https://cloud.ibm.com/docs/architecture-framework).

-   Provide a prescriptive, end-2-end enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection to meet enterprise requirements.

-   Ensure requirements can be met from a performance, system availability and security perspective.

This document focuses on leveraging cloud platform capabilities to architect resilient applications on VPC Virtual Servers. It is not intended for applications deployed on Power Virtual Servers (PowerVS) and does not cover application or database high availability design.

# 2. Pattern Overview

The Cross-Region Resiliency Pattern for Web Apps deploys a 3-tier web application on VPC Virtual Servers using compute, storage, and network cloud resources as well as other Cloud Services provisioned in multiple availability zones across two regions to protect from region-wide natural disasters or outages.

This pattern is recommended to address out-of-region disaster recovery policies or business continuity policies with geo or distance compliance requirements. It supports recovery point objective (RPO)\<=15 mins and recovery time objective (RTO)\<=1 hour requirements.

Following the [Architecture Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro)\*, the Cross-Region Resiliency Pattern for Web Apps covers design considerations and architecture decisions for the following aspects and domains:

-   **Compute:** Virtual Servers

-   **Storage:** Primary Storage, Backup Storage

-   **Networking:** Enterprise Connectivity, Segmentation and Isolation, Cloud Native Connectivity, Load Balancing, DNS

-   **Security:** Data Security, Identity and Access Management, Application Security, Infrastructure and Endpoint Security

-   **Resiliency:** High Availability, Disaster Recovery, Backup and Restore,

-   **Service Management:** Monitoring, Logging, Auditing, Alerting

| Aspects            | Domains                 |                              |                            |                             |                             |                               |                           |
|--------------------|-------------------------|------------------------------|----------------------------|-----------------------------|-----------------------------|-------------------------------|---------------------------|
| Compute            | Bare Metal Servers      | Virtual Servers              | Virtualization             | Containers                  | Serverless                  |                               |                           |
| Storage            | Primary Storage         | Backup                       | Archive                    | Data  Migration             |                             |                               |                           |
| Networking         | Enterprise Connectivity | BYOIP/Edge Gateways          | Segmentation and Isolation | Cloud Native Connectivity   | Load Balancing              | CDN                           | DNS                       |
| Security           | Data Security           | Identity & Access Management | Application Security       | Infrastructure and Endpoint | Threat Detection & Response | Governance, Risk & Compliance |                           |
| Resiliency         | High Availability       | Disaster Recovery            | Backup and Restore         |                             |                             |                               |                           |
| Service Management | Monitoring              | Logging                      | Auditing                   | Alerting                    | Event Management            | Automated Deployment          | Management/ Orchestration |
| Data               | Application Integration | Data Ops                     | Data Analytics             | Data Storage                | Business Intelligence       | Artificial Intelligence       |                           |

Domains covered in this document

[Cross-Region Resiliency for Web Apps Solution Design Scope]

\*The Architecture Framework provides a consistent approach to design cloud solutions by addressing requirements across a set of "aspects" and "domains", which are technology-agnostic architectural areas that need to be considered for any enterprise solution. See [Introduction to the Architecture Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro) for more details.

# 3. Pattern Requirements

The following represents a typical set of requirements for enterprise-ready web applications deployed in a public cloud.

| **Aspect**         | **Requirements**                                                                                                                                      |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| Compute            | Provide properly isolated compute resources with adequate compute capacity for the applications.                                                      |
| Storage            | Provide storage that meets the application and database performance requirements.                                                                     |
| Networking         | Deploy workloads in isolated environment and enforce information flow policies.                                                                       |
|                    | Provide secure, encrypted connectivity to the cloud’s private network for management purposes.                                                        |
|                    | Distribute incoming application requests across available compute resources.                                                                          |
|                    | Support failover of application to alternate site in the event of planned or unplanned outages                                                        |
|                    | Provide public and private DNS resolution to support use of hostnames instead of IP addresses.                                                        |
| Security           | Ensure all operator actions are executed securely through a bastion host.                                                                             |
|                    | Protect the boundaries of the application against denial-of-service and application-layer attacks.                                                    |
|                    | Encrypt all application data in transit and at rest to protect from unauthorized disclosure.                                                          |
|                    | Encrypt all backup data to protect from unauthorized disclosure.                                                                                      |
|                    | Encrypt all security data (operational and audit logs) to protect from unauthorized disclosure.                                                       |
|                    | Encrypt all data using customer managed keys to meet regulatory compliance requirements for additional security and customer control.                 |
|                    | Protect secrets through their entire lifecycle and secure them using access control measures.                                                         |
| Resiliency         | Support application availability targets and business continuity policies.                                                                            |
|                    | Ensure availability of the application in the event of planned and unplanned outages                                                                  |
|                    | Provide highly available compute, storage, network, and other cloud services to handle application load and performance requirements.                 |
|                    | Backup application data to enable recovery in the event of unplanned outages.                                                                         |
|                    | Provide highly available storage for security data (logs) and backup data.                                                                            |
|                    | Automate recovery tasks to minimize down time                                                                                                         |
| Service Management | Monitor system and application health metrics and logs to detect issues that might impact the availability of the application.                        |
|                    | Generate alerts/notifications about issues that might impact the availability of applications to trigger appropriate responses to minimize down time. |
|                    | Monitor audit logs to track changes and detect potential security problems.                                                                           |
|                    | Provide a mechanism to identify and send notifications about issues found in audit logs.                                                              |

# 4. Solution Architecture

-   Web, Application, and Database tiers are deployed on VPC Virtual Server Instances (VSIs). Cloud Object Storage (COS) is used to store static web content. High performance VPC block storage (10 IOPS/GB) is used for the database tier.

-   The virtual servers in the Web and App tiers are placed within [Placement Groups](https://cloud.ibm.com/docs/vpc?topic=vpc-about-placement-groups-for-vpc&interface=ui) for host failure protection and are part of [Instance Groups](https://cloud.ibm.com/docs/vpc?topic=vpc-creating-auto-scale-instance-group&interface=ui) for autoscaling. A VPC Application Load Balancer (ALB) is used at the web and app tiers tier to route traffic to healthy application instances.

-   Web, Application, and Database tiers are deployed within the Workload Virtual Private Cloud (VPC). Management tools, such as backup tools, are deployed in the Management VPC. A local Transit Gateway allows traffic between the management and workload VPCs.

-   Virtual servers for each tier are placed in separate subnets within each availability zone. Security groups and ACLs are used as firewalls to limit access to virtual server instances for operational purposes and control network traffic to each web app tier.

-   The Cloud Internet Service is deployed as a proxy to the public VPC Application Load Balancer that front ends the web tier to provide Distributed Denial of Service (DDoS) protection and Web Application Firewall protection to the web servers exposed to the internet.

-   The Web Application is deployed across two regions using an active-standby approach to enable failover in the event of an outage of the primary region. A Global Transit Gateway connects VPCs across the two regions.

-   The Web and App tiers are deployed across two availability zones in the primary region and the DR region.

-   The Database tier is deployed in active-standby across two availability zones in the primary region with another standby replica in one availability zone in the DR region. Data replication is handled by the database software based on HA/DR configuration settings.

-   The Cloud Internet Service is configured as a Global Load Balancer to route traffic to the appropriate region.

-   All data is encrypted using customer-provided keys managed by Key Protect. All storage is encrypted at rest using storage encryption with customer-provided keys managed by Key Protect. Key Protect is provisioned in the primary region and configured with failover units in the second region.

-   Data is encrypted in transit using TLS encryption. A Secrets Manager instance is deployed in each region to store and manage SSL/TLS certificates.

-   IBM Storage Protect is used to create database backups to enable data recovery.

-   IBM Monitoring, IBM Logging, and Activity Tracker instances are deployed in each region. IBM Monitoring is used to check the health of the servers and storage as well as the health of the web application. IBM Logging is used to get logs from the servers, storage, and web application. Monitoring and Logging data is stored in Cloud Object Storage based on the selected retention period. Activity Tracker stores audit logs in Cloud Object Storage. Logs stored in COS are encrypted at rest using COS encryption with customer-provided keys.

-   A VPC VPN Client in the Management VPC in each region provides operational access to resources within the IBM Cloud private network.

-   A bastion host in the Management VPC in each region is used to provide remote access to the infrastructure and application for management purposes and to record all access and opertions performed by remote users for audit purposes.

## 4.1 Solution Architecture Diagram

![A diagram of a cloud server Description automatically generated](2050141b477bb83c52e6227c75d0adb0.png) [Cross-Region Resiliency for Web Apps Solution Architecture]

## 4.2 Solution Components

| Category           | Solution Components                                                                                                                                                                                                      | How it is used in solution                                                                                                            |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Compute            | [VPC VSIs](https://cloud.ibm.com/vpc-ext/provision/vs)                                                                                                                                                                   | Web, App, and database servers                                                                                                        |
| Storage            | [VPC Block Storage](https://cloud.ibm.com/docs/openshift?topic=openshift-vpc-block)                                                                                                                                      | Database servers storage                                                                                                              |
|                    | [Cloud Object Storage](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage) (COS)                                                                                      | Web app static content, backups, logs (application, operational and audit logs)                                                       |
| Networking         | [VPC Virtual Private Network (VPN) Client](https://cloud.ibm.com/docs/iaas-vpn?topic=iaas-vpn-getting-started)                                                                                                           | Remote access to manage resources in private network                                                                                  |
|                    | [Virtual Private Clouds (VPCs), Subnets, Security Groups (SGs), ACLs](https://cloud.ibm.com/docs/vpc?topic=vpc-getting-started)                                                                                          | VPCs for workload isolation Subnets, SGs, and ACLs for restricted access to web, app, and database tiers                              |
|                    | [Transit Gateway (TGW)](https://cloud.ibm.com/docs/transit-gateway?topic=transit-gateway-getting-started)                                                                                                                | Local Transit Gateway connects the Workload and Management VPCs within a region. Global Transit Gateway connects VPCs across regions. |
|                    | [Virtual Private Gateway & Virtual Private Endpoint (VPE)](https://cloud.ibm.com/docs/vpc?topic=vpc-about-vpe)                                                                                                           | Private network access to Cloud Services, e.g. Key Protect, COS, etc.                                                                 |
|                    | [VPC Application Load Balancer](https://cloud.ibm.com/docs/vpc?topic=vpc-load-balancers)                                                                                                                                 | Application Load Balancing for web and app tiers                                                                                      |
|                    | [Public Gateway](https://cloud.ibm.com/docs/vpc?topic=vpc-about-networking-for-vpc&interface=cli#public-gateway-for-external-connectivity)                                                                               | Web app access to the internet                                                                                                        |
|                    | [Cloud Internet Services (CIS)](https://cloud.ibm.com/docs/cis?topic=cis-getting-started)                                                                                                                                | Global Load balancing between regions. Public DNS resolution.                                                                         |
|                    | [DNS Services](https://cloud.ibm.com/docs/dns-svcs?topic=dns-svcs-about-dns-services)                                                                                                                                    | Private DNS resolution                                                                                                                |
| Security           | [IAM](https://cloud.ibm.com/docs/account?topic=account-cloudaccess)                                                                                                                                                      | IBM Cloud Identity & Access Management                                                                                                |
|                    | [BYO Bastion Host on VPC VSI with PAM SW](https://cloud.ibm.com/docs/framework-financial-services?topic=framework-financial-services-vpc-architecture-connectivity-bastion-tutorial-teleport)                            | Remote access with Privileged Access Management                                                                                       |
|                    | [Cloud Internet Services (CIS)](https://cloud.ibm.com/docs/cis?topic=cis-getting-started)                                                                                                                                | DDoS protection and Web App Firewall                                                                                                  |
|                    | [Key Protect](https://cloud.ibm.com/docs/key-protect?topic=key-protect-about)                                                                                                                                            | Key Management Service                                                                                                                |
|                    | [Secrets Manager](https://cloud.ibm.com/catalog/services/secrets-manager)                                                                                                                                                | Certificate and Secrets Management                                                                                                    |
| Service Management | [IBM Cloud Monitoring](https://cloud.ibm.com/docs/monitoring?topic=monitoring-about-monitor)                                                                                                                             | Apps and operational monitoring.                                                                                                      |
|                    | [IBM Log Analysis](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-getting-started)                                                                                                                           | Apps and operational logs                                                                                                             |
|                    | [IBM Cloud Activity Tracker](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started)                                                                                                         | Audit logs                                                                                                                            |
| Resiliency         | [Placement Groups](https://cloud.ibm.com/docs/vpc?topic=vpc-about-placement-groups-for-vpc&interface=ui) and [Instance Groups](https://cloud.ibm.com/docs/vpc?topic=vpc-creating-auto-scale-instance-group&interface=ui) | To avoid single points of failure and adjust capacity based on load changes                                                           |
|                    | VPC VSIs, VPC Block across multiple zones in two regions                                                                                                                                                                 | Web, app, database high availability and disaster recovery                                                                            |
|                    | [IBM Storage Protect](https://cloud.ibm.com/catalog/content/SPonIBMCloud-20c54034-d319-48c0-beb6-0b4adc54265c-global)                                                                                                    | Database backups                                                                                                                      |
|                    | [Cross-Region COS Buckets](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints-geo)                                                                                    | Backup storage                                                                                                                        |

# 5. Compute Design Considerations

The Cross-Region Resiliency pattern for Web Apps includes compute options that are highly available, properly isolated, provide adequate capacity for the applications, and can handle increased workload demands.

Provision VPC Virtual Servers for the web tier, application tier, and database tier as follows:

-   **VPC Virtual Server Profile**: select compute and memory profiles for each tier, based on tier capacity and performance requirements.

-   [**Placement Groups**](https://cloud.ibm.com/docs/vpc?topic=vpc-about-placement-groups-for-vpc&interface=ui): create Placement Groups for Virtual Servers to avoid single point of failures. Select “[power spread](https://cloud.ibm.com/docs/vpc?topic=vpc-about-placement-groups-for-vpc&interface=ui#power-spread-placement-groups-for-vpc)” to provision Virtual Servers in the Placement Group across different hosts to protect against host failures and on separate power and network domains to protect against power and network failures.

-   **VPC Autoscale**: create instance groups for autoscaling Virtual Servers in the Web and App tiers to adjust compute resources to handle dynamic changes in load conditions.

    -   Add the instance group to the Placement Group.

    -   Add a load balancer to the instance group to balance incoming requests across instances and configure specific health checks.

-   **Multi-zone deployment**: deploy Virtual Servers across multiple availability zones to protect the application from zone outages. Use a VPC Application Load Balancer (ALB) to front end the virtual servers in the web and app tiers. Use the Virtual Servers auto-scale instance group as the backend pool for the ALB to automatically adjust the server pool based on auto-scale policies.

# 6. Storage Design Considerations

The Cross-Region Resiliency pattern for Web Apps includes highly available cloud storage options for the application, backup, and log data.

The following are the IBM Cloud storage recommendations for Web Apps:

-   Use VPC Block Storage for the database tier. VPC Block storage is [highly available and durable](https://cloud.ibm.com/docs/vpc?topic=vpc-storageavailability) and provides built-in high availability within a single availability zone and provides the best IOPs and latency. Provision VPC block storage in multiple zones to protect data from zone outages.

-   Use Cloud Object Storage to store static web content as well as for logs and backups. Cloud Object Storage (COS) is highly available, durable, and secure and provides region and cross-region [resiliency options](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints). Use region COS buckets for web content and logs and cross-region COS buckets for backups.

# 7. Networking Design Considerations

The Cross-Region Resiliency pattern for Web Apps leverages IBM Cloud Virtual Private Cloud infrastructure and network services to segment the application tiers and support the application deployment across multiple availability zones in two regions.

-   Deploy the Web App within a Virtual Private Cloud (VPC) provisioned across multiple availability zones within a region to provide workload isolation within the public cloud. Place each Web App tier in a separate subnet in each availability zone. Use security groups and ACLs as firewalls to limit access to virtual server instances for operational purposes and to control network traffic to each web app tier.

-   Create an instance group for the VPC Virtual Server instances in the Web and App tiers to enable auto-scaling using a VPC Load Balancer.

-   Use VPC Application Load Balancers (ALB) to distribute incoming requests among the virtual server instances within the web and app tiers. Configure load balancing policies, rules, and check health settings to distribute the requests across the available virtual servers. Integrate the VPC ALB with the Virtual Servers Instance Group for the tier to auto-scale this backend pool based on load requirements.

-   Use a Public Application Load Balancer for the web tier in the front end. Use a Private Application Load Balancer for the application tier in the backend. Use the assigned FQDNs to send traffic to the ALBs to avoid connectivity problems.

-   Configure the Application Load Balancers for high availability by selecting the subnets in each availability zone where the virtual servers are deployed. The ALB automatically provisions load balancing appliances for each subnet zone.

-   Configure the Cloud Internet Service (CIS) as a proxy to the public VPC Load Balancers for the web tier to leverage CIS capabilities such as Web Application Firewall (WAF) and DDoS protection to secure the Web Application.

-   Use IBM Cloud Secrets Manager service to manage the Transport Layer Security (TLS) certificate for all incoming HTTPS requests. Make the SSL certificates available to the VPC Application Load Balancers to configure HTTPS encryption.

-   Use a Global Transit Gateway to enable connectivity between VPCs in the two regions for data replication to the DR site.

-   Use IBM Cloud Internet Services (CIS) Global Load Balancer to fail over to the DR region in the event of an outage in the primary region.

    -   Connect the Global Load Balancer to the public VPC Load Balancers for the web tier and configure health checks to monitor the availability of the Web Application in each region.

    -   Make the SSL certificates available to the CIS global load balancer to configure HTTPS encryption.

-   Pre-configure all networking in the primary and DR sites before any sort of disaster declaration.

    -   Select nonoverlapping CIDR blocks for VPC subnets in all locations.

    -   Since a Global Transit Gateway is used to connect the VPCs in the two regions, the Routes for CIDR blocks in one multizone region (MZR) are advertised to the other ones such that no routing changes are required in a disaster situation.

-   Use hostnames and DNS instead of IP addresses to minimize the number of changes required to redeploy an application in the DR site. Use CIS to provision and configure DNS records for public DNS resolution. Use IBM Cloud DNS to manage DNS records and resolve domain names from IBM Cloud's private network.

# 8. Security Design Considerations

The Cross-Region Resiliency pattern for Web Apps leverages IBM Cloud Data Protection Services to protect all application data, including configuration and meta data, as well as all security data, including logs and credentials to access application or cloud resources, from unauthorized disclosure, as follows:

-   Application, Databases, backup, and log data are encrypted at rest using storage encryption with customer managed keys through the integration of VPC Block and Cloud Object Storage services with [IBM Cloud Key Management Services (KMS)](https://cloud.ibm.com/docs/secrets-manager?topic=secrets-manager-mng-data&interface=ui#about-encryption).

-   The Web App encrypts data in transit using TLS encryption. The Secrets Manager cloud service is used to store and manage secrets and credentials to access applications and cloud resources, as well as SSL/TLS certificates and private keys.

-   Key Protect is used to support data encryption with customer provided keys to meet regulatory compliance requirements. Key Protect uses a shared FIPS 140-2 level 3 certified hardware security module (HSM) to store keys used by storage services for envelope encryption and is also used to offload TLS/SSL keys.

    -   Note that you could also use Hyper Protect Crypto Services (HPCS) as the Key Management Service. HPCS uses a dedicated HSM FIPS 140-2 Level 4 certified (highest level) and supports customer-managed master keys, giving the customer exclusive control of the entire key hierarchy. HPCS is recommended for financial services and other highly regulated industry applications.

-   Follow the disaster recovery recommendations provided in [Resiliency on IBM Cloud: Data Security Design Considerations](#_Data_Security).

# 9. Resiliency Design Considerations

## 9.1 High Availability Design

The Cross-Region Resiliency pattern for Web Apps deploys the 3-tier web architecture in two regions following the multi-zone, multi-region deployment described in **“VPC Resiliency on IBM Cloud”.**

The web tier and application tier are deployed in two availability zones within each region. Each tier is deployed across VPC Virtual Server Instances (VSIs) in a VPC Instance Group for Autoscaling. A public VPC Application Load Balancer (ALB) routes web requests to healthy virtual instances in the app tier. A private VPC ALB routes traffic to healthy virtual servers in the app tier.

Note that the Web and App tiers are subject to the 99.9% IBM Cloud infrastructure [SLA](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document) for a region. For 99.99% infrastructure SLA within the region, deploy the Web and App servers across 3 availability zones.

High availability at the database tier is typically achieved with an active-standby architecture that includes:

-   a primary database and one or more standby database replicas

-   data replication between the primary and standby replicas

-   a mechanism to failover from the primary database to the standby replica and back

In the Cross-Region Resiliency pattern for Web Apps, the database tier is deployed on Virtual Server Instances across two availability zones in the primary region following an active-standby architecture. Data is replicated to the standby copy in the same region by the database software using database specific replication and failover configuration options. A second standby database replica is configured in the DR region. Data is replicated asynchronously to the DR region by the database software using database specific HA/DR options.

Note that this database deployment architecture is subject to the 99.9% IBM Cloud infrastructure [SLA](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document) for a region. For 99.99% infrastructure SLA within the region, the database must be deployed on virtual servers across three availability zones within the region and use clustering and replication configurations which will be database specific and are beyond the scope of this document.

An alternative to deploying the database in VPC Virtual Server Instances is to use [IBM Cloud Databases](https://cloud.ibm.com/docs/cloud-databases?topic=cloud-databases-about) instances in a multi-zone region (MZR). IBM Cloud Databases provides highly available and scalable managed SQL and no-SQL databases with 99.99% SLA and low operational cost. Provision an instance of the [IBM Cloud Databases](https://cloud.ibm.com/docs/cloud-databases?topic=cloud-databases-about) in the primary region and configure database replicas to asynchronously replicate data to the DR region. See how to configure read-only replicas in a different region for [IBM Cloud Databases for MySQL](https://cloud.ibm.com/docs/databases-for-mysql?topic=databases-for-mysql-read-replicas), [IBM Cloud Databases for PostgreSQL](https://cloud.ibm.com/docs/databases-for-postgresql?topic=databases-for-postgresql-read-only-replicas&interface=ui#read-only-replicas-provision) , and [IBM Cloud Databases for EnterpriseDB](https://cloud.ibm.com/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-read-only-replicas&interface=ui).

## 9.2 Disaster Recovery Design

The Cross-Region Resiliency pattern for Web Apps deploys the 3-tier web architecture across two regions following an active-standby architecture. The active-standby with hot DR site approach, described in **“VPC Resiliency on IBM Cloud”**, is used to support Web Applications with RPO\<=15 mins and RTO\<=1 hour requirements.

The web, app, and database tiers are deployed in each region as specified in the [High Availability Design section](#91-high-availability-design). The Cloud Internet Service is configured as a Global Load Balancer with health checks to monitor the availability of the Web Application and route traffic to the DR region when needed.

This pattern relies on database-aware data replication to keep an application-consistent copy of the data in the DR region and minimize data loss in the event of unplanned regional outages in the primary region.

Note that the 3-tier Web Application could also be deployed across the two regions following an active-active architecture to support near zero RPO/RTO requirements. However, this would require application-level as well as database level support to maintain data consistency. For example, the application might need to become ‘region aware’, by having region-specific data sets, adding a lot of complexity to the application design. See [Always On: Assess, Design, Implement, and Manage Continuous Availability](http://www.redbooks.ibm.com/redpapers/pdfs/redp5109.pdf?_gl=1*12ze6gc*_ga*MTU5NjY3MTQzOS4xNjk1ODUxNTg0*_ga_FYECCCS21D*MTY5NTkwNDc3NS4zLjAuMTY5NTkwNjU3Ny4wLjAuMA..) for more information on how to design active-active application architectures across geographically dispersed sites.

## 9.3 Backup and Restore Design

The Cross-Region Resiliency pattern for Web Apps uses backup and restore to protect database contents from accidental deletion or corruption and provide short term storage for historical data.

Do the following to create transaction consistent database backups that can be quickly restored in the same region where the Web Application is deployed:

-   Use database-aware backup tools, such as [IBM Storage Protect](https://cloud.ibm.com/catalog/content/SPonIBMCloud-20c54034-d319-48c0-beb6-0b4adc54265c-global), to create and manage database backups.

-   Schedule regular automated database backups based on business Recovery Point Objective requirements.

-   Schedule regular file-level backups if needed to meet application and business process requirements.

Store database backups in [cross-region Cloud Object Storage](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints-geo) to enable recovery of historical data in the DR site in the event of a regional outage.

Note that Web, App, or Database server backups are not needed since the entire Web Application is deployed in standby in the DR region and database contents are replicated asynchronously to the DR site as specified in the [High Availability Design](#91-high-availability-design) and [Disaster Recovery Design](#_Disaster_Recovery_Design) sections.

# 10. Service Management Design Considerations

The Cross-Region Resiliency pattern for Web Apps uses IBM Cloud services to monitor the health of all the components of the solution, including infrastructure, cloud services, and application as well as operational logs, to detect and correct issues that might affect the availability of the Web Application.

## 10.1 Monitoring Design

Use IBM Cloud Monitoring to get a comprehensive view of the health of the Web App and cloud environment and enable a timely response to incidents, as follows:

1.  Provision one IBM Cloud Monitoring instance in each region where the application is deployed to collect and monitor platform metrics for Cloud Services provisioned in that region. See [Configuring Platform Metrics](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started#getting-started-step3-1) for details.

2.  Install and configure monitoring agents on each of the VPC Virtual Servers used to deploy the web, app, and database tiers. The monitoring agent collects the following metrics by default: system host, file, file system, process, and network metrics. See [Monitoring Infrastructure](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started#getting-started-step3-2) for details. IBM Cloud Monitoring also supports monitoring of application platform metrics through Prometheus exporters or custom metrics. See [Monitoring Features](https://cloud.ibm.com/docs/monitoring?topic=monitoring-features) for more details.

    -   Consider using [IBM Instana](https://www.ibm.com/docs/en/instana-observability/current?topic=overview) to get additional application performance metrics and automate application performance management for the Web, App, and Database tiers. IBM Instana provides data and actionable insights to monitor the applications and automate root-cause analysis.

3.  Use the Monitoring dashboard to view, aggregate, and analyze performance metrics.

4.  Define alerts for the conditions that you need to monitor and configure notification channels to trigger the appropriate actions and automate incident responses. For example, you can configure a Webhook notification to integrate IBM Cloud Monitoring with Service Now.

5.  Configure notification channels to inform operation and support teams when an alert is triggered to support incident response processes.

    -   Consider configuring streaming to forward selected metrics for further data processing, analysis or to trigger automated response based on specific values. See [Streaming Data](https://cloud.ibm.com/docs/monitoring?topic=monitoring-data_streaming#data_streaming_ui) for details.

6.  Backup historical metrics that might be needed for auditing purposes by querying and copying the data to cross-regional Cloud Object Storage buckets that can be accessed from another region in the event of a disaster.

## 10.2 Logging Design

Use IBM Log Analysis to monitor operational logs for applications, platform resources, and infrastructure as follows:

1.  Provision one IBM Logging instance in each region where the application is deployed to collect and monitor Platform Logs for Cloud Services provisioned in that region. See [Configuring Platform Logs](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-config_svc_logs) for details.

2.  Install and configure logging agents on each of the VPC Virtual Servers used to deploy the web, app, and database tiers. See [Logging with Infrastructure](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-infra_logging) for details. Use the [Ingestion REST API](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-ingest) to get the application logs.

3.  Use the Logging dashboard to view, analyze, and manage logs.

4.  Define alerts for the conditions that you need to monitor and integrate with IBM Cloud Monitoring to send notifications and manage log alerts along with metrics alerts. See [Integrating with IBM Cloud Monitoring](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-monitoring) for details.

5.  Configure streaming to forward logs to other tools such as data lakes or Security Information and Event Management (SIEM) for further analysis or threat detection and investigation. See [Streaming Data](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-streaming) for details.

6.  Copy logs to IBM Cloud Object Storage to support \>30 days data search or data retention policy requirements. See [Configuring Archiving Logs to COS](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-archiving-ov) for details.

## 10.3 Auditing Design

Use [IBM Cloud Activity Tracker](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started) to capture and monitor audit logs for Web Applications deployed on VPC Virtual Servers, as follows:

1.  Provision one IBM Cloud Activity Tracker instance in each region where the application is deployed to collect audit logs for IBM Cloud resources used by the application. See [Activity Tracker - Getting Started](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started#gs_objectives) for details.

2.  Install and configure logging agents on each of the VPC Virtual Servers used to deploy the web, app, and database tiers. See [Logging with Infrastructure](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-infra_logging) for details.

3.  Use the Logging dashboard to view, analyze, and manage logs.

4.  Create alerts to get notifications when configuration changes are made to the IBM Cloud account and integrate with IBM Cloud Monitoring to send notifications and manage audit log alerts along with metrics alerts. See [Integrating with IBM Cloud Monitoring](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-monitoring) for details.

5.  Configure streaming to forward logs to other tools such as data lakes or Security Information and Event Management (SIEM) for further analysis or threat detection and investigation. See [Streaming Data](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-streaming) for details.

6.  Copy audit logs to IBM Cloud Object Storage to support \>30 days data search or data retention policy requirements. See [Configuring Archiving Logs to COS](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-archiving-ov) for details.

## 10.4 Alerting Design

It is important to factor in incident detection, notification, escalation, discovery, and declaration to provide realistic, achievable objectives that provide business value. Use [IBM Cloud Event Notifications](https://cloud.ibm.com/docs/event-notifications?topic=event-notifications-en-about) to route events associated with IBM Cloud resources (event sources) to a destination (delivery target for a notification) to trigger actions and enable automated response to issues impacting the availability of Web Applications, as follows:

-   Provision an instance of the Event Notification Service in each region where the Web application is deployed.

-   Define and build event notifications by linking event sources and destinations. As an example, select event sources to detect cloud provider level (e.g., region, zone, services), network level (e.g. load balancers, global load balancers), security level and application level critical events and integrate them with destination targets. Select destinations such as ServiceNow to collect all events and assign owners and AIOps tool to automate response to events like file system is full.

-   Add IBM Cloud Monitoring as an event notification source to route operational monitoring and logging alert notifications as well as audit logging alert notifications for Web Apps. See [Add an Event Notification Source](https://cloud.ibm.com/docs/event-notifications?topic=event-notifications-en-add-source) for details.

-   Set service destinations such as Webhook and Code Engine for IBM Cloud Monitoring event sources to automate incident response and minimize down time for Web Apps.

# 12. Architecture Decisions

## 12.1 Compute Architecture Decisions

| **\#**                       | **AD**                                                         | **Requirement**                                                                                   | **Alternative**                                                      | **Decision**                      | **Rationale**                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Compute: Virtual Servers** |                                                                |                                                                                                   |                                                                      |                                   |                                                                                                                                                                                                                                                                                                                                                                                     |
| 1                            | Compute Web, App, DB Tiers                                     | - Provide properly isolated compute resources with adequate compute capacity for the applications | - VPC VSIs - VPC Dedicated Hosts                                     | VPC VSIs                          | x86 compute within isolated VPC network that can be quickly provisioned and scaled based on load requirements. Select the vCPU and memory configuration based on application requirements.                                                                                                                                                                                          |
| 2                            | VSI Profile Web Tier                                           |                                                                                                   | - Balanced - Compute - Memory                                        |  Compute Profile                  | Select a Compute since this tier tends to be higher in traffic workload and workloads are more CPU-intensive.                                                                                                                                                                                                                                                                       |
| 3                            | VSI Profile App Tier                                           |                                                                                                   | - Balanced - Compute - Memory                                        | Memory                            | Select a Memory profile since this tier tends to be more memory-intensive and involves more caching.                                                                                                                                                                                                                                                                                |
| 4                            | VSI Profile Database Tier                                      |                                                                                                   | - Balanced - Compute - Memory                                        | Balance                           | For databases deployed on virtual servers select a balance profile.                                                                                                                                                                                                                                                                                                                 |
| 5                            | Virtual Servers Placement Group Configuration Web, App Tiers   | - Provide compute resources that are highly available and can handle increased workload demands   | - Host Spread - Power Spread                                         | Power Spread                      | Use virtual server placement groups for each tier to avoid single point of failures. Use “power spread” for the placement group to ensure the virtual servers are placed on different computer hosts with different power and network supplies. Use auto-scale instance groups along with placement groups to adjust the number of virtual servers in response to workload demands. |
| 6                            | Virtual Servers High Availability Configuration Web, App Tiers |                                                                                                   | - Multiple instances-single zone - Multiple instances-multiple zones | Multiple instances-multiple zones | Deploy virtual servers in each placement group across multiple availability zones to protect the application from zone outages.                                                                                                                                                                                                                                                     |

## 12.2 Storage Architecture Decisions

| **\#**      | **AD**                             | **Requirement**                                                                         | **Alternative**                                                  | **Decision**       | **Rationale**                                                                                                                                                                               |
|-------------|------------------------------------|-----------------------------------------------------------------------------------------|------------------------------------------------------------------|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Storage** |                                    |                                                                                         |                                                                  |                    |                                                                                                                                                                                             |
| 1           | Main Storage Web, App Tier         | - Provide highly available storage that meets the application performance requirements. | - VPC Block storage  - VPC File storage  - Cloud Object storage  | COS                | Use COS to store large volumes of static content or unstructured data.                                                                                                                      |
| 2           | Main Storage Database Tier         | - Provide highly available storage that meets the database performance requirements.    | - VPC Block storage  - Managed DB (DBaaS)                        | VPC Block Storage  | For databases deployed on virtual servers use block storage for best performance.                                                                                                           |
| 3           | Logs Storage  (Audit, Operational) | - Provide highly available storage for logs                                             | - VPC Block storage  - Cloud Object storage                      | COS                | Cloud Object Storage provides low cost, high available storage and is integrated with operational and audit logging Cloud services.                                                         |
| 4           | Backup Storage                     | - Provide highly available storage for backups                                          | - VPC Block storage  - Cloud Object storage                      | COS                | Cloud Object Storage provides low cost, high available storage for data backups. Use cross-region Cloud Object Storage for data backups to enable recovery in the event of a region outage. |

## 12.3 Networking Architecture Decisions

## 

| **\#**                                    | **AD**                                | **Requirement**                                                                                                                       | **Alternative**                                                                                                     | **Decision**                                    | **Rationale**                                                                                                                                                                                                                                                                                              |
|-------------------------------------------|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Enterprise Connectivity**            |                                       |                                                                                                                                       |                                                                                                                     |                                                 |                                                                                                                                                                                                                                                                                                            |
| 1.1                                       | Connectivity for management           | - Provide secure, encrypted connectivity to the cloud’s private network for management purposes.                                      | - Client VPN for VPC - VPN for VPC                                                                                  | Client VPN for VPC                              | Client VPN for VPC provides client-to-site connectivity, which allows remote devices to securely connect to the VPC network using an OpenVPN software client.                                                                                                                                              |
| **2. Network Segmentation and Isolation** |                                       |                                                                                                                                       |                                                                                                                     |                                                 |                                                                                                                                                                                                                                                                                                            |
| 2.1                                       | Web App Deployment                    | - Deploy workloads in isolated environment and enforce information flow policies. - Provide isolated security zones between app tiers | - Virtual Private Clouds (VPCs) - Subnets - Security Groups (SGs) - ACLs                                            | VPCs, subnets, Security Groups (SGs) and ACLs   | VPCs provide secure, virtual networks for web apps which are logically isolated from other public cloud tenants. Subnets provide a range of private IP addresses for each Web app tier within a zone. Security Groups and ACLs are used as firewalls to limit access to virtual servers and web app tiers. |
| **3. Cloud Native Connectivity**          |                                       |                                                                                                                                       |                                                                                                                     |                                                 |                                                                                                                                                                                                                                                                                                            |
| 3.1                                       | Connectivity to Cloud Services        | - Provide secure connection to Cloud Services                                                                                         | - VPC Gateway +  Virtual Private Endpoints (VPE) - Private Cloud Service endpoints - Public Cloud Service Endpoints | VPC Gateway +  Virtual Private Endpoints (VPE)  | VPC Gateway +  Virtual Private Endpoints enable connectivity to IBM Cloud services using private IP addresses allocated from a VPC subnet.                                                                                                                                                                 |
| 3.2                                       | VPC to VPC Connectivity               | - Connect two or more VPCs over private network                                                                                       | - Local Transit Gateway - Global Transit Gateway                                                                    | Local Transit Gateway Global Transit Gateway    | The Local Transit Gateway enables connectivity between the Management and Workload VPCs in a region. The Global Transit Gateway connects VPCs across regions.                                                                                                                                              |
| **4. Load Balancing**                     |                                       |                                                                                                                                       |                                                                                                                     |                                                 |                                                                                                                                                                                                                                                                                                            |
| 4.1                                       | Application Load Balancer             | - Route web user http/https requests                                                                                                  | - VPC ALB - VPC NLB                                                                                                 | VPC ALB                                         | VPC ALB is recommended for web-based workloads. - Provides layer 4 and layer 7 load balancing - Supports HTTP, HTTPS, and TCP requests - Supports SSL offloading.                                                                                                                                          |
| 4.2                                       | Local Load Balancing:  Web Tier       | - Distribute user requests across zones for high availability                                                                         | - Public VPC ALB - Private VPC ALB - Public VPC NLB - Private VPC NLB                                               | Public ALB                                      | The public VPC ALB distributes incoming user requests among virtual servers in the web tier. The VPC ALB is configured with subnets across multiple zones for multi-zone availability.                                                                                                                     |
| 4.3                                       | Local Load Balancing:  App & DB Tiers | - Distribute requests across zones for high availability                                                                              | - Public VPC ALB - Private VPC ALB - Public VPC NLB - Private VPC NLB                                               | Private ALB                                     | The Private VPC ALB distributes traffic among virtual servers within the app and DB tiers. The VPC ALB is configured with subnets across multiple zones for multi-zone availability.                                                                                                                       |
| 4.4                                       | Global Load Balancing                 | - Distribute requests across regions or fail over workloads to alternate region in the event of failure in the primary site           | - IBM Cloud DNS (Private) - IBM Cloud Internet Services (public)                                                    | IBM Cloud Internet Services (public)            | The Cloud Internet Services (CIS) Global Load Balancer distributes user requests across regions in multi-region web app deployments.                                                                                                                                                                       |
| **5. Domain Name System (DNS)**           |                                       |                                                                                                                                       |                                                                                                                     |                                                 |                                                                                                                                                                                                                                                                                                            |
| 5.1                                       | Public DNS                            | - Provide DNS resolution to support use of hostnames instead of IP addresses for applications                                         | - IBM Cloud Internet Services (CIS) - IBM Cloud DNS                                                                 | IBM Cloud Internet Services (CIS)               | IBM Cloud Internet Services supports provisioning and configuring DNS records for public DNS resolution and can be integrated with the public VPC ALBs for the web tier.                                                                                                                                   |
| 5.2                                       | Private DNS                           |                                                                                                                                       | - IBM Cloud DNS                                                                                                     | IBM Cloud DNS                                   | IBM Cloud DNS manages private DNS records, resolves domain names from IBM Cloud's private network, and can be integrated with the private VPC ALBs for the app and DB tiers.                                                                                                                               |

## 12.4 Security Architecture Decisions

| **\#**                                                         | **AD**                              | **Requirement**                                                                                                                                                                                                      | **Alternative**                                                                                    | **Decision**                                                 | **Rationale**                                                                                                                                                                                                                                                                        |
|----------------------------------------------------------------|-------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Data: Encryption at Rest**                                |                                     |                                                                                                                                                                                                                      |                                                                                                    |                                                              |                                                                                                                                                                                                                                                                                      |
| 1.1                                                            | Encryption Approach                 | - Encrypt all data to protect from unauthorized disclosure                                                                                                                                                           | - Encryption with provider keys - Encryption with customer-managed keys                            | Encryption with customer-managed keys                        | Encrypting data with customer-managed keys is recommended since it provides added security and customer control needed to meet regulatory compliance requirements.                                                                                                                   |
| 1.2                                                            | Data Encryption  Web and App Tiers  | - Encrypt all application data at rest to protect from unauthorized disclosure                                                                                                                                       | - Storage encryption with customer-managed keys - App-level encryption with customer-managed keys  | Storage Encryption with customer-managed keys                | Cloud Object Storage (COS) is used to store static content and supports encryption with customer-managed keys by selecting a Key Management Service (KMS).                                                                                                                           |
| 1.3                                                            | Data Encryption  DB Tier on VSIs    | - Encrypt all application data at rest to protect from unauthorized disclosure                                                                                                                                       | - Storage encryption with customer-managed keys - App-level encryption with customer-managed keys  | - Storage encryption with customer-managed keys              | Databases deployed on VPC VSIs store data in VPC Block Storage which supports encryption with customer-managed keys by selecting a KMS.                                                                                                                                              |
| 1.4                                                            | Data Encryption  Backups            | - Encrypt all backup data to protect from unauthorized disclosure                                                                                                                                                    | Storage encryption with customer-managed keys App-level encryption                                 | Storage encryption with customer-managed keys                | Backup data is stored in COS which supports encryption with customer-managed keys by selecting a KMS.                                                                                                                                                                                |
| 1.5                                                            | Data Encryption  Logs               | - Encrypt all operational and audit logs at rest to protect from unauthorized disclosure                                                                                                                             | Storage encryption with customer-managed keys App-level encryption                                 | Storage encryption with customer-managed keys                | All operational and audit logs are stored in COS which supports encryption with customer-managed keys by selecting a KMS.                                                                                                                                                            |
| **2. Data: Encryption in Transit**                             |                                     |                                                                                                                                                                                                                      |                                                                                                    |                                                              |                                                                                                                                                                                                                                                                                      |
| 2.1                                                            | Web and App Tiers                   | - Encrypt all application data in transit to protect from unauthorized disclosure                                                                                                                                    | Application-level encryption with TLS                                                              | Application-level encryption with TLS                        | Web app uses HTTPS protocol to encrypt data transmissions.                                                                                                                                                                                                                           |
| 2.2                                                            | DB Tier                             |                                                                                                                                                                                                                      | Application-level encryption with TLS                                                              | Application-level encryption with TLS                        | The database application uses TLS to encrypt data in transit.                                                                                                                                                                                                                        |
| **3. Data: Key Lifecycle Management & Certificate Management** |                                     |                                                                                                                                                                                                                      |                                                                                                    |                                                              |                                                                                                                                                                                                                                                                                      |
| 3.1                                                            | Key Lifecycle Management & HSM      | - Encrypt data at rest and in transit using customer managed keys to protect from unauthorized access                                                                                                                | Key Protect Hyper Protect Crypto Services (HPCS)                                                   | Key Protect                                                  | Key Protect is recommended for applications that need to comply with regulations requiring encryption of data with customer-managed keys. Key Protect provides key management services using a shared (multi-tenant) FIPS 140-2 Level 3 certified hardware security modules (HSMs).  |
| 3.2                                                            | Certificate Management              | - Protect secrets through their entire lifecycle and secure them using access control measures                                                                                                                       | Secrets Manager BYO Certificate Manager                                                            | Secrets Manager                                              | IBM Secrets Manager creates, leases, and centrally manages secrets used by IBM Cloud Services or customer applications. Secrets are stored in a dedicated instance of Secrets Manager and can be encrypted using any of IBM Cloud Key Management Services.                           |
| **4. Identity and Access**                                     |                                     |                                                                                                                                                                                                                      |                                                                                                    |                                                              |                                                                                                                                                                                                                                                                                      |
| 4.1                                                            | Privileged Access Management        | - Ensure all operator actions are executed securely through a bastion host - Implement session recording to track all activities and note any potential threats - Manage access to resources & track commands issued | - BYO Bastion Host  - BYO Bastion Host with Privileged Access Management (PAM) SW                  | BYO Bastion Host with Privileged Access Management (PAM) SW  | The Bastion Host is a Virtual Server instance provisioned via SSH over private network to securely access resources within IBM Cloud’s private network. Using PAM software is recommended to perform session recording and to help track and manage all access.                      |
| **5. Application Security**                                    |                                     |                                                                                                                                                                                                                      |                                                                                                    |                                                              |                                                                                                                                                                                                                                                                                      |
| 5.1                                                            | DDoS                                | - Enforce information flow policies and protect the boundaries of the application. - Protect against or limit the effects of denial-of-service attacks.                                                              | - IBM Cloud Internet Services (CIS)                                                                | IBM Cloud Internet Services (CIS)                            | IBM Cloud Internet Services provides Distributed Denial of Service (DDoS) and Web Application Firewall security features to protect applications exposed to the public network                                                                                                       |
| 5.2                                                            | Web Application Firewall            | - Protect web applications from application layer attacks.                                                                                                                                                           | - IBM Cloud Internet Services (CIS) - BYO Firewall on VPC VSI                                      | IBM Cloud Internet Services (CIS)                            |                                                                                                                                                                                                                                                                                      |

## 12.4 Resiliency Architecture Decisions

| **\#**                    | **AD**                       | **Requirement**                                                                                                                                                                                                                                                        | **Alternative**                                                                                                    | **Decision**                 | **Rationale**                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|---------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. High Availability**  |                              |                                                                                                                                                                                                                                                                        |                                                                                                                    |                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|  1.1                      | High Availability Deployment | - Ensure availability of resources in the event of outages. - Support SLA targets for application availability.                                                                                                                                                        | - Single zone, single region - Multi zone, single region - Multi zone, multi region                                | Multi zone, multi region     | **Recommended deployment for Cross-Region Resiliency Pattern** - Provides protection from region outage - Supports \>99.99% availability for active-active application architecture with application-aware data replication across regions  Recommended approach for: - Mission critical applications with continuous or near continuous availability requirements - Business continuity policies with cross geo or distance requirements - Disaster Recovery |
| **2. Disaster Recovery**  |                              |                                                                                                                                                                                                                                                                        |                                                                                                                    |                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 2.1                       | **DR Approach**              | - Ensure availability of resources in the event of unplanned outages - Establish alternate site for failover of workloads in the event of failure in the primary site - Support business targets for RPO/RTO - Support Business Continuity and Disaster Recovery plans | - Active-Active - Active-Standby / Hot DR Site - Active-Standby / Warm DR Site - Backup & Restore (Cold DR Site)   | Active-Standby / Hot DR Site | Recommended approach for: Core business applications with  - High availability requirements  - RPO \< 15 mins - RTO \< 1 hour                                                                                                                                                                                                                                                                                                                                 |
| **3. Backup and Restore** |                              |                                                                                                                                                                                                                                                                        |                                                                                                                    |                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 3.1                       | DB server backup             | - Backup DB server images to enable recovery of databases in the event of unplanned outages                                                                                                                                                                            | - IBM Cloud Backup - IBM Storage Protect                                                                           | IBM Storage Protect          | Use Storage Protect to backup DB servers in DB tier since this is also the recommended tool for transaction consistent backups for the data volumes.                                                                                                                                                                                                                                                                                                          |
| 3.2                       | DB backup                    | - Create transaction consistent database backups to enable recovery of database tier in the event of unplanned outages                                                                                                                                                 | - IBM Storage Protect - DB Backup Tool - BYO Tool                                                                  | IBM Storage Protect          | Use Storage Protect for application consistent backups of the database servers. - Storage Protect supports Oracle, IBM Db2, MongoDB, Microsoft SQL Server, SAP HANA                                                                                                                                                                                                                                                                                           |
| 3.3                       | File Backup                  | - Create file system backups for according to business processes                                                                                                                                                                                                       | - IBM Storage Protect - BYO Backup Tool                                                                            | IBM Storage Protect          | Use Storage Protect if there is a business need to backup or recover specific files.                                                                                                                                                                                                                                                                                                                                                                          |
| 3.4                       | Backup Automation            | - Schedule regular database backups based on RPO requirements to enable data recovery in the event of unplanned outages                                                                                                                                                | - IBM Cloud Backup - IBM Storage Protect - BYO Backup Tool                                                         | IBM Storage Protect          | Use Storage Protect to schedule regular backups for the DB tier and define backup policies to manage the creation and deletion of backup                                                                                                                                                                                                                                                                                                                      |

## 12.5 Service Management Architecture Decisions

| **\#**            | **AD**                                         | **Requirement**                                                                                                                                                 | **Alternative**                                                    | **Decision**                                                       | **Rationale**                                                                                                                                                                                                                                                                                     |
|-------------------|------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Monitoring** |                                                |                                                                                                                                                                 |                                                                    |                                                                    |                                                                                                                                                                                                                                                                                                   |
| 1.1               | Operational Monitoring of Web, App, DB Servers | - Monitor system and app health to determine need to failover to alternate site. Operational metrics include CPU and memory usage, API response times, etc.     | - IBM Cloud Monitoring - BYO Monitoring Tool                       | IBM Cloud Monitoring                                               | - IBM Cloud Monitoring collects and monitors operational metrics for cloud infrastructure as well as cloud platform and services and provides a single view for all metrics                                                                                                                       |
| 1.2               | Operational Monitoring for Web App             |                                                                                                                                                                 | - IBM Cloud Monitoring - Instana (SaaS) - BYO Monitoring Tool      | IBM Cloud Monitoring + Instana (SaaS)                              | - Use Instana along with IBM Cloud Monitoring to get additional application performance metrics and automate application performance management for the Web, App, and Database tiers. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis. |
| 1.3               | Operational Monitoring for DB                  |                                                                                                                                                                 | - DB Tools - BYO Monitoring Tool - Instana (SaaS)                  | IBM Cloud Monitoring + DB Tool                                     | Use DB Tools along with IBM Cloud Monitoring to get additional DB-specific performance metrics.                                                                                                                                                                                                   |
| **2. Logging**    |                                                |                                                                                                                                                                 |                                                                    |                                                                    |                                                                                                                                                                                                                                                                                                   |
| 2.1               | Logs Monitoring for Web, App, DB Servers       | - Monitor operational logs to detect issues that might impact the availability of the system and app                                                            | - IBM Cloud Logging - BYO Logging Tool                             | IBM Cloud Logging                                                  | - IBM Logging collects operational logs from applications, platform resources, and infrastructure and provides interfaces to view and analyze all logs.                                                                                                                                           |
| 2.2               | Logs Monitoring for Web App                    |                                                                                                                                                                 | - IBM Cloud Logging - Application Logging - BYO Logging Tool       | IBM Cloud Logging + Application Logging                            | - Application logs can be sent to IBM Cloud Logging to get more application-specific logs                                                                                                                                                                                                         |
| 2.3               | Logs Monitoring for DB                         |                                                                                                                                                                 | - IBM Cloud Logging - DB Tools - BYO Logging Tool                  | IBM Cloud Logging +  DB Tools                                      | - Use DB tools along with IBM Cloud Logging to get more DB-specific log information.                                                                                                                                                                                                              |
| **3. Auditing**   |                                                |                                                                                                                                                                 |                                                                    |                                                                    |                                                                                                                                                                                                                                                                                                   |
| 3.1               | Audit Logging                                  | - Monitor audit logs to track changes to cloud resources and detect potential security problems                                                                 | IBM Cloud Activity Tracker - Hosted Event Search - Event Routing   | IBM Cloud Activity Tracker- Hosted Event Search                    | IBM Cloud Activity Tracker-Hosted Event Search provides interfaces to capture, store, view, search, and monitor user-initiated actions to provision, access, and manage IBM Cloud resources.                                                                                                      |
| **4. Alerting**   |                                                |                                                                                                                                                                 |                                                                    |                                                                    |                                                                                                                                                                                                                                                                                                   |
| 4.1               | Operational alerts                             | - Provide a mechanism to identify and send notifications about operational issues found across application and infrastructure                                   | IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications    | IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications    | - IBM Cloud Monitoring and IBM Cloud Logging support the configuration of alerts to detect operational issues and send notifications to targeted channels.  Event Notifications can be used to route the alert events to service destinations to automate response actions.                       |
| 4.2               | Audit alerts                                   | - Provide a mechanism to identify and send notifications about issues found in audit logs                                                                       | IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications | IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications | - IBM Activity Tracker supports the configuration of alerts to detect audit issues and send notifications to targeted channels.  Event Notifications can be used to route the alert events to service destinations to automate response actions.                                                  |

# 13. References

-   **VPC Resiliency on IBM Cloud White Paper**

-   Placement Groups for VPC: <https://cloud.ibm.com/docs/vpc?topic=vpc-about-placement-groups-for-vpc>

-   ALB Multi-Zone Configuration:

    [https://cloud.ibm.com/docs/vpc?topic=vpc-load-balancers-about&interface=api\#horizontal-scaling](https://cloud.ibm.com/docs/vpc?topic=vpc-load-balancers-about&interface=api#horizontal-scaling)

-   IBM Cloud SLA:

    [https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en\#detail-document](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document)

-   IBM Instana Monitoring Technology:

    <https://www.ibm.com/docs/en/instana-observability/current?topic=overview>

-   IBM Storage Protect:

    <https://cloud.ibm.com/catalog/content/SPonIBMCloud-20c54034-d319-48c0-beb6-0b4adc54265c-global>

# 14. Glossary of Terms/Abbreviations

# **No index entries found.**
