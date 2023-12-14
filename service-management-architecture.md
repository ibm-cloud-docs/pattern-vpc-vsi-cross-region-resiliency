---

copyright:
  years: 2023
lastupdated: "2023-12-13"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Service management architecture decisions
{: #service}

The following sections summarize the service management architecture decisions for the Web App cross-region resiliency pattern.

## Monitoring architecture decisions
{: #monitoring}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Operational Monitoring of Cloud infrastructure and services | Monitor system and app health to determine need to failover to alternate site. Operational metrics include CPU and memory usage, API response times, etc. | - IBM Cloud Monitoring \n - BYO Monitoring Tool | IBM Cloud Monitoring | IBM Cloud Monitoring collects and monitors operational metrics for cloud infrastructure as well as cloud platform and services and provides a single view for all metrics |
| Operational Monitoring of Applications | | - IBM Cloud Monitoring \n - Instana (SaaS) \n - BYO Monitoring Tool | IBM Cloud Monitoring + Instana (SaaS) | Use Instana along with IBM Cloud Monitoring to get additional application performance metrics and automate application performance management. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis. |
{: caption="Table 5. Monitoring architecture decisions" caption-side="bottom"}

## Logging architecture decisions
{: #logging}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Operational Monitoring of Cloud infrastructure and services | Monitor system and app health to determine need to failover to alternate site. Operational metrics include CPU and memory usage, API response times, etc. | - IBM Cloud Monitoring \n - BYO Monitoring Tool | IBM Cloud Monitoring | IBM Cloud Monitoring collects and monitors operational metrics for cloud infrastructure as well as cloud platform and services and provides a single view for all metrics |
| Operational Monitoring of Applications | | - IBM Cloud Monitoring \n - Instana (SaaS) \n - BYO Monitoring Tool | IBM Cloud Monitoring + Instana (SaaS) | Use Instana along with IBM Cloud Monitoring to get additional application performance metrics and automate application performance management. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis. |
{: caption="Table 6. Logging architecture decisions" caption-side="bottom"}

## Auditing architecture decisions
{: #auditing}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Audit Logging | Monitor audit logs to track changes to cloud resources and detect potential security problems | IBM Cloud Activity Tracker \n - Hosted Event Search \n - Event Routing   | IBM Cloud Activity Tracker- Hosted Event Search | IBM Cloud Activity Tracker-Hosted Event Search provides interfaces to capture, store, view, search, and monitor user-initiated actions to provision, access, and manage IBM Cloud resources. |
{: caption="Table 6. Auditing architecture decisions" caption-side="bottom"}

## Alerting architecture decisions
{: #alerting}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Operational alerts | Provide a mechanism to identify and send notifications about operational issues found across application and infrastructure | IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications | IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications | IBM Cloud Monitoring and IBM Cloud Logging support the configuration of alerts to detect operational issues and send notifications to targeted channels. \n Event Notifications are used to route the alert events to service destinations to automate response actions. |
| Audit alerts | Provide a mechanism to identify and send notifications about issues found in audit logs | IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications | IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications | IBM Activity Tracker supports the configuration of alerts to detect audit issues and send notifications to targeted channels. \n Event Notifications are used to route the alert events to service destinations to automate response actions.                            |
{: caption="Table 6. Alerting architecture decisions" caption-side="bottom"}
