# Top 100 AWS Networking Interview Questions and Answers

**AWS Networking Interview Preparation | Beginner to Advanced | DevOps Engineer**

This README covers 100 AWS networking interview questions with practical, interview-ready answers for DevOps Engineers, Cloud Engineers, and Platform Engineers.

## Table of Contents

- [Section 1: AWS Networking Fundamentals (1–10)](#section-1-aws-networking-fundamentals-1-10)
- [Section 2: Subnets, Routing and Gateways (11–20)](#section-2-subnets-routing-and-gateways-11-20)
- [Section 3: Security Groups and Network ACLs (21–30)](#section-3-security-groups-and-network-acls-21-30)
- [Section 4: VPC Peering and Connectivity (31–40)](#section-4-vpc-peering-and-connectivity-31-40)
- [Section 5: Elastic Load Balancing and DNS (41–50)](#section-5-elastic-load-balancing-and-dns-41-50)
- [Section 6: VPC Endpoints and Private Connectivity (51–60)](#section-6-vpc-endpoints-and-private-connectivity-51-60)
- [Section 7: Advanced VPC Networking (61–70)](#section-7-advanced-vpc-networking-61-70)
- [Section 8: Hybrid Cloud and Network Security (71–80)](#section-8-hybrid-cloud-and-network-security-71-80)
- [Section 9: AWS Networking Troubleshooting Scenarios (81–90)](#section-9-aws-networking-troubleshooting-scenarios-81-90)
- [Section 10: AWS Networking with Docker, ECS and EKS (91–100)](#section-10-aws-networking-with-docker-ecs-and-eks-91-100)
- [Important AWS Networking Ports](#important-aws-networking-ports)
- [Quick Revision: Important AWS Networking Services](#quick-revision-important-aws-networking-services)
- [How to Prepare](#how-to-prepare-these-100-questions)

---

## Section 1: AWS Networking Fundamentals (1–10)

### Q1. What is Amazon VPC?

Amazon Virtual Private Cloud (VPC) is a logically isolated virtual network in AWS where we can launch AWS resources such as EC2 instances, RDS databases, and load balancers.

We can configure:

- IP address ranges (CIDR).
- Public and private subnets.
- Route tables.
- Internet Gateway.
- NAT Gateway.
- Security Groups and Network ACLs.

### Q2. What is a CIDR block in AWS?

CIDR (Classless Inter-Domain Routing) defines the range of IP addresses assigned to a VPC or subnet.

For example, `10.0.0.0/16` provides 65,536 total IPv4 addresses.

### Q3. What is the difference between a VPC and a subnet?

- **VPC:** The entire virtual network.
- **Subnet:** A smaller IP network range within a VPC.

A VPC can contain multiple subnets across different Availability Zones.

### Q4. What is a public subnet?

A public subnet is a subnet whose route table has a route to an Internet Gateway.

For an EC2 instance to communicate directly with the internet, it generally also needs a public IPv4 address and appropriate security rules.

### Q5. What is a private subnet?

A private subnet does not have a direct route to an Internet Gateway.

Resources in private subnets are commonly used for:

- Application servers.
- Database servers.
- Backend services.
- Kubernetes worker nodes.

### Q6. What is an Availability Zone (AZ)?

An Availability Zone is an isolated location within an AWS Region containing one or more data centers.

We use multiple AZs to improve application availability and fault tolerance.

### Q7. What is the difference between an AWS Region and an Availability Zone?

- **Region:** A geographical area where AWS provides services.
- **Availability Zone:** An isolated infrastructure location within a Region.

For example, Mumbai (`ap-south-1`) is an AWS Region with multiple Availability Zones.

### Q8. What is an Internet Gateway (IGW)?

An Internet Gateway allows communication between resources in a VPC and the internet.

It must be attached to the VPC, and the subnet route table must have a route to it.

### Q9. What is a route table in AWS?

A route table contains rules that determine where network traffic is directed.

For example:

```text
Destination       Target
10.0.0.0/16       local
0.0.0.0/0         igw-xxxxxxxx
```

The first route allows communication within the VPC, while the second sends IPv4 internet-bound traffic through the Internet Gateway.

### Q10. What is the difference between a default VPC and a custom VPC?

- **Default VPC:** Automatically created by AWS in each Region, where supported.
- **Custom VPC:** Created by the user to meet specific networking requirements.

Custom VPCs provide greater control over CIDR ranges, subnets, routing, and security.

## Section 2: Subnets, Routing and Gateways (11–20)

### Q11. Can a VPC have multiple public and private subnets?

Yes. A VPC can have multiple public and private subnets across multiple Availability Zones.

For example:

```text
VPC: 10.0.0.0/16

AZ-1                  AZ-2
Public Subnet         Public Subnet
10.0.1.0/24           10.0.2.0/24

Private Subnet        Private Subnet
10.0.11.0/24          10.0.12.0/24
```

### Q12. What is a NAT Gateway?

A NAT Gateway allows resources in a private subnet to initiate connections to the internet or other external networks without allowing unsolicited inbound internet connections.

For example, a private EC2 instance can download software updates through a NAT Gateway.

### Q13. Where should we create a NAT Gateway?

A public NAT Gateway should be created in a public subnet and associated with an Elastic IP address.

The public subnet must have a route to the Internet Gateway.

### Q14. What is the difference between an Internet Gateway and a NAT Gateway?

| Internet Gateway | NAT Gateway |
|---|---|
| Enables internet connectivity for VPC resources | Enables outbound connectivity for private resources |
| Attached to a VPC | Created in a subnet |
| Supports inbound and outbound communication when routing and security allow | Does not allow unsolicited inbound connections |
| Does not require an Elastic IP | Public NAT Gateway uses an Elastic IP |

### Q15. Can a private subnet communicate with the internet?

Yes. A private subnet can access the internet through a NAT Gateway.

Its route table should contain:

```text
0.0.0.0/0 → NAT Gateway
```

### Q16. What happens if a private subnet has no NAT Gateway?

Resources in that subnet cannot access the public internet through a NAT Gateway.

However, they may still communicate through VPC endpoints, VPN connections, Transit Gateway, or other configured network paths.

### Q17. What is an Elastic IP address?

An Elastic IP is a static public IPv4 address that can be allocated to your AWS account and associated with supported resources such as EC2 instances or NAT Gateways.

### Q18. What is the difference between a public IP and an Elastic IP?

- **Public IP:** An automatically assigned public IPv4 address that generally changes when an EC2 instance is stopped and started.
- **Elastic IP:** A static public IPv4 address that remains allocated to your account until you release it.

### Q19. What is the local route in a VPC route table?

The local route allows communication between resources within the VPC CIDR range.

For example:

```text
Destination       Target
10.0.0.0/16       local
```

### Q20. Can two subnets in the same VPC communicate with each other?

Yes. By default, the VPC local route allows communication between subnets.

However, Security Groups, Network ACLs, and other network configurations must also allow the traffic.

## Section 3: Security Groups and Network ACLs (21–30)

### Q21. What is a Security Group in AWS?

A Security Group is a virtual firewall that controls inbound and outbound traffic for resources such as EC2 instances, RDS, and network interfaces.

We can configure rules based on:

- Protocol (TCP, UDP, ICMP).
- Port numbers.
- Source or destination IP ranges.
- Other Security Groups.

### Q22. What is a Network ACL (NACL)?

A Network ACL is a firewall that controls inbound and outbound traffic at the subnet level.

It supports both allow and deny rules.

### Q23. What is the difference between Security Groups and NACLs?

| Security Group | Network ACL |
|---|---|
| Works at resource/ENI level | Works at subnet level |
| Stateful | Stateless |
| Supports allow rules only | Supports allow and deny rules |
| Return traffic is automatically allowed | Return traffic must be explicitly allowed |
| Evaluates all applicable rules | Evaluates rules in numerical order |

### Q24. What does stateful and stateless mean?

- **Stateful:** If inbound traffic is allowed, the response traffic is automatically allowed.
- **Stateless:** Inbound and outbound traffic are evaluated separately.

Security Groups are stateful, while NACLs are stateless.

### Q25. Can we attach multiple Security Groups to one EC2 instance?

Yes. We can associate multiple Security Groups with an EC2 instance's network interface.

The effective rules are combined. Traffic is allowed if any applicable Security Group rule allows it.

### Q26. Can we deny traffic using a Security Group?

No. Security Groups support allow rules only.

To explicitly deny traffic at the subnet level, we can use Network ACL deny rules.

### Q27. What is an ephemeral port?

An ephemeral port is a temporary port used by a client for outgoing connections.

For example, when an EC2 instance connects to a web server on port 443, it uses a temporary source port for that connection.

Ephemeral port ranges depend on the operating system and application.

### Q28. What is the default behavior of a Security Group?

A newly created Security Group has:

- No inbound rules by default.
- An outbound rule allowing all outbound IPv4 traffic by default.

The default outbound rule can be modified or removed.

### Q29. What happens if a Security Group allows port 22 but the NACL blocks it?

The connection will fail because both Security Group and NACL rules must permit the traffic.

### Q30. How do you troubleshoot an EC2 instance that is not accessible through SSH?

I would check:

1. Whether the EC2 instance is running.
2. Whether it has a public IP or Elastic IP if accessing over the internet.
3. Whether the subnet route table has a route to the Internet Gateway.
4. Whether the Security Group allows inbound TCP port 22 from my IP.
5. Whether the NACL allows SSH traffic and return ephemeral traffic.
6. Whether the operating system firewall allows SSH.
7. Whether the SSH service is running.

## Section 4: VPC Peering and Connectivity (31–40)

### Q31. What is VPC Peering?

VPC Peering is a networking connection between two VPCs that allows resources in those VPCs to communicate using private IP addresses.

### Q32. Can we establish VPC Peering between VPCs in different AWS accounts?

Yes. We can establish VPC Peering between VPCs belonging to different AWS accounts.

We need the accepter VPC owner to accept the peering request.

### Q33. Can we establish VPC Peering between different AWS Regions?

Yes. VPC Peering supports connections between VPCs in different AWS Regions.

### Q34. Can two VPCs with overlapping CIDR blocks be peered?

No. VPC Peering does not support overlapping IPv4 CIDR blocks.

### Q35. Is VPC Peering transitive?

No. VPC Peering is non-transitive.

For example:

```text
VPC-A ---- VPC-B ---- VPC-C
```

VPC-A cannot communicate with VPC-C through VPC-B using those peering connections.

### Q36. What is AWS Transit Gateway?

AWS Transit Gateway is a network hub that connects multiple VPCs and on-premises networks.

Instead of creating individual peering connections between every VPC, we can connect them through a central Transit Gateway.

### Q37. What is the difference between VPC Peering and Transit Gateway?

| VPC Peering | Transit Gateway |
|---|---|
| Connects two VPCs | Connects multiple VPCs and networks |
| Non-transitive | Supports transitive routing |
| Point-to-point connectivity | Centralized hub-and-spoke architecture |
| No hourly attachment charge | Has attachment and data processing costs |
| Suitable for simpler connectivity | Suitable for large network architectures |

### Q38. What is AWS Site-to-Site VPN?

AWS Site-to-Site VPN creates an encrypted connection between an on-premises network and an AWS VPC over the internet.

It typically uses IPsec tunnels.

### Q39. What is AWS Direct Connect?

AWS Direct Connect provides a dedicated network connection between an on-premises data center and AWS.

It offers more consistent network performance and avoids sending traffic over the public internet.

### Q40. What is the difference between Site-to-Site VPN and Direct Connect?

| Site-to-Site VPN | Direct Connect |
|---|---|
| Uses internet connectivity | Uses a dedicated network connection |
| Encrypted IPsec tunnels | Private connectivity; encryption is not automatic |
| Can be set up relatively quickly | Requires connectivity provider and provisioning |
| Suitable for many smaller or backup connections | Suitable for consistent, high-volume connectivity |

## Section 5: Elastic Load Balancing and DNS (41–50)

### Q41. What is Elastic Load Balancing (ELB)?

Elastic Load Balancing automatically distributes incoming application traffic across multiple targets, such as EC2 instances, containers, and IP addresses.

It improves availability and scalability.

### Q42. What are the different types of load balancers in AWS?

AWS provides four main types:

1. Application Load Balancer (ALB).
2. Network Load Balancer (NLB).
3. Gateway Load Balancer (GWLB).
4. Classic Load Balancer (CLB), which is a previous-generation option.

### Q43. What is an Application Load Balancer (ALB)?

ALB operates at Layer 7 (Application Layer) and supports HTTP and HTTPS traffic.

It supports:

- Path-based routing.
- Host-based routing.
- HTTP header-based routing.
- Content-based routing.

### Q44. What is a Network Load Balancer (NLB)?

NLB operates at Layer 4 (Transport Layer) and supports TCP, UDP, TLS, and QUIC traffic.

It is designed for high-performance, low-latency networking and can handle millions of requests per second.

### Q45. What is the difference between ALB and NLB?

| ALB | NLB |
|---|---|
| Layer 7 | Layer 4 |
| HTTP/HTTPS | TCP/UDP/TLS/QUIC |
| Path-based routing | Connection-level routing |
| Host-based routing | No HTTP host-based routing |
| Suitable for web applications | Suitable for high-performance network traffic |

### Q46. What is a Target Group in AWS?

A Target Group is a collection of registered targets that receive traffic from a load balancer.

Targets can include:

- EC2 instances.
- IP addresses.
- Lambda functions (for supported ALB configurations).

### Q47. What is a health check in a Load Balancer?

A health check determines whether registered targets are healthy and ready to receive traffic.

For example, an ALB can periodically send HTTP requests to an application's `/health` endpoint.

### Q48. What happens if all targets in a Target Group become unhealthy?

The load balancer can enter a fail-open behavior and route requests to registered targets regardless of their health status, depending on the load balancer type and configuration.

### Q49. What is Amazon Route 53?

Amazon Route 53 is a highly available and scalable DNS web service.

It provides:

- Domain registration.
- DNS routing.
- Health checks.
- DNS failover.

### Q50. What are the different routing policies in Route 53?

Important routing policies include:

1. Simple Routing.
2. Weighted Routing.
3. Latency-Based Routing.
4. Failover Routing.
5. Geolocation Routing.
6. Geoproximity Routing.
7. IP-Based Routing.
8. Multivalue Answer Routing.

## Section 6: VPC Endpoints and Private Connectivity (51–60)

### Q51. What is a VPC Endpoint?

A VPC Endpoint allows resources in a VPC to privately connect to supported AWS services without requiring an Internet Gateway, NAT Gateway, or public IP address for that service connection.

### Q52. What are the types of VPC Endpoints?

There are two main categories:

1. Gateway Endpoints.
2. Interface Endpoints.

### Q53. What is a Gateway Endpoint?

A Gateway Endpoint provides private connectivity to Amazon S3 and DynamoDB.

It uses route table entries to direct traffic to the endpoint.

### Q54. What is an Interface Endpoint?

An Interface Endpoint uses AWS PrivateLink and creates an elastic network interface (ENI) with a private IP address in your subnet.

It supports private connectivity to many AWS services and supported endpoint services.

### Q55. What is the difference between Gateway and Interface Endpoints?

| Gateway Endpoint | Interface Endpoint |
|---|---|
| Supports S3 and DynamoDB | Supports many AWS services |
| Uses route table entries | Uses ENIs |
| No additional endpoint charge | Generally has hourly and data processing charges |
| Does not use AWS PrivateLink | Uses AWS PrivateLink |

### Q56. What is AWS PrivateLink?

AWS PrivateLink provides private connectivity between VPCs and supported services without exposing traffic to the public internet.

It is commonly used to access services hosted in another VPC or AWS account.

### Q57. Can a private EC2 instance access S3 without a NAT Gateway?

Yes. We can create an S3 Gateway Endpoint and associate it with the private subnet's route table.

This allows private connectivity to S3 without requiring a NAT Gateway.

### Q58. How do you access AWS Systems Manager from a private EC2 instance without internet access?

We can create the required Systems Manager Interface VPC Endpoints.

Common endpoints include:

- `ssm`
- `ssmmessages`
- `ec2messages` (where applicable)

We also need appropriate IAM permissions, Security Group rules, DNS settings, and a suitable SSM Agent version.

### Q59. What is a VPC Endpoint Policy?

A VPC Endpoint Policy is a resource-based policy attached to a VPC endpoint that controls which AWS principals and resources can use that endpoint.

It does not replace IAM policies or service resource policies.

### Q60. What is Private DNS in an Interface Endpoint?

Private DNS allows AWS service DNS names to resolve to private IP addresses associated with the Interface Endpoint.

This helps applications access AWS services privately without changing their service endpoint URLs.

## Section 7: Advanced VPC Networking (61–70)

### Q61. What is a Network Interface (ENI) in AWS?

An Elastic Network Interface (ENI) is a virtual network interface that can be attached to an EC2 instance.

It can have:

- Private IPv4 addresses.
- Public IPv4 addresses (associated with private IPv4 addresses).
- Security Groups.
- MAC address.
- Elastic IP association.

### Q62. What is the difference between a primary and secondary ENI?

- **Primary ENI:** The main network interface attached to an EC2 instance.
- **Secondary ENI:** An additional network interface that can be attached to an instance.

Secondary ENIs are useful for network separation and managing multiple network connections.

### Q63. What is VPC Flow Logs?

VPC Flow Logs capture information about IP traffic going to and from network interfaces in a VPC.

They help us troubleshoot connectivity issues and analyze network traffic.

### Q64. Where can we publish VPC Flow Logs?

VPC Flow Logs can be published to:

- Amazon CloudWatch Logs.
- Amazon S3.
- Amazon Data Firehose.

### Q65. What is a DHCP Option Set in AWS?

A DHCP Option Set provides network configuration information to instances in a VPC.

It can configure:

- Domain name.
- DNS servers.
- NTP servers.
- NetBIOS settings.

### Q66. What is a NAT Instance?

A NAT Instance is an EC2 instance configured to perform Network Address Translation for private subnet resources.

It can provide outbound internet connectivity to private instances.

However, NAT Instances require manual management, scaling, and maintenance compared with NAT Gateways.

### Q67. What is the difference between NAT Instance and NAT Gateway?

| NAT Instance | NAT Gateway |
|---|---|
| Runs on EC2 | Managed AWS service |
| Requires OS maintenance | AWS manages infrastructure |
| Supports Security Groups | Does not support Security Groups |
| Requires manual scaling and management | Automatically scales up to its documented capacity |
| Can be customized | Less customizable |

### Q68. What is an Egress-Only Internet Gateway?

An Egress-Only Internet Gateway allows IPv6 resources in a VPC to initiate outbound communication with the internet while preventing unsolicited inbound connections.

It is the IPv6 equivalent of the outbound-only functionality provided by a NAT Gateway for IPv4.

### Q69. What is the difference between IPv4 and IPv6 in AWS networking?

| IPv4 | IPv6 |
|---|---|
| 32-bit address | 128-bit address |
| Example: `10.0.1.10` | Example: `2001:db8::10` |
| Private IPv4 ranges are commonly used internally | IPv6 uses globally unique address ranges |
| NAT Gateway commonly provides outbound internet access | Egress-Only Internet Gateway can provide outbound-only internet access |

### Q70. What is a secondary CIDR block in a VPC?

A secondary CIDR block is an additional IP address range that we associate with an existing VPC.

It helps expand the available IP address space when the original CIDR range is insufficient.

## Section 8: Hybrid Cloud and Network Security (71–80)

### Q71. What is a Customer Gateway in AWS?

A Customer Gateway represents the on-premises VPN device or software endpoint in an AWS Site-to-Site VPN configuration.

It is an AWS resource that contains information about the customer-side gateway.

### Q72. What is a Virtual Private Gateway (VGW)?

A Virtual Private Gateway is an AWS-side VPN endpoint that can be attached to a VPC to establish Site-to-Site VPN connectivity.

### Q73. What is the difference between Virtual Private Gateway and Transit Gateway?

- **Virtual Private Gateway:** Generally used to connect a VPC to an on-premises network.
- **Transit Gateway:** Acts as a central hub to connect multiple VPCs and on-premises networks.

### Q74. What is BGP in AWS networking?

BGP (Border Gateway Protocol) is a routing protocol used to exchange routing information between networks.

It is commonly used with AWS Direct Connect and dynamically routed Site-to-Site VPN connections.

### Q75. What is AWS Network Firewall?

AWS Network Firewall is a managed network security service that helps protect VPC traffic using firewall rules.

It supports traffic filtering, stateful inspection, and intrusion prevention capabilities.

### Q76. What is AWS WAF?

AWS Web Application Firewall (WAF) protects web applications against common web exploits and malicious HTTP/HTTPS requests.

For example, it can help protect applications against:

- SQL injection.
- Cross-site scripting (XSS).
- Malicious IP addresses.
- Unwanted bot traffic.

### Q77. What is AWS Shield?

AWS Shield is a managed Distributed Denial of Service (DDoS) protection service.

There are two main levels:

- AWS Shield Standard.
- AWS Shield Advanced.

### Q78. What is the difference between AWS WAF and AWS Shield?

| AWS WAF | AWS Shield |
|---|---|
| Protects against web application attacks | Protects against DDoS attacks |
| Uses web ACLs and rules | Provides DDoS detection and mitigation |
| Filters HTTP/HTTPS requests | Protects against network and transport layer attacks, among others |
| Can be associated with supported resources such as ALB and CloudFront | Integrates with supported AWS services |

### Q79. What is AWS Network Access Analyzer?

AWS Network Access Analyzer helps identify unintended network access paths in AWS environments.

It can analyze network configurations and identify paths that may violate defined network access requirements.

### Q80. What is AWS Reachability Analyzer?

AWS Reachability Analyzer is a network diagnostic tool that helps determine whether a network path exists between a source and destination.

For example, we can check whether an EC2 instance can reach another EC2 instance on TCP port 443.

It identifies configuration issues such as route tables, Security Groups, and NACL rules.

## Section 9: AWS Networking Troubleshooting Scenarios (81–90)

### Q81. An EC2 instance in a public subnet cannot access the internet. How do you troubleshoot?

I would check:

1. Whether the instance has a public IPv4 address or Elastic IP.
2. Whether the Internet Gateway is attached to the VPC.
3. Whether the route table has `0.0.0.0/0` pointing to the Internet Gateway.
4. Whether the Security Group allows outbound traffic.
5. Whether the NACL allows inbound and outbound traffic.
6. Whether the operating system firewall is blocking traffic.

### Q82. An EC2 instance in a private subnet cannot download software updates. What could be the issue?

Possible reasons:

- NAT Gateway is missing.
- NAT Gateway is not in a public subnet.
- NAT Gateway has no Elastic IP.
- Public subnet has no route to the Internet Gateway.
- Private subnet route table does not point to the NAT Gateway.
- Security Group or NACL blocks the traffic.
- DNS resolution is not working.

### Q83. Two EC2 instances in different subnets cannot communicate. How do you troubleshoot?

I would check:

1. Whether both instances belong to the same VPC or have a valid inter-VPC connection.
2. Whether the route tables have valid routes.
3. Whether Security Groups allow the required traffic.
4. Whether NACLs allow inbound and outbound traffic.
5. Whether the operating system firewall allows communication.
6. Whether the application is listening on the required port.

### Q84. An EC2 instance is accessible using its private IP but not its public IP. What could be wrong?

I would check:

- Whether the instance has a public IP or Elastic IP.
- Whether the Internet Gateway is attached.
- Whether the subnet route table has a route to the Internet Gateway.
- Whether inbound Security Group rules allow the required port.
- Whether NACL rules allow the traffic.
- Whether the OS firewall and application are configured correctly.

### Q85. A website hosted on EC2 is not accessible through an ALB. How do you troubleshoot?

I would check:

1. Whether the ALB is active.
2. Whether the ALB Security Group allows inbound traffic on ports 80 or 443.
3. Whether the EC2 Security Group allows traffic from the ALB Security Group.
4. Whether the targets are registered correctly.
5. Whether health checks are passing.
6. Whether the application is running and listening on the correct port.
7. Whether listener rules are configured correctly.

### Q86. An ALB Target Group shows unhealthy targets. What could be the reason?

Possible reasons:

- Application is not running.
- Health check path is incorrect.
- Health check port is incorrect.
- EC2 Security Group blocks traffic from the ALB.
- Application returns an unexpected HTTP status code.
- Network ACL blocks communication.
- Application takes too long to respond.

### Q87. Two VPCs are connected using VPC Peering, but communication is not working. What would you check?

I would check:

1. Whether the peering connection is active.
2. Whether both VPC CIDR ranges are non-overlapping.
3. Whether route tables contain routes to the peer VPC.
4. Whether Security Groups allow the required traffic.
5. Whether NACLs allow the traffic.
6. Whether DNS resolution settings are configured if DNS access is required.

### Q88. A private EC2 instance cannot access an S3 bucket. What could be the issue?

I would check:

- Whether an S3 Gateway Endpoint exists.
- Whether the private subnet's route table is associated with the endpoint.
- Whether the endpoint policy allows access.
- Whether the EC2 instance has the required IAM permissions.
- Whether the bucket policy allows access.
- Whether DNS and application configurations are correct.

### Q89. An EC2 instance cannot resolve domain names. How do you troubleshoot?

I would check:

1. VPC DNS resolution settings.
2. VPC DNS hostnames settings, where applicable.
3. DHCP Option Set configuration.
4. DNS server configuration inside the operating system.
5. Network connectivity to the configured DNS resolver.
6. Whether the required DNS records exist.

### Q90. How do you identify why network traffic is being blocked in a VPC?

I would use:

- **VPC Flow Logs:** Inspect accepted and rejected traffic.
- **Reachability Analyzer:** Identify network path problems.
- **Security Groups:** Verify allowed traffic.
- **Network ACLs:** Check subnet-level filtering.
- **Route tables:** Verify traffic destinations.
- **CloudWatch Logs:** Analyze Flow Logs when they are published there.

## Section 10: AWS Networking with Docker, ECS and EKS (91–100)

### Q91. How does AWS networking work in Amazon EKS?

Amazon EKS uses the Amazon VPC CNI plugin by default to provide networking for Kubernetes Pods.

The VPC CNI plugin assigns VPC IP addresses to Pods, allowing them to communicate within the VPC and with other connected networks.

### Q92. What is the Amazon VPC CNI plugin?

The Amazon VPC CNI plugin is a Kubernetes networking plugin that provides native VPC networking for Kubernetes Pods.

It:

- Assigns VPC IP addresses to Pods.
- Creates and manages network interfaces.
- Enables Pod-to-Pod communication.
- Supports communication with other VPC resources.

### Q93. What is the difference between public and private EKS clusters?

- **Public cluster endpoint:** The Kubernetes API server endpoint is accessible over the internet, subject to endpoint access restrictions.
- **Private cluster endpoint:** The Kubernetes API server endpoint is accessible through the cluster VPC.
- **Both:** Supports public and private endpoint access simultaneously.

For production environments, private endpoint access is commonly preferred for additional security.

### Q94. How does a Pod communicate with the internet in EKS?

In a typical EKS private subnet architecture:

1. A Pod receives a VPC IP address through the VPC CNI plugin.
2. The Pod sends outbound traffic.
3. The private subnet route table forwards internet-bound traffic to a NAT Gateway.
4. The NAT Gateway forwards traffic through the Internet Gateway.

### Q95. What is the difference between AWS VPC CNI and Calico?

- **AWS VPC CNI:** Provides native VPC networking and assigns VPC IP addresses to Pods.
- **Calico:** Commonly used for Kubernetes network policies and can also provide networking capabilities in supported configurations.

They can be used together in supported EKS configurations to provide networking and network policy enforcement.

### Q96. What is the difference between ECS networking modes?

Amazon ECS supports different networking modes:

1. `bridge`: Uses Docker's bridge networking.
2. `host`: Shares the host network namespace.
3. `awsvpc`: Gives each task its own elastic network interface or branch ENI, depending on configuration.
4. `none`: Disables external networking for the container.

### Q97. What is the awsvpc networking mode in ECS?

The `awsvpc` networking mode assigns each ECS task its own network interface and private IP address.

Benefits:

- Task-level Security Groups.
- Better network isolation.
- Direct VPC connectivity.
- Simplified networking for microservices.

### Q98. What is the difference between ECS and EKS networking?

| ECS | EKS |
|---|---|
| AWS container orchestration service | Managed Kubernetes service |
| Supports bridge, host and awsvpc networking modes | Uses Kubernetes networking and CNI plugins |
| awsvpc gives tasks their own network interfaces | VPC CNI commonly assigns VPC IPs to Pods |
| Uses ECS Services and Tasks | Uses Kubernetes Services and Pods |

### Q99. How do you expose an application running in EKS to the internet?

We can expose an application using:

1. LoadBalancer Service.
2. Kubernetes Ingress with AWS Load Balancer Controller.
3. Application Load Balancer (ALB).
4. Network Load Balancer (NLB).

For example, AWS Load Balancer Controller can provision an ALB for Kubernetes Ingress resources.

### Q100. How would you design a secure and highly available AWS VPC architecture for a production application?

I would design the architecture using multiple Availability Zones.

My approach would be:

1. Create a VPC with an appropriate CIDR range.
2. Create public and private subnets across at least two Availability Zones.
3. Place an Application Load Balancer in public subnets.
4. Deploy application servers or EKS worker nodes in private subnets.
5. Deploy databases in private subnets.
6. Configure NAT Gateways for private subnet outbound internet connectivity, considering AZ-level resilience.
7. Use Security Groups to control communication between application tiers.
8. Use Network ACLs for additional subnet-level traffic control where required.
9. Use VPC Endpoints for private access to supported AWS services.
10. Enable VPC Flow Logs for network monitoring and troubleshooting.
11. Use Route 53 for DNS management.
12. Use AWS WAF and Shield for appropriate application and DDoS protection.

This architecture improves security, availability, scalability, and network isolation.

---

## Important AWS Networking Ports

These port numbers are especially useful for DevOps and Cloud Engineer interviews.

| Port | Protocol | Purpose |
|---|---|---|
| 22 | TCP | SSH |
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP |
| 123 | UDP | NTP |
| 443 | TCP | HTTPS |
| 3306 | TCP | MySQL |
| 5432 | TCP | PostgreSQL |
| 6379 | TCP | Redis |
| 8080 | TCP | Common application port |
| 9090 | TCP | Common Prometheus port |
| 3000 | TCP | Common Grafana port |
| 6443 | TCP | Kubernetes API server |

## Quick Revision: Important AWS Networking Services

| AWS Service | Purpose |
|---|---|
| VPC | Virtual private network |
| Internet Gateway | Internet connectivity |
| NAT Gateway | Outbound internet access for private resources |
| Transit Gateway | Connect multiple networks |
| Route 53 | DNS management |
| Elastic Load Balancing | Load balancing |
| Direct Connect | Dedicated network connectivity |
| VPC Endpoint | Private connectivity to AWS services |
| Network Firewall | Network traffic protection |
| VPC Flow Logs | Network traffic monitoring |

## How to Prepare These 100 Questions

For DevOps Engineer interviews, prioritize these topics:

| Priority | Topics |
|---|---|
| Very high | VPC, CIDR, public/private subnets |
| Very high | Route tables, IGW, NAT Gateway |
| Very high | Security Groups vs NACL |
| Very high | ALB, NLB, Target Groups |
| Very high | VPC Peering and Transit Gateway |
| High | VPC Endpoints and PrivateLink |
| High | Route 53 and DNS |
| High | VPN and Direct Connect |
| High | VPC Flow Logs and troubleshooting |
| High | EKS and ECS networking |

### Interview Tip

Don't just memorize definitions. Practice explaining:

1. How an EC2 instance in a private subnet accesses the internet.
2. How an ALB communicates with EC2 instances.
3. How two VPCs communicate.
4. How a private EC2 instance accesses S3 without a NAT Gateway.
5. How Pods communicate with other services in EKS.

These scenarios help demonstrate practical networking knowledge in DevOps interviews.

---

**End of AWS Networking Interview Questions and Answers.**
