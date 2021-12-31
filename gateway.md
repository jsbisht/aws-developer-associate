https://learn.cantrill.io/courses/1101194/lectures/27852538

- errors list in this video
- and https://docs.aws.amazon.com/apigateway/api-reference/handling-errors/#api-error-codes

### API Gateway

Application Programming Interface (API)

This is a way that applications or services can communicate with each other.

Endpoints are used to access services. Each service has its own endpoint
in its own region.

When you request AWS stops an EC2 instance, the message is set to the API
in that region for that resource.

APIs also perform authentication using passwords or keys. API authorizes
each service and needs your permissions verified each time.

#### Authentication

#### Authorization

API gateway is an AWS managed service that provides managed API endpoints.
Allows you to create, publish, monitor, and secure APIs as a service.

Billed based on the number of API calls as well as data transfered.

This can be used for serverless architecture to provide an entry point
for that design.

This is great during an architecture evolution.

Step 1:
Create a managed API and point at the existing monolithic application.

Step 2:
Using API gateway allows the buisness to evolve along the way slowly.
This might move some of the data to fargate and aurora architecture.

Step 3:
Move to a full serverless architecture with DynamoDB
