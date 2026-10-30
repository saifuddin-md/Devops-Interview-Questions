# Top 20 Helm Interview Questions and Answers

A concise interview-preparation guide for DevOps, Kubernetes, and Cloud
Engineer roles.

## Basic Helm Questions

### 1. What is Helm in Kubernetes?

Helm is a package manager for Kubernetes. It helps deploy, manage,
upgrade, and roll back applications using reusable packages called Helm
Charts.

### 2. What is a Helm Chart?

A Helm Chart is a collection of files containing templates and
configuration required to deploy Kubernetes resources such as
Deployments, Services, ConfigMaps, and Ingress resources.

### 3. What are the main components of Helm?

-   **Helm CLI:** Command-line tool used to work with Helm.
-   **Chart:** Package containing templates and configuration.
-   **Release:** An installed instance of a chart.
-   **Repository:** A location where charts are stored and distributed.
-   **values.yaml:** Default configuration values.
-   **templates/:** Directory containing Kubernetes manifest templates.

### 4. What is the difference between Helm 2 and Helm 3?

| Helm 2 | Helm 3 |
|---|---|
| Uses Tiller | Does not use Tiller |
| Client-server architecture | Client-only architecture |
| More complex RBAC setup | Uses Kubernetes RBAC directly |
| ConfigMaps commonly used for release storage | Secrets used by default for release storage |

### 5. What is the purpose of values.yaml?

The `values.yaml` file stores default configuration values used by chart
templates.

Example:

``` yaml
replicaCount: 2

image:
  repository: nginx
  tag: "latest"

service:
  type: ClusterIP
  port: 80
```

### 6. What is the difference between Chart.yaml and values.yaml?

-   `Chart.yaml` stores chart metadata, such as the chart name, version,
    and description.
-   `values.yaml` stores configurable values used by the chart
    templates.

### 7. What is a Helm Release?

A Helm Release is an installed instance of a Helm Chart. The same chart
can be installed multiple times using different release names.

Example:

``` bash
helm install myapp ./mychart
```

Here, `myapp` is the release name.

## Intermediate Helm Questions

### 8. How do you install a Helm Chart?

``` bash
helm install myapp ./mychart
```

To install it into a specific namespace and create that namespace if
necessary:

``` bash
helm install myapp ./mychart -n dev --create-namespace
```

### 9. What is the difference between `helm install` and `helm upgrade`?

-   `helm install` creates a new release.
-   `helm upgrade` updates an existing release.

Example:

``` bash
helm upgrade myapp ./mychart
```

### 10. What is `helm upgrade --install`?

It installs a release if it does not exist and upgrades it if it already
exists. This is commonly used in CI/CD pipelines.

``` bash
helm upgrade --install myapp ./mychart
```

### 11. How do you roll back a Helm deployment?

First, check the release history:

``` bash
helm history myapp
```

Then roll back to a specific revision:

``` bash
helm rollback myapp 1
```

Here, `1` is the target revision number.

### 12. How do you check installed Helm releases?

List releases in the current namespace:

``` bash
helm list
```

List releases across all namespaces:

``` bash
helm list -A
```

### 13. What is the difference between `--set` and `-f` in Helm?

-   `--set` passes configuration values directly on the command line.
-   `-f` loads configuration values from a YAML file.

Examples:

``` bash
helm install myapp ./mychart --set replicaCount=3
```

``` bash
helm install myapp ./mychart -f prod-values.yaml
```

### 14. What are `helm lint` and `helm template` used for?

-   `helm lint` checks a chart for common errors and potential issues.
-   `helm template` renders the chart templates into Kubernetes YAML
    without installing the release.

``` bash
helm lint ./mychart

helm template myapp ./mychart
```

## Advanced Helm Questions

### 15. What are Helm Hooks?

Helm Hooks let a chart run Kubernetes resources at specific points in a
release lifecycle.

Common hooks include:

-   `pre-install`
-   `post-install`
-   `pre-upgrade`
-   `post-upgrade`
-   `pre-delete`
-   `post-delete`
-   `test`

Example annotation:

``` yaml
metadata:
  annotations:
    "helm.sh/hook": pre-upgrade
```

### 16. What are Helm Dependencies?

Dependencies are other charts required by a parent chart. They are
declared in the parent chart's `Chart.yaml`.

Example:

``` yaml
dependencies:
  - name: redis
    version: "20.6.0"
    repository: https://charts.example.com
```

Download or update dependencies with:

``` bash
helm dependency update
```

Use the repository configured for the chart dependency in a real
project.

### 17. How do you manage multiple environments using Helm?

Use one chart with separate values files for each environment.

Example directory structure:

``` text
mychart/
├── Chart.yaml
├── values.yaml
├── values-dev.yaml
├── values-qa.yaml
└── values-prod.yaml
```

Deploy to each environment:

``` bash
helm upgrade --install myapp ./mychart -f values-dev.yaml -n dev

helm upgrade --install myapp ./mychart -f values-qa.yaml -n qa

helm upgrade --install myapp ./mychart -f values-prod.yaml -n prod
```

### 18. How do you manage secrets in Helm?

Avoid committing plaintext passwords, tokens, or other sensitive values
to `values.yaml` or source control.

Common approaches include:

-   Kubernetes Secrets, with appropriate access controls and encryption
    at rest.
-   HashiCorp Vault.
-   Sealed Secrets.
-   External Secrets Operator.
-   SOPS-encrypted files.

### 19. How do you troubleshoot a failed Helm deployment?

Start by inspecting the release:

``` bash
helm status myapp -n dev
helm history myapp -n dev
```

Then inspect Kubernetes resources and events:

``` bash
kubectl get pods -n dev
kubectl describe pod <pod-name> -n dev
kubectl logs <pod-name> -n dev
kubectl get events -n dev --sort-by=.metadata.creationTimestamp
```

Check the rendered manifests and values if needed:

``` bash
helm get values myapp -n dev
helm get manifest myapp -n dev
```

Identify the root cause, correct the configuration or application issue,
and upgrade or roll back the release as appropriate.

### 20. How do you deploy Helm Charts through a Jenkins CI/CD pipeline?

A typical pipeline follows these steps:

1.  Check out source code from Git.
2.  Build the Docker image.
3.  Push the image to a container registry.
4.  Set the image repository and tag for the Helm deployment.
5.  Validate the chart.
6.  Deploy or upgrade the release.
7.  Verify the rollout.

Example shell commands:

``` bash
helm lint ./mychart

helm upgrade --install myapp ./mychart \
  --namespace dev \
  --create-namespace \
  --set image.repository=myrepo/myapp \
  --set image.tag="$BUILD_NUMBER" \
  --wait
```

The Jenkins agent must have Helm installed and valid Kubernetes
credentials. In production, use protected credentials and a controlled
image-tagging strategy.

------------------------------------------------------------------------

## Quick Helm Command Reference

  Command                    Purpose
  -------------------------- ---------------------------------------
  `helm version`             Check Helm version
  `helm list -A`             List releases across namespaces
  `helm repo list`           List configured chart repositories
  `helm repo update`         Refresh repository indexes
  `helm search repo nginx`   Search chart repositories
  `helm install`             Install a release
  `helm upgrade`             Upgrade a release
  `helm upgrade --install`   Install or upgrade a release
  `helm rollback`            Roll back a release
  `helm uninstall`           Uninstall a release
  `helm status`              View release status
  `helm history`             View release revisions
  `helm lint`                Check a chart
  `helm template`            Render chart templates locally
  `helm get values`          View configured release values
  `helm get manifest`        View the release's stored manifests
  `helm dependency update`   Resolve and update chart dependencies
  `helm test`                Run chart tests

## Interview Preparation Tips

Focus on explaining and demonstrating these practical tasks:

-   Create a basic Helm chart.
-   Deploy an Nginx application with Helm.
-   Change the replica count using a values file.
-   Upgrade the container image tag.
-   Deploy the same chart to dev, QA, and production.
-   Inspect a failed deployment and diagnose pod issues.
-   Roll back to a previous release revision.
-   Explain how Helm fits into a Jenkins CI/CD pipeline.

**Tip:** Understand what each command does and when to use it.
Interviewers often ask follow-up questions about deployments, upgrades,
rollbacks, values files, CI/CD integration, and troubleshooting.
