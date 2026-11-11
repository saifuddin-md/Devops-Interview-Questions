# Kubernetes Troubleshooting Guide

A practical, command-focused guide to diagnosing and resolving common Kubernetes workload issues:

- `CrashLoopBackOff`
- `ImagePullBackOff` / `ErrImagePull`
- Pod scheduling and startup failures
- CPU and memory problems, including `OOMKilled`

> **Audience:** DevOps engineers, SREs, platform engineers, and Kubernetes learners.  
> **Scope:** The examples use `kubectl` and standard Kubernetes resources. Adapt names, namespaces, and resource values to your cluster.

---

## Table of contents

1. [Start with a troubleshooting workflow](#1-start-with-a-troubleshooting-workflow)
2. [CrashLoopBackOff](#2-crashloopbackoff)
3. [ImagePullBackOff and ErrImagePull](#3-imagepullbackoff-and-errimagepull)
4. [Pod failures and Pending pods](#4-pod-failures-and-pending-pods)
5. [CPU and memory issues](#5-cpu-and-memory-issues)
6. [Useful troubleshooting commands](#6-useful-troubleshooting-commands)
7. [End-to-end production-style example](#7-end-to-end-production-style-example)
8. [Interview questions](#8-interview-questions)
9. [Operational checklist](#9-operational-checklist)

---

## 1. Start with a troubleshooting workflow

When an application is reported as unavailable, avoid guessing or immediately deleting pods. Gather evidence first.

```bash
# Check pods in the current namespace
kubectl get pods

# Check pods in every namespace
kubectl get pods -A

# Include node placement and pod IP
kubectl get pods -o wide

# Inspect recent cluster events
kubectl get events --sort-by=.metadata.creationTimestamp
```

A useful first-response sequence is:

1. Identify the affected namespace, workload, and pod.
2. Check pod status and restart count.
3. Describe the pod and inspect its Events and container state.
4. Read current or previous container logs.
5. Check related Deployment, ConfigMap, Secret, Service, PVC, and Node resources.
6. Make the smallest safe correction.
7. Verify pod readiness and application health.

Set a namespace for commands in the examples:

```bash
kubectl config set-context --current --namespace=production
```

This changes the namespace used by subsequent commands in the current kubectl context. You can instead add `-n production` to individual commands.

## 2. CrashLoopBackOff

### What it means

`CrashLoopBackOff` means a container repeatedly exits or is terminated, and Kubernetes is delaying the next restart attempt. The status is a symptom, not the root cause.

Typical causes include:

- Application startup errors or a non-zero exit code
- Incorrect command or arguments
- Missing or invalid environment variables
- Missing configuration files or mounted secrets
- Dependency failures, such as database connectivity
- Liveness probe failures
- Memory exhaustion (`OOMKilled`)

### Troubleshooting steps

**1. Find the affected pod and restart count**

```bash
kubectl get pods -n production
```

**2. Inspect container state, exit code, probes, and events**

```bash
kubectl describe pod <pod-name> -n production
```

Look for `Last State`, `Reason`, `Exit Code`, probe failures, and the `Events` section.

**3. Read current logs**

```bash
kubectl logs <pod-name> -n production
```

For a pod with multiple containers:

```bash
kubectl logs <pod-name> -c <container-name> -n production
```

**4. Read logs from the previous crashed container**

```bash
kubectl logs <pod-name> --previous -n production
```

For a specific container:

```bash
kubectl logs <pod-name> -c <container-name> --previous -n production
```

**5. Inspect the owning workload**

```bash
kubectl get pod <pod-name> -n production \
  -o jsonpath='{.metadata.ownerReferences}'

kubectl describe deployment <deployment-name> -n production
```

### Common fixes

| Evidence | Possible action |
|---|---|
| Missing environment variable | Correct the Deployment's `env` or `envFrom` configuration. |
| Config file not found | Check ConfigMap/Secret name, key, mount path, and file permissions. |
| Database connection refused | Verify DNS, Service/endpoints, network policy, port, and credentials. |
| Liveness probe repeatedly fails | Check the health endpoint and timing; tune probe settings only when justified. |
| Wrong startup command | Correct the container `command` or `args`. |
| `OOMKilled` | Follow the memory troubleshooting section; inspect memory use and possible leaks. |

Do not simply increase restart limits: Kubernetes does not provide a general pod restart-limit setting that fixes an application crash.

## 3. ImagePullBackOff and ErrImagePull

### What they mean

- `ErrImagePull`: Kubernetes tried to pull an image and the pull failed.
- `ImagePullBackOff`: Kubernetes is backing off before retrying the failed pull.

### Common causes

- Typo in the image repository or tag
- Image or tag does not exist
- Private registry credentials are missing or invalid
- Image pull secret is in a different namespace
- Registry DNS, network, TLS, or rate-limit issue
- Node cannot reach the registry

### Troubleshooting steps

**1. Inspect the pod events**

```bash
kubectl describe pod <pod-name> -n production
```

Read the exact registry error in the Events section.

**2. Check the image configured in the Deployment**

```bash
kubectl get deployment <deployment-name> -n production \
  -o jsonpath='{.spec.template.spec.containers[*].image}'

kubectl describe deployment <deployment-name> -n production
```

**3. Correct an incorrect image reference**

```bash
kubectl set image deployment/<deployment-name> \
  <container-name>=<repository>/<image>:<valid-tag> \
  -n production

kubectl rollout status deployment/<deployment-name> -n production
```

Use an image repository and tag that actually exist and that your cluster is authorized to access.

### Private registry: image pull secret

Create a Docker registry secret in the **same namespace as the pod**:

```bash
kubectl create secret docker-registry regcred \
  --docker-server=<registry-server> \
  --docker-username=<username> \
  --docker-password=<password> \
  --docker-email=<email> \
  -n production
```

Reference it in the pod template:

```yaml
spec:
  imagePullSecrets:
    - name: regcred
  containers:
    - name: app
      image: <registry>/<repository>:<tag>
```

For a Deployment, this belongs under `spec.template.spec`. Do not put the secret under the Deployment's top-level `spec`.

**Security note:** Avoid committing credentials to Git or exposing them in shell history, CI logs, or tickets. Prefer a managed secrets solution or a secure CI/CD process. Restrict secret access with RBAC and rotate credentials when needed.

## 4. Pod failures and Pending pods

A pod can be unable to schedule, create its containers, or become ready. The exact status and Events help distinguish these cases.

### Common states

| Status / reason | What it generally indicates |
|---|---|
| `Pending` | Pod has not completed scheduling or setup. |
| `ContainerCreating` | Kubelet is preparing the container, mounts, or sandbox. |
| `CrashLoopBackOff` | Container repeatedly exits; see section 2. |
| `ImagePullBackOff` | Image cannot be pulled; see section 3. |
| `Running` | Pod is bound to a node and containers are running; it does not guarantee readiness or application health. |
| `Completed` | Containers finished successfully, often expected for Jobs. |
| `OOMKilled` | A container was killed after exceeding available/allowed memory. |

### Troubleshooting a Pending pod

**1. Describe it**

```bash
kubectl describe pod <pod-name> -n production
```

**2. Check scheduling events**

Examples include:

- `Insufficient cpu` or `Insufficient memory`
- Untolerated node taint
- Node selector or node affinity mismatch
- Unbound PersistentVolumeClaim
- Pod topology or volume zone constraints

**3. Check nodes and capacity**

```bash
kubectl get nodes
kubectl describe node <node-name>
kubectl top nodes
```

`kubectl top` requires Metrics Server or a compatible metrics API.

**4. Check storage**

```bash
kubectl get pvc -n production
kubectl describe pvc <pvc-name> -n production
kubectl get pv
```

### Common fixes

- Add node capacity or scale the cluster when capacity is genuinely insufficient.
- Right-size CPU and memory requests based on observed usage.
- Correct node selectors, affinity rules, and tolerations.
- Resolve PVC binding, StorageClass, access mode, or zone issues.
- Investigate `ContainerCreating` events for image, volume mount, CNI, or sandbox errors.

### Check node health

```bash
kubectl get nodes
kubectl describe node <node-name>
kubectl get events -A --sort-by=.metadata.creationTimestamp
```

Investigate node conditions such as `Ready`, `MemoryPressure`, `DiskPressure`, and `PIDPressure`.

## 5. CPU and memory issues

Kubernetes uses **requests** for scheduling and **limits** to constrain container resource use.

- CPU requests influence scheduling; CPU limits impose a CPU quota and can cause throttling.
- Memory requests influence scheduling; exceeding a container's memory limit can result in an OOM kill.
- A pod can be `Running` while its application is slow or failing readiness checks.

### Check resource usage

```bash
kubectl top pods -n production
kubectl top pods -n production --containers
kubectl top nodes
```

These commands require Metrics Server or a compatible metrics API. They show current usage, not historical peaks.

### CPU throttling

Symptoms may include slow response times, timeouts, or failed health checks. Check application metrics and container CPU usage, then review CPU requests and limits.

Example resource configuration:

```yaml
resources:
  requests:
    cpu: "500m"
    memory: "256Mi"
  limits:
    cpu: "1"
    memory: "512Mi"
```

- `500m` CPU means half a CPU core.
- `1` CPU means one CPU core.
- `Mi` is a binary memory unit (mebibyte).

These values are examples only. Set them based on workload measurements and capacity.

### OOMKilled / memory exhaustion

**1. Inspect the pod**

```bash
kubectl describe pod <pod-name> -n production
```

Look for a container's `Last State: Terminated`, `Reason: OOMKilled`, and exit code (often `137`).

**2. Check current usage**

```bash
kubectl top pod <pod-name> -n production --containers
```

Current usage may not show the peak that caused the kill. Use monitoring history when available.

**3. Investigate before changing limits**

- Check for memory leaks or unbounded caches.
- Review JVM or other runtime memory settings.
- Check workload concurrency and input sizes.
- Compare requests and limits with actual usage and node capacity.

**4. Adjust resources when evidence supports it**

Update the Deployment's resource settings in source-controlled manifests or Helm values, then apply through the normal release process:

```bash
kubectl apply -f deployment.yaml
kubectl rollout status deployment/<deployment-name> -n production
```

Increasing a memory limit without checking node capacity can move the problem to the node or cause scheduling pressure.

## 6. Useful troubleshooting commands

| Command | Purpose |
|---|---|
| `kubectl get pods` | List pods in the current namespace. |
| `kubectl get pods -A` | List pods across namespaces. |
| `kubectl get pods -o wide` | Include node and IP information. |
| `kubectl describe pod <pod>` | Inspect pod state, configuration, and events. |
| `kubectl logs <pod>` | Read container logs. |
| `kubectl logs <pod> --previous` | Read the previous container instance's logs. |
| `kubectl logs <pod> -c <container>` | Select a container in a multi-container pod. |
| `kubectl get events --sort-by=.metadata.creationTimestamp` | Review recent events in the current namespace. |
| `kubectl get events -A --sort-by=.metadata.creationTimestamp` | Review events across namespaces. |
| `kubectl get nodes` | Check node readiness. |
| `kubectl describe node <node>` | Inspect node conditions, capacity, and events. |
| `kubectl top pods` | View current pod resource usage (metrics required). |
| `kubectl top nodes` | View current node resource usage (metrics required). |
| `kubectl get deployments` | List Deployments. |
| `kubectl rollout status deployment/<name>` | Wait for a Deployment rollout. |
| `kubectl rollout history deployment/<name>` | Inspect rollout history. |
| `kubectl get pvc` | List PersistentVolumeClaims. |
| `kubectl get services` | List Services. |
| `kubectl get endpointslices` | Inspect EndpointSlices for Services. |

For a specific namespace, append `-n <namespace>` to namespaced commands. Use `-A` only where supported.

## 7. End-to-end production-style example

Suppose a team reports that three application pods are unhealthy.

```bash
kubectl get pods -n production
```

Example output:

```text
NAME       READY   STATUS             RESTARTS
app-1      0/1     CrashLoopBackOff   5
app-2      0/1     ImagePullBackOff   0
app-3      0/1     Pending            0
```

### Pod 1: CrashLoopBackOff

```bash
kubectl logs app-1 --previous -n production
kubectl describe pod app-1 -n production
```

Suppose logs show a database connection error.

Investigate database DNS, Service endpoints, network policies, port, and credentials. Do not assume the database password is wrong without evidence.

### Pod 2: ImagePullBackOff

```bash
kubectl describe pod app-2 -n production
```

Suppose Events show that the requested image tag does not exist.

Correct the image reference in the Deployment's source manifest or release configuration, then deploy the corrected version.

### Pod 3: Pending

```bash
kubectl describe pod app-3 -n production
```

Suppose Events show `Insufficient memory`.

Review node allocatable memory and pod requests:

```bash
kubectl describe nodes
kubectl top nodes
kubectl get pod app-3 -n production -o yaml
```

Right-size requests if they are inaccurate, or add cluster capacity if the workload genuinely needs it.

### Verify recovery

```bash
kubectl get pods -n production -w
kubectl rollout status deployment/<deployment-name> -n production
kubectl get events -n production --sort-by=.metadata.creationTimestamp
```

Then verify application health using the team's health endpoint, synthetic checks, or monitoring dashboards. A `Running` pod alone does not prove that the service is healthy.

## 8. Interview questions

**Q1. What is CrashLoopBackOff?**  
A: It is a status indicating that a container repeatedly exits and Kubernetes is delaying its next restart. Check `kubectl describe pod` and `kubectl logs --previous` to find the cause.

**Q2. What is the difference between ErrImagePull and ImagePullBackOff?**  
A: `ErrImagePull` is the failed image-pull attempt. `ImagePullBackOff` indicates Kubernetes is waiting before retrying.

**Q3. What does OOMKilled mean?**  
A: The container was killed because it exceeded available or permitted memory. Inspect termination state, resource settings, and historical memory metrics.

**Q4. How do you troubleshoot a Pending pod?**  
A: Describe it and inspect scheduling events. Check resource requests, node capacity, taints, affinity, and PVC binding.

**Q5. How do you check logs from a crashed container?**  
A: `kubectl logs <pod-name> --previous -n <namespace>`. Add `-c <container-name>` for multi-container pods.

**Q6. What is the difference between requests and limits?**  
A: Requests are used primarily for scheduling and resource guarantees. Limits cap resource use; CPU limits can throttle and memory limits can lead to OOM kills.

**Q7. Does Running mean the application is healthy?**  
A: No. Check readiness, application health, Service endpoints, and monitoring. A running container may not be ready to receive traffic.

**Q8. What is the difference between readiness and liveness probes?**  
A: Readiness determines whether a pod should receive Service traffic. Liveness determines whether a container should be restarted.

## 9. Operational checklist

Use this checklist during an incident:

- [ ] Confirm the affected cluster, namespace, workload, and impact.
- [ ] Check pod status, restart counts, and node placement.
- [ ] Describe affected pods and inspect Events.
- [ ] Check current and previous container logs.
- [ ] Inspect Deployment configuration and recent changes.
- [ ] Check ConfigMaps, Secrets, image references, and pull secrets.
- [ ] Check node health, CPU, memory, and storage.
- [ ] Check PVCs, Services, EndpointSlices, and network policies when relevant.
- [ ] Apply the smallest safe fix through the normal change process.
- [ ] Verify rollout, readiness, and actual application health.
- [ ] Record the root cause and preventive action.

---

## Quick reference: symptom to first command

| Symptom | Start here |
|---|---|
| `CrashLoopBackOff` | `kubectl logs <pod> --previous -n <namespace>` |
| `ImagePullBackOff` | `kubectl describe pod <pod> -n <namespace>` |
| `Pending` | `kubectl describe pod <pod> -n <namespace>` |
| `OOMKilled` | `kubectl describe pod <pod> -n <namespace>` |
| Node `NotReady` | `kubectl describe node <node>` |
| High CPU or memory | `kubectl top pods -n <namespace>` |
| Deployment rollout issue | `kubectl rollout status deployment/<name> -n <namespace>` |
| Storage issue | `kubectl describe pvc <pvc> -n <namespace>` |

**Remember:** `get` to identify, `describe` to inspect events and state, `logs` to understand application behavior, then fix and verify.
