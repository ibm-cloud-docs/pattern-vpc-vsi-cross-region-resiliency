---

copyright:
  years: 2023
lastupdated: "2023-12-12"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# High availability design
{: #high-availability-design}

IBM Cloud supports high availability application deployments in a single zone, across multiple zones in a multi-zone region, and across multiple regions to achieve high availability.

Failure domains determine the level of protection from infrastructure failures for each option. Application instances deployed in multiple availability zones are within a metro area connected over a low latency network and data can be replicated synchronously across the zones. Application instances deployed in multiple regions are typically in different geos connected over a WAN and data can be replicated asynchronously across the regions. The following table shows application deployment options based on failure domains available in a public cloud.

| **HA Deployment** | **Single-Zone** **Single Region** | **Multi-Zone,**  **Single Region** | **Multi-Zone,**  **Multi-Region** |
|-------------------|-----------------------------------|------------------------------------|-----------------------------------|
| Availability      | Low/Med                           | High                               | Very High                         |
| Failure Domain    | Virtual Server / Physical Host    | Zone                               | Region                            |
| Cost / Complexity | Low                               | Medium                             | High                              |
{: caption="Table 1. High availability deployment options" caption-side="bottom"}

## High Availability Deployment Options on IBM Cloud
{: #high-availability-deployments}

### Single-Zone deployment
{: #single-zone}

In single-zone deployments, multiple compute instances are deployed in one zone leveraging [Placement Groups](https://cloud.ibm.com/docs/vpc?topic=vpc-about-placement-groups-for-vpc&interface=ui) to provision virtual servers in separate physical hosts and [VPC Autoscale](https://cloud.ibm.com/docs/vpc?topic=vpc-creating-auto-scale-instance-group&interface=ui) to enable dynamic adjustment of capacity based on load changes. Single zone deployment provides cost-effective solutions with 99.9% infrastructure availability which might be appropriate for non-production environments or non-business critical applications. However, there is no protection from zone outages.

### Multi-Zone, Single-Region Deployment
{: #multi-zone-single-region}

In a multi-zone, single-region deployment, multiple compute instances are deployed across two or more availability zones within a region. Multi-zone, single region deployment can provide up to 99.99% infrastructure availability, when the application is deployed across 3 availability zones. This deployment protects the application from zone failures and is suitable for production-level enterprise workloads with \>99.9% availability requirements. Actual application availability depends on the application high availability design.

### Multi-Zone, Multi-Region Deployment
{: #multi-zone-multi-region}

A multi-zone, multi-region deployment provides protection against region outages. This is the recommended deployment for mission critical applications with continuous or near continuous availability requirements. This deployment also supports out of region disaster recovery and business continuity policies with cross geo or distance requirements.

Multi-zone deployments rely on application-aware data replication across availability zones and support active-active and active-standby architecture patterns. Multi-zone, multi-region deployments support architecture patterns for enterprise applications with continuous availability/always on requirements. The following tables show a comparison of the different deployment options and recommended use.

| **Deployment**             | **Availability** | **Description**                                                                                                                                                                  | **Recommended Usage**                                                                                                                                                                      |
|------------------|------------------|------------------|------------------|
| Single Zone                | 99.9%[^footnote1]           | - Multiple compute instances in one zone \n - Protection from infrastructure failures \n - Low/Medium cost \n | - Low to medium priority applications \n - Non-production workloads |
| Multi-zone,  Single Region | 99.99%[^footnote2]          | - Multiple compute instances across 2 or more availability zones \n - Synchronous data replication across zones \n - Protection from zone outages \n - Medium/high cost | - Core business applications \n - Production level workloads with stringent resiliency requirements \n - Business continuity policies with country boundaries or geo data residence constraints |
| Multi-zone,  Multi-Region  | \>99.99%[^footnote3]        | - Multiple compute instances across multiple availability zones in 2 or more regions \n - Asynchronous data replication across regions \n - Protection from region outages \n - High cost | - Mission critical applications with continuous or near continuous availability requirements \n - Business continuity policies with cross geo or distance requirements \n - Disaster Recovery |
{: caption="Table 2. High availability deployment options" caption-side="bottom"}

[^footnote1]: Based on Cloud infrastructure [SLA](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document) . 99.99% availability requires redundant deployments across 3 availability zones within a region and application designed following active-active clustering architecture.

[^footnote2]: Based on Cloud infrastructure [SLA](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document) . 99.99% availability requires redundant deployments across 3 availability zones within a region and application designed following active-active clustering architecture.

[^footnote3]: Based on active-active architecture with application-aware data replication across regions
