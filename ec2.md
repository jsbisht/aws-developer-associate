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
