# Architechture Referesher

Monolithic systems even when idle, occupy a lot of resources, so are billed more than needed.

With Tiered application, at least one instance of all the tier should be functional, as each tier is directly connected to each other.

Evolving with Queues, we can have events being placed into a queue for processing irrespective of if processing layer is funtional.

- ASG can look for minimum length of queued items and launch desired number of instances accordingly.

Processing layer can be further broken down using Microservice Architecture.

- Process
- Upload
- Store and Classify

If every component of an application needed a queue between another part of the application, it will be really complex architecture.

- In this case the event driven architecture comes into picture
- Event Router facilitate co-ordination between Event Producer and Event Consumer using Event Bus.
- No constant running or waiting for things
- Producer generate events when something happens
- Events are delivered to consumers of events

Mature event-driven architecture `only consumes resources while handling events (serverless)`.

## AWS Lambda

Docker is not supported or is an anti-pattern with Lambda functions.

> Custom runtimes such as `Rust` are possible using `layers`.

While defining a Lambda function you can only define the amount of memory, vCPU is allocated indirectly relative to the memory

- This can be from 128MB to 3GB (64MB steps increment allowed)
- 512 MB storage avaiable as `/tmp`
- 900s (15 minutes) function timeout

An Execution Role is required for Lambda function to work.

    Execution Role is assumed by the Lambda function

    This provides permissions to interact with other AWS services

### Use Cases

- Serverless Applications (S3, API Gateway, Lambda)
- File Processing (S3, S3 Events, Lambda)
- Database Triggers (DynamoDB, Streams, Lambda)
- Serverless CRON (EventBridge/CloudWatch Events with Lambda)
- Realtime Stream Data Processing (Kinesis with Lambda)
