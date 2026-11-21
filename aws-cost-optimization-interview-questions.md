# AWS Cost Optimization -- Interview Questions & Answers


## Table of Contents

- [1. Basic AWS Cost Questions](#-Basic-AWS-Cost-Questions)
- [2. Practical DevOps Interview Questions](#-Practical-DevOps-Interview-Questions)
- [3. Kubernetes / EKS Cost Optimization](#-EKS-Cost-Optimization)
- [4. Scenario-Based Interview Questions](#-Scenario-Based-Interview-Questions)
- [5. Real-world Interview Questions](#-Real-world-Interview-Question)
- [6. Real-World Use Case](#-Real-World-Use-Case)

---

## 🟠 Basic AWS Cost Questions

### 1. What is AWS Cost Optimization?

**Answer:** AWS Cost Optimization means reducing unnecessary AWS expenses while
maintaining performance, availability, and reliability.

**For example:**

-   Identifying unused EC2 instances.
-   Right-sizing EC2 instances.
-   Deleting unattached EBS volumes.
-   Using Auto Scaling.
-   Choosing the right pricing model.

### 2. Which AWS services do you use to monitor AWS costs?

**Answer:** **I use:**

-   **AWS Cost Explorer:** Analyze and visualize AWS spending.
-   **AWS Budgets:** Set budgets and receive cost alerts.
-   **AWS Cost and Usage Reports (CUR):** Get detailed billing
    information.
-   **AWS CloudWatch:** Monitor resource utilization.
-   **AWS Trusted Advisor:** Get cost optimization recommendations.

### 3. How do you identify which AWS service is costing more?

**Answer:** I use AWS Cost Explorer to analyze spending by service, region, usage
type, and linked account.

**For example:** if EC2 costs are increasing, I investigate instance types,
running hours, and resource utilization.

### 4. What is AWS Cost Explorer?

**Answer:** AWS Cost Explorer is a tool that helps us visualize, analyze, and
understand AWS spending over time.

We can identify cost trends, compare monthly expenses, and find services
contributing to higher costs.

### 5. What is AWS Budgets?

**Answer:** AWS Budgets allows us to set custom cost or usage budgets.

**For example:**, if our monthly AWS budget is \$500, we can configure alerts
when spending reaches 80% or 100%.

### 6. What is AWS Compute Optimizer?

**Answer:** It analyses historical usage of EC2, RDS, Lambda, and Auto Scaling groups and provides resizing or resource recommendations to optimize cost.

### 7. How do you monitor AWS spending?

**Answer:** Use CloudWatch for performance metrics, Cost Explorer to analyse trends, and AWS Budgets to set alerts for unexpected spend.

---

## 🟠 Practical DevOps Interview Questions

### 8.  How do you determine if an EC2 instance is over-provisioned?

**Answer:** Monitor CPU, memory, and network utilization using CloudWatch. If an instance consistently uses <30% CPU and memory, it may be oversized.

### 9. How do you reduce EC2 costs?

**Answer:** I follow these steps:

1.  Identify underutilized EC2 instances.
2.  Right-size instances based on CPU and memory utilization.
3.  Stop non-production instances after working hours.
4.  Use Auto Scaling to handle workload changes.
5.  Use Spot Instances for suitable fault-tolerant workloads.
6.  Purchase Savings Plans or Reserved Instances for predictable
    workloads.

### 10. What is EC2 Right Sizing?

**Answer:** Right-sizing means selecting an appropriate EC2 instance type based on actual resource utilization.

**For example:** if an instance has 2 vCPUs but consistently uses only **5--10%** CPU, I evaluate whether a smaller instance can handle the workload.

### 11. What are Savings Plans and Reserved Instances?

**Answer:** Both help reduce AWS costs compared to On-Demand pricing.

-   **Savings Plans:** Offer discounted pricing in exchange for a
    commitment to a consistent amount of usage.
-   **Reserved Instances:** Provide discounted pricing for eligible EC2
    usage with a commitment.

### 12. What are Spot Instances?

**Answer:** Spot Instances use spare AWS EC2 capacity at discounted prices. They are suitable for batch processing, testing, CI/CD workloads, and fault-tolerant applications.

However, AWS can interrupt them when capacity is needed.

### 13. How do you reduce EBS costs?

**Answer:**
-   Identify unattached EBS volumes.
-   Delete unnecessary snapshots.
-   Use appropriate volume types.
-   Right-size oversized volumes.
-   Monitor volume usage.

### 14. How do you reduce S3 costs?

**Answer:** I use S3 Storage Classes according to data access patterns.

**For example:**

-   S3 Standard for frequently accessed data.
-   S3 Standard-IA for infrequently accessed data.
-   S3 Glacier for archival data.

I also configure lifecycle policies to automatically transition or expire old objects.

### 15. How do you optimize database costs in AWS?

**Answer:** Monitor CPU, memory, and storage usage. Resize instances during low demand, switch to Aurora Serverless for variable workloads, and choose cost-effective storage types.

### 16. How do you reduce AWS Lambda costs?

**Answer:** Tune memory and timeout settings based on execution metrics. Optimize code performance to reduce execution time.

---

## 🟠 Kubernetes / EKS Cost Optimization
*These questions are particularly useful for DevOps and Platform Engineer interviews.*

### 17. How do you optimize AWS EKS costs?

**Answer:** I focus on:

-   Right-sizing worker nodes.
-   Setting appropriate CPU and memory requests and limits.
-   Using Cluster Autoscaler or Karpenter.
-   Using Spot Instances for suitable workloads.
-   Scaling down unused workloads.
-   Monitoring resource utilization.

### 18. How can you reduce costs in a Kubernetes cluster?

**Answer:** We can reduce costs by avoiding over-provisioning, optimizing pod resource requests and limits, using horizontal pod autoscaling, and scaling down unused nodes.

### 19. What is Karpenter?

**Answer:** Karpenter is a Kubernetes node provisioning tool that automatically launches and terminates nodes based on workload requirements. It helps improve resource utilization and optimize infrastructure costs.

---

## 🟠 Scenario-Based Interview Questions

*These are particularly important because interviewers want to know how you handle real-world situations.*

### 20. Suddenly, your AWS bill increases by 40%. How will you investigate?

**Answer:** My approach would be:

- 1.  Open AWS Cost Explorer.
- 2.  Compare current spending with previous periods.
- 3.  Identify which AWS service has increased in cost.
- 4.  Check usage patterns and resource utilization.
- 5.  Investigate unexpected resources or increased traffic.
- 6.  Take corrective action and configure AWS Budgets alerts.

### 21. Your development team is using EC2 instances 24/7, but they only work during business hours. How would you optimize costs?

**Answer:** I would suggest scheduling non-production EC2 instances to stop after working hours and start again before business hours. This would reduce unnecessary running costs.

### 22. How do you prevent unexpected AWS bills?

**Answer:** I would configure AWS Budgets, billing alerts, Cost Explorer, and anomaly detection. I would also regularly review resource utilization and unused resources.

### 23. Your EC2 CPU utilization is consistently below 10%. What would you do?

**Answer:** I would analyze CPU and memory utilization over a reasonable period and check the application's actual requirements. If the instance is over provisioned, I would recommend a smaller instance type after testing.

### 24. How would you design a cost-efficient new application?

**Answer:** Use serverless or containerized architecture, right-size resources, implement Auto Scaling, optimize storage, and monitor continuously with CloudWatch and Budgets.


---

## 🟠 Real-world Interview Question -- Most Important

### 25. As a DevOps Engineer, what steps do you take to optimize AWS infrastructure costs?

**Answer:** You can use this natural interview answer:

> "Cost optimization is one of the important responsibilities of a
> DevOps Engineer. I regularly monitor AWS spending using Cost Explorer
> and AWS Budgets. I check EC2 and other resource utilization through
> CloudWatch. If I identify underutilized resources, I evaluate
> right-sizing opportunities. I also look for unused EBS volumes,
> unnecessary snapshots, and idle resources. For non-production
> environments, I recommend scheduling instances to stop outside working
> hours. In EKS, I focus on node right-sizing, autoscaling, and
> appropriate pod resource requests and limits. My goal is to reduce
> unnecessary spending without affecting application performance or
> availability."

### 26. How do you monitor and control your AWS costs effectively?

**Answer:** AWS provides tools like AWS Cost Explorer and AWS Budgets. Setting up billing alerts, regularly reviewing cost reports, and tagging resources for better tracking are effective strategies.

### 27.How does AWS Budgets help in managing and controlling costs?

**Answer:** AWS Budgets allows you to set custom cost and usage budgets, receive alerts, and take action to control costs effectively.

---

## 🟠 Real-World Use Case

### 28. Describe a time you identified and fixed an unexpected AWS cost spike

**Answer:**-1: In one ECS on EC2 project, we noticed an unexpected increase in AWS costs even though application traffic was stable. issue was identified through **AWS Cost Explorer and the billing dashboard**. On analysis, we found two main causes:
* The **Auto Scaling Group** was over-provisioned, leading to idle EC2 instances 
* **NAT Gateway** costs were high due to unnecessary outbound traffic from private subnets 
To fix this, I:  which we fixed through Right-sized the **ASG** min, max, and desired capacity based on actual ECS task demand and by adding VPC endpoints.
For prevention, I implemented **AWS Budgets alerts**, proper tagging, and monthly cost reviews.
This helped reduce overall AWS costs by around **2530%** without affecting performance.
**Interview power line (remember this)**
The cost spike wasnt traffic-related  it was caused by over-provisioned EC2 capacity and unnecessary NAT Gateway traffic, which we fixed through right-sizing and VPC endpoints.

**A-2:** At a previous company, our QA and staging environments ran 24/7  even though they were only used during business hours.
By implementing this shutdown automation, we cut monthly EC2 costs by 35%, without impacting productivity. ??
This small change had a big impact  especially across multiple non-production accounts.

### Key Components:
1. ### Cost Analysis via AWS Cost Explorer & Budgets:
* **AWS Cost Explorer:** Utilize AWS Cost Explorer to generate reports and visualize cost trends over time. This helps identify services or regions consuming the most budget.
* **AWS Budgets:** Set up budget alarms to receive notifications when spending approaches or exceeds predefined thresholds, ensuring proactive cost management.

2. ### Identify Underutilized EC2 Instances and Right-Sizing:
**EC2 Resource Monitoring:**
* Use AWS CloudWatch and AWS Trusted Advisor to monitor CPU, memory, and network utilization of all EC2 instances.
* Identify EC2 instances with consistently low utilization (e.g., CPU < 10% over a thresholds).

3. ### Schedule EC2 and RDS Shutdown During Off-Hours:
**Resource Scheduling:**
* Use AWS Instance Scheduler to automatically shut down EC2 instances, RDS databases, and other services during non-business hours (e.g., nights or weekends).
* Set up start/stop schedules for development and testing environments that don't require 24/7 availability.
* Use Lambda and EventBridge (formerly CloudWatch Events) to schedule resource start/stop events.


---

## Important AWS Cost Optimization Services

  AWS service                    Purpose
  ------------------------------ ---------------------------------------
  Cost Explorer                  Analyze AWS spending
  AWS Budgets                    Set budgets and alerts
  Cost and Usage Reports (CUR)   Detailed billing data
  Cost Anomaly Detection         Detect unusual spending
  Trusted Advisor                Cost optimization recommendations
  Compute Optimizer              Resource right-sizing recommendations
  CloudWatch                     Monitor resource utilization

