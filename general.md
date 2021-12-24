# Concepts

An AWS Availability Zone (AZ) is the logical building block that makes up an AWS Region.

---

# AWS Config

You want your IAM users to chose new passwords every year using this rule. What type of trigger should this rule utilize?

- Because you want passwords to be change after a period of time, this is `periodic trigger` type.
- No other account activity needs to be checked, so you don't need a `configuration change trigger`.

---

# AWS SDK

For the sake of more robust application recovery, you would like to make sure that if a connection established to a server is experiencing issues, that connection will be reestablished to a new server within 10 minutes. How might you accomplish this?

- Use the AWS SDK to change the default client configuration
- Use the `ClientConfiguration.setConnectionTTL` method

By default, the SDK will attempt to reuse HTTP connections as long as possible.

In failure situations where a connection is established to a server that has been brought out of service, having a finite TTL can help with application recovery.

## SDK Necessity

Following changes cannot be made through the AWS Management Console, which is why the AWS SDK is required.

The AWS SDK enables you to change default client configurations, which is helpful when you want to:

- Change HTTP transport settings, such as connection timeout and request retries
- Specify TCP socket buffer size hints

## AmazonServiceException

AmazonServiceException is the most common exception that you'll experience when using the AWS SDK for Java. This exception represents an error response from an AWS service.

- When you encounter an AmazonServiceException, you know that your request was successfully sent to the AWS service but couldn't be successfully processed
- This can be because of errors in the request's parameters or because of issues on the service side.

---

# Docker Host

In your development environment, you probably run all three containers together on your Docker host. You might be tempted to use the same approach for your production environment, but this approach has several drawbacks:

- Changes to one container can impact all three containers, which may be a larger scope for the change than you want

- Each container is more difficult to scale because you have to scale every container proportionally

- Task definitions can only have 10 container definitions and your application stack might require more, either now or in the future

- Every container in a task definition must land on the same container instance, which may limit your instance choices to the largest sizes

---

# AWS Direct Connect

Direct Connect provides Amazon Web Services (AWS) customers with a way to transfer data that does not involve using the public Internet.

Say your company uses multiple Amazon VPCs and is setting up a new office.

- Establish a private connection from your new office’s network to your Amazon VPC using AWS Direct Connect
- This option provides predictable network performance, reduces bandwidth costs, and doesn’t require that customers be responsible for implementing high availability solutions for all VPN endpoints.
- The downside of this option (possible requiring additional telecom and hosting provider relationships) is mitigated in this instance because the office is new and would need to provision a whole new network anyway.

AWS Direct Connect uses dedicated, private network connections between your intranet and Amazon VPC. Use AWS Direct connect:

- If you have an immediate need, have low to modest bandwidth requirements, and can tolerate the inherent variability in Internet-based connectivity.
- A VPC VPN Connection utilizes IPSec to establish encrypted network connectivity between your intranet and Amazon VPC over the Internet.

## Scenario

You are managing your company's data and preparing for a migration to an AWS cloud environment. One of the first steps you are doing is uploading data to Amazon S3 buckets. You are uploading large amounts of data overnight, 3 to 5 TB to an S3 bucket. What steps can you take to decrease the amount of time to perform these uploads?

Using AWS Direct Connect, data that would have previously been transported over the Internet can now be delivered through a private network connection between AWS and your datacenter or corporate network. In many circumstances, private network connections can reduce costs, increase bandwidth, and provide a more consistent network experience than Internet-based connections.

---

# AWS VPN CloudHub

AWS VPN CloudHub is a hub-and-spoke VPN technology offered by AWS.

CloudHub allows your remote sites to communicate with one another over VPN tunnels that are created between your AWS Virtual Private Gateway (VPG) and your remote sites.

## hub-and-spoke

The hub-spoke model, sometimes called the “shared services” model, relies on VPC Peering connections between the hub VPC and each spoke VPC.

What are the Benefits of Hub-Spoke Model?
Network isolation. Ensure services of one tenant are not affected by the others.

- https://www.logicworks.com/blog/2019/11/common-aws-architecture-template/

## Scenario

Which protocols are available with IPsec to provide confidentiality?

- MD5 and SHA are hashing protocols related to data integrity, not confidentiality.
- PSK is short for Pre-Shared Keys. RSA and PSK are related to key exchange.
- The only correct IPsec configuration covering confidentiality. DES, 3DES, and AES are all encryption protocols.

---

# AWS VPC

## Scenario

You have recently configured an AWS VPC for a new client. The VPC consists of several EC2 instances in a public subnet. An IT administrator for one of your clients has called you about an inability to connect to an EC2 instance. He is trying to connect from a Windows laptop using Putty for Windows and the .pem file you provided him.

- If you use PuTTY to connect to your instances verify that your private key (.pem) file has been converted to the format recognized by PuTTY (.ppk).
- PuTTY does not natively support the private key format (.pem) generated by Amazon EC2.
- PuTTY has a tool named PuTTYgen, which can convert keys to the required PuTTY format (.ppk).
- You must convert your private key into this format (.ppk) before attempting to connect to your instance using PuTTY.

---

# AWS API Gateway

API Gateway only supports HTTPS endpoints.

- It does not support creating FTP endpoints.
- It does not support SSH endpoints.

## HTTP(s) Verbs

- TRACE is not supported by API Gateway

## Stage Variables

Users can use API Gateway stage variables to reference a single AWS Lambda function with multiple versions and aliases, such as alpha, beta, and production.

---

# AWS Elastic Load Balancer

You are creating an application that stores extremely sensitive financial information. All information in the system must be encrypted at rest and in transit.

- Terminating SSL terminates the security of a connection over HTTP, removing the S for "Secure" in HTTPS. This violates the "encryption in transit" requirement in the scenario.

If at least one instance behind the ELB is working, then the ELB is working.

So out of many instances if one fails, the ELB will stop sending requests to the failed instance.

- If no working instance exist ELB will start sending 504 Gateway Timeout errors?

---

# AWS Route53

Registring a domain:

1. Checks with TLD, if the domain is available
2. Route53 creates a zone file for the domain being registered

   - Zone file is database about all the DNS information about a domain

3. Route53 allocates name server for the zone, and adds the zone file to the name servers
4. As part of the domain registration, it communicates with the TLD registry and add these zone file to the TLD.

   - This records are added by adding the NS records

NOTE: inside Route53 records are referred to as `recordsets`.

---

# Keys

To ensure that an encryption key was not corrupted in transit, `Elastic Transcoder` uses a **`MD5`** digest of the decryption key as a checksum.

- The key MD5 must be exactly 16 bytes long and then base64-encoded, and **is required when you use AES decryption**.

## Scenario

River, Zoe, and Wash are members of the same team. Their team has two Customer Master Keys (CMKs): CMK1 and CMK2.

- The key policy for CMK1 allows access to the AWS account (root user) in general.
- The key policy for CMK2 allows access to River and Wash.
- River has no IAM policy.
- Zoe's IAM policy allows all AWS KMS actions for all CMKs.
- Wash’s IAM policy denies all AWS KMS actions for all CMKs.

To which CMK, if any, does Zoe have access?

- Only CMK1
- CMK1's key policy allows access to the AWS account (root user) and `thereby enables IAM policies to allow access to CMK1`.
- She cannot access CMK2 because the key policy for CMK2 does not allow access to the account. `Key policy takes precedence over IAM policy`.

---

# KMS

With Key Management Service you can

- create custom encryption keys,
- can manage your custom keys and
- can assign other accounts as users to these keys.

EBS volumes and snapshots of volumes encrypted using custom keys can be shared with other accounts as long as these accounts have been granted user rights on the custom key.

## Key management infrastructure (KMI)

- KMI does not have a concept of a data layer.
- KMI does not have a concept of an encryption layer.

The storage layer is responsible for storing encryption keys. The management layer is responsible for allowing authorized users to access the stored keys.

## Keys Request

When a data key is requested, AWS KMS returns both the encrypted and plaintext key back to:

- the service or application that requested it

The plaintext data key is used to encrypt the user's data in memory.

## Sharing Default Keys

Each account has set of default keys, those default keys can't be shared

- so unless you use custom keys you can't assign these to other accounts.

## Synchronizing Across Regions

You cannot synchronize or move/copy keys across regions

- Instead, users must specify a KMS key that is valid in the destination Region.

---

# AWS Secrets Manager

## Scenario

A company is migrating a legacy application to Amazon EC2. The application uses a user name and
password stored in the source code to connect to a MySQL database. The database will be migrated to an
Amazon RDS for MySQL DB instance. As part of the migration, the company wants to implement a secure
way to store and automatically rotate the database credentials?

- Store the database credentials in AWS Secrets Manager. Configure Secrets Manager to automatically
  rotate the credentials.

---

# Security Group

What is an effective way to determine which security group rules are unused?

Unused rules can be determined by analyzing active network traffic using `VPC Flow Logs` that post to `Amazon CloudWatch logs`.

---

# Amazon ECS

As you monitor Amazon ECS, store historical monitoring data so that you can compare it with current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues.

To establish a baseline, you should, at a minimum, monitor the following items:

- The `CPU and memory reservation and utilization metrics` for your Amazon ECS clusters
- The `CPU and memory utilization metrics` for your Amazon ECS services

## Task placement strategy

A task placement strategy is an algorithm for selecting instances for task placement or tasks for termination.

Amazon ECS supports the following task placement strategies.

`binpack` - Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.

`random` - Place tasks randomly.

`spread` - Place tasks evenly based on the specified attribute.

> You can use task placement constraints to prevent tasks from launching on cluster instances with certain properties.

## CLI

- The Amazon ECS CLI basic configuration information is stored in ~/.ecs/config file.
- The Amazon ECS CLI makes calls to AWS APIs on your behalf by using these credentials.

---

# Amazon EC2

# IP Address

When you stop an Amazon EC2 instance, its public IP address is released. When you start it again, a new public IP address is assigned. If you require a public IP address to be persistently associated with the instance, `allocate an Elastic IP address`.

## AMI

Copying a source AMI results in an identical but distinct target AMI with its own unique identifier. I

## Auto Scaling Groups (ASG)

You create collections of EC2 instances, called Auto Scaling groups.

- You can specify the `minimum number of instances` in each Auto Scaling group
- You can specify the `maximum number of instances` in each Auto Scaling group
- You specify the `desired capacity`, either when you create the group or at any time thereafter (Auto Scaling ensures that your group has this many instances)
- You specify the `scaling policies`, (Auto Scaling can launch or terminate instances as demand on your application increases or decreases)

NOTE: ASG can terminate or automatically replace the failed instance?

## Cluster placement groups

Cluster placement groups are logical groupings of instances in the same AZ.

- AWS recommends placement groups for applications that can benefit from low latency and/or high network throughput, should be deployed in a cluster placement group.
- Deploying instances into two separate AZs would place them on separate hardware, decreasing performance compared to instances in a cluster placement group.

Performance in Placement Groups is optimized when instances communicate using `**private IP addresses**`.

- P2 instances are optimized for accelerated computing and would be preferred over the T2 instances (which do not support Enhanced Networking) for this application.

## SSH Access

Permissions 0777 for '.ssh/my_private_key.pem' are too open.

- Restrict the access settings on my_private_key.pem to allow read and write privileges only for yourself.
- If your private key can be read or written to by anyone but you, then SSH ignores your key and you see the warning message.

SSH key pairs and security group rules do not have any built-in expiration, and SSH is enabled as a service by default. It is available even after restarts.

- Security groups do not expire.

## Status and Health Check

- Auto Scaling uses EC2 status checks to detect hardware and software issues with the instances.
- Load balancer performs health checks by sending a request to the instance and then waiting for a 200 response code, or by establishing a TCP connection (for a TCP-based health check) with the instance

Even if you've configured an Elastic Load Balancer, the autoscaler does not automatically check the ELBs health checks.

- You must add the ELB Health Check to your autoscaling policy.

## Failed System Check

If a system status check has failed for a EC2 instance, you can try one of the following options:

- Stop and then restart the instance.
- Post your issue to the Amazon EC2 forum.
- Retrieve the system log and look for errors.

## Scenario

The company you work for has a PHP application which is growing quickly and also needs to be highly available. For now, there are small burstable general purpose instances in the VPC. You were asked to make it elastic as well so you implemented an auto scaling group and have instances in multiple Availability Zones for high availability. The application is requiring unexpected compute resources. What other steps could you take to optimize this environment? (Choose 2 answers)

- Launch larger instances and remove some smaller ones.
- ~~Create a second auto scaling group for high availability.~~
- ~~Create a multi-AZ environment to offload read traffic.~~
- Launch some compute optimized instances.

## Scenario - Monitoring

CPU, network, and disk activity are metrics that are visible to the underlying host for an instance.

> Because memory is allocated in a single block to an instance and is managed by the guest OS, the underlying host does not have visibility into consumption. This metric would have to be delivered to CloudWatch as a custom metric by using the agent.

---

# AWS Lambda

When working with a published version of the AWS Lambda function, you should note that the:

- code and the configuration information cannot be changed

## AWS Lambda functions locally

AWS SAM (Server Application Model) CLI allows you to test the Lambda function locally.

## Versioning

When you publish a version, AWS Lambda makes a snapshot copy of the Lambda function `code and configuration` in the $LATEST version.

- A published version is immutable.
- You can't change the code or configuration information.

Versioning allows you to better manage your in-production Lambda function code. As a result, you can work with different variations of your Lambda function in your development workflow, such as development, beta, and production.

## CreateEventSourceMapping function

CreateEventSourceMapping identifies a stream as an event source for a Lambda function.

- It can be either an Amazon Kinesis stream or an Amazon DynamoDB stream.
- AWS Lambda invokes the specified function when records are posted to the stream.

## Stream Based Events - POLL

For stream-based event sources, AWS Lambda polls your stream and invokes your Lambda function. If your Lambda function is `throttled`

- AWS Lambda attempts to process the throttled batch of records until the time the data expires.

## Stream Based Events - PULL

The "pull model" where AWS Lambda pulls the updates from the stream and invokes your function. In this case, you must grant permission to both:

- pull from the stream (invocationrole)
- execute (executionrole).

## S3 Events

Amazon S3 can send an event to a Lambda function when an object is created or deleted.

- You configure notification settings on a bucket, and
- grant Amazon S3 permission to invoke a function on the function's resource-based permissions policy.

## Creating Lambda function

What are some options for creating a Lambda function which is to be invoked through the Amazon API Gateway? Assume that a deployment package has already been created.

- Call the Lambda CLI `create-function` command, uploading the deployment package from your local machine in the process.
- Upload your deployment package to an Amazon S3 bucket. Then call the Lambda CLI `create-function` command and specify the bucket and package name.
- Use the AWS Lamdba console.

## CodeStorageExceededException

CodeStorageExceededException means:

- You have exceeded your maximum total code size per account.

AWS Lambda Limits:

- Total size of all the deployment packages that can be uploaded per region: 75 GB
- Lambda function deployment package size (compressed .zip/.jar file): 50 MB
- Size of code/dependencies that you can zip into a deployment package (uncompressed .zip/.jar size): 250 MB
- Total size of environment variables set: 4 KB

## Concurrent Execution Limit

The default limit for concurrent executions is 1,000 with Lambda; however, `this is a soft limit` that can be raised by contacting AWS Support.

## In-place deployment

AWS Lambda does not support in-place deployments. This is because, after a function version has been published, it cannot be updated.

---

# AWS Elastic Beanstalk

With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS cloud without worrying about the infrastructure that runs those applications. You simply upload your application, and Elastic Beanstalk automatically handles the details of

- capacity provisioning
- load balancing
- scaling
- application health monitoring

~~Elastic Beanstalk uses the `Amazon Simple Notification Service (Amazon SNS)` to notify you of important events affecting your application.~~ ???

Enviroment Tiers in Beanstalk?

- If the application is designed to manage HTTP requests, it will be run in an `Web Server environment`.
- If the application pulls data from an SQS queue, it will be run in an `Worker environment`.

## Web Server Tier

Web Server Tier will be using the following AWS resources:

- **Route 53**: Directs web traffic to right servers
- **Elastic Load Balancer**: For every environment we should have at least one Elastic Load Balancer. This automatically distributes incoming application traffic and scales resources to meet traffic demands.
- **Auto Scaling**: For every environment you will have a auto scaling group that will manage the `capacity planning` of your application. This will add and remove EC2 instances according to the demand of the traffic.
- **EC2 Instances**: This will be part of the auto scaling group.
- **Security Groups**: EC2 instances will be governed by Security Groups. By default port 80 is open to everyone.

## Worker Tier

Elastic Beanstalk installs a daemon on each Amazon EC2 instance in the Auto Scaling group to process Amazon SQS messages in the worker environment.

- The daemon pulls data off the Amazon SQS queue
- inserts it into the message body of an HTTP POST request and
- sends it to a user-configurable URL path on the local host.

Worker Tier will be using the following AWS resources:

- **SQS Queue**: If you dont have an SQS Queue operational, as part of the creation of the worker tier, it will be created for you.
- **Auto scaling group**: created so that the performance isnt impacted based on load. Each EC2 is attached to auto scaling group and reads from SQS Queue.
- **IAM Service Role**: Unlike web server tier, worker tier uses a daemon to pull request from the SQS queue. The daemon then sends the data to the application allowing it to process the message. This is why Instance Profile Role is required to read from a queue.

## Deamon and Host Manager

Worker Tier uses a Daemon.

Web Server Tier uses a Host Manager which is responsible for:

- patching the instances
- monitoring the application log and the server
- publish log files to S3
- collecting metrics and events
- aids in deployment of the application

## Scaling

During rolling updates, Elastic Beanstalk will apply a separate timeout to each batch in the operation. The timeout is also set as part of the environment's rolling update configuration. if all instances in the batch are healthy within the command timeout, the operation will continue to the next batch. If not, the operation fails.

## Health Check

For Web Tier, ELB sends a health check to Auto Scaling group every 10 second.

For worker tier (environment that doesn have ELB), health of this environment is determined by EC2 instance health check.

Types of status check:

System Status Check

- If this fails, its like an issue with the underlying host.
- These failures are out of our control and are managed by AWS.
- The best way to resolve this is stop the instance and restart it.

Instance Status Check

- If this fails, it likely require our input.
- This looks at the EC2 instance rather than the underlying host.
- This is usually resolved by checking the network configuration.

### Default Checks

Some checks are also provided by the Elastic Beanstalk:

For Web Environment

- Route 53 is pointing to correct ELB
- Security group has port 80 inbound is allowed

For Worker Environment

- It will check that the SQS queue being used is polled every 3 minute at a minimum.

## Monitoring

Enhanced health reporting is a feature that you can enable on your environment to allow AWS Elastic Beanstalk to gather additional information about resources in your environment.

- These metrics can be sent to CloudWatch as custom metrics.

You can view health status in real time by using the environment overview page of the Elastic Beanstalk console or the eb health command in the Elastic Beanstalk command line interface (EB CLI).

### Instance Profile

Enhanced health requires the environment to have an instance profile. The instance profile should have roles that provide permissions for your environment instances to collect and report enhanced health information.

## Pricing

Elastic Beanstalk runs at no additional charge. You incur charges only for services deployed.

---

# CloudFormation

CloudFormation performs actions securely across multiple regions and accounts if configured to do so.

The template will describe all of your resources and their configurations without having to worry about _service dependency_. CloudFormation will handle the order of deployment.

Its easy to configure CloudFormation to `notify you of the status of deployment` through its integration with SNS (Simple Notification Service).

Core components of CloudFormation:

- Stacks
- Templates
- Stack Sets
- Designer

A stack, can include all the resources required to run a web application, such as a web server, a database, and networking rules.

## Template

```yml
AWSTemplateFormatVersion: AWS CloudFormation template version that the template conforms to.

Description: A text string that describes the template.

Metadata: Objects that provide additional information about the template.

Parameters: Values to pass to your template at runtime.

Rules: Validates a parameter or a combination of parameters passed to a template during a stack creation or stack update.

Mappings: use to specify conditional parameter values

Conditions:
  - Conditions that control whether certain resources are created
  - or whether certain resource properties are assigned a value during stack creation or update.
  - For example, you could conditionally create a resource that depends on whether the stack is for a production or test environment.

Transform:
  - For serverless applications, specifies the version of the AWS Serverless Application Model (AWS SAM) to use.
  - use AWS SAM syntax to declare resources in your template.
  - use AWS::Include transforms to work with template snippets that are stored separately from the main AWS CloudFormation template.

Resources:
  - Specifies the stack resources and their properties, such as an Amazon Elastic Compute Cloud instance or an Amazon Simple Storage Service bucket.

Outputs:
  - Describes the values that are returned whenever you view your stack's properties. Say, while using "aws cloudformation describe-stacks" AWS CLI command
```

## Stack Instance

A stack set, lets you create stacks in AWS accounts across regions by using a single CloudFormation template.

- Stack Sets use a component known as `Stack Instance`. They are simply references to another stack in a **different AWS account in a particular region** and **they belong to a single stack set.**

Stack set cannot apply changes to only one stack within the set.

- It is possible to delete stacks from within the stack set, without deleting the other stacks.
- To delete the stack set, you must delete all the stack instances.

You can choose a IAM role that has permission to allow creation of resources by CloudFormation.

- If you dont choose one, CloudFormation will apply temporary permission based on the current account.

## Stack Creation Failure

If account limits were preventing the launch of additional instances, the stack creation process would fail as soon as AWS CloudFormation attempts to launch the instance (the Amazon EC2 API would return an error to AWS CloudFormation in this case)

## Stack Deletion

All the resources that were created by the stack such as s3 buckets, iam users, etc will be deleted when we delete the stack from CloudFormation.

## Stack Deletion Failure

Say, the stack created a user and you attached a policy to the user. The stack would fail to delete if you dont detach the policy.

## cfn-signal

cfn-signal: Used to signal with a CreationPolicy or WaitCondition, so you can synchronize other resources in the stack.

Any issues preventing the instance from calling `cfn-signal` and sending a success/failure message to AWS CloudFormation would cause the `CreationPolicy` to time out.

## Scenario - Order of resource allocation

An AWS CloudFormation template declares two resources: an AWS Lambda function and an Amazon DynamoDB table. The function code is declared inline as part of the template and references the table. In what order will AWS CloudFormation provision the two resources?

- In above scenario, its not possible to determine this ahead of time

Because the reference to the Amazon DynamoDB table is made as part of an arbitrary string (the function code), AWS CloudFormation does not recognize this as a dependency between resources.

- To prevent any potential errors, you would need to declare explicitly that the function depends on the table.

---

# Elastic Beanstalk vs CloudFormation

- Elastic Beanstalk is for deployments.
- CloudFormation is a provisioning mechanism that supports Elastic Beanstalk.

---

# AWS S3

`Amazon S3 Transfer Acceleration` enables fast, easy, and secure transfers of files over long distances between your client and an S3 bucket. Transfer Acceleration takes advantage of Amazon CloudFront’s globally distributed edge locations. As the data arrives at an edge location, data is routed to Amazon S3 over an optimized network path.

- When your globally used application is hosted only in one region, you can use this.

## Multipart Upload

Multipart upload allows you to upload a single object as a set of parts. Each part is a contiguous portion of the object's data. You can upload these object parts independently and in any order. You can upload parts in parallel to improve throughput.

## Bucket Policy

Using an S3 Bucket Policy, you can specify who can access the bucket

- what time of day the can access
- from which CIDR block
- by IP address

Each Amazon S3 bucket and object has an ACL (Access Control List) associated with it. An ACL is a list of grants identifying

- the grantee and
- the permission granted

## Encryption

What are two separate ways to ensure data is encrypted in-flight both into and out of S3?

To encrypt your S3 objects in-flight,

- you need to use the TLS endpoint; alternatively
- you can encrypt the data yourself on the client side before upload.

## Cost

Amazon Simple Storage Service (Amazon S3) analytics

- Use this feature to analyze storage access patterns to help you decide when to transition the right data to the right storage class.
- This feature observes data access patterns to help you determine when to transition less frequently accessed STANDARD storage to the STANDARD_IA storage class.

Cost allocation Amazon S3 bucket tags

- A cost allocation tag is a key-value pair that you associate with an Amazon S3 bucket.
- To manage storage data most effectively, you can use these tags to categorize your Amazon S3 objects and filter on these tags in your data lifecycle policies.

## Column data

Amazon S3 Select enables applications to retrieve only a subset of data from an object by using simple SQL expressions.

If you have column headers, and you set the FileHeaderInfo to:

- **USE**: you can identify columns by name

## Scenario - Thumbnail processing

Users should be able to upload photos, which will then be displayed as thumbnails

- In this scenario, you may choose to create a Lambda function that creates a thumbnail automatically.
- Your Lambda function code can read the photo object from the S3 bucket, create a thumbnail version, and then save it in another S3 bucket.
- `You must use two bucket`s. If you use the same bucket as the source and the target, each thumbnail uploaded to the source bucket triggers another object-created event, which then invokes the Lambda function again, creating an unwanted recursion.

## Scenario - Different encryption keys with versioning

A user has enabled versioning on an S3 bucket. The user applies server side encryption for data at rest. If the user is supplying his own keys for encryption (SSE-C)

- It is possible to have different encryption keys for different versions of the same object

## Scenario - Sharing an object with a presigned URL

Jani has just joined your DevOps team. As part of her DevOps credentials, she now has access to list objects in a bucket, but she doesn't have access to download these objects according to bucket policy. You asked Jani to generate pre-signed URLs to some objects in this bucket and share them with employees. Will Jani be able to generate working pre-signed URLs to these objects? No.

All objects by default are private. Only the object owner has permission to access these objects.

- However, the object owner can optionally share objects with others by creating a presigned URL.
- The presigned URLs are valid only for the specified duration (until the expiration date).

If you have a video in your bucket and both the bucket and the object are private, you can share the video with others by generating a presigned URL.

The credentials that you can use to create a presigned URL include:

- IAM instance profile
- IAM user
- AWS Security Token Service

## Scenario - Deletion Protection

You store your company’s critical data in Amazon Simple Storage Service (Amazon S3). The data must be protected against accidental deletions or overwrites. How can this be achieved?

- versioning-enabled buckets enable you to recover objects from accidental deletion or overwrite.
- lifecycle policies are used to transition data to a different storage class and do not protect objects against accidental overwrites or deletions.

## Scenario - Access logs

You have a set of users that have been granted access to your Amazon S3 bucket. For compliance purposes, you need to keep track of all files accessed in that bucket. To have a record of who accessed your Amazon Simple Storage Service (Amazon S3) data and from where, what should you do?

- To track requests for access to your bucket, enable access logging. Each access log record provides details about a single access request, such as the requester, bucket name, request time, request action, response status, and error code (if any). Access log information can be useful in security and access audits.

---

# AWS CloudFront

You can use geo restriction, also known as `geoblocking`, to prevent users in specific geographic locations from accessing content that you're distributing through a CloudFront web distribution.

To use geo restriction, you have two options:

- Use the CloudFront geo restriction feature. Use this option to restrict access to all of the files that are associated with a distribution and to restrict access at the country level.
- Use a third-party geolocation service. Use this option to restrict access to a subset of the files that are associated with a distribution or to restrict access at a finer granularity than the country level.

---

# AWS EBS

Any application which has **performance sensitive workloads** and requires **minimal variability** with dedicated EC2 to EBS traffic should use

- provisioned IOPS EBS volumes, which are attached to an EBS-optimized EC2 instance or
- it should use an instance with 10 Gigabit network connectivity.
- Launching an instance that is EBS-optimized provides the user with a dedicated connection between the EC2 instance and the EBS volume.

## Monitoring

VolumeQueueLength monitors the number of requests sent for processing.

- The queue depth is the number of pending I/O requests from your application to your volume.
- For maximum consistency, a Provisioned IOPS volume must maintain an average queue depth (rounded to the nearest whole number) of one for every 1,000 Provisioned IOPS in a minute.

---

# AWS Storage Gateway

- all gateway-cached volume data and snapshot data is stored in Amazon S3 encrypted at rest using server-side encryption (SSE)
- you cannot access this data with the Amazon S3 API or with other tools such as the Amazon S3 console

---

# Amazon RDS

## Available Database Engines

1. MySQL - Best open-source RDBMS
2. MariaDB - Community-developed fork of MySQL
3. PostgreSQL - Close 2nd preferred open-source DB
4. Aurora - AWS's own fork of MySQL
5. Oracle - Common in coorporate environments
6. SQL Server - A microsoft database

## Multi AZ

When Multi AZ is configured, a secondary RDS instance is deployed within a different availability zone within the same region as the primary instance.

- The primary purpose of the second instance is to provide a failover option for your primary RDS instance.

- The replication of data between the primary RDS database and the secondary replica instance happens synchronously.

## Failover

The Amazon RDS Service Level Agreement requires that you follow best practice guidelines.

One of them is to test failover for your DB instance

- to understand how long the process takes for your use case and
- to ensure that the application that accesses your DB instance can automatically connect to the new DB instance after failover.

Failover will occur in a multi-AZ configuration to a standby server.

Read replicas are for scaling, not high availability, and are not involved in the failover process.

## Scenario - Migration

How would you migrate an Amazon Relational Database Service (Amazon RDS) layer between two stacks in the same region?

- Supply the connection information to the second stack as custom JSON to ensure that the instances can connect.
- Remove the Amazon RDS layer from the first stack.
- Add the Amazon RDS layer to the second stack.
- Remove the connection custom JSON.

> you cannot associate a single Amazon RDS database instance with multiple stacks at the same time.

---

# Amazon Redshift

- fast, fully-managed, petabyte-scale data warehouse
- based on PostgreSQL 8.0.2 (contains a number of differences)

Used for:

- Data cleansing
- Extract, Transform and Load (ETL)

---

# DynamoDB

AWS Lambda polls a DynamoDB stream and invokes a Lambda function when updates are made to the DynamoDB table. What happens when the Lambda function is throttled?

- AWS Lambda attempts to process the throttled batch of records until the time the data expires.

## Document Types

The document types are list and map. These data types can be nested within each other, to represent complex data structures up to 32 levels deep.

There is no limit on the number of values in a list or a map, as long as the item containing the values fits within the DynamoDB item size limit (400 KB).

- List: can store an ordered collection of values
- Map: can store an unordered collection of name-value pairs
- Sets: all the elements within a set must be of the same type. each value within a set must be unique

```txt




local secondary index and
global secondary index





```

## Scan and Query

Scans are less efficient than queries. When possible, always use queries with DynamoDB.

- Also, strongly consistent read would actually be a more expensive query in terms of compute and cost.

---

# AWS Kinesis

A typical Amazon Kinesis Streams application reads data from an Amazon Kinesis stream as data records. These applications can use the Amazon Kinesis Client Library, and they can run on Amazon EC2 instances.

The processed records can be sent to dashboards, used to generate alerts, dynamically change pricing and advertising strategies, etc.

## Concurrent Usage

Multiple Kinesis Data Streams applications can consume data from a stream, so that multiple actions, like archiving and processing, can take place concurrently and independently. For example, two applications can read data from the same stream.

- The first application calculates running aggregates and updates an Amazon DynamoDB table
- The second application compresses and archives data to a data store like Amazon Simple Storage Service (Amazon S3).

## Integration with AWS Lambda

Kinesis and Lambda integration works using a stream-based model where AWS Lambda polls the stream and when it detects new records invokes your Lambda function by passing the new records as a parameter.

- AWS Lambda invokes a Lambda function using the `RequestResponse invocation type (synchronous invocation)` by polling the Kinesis Stream.

## With AWS Lambda throttled

For stream-based event sources, AWS Lambda polls your stream and invokes your Lambda function. If your Lambda function is `throttled`

- AWS Lambda attempts to process the throttled batch of records until the time the data expires.

## Scenario

You need to replicate API calls across two systems in real time. What tool should you use as a buffer and transport mechanism for API call events?

- AWS Kinesis is an event stream service. Streams can act as buffers and transport across systems for in-order programmatic events, making it ideal for replicating API calls across systems.

## Re-Sharding

Kinesis Streams supports re-sharding which enables you to adjust the number of shards in your stream in order to adapt to changes in the rate of data flow through the stream.

- The shard or pair of shards that the re-sharding operation acts on are referred to as `parent shards`.
- The shard or pair of shards that result from the re-sharding operation are referred to as `child shards`.
- After you call a re-sharding operation, you need to `wait for the stream to become active` again.
- When you re-shard, data records that were flowing to the parent shards are `rerouted to flow to the child shards based on the hash key values` that the data record partition keys map to.

## Scenario - Number of Shards

You use Amazon Kinesis Data Streams to process messages. Each message is 0.5 KB. You expect 10,000 messages per second. How many shards should you provision?

Each shard supports a write throughput of 1 MB per second. You expect about 5 MB of messages. Therefore, you should provision at least 5 shards. It would be a good idea to provision 6 shards to have some margin for fluctuation.

---

# AWS Kinesis Firehose

Kinesis Firehose provides a managed service for aggregating streaming data and `inserting it into RedShift`.

RedShift also supports ad-hoc queries over well-structured data using a SQL-compliant wire protocol, so the business team should be able to adopt this system easily.

## Apache Parquet or ORC format conversion

Kinesis Data Firehose supports Columnar data formats such as Apache Parquet and Apache ORC.

## Support for multiple data destinations

- Amazon S3
- Amazon Redshift
- Amazon OpenSearch Service
- HTTP endpoints,
- Datadog
- New Relic
- MongoDB
- Splunk

You can specify the destination Amazon S3 bucket, the Amazon Redshift table, the Amazon OpenSearch Service domain, generic HTTP endpoints, or a service provider where the data should be loaded.

## Encryption

Amazon Kinesis Data Firehose provides you the option to have your data automatically encrypted after it is uploaded to the destination.

- As part of the delivery stream configuration, you can specify an AWS Key Management System (KMS) encryption key.

## Monitoring

Amazon Kinesis Data Firehose exposes several metrics through the console, as well as Amazon CloudWatch, including

- volume of data submitted
- volume of data uploaded to destination
- time from source to destination
- the delivery stream limits
- throttled records number
- upload success rate

## Pricing

With Amazon Kinesis Data Firehose, you pay

- only for the volume of data you transmit through the service, and
- if applicable, for data format conversion.

---

# Amazon SQS

You are getting a lot of empty receive requests when using Amazon SQS. What can you do to reduce this load?

- Use as long a poll as possible, instead of short polls.
- Long polling allows the Amazon SQS service to wait until a message is available in the queue before sending a response.

Each Amazon SQS message queue is independent within each region.

- You cannot share messages between regions.
- Pricing is the same in all regions except China.
- You can transfer data between Amazon SQS and Amazon EC2 or AWS Lambda free of charge within a single region.
- When you transfer data between Amazon SQS and Amazon EC2 or AWS Lambda in different regions, you will be charged the normal data transfer rate.

## Queue URLs sample

    http://sqs.us-east-1.amazonaws.com/123456789012/myqueue

## Handling downtime

The use of an SQS queue allows submitted requests to be retained as messages in the SQS queue until the application resumes normal operation and can process the requests.

## Instead of in memory cache

SQS queues are preferable to in-memory caches because in-memory storage will operate at all times and can be fairly expensive to address an issue that only comes up during spikes.

## Visibility Timeout

- This is used to hide a given message from other consumers while its getting processed
- If the visibility timeout expires, and the consumer does not send a delete message request, the message will become visible again in the queue.
- The value of visibility timeout should be longer than the time it takes for consumer to process the message.

## Queue Types

Standard queues can have messages out of order

FIFO queues maintain the order of messages and doesnt contain duplicates

Dead-Letter Queue is not used as a source queue to hold messages submitted by producers.

- This is instead used by the source queue to send messages that fail processing
- By analysing the messages in the dead letter queue, it might be possible to identify the cause of failure is from producer or consumer

---

# SQS vs SNS

SNS is a distributed publish-subscribe system. _Messages are pushed to subscribers as and when they are sent by publishers to SNS_.

SQS is distributed queuing system. Messages are not pushed to receivers. Receivers have to poll SQS to receive messages. Messages can be stored in SQS for short duration of time.

## Key Differences

**Entity Type**

- SQS : Queue (similar to JMS, MSMQ).
- SNS : Topic-Subscriber (Pub/Sub system).

**Message consumption**

- SQS : Pull Mechanism — Consumers poll messages from SQS.
- SNS : Push Mechanism — SNS pushes messages to consumers.

**Persistence**

- SQS : Messages are persisted for some duration is no consumer available. The retention period value is from 1 minute to 14 days. The default is 4 days.
- SNS : No persistence. Whichever consumer is present at the time of message arrival, get the message and the message is deleted. If no consumers available then the message is lost.

In SQS the message delivery is guaranteed but in SNS it is not.

**Consumer Type**

- SQS : All the consumers are supposed to be identical and hence process the messages in exact same way.
- SNS : All the consumers are (supposed to be) processing the messages in different ways.

---

# AWS CodePipeline

Continuous Integration (Commit -> Build -> Test)

Continuous Delivery (Push Button deployment)

![test img](../cloud-academy--aws-associate/60-code-pipeline/Screenshot%20from%202021-09-29%2023-30-20.webp?raw=true)

Location of source code:

- S3 bucket
- AWS CodeCommit repository
- GitHub repository

Build phase options:

- Jenkins
- AWS CodeBuild
- Solano CI

Use any of the following deployment provider:

- ECS
- CloudFormation
- CodeDeploy
- Elastic Beanstalk

You have multiple option available to mold a perfect pipeline.

- **Pipeline Action Types**: Has six action types as described above.
- **Invoke Lambda Functions**: This option under the Pipeline Action types allows us to invoke the lambda function.
- **CodePipeline CloudWatch Events**: You can use this to detect and react to stage changes.

  - This allows you to model in notifications to SNS topics, which can then kickoff lambda function (which may perform some other action in your build and release process)
  - Pipeline within CodePipeline are by default configured to use CloudWatch Events whenever a new commit is detected in CodeCommit.
  - Instead this behaviour can be swapped for making CodePipeline poll periodically for changes in the source repository.

## Editing Pipeline

Can be done using:

- Console (On the pipeline details page, choose Release change. This starts the most recent revision available in each source location specified in a source action through the pipeline).

- CLI (Once done, run the pipeline revision manually using or run the `start-pipeline-execution` command respectively).

## Scenario

If you are configuring a Jenkins project and

- it is not installed on an Amazon EC2 instance, or
- it is installed on an Amazon EC2 instance that is running a Windows operating system,

then

- fill out the fields as required by your proxy host and port settings, and
- provide the credentials of the IAM user you configured for integration between Jenkins and AWS CodePipeline.

---

# AWS CodeCommit

We are able to clone the repository using the access keys saved under `~/.aws/credentials`.

(Demo: Under the AWS CodeCommitUser, we select the option to generate git credentials for HTTPS. We use these generated credentials to login into CodeCommit)

## Encryption

Repositories are automatically encrypted at rest.

- No customer action is required.
- AWS CodeCommit uses AWS Key Management Service (KMS) to perform this encryption.

## Authentication

You can authenticate to CodeCommit using:

- HTTPS (port 223)
- SSH (port 22)

You can access CodeCommit with following identity types:

- IAM User (recommended)
- Amazon Web Services account root user
- IAM role, with temporary credentials are useful in the following situations:
  - Federated user access (AWS assigns a role to a federated user when access is requested through an identity provider.)
  - Cross-account access (Configure cross-account access to an AWS CodeCommit repository using roles )
  - AWS service access (For example, you can create a role that allows AWS Lambda to access a CodeCommit repository on your behalf.)
  - Applications running on Amazon EC2

## Credentials

To retrieve users temporary credentials:

- Web identities require the `assume-role-with-web-identity` command
- Federated identities require the `get-federated-token` command

## Repository protection

AWS recommends using IAM policies along with MFA-protection to restrict users who can delete repositories.

---

# AWS CodeBuild

- Fully managed build service
- Build docker images and then register them to ECR
- Can be triggered automatically via commits

`BuildSpec` file instructs CodeBuild to compile the docker image.

CodeBuild uses yaml based BuildSpec file. Here you specify the build phases and then the list of artifacts.

- **artifacts**: Represents information about where CodeBuild can find the build output and how CodeBuild prepares it for uploading to the S3 output bucket.
- **phases**

  - install
  - pre_build
  - build
  - post_build

In the Console you specify:

- What to Build (You can specify AWS CodeCommit)
- How to Build (You can use image managed by AWS CodeBuild)
  - Use the `buildspec.yml` file in the source directory
- Where to put build artifacts (Amazon S3)
- Service Role (Create or choose existing service role)
- VPC (Select the one which AWS CodeBuild will access)

---

# AWS CodeDeploy

CodeDeploy service is used to deploy your build artifacts stored in S3 onto the web servers or the lamba servers.

- CodeBuild is used to build the artifacts.
- These artifacts are then given to CodeDeploy for deployment.

Uses an agent based method for deployment on servers, whether they are `EC2 instances` or `on-prem servers`.

- The CodeDeploy agent is not required for deployments that target the `serverless lambda servers`.

> When we create an instance

- We add bash script to install CodeDeploy agent.
- We add SSH and HTTP access to the newly created security group.

> When we create a CodeDeploy setup

- CodeDeploy requires a service role. Here we give `AWSCodeDeployRole` under the policy.

CodeDeploy uses _JSON or YAML_ based `AppSpec file` to drive the deployment depending on the deployment endpoint.

- If you are deploying lambda function to the lambda servers, you can use JSON or YAML file.
- If you are deploying to an EC2 instance or on-prem server, then you must use YAML only.

AppSpec file also gives us ability to define `commands that run before and after the deployment`. Eg. The script within AppSpec file runs a script that is used to start an nginx server.

**Deployment group** is a group of servers that has CodeDeploy agent installed and for which you want to deploy a version or revision. Deployment groups can be specified by:

- specifying autoscaling groups
- EC2 instances indentified by tags
- on-prem servers identified by tags

What are the parameters that you need to specify for a deployment in AWS CodeDeploy?

- Revision
- Deployment group
- Deployment configuration

**CodeDeploy Deployment Group Triggers** allows us to `register an SNS topic` to receive deployment event messages. You can also subscribe to the topic to perform any downstream actions.

## Stopping in-flight deployment

When you stop an in-flight deployment, the AWS CodeDeploy service will instruct the agent on each instance to stop executing additional scripts.

- If you need to get your application back to a consistent state, you can either redeploy the revision, or deploy another revision.

## Deployment Types

- Canary: uses one or more function updates in a single action to act as a test, before updating the rest of the functions.
- All-at-once: as it would result in all functions being updated at the same time.
- Linear: deployment configuration enables you to deploy to percentage-based increments with a set delay between each group of functions.

## Scenario -

If you are using AWS CodeDeploy to deploy a set of 10 AWS Lambda functions and would like to ensure that two functions are updated every 3 minutes, which deployment configuration would be most suitable?

- Linear

## Scenario - Health Check

You have set up a deployment group with 10 instances in AWS CodeDeploy, and your minimum healthy instances is at 9. In this scenario, what will AWS CodeDeploy do?

- It deploys the revision to one instance at a time.
- If any of these deployments fail, AWS CodeDeploy immediately fails the overall deployment.

If you had set up a deployment group with 10 EC2 instances in AWS CodeDeploy, and your minimum healthy instances parameter is at 8.

- It deploys the revision to two instances at a time.
- If the revision deployment fails, AWS CodeDeploy immediately fails the overall deployment.

## Scenario - File Conflict

If your AWS CodeDeploy configuration includes creation of a file, nginx.conf, but the file already exists on the server (prior to the use of AWS CodeDeploy), what is the default behavior that will occur during deployment?

- The deployment will fail
- CodeDeploy will not remove files that it does not manage. This is maintained as a list of files on the instance.

## Deletion of custom deployment configuration

What will happen if you delete an unused, custom deployment configuration in AWS CodeDeploy?

- You cannot delete a custom deployment configuration that is still in use.
- You will no longer be able to associate the deleted deployment configuration with new deployments and new deployment groups. `This action cannot be undone`.

## Deletion of application with CodeDeploy

Deleting an application with AWS CodeDeploy removes information about the application from the AWS CodeDeploy system, including all related deployment group information and all related deployment details.

- It does not remove any related application revisions from instances on which the revisions may be installed
- nor does it delete revisions from Amazon S3 buckets where the revisions may be stored.
- It also does not terminate any Amazon EC2 instances or deregister any on-premises instances.
- This action cannot be undone.

---

# AWS CloudTrail

By default, AWS CloudTrail uses S3 server-side encryption with Amazon S3-managed encryption keys (SSE-S3) to encrypt the content of log files. Once the content is encrypted, it is delivered to the S3 storage bucket.

---

# Amazon CloudWatch

What predefined permission policy grants your Lambda function permissions for ONLY the Amazon CloudWatch Logs actions to write logs?

- AWSLambdaBasicExecutionRole
- You can use this policy if your Lambda function does not access any other AWS resources except writing logs.

MTTR (Mean time to recovery) may include outages, bugs, security hole, etc.

MTTD (Mean time to discovery) refers how quickly we are identifying problems that were introduced into the production system.

Lead Time - Time between feature request and feature release.

## Amazon CloudWatch Events

Amazon CloudWatch Events can be set up to point to AWS CodePipeline as a target.

- CloudWatch Events can be used to automatically start the CodePipeline

## Agent Install

When installing the CloudWatch Logs agent, no additional networking configuration is required as long as your instance can reach the CloudWatch API endpoint.

You can use AWS Systems Manager agents to install and start the agent, but it is not required to install the Systems Manager agent alongside the CloudWatch Logs agent

The agent must be started before new log data is sent to CloudWatch Logs.

---

# AWS X-Ray Daemon

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

# SAML

`AssumeRoleWithSAML` returns a set of temporary security credentials for users who have been authenticated via a SAML authentication response. The temporary security credentials returned by this operation consist of

- an access key ID
- a secret access key
- a security token

Applications can use these temporary security credentials to sign calls to AWS services.

---

# Identity Federation

During the federation process, one party would act as an Identity Provider, known as an IdP, and the other would be the Service Provider, an SP.

The identity provider authenticates the user, and the service provider controls access to their service or resources based on IdPs authentication.

When the identity provider has been selected, and the user is authenticated by that provider, an assertion is sent back to the service provider, say Medium. And this assertion is a message that contains metadata and attributes about the user such as their username. This assertion allows the service provider to authorize access to their services.

## Identity Standards

Using many different identity standards, including:

- OAuth 2.0
- OpenID Connect (OIDC)
- SAML 2.0, (Security Assertion Markup Language)

AWS enables you to easily configure access for contractors and other 3rd parties, in addition to allowing scalable access to authenticate to your mobile and web applications.

### Oauth

OAuth 2.0 is an open standard authorization protocol, and any developer can implement it. Designed to work with Hypertext Transfer Protocol (HTTP), it allows access tokens to be issued to third-party clients by an authorization server with the approval of the resource owner. The third party then uses the access token to access the protected resources hosted by the resource server.

### OpenID Connect

OpenID Connect is a simple identity layer on top of the OAuth 2.0 protocol, which allows computing clients to verify the identity of an end-user based on the authentication performed by an authorization server, as well as to obtain basic profile information about the end-user.

### SAML 2.0

Security Assertion Markup Language 2.0 (SAML 2.0) is a standard for exchanging authentication and authorization identities between identity and service providers.

## AWS Services for Identity Federation

AWS Organizations, they provide a means of centrally managing and categorizing multiple AWS accounts that you own, bringing them together into a single organization.

### AWS Single Sign-On (SSO)

This service has primarily been designed for users to easily access multiple accounts within their AWS Organization enabling a single sign-on approach negating the need to provide credentials for each account.

- AWS SSO can also be used to manage access to cloud-based apps using SAML 2.0 such as Office 365 and Salesforce.

### AWS Identity and Access Management

Identity federation allows you to access and manage AWS resources even if you don't have a user account within IAM.

---

# Amazon Cognito

Login information of alot of coorporate employees is probably already stored, for example in Microsoft Active Directory service. We dont want them to create yet another username and password for our custom new application.

Well, all these pain points can be resolved by using Amazon Cognito.

Amazon Cognito has been built purely to enable the simplification of enabling secure authentication and access control for new and existing users accessing your web or mobile applications, rather than your AWS account.

- It not only integrates well with SAML 2.0 but also web identity federation as well.
- One of the biggest features of Amazon Cognito is that it has the capability to scale to millions of new users which is great when working with mobile applications.

![test img](../cloud-academy--aws-associate/63-identity-federation/Screenshot%20from%202021-09-30%2000-23-16.webp?raw=true)

## Using user pools

A user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito. Your users can also sign in through social identity providers like Google, Facebook, Amazon, or Apple, and through SAML identity providers.

### Attributes

While the user pool is created, we choose from the list of standard attributes or define our custom attributes to be captured from the user.

All the attributes information captured is stored within the Cognito User pool and can be accessed by your application when you need it.

### Account Recovery

The service even includes account recovery features that can be quite annoying to set up on your own, and would normally require another backend service to handle for you: Including email and phone.

### Third-party ID providers

If you dont want your users to go through the custom sign up, your users can also sign in for your application by using third-party ID providers.

Using third-party ID providers require you (the application developer) to first set up a developer account with those external third-party providers and set up your application with them.

You can also sign in with any SAML (Security Assertion Markup Language) identity provider. For example, your SAML ID provider might be:

- an active directory federation service
- on-premises AD
- or one you are even hosting on an EC2 server.

### Lambda Integration

User pools also have integrations with AWS lambda.

### User Pools Authentication Flow

Lets examine how the authentication flow is handled by your application and the service.

- Your user will be presented with a login screen or terminal of some sort from within your application. They will submit their credentials, either from having created an account directly or by logging into a third-party provider.

- The application will call the `InitiateAuth` operation with those credentials. This API call kicks off the authentication flow. It directly indicates to amazon Cognito that you want to authenticate.

- If the call is successful - Cognito will respond either with a token or with a challenge.

- When the client is ready to respond back to the server(cognito), they can reply with `RespondToAuthChallenge` and provide whatever information the challenge requires back.

- If the user fails the challenge, you can have Cognito set up to resend a new one. This can include multiple rounds until the user is successful or fails out.

- If successful Cognito will shoot back some tokens for the client to use.

![test img](../cloud-academy--aws-associate/64-cognito/Screenshot%20from%202021-09-30%2000-37-14.webp?raw=true)

## Using identity pools (federated identities)

Amazon Cognito identity pools - also known as `Federated Identities`, _provide temporary AWS credentials for users who are guests (unauthenticated)_ and for users who have been authenticated and received a token.

- Can work in tandem with Amazon Cognito User Pools
- Can federate with public providers

An identity pool is a store of user identity data specific to your account.

### Types of Identities

Identity pools define two types of identities: authenticated and unauthenticated.

To gain the authenticated state, a user must be authenticated by a public login provider. This can be:

- Amazon Cognito user pool
- Public ID providers
- Open ID connect provider

You can also use Unauthenticated identities to act as a sort of guest pass for when you want people to have some access to basic services and later prompting them to sign in or sign up.

### Role

Each type of identity has a role that goes along with it. Roles have policies attached to them, that set the permissions for what that user is allowed to do within AWS.

### Identity Pools Authenciation Flow

- The first part is that you application needs to have the user sign in with Cognito user pools and that can take place using either the user pool itself, a social sign-in, your SAML-backed authentication service
- At this stage, an Identity Token is sent by the IDP back to the Cognito user Pool.
- Now Cognito does not store the credentials by the IDP or forward this to your mobile app, instead, the IDP token will be normalized into a standard token, called a `Cognito User Pool Token, or CUP token` and this will be used and stored by Cognito.
  - > This essentially means that it doesn’t matter if the user authenticated via an account in the User Pool, or federated access, all tokens will be standardized
- Now some services do not allow you to use the CUP tokens for authentication. You might have to use the Identity Pool to authenticate.
  - The CUP Token can be sent to the Identity Pool, where an `STS Token (Security Token Service)` will be created based off of your CUP token, and this will be sent back to your application.
- With these AWS credentials, your application will now be allowed to call upon those other AWS services.

### GetFederationToken

GetFederationToken returns a set of temporary security credentials (consisting of an access key ID, a secret access key, and a security token) for a federated user.

- You call the GetFederationToken action using the long-term security credentials of an IAM user.

## Enable or edit authentication providers

If you allow your users to authenticate using public identity providers (e.g. Amazon Cognito user pools, Login with Amazon, Sign in with Apple, Facebook, Google), you can specify your application identifiers in the Amazon Cognito identity pools (federated identities) console. This associates the application ID (provided by the public login provider) with your identity pool.

## Amazon Cognito Sync

It ensures that every instance of a given identity is notified when identity data changes.

- Helps to sync your application’s user data across various platforms.
- When syncing information back to Cognito - the smallest amount of data you can operate on is an entire dataset.
  - All generic read and write operations are performed locally
  - When you decide to sync the information, the entire dataset is synchronized all at once.

---

# AWS SSO vs Amazon Cognito

Amazon Cognito is for authenticating users while AWS SSO is for authenticating employees.

Amazon Cognito is our identity management solution `for developers building B2C or B2B apps for their customers`, which makes it a customer-targeted IAM and user directory solution.

AWS SSO is `focused on SSO for employees accessing AWS and business apps`, initially with Microsoft AD as the underlying employee directory.

---

# AWS SSO

It is used to help you implement a federated access control system providing a portal to your users allowing them to access multiple accounts within your AWS Organization without having to supply IAM credentials for each one.

## Choosing Identity Pool

You then have the decision of deciding which identity pool you want to have AWS SSO use as your source of users.

You can either use:

- an external identity pool supported by AWS SSO with SAML two, such as Azure Active Directory or Okta Universal Directory
- you can choose to manage your users with the default identity user store that comes natively with AWS SSO

---

# Amazon Route 53

## Amazon Route 53 and DNS Records

Amazon Route 53 is the domain name management service provided by AWS. The domain name management system or DNS is responsible for translating domain names to IP addresses.

### Registring domain using Route 53

When you use Amazon Route 53 to register a domain, the service becomes the authoritative DNS server for the domain and creates a public hosted zone.

- A public zone defines how traffic is routed on the public internet.
- A private zone defines how traffic is routed inside a virtual private cloud or VPC.

VPCs intended to be used with Private Zones need to have following enabled in their configuration.

- DNS Hostname and
- DNS Support

### Records creation

Amazon Route 53 creates a set of `4 unique NS records` and `1 SOA record` in each hosted zone created.

Following two records are essential to integrating your domain to the existing DNS system (Private and Public Hosted Zones).

**Name Server (NS) records**: used to identify the DNS servers for a given hosted zone.

**Start of Authority (SOA) record**: used to define the authoritative DNS servers for an individual DNS zone.

### Types of records supported

**A record**: used to map a hostname to an IP address. An A record is used for IPv4 address.

**AAAA record**: also used to map a hostname to an IP address. The AAAA record is used for IPv6 addresses.

**Mail exchange (MX) record**: used to identify email servers for a given domain.

- You can have more than one and set the priority using a number.
- For example, you may have a primary email server with a priority of 10 and a secondary email server with a priority of 20.
- The lowest number record is used first.

**Text (TXT) record**: used to provide information in a text format to systems outside of your domain.

**Canonical name (CNAME)**: used to map a hostname to another hostname.

- This can be used to map multiple names to the same host.
- For example, when a server needs to respond as webserver using the hostname WWW and mail server using the hostname MAIL at the same time.

### Route 53 specific record type

**Alias record type** is unique to Amazon Route 53 and maps a custom hostname in your domain to an AWS Resource which is usually represented by an internal AWS name.

- For example, CloudFront distributions, Amazon S3 buckets, and Elastic Load Balancers provide you a domain name that is internal to AWS.
- You can use an alias record to define a custom name to that resource.
- You can also use Alias records to map to apex records which are the top nodes of a DNS namespace like on example.com or cloudacademy.com

### Record attributes

When you create a record using Route 53 you specify:

- the record name
- the record type
- the actual value
- the Time-To-Live in seconds
- the Routing policy for this record

The Time to Live specifies the amount of time the record is considered valid. The same record result obtained before is used in the future and DNS won’t be queried again until the TTL has expired.

The Routing policy for a record defines how to answer a DNS query. Each type of policy does something different including the possible use of health checks.

## Amazon Route 53 Health Checks

When you create a health check, Route 53 sends requests to the endpoint every 30 seconds.

Health checks allows you to independently verify different tiers of your application before the actual total application is considered healthy.

### CloudWatch Alarms

With Route 53 health checks you can also monitor the state of a cloud watch alarm.

- The health check status is `healthy` when the alarm is in the **OK state**.
- The health check status is `unhealthy` when the alarm status is in the **ALARM state**.
- You can also `choose what the health check status is` when the alarm is in the **INSUFFICIENT state**. Possible health check values in this case are healthy, unhealthy or “last known status”.

### Record Health Check

When Route 53 receives a query it chooses a record based on the routing policy, it then determines the current health of the selected record by checking the status of the health check for that record and responds to the query with the value of a healthy record.

- Unhealthy records are not considered.
- If you do not associate a health check with a record, Route 53 treats those records as always healthy.

> You can use the list of recommended health checkers by region or customize the list to the regions specific to your business.

### Endpoint Health Check

Endpoint health checks can be specified by IP address or by domain name. The health check protocol can be:

- TCP
- HTTP
- HTTPS

For the HTTP-related protocols, you can use an optional string matching where you indicate that Route 53 is to `search the response body for the string specified`. Route 53 considers the endpoint healthy only if the string specified appears entirely within the first 5120 bytes of the response body.

## Amazon Route 53 Routing Policy

The Routing policy for a record defines how to answer a DNS query.

Different types of routing policy are:

- Simple routing policy
- Weighted routing policy
- Geolocation routing policy
- Geo-proximity routing policy
- Failover routing policy
- Latency routing policy
- Multi value Answer routing policy

### Simple routing policy

This provides the IP address associated with a name.

With Simple routing an A record is associated with one or more IP addresses.

- A random selection will choose which IP to use.

> It is important to note that Simple Routing policies do not support health checks. All other routing policies do.

### Weighted routing policy

This is similar to simple routing and you can define a weight per IP address.

- A value of 0 suggests a record is never returned.
- This is useful for simple load distribution or testing new software.

### Geolocation routing policy

This tags records with a location that can be Default, Continent or Country.

What does this do and what is this used for ???

### Geo-proximity routing policy

This requires that you use Route 53’s traffic Flow feature and create a `Traffic Policy`. A traffic policy is a resource that combines one or more routing policies.

Geo-proximity routing is based on distance and a defined bias. You can specify a `Bias value` from -99 to 99.

- Route more traffic to an endpoint by using a positive value
- Route less traffic to an endpoint by using a negative value.

### Failover routing policy

This policy is able to route traffic to a primary resource and based on a health check re-direct traffic to a secondary resource. `The re-direction happens if the health check fails`.

### Latency routing policy

This policy chooses the record with the lowest latency to the customer.

You define multiple records with the same name and assign a region to each record.

AWS maintains a database of latency between the general location of users and the regions tagged in DNS records.

> This may not always be the closest resource, especially if the closest resource is saturated. And this looks for a healthy record.

### Multi value Answer routing policy

This returns multiple IP addresses to a query. Up to 8 IP addresses corresponding to healthy records based on a health check are returned.

## Amazon Route 53 Traffic Flow

Traffic flow simplifies the process of creating and maintaining records in large and complex configurations.

- This is useful when you have a group of resources that perform the same operation, such as a fleet of web servers for the same domain.

### Visual Editor

The traffic flow visual editor lets you create complex sets of records and see the relationships among them.

### Traffic Policy

Each configuration is called a `traffic policy` and it’s automatically versioned so you don’t have to start all over again when your configuration changes.

- Old versions of traffic policies continue to be available until you delete them.

### Policy Record

A traffic policy can define hundreds of records. Traffic flow takes care of automatically creating the actual records as per the policy record.

- A policy records associates traffic policies with a domain or subdomain name records.
- The traffic policy record appears in the `list of records for the hosted zone`.
- You can use the same traffic policy to `create records in multiple public-hosted zones`.
- This is useful when you’re using the same hosts for multiple domains.

Please note that the `Geo-proximity routing policy` is available only if you use traffic flow.

## Amazon Route 53 Resolver

The Route 53 resolver is the DNS service for VPCs that integrates with your data center.

Connectivity needs to be established between your data center DNS and AWS using:

- a Direct Connect (DX)
- a Virtual Private Network (VPN) connection.

### Inbound and Outbound DNS queries

**Inbound DNS queries** allow queries that originate in your data center to resolve AWS-hosted domains.

**Outbound DNS queries** are enabled using conditional forwarding rules.

- Domains hosted in your data center can be configured as forwarding rules in Route 53 resolver.
- Rules trigger when a query is made to one of those domains and the request is forwarded to your data center.

### DNS Firewall

You use a `firewall rule group` to define how Route 53 Resolver DNS firewall inspects and filters traffic coming from your VPC.

Each `rule` consists of a **`domain list`** to inspect in DNS queries and an action to take when a query results in a match.

You can allow a matching query:

- to go through
- allow it to go through with an alert or
- you can block it and respond with a default or a custom response

## Amazon Route 53 Application Recovery Controller

- monitors an application’s ability to recover from failures
- controls application recovery across multiple availability zones, regions, and possibly your own data center environments

### Readiness Check

You can define a readiness check to monitor:

- AWS resource configurations, capacity, and network routing policies.
- Auto Scaling Groups
- Amazon EC2 instances
- Amazon EBS volumes
- Elastic Load Balancers
- RDS instances
- DynamoDB tables, etc.

These readiness checks ensure that the recovery environment is scaled and configured to take over when needed.

- You can check `AWS service limits` to verify that enough capacity can be deployed.
- You can also `verify the capacity and scaling setups` for applications to be exactly the same across regions before a failover takes place.

### Routing Control

A routing control is used to turn traffic flow ON or OFF to individual cells in Regions or Availability Zones.

Readiness Checks work with Routing Controls to give you a way to failover an entire application based on custom conditions like application metrics, partial failures, increased error rates, or latency.

- You can also failover manually.
- With Routing Controls `you can shift traffic` for maintenance purposes or during a real failure scenario.
- You can also `apply safety rules` to routing controls as a way to prevent a failover to an unprepared replica.

---

# AWS X-Ray

X-Ray is able to trace only requests upon entry into your systems. Connectivity issues between a client and your resources cannot be traced.

---
