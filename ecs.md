# Elastic Container Service (ECS)

ECS is a managed container based compute service.

You can always run docker containers on an EC2 instance.

ECS allows you to run containers on infrastructure which is fully or partially managed by AWS.

ECS management components

- Scheduling and Orchestration
- Cluster manager
- Placement engine

---

## ECS Cluster

Clusters is where your containers run from. You provide ECS with an image, and it run that as container inside the cluster.

You create a cluster and then either deploy a `Task` or a `Service` within that.

---

## ECS Concepts

### Container definition

This is used to let ECS know where the container image is located. The port to be used for creating the container.

### Task definition

Task in ECS defines a self contained application. A task can have `one or more` application defined.

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

**NOTE**: Container Defination is defined inside task defination.

### Task role

Task role is an IAM role that the task assumes. The task gains temporary credentials that allow access to AWS products and services.

### Service definition

Service defination defines how we want a task to scale. This allows us to have multiple independent copies of our task to run.

- You can deploy a load balancer in front of the service so that incoming load is distributed across all the tasks.
- So for tasks running within ECS that are long running, we use a service to provide scalability and high availability.
- Service lets you replace failed tasks, distribute loads across copies of the task.

---

# ECS - Cluster Mode

ECS users clusters which run in two mode:

- EC2 mode (uses EC2 instances as container hosts, can be seen to be running ECS software)
- Fargate mode (serverless way of running docker containers where AWS manages the container host part, leaving you to architect your environment using containers)

Demo: [How to run task using fargate mode](https://learn.cantrill.io/courses/1101194/lectures/36185027)

---

## ECS - EC2 Mode [EC2 instance or EC2 host ???]

ECS cluster is created within a VPC. It benefits from the multiple AZs that are within that VPC.

- EC2 instances are used to run containers
- When you create the cluster you specify the number of containers in that cluster.
- The number of containers to be maintained in a cluster is controlled by Auto Scaling Group

Note that while using EC2 mode, even though the containers are not running on the EC2 instances we will still be billed for them.

- In EC2 mode, we are responsible for managing the capacity of the cluster

Tasks and Services use images on container registry (ECR). Via the task and service defination container images are deployed onto the EC2 host.

- ECS will manage the number of task that are deployed if you use service and service defination.
- You need to manage the capacity at the cluster level.

**NOTE**: EC2 Linux/Windows + Networking

### Pricing

With EC2 mode you pay for the EC2 instances regardless of container usage

You can take advantage of spot pricing or reserved EC2 servers.

---

## ECS - Fargate mode

Removes the management overhead from ECS, no need to manage EC2.

Its nearly same as EC2 mode in terms of how things work.

It differs from EC2 mode in terms of using a `shared fargate infrastructure`, which allows all customers to access from the same pool of resources.

Fargate deployment still uses a cluster with a VPC where AZs are specified.

**NOTE**: Tasks and services are actually running from shared infrastructure, then they are injected into your VPC setup.

- Each task is given an elastic network interface which has an IP address within the VPC. They then run like an VPC resource.

You do have choose to deploy into new VPC or choose existing one that you had designed.

**NOTE**: Fargate (Network Only)

### Pricing

You pay only for container resources that you consume.

---

## EC2 vs ECS(EC2) vs Fargate

If you already are using containers, use **ECS**.

**EC2 mode** is good for a large workload with price conscious. This allows for spot pricing and prepayment. But you need to manage the scaling, failing instances, etc.

Large workload but overhead conscious **Fargate**.

Small or burst style workloads **Fargate** makes sense.

Batch or periodic workloads **Fargate**.

---

## Task placement strategy

A task placement strategy is an algorithm for selecting instances for task placement or tasks for termination.

Amazon ECS supports the following task placement strategies.

`binpack` - Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.

`random` - Place tasks randomly.

`spread` - Place tasks evenly based on the specified attribute.

**NOTE**: You can use task placement constraints to prevent tasks from launching on cluster instances with certain properties.

---

## Consideration

As you monitor Amazon ECS, store historical monitoring data so that you can compare it with current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues.

To establish a baseline, you should, at a minimum, monitor the following items:

- The `CPU and memory reservation and utilization metrics` for your Amazon ECS clusters
- The `CPU and memory utilization metrics` for your Amazon ECS services

---

## CLI

- The Amazon ECS CLI basic configuration information is stored in ~/.ecs/config file.
- The Amazon ECS CLI makes calls to AWS APIs on your behalf by using these credentials.

---
