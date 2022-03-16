## Course Fundamentals and AWS Accounts

### [DEMO] Securing An AWS Account

Using MFA to secure an account.

### [DEMO] Creating a Budget

Monthly 10 USD budget for the account.

### [DEMO] Adding an IAM Admin - GENERAL ACCOUNT

Using MFA to secure General account.

### [DEMO] Adding an IAM Admin User - PRODUCTION ACCOUNT

Using MFA to secure Production account.

### [DEMO] Creating Access keys and setting up AWS CLI v2 tools

https://learn.cantrill.io/courses/1101194/lectures/24949221

Create access keys to access aws services. Use that to create a profile through CLI

> aws configure --profile iamadmin-general

Post this try accessing

> aws s3 ls

That will use default profile which doesnt have access to s3.

So, we use named profile.

> aws s3 ls --profile iamadmin-general

## AWS Fundamentals

### [DEMO] My First EC2 Instance

https://learn.cantrill.io/courses/1101194/lectures/24978332

### [DEMO] My First S3 Bucket [UPDATED202202]

https://learn.cantrill.io/courses/1101194/lectures/24978334

### [DEMO] Simple Automation With CFN [UPDATED202202]

https://learn.cantrill.io/courses/1101194/lectures/25216284

### [Demo] Simple Monitoring with Cloudwatch

https://learn.cantrill.io/courses/1101194/lectures/25301525

## IAM, ACCOUNTS AND AWS ORGANISATIONS

### [DEMO] Simple Identity Permissions in AWS [UPDATED202202]

https://learn.cantrill.io/courses/1101194/lectures/25335803

Using the cloudformation template create cats and animal bucket in s3. It also creates permission policy to allow access to all s3 buckets except cats bucket and its files. Along with that a user sally is created.

To user sally assign permission policy to allow access to all buckets except cats bucket.

### [DEMO] Permissions control using IAM Groups [UPDATED202202]

https://learn.cantrill.io/courses/1101194/lectures/25335805

Using the cloudformation template create cats and animal bucket in s3. It also creates permission policy to allow access to all s3 buckets except cats bucket and its files. Along with that a user sally is created.

Add user sally to a group and assign permission policy to sally through the group.

### [DEMO] AWS Organizations - PART1 [NEWUI]

https://learn.cantrill.io/courses/1101194/lectures/25362688

Using General (training) account as the management account of the AWS organisation, add production account to the organsiation. Also, create another account from within AWS Organisation.

OrganizationAccountAccessRole needs to be created for role switching within production account.

### [DEMO] AWS Organizations - PART2 [NEWUI]

https://learn.cantrill.io/courses/1101194/lectures/25362689

From General (training) account, use Switch Role to log into Production account using the account id and OrganizationAccountAccessRole role. Similarly, use Switch Role to log into Development account.

### [DEMO] Using Service Control Policies

https://learn.cantrill.io/courses/1101194/lectures/25362691

### [DEMO] Implementing an Organizational Trail

https://learn.cantrill.io/courses/1101194/lectures/25527525

Management events (api activity, kms events, starting and stopping ec2, etc) - charges
Data events (s3 and lambda) - charges
Insight events (unusual activities) - charges

See the cloudtrail in:

- CloudWatch (Log Group having account id)
- S3 bucket

Event History (no cloud trail required)
