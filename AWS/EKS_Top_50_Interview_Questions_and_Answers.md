# Amazon EKS -- Top 50 Interview Questions and Answers

A practical interview-preparation guide for **DevOps Engineer, AWS
DevOps Engineer, and Platform Engineer** roles. Answers are written in
simple, interview-friendly English.

## Table of Contents

-   [Section 1: EKS Fundamentals](#section-1-eks-fundamentals)
-   [Section 2: EKS Architecture, Nodes and
    Networking](#section-2-eks-architecture-nodes-and-networking)
-   [Section 3: EKS Workloads and Kubernetes
    Resources](#section-3-eks-workloads-and-kubernetes-resources)
-   [Section 4: EKS Security, IAM and
    Storage](#section-4-eks-security-iam-and-storage)
-   [Section 5: EKS Troubleshooting, Scaling and Real-World
    Scenarios](#section-5-eks-troubleshooting-scaling-and-real-world-scenarios)
-   [Additional Topics to Prepare](#additional-topics-to-prepare)

------------------------------------------------------------------------

## Section 1: EKS Fundamentals

### 1. What is Amazon EKS?

Amazon EKS (Elastic Kubernetes Service) is a fully managed Kubernetes
service provided by AWS. It allows us to run and manage containerized
applications using Kubernetes without managing the Kubernetes control
plane ourselves.

**Key points:** - AWS manages the Kubernetes control plane. - We manage
worker nodes or compute resources. - It supports EC2 and AWS Fargate for
running workloads. - It integrates with AWS services such as IAM, VPC,
ELB, and CloudWatch.

### 2. What is Kubernetes?

Kubernetes is an open-source container orchestration platform used to
automate the deployment, scaling, and management of containerized
applications. It helps manage containers, maintain application
availability, scale workloads, and recover from failures.

### 3. What is the difference between Kubernetes and Amazon EKS?

### 3. What is the difference between Kubernetes and Amazon EKS?

| Kubernetes | Amazon EKS |
|---|---|
| Open-source container orchestration platform. | AWS-managed Kubernetes service. |
| Can be installed on our own infrastructure. | AWS manages the Kubernetes control plane. |
| In a self-managed setup, we maintain the control plane. | AWS manages control-plane availability. |
| Requires us to configure cloud integrations. | Provides built-in AWS integrations. |
| Can run on different platforms, including on-premises and cloud environments. | Runs Kubernetes control planes managed by AWS, with workloads on AWS infrastructure or supported hybrid environments. |

### 4. What are the main components of Amazon EKS architecture?

EKS architecture has two main parts:

**Control plane** - **API Server:** Handles requests from kubectl and
other clients. - **Scheduler:** Assigns Pods to suitable worker nodes. -
**Controller Manager:** Maintains the desired cluster state. - **etcd:**
Stores Kubernetes cluster data.

**Data plane (worker nodes)** - EC2 instances or Fargate - Kubelet -
Kube-proxy - Container runtime - Pods

AWS manages the EKS control plane, while we manage worker nodes and
application workloads.

### 5. What is an EKS cluster?

An EKS cluster is a Kubernetes environment running on AWS. It consists
of a managed Kubernetes control plane and compute resources where our
applications run. We deploy applications using Kubernetes resources such
as Deployments, Services, and Pods.

### 6. What is the EKS control plane?

The control plane manages the overall Kubernetes cluster. Its main
components are the API Server, Scheduler, Controller Manager, and etcd.
AWS manages the control plane in Amazon EKS.

### 7. What are worker nodes in EKS?

Worker nodes are the compute resources where application containers run.
They can be Amazon EC2 instances or AWS Fargate compute. EC2 worker
nodes run Kubernetes components such as kubelet and a container runtime
to communicate with the control plane and execute workloads.

### 8. What is the difference between EKS and ECS?

  -----------------------------------------------------------------------
  Amazon EKS                          Amazon ECS
  ----------------------------------- -----------------------------------
  Managed Kubernetes service          AWS-native container orchestration
                                      service

  Uses Kubernetes concepts            Uses ECS-specific concepts

  Supports Kubernetes manifests and   Uses task definitions and services
  APIs                                

  Portable across Kubernetes          Closely integrated with AWS
  platforms                           

  Has a broad Kubernetes ecosystem    Often simpler for AWS-native
                                      container workloads
  -----------------------------------------------------------------------

### 9. What is the difference between EKS and self-managed Kubernetes?

In self-managed Kubernetes, we are responsible for installing,
configuring, upgrading, and maintaining the control plane and worker
nodes. In Amazon EKS, AWS manages the Kubernetes control plane and its
availability. We remain responsible for worker nodes, application
workloads, add-ons, networking configuration, and security settings.

### 10. What is the difference between a Pod and a Node?

A **Pod** is the smallest deployable unit in Kubernetes. It contains one
or more containers. A **Node** is a worker machine, either virtual or
physical, where Pods run. An EC2 instance in an EKS cluster can act as a
worker node, and multiple Pods can run on it depending on available
resources.

------------------------------------------------------------------------

## Section 2: EKS Architecture, Nodes and Networking

### 11. What are Managed Node Groups in EKS?

Managed Node Groups allow us to create and manage a group of EC2 worker
nodes through Amazon EKS. EKS automates several node lifecycle
operations, including creating the Auto Scaling group, replacing
unhealthy instances in supported situations, and performing managed
updates. It simplifies worker-node management.

### 12. What is the difference between Managed Node Groups and Self-Managed Nodes?

| Managed Node Groups | Self-Managed Nodes |
|---|---|
| Managed through Amazon EKS. | Managed by us. |
| AWS automates several node lifecycle operations. | We handle more node lifecycle operations. |
| Integrated with EKS update workflows. | Requires more manual configuration and updates. |
| Easier to maintain and operate. | Offers greater infrastructure control and flexibility. |

### 13. What is AWS Fargate in EKS?

AWS Fargate is a serverless compute engine that allows us to run
Kubernetes Pods without managing EC2 worker nodes. We define CPU and
memory requirements, and AWS provides the underlying compute capacity.
It is useful when we want to run workloads without managing worker
instances, subject to Fargate's supported configurations and
limitations.

### 14. What is the difference between EC2 and Fargate in EKS?

| EC2 | Fargate |
|---|---|
| We manage the worker nodes. | AWS manages the underlying compute infrastructure. |
| Multiple Pods can run on a single worker node. | Each Fargate Pod runs in its own isolated compute environment. |
| Provides greater control over node configuration. | Requires less infrastructure management. |
| Suitable for workloads requiring node-level customization. | Suitable for workloads that fit Fargate's supported configurations and limitations. |

### 15. What is a VPC in EKS?

A VPC (Virtual Private Cloud) is an isolated virtual network in AWS
where we deploy our EKS cluster and associated resources. It provides
networking components such as public and private subnets, route tables,
Internet Gateways, NAT Gateways, and Security Groups.

### 16. Why do we use private subnets for EKS worker nodes?

We commonly use private subnets to prevent direct inbound internet
access to worker nodes. This reduces their exposure. For outbound
internet access, we can use a NAT Gateway. VPC endpoints can provide
private connectivity to supported AWS services.

### 17. What is the Amazon VPC CNI plugin?

Amazon VPC CNI is the default networking plugin commonly used by EKS. It
assigns VPC IP addresses to Kubernetes Pods and configures networking so
Pods can communicate with other resources according to VPC routing and
security rules.

### 18. How does networking work in EKS?

EKS commonly uses the Amazon VPC CNI plugin. In its standard
configuration, Pods receive IP addresses from the VPC address space.
Pods can communicate with other Pods, nodes, and AWS resources according
to routing, Security Groups, and any configured network policies.

### 19. What is a Security Group in EKS?

A Security Group acts as a virtual firewall that controls inbound and
outbound traffic. In EKS, Security Groups can be associated with
worker-node network interfaces, cluster network interfaces, and---in
supported configurations---Pod network interfaces using Security Groups
for Pods.

### 20. What is the difference between a Security Group and a Network ACL?

| Security Group | Network ACL |
|---|---|
| Applied at the network interface (ENI) level. | Applied at the subnet level. |
| Stateful. | Stateless. |
| Supports allow rules only. | Supports both allow and deny rules. |
| Return traffic is automatically allowed, regardless of inbound or outbound rules. | Return traffic must be explicitly allowed by the applicable inbound and outbound rules. |

------------------------------------------------------------------------

## Section 3: EKS Workloads and Kubernetes Resources

### 21. What is a Deployment in Kubernetes?

A Deployment is a Kubernetes resource used to manage application Pods.
It helps us maintain the desired number of replicas, perform rolling
updates, roll back to previous versions, and manage application
releases. For example, a Deployment with three replicas tries to keep
three Pods running.

### 22. What is a ReplicaSet?

A ReplicaSet ensures that the specified number of Pod replicas are
running. If we configure three replicas and one Pod fails, the
ReplicaSet creates another Pod to maintain the desired count. A
Deployment manages ReplicaSets, so we generally create Deployments
rather than ReplicaSets directly.

### 23. What is a Service in Kubernetes?

A Service provides a stable network endpoint to access a group of Pods.
Pod IP addresses can change when Pods are recreated, so a Service
provides a consistent way for other workloads or users to reach them.
Common Service types are ClusterIP, NodePort, and LoadBalancer.

### 24. What are the different types of Services in Kubernetes?

1.  **ClusterIP:** Exposes the application internally within the
    cluster. It is the default Service type.
2.  **NodePort:** Exposes the application through a port on each worker
    node.
3.  **LoadBalancer:** Requests an external load balancer through the
    cloud-provider integration, such as AWS.

We can use a LoadBalancer Service to expose an application running in
EKS to external users.

### 25. What is an Ingress in Kubernetes?

Ingress is a Kubernetes API resource that defines rules for external
HTTP and HTTPS access to Services inside the cluster. It can route
traffic based on hostnames and URL paths and can define TLS settings. In
EKS, the AWS Load Balancer Controller can implement Ingress resources
using an Application Load Balancer (ALB).

### 26. What is AWS Load Balancer Controller?

AWS Load Balancer Controller is a Kubernetes controller that manages AWS
load balancers for applications running in EKS. It supports Application
Load Balancers (ALBs) through Ingress resources and Network Load
Balancers (NLBs) through supported Service configurations. It provisions
and configures load balancers based on Kubernetes resources.

### 27. What is the difference between Ingress and LoadBalancer Service?

| Ingress | LoadBalancer Service |
|---|---|
| Defines HTTP/HTTPS routing rules. | Exposes a Kubernetes Service through a cloud load balancer. |
| Can route traffic to multiple applications using host-based or path-based rules. | Typically exposes one Service through a dedicated load balancer. |
| Can use a single ALB to route traffic to multiple applications. | Commonly provisions a separate load balancer for each Service. |
| Requires an Ingress controller to implement routing rules. | Uses cloud-provider integration or an appropriate load balancer implementation. |
| Supports advanced HTTP routing, such as host-based and path-based routing. | Primarily provides external access to a Service. |

### 28. What is a ConfigMap in Kubernetes?

A ConfigMap stores non-sensitive application configuration as key-value
pairs. Examples include application environment settings, database
hostnames, and log levels. We can expose ConfigMap values to containers
as environment variables or mounted files.

### 29. What is a Secret in Kubernetes?

A Secret stores sensitive information such as database passwords, API
tokens, credentials, and TLS certificates. Pods can consume Secrets
through environment variables or mounted volumes.

**Important:** Kubernetes Secrets are base64-encoded by default; base64
is not encryption. Configure encryption at rest where appropriate and
restrict access using RBAC. In EKS, applications can also integrate with
AWS Secrets Manager or Systems Manager Parameter Store using supported
CSI integrations.

### 30. What is a Namespace in Kubernetes?

A Namespace provides logical separation of resources within a Kubernetes
cluster. For example, we can create separate namespaces for development,
testing, staging, and production. Namespaces help organize resources and
manage access through Kubernetes RBAC.

------------------------------------------------------------------------

## Section 4: EKS Security, IAM and Storage

### 31. How does IAM work with Amazon EKS?

IAM manages authentication to EKS and permissions to access AWS
resources. IAM identities can authenticate to a Kubernetes cluster,
while IAM roles can provide AWS permissions to applications running
inside Pods. We commonly use IAM Roles for Service Accounts (IRSA) or
EKS Pod Identity to grant AWS permissions to workloads.

### 32. What is IRSA in EKS?

IRSA stands for IAM Roles for Service Accounts. It allows a Kubernetes
ServiceAccount to be associated with an IAM role so that Pods using that
ServiceAccount can obtain temporary AWS credentials.

**Example: Giving an application access to S3** 1. Create an IAM policy
containing the required S3 permissions. 2. Create an IAM role with a
trust relationship for the cluster's OIDC provider. 3. Associate the
role with a Kubernetes ServiceAccount. 4. Configure the application Pod
to use that ServiceAccount.

This avoids storing long-lived AWS access keys in containers.

### 33. What is EKS Pod Identity?

EKS Pod Identity allows applications running in EKS Pods to obtain
temporary AWS credentials through an associated IAM role. We create a
Pod Identity association between an EKS cluster, Kubernetes namespace,
ServiceAccount, and IAM role. The EKS Pod Identity Agent runs on the
nodes and helps provide credentials to supported workloads.

### 34. What is the difference between IRSA and EKS Pod Identity?

| IRSA (IAM Roles for Service Accounts) | EKS Pod Identity |
|---|---|
| Uses an IAM OIDC identity provider associated with the EKS cluster. | Uses the EKS Pod Identity service and Pod Identity Agent. |
| Requires an OIDC provider to be configured for the cluster. | Does not require us to configure an IAM OIDC provider for each cluster specifically for Pod Identity. |
| Uses OIDC-based web identity federation. | Uses the EKS Pod Identity credential delivery mechanism. |
| Requires IAM role trust policies referencing the cluster's OIDC provider. | Uses Pod Identity associations to map Kubernetes service accounts to IAM roles. |
| Established approach for granting AWS permissions to Kubernetes workloads. | Newer AWS-native approach for granting AWS permissions to Kubernetes workloads. |

Both provide temporary IAM credentials to applications running in Pods.

### 35. What is Kubernetes RBAC?

RBAC stands for Role-Based Access Control. It controls what users and
ServiceAccounts can do with Kubernetes resources.

-   **Role:** Defines permissions within a namespace.
-   **ClusterRole:** Defines cluster-scoped permissions or reusable
    permissions.
-   **RoleBinding:** Grants a Role or ClusterRole within a namespace.
-   **ClusterRoleBinding:** Grants a ClusterRole across the cluster.

For example, we can give a developer permission to view Pods in the
development namespace without allowing them to delete resources.

### 36. What is the difference between IAM and Kubernetes RBAC?

| IAM | Kubernetes RBAC |
|---|---|
| Controls access to AWS resources and AWS APIs. | Controls access to Kubernetes resources and API operations. |
| Uses IAM users, groups, roles, and policies. | Uses Roles, ClusterRoles, RoleBindings, and ClusterRoleBindings. |
| Defines permissions for AWS services and resources. | Defines permissions within a Kubernetes cluster or namespace. |
| Example: Allow access to an S3 bucket. | Example: Allow a user to view, create, or delete Pods. |

Both are important for securing EKS. IAM commonly handles AWS identity
and authentication, while Kubernetes RBAC authorizes actions against
Kubernetes resources.

### 37. How do you manage persistent storage in EKS?

We use Kubernetes PersistentVolumes (PV) and PersistentVolumeClaims
(PVC) to manage persistent storage. In EKS, common storage options
include:

-   **Amazon EBS:** Block storage, often used for databases and
    application data.
-   **Amazon EFS:** Shared network file storage.

The AWS EBS CSI Driver and EFS CSI Driver integrate these storage
services with Kubernetes.

### 38. What is the difference between EBS and EFS in EKS?

| Amazon EBS | Amazon EFS |
|---|---|
| Provides block storage. | Provides shared network file storage. |
| Commonly used for workloads requiring block devices. | Supports shared file access from multiple clients. |
| Suitable for databases and applications requiring persistent block storage. | Suitable for shared files, application content, and shared data. |
| Uses the Amazon EBS CSI Driver. | Uses the Amazon EFS CSI Driver. |
| Typically supports ReadWriteOnce access mode for a volume. | Supports ReadWriteMany access mode for shared access. |
| Generally tied to a specific Availability Zone. | Regional file system that can be accessed across multiple Availability Zones. |

### 39. What is a PersistentVolumeClaim (PVC)?

A PersistentVolumeClaim is a request for storage made by a Kubernetes
application. For example, if an application needs 10 GB of persistent
storage, we can create a PVC requesting 10 GB. Kubernetes uses the
configured StorageClass and available storage-provisioning mechanism to
satisfy the request.

### 40. What is a StorageClass in Kubernetes?

A StorageClass defines how storage should be dynamically provisioned. In
EKS, we can use StorageClasses with CSI drivers to create AWS storage
resources automatically. For example, when an application creates a PVC,
the EBS CSI Driver can provision an EBS volume based on the StorageClass
configuration.

------------------------------------------------------------------------

## Section 5: EKS Troubleshooting, Scaling and Real-World Scenarios

### 41. How do you deploy an application in EKS?

We can deploy applications in EKS using Kubernetes manifests or Helm
charts.

**Typical workflow:** 1. Build the Docker image. 2. Push the image to
Amazon ECR. 3. Configure kubectl to connect to the EKS cluster. 4.
Create a Kubernetes Deployment. 5. Create a Service to expose the
application. 6. Configure Ingress or a LoadBalancer if external access
is required. 7. Verify the application and monitor its health.

Example commands:

``` bash
aws eks update-kubeconfig \
  --region ap-south-1 \
  --name my-eks-cluster

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

kubectl get pods
kubectl get services
```

### 42. How do you connect to an EKS cluster using kubectl?

First, configure AWS CLI credentials and ensure the IAM identity has the
required EKS access. Then run:

``` bash
aws eks update-kubeconfig \
  --region ap-south-1 \
  --name my-eks-cluster
```

This updates the kubeconfig file with the cluster connection details and
authentication configuration. Verify connectivity:

``` bash
kubectl get nodes
kubectl get pods -A
```

### 43. What is Amazon ECR, and how is it used with EKS?

Amazon ECR (Elastic Container Registry) is a managed container image
registry provided by AWS. We use ECR to store container images and
deploy them into EKS.

**Typical workflow:** 1. Build a Docker image. 2. Tag the image. 3.
Authenticate Docker with ECR. 4. Push the image to ECR. 5. Reference the
ECR image in the Kubernetes Deployment manifest.

Example:

``` bash
docker build -t myapp:v1 .

docker tag myapp:v1 \
  123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1

docker push \
  123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1
```

Replace the example account ID, region, and repository name with your
own values.

### 44. What is the Horizontal Pod Autoscaler (HPA)?

HPA automatically scales the number of Pod replicas based on resource
utilization or configured custom metrics. For example, if CPU
utilization increases, HPA can increase the number of application Pods.

Example:

``` bash
kubectl autoscale deployment myapp \
  --cpu-percent=70 \
  --min=2 \
  --max=10
```

This sets a target average CPU utilization of 70%, with a minimum of two
and maximum of ten replicas. Resource requests and a metrics source such
as Metrics Server are needed for CPU-based scaling.

### 45. What is Cluster Autoscaler?

Cluster Autoscaler adjusts the number of worker nodes based on Pod
scheduling requirements and node utilization. If Pods cannot be
scheduled because there are insufficient resources, it can increase
node-group capacity when configured correctly. It can also remove
underutilized nodes when workloads can be moved safely.

### 46. What is the difference between HPA and Cluster Autoscaler?

| HPA (Horizontal Pod Autoscaler) | Cluster Autoscaler |
|---|---|
| Scales the number of Pods. | Scales the number of worker nodes. |
| Works at the application level. | Works at the infrastructure level. |
| Uses CPU, memory, or custom metrics to scale Pods. | Adds nodes when Pods cannot be scheduled due to insufficient resources and removes eligible underutilized nodes. |
| Requires sufficient cluster capacity to schedule additional Pods. | Helps provide additional node capacity when needed. |
| Example: Increases Pods from 2 to 5 when CPU utilization is high. | Example: Adds a worker node when existing nodes cannot accommodate pending Pods. |

**Example:** HPA increases application replicas from two to six. If
existing nodes do not have enough resources, Cluster Autoscaler can add
EC2 worker nodes, provided the node group has scaling capacity.

### 47. What is the Metrics Server in EKS?

Metrics Server collects resource-utilization metrics such as CPU and
memory usage from Kubernetes nodes and Pods. HPA can use these metrics
to make scaling decisions.

Example commands:

``` bash
kubectl top nodes
kubectl top pods -A
```

### 48. What would you do if a Pod is stuck in Pending state?

If a Pod is stuck in Pending state, I first check why Kubernetes cannot
schedule it.

Commands:

``` bash
kubectl get pods
kubectl describe pod <pod-name> -n <namespace>
```

I investigate common causes such as: - Insufficient CPU or memory. -
Node selector or affinity mismatch. - Untolerated node taints. - PVC or
storage-provisioning issues. - Resource quota limitations.

I use the events in the `describe` output to identify the cause, resolve
the configuration or capacity issue, and verify that the Pod gets
scheduled.

### 49. What would you do if a Pod is in CrashLoopBackOff state?

CrashLoopBackOff means a container repeatedly starts and crashes, and
Kubernetes retries it with increasing delays.

First, I check the Pod status, logs, previous container logs, and
events:

``` bash
kubectl get pods

kubectl logs <pod-name> -n <namespace>

kubectl logs <pod-name> -n <namespace> --previous

kubectl describe pod <pod-name> -n <namespace>
```

I investigate possible causes such as: - Application startup errors. -
Incorrect environment variables. - Missing ConfigMaps or Secrets. -
Database or external-service connectivity issues. - Incorrect startup,
liveness, or readiness probes. - Memory-related issues, including
OOMKilled.

After identifying the root cause, I fix the application or configuration
issue and verify that the Pod becomes healthy.

### 50. How do you monitor an EKS cluster?

We can monitor an Amazon EKS cluster using AWS services and Kubernetes monitoring tools.

| Tool | Purpose |
|---|---|
| Amazon CloudWatch | Monitors logs, metrics, and alarms. |
| CloudWatch Container Insights | Monitors cluster, node, and Pod performance. |
| Prometheus | Collects and stores Kubernetes and application metrics. |
| Grafana | Visualizes metrics using dashboards. |
| kubectl | Inspects Pod, node, and cluster status. |
| AWS CloudTrail | Tracks EKS-related AWS API activity for auditing. |

**Example: Useful kubectl monitoring commands**

Check cluster nodes:

```bash
kubectl get nodes

Useful commands:

``` bash
kubectl get nodes
kubectl get pods -A
kubectl top nodes
kubectl top pods -A
kubectl get events -A
```

In production, we can configure CloudWatch alarms and dashboards to
identify resource issues, application failures, and infrastructure
problems.

------------------------------------------------------------------------

## Additional Topics to Prepare

For experienced DevOps Engineer and Platform Engineer interviews, also
prepare these practical topics:

  -----------------------------------------------------------------------
  Priority                Topic                   What to prepare
  ----------------------- ----------------------- -----------------------
  High                    EKS Cluster Setup       Create an EKS cluster
                                                  using Terraform

  High                    EKS Networking          VPC CNI, CoreDNS,
                                                  kube-proxy

  High                    AWS Load Balancer       ALB, NLB, Ingress
                          Controller              

  High                    IAM and Security        IRSA, Pod Identity,
                                                  RBAC

  High                    EKS Troubleshooting     Pending,
                                                  CrashLoopBackOff,
                                                  ImagePullBackOff

  High                    Auto Scaling            HPA, Cluster
                                                  Autoscaler, Karpenter

  High                    CI/CD with EKS          Jenkins, GitHub Actions

  Medium                  Helm                    Charts, values.yaml,
                                                  upgrades, and rollback

  Medium                  Storage                 EBS CSI, EFS CSI

  Medium                  Monitoring              CloudWatch, Prometheus,
                                                  Grafana

  Medium                  Terraform               Provision EKS using
                                                  Terraform

  Medium                  EKS Upgrades            Kubernetes version and
                                                  node-group upgrades
  -----------------------------------------------------------------------

## Recommended Preparation Order

1.  **EKS fundamentals:** Cluster architecture, control plane, worker
    nodes, Pods, Deployments, and Services.
2.  **EKS networking:** VPC, subnets, VPC CNI, Security Groups, ALB, and
    Ingress.
3.  **EKS security:** IAM, IRSA, Pod Identity, ServiceAccounts, and
    RBAC.
4.  **Real-world troubleshooting:** Pending Pods, CrashLoopBackOff,
    ImagePullBackOff, and networking issues.
5.  **DevOps integration:** Jenkins, ECR, EKS, Terraform, and Helm
    deployment workflows.

## Interview Tip

Don't just memorize these answers. Understand each concept and prepare
one practical example from your own DevOps experience. For
scenario-based questions, explain:

1.  What the issue was.
2.  Which commands or tools you used to investigate it.
3.  What the root cause was.
4.  How you fixed it.
5.  How you verified the solution.

------------------------------------------------------------------------

**Document:** Amazon EKS -- Top 50 Interview Questions and Answers\
**Audience:** DevOps Engineer \| AWS DevOps Engineer \| Platform
Engineer
