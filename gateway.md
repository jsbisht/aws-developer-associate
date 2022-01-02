# API Gateway

## Overview

https://learn.cantrill.io/courses/1101194/lectures/27852538

### Considerations

API Gateway acts as an endpoint or entrypoint for your applications.

    It sits between applications that uses API's and backend services

API Gateway features include:

- High availability
- Scalable
- Handles authorisation
- Throttling (can restrict how often users can access API)
- Caching
- CORS (supports cross domain restrictions and configuration)
- Transformations
- OpenAPI spec (makes it easier to create defination files)
- Direct Integration

It can connect to services in AWS or on-premises.

It supports

- HTTP APIs
- REST APIs
- WebSockets APIs

### Architecture

Applications or APIs on the left connect to API Gateway using the API endpoint address.

API gateway acts as a connector of application using the endpoint and the backend services listed on the right.

API gateway goes through three phases:

1. Request
2. Integrations
3. Response

API gateway also integrates with CloudWatch to store logging and metrics based data.

API gateway also provides a cache to reduce the number of calls to backend.

![img](./ss/api-gateway-overview.webp)

### Authentication

API gateway support a range of authentication methods.

You can make the API complete open access, so no authentication is required.

API gateway can use AWS Cognito User Pools for authentication.

- Client authenticates with Cognito and receives a token
- The token is then passed in the subsequent requests to API gateway
- API gateway validates the token with Cognito

API gateway can also be extended to use Lambda based authorisation (earlier known as custom authoriser).

- Client calls API gateway with a bearer token
- API gateway calls Lambda authorizer to validate the token
- On successful validation, it `returns an IAM policy and principal identifier` to API gateway. Which `handles the request via Lambda integration`.
- On failed validation, it returns `403 ACCESS_DENIED` to the client

![img](./ss/api-gateway-authentication.webp)

### Endpoint Types

Edge-Optimized

- Requests are routed to nearest CloudFront POP (Point of Presense)

Regional Endpoint

- Doesnt uses CloudFront network
- Used when client are in the same region

Private Endpoint

- Accessible only within a VPC `via interface endpoint`

### Stages

When you deploy API configuration in an API gateway to a stage.

- Each stage has a unique API endpoint and settings
- Each stage can be deployed individually

Following is an example of different version used by each stage backed by its own Lambda functions.

![img](./ss/api-gateway-stages.webp)

You can also enable canary deployment which will be deployed on the canary part of the stage and not the stage itself.

- Stages enabled for canary deployments can be configured so a certain percentage of traffic is sent to the canary.
- Canary can be later promoted to be the base stage.

### Error Codes (Client and Server Errors)

A response code of 2xx indicates the operation was successful. Other error codes indicate either a client error (4xx) or a server error (5xx).

Some errors are resolved if you simply retry the same request.

| HTTP Status Code | Error                                                                               | code Retry        |
| ---------------- | ----------------------------------------------------------------------------------- | ----------------- |
| 400              | Bad Request Exception                                                               | No                |
| 403              | Access Denied Exception                                                             | No                |
| 404              | Not Found Exception                                                                 | No                |
| 409              | Conflict Exception                                                                  | No                |
| 429              | Limit Exceeded Exception                                                            | No                |
| 429              | Too Many Requests Exception                                                         | Yes               |
| 502              | Bad Gateway Exception                                                               | Yes if idempotent |
|                  | usually for an incompatible output returned from a Lambda proxy integration backend |                   |
|                  | occasionally for out-of-order invocations due to heavy loads                        |                   |
| 503              | Service Unavailable Exception                                                       | Yes               |
| 504              | Endpoint Request Timed-out Exception                                                | Yes if idempotent |

### Caching

    Cache is defined per stage within API Gateway

![img](./ss/api-gateway-cache.webp)
