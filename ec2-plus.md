# Bootstrapping EC2

Bootstrapping is a process where scripts or other config steps can be run when an instance is first launched. This allows an instance to be brough to service in a particular configured state.

In systems automation, bootstrapping allows the system to self configure or perform some self configuration steps. In AWS this is `EC2 Build Automation`.

This could perform some software installs and post install configs.

---

## User Data

Bootstrapping is done using user data - accessed via the meta data service. The following gives the user data part of the instance meta data. It logs the user data that was passed to the instance.

> http://169.254.169.254/latest/user-data

Anything you pass in is executed by the instance OS. `It is excecuted only once on launch`!

- User data is limited to 16 KB in size
- Can be modified when instance is in stopped state
- Its not secure and shouldnt be used for passwords or long term credentials
- The scripts are run as `root` user

EC2 doesn't validate the user data. You can tell EC2 to pass in trash data and the data will be passed. The OS needs to understand the user data.

NOTE: This is the part of `3: Configure Instance -> Advanced Details -> User Data` when you use launch an EC2 instance wizard.

NOTE: Logging for the scripts being run will be part of either `cloud-init.log` or `cloud-init-output.log`.

NOTE: When using CloudFormation, the UserData can be passed as well (but needs to be in Base 64 format).

---

## Boot-Time-To-Service-Time

This is a metric which measures how quickly you are able to bring an instance into service (say accept user requests).

- For AWS Managed AMI's, Launch Time is in minutes
- If you have bootstraping config post launch that takes long time, you could instead have these steps prebaked into the AMI to reduce the bootstraping time post launch. Thus reducing Boot-Time-To-Service-Time.

---

# EC2 Instance Roles

IAM roles are the best practice for AWS services to be granted permissions to other AWS services.

Allowing a service to assume a role grants the service the permissions that role has.

EC2 Instance Role allows anything running within that service permission that are attached to EC2 instance role.

NOTE: For a given instance in the EC2 instances list select "Instance -> Security -> Modify IAM Role"

---

## InstanceProfile

The instance profile is the item that allows the permissions inside the instance.

- When you create an Instance Role from the management console, InstanceProfile is automatically created with the same name used for Instance Role (eg. ExampleInstanceRole).
- From CLI both Instance Role and InstanceProfile need to be created individually.
- When using an Instance Role with EC2 instance, you are not attaching the Instance Role instead its the InstanceProfile thats attached

When IAM roles are assumed, you are provided temporary roles based on the permission assigned to that role. These credentials are passed through instance **meta-data**.

EC2 and the Secure Token Service (STS) ensure the credentials never expire.

To check the security credentails in use for a given EC2 instance:

> > http://169.254.169.254/latest/meta-data/iam/security-credentials/ExampleInstanceRole

Key facts

- Credentials are inside meta-data
- iam/security-credentials/role-name
- automatically rotated - always valid
- the resources need to check the meta-data periodically, if they intend to cache the credentials
- `should always use roles compared to storing long term credentials`
- CLI tools use role credentials automatically

---

# Credentials Precedence with CLI

Credentials Precedence used by CLI in order to interact with AWS

- `Command line options` – Overrides settings in any other location. You can specify --region, --output, and --profile as parameters on the command line.

- `Environment variables` – You can store values in your system's environment variables.

- `CLI credentials and config file` – The credentials and config file are updated when you run the command aws configure. The credentials file and config file is located at `~/.aws/credentials` and `~/.aws/config` respectively.

- `Container credentials` – You can associate an IAM role with each of your Amazon Elastic Container Service (Amazon ECS) _task definitions_. Temporary credentials for that role are then available to that task's containers.

- `Instance profile credentials` – You can associate an IAM role with each of your Amazon Elastic Compute Cloud (Amazon EC2) instances. Temporary credentials for that role are then available to code running in the instance. The credentials are delivered through the Amazon EC2 metadata service.

---

# AWS System Manager Parameter Store

Passing secrets into an EC2 instance is bad practice because anyone
who has access to the meta-data has access to the secrets.

Parameter store

- Is a public service and can be used by any service within AWS or anything having access to public store endpoints.
- Can be accessed by Applications, EC2 instances, lambda functions.
  - Any access needs to be authorized with IAM
  - Any access to encrypted data will need to decrypted using CMK inside KMS
- Allows for storage of `configuration` and `secrets`.
- You can store license codes, database strings, and full configs and passwords.
- Any changes can create events.
- Parameter store allows for hierarchies and versioning.

Public Parameters - Used to refer to latest AMI's per region.

---

## Create Parameter

Parameter Name (eg. `/wordpress/db-user`)

```
/wordpress/
/wordpress/db-user
/wordpress/db-password

/dev-team-passwords/
```

Standard Tier

- Allows upto 10,000 parameters
- Parameter value size upto 4KB

Advanced Tier

- Allows more than 10,000 parameters
- Parameter value size upto 8KB

Parameter Type:

- Strings (any string value)
- StringList (comma seperated string values)
- SecureString (sensitive data encrypted using KMS of same or another account)

Data Type

- `text` - Plain text data is suitable for DB Strings, Connection Strings, DB Usernames.
- Ciphertext data allows us to store data encrypted using KMS (KMS permission needs to be configured).

Value (eg. `dbadmin`)

---

## CLI

To get a single parameter

> aws ssm get-paramters --names /wordpress/db-user

To get all the parameter inside a hierarchy

> aws ssm get-paramters-by-path --names /wordpress/

To get all the parameter with unencrypted values

> aws ssm get-paramters-by-path --names /wordpress/ --with-decryption

(Decryption requires permission to access parameter store and permission for the CMK from KMS used to encrypt the data)

---

# System and Application Logging on EC2

Cloudwatch monitors the outside metrics of an instance
Cloudwatch logs is for logging

Both of them doesnt natively capture data inside an instance.

CloudWatch Agent is required for OS visible data. It sends this data into CW

For CW to function, it needs configuration and permissions in addition
to having to install the cloud watch agent.

The cloudwatch agent needs to know what information to inject
into cloud watch and cloud watch logs.

## Permission

The agent needs some permissions to interact with AWS. This is done with an IAM role as best practice. `The IAM role is attached to the instance` which provides the instance and anything running on the instance, permissions to manage CW logs.

The role say `CloudWatchRole` will require following policies to be attached:

- CloudWatchAgentServerPolicy
- AmazonSSMFullAccess

The data is then injected into CW logs.

## Logging Config

There is `one log group for each individual log` we want to capture.

There is `one log stream for each group for each instance` that needs management.

We can use parameter store to capture the configuration for the CW agent.

## Config Wizard

Wizard is intitiated through CLI using

> sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard

Log file path

> /var/log/httpd/access_log
> group name: access_log

> /var/log/httpd/error_log
> group name: error_log

Config post the wizard is located at

> /opt/aws/amazon-cloudwatch-agent/bin/config.json
