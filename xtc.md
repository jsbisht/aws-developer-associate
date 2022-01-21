# Cloud HSM

![img](./ss/hsm-1.webp)

![img](./ss/hsm-2.webp)

![img](./ss/hsm-3.webp)

# EC2 plus

### AWS::CloudFormation::Init

**cfn-init** is a helper script installed on EC2 OS.

This is a simple configuration management system.

Procedural (user Data) vs Desired State (cfn-init)

Packages, groups, users, sources, files, commands, services, and ownerships.

This is executed as any other command by passing into the instance with
the user-data.

This is provided with directives via Metadata and AWS::CloudFormation::Init on
a CFN resource

#### cfn-init explained

Starts off with a **cloud formation template**
This has a logical resource within it which is to create an EC2 instance.

This has a specific section called `Metadata:`

This then passes in the information passed in as `UserData`

cfn-init gets variables passed into the userdata by cloud formation

It knows the desired state desired by the user and can work towards a
final stated configuration

This can monitor the userdata and change things as the EC2 data changes.

#### CreationPolicy and Signals

The template has a specific part designated signals

A creation policy is added to a logical resource. It is provided a
timeout value. The resource itself will trigger a signal that cloud formation
can continue

# VPC Peering

Instances in either VPC can communicate with each other as if they are within the same network.

The VPCs can be in different regions (also known as an inter-region VPC peering connection).

This is neither a gateway nor a VPN connection, and does not rely on a separate piece of physical hardware.

## use case

A VPC peering connection helps you to facilitate the transfer of data. For example, if you have more than one AWS account, you can peer the VPCs across those accounts to create a file sharing network. You can also use a VPC peering connection to allow other VPCs to access resources you have in one of your VPCs.

- In case of cross account peering connection, one account creates the peering connection request and other approves the same

# VPC

Can a VPC span across regions?

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

# Misc

- ReceiveMessageWaitTimeSeconds
- [SQS short and long polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html)
- WAF on API Gateway and on the ALB (protects against common internet attacks, including application layer attacks.)

difference between OAI and using signed cookies/url

eni, security group and nacl relationship

lambda exponential backoff

lambda edge

## Deployment

trace all calls that your Node.js application hosted in elastic beanstalk sends to external HTTP web APIs as well as SQL database queries
Enable the X-Ray daemon by including the xray-daemon.config configuration file in the .ebextensions directory of your source code.

In-place deployment: The application on each instance in the deployment group is stopped, the latest application revision is installed, and the new version of the application is started and validated. You can use a load balancer so that each instance is deregistered during its deployment and then restored to service after the deployment is complete.

- Only deployments that use the EC2/On-Premises compute platform can use in-place deployments.
- AWS Lambda compute platform deployments cannot use an in-place deployment type.

For serverless applications (also referred to as Lambda-based applications), the `Transform` section specifies the version of the AWS Serverless Application Model (AWS SAM) to use. When you specify a transform, you can use AWS SAM syntax to declare resources in your template. The model defines the syntax that you can use and how it is processed. More specifically, the `AWS::Serverless` transform, which is a macro hosted by AWS CloudFormation, takes an entire template written in the AWS Serverless Application Model (AWS SAM) syntax and transforms and expands it into a compliant AWS CloudFormation template.

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

After attaching the newly created EBS volume to the Linux EC2 instance, Create a file system on this volume.

AWS SAM is an extension of AWS CloudFormation, you get the reliable deployment capabilities of AWS CloudFormation. This architecture allows the developers to locally build, test, and debug serverless applications.

- The CLI provides a Lambda-like execution environment locally. It helps you catch issues upfront by providing parity with the actual Lambda execution environment. To step through and debug your code to understand what the code is doing, you can use AWS SAM with AWS toolkits like the AWS Toolkit for JetBrains, AWS Toolkit for PyCharm, AWS Toolkit for IntelliJ, and AWS Toolkit for Visual Studio Code.

Performing a Canary deployment is incorrect because this type of deployment is primarily used in Lambda and not in Elastic Beanstalk.

If an EBS volume is the root device of an instance, you must stop the instance before you can detach the volume.

You have created a Node.js Lambda function that updates a DynamoDB table and sends an email notification via Amazon SNS. However, upon testing, the function is not working as expected.

- AWS X-Ray can be used. It collects traces. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors.
- AWS X-Ray works with Amazon EC2, Amazon EC2 Container Service (Amazon ECS), AWS Lambda, and AWS Elastic Beanstalk.

Amazon ECS supports the following task placement strategies:

- binpack – Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.
- random
- spread

S3 is not part of your VPC, unlike your EC2 instances, EBS volumes, ELBs, and other services that typically reside within your private network. An EC2 instance needs to have access to the Internet, via the Internet Gateway or a NAT Instance/Gateway in order to access S3. Alternatively, you can also create a VPC endpoint so your private subnet would be able to connect to S3.

![img](https://media.tutorialsdojo.com/amazon-s3-vpc-endpoint.PNG)

SAM deployment

- sam package
- sam deploy

Cloudfromation deployment

- aws cloudformation package
- aws cloudformation deploy

## Security

For certificates in a Region supported by AWS Certificate Manager (ACM), it is recommended that you use ACM to provision, manage, and deploy your server certificates. In unsupported Regions, you must use IAM as a certificate manager.

If you have resources that are running inside AWS, that need programmatic access to various AWS services,then the best practice is to always use IAM roles. However, for applications running outside of an AWS environment, these will need access keys for programmatic access to AWS resources.

TDE automatically encrypts data before it is written to storage, and automatically decrypts data when the data is read from storage.
Amazon RDS supports using Transparent Data Encryption (TDE) to encrypt stored data on your DB instances running Microsoft SQL Server.

Another best practice they recommend is to delete root user access keys.

Take note that SCPs are necessary, but not sufficient, for granting access for the accounts in your organization. You still need to attach IAM policies to users and roles in your organization’s accounts to actually grant permissions to them.

**put-bucket-policy** command can only be used to apply policy at the bucket level, not on objects. You can use S3 Access Control Lists (ACLs) instead to manage permissions of S3 objects.

The content in the 'hooks' section of the AppSpec file varies, depending on the compute platform for your deployment. The 'hooks' section for an EC2/On-Premises deployment contains mappings that link deployment lifecycle event hooks to one or more scripts. The 'hooks' section for a Lambda or an Amazon ECS deployment specifies Lambda validation functions to run during a deployment lifecycle event.

![img](https://docs.aws.amazon.com/codedeploy/latest/userguide/images/lifecycle-event-order-lambda.png)

**BeforeInstall** is only applicable for ECS, EC2 or On-Premises compute platforms and not for Lambda deployments.

**Install** deployment lifecycle event is not available for Lambda as well as for ECS deployments.

To upload an object to the S3 bucket which uses SSE-KMS, you have to send a request with an x-amz-server-side-encryption header with the value of aws:kms. When you upload an object, you can specify the KMS key using the x-amz-server-side-encryption-aws-kms-key-id header. If the header is not present in the request, Amazon S3 assumes the default KMS key.

**AWS Secrets Manager** is an AWS service that makes it easier for you to manage secrets. Secrets can be database credentials, passwords, third-party API keys, and even arbitrary text. You can store and control access to these secrets centrally by using the Secrets Manager console, the Secrets Manager command line interface (CLI), or the Secrets Manager API and SDKs.

- Also, you can configure Secrets Manager to automatically rotate the secret for you according to a schedule that you specify.
- Systems Manager Parameter Store doesn’t rotate its parameters.

The consolidated billing feature in AWS Organizations allows you to consolidate payment for multiple AWS accounts.

To avoid security issues, it is of utmost importance to test the impact of service control policies (SCPs) on your IAM policies and resource policies before applying them. The IAM policy simulator evaluates the policies that you choose and determines the effective permissions for each of the actions that you specify.

S3 when using SSE-C requires **x-amz-server-side​-encryption​-customer-algorithm**, **x-amz-server-side-encryption-customer-key** and **x-amz-server-side-encryption-customer-key-MD5** headers on the upload request headers to be passed during upload request.

Encrypt operation is primarily used to encrypt RSA keys, database passwords, or other sensitive information. This operation can also be used to move encrypted data from one AWS region to another however, this is `not recommended if you want to encrypt your data locally`. You have to use the GenerateDataKey operation instead.

Envelope encryption - Encrypt plaintext data with a data key and then encrypt the data key with a top-level plaintext master key.

When a client makes a request to one of your API’s methods, API Gateway calls your **Lambda authorizer**, which takes the caller’s identity as input and returns an IAM policy as output.

There are two types of Lambda authorizers:

– A token-based Lambda authorizer (also called a TOKEN authorizer) receives the caller’s identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token.
– A request parameter-based Lambda authorizer (also called a REQUEST authorizer) receives the caller’s identity in a combination of headers, query string parameters, stageVariables, and $context variables.
