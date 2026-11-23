# Amazon CloudWatch -- Top 50 Interview Questions and Answers

A practical interview-preparation guide for AWS Cloud Engineer, DevOps
Engineer, Cloud Support Engineer, and Site Reliability Engineer (SRE)
roles.

## Section 1: CloudWatch fundamentals (1--10)

### 1. What is Amazon CloudWatch?

Amazon CloudWatch is an AWS monitoring and observability service used to
monitor AWS resources, applications, and infrastructure. It collects and
monitors metrics and logs, supports alarms, and provides dashboards and
analysis tools.

### 2. What are the main components of CloudWatch?

-   **CloudWatch Metrics:** Collects performance data.
-   **CloudWatch Logs:** Stores and analyzes log data.
-   **CloudWatch Alarms:** Monitors metrics and triggers configured
    actions.
-   **CloudWatch Dashboards:** Visualizes monitoring data.
-   **CloudWatch Logs Insights:** Queries and analyzes logs.
-   **CloudWatch Synthetics:** Uses canaries to monitor application
    endpoints and workflows.
-   **CloudWatch Agent:** Collects system-level metrics and logs from
    supported servers.

### 3. What is a CloudWatch metric?

A metric is a time-ordered set of data points used to monitor a resource
or application. Examples include EC2 `CPUUtilization`, RDS
`DatabaseConnections`, and load balancer `RequestCount`.

### 4. What is a CloudWatch namespace?

A namespace is a container for CloudWatch metrics. AWS service
namespaces include `AWS/EC2`, `AWS/RDS`, `AWS/Lambda`, and `AWS/EBS`.
Custom metrics can use a namespace such as `MyApplication`.

### 5. What is a CloudWatch dimension?

A dimension is a name-value pair that helps identify a metric. For
example, `InstanceId=i-1234567890abcdef0` identifies metrics associated
with a particular EC2 instance.

### 6. What is the difference between metrics and logs?

 ### 6. What is the difference between metrics and logs?

| Metrics | Logs |
|---|---|
| Usually numerical measurements. | Text or structured records. |
| Used to track performance and trends. | Used to investigate events and troubleshoot issues. |
| Can be monitored using CloudWatch Alarms. | Can be searched and analyzed using CloudWatch Logs Insights. |
| Example: CPU utilization is 80%. | Example: Database connection failed. |

### 7. What is the default monitoring interval for EC2?

EC2 basic monitoring publishes metrics at five-minute intervals by
default. Detailed monitoring provides one-minute intervals and may incur
additional charges.

### 8. What is custom monitoring in CloudWatch?

Custom monitoring means publishing application-specific or
system-specific metrics to CloudWatch using APIs, AWS SDKs, or supported
agents. Examples include queue depth, business transaction count, and
application-specific performance measurements.

### 9. What is a custom metric?

A custom metric is a metric published by a customer or application
rather than one automatically provided by an AWS service. For example,
an application could publish the number of successful user logins.

### 10. What is the CloudWatch Agent?

The CloudWatch Agent is software that can run on EC2 instances and
supported on-premises servers to collect operating-system metrics and
logs. Depending on configuration and platform, it can collect memory and
disk utilization, disk I/O, and application or system logs.

## Section 2: CloudWatch alarms and monitoring (11--20)

### 11. What is a CloudWatch alarm?

A CloudWatch alarm watches a metric or metric expression and changes
state when configured conditions are met. For example, an alarm can
enter the ALARM state when average EC2 CPU utilization exceeds 80% for a
specified evaluation window. It can then notify an SNS topic or perform
another supported action.

### 12. What are the three states of a CloudWatch alarm?

1.  **OK:** The metric is within the configured condition.
2.  **ALARM:** The alarm condition has been met.
3.  **INSUFFICIENT_DATA:** CloudWatch does not have enough data to
    determine the state.

### 13. What is an alarm threshold?

A threshold is the value against which a metric is evaluated. For
example, an alarm could be configured for `CPUUtilization > 80`.

### 14. What is the evaluation period in CloudWatch?

The evaluation period is the number of metric periods considered when
CloudWatch determines an alarm state. For example, an alarm might
evaluate five one-minute periods. The `datapointsToAlarm` setting can
specify how many of those periods must breach the threshold.

### 15. What is the difference between a metric alarm and a composite alarm?

-   **Metric alarm:** Evaluates a metric or metric math expression
    against configured conditions.
-   **Composite alarm:** Combines the states of other alarms using
    logical rules such as `AND`, `OR`, and `NOT`. Composite alarms can
    help reduce notification noise.

### 16. Can CloudWatch automatically restart an EC2 instance?

CloudWatch alarms can invoke supported EC2 alarm actions, such as
reboot, stop, terminate, or recover, subject to the action type and
instance eligibility. The action must be configured on the alarm;
CloudWatch does not automatically restart every instance by default.

### 17. What is an SNS notification in CloudWatch?

Amazon Simple Notification Service (SNS) can deliver notifications when
a CloudWatch alarm changes state. Depending on the subscription type,
destinations can include email, SMS, HTTPS endpoints, and Lambda.

### 18. What is anomaly detection in CloudWatch?

CloudWatch anomaly detection uses machine learning to model expected
metric behavior and identify values that are unusually high or low
compared with the learned pattern. It can be used as an alarm threshold
band.

### 19. What is a CloudWatch dashboard?

A CloudWatch dashboard is a customizable view that displays metrics and
other monitoring information from AWS resources. It can bring important
operational indicators together in one place.

### 20. Can one CloudWatch alarm monitor multiple EC2 instances?

Yes, depending on the monitoring goal and metric configuration. Metric
math, metric search, and aggregation can be used to monitor groups of
instances. For individual instance health, separate alarms or
appropriately scoped metrics are often clearer.

## Section 3: CloudWatch Logs (21--30)

### 21. What is Amazon CloudWatch Logs?

CloudWatch Logs is an AWS service for collecting, storing, monitoring,
and analyzing log data from applications and AWS resources. Sources can
include EC2 applications, Lambda functions, VPC Flow Logs, and other AWS
services.

### 22. What is a Log Group?

A Log Group is a collection of Log Streams that share settings such as
retention and access controls. A common example is
`/aws/lambda/my-function`.

### 23. What is a Log Stream?

A Log Stream is a sequence of log events from the same source within a
Log Group. For example, a Lambda function's execution environment can
write events to a log stream.

### 24. What is a Log Event?

A Log Event is an individual log record containing a timestamp and a raw
message. Example:

``` text
2026-11-23 10:30:00 ERROR Database connection failed
```

### 25. What is CloudWatch Logs Insights?

CloudWatch Logs Insights is a query tool for searching and analyzing log
data. Example query to show recent error messages:

``` sql
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20
```

### 26. What is a CloudWatch Logs metric filter?

A metric filter scans incoming log events for a configured pattern and
publishes matching information as a CloudWatch metric. For example, a
filter can count application messages containing `ERROR`, and an alarm
can monitor that metric.

### 27. What is log retention in CloudWatch?

Log retention determines how long CloudWatch Logs keeps log events
before they expire. Retention options range from one day to ten years,
or logs can be retained indefinitely.

### 28. What is a subscription filter?

A subscription filter delivers matching log events for near-real-time
processing. Supported destinations include AWS Lambda, Amazon Kinesis
Data Streams, and Amazon Data Firehose, as well as supported
cross-account destinations.

### 29. How do you send EC2 logs to CloudWatch?

1.  Create or use an EC2 IAM role with the permissions required by the
    CloudWatch Agent.
2.  Attach the role to the EC2 instance.
3.  Install the CloudWatch Agent.
4.  Configure the agent to collect the required application or system
    log files.
5.  Start or restart the agent.
6.  Verify that the expected Log Group and Log Streams appear in
    CloudWatch Logs.

### 30. What is the difference between CloudWatch Logs and CloudTrail?


| CloudWatch Logs | AWS CloudTrail |
|---|---|
| Collects and analyzes application, system, and service logs. | Records AWS account activity and API events. |
| Helps troubleshoot application behavior. | Helps audit who performed an AWS action, when, and from where. |
| Can ingest application error messages. | Records management events and, when configured, data events. |
| Provides Logs Insights queries for log analysis. | Provides Event History and trail-based event delivery. |

## Section 4: Advanced CloudWatch concepts (31--40)

### 31. What is a CloudWatch Event?

CloudWatch Events was the earlier name for functionality now provided by
Amazon EventBridge. EventBridge responds to events from AWS services,
applications, and custom sources. For example, a rule can invoke a
Lambda function when an EC2 instance changes state.

### 32. What is CloudWatch Metric Math?

Metric Math lets you perform calculations using one or more CloudWatch
metrics. For example, `m1 / m2 * 100` can calculate a percentage when
`m1` and `m2` represent appropriate numerator and denominator metrics.

### 33. What is a high-resolution metric?

A high-resolution custom metric is published with a one-second storage
resolution. Standard-resolution custom metrics use a 60-second
resolution. High-resolution metrics can support alarms with shorter
periods, subject to CloudWatch limits and pricing.

### 34. What is CloudWatch Contributor Insights?

Contributor Insights analyzes log data to identify the top contributors
affecting system performance. Examples include the IP addresses
generating the most requests, users generating the most API calls, or
resources producing the most errors.

### 35. What is CloudWatch Synthetics?

CloudWatch Synthetics uses configurable canaries to monitor application
endpoints and workflows. A canary can periodically run scripted checks
that simulate user activity and help detect availability or performance
problems.

### 36. What is CloudWatch ServiceLens?

CloudWatch ServiceLens helps monitor and troubleshoot applications by
bringing together metrics, logs, traces, and service maps. It helps
teams understand application dependencies and investigate performance
issues.

### 37. What is CloudWatch Application Signals?

Application Signals provides application performance monitoring for
supported and instrumented applications. It helps teams monitor service
health indicators such as latency, availability, errors, and
service-level objectives (SLOs).

### 38. What is CloudWatch cross-account observability?

Cross-account observability allows teams to monitor and troubleshoot
resources across multiple AWS accounts from a monitoring account.
Depending on configuration, it can provide access to metrics, logs,
traces, and related observability data.

### 39. What is CloudWatch Metric Stream?

CloudWatch Metric Streams continuously streams CloudWatch metrics to
supported destinations, such as Amazon Data Firehose and supported
third-party monitoring solutions. It helps organizations analyze metrics
outside the CloudWatch console.

### 40. What is the difference between CloudWatch and AWS Config?

| CloudWatch | AWS Config |
|---|---|
| Monitors operational performance and health. | Tracks resource configurations and configuration changes. |
| Collects metrics and logs. | Records configuration history and evaluates compliance. |
| Creates metric and composite alarms. | Uses rules to assess resource configurations. |
| Example: Monitoring EC2 CPU utilization. | Example: Checking whether an S3 bucket has encryption enabled. |

## Section 5: Real-time scenario-based interview questions (41--50)

### 41. Your EC2 CPU utilization suddenly reaches 95%. How will you troubleshoot it?

**Answer:** 1. Open CloudWatch and inspect the instance's
`CPUUtilization` metric. 2. Identify when the increase started and
whether it is sustained. 3. Check memory and disk metrics if the
CloudWatch Agent is configured. 4. Connect to the instance using SSH or
AWS Systems Manager Session Manager. 5. Use tools such as `top`, `htop`,
or `ps` to identify high-CPU processes. 6. Review application and system
logs for errors or unusual activity. 7. Investigate the cause and apply
an appropriate fix, such as optimizing the application, adjusting
capacity, or restarting a failed process under the relevant operational
procedure.

### 42. Your application is generating too many ERROR logs. How will you monitor them?

**Answer:** 1. Send application logs to CloudWatch Logs. 2. Create or
select the appropriate Log Group. 3. Configure a metric filter that
matches error messages. 4. Publish matching events as a custom metric.
5. Create a CloudWatch alarm on that metric. 6. Configure an SNS
notification so the team is alerted when the error count crosses the
threshold.

### 43. How will you monitor EC2 memory utilization using CloudWatch?

**Answer:** EC2 does not publish guest operating-system memory
utilization as a standard EC2 metric by default. I would: 1. Install the
CloudWatch Agent. 2. Attach an IAM role with the required permissions.
3. Configure the agent to collect memory metrics. 4. Start the agent and
verify that metrics are being published. 5. Create a CloudWatch alarm
for high memory utilization.

### 44. How will you monitor an application running on multiple EC2 instances?

**Answer:** - Use the CloudWatch Agent to collect operating-system
metrics. - Send application logs to CloudWatch Logs. - Use CloudWatch
dashboards for a centralized view. - Configure alarms for abnormal
conditions. - Use SNS for notifications. - Use metric math or suitable
aggregation to monitor fleet-level indicators, while retaining
instance-level visibility for troubleshooting.

### 45. What will you do if a CloudWatch alarm shows INSUFFICIENT_DATA?

**Answer:** I would check: 1. Whether the resource is running and
producing data. 2. Whether the correct namespace, metric name, and
dimensions are configured. 3. Whether the CloudWatch Agent or metric
publisher is running. 4. Whether IAM permissions allow metrics to be
published. 5. Whether the selected period and evaluation settings are
appropriate. 6. Whether the metric has enough recent data points. 7.
Whether the alarm's missing-data treatment is configured appropriately
for the use case.

### 46. How can you monitor an AWS Lambda function using CloudWatch?

**Answer:** CloudWatch automatically collects Lambda metrics such as: -
`Invocations` - `Errors` - `Duration` - `Throttles` -
`ConcurrentExecutions`

Lambda execution logs can also be sent to CloudWatch Logs. I would
create alarms for elevated errors, long duration, or throttling and use
Logs Insights to investigate failures.

### 47. How do you monitor an AWS RDS database using CloudWatch?

**Answer:** CloudWatch provides RDS metrics such as: -
`CPUUtilization` - `FreeStorageSpace` - `DatabaseConnections` -
`ReadIOPS` - `WriteIOPS` - `FreeableMemory`

I would create alarms for important thresholds and use the metrics
alongside database logs and other diagnostic tools to investigate
performance problems.

### 48. How will you receive an email when EC2 CPU utilization exceeds 80%?

**Answer:** 1. Open CloudWatch and choose Alarms. 2. Create an alarm and
select the EC2 `CPUUtilization` metric for the instance. 3. Configure
the condition, such as CPU utilization greater than 80%. 4. Set the
evaluation period and datapoints required to trigger the alarm. 5.
Select or create an SNS topic as the alarm action. 6. Subscribe an email
address to the SNS topic. 7. Confirm the email subscription. 8. Create
the alarm and verify it is in the expected state.

When the alarm enters the ALARM state, SNS sends an email to confirmed
subscribers.

### 49. Your CloudWatch dashboard is not showing EC2 metrics. How will you troubleshoot?

**Answer:** 1. Confirm that the correct AWS account and Region are
selected. 2. Confirm that the EC2 instance is publishing metrics. 3.
Check the dashboard widget's namespace, metric name, and dimensions. 4.
Verify the time range and statistic selected in the widget. 5. Check
whether the dashboard or viewing user has the required IAM permissions.
6. Confirm that the metric exists and has data for the selected time
range. 7. Review any dashboard JSON or widget configuration for errors.

### 50. How would you design a complete AWS monitoring solution using CloudWatch?

**Answer:** I would use a combination of services: \| Component \|
Purpose \| \|---\|---\| \| CloudWatch Metrics \| Monitor resource and
application performance \| \| CloudWatch Logs \| Collect application and
system logs \| \| CloudWatch Agent \| Collect guest operating-system
metrics and log files \| \| CloudWatch Alarms \| Detect abnormal
conditions \| \| Amazon SNS \| Send email, SMS, or other configured
notifications \| \| CloudWatch Dashboards \| Provide centralized
visualization \| \| CloudWatch Logs Insights \| Search and analyze log
data \| \| Amazon EventBridge \| Trigger automated workflows in response
to events \| \| CloudWatch Synthetics \| Monitor application
availability and user-facing workflows \|

A typical flow is: AWS resources generate metrics and logs; CloudWatch
collects and evaluates the data; alarms notify operators through SNS;
and EventBridge can trigger configured automated responses. Dashboards
give the operations team a consolidated view for troubleshooting and
capacity planning.

## Bonus: Useful AWS CLI commands

### 1. List CloudWatch metrics

``` bash
aws cloudwatch list-metrics
```

### 2. Describe CloudWatch alarms

``` bash
aws cloudwatch describe-alarms
```

### 3. Get EC2 CPU metric statistics

Replace the instance ID and timestamps with values for your environment.

``` bash
aws cloudwatch get-metric-statistics \
  --namespace AWS/EC2 \
  --metric-name CPUUtilization \
  --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
  --start-time 2026-11-23T10:00:00Z \
  --end-time 2026-11-23T11:00:00Z \
  --period 300 \
  --statistics Average
```

### 4. Describe log groups

``` bash
aws logs describe-log-groups
```

### 5. Describe log streams

``` bash
aws logs describe-log-streams \
  --log-group-name /aws/lambda/my-function
```

### 6. Start the CloudWatch Agent on a Linux systemd host

``` bash
sudo systemctl start amazon-cloudwatch-agent
```

### 7. Check CloudWatch Agent status

``` bash
sudo systemctl status amazon-cloudwatch-agent
```

## High-priority topics to revise

1.  CloudWatch Metrics, namespaces, and dimensions
2.  CloudWatch Alarms and SNS notifications
3.  CloudWatch Agent and EC2 memory monitoring
4.  CloudWatch Logs, Log Groups, and Log Streams
5.  Metric Filters and Logs Insights
6.  EC2, RDS, and Lambda monitoring
7.  EventBridge integration and automated responses
8.  Dashboards, Metric Math, and anomaly detection
9.  Troubleshooting `INSUFFICIENT_DATA`
10. Designing end-to-end monitoring and alerting

## Interview preparation tip

Do not only memorize definitions. Practice explaining a complete
monitoring workflow: install and configure the CloudWatch Agent on EC2,
publish memory metrics, create a metric alarm, connect an SNS topic,
confirm the email subscription, and use CloudWatch Logs Insights to
investigate an application error.

------------------------------------------------------------------------

*Note: AWS service features, limits, console labels, and pricing can
change. Verify implementation details against current AWS documentation
before using commands or designing production monitoring.*
