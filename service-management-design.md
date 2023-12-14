---

copyright:
  years: 2023
lastupdated: "2023-12-13"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# Service management design
{: #service-management-design}

The web app cross-region resiliency pattern uses IBM Cloud services to monitor the health of all the components of the solution, including infrastructure, cloud services, and application as well as operational logs, to detect and correct issues that might affect the availability of the Web Application.

## Monitoring Design
{: #smonitoring-design}

Use IBM Cloud Monitoring to get a comprehensive view of the health of the Web App and cloud environment and enable a timely response to incidents, as follows:

- Provision one IBM Cloud Monitoring instance in each region where the application is deployed to collect and monitor platform metrics for Cloud Services provisioned in that region. See [Configuring Platform Metrics](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started#getting-started-step3-1) for details.

- Install and configure monitoring agents on each of the VPC Virtual Servers used to deploy the web, app, and database tiers. The monitoring agent collects the following metrics by default: system host, file, file system, process, and network metrics. See [Monitoring Infrastructure](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started#getting-started-step3-2) for details. IBM Cloud Monitoring also supports monitoring of application platform metrics through Prometheus exporters or custom metrics. See [Monitoring Features](https://cloud.ibm.com/docs/monitoring?topic=monitoring-features) for more details.

    -   Consider using [IBM Instana](https://www.ibm.com/docs/en/instana-observability/current?topic=overview) to get additional application performance metrics and automate application performance management for the Web, App, and Database tiers. IBM Instana provides data and actionable insights to monitor the applications and automate root-cause analysis.

- Use the Monitoring dashboard to view, aggregate, and analyze performance metrics.

- Define alerts for the conditions that you need to monitor and configure notification channels to trigger the appropriate actions and automate incident responses. For example, you can configure a Webhook notification to integrate IBM Cloud Monitoring with Service Now.

- Configure notification channels to inform operation and support teams when an alert is triggered to support incident response processes.

    -   Consider configuring streaming to forward selected metrics for further data processing, analysis or to trigger automated response based on specific values. See [Streaming Data](https://cloud.ibm.com/docs/monitoring?topic=monitoring-data_streaming#data_streaming_ui) for details.

- Backup historical metrics that might be needed for auditing purposes by querying and copying the data to cross-regional Cloud Object Storage buckets that can be accessed from another region in the event of a disaster.

## Logging Design
{: #logging-design}

Use IBM Log Analysis to monitor operational logs for applications, platform resources, and infrastructure as follows:

- Provision one IBM Logging instance in each region where the application is deployed to collect and monitor Platform Logs for Cloud Services provisioned in that region. See [Configuring Platform Logs](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-config_svc_logs) for details.

- Install and configure logging agents on each of the VPC Virtual Servers used to deploy the web, app, and database tiers. See [Logging with Infrastructure](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-infra_logging) for details. Use the [Ingestion REST API](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-ingest) to get the application logs.

- Use the Logging dashboard to view, analyze, and manage logs.

- Define alerts for the conditions that you need to monitor and integrate with IBM Cloud Monitoring to send notifications and manage log alerts along with metrics alerts. See [Integrating with IBM Cloud Monitoring](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-monitoring) for details.

- Configure streaming to forward logs to other tools such as data lakes or Security Information and Event Management (SIEM) for further analysis or threat detection and investigation. See [Streaming Data](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-streaming) for details.

- Copy logs to IBM Cloud Object Storage to support \>30 days data search or data retention policy requirements. See [Configuring Archiving Logs to COS](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-archiving-ov) for details.

## Auditing Design
{: #auditing-design}

Use [IBM Cloud Activity Tracker](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started) to capture and monitor audit logs for Web Applications deployed on VPC Virtual Servers, as follows:

- Provision one IBM Cloud Activity Tracker instance in each region where the application is deployed to collect audit logs for IBM Cloud resources used by the application. See [Activity Tracker - Getting Started](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started#gs_objectives) for details.

- Install and configure logging agents on each of the VPC Virtual Servers used to deploy the web, app, and database tiers. See [Logging with Infrastructure](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-infra_logging) for details.

- Use the Logging dashboard to view, analyze, and manage logs.

- Create alerts to get notifications when configuration changes are made to the IBM Cloud account and integrate with IBM Cloud Monitoring to send notifications and manage audit log alerts along with metrics alerts. See [Integrating with IBM Cloud Monitoring](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-monitoring) for details.

- Configure streaming to forward logs to other tools such as data lakes or Security Information and Event Management (SIEM) for further analysis or threat detection and investigation. See [Streaming Data](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-streaming) for details.

- Copy audit logs to IBM Cloud Object Storage to support \>30 days data search or data retention policy requirements. See [Configuring Archiving Logs to COS](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-archiving-ov) for details.

## Alerting Design
{: #alerting-design}

It is important to factor in incident detection, notification, escalation, discovery, and declaration to provide realistic, achievable objectives that provide business value. Use [IBM Cloud Event Notifications](https://cloud.ibm.com/docs/event-notifications?topic=event-notifications-en-about) to route events associated with IBM Cloud resources (event sources) to a destination (delivery target for a notification) to trigger actions and enable automated response to issues impacting the availability of Web Applications, as follows:

-   Provision an instance of the Event Notification Service in each region where the Web application is deployed.

-   Define and build event notifications by linking event sources and destinations. As an example, select event sources to detect cloud provider level (e.g., region, zone, services), network level (e.g. load balancers, global load balancers), security level and application level critical events and integrate them with destination targets. Select destinations such as ServiceNow to collect all events and assign owners and AIOps tool to automate response to events like file system is full.

-   Add IBM Cloud Monitoring as an event notification source to route operational monitoring and logging alert notifications as well as audit logging alert notifications for Web Apps. See [Add an Event Notification Source](https://cloud.ibm.com/docs/event-notifications?topic=event-notifications-en-add-source) for details.

-   Set service destinations such as Webhook and Code Engine for IBM Cloud Monitoring event sources to automate incident response and minimize down time for Web Apps.
