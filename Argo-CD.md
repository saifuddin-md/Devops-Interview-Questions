### Core Concepts & Architecture (Q1–Q10)

**1. What is ArgoCD and why is it used in Kubernetes environments?**

ArgoCD is a declarative, GitOps continuous delivery tool for Kubernetes. It is used because it treats Git repositories as the single source of truth for defining the desired state of applications. It continuously monitors running applications and compares their live state against the desired state specified in Git, automatically or manually syncing them. It solves the problem of configuration drift and provides a clear audit trail for deployments.

**2. How does ArgoCD implement GitOps principles?**

GitOps relies on four principles: 1) Declarative configuration, 2) Versioned and immutable storage (Git), 3) Pulled automatically, and 4) Continuous reconciliation. ArgoCD implements this by storing Kubernetes manifests in Git. The ArgoCD controller (running inside the cluster) *pulls* these manifests, compares them to the cluster state, and reconciles any differences, ensuring the cluster always matches Git.

**3. What are the core components of ArgoCD architecture?**

*   **API Server:** Exposes the API for the Web UI, CLI, and CI/CD systems. Handles authentication and RBAC.
*   **Repository Server:** Maintains a local cache of the Git repository, generates Kubernetes manifests (using Helm, Kustomize, etc.), and returns them to the Application Controller.
*   **Application Controller:** The core "brain." It continuously monitors the live state of applications and compares it to the desired state in Git. It detects drift and initiates sync operations.
*   **Redis:** Used for caching.
*   **Dex (Optional):** An identity provider for SSO.
*   **Notifications Controller (Optional):** Sends alerts based on ArgoCD events.

**4. Explain the difference between desired state and live state in ArgoCD.**

*   **Desired State:** The configuration defined in the Git repository (e.g., a Deployment with 3 replicas). This is what you *want* the cluster to look like.
*   **Live State:** The actual current configuration of the resources running in the Kubernetes cluster (e.g., a Deployment currently running 2 replicas because one crashed). 
ArgoCD's job is to detect the difference (drift) and make the Live State match the Desired State.

**5. What is an ArgoCD Application?**

An Application is a Custom Resource Definition (CRD) in ArgoCD. It acts as a mapping between a source (a Git repository path, Helm chart, etc.) and a destination (a specific Kubernetes cluster and namespace). It defines *what* to deploy and *where* to deploy it.

**6. What are ArgoCD Projects and their use cases?**

AppProjects (Projects) provide a logical grouping of Applications. They are used for multi-tenancy and security. Use cases include restricting which Git repositories a team can deploy from, limiting which clusters and namespaces they can deploy to, and defining specific RBAC roles for that project.

**7. How do you configure sync policies (Manual vs Automated) in ArgoCD?**

Sync policies are configured in the Application YAML manifest under `spec.syncPolicy`.
*   **Manual:** The default. ArgoCD detects drift but does not fix it until a user clicks "Sync" in the UI or runs the CLI command.
*   **Automated:** ArgoCD automatically applies changes when it detects drift. It can be configured with `prune: true` (delete resources no longer in Git) and `selfHeal: true` (revert manual changes made directly to the cluster).

**8. What is the Application Controller responsible for?**

It is a Kubernetes controller that continuously watches the state of ArgoCD Applications. It polls the Git repository (via the Repo Server), compares the manifests to the live cluster state, and executes the sync process if automated sync is enabled or requested by a user. It also updates the health and sync status of the Application.

**9. What role does the Repo Server play in ArgoCD?**

The Repo Server is an internal service that handles all interaction with Git. It clones the repository, checks out the specific revision, and generates the final Kubernetes manifests. It supports plain YAML, Helm, Kustomize, Jsonnet, and custom plugins. It caches these manifests to improve performance.

**10. How does ArgoCD detect and handle configuration drift?**

ArgoCD detects drift by periodically polling the Kubernetes API for the live state of resources and comparing it to the desired state generated from Git. If a difference is found, the Application status becomes `OutOfSync`. If automated sync with `selfHeal` is enabled, ArgoCD will immediately apply the Git state to overwrite the manual change. If not, it waits for manual intervention.

---

### Lifecycle & Deployment Strategies (Q11–Q15)

**11. What are the different health statuses in ArgoCD?**

*   **Healthy:** The resource is functioning correctly.
*   **Progressing:** The resource is not yet healthy but is moving towards a healthy state (e.g., a Deployment rolling out).
*   **Degraded:** The resource has failed (e.g., a Pod crash-looping).
*   **Suspended:** The resource is paused (e.g., a CronJob).
*   **Missing:** The resource does not exist in the cluster.
*   **Unknown:** ArgoCD cannot determine the health status.

**12. How does ArgoCD handle rollback during failures?**

ArgoCD keeps a history of all previous syncs. If a deployment fails, you can use the UI or CLI (`argocd app rollback <app-name> <revision>`) to revert the application to a previous Git commit. This triggers a sync of the older manifests, restoring the cluster to the previous working state.

**13. How does ArgoCD support multi-cluster deployments?**

ArgoCD can manage multiple clusters from a single control plane. You register external clusters by adding their API server credentials to ArgoCD (using `argocd cluster add`). When creating an Application, you specify the destination `server` (the target cluster's API URL) and `namespace`. ArgoCD then deploys the manifests to that specific external cluster.

**14. How can you integrate ArgoCD with Helm charts?**

ArgoCD has native Helm support. In the Application manifest, you set the `source` to the Git repo containing the Helm chart, specify the `path` to the chart, and provide `helm.values` or `helm.valueFiles` to override default values. ArgoCD runs `helm template` under the hood to generate the manifests.

**15. How does ArgoCD work with Kustomize?**

Similar to Helm, ArgoCD has native Kustomize support. You point the Application `source` to a directory containing a `kustomization.yaml` file. ArgoCD runs `kustomize build` to render the final manifests. You can also pass Kustomize-specific parameters like `namePrefix` or `images` directly in the Application spec.

---

### Security & Access Control (Q16–Q19)

**16. How do you manage secrets securely in ArgoCD?**

You should **never** store plain-text secrets in Git. Common methods include:
*   **Sealed Secrets:** Encrypt secrets so only the cluster can decrypt them.
*   **External Secrets Operator:** Sync secrets from external vaults (AWS Secrets Manager, HashiCorp Vault) into Kubernetes.
*   **SOPS:** Encrypt secrets in Git and use a plugin to decrypt them during the ArgoCD sync process.
*   **ArgoCD Vault Plugin (AVP):** Replaces placeholders in manifests with secrets from Vault.

**17. What is the difference between ArgoCD CLI, Web UI, and API?**

*   **Web UI:** A graphical interface for visualizing applications, checking health, and manual syncing.
*   **CLI (`argocd`):** A command-line tool for scripting, automation, and managing ArgoCD from a terminal.
*   **API:** A REST/gRPC API that the UI and CLI use under the hood. It allows you to programmatically manage ArgoCD (e.g., from a CI/CD pipeline).

**18. How do you implement RBAC in ArgoCD?**

RBAC is configured via a ConfigMap (`argocd-rbac-cm`). You define roles and bind them to users or groups (from SSO). Policies use a syntax like `p, <role/user/group>, <resource>, <action>, <object>`. For example: `p, role:developer, applications, sync, default/*` allows developers to sync apps in the default project.

**19. How can you integrate ArgoCD with SSO providers?**

ArgoCD uses **Dex** (an identity broker) or can directly integrate with OIDC providers (like Okta, Azure AD, GitHub, Google). You configure the provider details in the `argocd-cm` ConfigMap. Once configured, users log in via the provider, and ArgoCD maps their group memberships to RBAC roles.

---

### Advanced Features & Integrations (Q20–Q25)

**20. How does ArgoCD handle progressive delivery (blue-green / canary)?**

ArgoCD itself is a sync tool, not a progressive delivery controller. To achieve blue-green or canary, you integrate ArgoCD with **Argo Rollouts**. ArgoCD deploys the Rollout CRD, and Argo Rollouts manages the gradual traffic shifting and promotion based on metrics.

**21. What are ArgoCD Notifications and how are they configured?**

ArgoCD Notifications is a controller that continuously monitors ArgoCD applications and triggers notifications based on events (e.g., sync succeeded, health degraded). It is configured via ConfigMaps (`argocd-notifications-cm`) where you define triggers, templates, and subscriptions.

**22. How can you integrate ArgoCD with Slack, Teams, or Email alerts?**

This is done through the ArgoCD Notifications controller. You configure the notification service (e.g., Slack webhook URL) in the `argocd-notifications-secret`, define a message template in the ConfigMap, and subscribe specific applications to the trigger.

**23. How does ArgoCD compare with FluxCD?**

*   **ArgoCD:** Centralized control plane with a powerful Web UI. Best for teams that want a visual dashboard and multi-cluster management from a single pane of glass. Uses a "pull" model.
*   **FluxCD:** Decentralized, lightweight, and CLI-first. It is a set of controllers that run in the cluster. It is often considered more "GitOps-native" because it doesn't require a central UI, but it has a steeper learning curve for visualization.

**24. What scaling strategies can you use for ArgoCD in enterprise setups?**

*   **Sharding:** Distribute the load of the Application Controller across multiple replicas by sharding clusters.
*   **HA (High Availability):** Run multiple replicas of the API Server, Repo Server, and Controller.
*   **Repo Server Caching:** Ensure Redis is properly sized to cache generated manifests.
*   **Webhook Configuration:** Use Git webhooks instead of polling to reduce load on the Repo Server.

**25. How do you monitor ArgoCD using Prometheus & Grafana?**

ArgoCD exposes Prometheus metrics on its components (API Server, Controller, Repo Server). You configure Prometheus to scrape these endpoints. Grafana can then visualize metrics like `argocd_app_sync_total`, `argocd_app_health_status`, and `argocd_git_request_duration_seconds`. Pre-built Grafana dashboards are available in the ArgoCD community.

---

### Patterns, Challenges & Best Practices (Q26–Q30)

**26. What are ArgoCD App-of-Apps patterns and when should you use them?**

The App-of-Apps pattern uses a single "parent" ArgoCD Application that points to a Git directory containing multiple "child" Application manifests. When the parent syncs, it creates the child applications. Use this when you need to bootstrap an entire cluster or manage a large set of microservices as a single unit.

**27. What are the common challenges faced with ArgoCD in production?**

*   **Secret Management:** Handling sensitive data securely without committing plain text.
*   **Repo Server Performance:** Slowness when generating manifests for large Helm charts or many apps.
*   **Drift Detection Latency:** The default polling interval (3 minutes) can be too slow for critical apps; webhooks are needed.
*   **RBAC Complexity:** Managing granular permissions across many teams and clusters.
*   **Cluster Management:** Onboarding and offboarding clusters securely.

**28. How can ArgoCD be integrated with CI pipelines (e.g., Jenkins, GitHub Actions)?**

In a GitOps workflow, CI is responsible for building the image and updating the Git repository (e.g., updating the image tag in a Helm values file). The CI pipeline then triggers ArgoCD via the CLI (`argocd app sync`) or API to deploy the change. Alternatively, ArgoCD can be configured to auto-sync on Git commit.

**29. What are ArgoCD best practices for large-scale deployments?**

*   Use **App-of-Apps** or **ApplicationSets** for managing many apps.
*   Enable **Auto-Sync** and **Self-Heal** for true GitOps.
*   Use **SSO** and strict **RBAC**.
*   Implement **Sharding** for the Application Controller.
*   Use **Webhooks** instead of polling to reduce load.
*   Keep manifests small and use **Kustomize/Helm** for reusability.
*   Monitor ArgoCD itself with Prometheus.

**30. What's the difference between ArgoCD and Argo Workflows?**

*   **ArgoCD:** A **Continuous Delivery** tool. It ensures the cluster state matches the Git state (declarative, long-running reconciliation).
*   **Argo Workflows:** A **Workflow Engine** for Kubernetes. It is used to orchestrate parallel jobs, DAGs (Directed Acyclic Graphs), and CI/CD pipelines (imperative, run-to-completion). 
They are complementary: you can use Argo Workflows to build an image, and ArgoCD to deploy it.
