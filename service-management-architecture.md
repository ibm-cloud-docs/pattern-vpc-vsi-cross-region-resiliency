---

copyright:
  years: 2023
lastupdated: "2025-07-01"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Architecture decisions for service management
{: #service}

The following sections summarize the architecture decisions for service management for the Web App cross-region resiliency pattern.

## Architecture decisions for monitoring
{: #monitoring}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Operational Monitoring of Cloud infrastructure and services | Monitor system health to detect issues that might impact the availability of the system and determine the need to failover to an alternative site. | - IBM Cloud Monitoring \n - BYO Monitoring Tool | IBM Cloud Monitoring | IBM Cloud Monitoring collects and monitors operational metrics for cloud infrastructure as well as the cloud platform and services and provides a single view for all metrics |
| Operational Monitoring of Applications | Monitor app health to detect issues that might impact the availability of the app and determine the need to failover to an alternative site. | - IBM Cloud Monitoring \n - Instana (SaaS) \n - BYO Monitoring Tool | IBM Cloud Monitoring + Instana (SaaS) | Instana is used along with IBM Cloud Monitoring to get additional application performance metrics and automate application performance management. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis. |
{: caption="Architecture decisions for monitoring" caption-side="bottom"}

## Architecture decisions for logging
{: #logging}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Log Monitoring of Cloud infrastructure and services | Monitor operational logs to detect issues that might impact the availability of the system. | - IBM Cloud Logs \n - BYO Logging Tool | IBM Cloud Logs | IBM Cloud Logs collects operational logs from applications, platform resources, and infrastructure and provides interfaces to view and analyze all logs. |
| Log Monitoring of Web App | Monitor application operational logs to detect issues that might impact the availability of the app.| - IBM Cloud Logging \n - Application Logging Tool \n - BYO Logging Tool | IBM Cloud Logs + Application Logging Tool | Use the Application Logging Tool to send application logs to IBM Cloud Logs and aggregate application-specific log details. |
| Log Monitoring of DB | Monitor database logs to detect issues that might impact the availability of the database.| - IBM Cloud Logs \n - DB Tools \n - BYO Logging Tool | IBM Cloud Logs + DB Tools | DB tools are used along with IBM Cloud Logs to get more DB-specific log information. |
{: caption="Architecture decisions for logging" caption-side="bottom"}

## Architecture decisions for auditing
{: #auditing}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Audit Logging | Monitor audit logs to track changes to cloud resources and detect potential security problems. | {{site.data.keyword.logs_full_notm}}  | {{site.data.keyword.logs_full_notm}} | {{site.data.keyword.logs_full_notm}} provides an interface to capture, store, view, search, and monitor user-initiated actions to provision, access, and manage IBM Cloud resources. |
{: caption="Architecture decisions for auditing" caption-side="bottom"}

## Architecture decisions for alerting
{: #alerting}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Operational alerts        | Provide a mechanism to identify and send notifications about operational issues that are found across application and infrastructure. | * {{site.data.keyword.mon_full_notm}} \n * {{site.data.keyword.logs_full_notm}} \n * {{site.data.keyword.en_full}}    | {{site.data.keyword.mon_full_notm}} \n * {{site.data.keyword.logs_full_notm}} \n * {{site.data.keyword.en_full}}    | * {{site.data.keyword.mon_full_notm}} and {{site.data.keyword.logs_full_notm}} support the configuration of alerts to detect operational issues and send notifications to targeted channels. \n * {{site.data.keyword.en_short}} are used to route the alert events to service destinations to automate response actions. |
| Audit alerts              | Provide a mechanism to identify and send notifications about issues that are found in audit logs.                      | * {{site.data.keyword.logs_full_notm}} \n * {{site.data.keyword.mon_full_notm}} \n * {{site.data.keyword.en_short}} | {{site.data.keyword.logs_full_notm}} \n * {{site.data.keyword.mon_full_notm}} \n * {{site.data.keyword.en_short}} | * {{site.data.keyword.logs_full_notm}} supports the configuration of alerts to detect audit issues and send notifications to targeted channels. \n * {{site.data.keyword.en_short}} are used to route the alert events to service destinations to automate response actions.    |
{: caption="Architecture decisions for alerting" caption-side="bottom"}
