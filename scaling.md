# Scaling, Load Balancing & High-Availability

## Regional and Global AWS Architecture

![img](./imgs/scaling/RegionalandGlobalInfrastructure2.webp)

---

## Elastic Load Balancer (ELB)

There are 3 types of ELB available within AWS:

- v1 (Classic Load Balancer - Not Recommended)
- v2 (Application Load Balancer - Recommended)
- v2 (Network Load Balancer - Recommended)

v2 load balancers are faster and `support target groups and rules`.

### Classic Load Balancer

Introduced in 2009. Is not replaced by v2 ELB which is recommended at the moment.

- Classic Load Balancer can load balance between HTTP, HTTPS and other lower level protocols
- Classic Load Balancer is not really layer 7 supporting. It lacks many feature.
- One limitation of Classic Load Balancer is that it only support 1 SSL certificate per load balancer

### Application Load Balancer

Supports layer 7 features. Such as:

- HTTP
- HTTPS
- WebSocket

### Network Load Balancer

Supports:

- TCP
- TLS (Secure form of TCP protocol)
- UDP

In general, this would be used for any application not using HTTP/HTTPS.

---

## Elastic Load Balancer Architecture

Its the job of the load balancer to accept connections from the customer.

- It then distributes these jobs to registered backend compute
- So the infrastructure can scale up or scale down without affecting the customer
- So even if the infrastructure fail and is repaired, user wouldnt be directly affected

Consider the following example where we have a single AWS VPC (region?) with two availability zone A and B. Each of the AZ has one public subnet and few private subnets.

An ELB is placed between these two nodes and which then adds one or more ELB nodes within the subnets that we pick. In our case we have chosen public subnets.

    Each ELB is configured with an A record DNS name.

    This resolves to the ELB nodes.

So any connection made using the DNS name of the ELB are actually made to the nodes of the ELB.

    The DNS name resolves to all of the individual nodes.

Any incoming request is distributed equally among the nodes of the ELB. [How?]

- The nodes are present if different AZ's
- If a node fails, it is replaced
- If the load to ELB increases, more nodes are provisioned inside each of the subnet the ELB is configured to use

![img](./imgs/scaling/ELBArchitecture1.webp)

### Type of ELB Nodes

The type of ELB node controls the IP addressing of the ELB nodes.

- Internet Facing (given public and private addresses)
- Internal Only (only private addresses)

ELB nodes are `configured with listeners` which `accept traffic on a port and protocol` and `communicate with targets on a port and protocol`.

Once a connection is made to the `Internet Facing ELB node`, it then makes a connection to the instances be it public or private instances.

    ELB requires that the subnet has 8+ free IPs per subnet.

Since 5 IP addresses are reserved by AWS. So we need `/27 or larger subnet` to deploy an ELB which can scale. (/28 also will work in certain scenarios)

    EC2 doesnt need to be public to work with an ELB

### Typical Multi-Tiered Application

Consider the following example of a VPC containing two AZs.

We also have an internet facing ELB. We have an Web Application Scaling Group (ASG), providing frontend capability of the application.

We also have another internal ELB. We have ASG for application instances. These are used by the web servers of the application.

And in the DB subnet, we have aurora instances.

#### The Problem

In this case everything is totally dependent on the subsequent layer.

- If the web instance user is connected to scaled or failed, there is an issue.
- If the app instances web layer is connected to scaled or failed, we have a problem.

#### The Solution

What we can do is that we can add ELB's between the layers (tiers), to abstract one tier from another.

![img](./imgs/scaling/ELBArchitecture2.webp)

In this case the user will connect directly to the ELB, which will connect to an application instance.

- So even if a instance fails or scales, user would be unaffected [How? What happens to the current request which was sent to failed instance?]

The web instance will connect to the app instance through an internal ELB. This would abstract away the direct connection between instances of these tiers.

    ELB's allow each tier to scale independently.

    If load on app tier increases, it can scale without affecting any other tier

### Cross-Zone ELB

Note that we use at least one ELB node per AZ. So, in the following example we have a simplified VPC with one ELB in each AZ.

So the user will connect to the DNS name of the application which points to the DNS name of the ELB DNS name.

The DNS name of a ELB will balance load equally between the ELB nodes. [How DNS name can send traffic to any of the ELB node?]

![img](./imgs/scaling/ELBArchitecture3.webp)

Historically LB nodes could only distribute load within the AZ they are in.

With `Cross-Zone Load Balancing` an ELB node can be distribute load across any of the instance in any AZ.

- This is now enabled by default for an application load balancer.
