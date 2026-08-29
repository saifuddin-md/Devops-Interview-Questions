Jenkins & CI/CD Interview Questions and Answers

# 1\. What is CI/CD?

**Answer:**

CI : Developers frequently merge code into a shared repository, and Jenkins automatically builds and tests it.

CD : After successful testing, the application is automatically delivered or deployed to the required environment.

# 2\. What is a Jenkinsfile?

**Answer:**

Jenkinsfile is a text file that contains the Jenkins Pipeline code written in Groovy. It is usually stored in the

application's Git repository and defines the steps to build, test, and deploy the application.

# 3\. What is a Jenkins Plugin?

**Answer:**

A Jenkins Plugin is an extension/software, Plugins allow Jenkins to communicate with external tools such as Git,

Docker, Kubernetes, Maven, and Slack. They are installed and managed from the Jenkins Plugin Manager.

# 4\. How do you store credentials securely in Jenkins?

**Answer:**

I store sensitive information such as passwords, API tokens, and SSH keys in Jenkins Credentials Manager. In the

Jenkinsfile, we reference the credential using its ID instead of hardcoding the secret.

**Store a credential in**: Manage Jenkins → Credentials → Add Credentials.

# 5\. What is a Jenkins webhook?

**Answer:**

A Jenkins webhook is an HTTP notification sent by GitHub or GitLab to Jenkins when an event, such as a code

push or pull request, occurs. Jenkins receives the webhook and automatically triggers the pipeline.

# 6\. What happens if a Jenkins build fails?

**Answer:**

When a Jenkins build fails, Jenkins marks it as Failed. Based on the pipeline configuration, it can stop

execution, retry the failed step, send notifications,.

# 7\. What is Jenkins Agent?

**Answer:**

Jenkins Agent means the machine/node where Jenkins actually executes your pipeline commands.

# 8\. What is the difference between ""agent any"" and "agent none?

**Answer:**

**agent any:** Jenkins allocates any available agent/node to run the entire pipeline or stage.

_agent any = automatically use any available agent_

**agent none:** Jenkins does not allocate a global agent. Each stage must define its own agent (or manage execution

differently), allowing different stages to run on different agents.

_agent none = no agent for the whole pipeline, so stages specify their own agents._

# 9\. Why do we use a worker node?

**Answer:**

We use Jenkins worker nodes/agents to execute pipeline jobs and reduce the workload on the Jenkins Controller.

They also allow us to run jobs in different environments.

# 10\. How do you integrate an agent/worker node with Jenkins Controller?

**Answer:**

We integrate a worker node by installing Java on the worker machine, adding it as a node in Jenkins, assigning

a label, configuring a connection method such as SSH with credentials, and bringing the node online. Then we use the

node's label in the Jenkinsfile to run the pipeline on that worker.

# 11\. How do you select a specific node for a job?

**Answer:**

We select a specific Jenkins worker node by assigning a label to the node and using that label in the Jenkinsfile.

**Example:**

pipeline {

agent {

label 'linux-worker'

}

# 12\. What is jobs?

**Answer:**

A Jenkins Pipeline job is an automated workflow written as code that defines the steps to build, test, and deploy

an application. It is usually written in a Jenkinsfile and runs on Jenkins agents.

# 13\. If Jenkins server runs out of space, how will you troubleshoot and prevent it?

**Answer:**

First, I would check disk usage using df -h and du to identify what is consuming space. I then clean unnecessary

builds, artifacts, workspaces, logs, or Docker resources.

**Example:**

**df -h**

**du -sh /var/lib/jenkins/\***

**du -sh /var/lib/jenkins/jobs/\*/builds**

# 14\. How to trigger a Jenkins job when code is pushed to GitHub?

**Answer:**

We use a GitHub webhook to trigger Jenkins. When a developer pushes code to GitHub, GitHub sends an HTTP webhook

notification to Jenkins, and Jenkins automatically triggers the configured pipeline.

# 15\. How do you pass parameters between stages?/ How do you pass data or values generated in one stage to another stage?

**Answer:**

We can pass data between Jenkins stages using environment variables for simple values.

# 16\. Difference between Declarative and Scripted pipeline?

**Answer:**

**Scripted:**

- Uses a more flexible, Groovy-based syntax and allows complex programming logic.
- syntax or runtime errors may only be discovered when the pipeline executes.
- Restarting directly from a specific stage is generally not supported.
- Not build-in support for skipping stages.

**Declarative:**

- Declarative Pipeline is more structured, easier to read.
- syntax and structural errors are validated by Jenkins before the pipeline starts executing.
- Restarting from a specific completed stage is supported for eligible Declarative Pipelines.
- Supports skipping stages using features such as the when directive.

**example:**

**stage ('Deploy to Production') {**

**when {**

**branch 'main'**

**}**

# 17\. Your build pipeline failed at the deployment stage - how would you debug it?

**Answer:**

I would first check the Jenkins console output to identify the exact deployment error. Then I would verify the deployment script, artifact, environment variables, credentials, permissions, and connectivity to the target environment. I would also check the target application's logs and, if needed, reproduce the deployment manually from the Jenkins agent to identify the root cause.

# 18\. How do you handle rollback in case of a failed release? / If a Jenkins pipeline fails after deployment, how do you roll back automatically?

**Answer:**

In case of a failed release, I keep the previous stable version available and perform health checks after deployment. If the deployment or validation fails, the pipeline automatically triggers a rollback to the previous stable version.

**Example:**

stage('Deploy') {

steps {

script {

try {

// Deploy new version

sh 'docker stop myapp || true'

sh 'docker rm myapp || true'

sh 'docker run -d --name myapp -p 8080:8080 myapp:2.0'

// Health check

sh 'sleep 10'

sh 'curl -f <http://localhost:8080/health>'

} catch (Exception e) {

echo 'Deployment failed. Rolling back...'

// Rollback to previous version

sh 'docker stop myapp || true'

sh 'docker rm myapp || true'

sh 'docker run -d --name myapp -p 8080:8080 myapp:1.0'

// Keep pipeline failed

throw e

}

}

}

}

}

**Here:**

\* A try block contains the code that you want to execute and monitor for errors.

\* If an error occurs inside the try block, Jenkins moves to the catch block.

\* catch (Exception e) means "if an error happens, catch it and handle it here."

\* throw e means send the original error back to Jenkins so Jenkins knows that the deployment failed.

# 19\. How do you implement manual approval before production deployment?

**Answer:**

We can use the input step in a Declarative Pipeline to add a manual approval before production deployment. Jenkins pauses the pipeline and waits for an authorized user to approve it. Once approved, the production deployment stage continues.

**Example:**

steps {

input message: 'Deploy to Production?',

ok: 'Approve'

}

# 20\. What is SAST/DAST?

**Answer:**

**SAST (Static Application Security Testing):** Scans the source code for vulnerabilities without running the application.

**DAST (Dynamic Application Security Testing):** Scans the running application to find vulnerabilities.

- - Checks the running application" means OWASP ZAP tests your application while it is deployed and accessible through a URL.
    - OWASP ZAP connects to that URL and sends different HTTP requests to test how the application responds.

# 21\. How do you integrate security scans (SAST/DAST) into CI/CD?

**Answer:**

I have used SonarQube for SAST and OWASP ZAP for DAST in Jenkins CI/CD pipelines. SonarQube analyzes the source code for security vulnerabilities, bugs, and code quality issues. OWASP ZAP scans the running application to identify web security vulnerabilities.

# 22\. What is Blue-Green deployment?

**Answer:**

Blue-Green deployment uses two separate environments for the same application. Blue runs the current production version, while Green is used to deploy and test the new version. Once Green passes health checks, the load balancer switches traffic from Blue to Green. We keep Blue available for quick rollback if the new version has issues. This helps achieve zero-downtime deployment.

# 23\. How do you ensure zero-downtime deployment through your pipeline?

**Answer:**

I ensure zero-downtime deployment by using strategies such as Blue-Green, Rolling, or Canary deployments. For example, in Blue-Green deployment, I keep the current version running while deploying and testing the new version. Once the new version is healthy, I switch traffic to it. If it fails, I keep traffic on the old version.

# 24\. What are Triggers?

**Answer:**

Trigger in Jenkins defines the way in which the pipeline should be executed frequently. PollSCM, Cron, etc are the

currently available Triggers.

# 25\. How to create a backup and copy files in Jenkins?

**Answer:**

In Jenkins, all the settings, build logs and configurations are stored in the JENKINS_HOME directory. Whenever you want to create a backup of your Jenkins you can back up JENKINS_HOME directory frequently. It consists of all the job configurations and slave node configurations. Hence, regularly copying this directory allows us to keep a backup of Jenkins. or we can use plugin "ThinBackup".

# 26\. Explain the CI/CD pipeline you have worked on?

**Answer:**

In my last project, I built a CI/CD pipeline in Jenkins integrated with GitHub. Whenever developers

push code, the pipeline automatically triggers through a webhook.

The pipeline first checked out the source code. Then we compiled and built the application using Maven,

After the build, we executed unit tests, then we performed code quality analysis using SonarQube. After

that, we performed dependency and filesystem vulnerability scanning using Trivy. Once the code quality

and security checks were successful, we packaged the application as a JAR file and uploaded the artifact

to the Nexus Repository.

Then, Jenkins triggered the Docker image build. After building the image, we performed another security

scan using Trivy to identify vulnerabilities in the Docker image. If the image passed the security checks,

we tagged and pushed it to Docker Hub.

For deployment, Jenkins used Kubernetes credentials and kubeconfig to deploy the Docker image to the K8s cluster. After a successful deployment, the pipeline sent an email notification with the build and scan reports attached.