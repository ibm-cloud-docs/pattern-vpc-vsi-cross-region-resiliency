---

copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Resiliency design
{: #resiliency-design}

## High availability design
{: #high-availability-design}

The web app cross-region resiliency pattern deploys a 3-tier web architecture in two regions following the multi-zone, multi-region deployment described in [VPC Resiliency on IBM Cloud](/docs/vpc-resiliency).

The web tier and application tier are deployed in two availability zones within each region. Each tier is deployed across VPC Virtual Server Instances (VSIs) in a VPC Instance Group for Autoscaling. A public VPC Application Load Balancer (ALB) routes web requests to healthy virtual instances in the app tier. A private VPC ALB routes traffic to healthy virtual servers in the app tier.

The web and app tiers are subject to the 99.9% IBM Cloud infrastructure [SLA](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document){: external} for a region. For 99.99% infrastructure SLA within the region, deploy the web and app servers across 3 availability zones.

High availability at the database tier is typically achieved with an active-standby architecture that includes:

- A primary database and one or more standby database replicas
- Data replication between the primary and standby replicas
- A mechanism to failover from the primary database to the standby replica and back

In the web app cross-region resiliency pattern, the database tier is deployed on Virtual Server Instances across two availability zones in the primary region following an active-standby architecture. Data is replicated to the standby copy in the same region by the database software by using database-specific replication and failover configuration options. A second standby database replica is configured in the DR region. Data is replicated asynchronously to the DR region by the database software that uses database-specific HA/DR options.

This database deployment architecture is subject to the 99.9% IBM Cloud infrastructure [SLA](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document){: external} for a region. For 99.99% infrastructure SLA within the region, the database must be deployed on virtual servers across three availability zones within the region and use clustering and replication configurations that are database-specific and are beyond the scope of this document.

An alternative to deploying the database in VPC Virtual Server Instances is to use [IBM Cloud Databases](/docs/cloud-databases?topic=cloud-databases-about) instances in a multi-zone region (MZR). IBM Cloud Databases provide highly available and scalable managed SQL and no-SQL databases with 99.99% SLA and low operational cost. Provision an instance of the [IBM Cloud Databases](/docs/cloud-databases?topic=cloud-databases-about) in the primary region and configure database replicas to asynchronously replicate data to the DR region. See how to configure read-only replicas in a different region for [IBM Cloud Databases for MySQL](/docs/databases-for-mysql?topic=databases-for-mysql-read-replicas), [IBM Cloud Databases for PostgreSQL](/docs/databases-for-postgresql?topic=databases-for-postgresql-read-only-replicas&interface=ui#read-only-replicas-provision), and [IBM Cloud Databases for EnterpriseDB](/docs/databases-for-enterprisedb?topic=databases-for-enterprisedb-read-only-replicas&interface=ui).

## Backup and restore design
{: #backup-design}

The web app cross-region resiliency pattern uses backup and restore to protect database contents from accidental deletion or corruption and provide short-term storage for historical data.

Do the following to create transaction-consistent database backups that can be quickly restored in the same region where the web application is deployed:

- Use database-aware backup tools, such as [IBM Storage Protect](https://cloud.ibm.com/catalog/content/SPonIBMCloud-20c54034-d319-48c0-beb6-0b4adc54265c-global){: external}, to create and manage database backups.

- Schedule regular automated database backups based on business Recovery Point Objective requirements.

- Schedule regular file-level backups if needed to meet application and business process requirements.

Store database backups in [cross-region Cloud Object Storage](/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints-geo) to enable recovery of historical data in the DR site if a regional outage occurs.

Note that web, app, or database server backups are not needed since the entire web application is deployed in standby in the DR region and database contents are replicated asynchronously to the DR site as specified in the [High availability design](#91-high-availability-design) and [Disaster recovery design](#_Disaster_Recovery_Design) sections.

## Disaster recovery design
{: #dr-design}

The web app cross-region resiliency pattern deploys a 3-tier web architecture across two regions following an active-standby architecture. The active-standby with hot DR site approach, described in [VPC Resiliency on IBM Cloud](/docs/vpc-resiliency), is used to support web applications with RPO\<=15 mins and RTO\<=1-hour requirements.

The web, app, and database tiers are deployed in each region as specified in the [High availability design section](#91-high-availability-design). The Cloud Internet Service is configured as a global load balancer with health checks to monitor the availability of the web application and route traffic to the DR region when needed.

This pattern relies on database-aware data replication to keep an application-consistent copy of the data in the DR region and minimize data loss if unplanned regional outages occur in the primary region.

The 3-tier web application could also be deployed across the two regions following an active-active architecture to support near zero RPO/RTO requirements. However, this would require application-level as well as database-level support to maintain data consistency. For example, the application might need to become ‘region aware’, by having region-specific data sets, adding a lot of complexities to the application design. For more information on how to design active-active application architectures across geographically dispersed sites, see [Always On: Assess, Design, Implement, and Manage Continuous Availability](http://www.redbooks.ibm.com/redpapers/pdfs/redp5109.pdf?_gl=1*12ze6gc*_ga*MTU5NjY3MTQzOS4xNjk1ODUxNTg0*_ga_FYECCCS21D*MTY5NTkwNDc3NS4zLjAuMTY5NTkwNjU3Ny4wLjAuMA..){: external}.
