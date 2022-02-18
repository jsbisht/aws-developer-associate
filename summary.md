# Todo

CloudFormation revision

RDS Encrytion

EB deployment - Immutable

EC2 http://169.254.169.254/latest/userdata/ endpoint.

EC2 http://169.254.169.254/latest/meta-data/ endpoint.

AWS Fargate

EFS

- You cant connect this to CloudFront unlike S3.

PutTraceSegments API

Desired, Minimum and Maximum capacity

**sam publish** vs sam deploy

AWS ACM

AWS SSO and SAML

Region only services - N. Vergina?

CodeDeploy - Deployment Group

KMS features

Cognito User Pool and Identity Pool

AWS STS

# Misc

![img](https://w7e4q5w4.stackpathcdn.com/wp-content/uploads/2020/01/SOAF31-Dedicated-Instances-vs.-Dedicated-Hosts.png)

![img](https://w7e4q5w4.stackpathcdn.com/wp-content/uploads/2020/01/CDAF28-Using-AWS-Lambda-as-a-target-for-Application-Load-Balancer.png)

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

Your sign-in page URL has the following format, by default: https://Your_AWS_Account_ID.signin.aws.amazon.com/console/

If you create an AWS account alias for your AWS account ID, your sign-in page URL looks like the following example: https://Your_Alias.signin.aws.amazon.com/console/

> iam create-account-alias --account-alias finance-dept

Memcached over Redis if you need to run large nodes with multiple cores or threads.

**Amazon Guard​Duty** is incorrect because this is just a threat detection service that continuously monitors for malicious activity and unauthorized behavior to protect your AWS accounts and workloads.

**AWS Firewall Manager** is incorrect because this just simplifies your AWS WAF and AWS Shield Advanced administration and maintenance tasks across multiple accounts and resources.

# Cloud

In the cloud, resources are elastic, meaning they can instantly grow or shrink to match the requirements of a specific application.

There are two basic types of elasticity:

1. Time-based: Time-based elasticity means turning off resources when they are not being used, such as a development environment that is needed only during business hours.
2. Volume-based: Volume-based elasticity means matching scale to the intensity of demand, whether that’s compute cores, storage sizes, or throughput.

You can also use a combination of ELB and Auto Scaling to maximize the elasticity of your architecture. Beyond Auto Scaling for Amazon EC2, you can use Application Auto Scaling to automatically scale resources for other AWS services, including:

- Amazon ECS
- Amazon EC2 Spot Fleets
- Amazon DynamoDB

# VPC

- 1 subnet can only be part of 1 AZ
- 1 VPC can contain multiple AZ.
- By default subnet within a VPC can communicate with other subnets in the same VPC
- Regions can only have 1 default VPC and many custom VPCs

Subnet: Consider a subnet say **10.16.16.0/20**

- Network Address (10.16.16.0)
- Network + 1 (10.16.16.1) `VPC Router`
- Network + 2 (10.16.16.2) `Reserved (DNS)`
- Network + 3 (10.16.16.3) `Reserved Future Use`
- Broadcast Address (10.16.31.255) `Last IP in the subnet`

VPC Router

- Runs in all the AZ
- Router has a network address `network + 1` in every subnet in the VPC.
- Routes traffic between subnets.

DNS

- Provided by Route 53
- Available on the base IP address of the VPC + 2.

Route Table

- `Route table` associated with the subnet defines what the Router will do with traffic when data leaves that subnet.
- All the subnets when created are associated with a `main route table`. If we don't associate a `custom route table` with a subnet, it uses the `main route table` of the VPC.
- If we do associate a `custom route table` we create with a subnet, then the main route table is disassociated.
- `A subnet can only have one route table associated at a time`.

Internet Gateway

- Regional resilient gateway attached to a VPC.
- DO NOT NEED one per AZ.
- `One IGW will cover all AZ's in a region`
- An IGW can be created and can exist without VPC
- `It can only be attached to one VPC at a time`.
- An IGW exists between VPC and AWS public zone.
- A VPC can have no IGW, which makes it private.
- The `IGW creates a record that links the instance's private IP to the public IP`.
- This is why when an EC2 instance is created it `only sees the private IP address`.

# VPC Peering

Can a VPC span across regions?

Yes. Using VPC Peering.

Instances in either VPC can communicate with each other as if they are within the same network. The VPCs can be in different regions (also known as an inter-region VPC peering connection). This is neither a gateway nor a VPN connection, and does not rely on a separate piece of physical hardware.

A VPC peering connection helps you to facilitate the transfer of data. For example, if you have more than one AWS account, you can peer the VPCs across those accounts to create a file sharing network. You can also use a VPC peering connection to allow other VPCs to access resources you have in one of your VPCs.

- In case of cross account peering connection, one account creates the peering connection request and other approves the same

Instances in either VPC can communicate with each other as if they are within the same network. The VPCs can be in different regions (also known as an inter-region VPC peering connection). This is neither a gateway nor a VPN connection, and does not rely on a separate piece of physical hardware.

A VPC peering connection helps you to facilitate the transfer of data. For example, if you have more than one AWS account, you can peer the VPCs across those accounts to create a file sharing network. You can also use a VPC peering connection to allow other VPCs to access resources you have in one of your VPCs.

- In case of cross account peering connection, one account creates the peering connection request and other approves the same

# AWS Direct Connect

Direct Connect provides Amazon Web Services (AWS) customers with a way to transfer data that does not involve using the public Internet.

Say your company uses multiple Amazon VPCs and is setting up a new office.

- Establish a private connection from your new office’s network to your Amazon VPC using AWS Direct Connect
- This option provides predictable network performance, reduces bandwidth costs, and doesn’t require that customers be responsible for implementing high availability solutions for all VPN endpoints.
- The downside of this option (possible requiring additional telecom and hosting provider relationships) is mitigated in this instance because the office is new and would need to provision a whole new network anyway.

AWS Direct Connect uses dedicated, private network connections between your intranet and Amazon VPC. Use AWS Direct connect:

- If you have an immediate need, have low to modest bandwidth requirements, and can tolerate the inherent variability in Internet-based connectivity.
- A VPC VPN Connection utilizes IPSec to establish encrypted network connectivity between your intranet and Amazon VPC over the Internet.

You are uploading large amounts of data overnight, 3 to 5 TB to an S3 bucket. What steps can you take to decrease the amount of time to perform these uploads?

Using AWS Direct Connect, data that would have previously been transported over the Internet can now be delivered through a private network connection between AWS and your datacenter or corporate network. In many circumstances, private network connections can reduce costs, increase bandwidth, and provide a more consistent network experience than Internet-based connections.

# Stateful vs Stateless Firewalls

Stateless firewalls response will be sent on any ephemeral port, as its stateless. So in the firewall config we need to open all the ephemeral ports.

Stateful firewalls for this reason, makes setting firewall easy as the response port will be same as the request port. So, full emphemral port range is not kept open.

# NACL, Security Group

NACL: Stateless

- Every subnet has an associated NACL which filters the data as it crosses the boundry of the subnet.
- Only data coming into and out of subnet is affected by NACL
- Connections within a subnet aren't impacted by NACLs
- Cannot be assigned to AWS resources, but only subnets

```
          |-----    Subnet
          |
1 NACL    |-----    Subnet
          |
          |-----    Subnet
```

| INBOUND |                  |          |            |           |            |
| ------- | ---------------- | -------- | ---------- | --------- | ---------- |
| Rule #  | Type             | Protocol | Port Range | Source    | Allow/Deny |
| 110     | HTTPS(443)       | TCP (6)  | 443        | 0.0.0.0/0 | ALLOW      |
| \*      | All IPv4 traffic | All      | All        | 0.0.0.0/0 | DENY       |

Security Group: Stateful

- Security Groups (SGs) are another security feature of AWS VPC ... only unlike NACLs they are attached to AWS resources, not VPC subnets.
- SGs offer a few advantages vs NACLs in that they can recognize AWS resources and filter based on them, they can reference other SGs and also themselves.
- Security Groups are attached to primary Elastic Network Interfaces (ENIs), not to EC2 instances

App Tier's Security Group might look as follows.

| INBOUND    |          |            |                         |                      |
| ---------- | -------- | ---------- | ----------------------- | -------------------- |
| Type       | Protocol | Port Range | Source                  | Description          |
| Custom TCP | TCP      | 1337       | sg-45670123acbd/a4l-app | inbound from a4l-web |

Logical referencing scales. So any instances having SG `sg-0123acbd4567/a4l-web` attached to them will be able to connect to any instance having SG `sg-45670123acbd/a4l-app` attached to them using port 1337.

# ENI

An elastic network interface is a logical networking component in a VPC that represents a virtual network card.

- Every EC2 has at least one Elastic Network Interface (ENI) which is the primary ENI.
- We can attach secondary ENIs to an EC2 instance which are in seperate subnets, but everything must be within one AZ.
- Secondary interfaces function in all the same ways as primary interfaces except you can detach interfaces and move them to other EC2 instances.
- When you launch an instance, the Security Groups are attached on the network interface and not to the EC2 instance.

Each instance has a default network interface, called the primary network interface. You cannot detach a primary network interface from an instance. You can create and attach additional network interfaces. The maximum number of network interfaces that you can use varies by instance type.

- In a VPC, all subnets have a modifiable attribute that determines whether network interfaces created in that subnet (and therefore instances launched into that subnet) are assigned a public IPv4 address. The public IPv4 address is assigned from Amazon's pool of public IPv4 addresses. When you launch an instance, the IP address is assigned to the primary network interface that's created.

Primary IPv4 private address: Given a DNS name that is associated with the private IP say `ip-10-16-0-10.ec2.internal`. Its only resolvable inside the VPC and always points to private IP address.

Elastic IP address

- We can have `1 public elastic IP per private IPv4 address`
- Can associate with a private IP on the primary interface or on the secondary interface.
- If you need different rules for different IPs, then you need to multiple ENI with different Security Group on each.

Public IPv4 address

- If you stop an instance the address is deallocated.
- When you start up again, it is given a brand new IPv4 address
- Restarting the instance will not change the IP address
- Changing between EC2 hosts will change the address

Public IPv4 address is allocated a public DNS name

- Inside the VPC, Public DNS name will resolve to the primary private IPv4 address of the instance
- Outside the VPC, Public DNS name will resolve to the public IP address

Public IPv4 address is not directly attached to the instance or any of the interface, its associated with it.

- Translation of this address to private IPv4 address is done by AWS Internet Gateway

# NAT

Under the lack of abundant public IPv4 addresses, gives private CIDR range outgoing internet access.

IP masquerading (aka NAT)

- This hides CIDR block behind one IP. This is many private IPs attached to one public IP.
- Under the lack of abundant public IPv4 addresses, gives private CIDR range **outgoing** internet access.
- But note that the public IPs cannot connect to these private IP's when NAT is used.

![img](./ss/nat-architecture-2.webp)

We instead choose a NAT Gateway to provide private IP based instances access to internet.

So we provision a NAT Gateway in the public subnet, which allows us to use public IP addresses.

- public subnet has a route table attached to it
- the route table provides routing of public IP address to Internet Gateway
- Since NAT gateway is in the pubilc subnet, it can send data out and get a response back

The private instances can also have a route table, which can be different from the public subnet route table.

- we will configure this route table with a default route to point to the NAT gateway
- so any packet not pointing to the IPs within the VPC will be sent to the NAT gateway

Considerations

- needs to run from a public subnet
- Uses Elastic IPs (Static IPv4 Public)
- NAT Gateway only use NACL and dont use Security Group

# Classic Load Balancer and Application Load Balancer

Do nothing, the ELB will direct traffic to it after the health check threshold is passed.

# Security

![img](https://w7e4q5w4.stackpathcdn.com/wp-content/uploads/2020/01/CDAF33-AWS-STS-API-Operations-Comparison-Table.png)

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

AWSLambdaDynamoDBExecutionRole includes the following permission:

- dynamodb:DescribeStream
- dynamodb:GetRecords
- dynamodb:GetShardIterator
- dynamodb:ListStreams

**If your identity store is not compatible with SAML 2.0**, then you can build a custom identity broker application to perform a similar function. The broker application authenticates users, requests temporary credentials for users from AWS, and then provides them to the user to access AWS resources.

- The application verifies that employees are signed into the existing corporate network's identity and authentication system, which might use LDAP, Active Directory, or another system.
- The identity broker application then obtains temporary security credentials for the employees.

  - To get temporary security credentials, the identity broker application calls either **AssumeRole** or **GetFederationToken** to obtain temporary security credentials, depending on how you want to manage the policies for users and when the temporary credentials should expire.
  - The call returns temporary security credentials consisting of an AWS access key ID, a secret access key, and a session token. The identity broker application makes these temporary security credentials available to the internal company application.
  - The app can then use the temporary credentials to make calls to AWS directly. The app caches the credentials until they expire, and then requests a new set of temporary credentials.

x-amz-server-side-encryption header can only accept two values: AES256 and aws:kms.

- header should be AES256, which means that the bucket is using Amazon S3-Managed Keys (SSE-S3).
- if this header has a value of aws:kms, then it uses AWS KMS-Managed Keys (SSE-KMS).

# API Gateway

API Gateway Lambda Authorizer: When a client makes a request to one of your API’s methods, API Gateway calls your **Lambda authorizer**, which takes the caller’s identity as input and returns an IAM policy as output.

There are two types of Lambda authorizers:

– A **token-based Lambda authorizer** (also called a TOKEN authorizer) receives the caller’s identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token.
– A **request parameter-based Lambda authorizer** (also called a REQUEST authorizer) receives the caller’s identity in a combination of headers, query string parameters, stageVariables, and $context variables.

Take note that if the scenario uses an application hosted in Lambda, you have to use Lambda integration instead of HTTP proxy or HTTP custom.

For a Lambda function, you can have two types of integration:

– Lambda proxy integration
– Lambda custom integration

![img](https://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-my-resource-get-method-execution-boxes.png)

The following are the Gateway response types which are associated with the HTTP 504 error in API Gateway:

- INTEGRATION_FAILURE – The gateway response for an integration failed error. If the response type is unspecified, this response defaults to the DEFAULT_5XX type.
- INTEGRATION_TIMEOUT – The gateway response for an integration timed out error. If the response type is unspecified, this response defaults to the DEFAULT_5XX type.

**For the integration timeout**, the range is from 50 milliseconds to 29 seconds for all integration types, including Lambda, Lambda proxy, HTTP, HTTP proxy, and AWS integrations.

For functions with a long timeout, your client might be disconnected during synchronous invocation while it waits for a response and returns an HTTP 504 error.

In this scenario, there is an issue where the users are getting HTTP 504 errors in the serverless application. This means the Lambda function is working fine at times but there are instances when it throws an error. Based on this analysis, the most likely cause of the issue is the INTEGRATION_TIMEOUT error since you will only get an INTEGRATION_FAILURE error if your AWS Lambda integration does not work at all in the first place.

Since the incoming requests are increasing, the API Gateway automatically enabled throttling which caused the HTTP 504 errors. is incorrect because a large number of incoming requests will most likely **produce an HTTP 502 or 429 error but not a 504 error**.

An authorization failure occurred between API Gateway and the Lambda function usually produces HTTP 403 errors and not 504s.

If executing the function would cause you to exceed a concurrency limit at either the account level (ConcurrentInvocationLimitExceeded) or function level (ReservedFunctionConcurrentInvocationLimitExceeded), Lambda may return a TooManyRequestsException as a response.

Amazon API Gateway does not support unencrypted (HTTP) endpoints. By default, Amazon API Gateway assigns an internal domain to the API that automatically uses the Amazon API Gateway certificate. When configuring your APIs to run under a custom domain name, you can provide your own certificate for the domain.

The base URL for REST APIs is in the following format:

> https://{restapi_id}.execute-api.{region}.amazonaws.com/{stage_name}/

"Effect": "Allow", "Action": ["execute-api:InvalidateCache"] allows any request to invalidate cache results in API Gateway.

You can use the API Gateway Import API feature to import a REST API from an external definition file (on-premise) into API Gateway. Currently, the Import API feature supports OpenAPI v2.0 and OpenAPI v3.0 definition files.

Amazon API Gateway Lambda proxy integration is a simple, powerful, and nimble mechanism to build an API with a setup of a single API method. The Lambda proxy integration allows the client to call a single Lambda function in the backend. In Lambda proxy integration, when a client submits an API request, API Gateway passes to the integrated Lambda function the raw request as-is. Because API Gateway doesn't intervene very much between the client and the backend Lambda function for the Lambda proxy integration, the client and the integrated Lambda function can **adapt to changes in each other without breaking the existing integration setup of the API**.

For API Gateway to pass the Lambda output as the API response to the client, the Lambda function must return the result in the following JSON format:

```json
{
    "isBase64Encoded": true|false,
    "statusCode": httpStatusCode,
    "headers": { "headerName": "headerValue", ... },
    "body": "..."
}
```

Since the Lambda function returns the result in XML format, it will cause the 502 errors in the API Gateway.

# S3

**put-bucket-policy** command can only be used to apply policy at the bucket level, not on objects. You can use S3 Access Control Lists (ACLs) instead to manage permissions of S3 objects.

Glacier Retrival

- Expedited: 1-5 minutes
- Standard: 3-5 hours
- Bulk: 5-12 hours

# EBS

After attaching the newly created EBS volume to the Linux EC2 instance, Create a file system on this volume.

- After you attach an Amazon EBS volume to your instance, it is exposed as a block device. You can format the volume with any file system and then mount it.

If an EBS volume is the root device of an instance, you must stop the instance before you can detach the volume.

# Database

![img](https://w7e4q5w4.stackpathcdn.com/wp-content/uploads/2020/01/SOAF3-Multi-AZ-Deployments-vs-Read-Replicas.png)

# DynamoDB

To avoid potential throttling, the provisioned write capacity for a global secondary index should be equal or greater than the write capacity of the base table since new updates will write to both the base table and global secondary index.

DynamoDB adaptive capacity automatically boosts throughput capacity to high-traffic partitions. However, each partition is still subject to the hard limit. This means that adaptive capacity can’t solve larger issues with your table or partition design. To avoid hot partitions and throttling, optimize your table and partition structure.

To solve this issue, consider one or more of the following solutions:

– Increase the amount of read or write capacity for your table to anticipate short-term spikes or bursts in read or write operations. If you decide later you don’t need the additional capacity, decrease it. Take note that Before deciding on how much to increase read or write capacity, consider the best practices in designing your partition keys.

– Implement error retries and exponential backoff. This technique uses progressively longer waits between retries for consecutive error responses to help improve an application’s reliability. If you’re using an AWS SDK, this logic is built‑in. If you’re using another SDK, consider implementing it manually.

– Distribute your read operations and write operations as evenly as possible across your table. A “hot” partition can degrade the overall performance of your table.

– Implement a caching solution, such as DynamoDB Accelerator (DAX) or Amazon ElastiCache. DAX is a DynamoDB-compatible caching service that offers fast in‑memory performance for your application. If your workload is mostly read access to static data, query results can often be served more quickly from a well‑designed cache than from a database.

About local secondary indexes:

– For each partition key value, the total size of all indexed items must be 10 GB or less.
– When you query this index, you can choose either eventual consistency or strong consistency.
– Queries or scans on this index consume read capacity units from the base table

Global secondary indexes:

- Queries on this index support eventual consistency only.

Suppose that each item is 4 KB and you set the page size to 40 items. A Query request would then consume only 20 eventually consistent read operations or 40 strongly consistent read operations.

- A larger number of smaller Query or Scan operations would allow your other critical requests to succeed without throttling, instead of using large page size.

You may use ElastiCache as your database cache, it will not reduce the DynamoDB response time to microseconds unlike DynamoDB DAX.

Increasing the amount of read or write capacity for your table is incorrect because although it will also solve the throttling issues of your application, adding both RCU and WCU to your table will significantly increase your operating costs.

Implementing **read sharding** to distribute workloads evenly is incorrect because you should implement a **write sharding** in your application instead. One way to better distribute writes across a partition key space in DynamoDB is to expand the space.

- Sharding Using Random Suffixes: You can add a random number to the partition key values to distribute the items among partitions.
- Sharding Using Calculated Suffixes: You can use a number that is calculated based on something that you are querying on.

Locking

- pessimistic concurrency: this will just prevent a value from being updated by locking the item or row in the database. This can block users from reading, updating, or deleting an entry depending on the lock type which is **not suitable for the multithreaded application**.
- optimistic locking: Optimistic locking is a strategy to ensure that the client-side item that you are updating (or deleting) is the same as the item in DynamoDB. If you use this strategy, then your database writes are protected from being overwritten by the writes of others — and vice-versa.
- conditional writes: By default, the DynamoDB write operations (PutItem, UpdateItem, DeleteItem) are **unconditional**: each of these operations will overwrite an existing item that has the specified primary key. DynamoDB optionally supports **conditional writes** for these operations. A conditional write will succeed only if the item attributes meet one or more expected conditions. Otherwise, it returns an error.
- atomic counters: You might use an atomic counter to track the number of visitors to a website. In this case, your application would increment a numeric value, regardless of its current value. If an UpdateItem operation fails, the application could simply retry the operation. This would risk updating the counter twice, but you could probably tolerate a slight overcounting or undercounting of website visitors. An atomic counter would **not be appropriate where overcounting or undercounting can't be tolerated**. In such case use conditional write instead.

DynamoDB has two read/write capacity modes for processing reads and writes on your tables:

- On-demand: without capacity planning. pay-per-request pricing for read and write requests so that you pay only for what you use.
- Provisioned (default, free-tier eligible)

A write capacity unit (WCU) represents one write per second, for an item up to **1KB** in size.

A read capacity unit (RCU) represents one read per second, for an item up to **4KB** in size.

To create, update, or delete an item in a DynamoDB table, use one of the following operations:

- PutItem
- UpdateItem
- DeleteItem

To return the number of write capacity units consumed by any of these operations, set the ReturnConsumedCapacity parameter to one of the following:

- TOTAL — returns the total number of write capacity units consumed.
- INDEXES — returns the total number of write capacity units consumed, with subtotals for the table and any secondary indexes that were affected by the operation.
- NONE — no write capacity details are returned. (This is the default.)

DynamoDB Global Table

- The last writer wins

To avoid potential throttling, the provisioned write capacity for a global secondary index should be equal or greater than the write capacity of the base table since new updates will write to both the base table and global secondary index.

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

![img](https://w7e4q5w4.stackpathcdn.com/wp-content/uploads/2020/01/CDAF1-CodeDeploy-Run-Order-of-Hooks-in-a-Lambda-Function-Version-Deployment.png)

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

If concurrent execution limit has been reached, the function will be throttled but not terminated. That means additional invocation lambda will not be created, but existing lambda in running state wont be affected by this.

For Lambda functions that process Kinesis or DynamoDB streams, the number of shards is the unit of concurrency. If your stream has 100 active shards, there will be at most 100 Lambda function invocations running concurrently. This is because Lambda processes each shard’s events in sequence.

You invoke your Lambda function using the Invoke operation, and you can specify the invocation type as synchronous or asynchronous. In the Invoke API, you have 3 options to choose from for the InvocationType:

- `RequestResponse` (default) – Invoke the function synchronously. Keep the connection open until the function returns a response or times out. The API response includes the function response and additional data.
- `Event` – Invoke the function asynchronously. Send events that fail multiple times to the function’s dead-letter queue (if it’s configured). The API response only includes a status code.
- `DryRun`

NAT Gateway and Security group is required by Lambda to connect to internet.

ENI's will be used by Lambda to connect to resources within the AWS network. (This would also require making Lambda private)

# EC2

Bootstrapping is done using user data - accessed via the meta data service. The following gives the user data part of the instance meta data. It logs the user data that was passed to the instance.

    http://169.254.169.254/latest/user-data

Anything you pass in is executed by the instance OS. `It is excecuted only once on launch`!

- User data is limited to 16 KB in size
- Can be modified when instance is in stopped state
- Its not secure and shouldnt be used for passwords or long term credentials
- The scripts are run as `root` user

Instance metadata is data about your instance that you can use to configure or manage the running instance.

- Instance metadata is way the instance or anything inside instance can get information about the environment.
- Accessible inside all instances.

Instance metadata is accessed from an EC2 instance using

    http://169.254.169.254/latest/meta-data/

An EC2 instance needs to have access to the Internet, via the Internet Gateway or a NAT Instance/Gateway in order to access S3.

- S3 is not part of your VPC, unlike your EC2 instances, EBS volumes, ELBs, and other services that typically reside within your private network. An EC2 instance needs to have access to the Internet, via the Internet Gateway or a NAT Instance/Gateway in order to access S3. Alternatively, you can also create a VPC endpoint so your private subnet would be able to connect to S3.

![img](https://media.tutorialsdojo.com/amazon-s3-vpc-endpoint.PNG)

You can scale the size of your group **manually** by adjusting your desired capacity, or you can automate the process through the use of scheduled scaling or a scaling policy. The **desired capacity** must be less than or equal to the maximum size of the group. If your new value for Desired capacity is greater than Maximum capacity, you must update Maximum capacity.

By default, your instance is enabled for basic monitoring. You can optionally enable detailed monitoring.

- Basic – Data is available automatically in 5-minute periods at no charge.
- Detailed – Data is available in 1-minute periods for an additional cost.

EC2 is incorrect because although you can run Docker in your EC2 instances, it does not provide a highly scalable, fast, container management service in comparison to ECS. Take note that in itself, EC2 is not scalable and should be paired with Auto Scaling and ELB.

Instance profile

- Using roles to grant permissions to applications that run on EC2 instances requires a bit of extra configuration. An application running on an EC2 instance is abstracted from AWS by the virtualized operating system. Because of this extra separation, an additional step is needed to assign an AWS role and its associated permissions to an EC2 instance and make them available to its applications.
- This extra step is the **creation of an instance profile that is attached to the instance**. The **instance profile contains the role** and can provide the role's temporary credentials to an application that runs on the instance. Those temporary credentials can then be used in the application's API calls to access resources and to limit access to only those resources that the role specifies.
- Note that only one role can be assigned to an EC2 instance at a time, and all applications on the instance share the same role and permissions.
- Instance profile is just a container for an IAM role that you can use to pass role information to an EC2 instance when the instance starts. This is different from an AWS CLI profile, which you can use for switching to various profiles, _after the instances has started with instance profile_. In addition, an instance profile is associated with the instance and not configured in the AWS CLI.

![img](https://docs.aws.amazon.com/IAM/latest/UserGuide/images/roles-usingrole-ec2roleinstance.png)

The instance profile is the item that allows the permissions inside the instance.

- When you create an Instance Role from the management console, InstanceProfile is automatically created with the same name used for Instance Role (eg. ExampleInstanceRole).
- From CLI both Instance Role and InstanceProfile need to be created individually.
- When using an Instance Role with EC2 instance, you are not attaching the Instance Role instead its the InstanceProfile thats attached

To check the security credentails in use for a given EC2 instance:

    http://169.254.169.254/latest/meta-data/iam/security-credentials/ExampleInstanceRole

- `Container credentials` – You can associate an IAM role with each of your Amazon Elastic Container Service (Amazon ECS) _task definitions_. Temporary credentials for that role are then available to that task's containers.

- `Instance profile credentials` – You can associate an IAM role with each of your Amazon Elastic Compute Cloud (Amazon EC2) instances. Temporary credentials for that role are then available to code running in the instance. The credentials are delivered through the Amazon EC2 metadata service. **Credentials are delivered through EC2 metadata service**.

EC2 Placement Groups

- Cluster (PERFORMANCE): Close together, Same AZ. If the hardware fails, the entire cluster might fail.
- Spread (Hardware Failure): Across distinct underlying hardware, Can be multi-AZ. Limits 7 instances per AZ.
- Partition (Resilience): Groups of instances, no shared hardware b/w groups. Useful when you require more than 7 instances.

The following table shows encryption support for various AMI-copying scenarios.

| Scenario | Description                | Supported |
| -------- | -------------------------- | --------- |
| 1        | Unencrypted-to-unencrypted | Yes       |
| 2        | Encrypted-to-encrypted     | Yes       |
| 3        | Unencrypted-to-encrypted   | Yes       |
| 4        | Encrypted-to-unencrypted   | No        |

    You cannot copy an encrypted snapshot to yield an unencrypted one.

EC2 Purchase Options

- On-Demand Instances (Default)
- Spot Instances
- Reserved Instances
- Dedicated Hosts
- Dedicated Instances

AMI

- AMI can only be used in one region.
- AMI can be copied between regions
- An AMI cannot be edited. If you need to update an AMI, launch an instance, make changes and then make new AMI.

# ECS

ECS Cluster

- Clusters is where your containers run from. You provide ECS with an image, and it run that as container inside the cluster.
- You create a cluster and then either deploy a `Task` or a `Service` within that.

Container definition

- This is used to let ECS know where the container image is located.
- Also defines the port to be used for creating the container.

Task definition: Task in ECS defines a self contained application. A task can have `one or more` application defined.

```
TASK

    --- Container Defination

    --- Container Defination

    --- Container Defination

```

Task definition stores the resources used by the task like

- Container definations
- CPU and Memory
- Networking mode the task uses
- Compatibility mode (EC2 vs Fargate mode)
- Task role

Task role: an IAM role that the task assumes. The task gains temporary credentials that allow access to AWS products and services.

Service defination: defines how we want a task to scale. This allows us to have multiple independent copies of our task to run.

- You can deploy a load balancer in front of the service so that incoming load is distributed across all the tasks.
- So for tasks running within ECS that are long running, we use a service to provide scalability and high availability.
- Service lets you replace failed tasks, distribute loads across copies of the task.

ECS users clusters which run in two mode:

- EC2 mode (uses EC2 instances as container hosts, can be seen to be running ECS software)
- Fargate mode (serverless way of running docker containers where AWS manages the container host part, leaving you to architect your environment using containers)

EC2 mode: The number of containers to be maintained in a cluster is controlled by Auto Scaling Group

- Note that while using EC2 mode, even though the containers are not running on the EC2 instances we will still be billed for them.
- In EC2 mode, we are responsible for managing the capacity of the cluster
- ECS will manage the number of task that are deployed if you use service and service defination.

Fargate mode: Removes the management overhead from ECS, no need to manage EC2.

- It differs from EC2 mode in terms of using a `shared fargate infrastructure`, which allows all customers to access from the same pool of resources.
- Fargate deployment still uses a cluster with a VPC where AZs are specified.
- Tasks and services are actually running from shared infrastructure, then they are injected into your VPC setup.
- Each task is given an elastic network interface which has an IP address within the VPC. They then run like an VPC resource.

EC2 vs ECS(EC2) vs Fargate

- If you already are using containers, use **ECS**.
- **EC2 mode** is good for a large workload with price conscious. This allows for spot pricing and prepayment. But you need to manage the scaling, failing instances, etc.
- Large workload but overhead conscious **Fargate**.
- Small or burst style workloads **Fargate** makes sense.
- Batch or periodic workloads **Fargate**.

Tasks and Services use images on container registry (ECR). Via the task and service defination container images are deployed onto the EC2 host.

![img](https://w7e4q5w4.stackpathcdn.com/wp-content/uploads/2020/02/CodeDeploy-Run-Order-of-Hooks-in-an-Amazon-ECS-Deployment.png)

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

Cluster Query Language: Cluster queries are expressions that enable you to group objects. For example, you can group container instances by attributes such as Availability Zone, instance type, or custom metadata. You can add custom metadata to your container instances, known as attributes. You can use the built-in attributes provided by Amazon ECS or define custom attributes.

Task Group: A set of related tasks. All tasks with the same task group name are considered as a set when performing spread placement.

Task Placement Constraint: A rule that is considered during task placement. Although it uses cluster queries **when you are placing tasks on container instances based on a specific expression**, it does not provide the actual expressions which are used to group those container instances.

Task Placement Strategy: An algorithm for selecting instances for task placement or tasks for termination.

Unlike Elastic Beanstalk, take note that even though you can use Service Auto Scaling in ECS, you still have to enable and configure it.

# Elastic BeanStalk

Elastic Beanstalk recommends one of two methods for performing platform updates.

- Method 1 – Update your Environment’s Platform Version – This is the recommended method when you’re updating to the latest platform version, **without a change in runtime, web server, or application server versions**, and **without a change in the major platform** version. This is the most common and routine platform update.

- Method 2 – Perform a Blue/Green Deployment – This is the recommended method when you’re updating to a different runtime, web server, or application server versions, or **to a different major platform version**.

Package your application as a **zip** file and deploy it using the **eb deploy** command.

# SQS

- ReceiveMessageWaitTimeSeconds
- [SQS short and long polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html)

Amazon SQS FIFO queues follow exactly-once processing. It introduces a parameter called Message Deduplication ID, which is the token used for deduplication of sent messages. Add a MessageDeduplicationId parameter to the SendMessage API request. Ensure that the messages are sent at least 5 minutes apart.

Specify the Amazon Resource Name of the SQS Queue in the Lambda function’s **DeadLetterConfig** parameter.

# Kinesis

Kinesis Client Library

- you should ensure that the number of instances does not exceed the number of shards (except for failure standby purposes).
- Each shard is processed by exactly one KCL worker and has exactly one corresponding record processor, so you never need multiple instances to process one shard.
- However, one worker can process any number of shards, so it’s fine if the number of shards exceeds the number of instances.

The purpose of resharding in Amazon Kinesis Data Streams is to enable your stream to adapt to changes in the rate of data flow.

- You split shards to increase the capacity (and cost) of your stream.
- You merge shards to reduce the cost (and capacity) of your stream.

It is better to use Kinesis instead of Lambda for the real-time data analytics application.

Unlike Kinesis Data Stream service, DynamoDB and Redshift does not provide real time processsing.

If your stream has 100 active shards, there will be at most 100 Lambda function invocations running concurrently. This is because Lambda processes each shard’s events in sequence.

Integrating Amazon Kinesis Data Firehose with the Amazon Kinesis Data Stream to increase the capacity of the stream is incorrect this method will not increase the capacity of the stream as it doesn’t mention anything about resharding.

# Deployment

In-place deployment: The application on each instance in the deployment group is stopped, the latest application revision is installed, and the new version of the application is started and validated. You can use a load balancer so that each instance is deregistered during its deployment and then restored to service after the deployment is complete.

- Only deployments that use the EC2/On-Premises compute platform can use in-place deployments.
- AWS Lambda compute platform deployments cannot use an in-place deployment type.

Performing a Canary deployment is incorrect because this type of deployment is primarily used in Lambda and not in Elastic Beanstalk.

# SAM

The AWS Serverless Application Model (AWS SAM) is an open source framework for building serverless applications. It provides **shorthand** syntax to express functions, APIs, databases, and event source mappings. You define the application you want with just a few lines per resource and model it using YAML.

AWS SAM is natively supported by AWS CloudFormation and provides a simplified way of defining the Amazon API Gateway APIs, AWS Lambda functions, and Amazon DynamoDB tables needed by your serverless application. During deployment, SAM transforms and expands the SAM syntax into AWS CloudFormation syntax. Then, CloudFormation provisions your resources with reliable deployment capabilities.

AWS SAM is an extension of AWS CloudFormation, you get the reliable deployment capabilities of AWS CloudFormation. This architecture allows the developers to locally build, test, and debug serverless applications.

- The CLI provides a Lambda-like execution environment locally. It helps you catch issues upfront by providing parity with the actual Lambda execution environment. To step through and debug your code to understand what the code is doing, you can use AWS SAM with AWS toolkits like the AWS Toolkit for JetBrains, AWS Toolkit for PyCharm, AWS Toolkit for IntelliJ, and AWS Toolkit for Visual Studio Code.

For SAM (and Serverless Framework) users, who deal mostly in Lambda functions, one of the more most useful transforms is the Events property on the Lambda function

```yaml
Resources:
  HelloWorldFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: HelloWorldFunction
      Handler: app.lambdaHandler
      Runtime: nodejs12.x
      Events: # <--- "Events" property is not a real Cloudformation Lambda property
        HelloWorld:
          Type: Api
          Properties:
            Path: /hello
            Method: get
```

The SAM template snippet shown above gets transformed/expanded into several API Gateway objects (a RestApi, a deployment, and a stage).

- The **AWS::Serverless::Function** type used in this snippet is not a real Cloudformation type -- you won't find it in the docs.
- SAM expands it into a Cloudformation template containing a **AWS::Lambda::Function** object and several different AWS::ApiGateway::\* objects that Cloudformation understands.
- if you were authoring pure Cloudformation, you would have had to code all this by hand, over and over, for each API Gateway endpoint that you wanted to create. Now, with a SAM template, you define the API as an "Event" property of the Lambda function, and SAM (or Serverless Framework) takes care of the drudgery.

SAM deployment

- sam package
- sam deploy

SAM alone is not enough to run the C++ code in Lambda. You have to use a Custom Runtime. So create a new layer which contains the Custom Runtime for C++ and then launch a Lambda function which uses that runtime.

Use SAM or Cloudformation

- SAM also gives you additional "transforms" that allow you to define certain concepts concisely, and SAM will figure out what you mean and fill in the the missing pieces to create a full, expanded, legal Cloudformation template.
- for a simple application with Lambdas triggered by a new API you're creating, the SAM template will let you accomplish this in fewer lines than CloudFormation.
- If Lambda, APIGW, DDB are the only resources then start with SAM. If you need other resources later (VPCs, CloudWatch, ...) it is not hard to "transition" to a more generic, Cloudformation template that can use both SAM and non-SAM resources.

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

After you’ve defined a stack set, you can create, update, or delete stacks in the target accounts and regions you specify. When you create, update, or delete stacks, you can also specify operational preferences, such as the order of regions in which you want the operation to be performed, the failure tolerance beyond which stack operations stop, and the number of accounts in which operations are performed on stacks concurrently.

Set up CloudFormation with Systems Manager Parameter Store to retrieve the latest AMI IDs for your template and whenever you decide to update the EC2 instances, call the update-stack API in CloudFormation in your CloudFormation template.

**ZipFile** parameter to is the correct one to be used in this scenario, which will allow the developer to place the python code inline in the template. If you include your function source inline with this parameter, AWS CloudFormation places it in a file named index and zips it to create a deployment package.

The **CodeUri** parameter is incorrect because this is not a valid property of **AWS::Lambda::Function** resource but of the **AWS::Serverless::Function** resource in _AWS SAM_. This parameter accepts the S3 URL of your code and not the actual code itself.

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

# CodePipeline

There are three configuration options for manual approval actions in CodePipeline:

- Publish Approval Notifications: You can configure an approval action to publish a message to an Amazon Simple Notification Service topic when the pipeline stops at the action.
- Specify a URL for Review: The URL is included in the notification that is published to the Amazon SNS topic. Approvers can use the console or CLI to view it.
- Enter Comments for Approvers: you can also add comments that are displayed to those who receive the notifications or those who view the action in the console or CLI response.

No Configuration Options

- You can also choose not to configure any of these three options. You might not need them if, for example, you can notify someone directly that the action is ready for their review, or you simply want the pipeline to stop until you decide to approve the action yourself.

# X-Ray

With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors.

- AWS X-Ray works with Amazon EC2, Amazon EC2 Container Service (Amazon ECS), AWS Lambda, and AWS Elastic Beanstalk.

Elastic Beanstalk: Enable the X-Ray daemon by including the **xray-daemon.config** configuration file in the .ebextensions directory of your source code.

- trace all calls that your Node.js application hosted in elastic beanstalk sends to external HTTP web APIs as well as SQL database queries.

AWS Lambda: You simply need to tick the Enable AWS X-Ray checkbox in the Lambda function to enable active tracing.

Including the **xray-daemon.config** configuration file in the AMI is incorrect because this configuration file is only applicable in Elastic Beanstalk. You have to install the X-Ray daemon via a user data script.

A segment document can be up to 64 kB and contain a whole segment with subsegments, a fragment of a segment that indicates that a request is in progress, or a single subsegment that is sent separately.

You can send segment documents directly to X-Ray by using the **PutTraceSegments** API.

X-Ray compiles and processes segment documents to generate queryable trace summaries and full traces that you can access by using the **GetTraceSummaries** and **BatchGetTraces** APIs, respectively.

Develop a custom debug tool which will enable them to view the full traces of their application without using the X-Ray console.
Use the **GetTraceSummaries** API to get the list of trace IDs of the application and then retrieve the list of traces using **BatchGetTraces** API.

Running the **GetTraceSummaries** operation retrieves IDs and annotations for traces available for a specified time frame using an optional filter.

![img](https://docs.aws.amazon.com/xray/latest/devguide/images/scorekeep-filter-httpurlCONTAINSuser-cropped.png)

Fetching the data using the **BatchGetTraces** API is incorrect because this API simply retrieves a list of traces specified by ID. It does not support filter expressions and **it doesnt returns the annotations**.

Segments and subsegments can include an annotations object containing one or more fields that X-Ray indexes for use with filter expressions.

Segments and subsegments can include a metadata object containing one or more fields with values of any type, including objects and arrays.

metadata and annotations does not record the application’s calls to your AWS services and resources.

A segment can break down the data about the work done into subsegments.

- Subsegments provide more granular timing information and details about downstream calls that your application made to fulfill the original request.
- A subsegment can contain additional details about a call to an AWS service, an external HTTP API, or an SQL database.

Below are the optional subsegment fields:

- namespace – aws for AWS SDK calls; remote for other downstream calls.
- http – http object with information about an outgoing HTTP call.
- aws – aws object with information about the downstream AWS resource that your application called.
- error, throttle, fault, and cause – error fields that indicate an error occurred and that include information about the exception that caused the error.
- annotations – annotations object with key-value pairs that you want X-Ray to index for search.
- metadata – metadata object with any additional data that you want to store in the segment.
- subsegments – array of subsegment objects.

Unlike CloudTrail, X-Ray doesnt record calls made to the AWS resources.

![img](https://docs.aws.amazon.com/xray/latest/devguide/images/scorekeep-PUTrules-timeline-subsegments.png)

For services that don’t send their own segments like Amazon DynamoDB, X-Ray uses subsegments to generate inferred segments and downstream nodes on the service map. This lets you see all of your downstream dependencies, even if they don’t support tracing, or are external.

Refactoring your application to send segment documents directly to X-Ray by using the **PutTraceSegments API** is incorrect because although this solution will work, it entails a lot of manual effort to perform. You don’t need to do this because you can just install the X-Ray daemon on the instance to automate this process.

adding annotations in the subsegment section of the segment document is the correct answer.

- Adding annotations in the segment document is incorrect because although the use of annotations is correct, you have to add this in the subsegment section of the segment document since you want to trace the downstream call to RDS and not the actual request to your application.

# CloudWatch

You can use the CloudWatch agent to collect both system metrics and log files from **Amazon EC2 instances** and **on-premises servers**. Aside from the usual metrics, it also tracks the memory, swap, and disk space utilization metrics of your server.

The data provided by CloudWatch is not as detailed as compared with the Enhanced Monitoring feature in RDS. Take note as well that you do not have direct access to the instances/servers of your RDS database instance, unlike with your EC2 instances where you can install a CloudWatch agent or a custom script to get CPU and memory utilization of your instance.

A namespace is a container for CloudWatch metrics. Metrics in different namespaces are isolated from each other, so that metrics from different applications are not mistakenly aggregated into the same statistics.

AWS_XRAY_DEBUG_MODE is used to configure the SDK to output logs to the console without using a logging library.

AWS_XRAY_TRACING_NAME is primarily used in X-Ray SDK where you can **set a service name** that the SDK uses for segments.

AUTO_INSTRUMENT is primarily used in X-Ray SDK for Django Framework only. This allows the recording of subsegments for built-in database and template rendering operations.

AWS_XRAY_CONTEXT_MISSING: The X-Ray SDK uses this variable to determine its behavior in the event that your function tries to record X-Ray data, but a tracing header is not available. Lambda sets this value to LOG_ERROR by default.

AWS_XRAY_DAEMON_ADDRESS: This environment variable exposes the X-Ray daemon’s address in the following format: IP_ADDRESS:PORT.

\_X_AMZN_TRACE_ID: Contains the tracing header, which includes trace ID, and parent segment ID. If Lambda receives a tracing header when your function is invoked, that header will be used to populate the \_X_AMZN_TRACE_ID environment variable. If a tracing header was not received, Lambda will generate one for you.

# CloudTrail

Although you can indeed use CloudTrail to track the API call, it can’t capture information about the IP traffic of your VPC. So, create a flow log in your VPC to capture information about the IP traffic going to and from network interfaces in your VPC.

# AppSync

AWS AppSync is quite similar with Amazon Cognito Sync which is also a service for synchronizing application data across devices. It enables user data like app preferences or game state to be synchronized as well however, the key difference is that, it also extends these capabilities by allowing multiple users to synchronize and collaborate in real time on shared data.

![img](https://d1.awsstatic.com/AppSync/product-page-diagram_AppSync@2x.d46d96d1e27169aa5005223299068da899280538.png)

# StepFunctions

SWF is incorrect because this is just a fully-managed state tracker and task coordinator service. It **does not provide serverless orchestration** to multiple AWS resources. Use step functions instead.

# SWF

What is SWF?

You can use **markers** to record events in the workflow execution history for application specific purposes. Markers are useful when you want to record custom information to help implement decider logic. For example, you could use a marker to count the number of loops in a recursive workflow.

Using **Signals** is incorrect because it just enables you to inject information into a running workflow execution. Take note that in this scenario, you are required to record information in the workflow history of a workflow execution.

Using **Timers** is incorrect because it just enables you to notify your decider when a certain amount of time has elapsed and does not meet the requirement in this scenario.

Likewise, using **Tags** is incorrect because it just enables you to filter the listing of the executions when you use the visibility operations, which once again does not meet the requirement in this scenario.
