# CloudWatch

Used for ingestion, storage and management of metrics.

## Architecture

Public service with public space endpoint, accessible from aws and on-premise.

Any service using CloudWatch should have access to public space endpoint, usually via Internet Gateway or an Interface Endpoint within a VPC

- Public VPC's connect to CloudWatch using `Internet Gateway`.
- Private VPC's connect to CloudWatch using `Interface Endpoint`.
- On-premise VMs and Applications use either an Agent, CLI or API.
- Internet connected applications use CLI or API.

## Integration

Many AWS Services include native `management plane integration`.

- EC2 for example provides metric data into CloudWatch without any user configuration (externally visible metrics)
- This includes only metrics available from outside the EC2

For metrics inside the instances or services, `CloudWatch Agent is required`.

**On-Premise integration** and **Application Integration** is done `either via Agent or via API` (custom metrics).

### CloudWatch Agent

- Injects detailed and custom metrics from an EC2 instance into CloudWatch
- Logs system, application and custom logs into CloudWatch logs

## Alarms

Alarms are used to react to metrics. And can be used to notify or perform actions.

- SNS Notifications can be sent
- Auto Scaling Group can perform scaling
- EventBridge Events

Valid alarm states:

- Ok
- Alarm
- Insufficient Data

## Data

Datapoint - Smallest component of CloudWatch. It consists of:

- Timestamp
- Value
- Unit of measure (Optional)

Metric - Time ordered set of Datapoints. It includes:

- Metric Name
- Namespace

Namespace - CloudWatch data for multiple application or services is segregated using Namespace such as 'AWS/EC2', 'AWS/Lambda'

Dimension - Used to differentiate between datapoints of diffrent application or services (instances). When you add data into CloudWatch you provide:

- Timestamp
- Value
- Unit of measure (Optional)
- Metric Name
- Namespace
- 0 or more dimensions

---

# CloudWatch Logs

CloudWatch logs is a product which can store, manage and provide access to logging data for on-premises and AWS environments including systems and applications.

- It has native support for logs from aws products and services.
- For application or system logs for any on-premise system, you need to install CloudWatch agent.

CloudWatch Logs also supports ingestion of logs from:

- VPC flow logs
- CloudTrail
- Various other products like Elatic Beanstalk, ECS Container Logs, API Gateway, Lambda execution logs
- Route53 DNS request logging

## Log Events

Consists of two parts:

- Timestamp
- Raw Message

## Log Stream

Sequense of Log Events that share the same Logging Source say `/var/output.log`.

If you have multiple EC2 instances doing similar logging to `/var/output.log` in their instance volume, then we can have 3 log stream, one for each EC2 instance.

## Log Group

Log Group is to monitor all the Log Stream for a given logging source say `/var/output.log` originating from each EC2 instance. So, in this case Log Group will have three Log Stream.

- Log Group is used to set retention, access permissions to the logs and encryption of logs at rest using KMS

## Metric filters

Metric filters can be used to generate:

- alarms based on patterns within a Log Group and
- Metrics within Cloudwatch
- eventual events within Eventbridge.

## Subscription filter

Via subscription filters, CloudWatch Logs can stream the data in `realtime` to following for further delivery.

- Lambda
- Elasticsearch
- Kinesis streams and firehose

This is done on `per Log Group basis`. The Subscription Filter needs to be created on that LogGroup. Following is configured by the subscription filter:

- Pattern (To limit what gets handled by that filter)
- Set Destination ARN of the destination
- Distribution (Controls how logging data gets grouped up)

Post this we need to define permission for the CloudWatch log to get access to that destination.

### Destination S3 - For long term storage

In this case you can configure Kinesis Data Firehose as the destination, which allows `near realtime` delivery of logging data to S3.

- The delay is added because of the buffer (about 60 seconds)

### Destination ElasticSearch

You can configure destination as AWS Managed Lambda function that allows data to be delivered in `realtime` to ElasticSearch.

### Destination Custom

You can configure destination as a Lambda function that can be used to export data to nearly any destination in `realtime`.

### Destination Kinesis Data Stream

You can deliver logs to Kinesis Data Stream in `realtime`, this allows data to be used within other systems.

## Common Subscription Filter Architecture

We generally use subscription filter to aggregate logging data from multiple accounts.

We use multiple subscription filter in each account to send the data into `Kinesis Data Stream` which can allow realtime access to the logging data.

This data can then be sent to `Kinesis Data Firehose` which sends this for long term storage into S3.

## Export to S3

You can export logs to S3 via `CreateExportTask`. But this operation takes about 12 hours.

- Logs exported cannot be encrypted using KMS
- Logs can only be encrypted using S3 encryption

## Consideration

To configure logs within an EC2 instance we need the following at the minimum:

- EC2 instance role with CloudWatch permissions
- CloudWatch Agent Install with configuration

---

# AWS X-Ray

Equivalent to AppDynamics.

Its a distributed tracing application. `Designed to track session through an application`.

- Application could be monolithic, serverless or using an distributed microservice architecture
- Can include API Gateway, Lambda, Elastic Beanstalk, Load Balancer or Downstream services such as DynamoDB

We can now see a web application composed of multiple microservices, multiple VPCs, multiple regions and multiple availability zones.

> X-Ray is able to trace only requests upon entry into your systems. Connectivity issues between a client and your resources cannot be traced.

---

## Data Captured

### Traces

Traces are formed by correlating segments using a `unique trace ID`. X-Ray trace is set of data points that share the common trace ID.

Consider an client that initiates a new request to our application. The request is tagged with a `unique trace ID`. As the service makes its way downstream through further services in your application, the services relay information regarding the request back to X-Ray using the same `unique trace ID`.

### Tracing Header

The unique trace ID generated to track a request through our distributed application is inserted into the tracing header.

### Segments

The applicatons sends the data in the form of segments. A segment provides the resource name, request and response details. And details about the job done.

For an HTTP request following data might be recorded:

- **host**: hostname, alias and/or ip address
- **request**: Includes the HTTP method, client address, path and/or user agent
- **response**: Includes the status and content
- **work done**: Start and End time
- **subsegments**: Other called upon subsegments

### Subsegments

A Segment can includes Subsegments if that level of granularity is required.

Subsegments are used to record downstream calls from the point of view of the service that calls it.

X-Ray uses subsegments to identify downstream services that dont send segments.

### Service Graphs

X-Ray uses all the data that the application sends and generates what is called as Service Graph

- JSON Document detailing services and resources which make up your application

### Service Map

X-Ray uses these service graphs and generates a service map, which is the visual version of the service graph showing Traces.

## How Traces are collected for Various Services

- EC2 `(X-Ray Agent)`
- ECS `(Agent is installed as part of tasks)`
- Lambda `(Requires enabling data collection with X-Ray for the Lambda function)`
- Beanstalk `(Agent is preinstalled)`
- API Gateway `(Agent is enabled as a per stage basis)`
- SNS & SQS `(Can be configured to send data into X-Ray)`

---

## X-Ray Components

- **AWS X-Ray SDK**: Used to instrument the application code.
- **AWS X-Ray Daemon**: This collects all the local trace data and batches the information up and periodically sends it over the internet to the AWS X-Ray service. _The Daemon listens on port 2000 by default for UDP connections_.
- **AWS X-Ray API**: Used to receive the collected telemetry as delivered by AWS X-Ray Daemon.
- **AWS X-Ray Console**: This is where all the visualization happens.

---

## AWS X-Ray Daemon

Listens on UDP port 2000.

Credentials used by X-Ray Daemon can be either supplied via:

- an IAM rule (EC2 Environment)
- by setting environment variable for AWS access key ID and secret key (Non-EC2 Environment)

X-Ray daemon is provided appropriate IAM permissions to allow it to upload segment data and telemetry to X-Ray API. This is done by providing access to the following X-Ray API endpoints:

- xray:PutTraceSegments
- xray:PutTelemetryRecords

`AWSXrayWriteOnlyAccess` - Write persmission to authorize X-Ray Daemon, CLI or SDK to upload segment documents and telemetry to the X-Ray API.

`AWSXrayReadOnlyAccess` - Read persmission to authorize X-Ray Daemon, CLI or SDK to get trace data and service maps from the X-Ray API.

---

# VPC Flow Logs

VPC FLow logs is a feature allowing the monitoring of traffic flow to and from interfaces within a VPC

- Flow Logs DON'T monitor packet contents ... that requires a packet sniffer (installed on the EC2 instance).
- Flow Logs can be stored on S3 or CloudWatch Logs

Data captured includes:

- Source and Destination IP Address and Port
- Packet Size
- Protocol
- Action
- Account ID
- Interface ID
- And other externally available metadata

## Monitoring Points

VPC Flow logs can be added at a

- VPC
- Subnet or
- Interface level (ENI).

## Considerations

- Flow logs arent Real Time.
- Destination can be S3 or CloudWatch Logs.

> Following is traffic to and from is not recorded

- `169.254.169.254` (metadata IP)
- `169.254.169.123` (aws time synchronization server)
- DHCP
- Amazon DNS Server
- Amazon Windows Licence

Under the logs if the traffic was controlled through:

- `Security Group`: You would see only one entry in the log, as security groups are stateful. If the traffic is allowed INBOUND, its automatically allowed OUTBOUND.
- `Network ACL (NACL)`: Yould see two log entry if the Security Group allows traffic, NACL allows INBOUND traffic but blocks OUTBOUND traffic.

Sample Log format:

```
[version, account, eni, source, destination, srcport, destport="22", protocol="6", packets, bytes, windowstart, windowend, action="REJECT", flowlogstatus]
```
