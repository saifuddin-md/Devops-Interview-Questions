# Top 50 GitHub Actions Interview Questions and Answers

## Basic Level (1–15)

**Question 1: What is GitHub Actions?**

**Answer:** GitHub Actions is a CI/CD platform used to automate software development workflows, such as building, testing, and deploying applications directly from GitHub repositories.

**Question 2: What are GitHub workflows?**

**Answer:** Workflows are automated processes defined in YAML files that specify when and how tasks should run. They are stored in the **`.github/workflows/`** directory.

**Question 3: What is a job in GitHub Actions?**

**Answer:** A job is a group of steps that execute on the same runner. Jobs can run in parallel or sequentially depending on their dependencies.

**Question 4: What is a runner in GitHub Actions?**

**Answer:** A runner is a machine that executes the jobs defined in a GitHub Actions workflow.

**Question 5: What are GitHub-hosted runners?**

**Answer:** GitHub-hosted runners are virtual machines managed by GitHub that provide an environment for executing workflow jobs.

**Question 6: What are self-hosted runners?**

**Answer:** Self-hosted runners are machines managed by users or organizations to execute GitHub Actions jobs. They provide greater control over the environment and installed software.

**Question 7: What is a step in GitHub Actions?**

**Answer:** A step is an individual task within a job. It can execute a shell command using **`run:`** or invoke an action using **`uses:`**.

**Question 8: What is an event in GitHub Actions?**

**Answer:** An event is an activity that triggers a workflow, such as pushing code, creating a pull request, or manually starting a workflow.

**Question 9: What are some common GitHub Actions events?**

**Answer:** Common events include:

- **`push`** — triggers a workflow when code is pushed.
- **`pull_request`** — triggers a workflow for pull request activities.
- **`schedule`** — runs a workflow on a schedule.
- **`workflow_dispatch`** — allows manual workflow execution.
- **`release`** — triggers a workflow when a release event occurs.

**Question 10: What is the syntax used to define a GitHub Actions workflow?**

**Answer:** GitHub Actions workflows use YAML syntax, with **`.yml`** or **`.yaml`** file extensions.

**Question 11: Where are GitHub Actions workflows stored?**

**Answer:** Workflows are stored in the **`.github/workflows/`** directory at the root of the repository.

Example:


```
.github/
└── workflows/
    ├── ci.yml
    └── deploy.yml
```

**Question 12: How do you trigger a workflow manually?**

**Answer:** Use the **`workflow_dispatch`** event in the workflow YAML file.


```
name: Manual Workflow

on:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Workflow started manually"
```

**Question 13: What is `on: push` in a workflow?**

**Answer:** **`on: push`** triggers a workflow when a push event occurs in the repository. It can be configured to run for all branches or specific branches.


```
on:
  push:
```

**Question 14: How do you specify a specific branch in a workflow trigger?**

**Answer:** Use the **`branches`** filter under the **`push`** event.


```
on:
  push:
    branches:
      - main
```

This triggers the workflow when code is pushed to the **`main`** branch.

**Question 15: What is `on: pull_request`?**

**Answer:** **`on: pull_request`** triggers a workflow when specified pull request activities occur, such as opening or updating a pull request.


```
on:
  pull_request:
    branches:
      - main
```

## Intermediate Level (16–30)

**Question 16: What is a matrix in GitHub Actions?**

**Answer:** A matrix allows a job to run multiple times with different configurations, such as different operating systems or programming language versions.

**Question 17: How do you define environment variables in GitHub Actions?**

**Answer:** Use the **`env:`** keyword at the workflow, job, or step level.


```
env:
  APP_NAME: my-app

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "$APP_NAME"
```

**Question 18: How do you access environment variables in GitHub Actions?**

**Answer:** Use **`${{ env.VARIABLE_NAME }}`** in workflow expressions. Inside shell commands, use the syntax supported by the selected shell, such as **`$VARIABLE_NAME`** in Bash.

Example:


```
env:
  APP_NAME: my-app

steps:
  - run: echo "${{ env.APP_NAME }}"
```

**Question 19: How do you store secrets in GitHub Actions?**

**Answer:** Store secrets in GitHub under **Settings → Secrets and variables → Actions**. Secrets can be configured at repository, organization, or environment level, depending on the required scope.

Examples include API keys, passwords, and access tokens.

**Question 20: How do you use secrets in GitHub Actions workflows?**

**Answer:** Use the **`secrets`** context to access a configured secret.


```
steps:
  - name: Use API token
    env:
      API_TOKEN: ${{ secrets.API_TOKEN }}
    run: ./deploy.sh
```

**Question 21: What is `jobs.<job-id>.strategy.matrix` used for?**

**Answer:** It defines multiple job configurations so that the same job can execute with different parameters, often in parallel.

Example:


```
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node: [20, 22]
```

This creates four job combinations.

**Question 22: How do you define a job dependency in GitHub Actions?**

**Answer:** Use the **`needs:`** keyword to specify which job must complete successfully before another job runs.


```
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building application"

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying application"
```

**Question 23: Can jobs run in parallel in GitHub Actions?**

**Answer:** Yes. Jobs run in parallel by default when they have no dependencies. The **`needs:`** keyword is used to define execution order.

**Question 24: What is an artifact in GitHub Actions?**

**Answer:** An artifact is a file or collection of files produced during a workflow run that can be stored and downloaded later. Artifacts are commonly used to share build outputs, test reports, and deployment packages between jobs.

**Question 25: How do you upload artifacts in GitHub Actions?**

**Answer:** Use the **`actions/upload-artifact`** action.


```
steps:
  - name: Create build file
    run: echo "Build completed" > build.txt

  - name: Upload artifact
    uses: actions/upload-artifact@v4
    with:
      name: build-output
      path: build.txt
```

**Question 26: How do you download artifacts in GitHub Actions?**

**Answer:** Use the **`actions/download-artifact`** action.


```
steps:
  - name: Download artifact
    uses: actions/download-artifact@v4
    with:
      name: build-output
      path: ./downloaded
```

**Question 27: What is the default shell in GitHub Actions?**

**Answer:** On Linux and macOS, Bash is the default for **`run:`** steps when available. Windows runners commonly use PowerShell by default. The exact behavior depends on the operating system and shell configuration.

**Question 28: Can you use PowerShell in GitHub Actions?**

**Answer:** Yes. Specify **`shell: pwsh`** to run commands using PowerShell Core.


```
steps:
  - name: Run PowerShell command
    shell: pwsh
    run: |
      Write-Host "Hello from PowerShell"
```

**Question 29: How do you run a specific script in a workflow step?**

**Answer:** Use the **`run:`** keyword with the script path.


```
steps:
  - name: Run shell script
    run: ./scripts/build.sh
```

The script must exist in the repository and have the appropriate permissions and interpreter.

**Question 30: How do you define or use an action in GitHub Actions?**

**Answer:** Use the **`uses:`** keyword to reference an existing action by its repository and version or reference.

Example:


```
steps:
  - name: Check out repository
    uses: actions/checkout@v4
```

Custom actions can be defined using an **`action.yml`** or **`action.yaml`** metadata file.

## Advanced Level (31–40)

**Question 31: What is a reusable workflow in GitHub Actions?**

**Answer:** A reusable workflow is a workflow that can be called by another workflow. It helps avoid duplication and allows teams to share common CI/CD processes across projects.

**Question 32: How do you call a reusable workflow?**

**Answer:** Use the **`jobs.<job-id>.uses`** syntax to reference a reusable workflow. The called workflow must declare the **`workflow_call`** event.

Example:


```
jobs:
  call-workflow:
    uses: ./.github/workflows/build.yml
```

**Question 33: What is an `if` condition in GitHub Actions?**

**Answer:** An **`if`** condition controls whether a job or step should execute based on a specified expression.


```
steps:
  - name: Deploy to production
    if: github.ref == 'refs/heads/main'
    run: echo "Deploying to production"
```

**Question 34: How do you check the status of a previous step?**

**Answer:** Use status-check functions such as **`success()`**, **`failure()`**, **`cancelled()`**, and **`always()`**.

Example:


```
steps:
  - name: Run tests
    run: npm test

  - name: Handle failure
    if: failure()
    run: echo "Tests failed"
```

**Question 35: How do you trigger a workflow at a specific time?**

**Answer:** Use the **`schedule`** event with a cron expression.


```
on:
  schedule:
    - cron: '0 2 * * *'
```

This schedules the workflow to run daily at 02:00 UTC. Actual execution may be delayed during periods of high GitHub load.

**Question 36: What is a composite action in GitHub Actions?**

**Answer:** A composite action is a custom action that combines multiple steps into a single reusable action. It is useful for sharing common commands across workflows.

**Question 37: What is the default timeout for a GitHub Actions job?**

**Answer:** The default job timeout is generally 360 minutes (6 hours) for GitHub-hosted runners. Limits and behavior can vary by runner type and execution environment.

**Question 38: How do you set a custom timeout for a job or step?**

**Answer:** Use the **`timeout-minutes:`** keyword.

Example:


```
jobs:
  build:
    runs-on: ubuntu-latest
    timeout-minutes: 30
    steps:
      - run: echo "Building application"
```

**Question 39: What is `continue-on-error` in GitHub Actions?**

**Answer:** **`continue-on-error: true`** allows a step or job to fail without causing the overall workflow to fail solely because of that failure.

Example:


```
steps:
  - name: Run optional check
    continue-on-error: true
    run: ./optional-check.sh
```

**Question 40: How do you cancel a running GitHub Actions workflow?**

**Answer:** You can cancel a workflow from the repository's **Actions** tab by opening the running workflow and selecting **Cancel workflow**. You can also use the GitHub CLI or API.

## CI/CD and Deployment Level (41–50)

**Question 41: How do you debug a failing GitHub Actions workflow?**

**Answer:** You can debug a failing workflow by:

- Reviewing the logs in the Actions tab.
- Checking error messages, environment variables, permissions, and dependencies.
- Setting the **`ACTIONS_STEP_DEBUG`** secret to **`true`** to enable step-level debug logging.
- Using **`ACTIONS_RUNNER_DEBUG`** for additional runner diagnostic logs.
- Testing the workflow locally with tools such as **`act`**, where supported.

Never expose secrets in debugging output.

**Question 42: What is `GITHUB_TOKEN` in GitHub Actions?**

**Answer:** **`GITHUB_TOKEN`** is a temporary authentication token automatically generated for a workflow run. It allows the workflow to interact with the repository and supported GitHub APIs.

You can control its permissions using the **`permissions:`** keyword.

Example:


```
permissions:
  contents: read
```

**Question 43: How do you pass data between jobs in GitHub Actions?**

**Answer:** You can pass data between jobs using job outputs or artifacts.

- **Job outputs:** Used for small values, such as version numbers or deployment identifiers.
- **Artifacts:** Used for files, such as build packages and test reports.

**Question 44: What is a GitHub Actions environment?**

**Answer:** A GitHub Actions environment represents a deployment target, such as development, staging, or production. It can provide environment-specific secrets, variables, protection rules, and required reviewer approvals.

Example:


```
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - run: echo "Deploying to production"
```

**Question 45: How do you roll back a failed deployment using GitHub Actions?**

**Answer:** You can roll back a failed deployment by redeploying a previously stable release or restoring a known-good application version.

Common approaches include:

- Redeploying a previous container image.
- Reverting to a stable release.
- Using blue-green or canary deployments.
- Automating rollback when health checks fail.

**Question 46: Can GitHub Actions deploy applications to AWS?**

**Answer:** Yes. GitHub Actions can deploy applications to AWS using the AWS CLI, AWS SDKs, or AWS-specific actions.

A recommended approach is to use OpenID Connect (OIDC) to obtain short-lived AWS credentials instead of storing long-lived access keys.

**Question 47: How do you deploy an application to Kubernetes using GitHub Actions?**

**Answer:** You can deploy applications to Kubernetes using **`kubectl`**, Helm, or other deployment tools.

The typical process is to build a container image, push it to a container registry, authenticate with the cluster, deploy the application, and verify its status.

Example:


```
steps:
  - name: Deploy application
    run: kubectl apply -f k8s/deployment.yaml

  - name: Verify deployment
    run: kubectl rollout status deployment/my-app
```

**Question 48: How do you cache dependencies in GitHub Actions?**

**Answer:** Use **`actions/cache`** to cache dependencies or other reusable files between workflow runs, reducing installation time and speeding up builds.

Example:


```
steps:
  - uses: actions/checkout@v4

  - name: Cache npm dependencies
    uses: actions/cache@v4
    with:
      path: ~/.npm
      key: ${{ runner.os }}-npm-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.os }}-npm-

  - name: Install dependencies
    run: npm ci
```

**Question 49: How do you test a GitHub Actions workflow locally?**

**Answer:** You can use the **`act`** CLI tool to run many GitHub Actions workflows locally using Docker.

Examples:


```
act
```

To simulate a push event:


```
act push
```

Some GitHub-specific features and runner behaviors may differ from local execution.

**Question 50: How do you restrict a workflow to specific branches?**

**Answer:** Use the **`branches`** filter under the relevant event, such as **`push`** or **`pull_request`**.

Example:


```
name: CI Pipeline

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Running CI for main branch changes"
```

This configuration triggers the workflow for pushes to **`main`** and pull request events targeting **`main`**.

## Quick Revision: Important GitHub Actions Keywords

| **Keyword**             | **Purpose**                                  |
| ----------------------- | -------------------------------------------- |
| **`on`**                | Defines workflow triggers                    |
| **`jobs`**              | Defines jobs in a workflow                   |
| **`steps`**             | Defines individual tasks within a job        |
| **`runs-on`**           | Specifies the runner                         |
| **`uses`**              | References an action or reusable workflow    |
| **`run`**               | Executes shell commands                      |
| **`needs`**             | Defines job dependencies                     |
| **`strategy.matrix`**   | Runs jobs with multiple configurations       |
| **`env`**               | Defines environment variables                |
| **`secrets`**           | Accesses configured secrets                  |
| **`if`**                | Controls conditional execution               |
| **`with`**              | Passes inputs to an action                   |
| **`permissions`**       | Controls **`GITHUB_TOKEN`** permissions      |
| **`timeout-minutes`**   | Sets a job or step timeout                   |
| **`continue-on-error`** | Allows execution to continue after a failure |

**Interview preparation tip:** Focus on workflow triggers, jobs and steps, runners, secrets, artifacts, matrix strategies, reusable workflows, and deployments to AWS and Kubernetes. These are essential concepts for GitHub Actions interviews.
