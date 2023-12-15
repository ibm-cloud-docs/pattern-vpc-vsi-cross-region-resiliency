---

copyright:
  years: 2023
lastupdated: "2023-12-13"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Networking design
{: #networking-design}

The web app cross-region resiliency pattern uses IBM Cloud Virtual Private Cloud infrastructure and network services to segment the application tiers and support the application deployment across multiple availability zones in two regions.

- Deploy the web app within a Virtual Private Cloud (VPC) provisioned across multiple availability zones within a region to provide workload isolation within the public cloud. Place each Web App tier in a separate subnet in each availability zone. Use security groups and ACLs as firewalls to limit access to virtual server instances for operational purposes and to control network traffic to each web app tier.

- Create an instance group for the VPC Virtual Server instances in the web and app tiers to enable auto-scaling by using a VPC Load Balancer.

- Use VPC Application Load Balancers (ALB) to distribute incoming requests among the virtual server instances within the web and app tiers. Configure load balancing policies, rules, and check health settings to distribute the requests across the available virtual servers. Integrate the VPC ALB with the Virtual Servers Instance Group for the tier to auto-scale this backend pool based on load requirements.

- Use a Public Application Load Balancer for the web tier in the front end. Use a Private Application Load Balancer for the application tier in the backend. Use the assigned FQDNs to send traffic to the ALBs to avoid connectivity problems.

- Configure the Application Load Balancers for high availability by selecting the subnets in each availability zone where the virtual servers are deployed. The ALB automatically provisions load balancing appliances for each subnet zone.

- Configure the Cloud Internet Service (CIS) as a proxy to the public VPC Load Balancers for the web tier to use CIS capabilities such as Web Application Firewall (WAF) and DDoS protection to secure the web application.

- Use the IBM Cloud Secrets Manager service to manage the Transport Layer Security (TLS) certificate for all incoming HTTPS requests. Make the SSL certificates available to the VPC Application Load Balancers to configure HTTPS encryption.

- Use a Global Transit Gateway to enable connectivity between VPCs in the two regions for data replication to the DR site.

- Use IBM Cloud Internet Services (CIS) Global Load Balancer to fail over to the DR region if an outage in the primary region occurs.

    - Connect the Global Load Balancer to the public VPC Load Balancers for the web tier and configure health checks to monitor the availability of the Web Application in each region.

    - Make the SSL certificates available to the CIS global load balancer to configure HTTPS encryption.

- Pre-configure all networking in the primary and DR sites before any disaster declaration.

    - Select nonoverlapping CIDR blocks for VPC subnets in all locations.

    - Since a Global Transit Gateway is used to connect the VPCs in the two regions, the Routes for CIDR blocks in one multizone region (MZR) are advertised to the other ones such that no routing changes are required in a disaster situation.

- Use hostnames and DNS instead of IP addresses to minimize the number of changes that are required to redeploy an application in the DR site. Use CIS to provision and configure DNS records for public DNS resolution. Use IBM Cloud DNS to manage DNS records and resolve domain names from IBM Cloud's private network.
