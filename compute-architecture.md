---

copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for compute
{: #compute-architecture}

The following are computer architecture decisions for the web app cross-region resiliency pattern.

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Compute for Web, App, DB Tiers   | Provide properly isolated compute resources with adequate compute capacity for the applications | - Virtual Servers for VPC \n - Virtual Servers for VPC-Dedicated Hosts   | Virtual Servers for VPC   | x86 compute within isolated VPC network that can be quickly provisioned and scaled based on load requirements. \n \n Select the vCPU and memory configuration based on application requirements. |
| Virtual Servers for VPC \n Profile for Web Tier          |                | - Balanced \n - Compute \n - Memory   |  Compute                  | The compute profile is recommended for the web tier, which tends to be higher in traffic workload and more CPU-intensive.  |
| Virtual Servers for VPC \n Profile for App Tier                 |               | - Balanced \n - Compute \n - Memory                   | Memory  | The memory profile is recommended for the app tier, which tends to be more memory-intensive and involves more caching. |
| Virtual Servers for VPC \n Profile for Database Tier                    |              | - Balanced \n - Compute \n - Memory               | Balance              | The balance profile is recommended for databases. |
| Virtual Servers for VPC \n Placement Group Configuration | Provide compute resources that are highly available and can handle increased workload demands   | - Host spread \n - Power spread     | Power Spread    | Power spread ensures that the virtual servers are placed on different computer hosts with different power and network supplies. |
| Virtual Servers High Availability Configuration |            | - Multiple instances-single zone \n - Multiple instances-multiple zones | Multiple instances-multiple zones | Virtual servers are deployed across multiple availability zones to protect the application from zone outages. |
{: caption="Table 1. Architecture decisions for compute" caption-side="bottom"}
