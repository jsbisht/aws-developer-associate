# Misc

WAF on API Gateway and on the ALB, protects against common internet attacks, including application layer attacks.

Integrate CloudWatch Events with Lambda, which will automatically trigger the function every 30 minutes.

You should retry original requests that receive server (5xx) or throttling errors. However, client errors (4xx) indicate that you need to revise the request to correct the problem before trying again.

The consolidated billing feature in **AWS Organizations** allows you to consolidate payment for multiple AWS accounts.

To avoid security issues, it is of utmost importance to test the impact of service control policies (SCPs) on your IAM policies and resource policies before applying them. The **IAM policy simulator** evaluates the policies that you choose and determines the effective permissions for each of the actions that you specify.

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

API Gateway Lambda Authorizer: When a client makes a request to one of your API’s methods, API Gateway calls your **Lambda authorizer**, which takes the caller’s identity as input and returns an IAM policy as output.

There are two types of Lambda authorizers:

– A **token-based Lambda authorizer** (also called a TOKEN authorizer) receives the caller’s identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token.
– A **request parameter-based Lambda authorizer** (also called a REQUEST authorizer) receives the caller’s identity in a combination of headers, query string parameters, stageVariables, and $context variables.

# S3

**put-bucket-policy** command can only be used to apply policy at the bucket level, not on objects. You can use S3 Access Control Lists (ACLs) instead to manage permissions of S3 objects.

# EBS

After attaching the newly created EBS volume to the Linux EC2 instance, Create a file system on this volume.

- After you attach an Amazon EBS volume to your instance, it is exposed as a block device. You can format the volume with any file system and then mount it.

If an EBS volume is the root device of an instance, you must stop the instance before you can detach the volume.

# DynamoDB

To avoid potential throttling, the provisioned write capacity for a global secondary index should be equal or greater than the write capacity of the base table since new updates will write to both the base table and global secondary index.

# SQS

- ReceiveMessageWaitTimeSeconds
- [SQS short and long polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html)

# Cloudfront

Origin Access Identity (OAI): You can configure an S3 bucket as the origin of a CloudFront distribution. OAI prevents users from viewing your S3 files by simply using the direct URL for the file. Instead, they would need to access it through a CloudFront URL.

# Lambda

Exponential Backoff: In addition to simple retries, each AWS SDK implements exponential backoff algorithm for better flow control. The idea behind exponential backoff is to use progressively longer waits between retries for consecutive error responses. Jitters are used to randomise retry intervals.

Lambda@Edge: Lets you execute functions that customize the content that CloudFront delivers. You can author Node.js or Python functions in one Region, US East (N. Virginia), and then execute them in AWS locations globally that are closer to the viewer.

Errors for Lambda creation

- CodeStorageExceededException - You have exceeded your maximum total code size per account.
- InvalidParameterValueException - One of the parameters in the request is invalid.
- ResourceConflictException - The resource already exists, or another operation is in progress.
- ResourceNotFoundException - The resource specified in the request does not exist.
- ServiceException - The AWS Lambda service encountered an internal error.
- TooManyRequestsException - The request throughput limit was exceeded.

# EC2

An EC2 instance needs to have access to the Internet, via the Internet Gateway or a NAT Instance/Gateway in order to access S3.

- S3 is not part of your VPC, unlike your EC2 instances, EBS volumes, ELBs, and other services that typically reside within your private network. An EC2 instance needs to have access to the Internet, via the Internet Gateway or a NAT Instance/Gateway in order to access S3. Alternatively, you can also create a VPC endpoint so your private subnet would be able to connect to S3.

![img](https://media.tutorialsdojo.com/amazon-s3-vpc-endpoint.PNG)

# ECS

Amazon ECS supports the following task placement strategies:

- binpack – Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.
- random
- spread

# Elastic BeanStalk

Elastic Beanstalk recommends one of two methods for performing platform updates.

- Method 1 – Update your Environment’s Platform Version – This is the recommended method when you’re updating to the latest platform version, **without a change in runtime, web server, or application server versions**, and **without a change in the major platform** version. This is the most common and routine platform update.

- Method 2 – Perform a Blue/Green Deployment – This is the recommended method when you’re updating to a different runtime, web server, or application server versions, or **to a different major platform version**.

Package your application as a **zip** file and deploy it using the **eb deploy** command.

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

# X-Ray

With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors.

- AWS X-Ray works with Amazon EC2, Amazon EC2 Container Service (Amazon ECS), AWS Lambda, and AWS Elastic Beanstalk.

Elastic Beanstalk: Enable the X-Ray daemon by including the xray-daemon.config configuration file in the .ebextensions directory of your source code.

- trace all calls that your Node.js application hosted in elastic beanstalk sends to external HTTP web APIs as well as SQL database queries.
