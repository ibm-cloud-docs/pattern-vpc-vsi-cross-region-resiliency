---

copyright:
  years: 2023
lastupdated: "2023-12-12"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# Service management design
{: #service-management-design}

Operations Management is a key aspect of building resilient applications. To support the application availability targets you must:

-   Continuously monitor the application and platform infrastructure to detect failures and degradations.

-   Integrate automated monitoring with rich notification tooling to automate problem resolution and enable a timely response to incidents.

It is important to monitor the health of all the components of the solution, including infrastructure, cloud services, and application as well as operational logs to detect and correct issues that might affect the availability of enterprise applications. Proper operational monitoring can help you determine whether you need to fail over to an alternate site or whether operations have returned to normal after a system disruption. Equally important is to track and monitor all activity performed on the IBM Cloud to detect changes and potential security threats that might impact the availability of the applications deployed on IBM Cloud.

Use [IBM Cloud Monitoring](https://cloud.ibm.com/docs/monitoring?topic=monitoring-about-monitor) and [IBM Log Analysis](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-getting-started) to monitor operational metrics and logs. Use [IBM Cloud Activity Tracker](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started) to monitor audit logs for the application, IBM Cloud Infrastructure, and other IBM Cloud services. Configure IBM Cloud Monitoring, IBM Logging, and Activity Tracker to set up alerts, send notifications and trigger actions in response to the alerts.

Incident detection, notification, escalation, discovery, and declaration must be considered to provide realistic, achievable objectives that provide business value. Use [IBM Cloud Event Notifications](https://cloud.ibm.com/docs/event-notifications?topic=event-notifications-en-about) to route events associated with IBM Cloud resources (event sources) to a destination (delivery target for a notification) to trigger actions and help automate the response to critical incidents. Define and build event notifications by linking event sources and destinations. As an example, select event sources to detect cloud provider level (e.g., region, zone, services), network level (e.g. load balancers, global load balancers), security level and application level critical events and integrate them with destination targets. Select destinations such as ServiceNow to collect all events and assign owners and AIOps tool to automate response to events like file system is full.
