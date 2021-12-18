# Cloud HSM

![img](./imgs/hsm-1.png)

![img](./imgs/hsm-2.png)

![img](./imgs/hsm-3.png)

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
