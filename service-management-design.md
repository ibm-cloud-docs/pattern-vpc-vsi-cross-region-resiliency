---

copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Service management design
{: #service-management-design}

The web app cross-region resiliency pattern uses IBM Cloud services to monitor the health of all the components of the solution, including infrastructure, cloud services, and application as well as operational logs, to detect and correct issues that might affect the availability of the Web Application.

## Monitoring design
{: #smonitoring-design}

Use IBM Cloud Monitoring to get a comprehensive view of the health of the Web App and cloud environment and enable a timely response to incidents, as follows:
- Provision one IBM Cloud Monitoring instance in each region where the application is deployed to collect and monitor platform metrics for Cloud Services that are provisioned in that region. See [Configuring Platform Metrics](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started#getting-started-step3-1) for details.

- Install and configure monitoring agents on each of the VPC Virtual Servers that are used to deploy the web, app, and database tiers. The monitoring agent collects the following metrics by default: system host, file, file system, process, and network metrics. See [Monitoring Infrastructure](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started#getting-started-step3-2) for details. IBM Cloud Monitoring also supports monitoring of application platform metrics through Prometheus exporters or custom metrics. See [Monitoring Features](https://cloud.ibm.com/docs/monitoring?topic=monitoring-features) for more details.

- Use [IBM Instana](https://www.ibm.com/docs/en/instana-observability/current?topic=overview) to get additional application performance metrics and automate application performance management for the Web, App, and Database tiers. IBM Instana provides data and actionable insights to monitor the applications and automate root-cause analysis.

- Use the Monitoring dashboard to view, aggregate, and analyze performance metrics.

- Define alerts for the conditions that you need to monitor and configure notification channels to trigger the appropriate actions and automate incident responses. For example, you can configure a webhook notification to integrate IBM Cloud Monitoring with Service Now.

- Configure notification channels to inform operation and support teams when an alert is triggered to support incident response processes. \n
    Consider configuring streaming to forward selected metrics for further data processing, analysis or to trigger an automated response based on specific values. See [Streaming Data](https://cloud.ibm.com/docs/monitoring?topic=monitoring-data_streaming#data_streaming_ui) for details.
    {: note}

- Backup historical metrics that might be needed for auditing purposes by querying and copying the data to cross-regional Cloud Object Storage buckets that can be accessed from another region if a disaster occurs.

## Auditing and Logging design
{: #logging-design}

Use [IBM Cloud Logs](/docs/cloud-logs?topic=cloud-logs-about-cl) to monitor auditing and operational logs for applications, platform resources, and infrastructure.  IBM Cloud Logs provides the following capabilites:

- Customizable user interface for live streaming of log tailing, real-time troubleshooting issue alerts, and log archiving.
- Use of logging agent to collect and send infrastructure and application logs to an IBM Cloud Logs instance directly.
- Aggregated logs across environments and cloud providers.
- Historical access to logs.
- Highly available, scalable, and compliant with industry security standards.
- Integrated with IBM Cloud IAM for user access management.

See [Getting started with IBM Cloud Logs](docs/cloud-logs?topic=cloud-logs-getting-started) on provisioning and setting up IBM Cloud Logs for both auditing and application logging.

## Alerting design
{: #alerting-design}

It is important to factor in incident detection, notification, escalation, discovery, and declaration to provide realistic, achievable objectives that provide business value. Use [IBM Cloud Event Notifications](https://cloud.ibm.com/docs/event-notifications?topic=event-notifications-en-about) to route events associated with IBM Cloud resources (event sources) to a destination (delivery target for a notification) to trigger actions and enable automated response to issues impacting the availability of Web Applications, as follows:
- Provision an instance of the Event Notification Service in each region where the Web application is deployed.

- Define and build event notifications by linking event sources and destinations. As an example, select event sources to detect cloud provider level (for example, region, zone, services), network level (for example load balancers, global load balancers), security level and application level critical events and integrate them with destination targets. Select destinations such as ServiceNow to collect all events and assign owners and AIOps tool to automate response to events like the file system is full.

- Add IBM Cloud Monitoring as an event notification source to route operational monitoring and logging alert notifications as well as audit logging alert notifications for Web Apps. See [Add an Event Notification Source](https://cloud.ibm.com/docs/event-notifications?topic=event-notifications-en-add-source) for details.

- Set service destinations such as webhook and Code Engine for IBM Cloud Monitoring event sources to automate incident response and minimize downtime for web apps.
