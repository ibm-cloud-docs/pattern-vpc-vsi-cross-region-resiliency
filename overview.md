---

copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}

The web app cross-region resiliency pattern provides a solution design for a 3-tier web architecture deployment that meets disaster recovery requirements for enterprise workloads. It uses cloud platform capabilities to deploy resilient applications on [IBM Cloud Virtual Servers for VPC](https://cloud.ibm.com/docs/vpc?topic=vpc-getting-started&interface=ui). It is not intended for applications that are deployed on Power Virtual Servers (PowerVS) and does not cover application or database high availability design.

This pattern is recommended to address out-of-region disaster recovery policies or business continuity policies with geo or distance compliance requirements. It supports recovery point objective (RPO)\<=15 mins and recovery time objective (RTO)\<=1-hour requirements.

The web app cross-region resiliency pattern is intended to:

- Accelerate and simplify solution design by providing a standard IBM Cloud deployment architecture reference following the [IBM Architecture Framework](https://cloud.ibm.com/docs/architecture-framework).

- Provide a prescriptive, end-to-end enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection to meet enterprise requirements.

- Ensure that requirements can be met from a performance, system availability, and security perspective.
