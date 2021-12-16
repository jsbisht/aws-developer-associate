# Virtualization Fundamentals

## SR-IOV

This overcomes the problem with network IO being the bottleneck while a host is running multiple virtual machines all having network intensive work load. It does this by virtualizing the network card. This means less CPU usage for the guest.

In EC2 - This is Enhanced Networking.

https://learn.cantrill.io/courses/1101194/lectures/27026221

# EC2 Architecture and Resilience

EC2 host

- Runs within single AZ

When instances are provisioned within a specific subnet within a VPC, a primary elastic network interface is provisioned in a subnet which maps to the physical hardware on the EC2 host.

- Subnets are also within one specific AZ.
- Instances can have multiple network interfaces even within different subnets, as long as they're within the same AZ.

Elastic Block Store (EBS)

- EC2 can make use of remote storage, Elastic Block Store (EBS).
- EBS also runs within a specific AZ.
- EC2 instance in one AZ cant access EBS volume of another AZ.
- EBS allows you to allocate volumes of persistent storage to instances within the same AZ.

---

## EC2 Categories

**General Purpose** - default steady state workloads with even resources
**Compute Optimized** - Media processing, scientific modeling and gaming
**Memory Optimized** - Processing large in-memory datasets
**Accelerated Computing** - Hardware GPU, FPGAs
**Storage Optimized** - Large amounts of super fast local storage. Massive amounts of IO per second. Elastic search and analytic workloads.

### Naming Scheme

**R5dn.8xlarge** - whole thing is the instance type. When in doubt give the
full instance type

- 1st char: `Instance family`. No expectation to remember all the details
- 2nd char: `Instance generation`. Generally always select the newest generation.
- chars before period: `Additional capabilities`
  - a: amd cpu
  - d: nvme storage
  - n: network optimized
  - e: extra capacity for ram or storage
- char after period: `Instance size`. Memory and CPU considerations.
  - often easier to scale system with a larger number of smaller instance sizes

![img](imgs/ec2-instance-types.png)

https://aws.amazon.com/ec2/instance-types/

### EC2 SSH vs EC2 Instance Connect management

https://learn.cantrill.io/courses/1101194/lectures/27806428

---

[Storage Fundamentals](./storage.md)

---

# EC2 Network Interfaces, Instance IPs and DNS

Every EC2 has at least one Elastic Network Interface (ENI) which is the primary ENI.

We can attach secondary ENIs to an EC2 instance which are in seperate subnets, but everything must be within one AZ.

When you launch an instance, the Security Groups are on the network interface and not the instance.

---

## Elastic Network Interface

- Has a MAC address
- Has a Primary IPv4 private address
  - Belongs to, the range of the subnet, the ENI is within.
- 0 or more secondary private IP addresses
- `0 or 1 public IPv4 address` (One per interface)
- `1 elastic IP per private IPv4 address` (One per private IP address)
- 0 or more IPv6 address on the interface (all public IP addresses by default)
- Security groups
  - This applies to the network interfaces and will impact all IP addresses on that interface
  - if you need different IP addresses impacted by different security
    groups, then you need to make multiple interfaces and apply different
    security groups to those interfaces
- Source / destination checks
  - If traffic is on the interface, it will be discarded if it is not from `one of the IP addresses on the interface as source` or going to `one of the IP addresses on the interface as destination`
  - This setting needs to be disabled for an EC2 instance to work as a NAT instance.

## **NOTE**: Secondary interfaces function in all the same ways as primary interfaces except you can detach interfaces and move them to other EC2 instances.

## Primary IPv4 private address

Given a DNS name that is associated with the private IP say `ip-10-16-0-10.ec2.internal`. Its only resolvable inside the VPC and always points to private IP address.

---

## Public IPv4 address

- Instance must manually be set to recieve an IPv4 addr
- Settings into a subnet which automatically allocates an IPv4
- Dynamic IP that is not fixed
- If you stop an instance the address is deallocated.
- When you start up again, it is given a brand new IPv4 address
- Restarting the instance will not change the IP address
- Changing between EC2 hosts will change the address

Public IPv4 address is allocated a public DNS name

- Inside the VPC, Public DNS name will resolve to the primary private IPv4 address of the instance
- Outside the VPC, Public DNS name will resolve to the public IP address

Public IPv4 address is not directly attached to the instance or any of the interface, its associated with it.

- Translation of this address to private IPv4 address is done by AWS Internet Gateway

---

## Elatic IP address

We can have `1 public elastic IP per private IPv4 address` (One per private IP address)

- Allocated to your AWS account
- Can associate with a private IP on the primary interface or on the secondary interface.
- If you are using a public IPv4 and assign an elastic IP to an instance, the original dynamic IPv4 address will be lost.
- If you remove the elastic IP from the instance, it gets a new dynamic IPv4 address.

---

## Considerations

If you provision a secondary ENI's mac address a license, you can move around the license to different EC2 instances by dettaching and attaching the secondary ENI to that instance.

If you need different rules for different IPs, then you need to multiple ENI with different Security Group on each.

The OS never see's the IPv4 public address.

- NAT plays a role here
- You always configure the private IPv4 private address on the interface.
- Never configure an OS with a public IPv4 address.

---

# Amazon Machine Image (AMI)

AMI's can be used to launch EC2 instance.

Also, you can create an AMI from an existing EC2 instance to capture the current config.

- When you launch an EC2 instance, you are using an Amazon provided AMI.
- Can be Amazon or community provided
- Marketplace (can include commercial software)
  - Will charge you for the instance cost and an extra cost for the AMI
- Region wide unique ID (ami-`random set of chars`)
- Controls permissions of who can access the AMI
  - Default to only your account being able to access it
  - Can be set to be public
  - Can be made accessible to specific AWS accounts

---

## AMI Lifecycle

### 1. Launch

You could launch it with instance volume or EBS volume attached.

### 2. Configure

Post launch, you can customize the instance's with certain applications installed, services setup or certain volumes attached of certain sizes.

### 3. Create Image

Once an instance has been customized, an AMI can be created from that.

- AMI will contain the permission of who can use it.
- During image creation, snapshots of any EBS volumes attached will be taken.
- These snapshots will be referenced within the AMI as `Block Device Mapping`.
- Block device mapping links the snapshot IDs and a device ID for each snapshot.

An Amazon Machine Image (AMI) is stored in Amazon S3, but it is not directly accessible. Instead, you must use API calls or the Management Console to use an AMI.

**NOTE**: You can change the EBS volume size during the AMI creation.

### 4. Launch

When the AMI is used to create a new instance, this new instance will have the same EBS configuration as the original instance.

During the launch:

- Snapshots are used to create new EBS volumes in the AZ where you are launching the instance into.
- These new EBS volumes are attached to the new instance using the same device ID contained in the `Block Device Mapping`.

AMI are regional construct:

- The AMI from AZ-1, is stored in the region. Snapshot of EBS volumes are stored in S3, which is already a regional service.
- You can take the AMI and create the new instance in the same AZ or another AZ in the same region.

**NOTE**: AMI's doesnt contain the data.

---

## Consideration

- AMI can only be used in one region.
- `AMI can be copied between regions`
- AMI Baking: creating an AMI from a configured instance.
- An AMI cannot be edited. If you need to update an AMI, launch an instance, make changes and then make new AMI.
- Remember permissions by default are your account only. You can extend that to other account, partner account or make it completely public.
- `Billing` is for the storage capacity for the EBS snapshots the AMI references

---

## Copying

### Cross-account copying

The owner of the source AMI is charged standard Amazon EBS or Amazon S3 transfer fees, and you are charged for the storage of the target AMI in the destination Region.

### Cross-Region copying

AMI once created in a region, can be copied between regions.

---

## Encryption

You can enable `target EBS snapshots encryption` while copying AMI between regions.

- The default master key is used for encryption.
- You can change it to Customer Master Key as well.

Since KMS is regional service, when moving an encrypted AMI it will be encrypted in the new region using the master key (default or CMK) of that region.

### Encryption and copying

The following table shows encryption support for various AMI-copying scenarios.

| Scenario | Description                | Supported |
| -------- | -------------------------- | --------- |
| 1        | Unencrypted-to-unencrypted | Yes       |
| 2        | Encrypted-to-encrypted     | Yes       |
| 3        | Unencrypted-to-encrypted   | Yes       |
| 4        | Encrypted-to-unencrypted   | No        |

**NOTE**: You cannot copy an encrypted snapshot to yield an unencrypted one.

---
