# Misc

Note that the number of duplicates due to producer retries is usually low compared to the number of duplicates due to consumer retries.

- https://docs.aws.amazon.com/streams/latest/dev/kinesis-record-processor-duplicates.html
- https://docs.aws.amazon.com/streams/latest/dev/kinesis-record-processor-scaling.html

AWS CodeStar - makes it easy to centrally monitor application activity and manage day-to-day development tasks such as recent code commits, builds, and deployments for a project.

Amazon CodeGuru - provides intelligent recommendations to improve the quality of your codebase and for identifying an application’s most “expensive” lines of code in terms of resource intensiveness, CPU performance, and code efficiency.

WAF on API Gateway and on the ALB, protects against common internet attacks, including application layer attacks.

Integrate CloudWatch Events with Lambda, which will automatically trigger the function every 30 minutes.

You should retry original requests that receive server (5xx) or throttling errors. However, client errors (4xx) indicate that you need to revise the request to correct the problem before trying again.

The consolidated billing feature in **AWS Organizations** allows you to consolidate payment for multiple AWS accounts.

To avoid security issues, it is of utmost importance to test the impact of service control policies (SCPs) on your IAM policies and resource policies before applying them. The **IAM policy simulator** evaluates the policies that you choose and determines the effective permissions for each of the actions that you specify.

Your sign-in page URL has the following format, by default:

- https://<Your_AWS_Account_ID>.signin.aws.amazon.com/console/
- https://<Your_Alias>.signin.aws.amazon.com/console/

Memcached over Redis if you need to run large nodes with multiple cores or threads.

# Cloud

In the cloud, resources are elastic, meaning they can instantly grow or shrink to match the requirements of a specific application.

There are two basic types of elasticity:

1. Time-based: Time-based elasticity means turning off resources when they are not being used, such as a development environment that is needed only during business hours.
2. Volume-based: Volume-based elasticity means matching scale to the intensity of demand, whether that’s compute cores, storage sizes, or throughput.

You can also use a combination of ELB and Auto Scaling to maximize the elasticity of your architecture. Beyond Auto Scaling for Amazon EC2, you can use Application Auto Scaling to automatically scale resources for other AWS services, including:

- Amazon ECS
- Amazon EC2 Spot Fleets
- Amazon DynamoDB

# Networking

## VPC

Can a VPC span across regions?

[./summary-plus#vpc-peering](./summary-plus#vpc-peering)

## AWS Direct Connect

[./summary-plus#aws-direct-connect](./summary-plus#aws-direct-connect)

## Stateful vs Stateless Firewalls

## NACL, Security Group and ENI relationship

NACL: Stateless [./vpc.md#network-access-control-list-nacl](./vpc.md#network-access-control-list-nacl)

Security Group: Stateful [./vpc.md#vpc-security-groups](./vpc.md#vpc-security-groups)

ENI: [./summary-plus.md#eni](./summary-plus.md#eni)

## NAT

Under the lack of abundant public IPv4 addresses, gives private CIDR range outgoing internet access.

[./vpc.md#network-address-translation-nat](./vpc.md#network-address-translation-nat)

# Security

For certificates in a Region supported by AWS Certificate Manager (ACM), it is recommended that you use ACM to provision, manage, and deploy your server certificates. In unsupported Regions, you must use **IAM as a certificate manager**.

- Use IAM as a certificate manager only when you must support HTTPS connections in a Region that is not supported by ACM.

**IAM Role vs Access Keys**: If you have resources that are running inside AWS, that need programmatic access to various AWS services,then the best practice is to always use IAM roles. However, for applications running outside of an AWS environment, these will need access keys for programmatic access to AWS resources.

**Container Instance IAM Role** only applies if you are using the EC2 launch type. Not with a Fargate launch type.

**Service-linked Role** is a unique type of IAM role that is linked directly to Amazon ECS itself and not on the ECS task.

TDE automatically encrypts data before it is written to storage, and automatically decrypts data when the data is read from storage.

- Amazon RDS supports using Transparent Data Encryption (TDE) to encrypt stored data on your DB instances running Microsoft SQL Server.

Take note that SCPs are necessary, but not sufficient, for granting access for the accounts in your organization. You still need to attach IAM policies to users and roles in your organization’s accounts to actually grant permissions to them.

The bucket will use server-side encryption with Amazon S3-Managed encryption keys (SSE-S3) to encrypt the data using 256-bit Advanced Encryption Standard (AES-256) block cipher. **x-amz-server-side-encryption** must be used during the upload.

To upload an object to the S3 bucket which uses _SSE-KMS_, you have to send a request with an **x-amz-server-side-encryption** header with the value of **aws:kms**. When you upload an object, you can specify the KMS key using the **x-amz-server-side-encryption-aws-kms-key-id** header. If the header is not present in the request, Amazon S3 assumes the default KMS key.

S3 when using _SSE-C_ requires **x-amz-server-side​-encryption​-customer-algorithm**, **x-amz-server-side-encryption-customer-key** and **x-amz-server-side-encryption-customer-key-MD5** headers on the upload request headers to be passed during upload request.

**AWS Secrets Manager** is an AWS service that makes it easier for you to manage secrets. Secrets can be database credentials, passwords, third-party API keys, and even arbitrary text. You can store and control access to these secrets centrally by using the Secrets Manager console, the Secrets Manager command line interface (CLI), or the Secrets Manager API and SDKs.

- Also, you can configure Secrets Manager to automatically rotate the secret for you according to a schedule that you specify.
- Systems Manager Parameter Store doesn’t rotate its parameters.

**Encrypt operation** is primarily used to encrypt RSA keys, database passwords, or other sensitive information. This operation can also be used to move encrypted data from one AWS region to another however, this is _not recommended if you want to encrypt your data locally_. You have to use the **GenerateDataKey operation** instead.

Envelope encryption - Encrypt plaintext data with a data key and then encrypt the data key with a top-level plaintext master key.

# API Gateway

API Gateway Lambda Authorizer: When a client makes a request to one of your API’s methods, API Gateway calls your **Lambda authorizer**, which takes the caller’s identity as input and returns an IAM policy as output.

There are two types of Lambda authorizers:

– A **token-based Lambda authorizer** (also called a TOKEN authorizer) receives the caller’s identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token.
– A **request parameter-based Lambda authorizer** (also called a REQUEST authorizer) receives the caller’s identity in a combination of headers, query string parameters, stageVariables, and $context variables.

For a Lambda function, you can have two types of integration:

– Lambda proxy integration
– Lambda custom integration

![img](https://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-my-resource-get-method-execution-boxes.png)

Amazon API Gateway does not support unencrypted (HTTP) endpoints. By default, Amazon API Gateway assigns an internal domain to the API that automatically uses the Amazon API Gateway certificate. When configuring your APIs to run under a custom domain name, you can provide your own certificate for the domain.

The base URL for REST APIs is in the following format:

> https://{restapi_id}.execute-api.{region}.amazonaws.com/{stage_name}/

"Effect": "Allow", "Action": ["execute-api:InvalidateCache"] allows any request to invalidate cache results in API Gateway.

You can use the API Gateway Import API feature to import a REST API from an external definition file (on-premise) into API Gateway. Currently, the Import API feature supports OpenAPI v2.0 and OpenAPI v3.0 definition files.

# S3

**put-bucket-policy** command can only be used to apply policy at the bucket level, not on objects. You can use S3 Access Control Lists (ACLs) instead to manage permissions of S3 objects.

# EBS

After attaching the newly created EBS volume to the Linux EC2 instance, Create a file system on this volume.

- After you attach an Amazon EBS volume to your instance, it is exposed as a block device. You can format the volume with any file system and then mount it.

If an EBS volume is the root device of an instance, you must stop the instance before you can detach the volume.

# DynamoDB

To avoid potential throttling, the provisioned write capacity for a global secondary index should be equal or greater than the write capacity of the base table since new updates will write to both the base table and global secondary index.

DynamoDB adaptive capacity automatically boosts throughput capacity to high-traffic partitions. However, each partition is still subject to the hard limit. This means that adaptive capacity can’t solve larger issues with your table or partition design. To avoid hot partitions and throttling, optimize your table and partition structure.

- You can add a random number to the partition key values to distribute the items among partitions (Sharding Using Random Suffixes), or you can use a number that is calculated based on something that you are querying on (Sharding Using Calculated Suffixes).

About local secondary indexes:

– For each partition key value, the total size of all indexed items must be 10 GB or less.
– When you query this index, you can choose either eventual consistency or strong consistency.
– Queries or scans on this index consume read capacity units from the base table

Global secondary indexes:

- Queries on this index support eventual consistency only.

Suppose that each item is 4 KB and you set the page size to 40 items. A Query request would then consume only 20 eventually consistent read operations or 40 strongly consistent read operations.

- A larger number of smaller Query or Scan operations would allow your other critical requests to succeed without throttling, instead of using large page size.

You may use ElastiCache as your database cache, it will not reduce the DynamoDB response time to microseconds unlike DynamoDB DAX.

# RDS

When switching form RDS database to RDS Read Replicas or ElastiCache Cluster, note that ElastiCache Cluster requires more code change than switching to RDS Read Replicas.

RDS - Enable slow query log in RDS, to collect all SQL statements that took longer.

# Cache

Caching strategies

- Lazy loading - Your data store then returns the data to your application. Your application next writes the data received from the store to the cache.
- Write-through
- Russian doll - this strategy configures your cache to have nested records
- Adding TTL

# Cloudfront

Origin Access Identity (OAI): You can configure an S3 bucket as the origin of a CloudFront distribution. OAI prevents users from viewing your S3 files by simply using the direct URL for the file. Instead, they would need to access it through a CloudFront URL.

If you’re using the domain name that CloudFront assigned to your distribution, such as dtut0ria1sd0jo.cloudfront.net, you can change the **Viewer Protocol Policy setting** for one or more cache behaviors to **require HTTPS communication** by setting it as either Redirect HTTP to HTTPS, or HTTPS Only.

**If your origin is an Elastic Load Balancing load balancer**, you can use a certificate provided by AWS Certificate Manager (ACM). You can also use a certificate that is signed by a trusted third-party certificate authority and imported into ACM. Note that you can’t use a self-signed certificate for HTTPS communication between CloudFront and your origin.

Configuring the ALB to use its default SSL/TLS certificate is incorrect because **there is no default SSL certificate in ELB**, unlike what we have in CloudFront.

# Lambda

Exponential Backoff: In addition to simple retries, each AWS SDK implements exponential backoff algorithm for better flow control. The idea behind exponential backoff is to use progressively longer waits between retries for consecutive error responses. Jitters are used to randomise retry intervals.

Lambda@Edge: Lets you execute functions that customize the content that CloudFront delivers. You can author Node.js or Python functions in one Region, US East (N. Virginia), and then execute them in AWS locations globally that are closer to the viewer.

Using Signed URLs and Signed Cookies in CloudFront to distribute th firmware update file is incorrect because although this solution provides a way to authenticate the premium users for the private content, the process of authentication has a significant latency in comparison to the Lambda@Edge solution. In this option, you have to refactor your application (which is deployed to a specific AWS region) to either create and distribute signed URLs to authenticated users or to send Set-Cookie headers that set signed cookies on the viewers for authenticated users. This will cause the latency, which could have been improved if the authentication logic resides on CloudFront edge locations using Lambda@Edge.

You can use Lambda functions to change CloudFront requests and responses at the following points:

– After CloudFront receives a request from a viewer (viewer request)
– Before CloudFront forwards the request to the origin (origin request)
– After CloudFront receives the response from the origin (origin response)
– Before CloudFront forwards the response to the viewer (viewer response)

Errors for Lambda creation

- CodeStorageExceededException - You have exceeded your maximum total code size per account.
- InvalidParameterValueException - One of the parameters in the request is invalid.
- ResourceConflictException - The resource already exists, or another operation is in progress.
- ResourceNotFoundException - The resource specified in the request does not exist.
- ServiceException - The AWS Lambda service encountered an internal error.
- TooManyRequestsException - The request throughput limit was exceeded.

There are two types of concurrency controls available:

Reserved concurrency – Reserved concurrency guarantees the maximum number of concurrent instances for the function. When a **function has reserved concurrency, no other function can use that concurrency**. There is **no charge for configuring reserved concurrency for a function**.

Provisioned concurrency – Provisioned concurrency initializes a requested number of execution environments so that they are prepared to respond immediately to your function's invocations. Note that configuring **provisioned concurrency incurs charges to your AWS account**.

For some applications this risk of rejection is not acceptable. In these cases the engineer can reserve a set number of executions for any given lambda, guaranteeing that they will always be available, using the Reserved Concurrency parameter.

Reserved executions can be set on any or all of the lambdas, in any combination. However, AWS reserves 100 executions to the common pool at all times. So, if the account has a limit of 1000, the maximum number of combined Reserved Concurrency would be 900.

![img](https://miro.medium.com/max/791/1*SkZDXoSs6toRBJ1a-xHshg.png)

With this setup, the reserved concurrency (aka reserved executions) will always remain untouched by the other lambdas even if the lambda for which they were reserved is not using them.

For Lambda functions that process Kinesis or DynamoDB streams, the number of shards is the unit of concurrency. If your stream has 100 active shards, there will be at most 100 Lambda function invocations running concurrently. This is because Lambda processes each shard’s events in sequence.

You invoke your Lambda function using the Invoke operation, and you can specify the invocation type as synchronous or asynchronous. In the Invoke API, you have 3 options to choose from for the InvocationType:

- `RequestResponse` (default) – Invoke the function synchronously. Keep the connection open until the function returns a response or times out. The API response includes the function response and additional data.
- `Event` – Invoke the function asynchronously. Send events that fail multiple times to the function’s dead-letter queue (if it’s configured). The API response only includes a status code.
- `DryRun`

# EC2

An EC2 instance needs to have access to the Internet, via the Internet Gateway or a NAT Instance/Gateway in order to access S3.

- S3 is not part of your VPC, unlike your EC2 instances, EBS volumes, ELBs, and other services that typically reside within your private network. An EC2 instance needs to have access to the Internet, via the Internet Gateway or a NAT Instance/Gateway in order to access S3. Alternatively, you can also create a VPC endpoint so your private subnet would be able to connect to S3.

![img](https://media.tutorialsdojo.com/amazon-s3-vpc-endpoint.PNG)

# ECS

The Amazon ECS container agent is included in the Amazon ECS-optimized AMIs, but you can also install it on any Amazon EC2 instance that supports the Amazon ECS specification.

Amazon ECS supports the following task placement strategies:

- binpack – Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.
- random
- spread

When using the spread strategy, you must also indicate a field parameter. It is used to indicate the bins that you are considering. The accepted values are **instanceID**, **host**, or a **custom attribute key:value** pairs such as attribute:ecs.availability-zone to balance tasks across zones.

Placement strategy sample:

```json
"placementStrategy": [
  {
    "field": "attribute:ecs.availability-zone",
    "type": "spread"
  }
]
```

# Elastic BeanStalk

Elastic Beanstalk recommends one of two methods for performing platform updates.

- Method 1 – Update your Environment’s Platform Version – This is the recommended method when you’re updating to the latest platform version, **without a change in runtime, web server, or application server versions**, and **without a change in the major platform** version. This is the most common and routine platform update.

- Method 2 – Perform a Blue/Green Deployment – This is the recommended method when you’re updating to a different runtime, web server, or application server versions, or **to a different major platform version**.

Package your application as a **zip** file and deploy it using the **eb deploy** command.

# SQS

- ReceiveMessageWaitTimeSeconds
- [SQS short and long polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html)

Amazon SQS FIFO queues follow exactly-once processing. It introduces a parameter called Message Deduplication ID, which is the token used for deduplication of sent messages. Add a MessageDeduplicationId parameter to the SendMessage API request. Ensure that the messages are sent at least 5 minutes apart.

# Kinesis

Kinesis Client Library

- you should ensure that the number of instances does not exceed the number of shards (except for failure standby purposes).
- Each shard is processed by exactly one KCL worker and has exactly one corresponding record processor, so you never need multiple instances to process one shard.
- However, one worker can process any number of shards, so it’s fine if the number of shards exceeds the number of instances.

The purpose of resharding in Amazon Kinesis Data Streams is to enable your stream to adapt to changes in the rate of data flow.

- You split shards to increase the capacity (and cost) of your stream.
- You merge shards to reduce the cost (and capacity) of your stream.

It is better to use Kinesis instead of Lambda for the real-time data analytics application.

# Deployment

In-place deployment: The application on each instance in the deployment group is stopped, the latest application revision is installed, and the new version of the application is started and validated. You can use a load balancer so that each instance is deregistered during its deployment and then restored to service after the deployment is complete.

- Only deployments that use the EC2/On-Premises compute platform can use in-place deployments.
- AWS Lambda compute platform deployments cannot use an in-place deployment type.

Performing a Canary deployment is incorrect because this type of deployment is primarily used in Lambda and not in Elastic Beanstalk.

# SAM

The AWS Serverless Application Model (AWS SAM) is an open source framework for building serverless applications. It provides shorthand syntax to express functions, APIs, databases, and event source mappings. You define the application you want with just a few lines per resource and model it using YAML.

[./summary-plus#sam](./summary-plus#sam)

SAM deployment

- sam package
- sam deploy

# CloudFormation

Cloudfromation deployment

- aws cloudformation package
- aws cloudformation deploy

For serverless applications (also referred to as Lambda-based applications), the **Transform** section **specifies the version of the AWS Serverless Application Model (AWS SAM) to use**. When you specify a transform, you can use AWS SAM syntax to declare resources in your template. The model defines the syntax that you can use and how it is processed. More specifically, the `AWS::Serverless` transform, which is a macro hosted by AWS CloudFormation, takes an entire template written in the AWS Serverless Application Model (AWS SAM) syntax and transforms and expands it into a compliant AWS CloudFormation template.

```yaml
Transform: AWS::Serverless-2016-10-31
Resources:
  MyServerlessFunctionLogicalID:
    Type: AWS::Serverless::Function
    Properties:
      Handler: index.handler
      Runtime: nodejs8.10
      CodeUri: "s3://testBucket/mySourceCode.zip"
```

stack set is a regional resource so if you create a stack set in one region, you cannot see it or change it in other regions.

[./summary-plus#cloudformation](./summary-plus#cloudformation)

# CodeDeploy

The content in the 'hooks' section of the AppSpec file varies, depending on the compute platform for your deployment. The 'hooks' section for an EC2/On-Premises deployment contains mappings that link deployment lifecycle event hooks to one or more scripts. The 'hooks' section for a Lambda or an Amazon ECS deployment specifies Lambda validation functions to run during a deployment lifecycle event.

Lifecycle Events

- **BeforeInstall** is only applicable for ECS, EC2 or On-Premises compute platforms and not for Lambda deployments.
- **Install** deployment lifecycle event is not available for Lambda as well as for ECS deployments.

![img](https://docs.aws.amazon.com/codedeploy/latest/userguide/images/lifecycle-event-order-lambda.png)

The following table outlines other traffic-shifting options that are available:

- **Canary**
- **Linear**
- **All-at-once**

If you choose **Canary10Percent10Minutes** then 10 percent of your customer traffic is immediately shifted to your new version. After 10 minutes, all traffic is shifted to the new version. However, if your **pre-hook/post-hook tests fail**, or if a **CloudWatch alarm is triggered**, CodeDeploy rolls back your deployment.

**CodeDeployDefault.HalfAtATime** is incorrect because this is only applicable for EC2/On-premises compute platform and not for Lambda.

CodeDeploy agent needs to be installed on EC2 instances while its not required while using CodeDeploy with ECS or Lambda.

# X-Ray

With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors.

- AWS X-Ray works with Amazon EC2, Amazon EC2 Container Service (Amazon ECS), AWS Lambda, and AWS Elastic Beanstalk.

Elastic Beanstalk: Enable the X-Ray daemon by including the xray-daemon.config configuration file in the .ebextensions directory of your source code.

- trace all calls that your Node.js application hosted in elastic beanstalk sends to external HTTP web APIs as well as SQL database queries.

Segments and subsegments can include an annotations object containing one or more fields that X-Ray indexes for use with filter expressions.

Segments and subsegments can include a metadata object containing one or more fields with values of any type, including objects and arrays.

metadata and annotations does not record the application’s calls to your AWS services and resources.

A segment can break down the data about the work done into subsegments.

- Subsegments provide more granular timing information and details about downstream calls that your application made to fulfill the original request.
- A subsegment can contain additional details about a call to an AWS service, an external HTTP API, or an SQL database.

![img](https://docs.aws.amazon.com/xray/latest/devguide/images/scorekeep-PUTrules-timeline-subsegments.png)

For services that don’t send their own segments like Amazon DynamoDB, X-Ray uses subsegments to generate inferred segments and downstream nodes on the service map. This lets you see all of your downstream dependencies, even if they don’t support tracing, or are external.
