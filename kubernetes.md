# **Kubernetes Interview Preparation Guide**

A comprehensive Kubernetes interview preparation guide
covering **beginner to advanced concepts, scenario-based questions,
troubleshooting, and frequently used kubectl commands**.

---

## Table of Contents

- [1. Beginner Level](#-beginner-level)
- [2. Intermediate Level](#-intermediate-level)
- [3. Advanced Level](#-Advanced-Level)
- [4. Scenario-Based Questions](#-Scenario-Based-Questions)
- [5. Frequently Asked Commands](#-Frequently-Asked-Commands)
- [6. Additional Resources](#-Additional-Resources)
- [7. Final Interview Checklist](#-Final-Interview-Checklist)

---

## 🟠 **Beginner Level**

### **1. What is Kubernetes?**

**Kubernetes (K8s)** is an open-source
container orchestration platform that automates the deployment, scaling,
networking, and management of containerized applications.

### **2. What is a Pod?**

A **Pod** is the smallest deployable unit in Kubernetes. A Pod
can contain one or more containers that share the same network namespace and
can share storage volumes.

### **3. What is a Node?**

A **Node** is a physical or virtual machine that runs Kubernetes
workloads (Pods).

A node typically contains:
- kubelet
- Container runtime
- kube-proxy (where applicable)

### **4. What is a Cluster?**

A **Kubernetes cluster** is a collection of machines managed by
Kubernetes.

A cluster generally consists of:

·      
**Control plane nodes** — manage the cluster

·      
**Worker nodes** — run application workloads

### **5. What is a Deployment?**

A **Deployment** manages a set of Pods through **ReplicaSets**.

It provides features such as:
- Rolling updates
- Rollbacks
- Scaling
- Declarative updates
- Replica management

### **6. What is a ReplicaSet?**

A **ReplicaSet** ensures that a specified number of Pod replicas
are running at any given time.

#### *Example:* 
If a ReplicaSet is configured with three replicas, Kubernetes attempts to maintain three matching Pods.

### **7. What is a Namespace?**

A **Namespace** provides logical isolation within a Kubernetes
cluster.

Namespaces can be used to organize and separate resources such as:

- Pods
- Deployments
- Services
- ConfigMaps
- Secrets

## **8. What is a Service?**

A **Service** provides a stable network endpoint for accessing a group of Pods.

Because Pods are ephemeral and their IP addresses can change, Services provide stable connectivity and DNS-based discovery.

### Types of Kubernetes Services

| Service Type | Description |
|---|---|
| **ClusterIP** | Exposes the Service internally within the cluster. This is the default type. |
| **NodePort** | Exposes the Service on a static port on each node’s IP address. |
| **LoadBalancer** | Exposes the Service externally through a cloud/provider load balancer when supported. |
| **ExternalName** | Maps a Service to an external DNS name using a CNAME record. |

### **9. What is kubectl?**

**kubectl** is the Kubernetes command-line tool used to communicate with the
Kubernetes API server and manage cluster resources.

#### *Example:*
```bash
kubectl get pods

kubectl get nodes

kubectl describe pod \<pod-name>

kubectl apply -f app.yaml
```
---

## 🟠 **Intermediate Level**

## **10. What is the Difference Between a Pod and a Deployment?**

| **Pod** | **Deployment** |
|---|---|
| Runs one or more containers | Manages Pods |
| Can be created directly | Creates and manages ReplicaSets |
| Does not provide built-in rollout management | Supports rolling updates and rollbacks |
| Not normally used directly for production workloads | Commonly used for stateless applications |

### ** **

### **11. What is etcd?**

**etcd** is a distributed, consistent
key-value store used by Kubernetes to store cluster state and configuration.

Examples of information stored in **etcd** include:

  
- Kubernetes resource definitions   
- Cluster configuration    
- Desired state    
- Metadata

Protecting and backing up **etcd** is critical for cluster
recovery.

### ** **

### **12. What is kubelet?**

**kubelet** is the primary node agent.

It runs on every worker node and:

- Communicates with the Kubernetes API server
-	Ensures assigned Pods are running
-	Monitors containers
-	Reports node and Pod status

### **13. What is kube-proxy?**

**kube-proxy** is a node-level component
responsible for implementing Service networking rules.

It helps route traffic destined for Kubernetes Services to the
appropriate backend Pods.

Modern Kubernetes networking can use alternatives to kube-proxy, depending on the cluster’s
networking implementation.

### **14. What is a ConfigMap?**

A **ConfigMap** stores non-sensitive configuration data as
key-value pairs.

#### *Example:*

- Application configuration   
- Environment variables  
- Configuration files
- Command-line arguments

#### *Example:*
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: production
  LOG_LEVEL: info
```
**Notes:** data stores configuration as text. Passwords and other sensitive information should be stored in a Kubernetes Secret, not a ConfigMap.

### **15. What is a Secret?**

A **Secret** stores sensitive information such as:
     
- Passwords  
- API tokens   
- TLS certificates  
- Credentials
- 
#### *Example:*
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
stringData:
  DB_USERNAME: admin
  DB_PASSWORD: password
```

Notes: Kubernetes Secrets are Base64-encoded by default, but **Base64
encoding is not encryption**. Appropriate encryption-at-rest and access
controls should be used for sensitive environments.

### **16. What is the Difference Between ConfigMap and Secret**

| **Feature** | **ConfigMap** | **Secret** |
|---|---|---|
| **Intended use** | Non-sensitive configuration | Sensitive configuration |
| **Typical data** | URLs, feature flags, settings | Passwords, tokens, certificates |
| **Default representation** | Plain text | Base64-encoded |
| **Encryption** | Not inherently encrypted | Can be encrypted at rest when configured |
| **Access** | Controlled through RBAC | Should be tightly restricted |


### **17. What are Labels and Selectors?**

**Labels** are key-value pairs attached to Kubernetes resources to identify and organize them.

***Example:***
```yaml
labels:
  app: nginx
  environment: production
```

**Selectors** are used to identify resources with matching labels.

***Example***, a Service can select Pods
using:
```yaml
selector:
  app: nginx
```
### **18. What is a DaemonSet?**

A **DaemonSet** ensures that a copy of a Pod runs on every node, or on a selected subset of nodes.

Common use cases include:   
- Log collection agents  
- Monitoring agents 
- Node-level networking components
- Security agents

---

## 🟠 **Advanced Level**

### **19. What is a StatefulSet?**

A StatefulSet manages stateful applications that require:
-	Stable network identities and Pod names 
-	Persistent storage 
-	Ordered deployment and termination 

Common use cases include Databases, distributed systems, and message queues.

**tip:** A Deployment is generally used for stateless applications, while a StatefulSet is used when Pods need stable identities or persistent storage.


### **20. What is a Job?**

A **Job** creates Pods that run a task until it successfully
completes.

**Example use cases:**
      
- Database migrations
    
- Batch processing
   
- One-time scripts
   
- Data processing tasks

### **21. What is a CronJob?**

A **CronJob** creates Jobs according to a schedule, similar to Linux cron.

**Example:**

schedule: "0 2 \* \* \*"

This could be used to run a task every day at 2:00 AM.

### **22. What is a Persistent Volume (PV)?**

A **PersistentVolume (PV)** is a cluster-level storage resource.

A PV can be:

- Dynamically provisioned
- Statically provisioned

It represents storage that can be consumed by workloads through
PersistentVolumeClaims.

### **23. What is a PersistentVolumeClaim (PVC)?**

A **PersistentVolumeClaim (PVC)** is a request for storage made by a workload.

*A PVC can specify requirements such as:*
   
- Storage capacity 
- Access modes     
- Storage class

***Example:***
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-storage
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

### **24. What is  the Difference Between PV and PVC**

| **PV** | **PVC** |
|---|---|
| Represents actual storage | Represents a request for storage |
| Cluster-level resource | Namespace-scoped resource |
| Can be statically or dynamically provisioned | Used by workloads to request storage |
| Provides storage capacity | Specifies storage requirements |


### **25. What is a StorageClass?**

A **StorageClass** defines how storage should be dynamically provisioned.

It can specify:
    
- Storage provisioner 
- Parameters  
- Reclaim policy
- Volume binding mode

***Example:***
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
provisioner: example.com/fast
```

### **26. What is a Taint?**

A **taint** is applied to a node to prevent Pods from being scheduled there unless they have a matching toleration.

***Example:***
```bash
kubectl taint nodes node1 dedicated=database\:NoSchedule
```

### **27. What is a Toleration?**

A **toleration** allows a Pod to be scheduled onto a node with a matching taint.

***Example:***
```yaml
tolerations:
  - key: dedicated
    operator: Equal
    value: database
    effect: NoSchedule
```
A toleration allows a Pod to tolerate a taint; it does not by itself force the Pod onto that node.

### **28. What is Node Affinity?**

**Node affinity** defines rules that influence which nodes a Pod can be scheduled on based on node labels.

**Example:**
```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: disktype
              operator: In
              values:
                - ssd
```
---

## 🟠 **Scenario-Based Questions**

### **29. A Pod is stuck in Pending state. How do you troubleshoot?**

Start by inspecting the Pod:
```bash
kubectl describe pod <pod-name>
```
***Then check:***
    
- Scheduler events
- Node availability
- CPU and memory resources
- Resource requests   
- Taints and tolerations
- Node selectors and affinity rules    
- PVC binding status    
- Namespace quotas   
- Scheduling constraints

***Useful commands:***
```bash
kubectl get pod <pod-name> -o wide

kubectl describe pod <pod-name>

kubectl get nodes

kubectl describe nodes

kubectl get events --sort-by=.lastTimestamp
```

### **30. A Pod is in CrashLoopBackOff. What do you do?**

**CrashLoopBackOff** usually means the container starts, exits or crashes, and Kubernetes repeatedly attempts to restart it with increasing delays.

#### **1. Check the pod logs**
```bash
kubectl logs \<pod-name>
```
**For a specific container:**
```bash
kubectl logs \<pod-name> -c \<container-name>
```
**If the container has restarted, check its previous logs:**
```bash
kubectl logs \<pod-name> --previous
```
#### **2. Describe the Pod**
```bash
kubectl describe pod \<pod-name>
```
**Look at:**

- **Last State**: Why did it crash? (Exit code, OOMKilled)
- **Events**: Scheduling, pulling image, starting issues
- **Restart Count**: How many times has it restarted?
- **Conditions**: Are all containers ready?

#### **3. Verify Common causes**

- **a. Image Issues:**              # *Verify image exists* or accessible*
- **b. Resource Limits:**           # *Check resource requests/limits and if OOMKilled*
- **c. Configuration Issues:**      # *Verify ConfigMaps and Secrets exist or environment variables*
- **d. Startup Issues:**            # *Check liveness/readiness probe configuration*
- **e. Networking Issues:**         # *Can't connect to database/service*

### **31. How do you scale a Deployment?**
```bash
kubectl scale deployment nginx --replicas=5
```
**Verify:**
```bash
kubectl get deployment nginx

kubectl get pods
```
### **32. How do you perform a rolling update?**

**Update the container image:**
```bash
kubectl set image deployment/nginx nginx=nginx:1.27
```
**Check the rollout:**
```bash
kubectl rollout status deployment/nginx
```
### **33. How do you roll back a Deployment?**
```bash
kubectl rollout undo deployment/nginx
```
**View rollout history:**
```bash
kubectl rollout history deployment/nginx
```

### **34. How do you check rollout status?**
```bash
kubectl rollout status deployment/nginx
```
### **35. How do you expose a Deployment?**

***Example** **,* to expose the Deployment through a LoadBalancer Service:
```bash
kubectl expose deployment nginx --type=LoadBalancer --port=80
```
***Check the Service:***
```bash
kubectl get service nginx
```
### **36. How do you view cluster events?**
```bash
kubectl get events
```
***For events sorted chronologically:***
```bash
kubectl get events --sort-by=.lastTimestamp
```
***For a particular namespace:***
```bash
kubectl get events -n \<namespace> --sort-by=.lastTimestamp
```
### **37. How do you check node health?**
```bash
kubectl get nodes
```
***For detailed information:***
```bash
kubectl describe node \<node-name>
```
***Check resource usage:***
```bash
kubectl top nodes
```
### **38. How do you debug Kubernetes networking issues?**

Follow a systematic approach:

1. **Check Pods**
```bash  
kubectl get pods -o wide
```
2. **Check the Service**
```bash  
kubectl get service \<service-name>
kubectl describe service \<service-name>
```
3. **Check EndpointSlices**

```bash  
kubectl get endpointslices
```
4. **Check DNS resolution**
  
Test DNS from a temporary/debug Pod.

5. **Check NetworkPolicies**
```bash  
kubectl get networkpolicies
```
6. **Test connectivity**
        
Use tools such as curl, wget, nslookup, or dig from an appropriate debugging container.

7. **Check the CNI/networking layer**
         
Verify that the cluster’s CNI plugin and related components are healthy.

### **39. If there are 2 pods in different namespaces (ns1 & ns2), how can they communicate? How would you test it?**

Yes. Pods in different Kubernetes namespaces can communicate with each other by default. A namespace is a logical boundary for organizing resources, not a network boundary.

I would you test it:

**Get the Pod IPs:**
```bash  
kubectl get pod \<pod-in-ns1> -n ns1 -o wide

kubectl get pod \<pod-in-ns2> -n ns2 -o wide
```
**Test Pod-to-Pod connectivity**
```bash  
kubectl exec -it \<pod-in-ns1> -n ns1 –sh

curl http\://\<pod-ip-in-ns2>:8080
```

### **40. What is a PodDisruptionBudget?**

A **PodDisruptionBudget (PDB)** is a Kubernetes resource that limits the number of Pods of a deployment that can be down simultaneously from voluntary disruptions. It ensures application availability during: **Node maintenance, Cluster upgrades and Manual Pod deletions.**

**Key Points:**

- Specifies **minAvailable** or **maxUnavailable**
- Prevents voluntary disruptions from breaking application SLAs
- Does not protect against involuntary disruptions (node failures, crashes)

***Example:***
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```

### **41. How do you integrate Kubernetes with Jenkins or pipelines?**

I integrated Jenkins with Kubernetes using **ServiceAccount**, and **RBAC**. First, I created a ServiceAccount for Jenkins, then Configure RBAC, then I Stored Kubernetes **ServiceAccount token** in Jenkins Credentials, then I used **withKubeConfig** block in pipeline to access Kubernetes, and then I used **kubectl** commands to deploy and manage Kubernetes resources.

I also have worked with the GitOps architecture using GitHub Actions and Argo CD.

GitHub Actions handled the CI part, such as building the application, running tests, performing SonarQube and Trivy scans, building Docker image, and pushing it to a container registry. After that, GitHub Actions updates the **K8s manifest** in a Git repository with new Docker image **tag**. Argo CD was connected to that GitOps repository and continuously monitored it. When Argo CD detected the new image tag, it compared the desired state in Git with the actual state in Kubernetes and synchronized the application to the Kubernetes cluster.

### **42. What is the base configuration required to connect pipeline with EKS?**

To connect a CI/CD pipeline with EKS, we mainly need AWS credentials, an EKS cluster, kubectl, and proper IAM permissions. The pipeline needs to authenticate with AWS, get the EKS cluster credentials using aws eks update-kubeconfig, and then use kubectl to deploy the application.

### **43. What recent issue did you face in Kubernetes/EKS?**

Here are a few more **easy answers** you can use in an interview:

#### **1. Pod Issue: Pod Not Starting** 
- **Issue:** “I faced an issue where a pod was not starting.”
- **Root Cause:** There was a configuration problem, such as an incorrect environment variable or configuration value. 
- **Solution:** “I checked the pod logs and events, identified the configuration issue, fixed it, and redeployed the application.”

#### **2. ImagePullBackOff**
- **Issue:** “I faced an **`ImagePullBackOff`**** **issue in EKS.”     
- **Root Cause:** The container image name, image tag, or registry configuration was incorrect, so Kubernetes could not pull the image. 
- **Solution:** “I checked the pod events using **`kubectl describe pod`**, corrected the image or registry configuration, and redeployed the application. After that, the pod started successfully.”

### **3. CrashLoopBackOff**
- **Issue:** “One application pod was continuously restarting with **`CrashLoopBackOff`**.”    
- **Root Cause:** The application had a configuration issue and was failing during startup.     
- **Solution:** “I checked the application logs using **`kubectl logs`**, identified the configuration problem, fixed it, and restarted the deployment. After that, the pod was running normally.”

### **4. Node Resource Issue**
- **Issue:** “Some pods were not getting scheduled and remained in the Pending state.”
- **Root Cause:** The EKS worker nodes did not have enough CPU or memory resources available.
- **Solution:** “We checked the resource usage and increased the cluster capacity by adding more worker nodes or resources. After that, the pods were scheduled successfully.”

### **44. How many nodes were there in your cluster?**

“We had 3 worker nodes in our EKS cluster. We used them to run the application pods. The cluster was configured with auto-scaling, so additional nodes could be added when the workload increased.”

### **45. What was your strategy to manage/upgrade 35 nodes?**

“We used a **Canary and Rolling Update strategy with Blue/Green node groups**. Instead of upgrading all 35 nodes at once, we created a new node group with the upgraded AMI/version alongside the existing node group.

First, we moved a small number of workloads to the new nodes and monitored them. We used **taints, tolerations, and node selectors** to control which workloads ran on the new nodes.

We monitored pod startup time, application errors, latency, and queue depth. If everything was stable, we gradually moved more workloads to the new node group.

Once all workloads were running properly on the new nodes, we removed the old node group. This reduced the risk of downtime during the upgrade.”

**Easy explanation of each term**

**1. Blue/Green Node Groups**
      
- **Blue** = old node group    
- **Green** = new upgraded node group 
- Both run at the same time during the migration. 

**2.Canary**
· 
We first move a **small number of workloads** to the new nodes.
If there is no issue, we continue.

**3.Rolling Update**
· 
We gradually move workloads from old nodes to new nodes instead of doing
everything at once.

**4.Taints & Tolerations**
· 
Used to control which pods are allowed to run on specific nodes.

**5. Node Selectors**
· 
Used to tell Kubernetes: “Run this particular workload on these new nodes.”

**6. Monitoring**

We check:
      
- Pod startup time 
- Error rate       
- Application latency      
- Queue depth     
- CPU/memory usage
  
---
## 🟠 **Frequently Asked Commands**

| Command | Description |
| --- | --- |
| `kubectl get pods` | List Pods |
| `kubectl get pods -A` | List Pods across all namespaces |
| `kubectl get deployments` | List Deployments |
| `kubectl get services` | List Services |
| `kubectl get nodes` | List Nodes |
| `kubectl get namespaces` | List Namespaces |
| `kubectl describe pod <pod-name>` | Show detailed Pod information |
| `kubectl describe node <node-name>` | Show detailed Node information |
| `kubectl logs <pod-name>` | View Pod logs |
| `kubectl logs <pod-name> --previous` | View logs from the previous container instance |
| `kubectl exec -it <pod-name> -- /bin/sh` | Open a shell inside a running container |
| `kubectl apply -f app.yaml` | Create or update resources from a manifest |
| `kubectl delete -f app.yaml` | Delete resources defined in a manifest |
| `kubectl scale deployment app --replicas=3` | Scale a Deployment |
| `kubectl rollout status deployment/app` | Check rollout status |
| `kubectl rollout history deployment/app` | View Deployment rollout history |
| `kubectl rollout undo deployment/app` | Roll back a Deployment |
| `kubectl top pods` | Display Pod resource usage |
| `kubectl top nodes` | Display Node resource usage |
| `kubectl get events` | View cluster events |
| `kubectl get events --sort-by=.lastTimestamp` | Sort events by timestamp |
| `kubectl get namespaces` | List all Namespaces |
| `kubectl get svc` | List Services |
| `kubectl get endpointslices` | List EndpointSlices |
| `kubectl get networkpolicies` | List NetworkPolicies |
---

---

## 🟠 **Additional Resources**

·      
[*Kubernetes Official Documentation*](https://kubernetes.io/docs/)

·      
[*Kubernetes GitHub Repository*](https://github.com/kubernetes/kubernetes)

·      
[*kubectl Cheat Sheet*](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

---

## 🟠 **Final Interview Checklist**

Before
attending a Kubernetes interview, make sure you can confidently explain:

☐       
Kubernetes architecture

☐       
Control plane components

☐       
Worker node components

☐       
Pods and Pod lifecycle

☐       
Deployments and ReplicaSets

☐       
StatefulSets and DaemonSets

☐       
Jobs and CronJobs

☐       
Services and service types

☐       
Ingress

☐       
ConfigMaps and Secrets

☐       
Labels and selectors

☐       
PV, PVC, and StorageClass

☐       
Resource requests and limits

☐       
Liveness, readiness, and
startup probes

☐       
Taints and tolerations

☐       
Node affinity and pod affinity

☐       
Kubernetes networking and DNS

☐       
NetworkPolicies

☐       
Troubleshooting Pending Pods

☐       
Troubleshooting CrashLoopBackOff

☐       
Troubleshooting image pull
failures

☐       
Rolling updates and rollbacks

☐       
Common kubectl commands

☐       
YAML manifest structure

☐       
Imperative vs. declarative
management
