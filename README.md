# Project 3 – Secure Serverless Orders API (Cost-aware)

A small, production-style serverless backend for creating customer orders, focused on **security, low operational overhead and predictable cost**.

---

## Architecture overview

This project uses:

- IAM user group (for administrator / engineer access to AWS resources)
- DynamoDB (order storage)
- AWS Lambda (business logic)
- Amazon API Gateway (REST API)
- Amazon Cognito (user authentication)
- AWS WAF (request filtering and protection)
- Amazon CloudWatch Logs (logging and monitoring)

---

## Why we used an IAM user group (not direct user permissions)

We created a dedicated IAM **user group** and attached all required policies to the group.

This is used for **administrative access to AWS resources**, not for API authentication.

**Why**

- Centralised permission management
- Easier onboarding for new engineers
- Cleaner auditing and access control
- Matches real production practice

**Trade-off**

- Slightly more setup than attaching policies directly to a user

---

## Why DynamoDB (created first)

DynamoDB was created first because the Lambda function depends on it.

**Why DynamoDB**

- Fully serverless
- No capacity planning
- Scales automatically
- Very low operational overhead

**How it is used**

- Stores one item per order
- `orderId` is the partition key

**Cost decision**

- On-demand billing (pay per request)

**Trade-off**

- Limited querying compared to relational databases
- No joins or complex relationships

---

## Why Lambda

Lambda is the application and business logic layer.

**Why**

- No servers to manage
- Automatic scaling
- Pay only when code runs

**How it is used**

- Validates the request body
- Generates a unique order ID
- Writes one record to DynamoDB

**Security**

- The Lambda execution role is limited to `dynamodb:PutItem` on the Orders table (least-privilege access)

**Trade-off**

- Possible cold starts at low traffic
- Stateless execution model

---

## Why API Gateway (REST API)

API Gateway exposes the public HTTP endpoint.

**Why REST API**

- Native integration with Cognito authorizers
- Works cleanly with AWS WAF
- Common enterprise and production choice

**How it is used**

- Public endpoint: `/orders`
- POST method integrated with Lambda using proxy integration

**Trade-off**

- More expensive than HTTP API
- Slightly higher latency

---

## Why Amazon Cognito

Cognito is used to protect the API with authentication.

**Why**

- Fully managed user directory
- No custom authentication backend required
- Direct integration with API Gateway

**How it is used**

- API Gateway uses a Cognito authorizer
- Only authenticated users can invoke the endpoint

**Trade-off**

- More configuration compared to a very simple custom login solution
- Pricing depends on monthly active users

---

## Why AWS WAF

WAF is used to protect the API from abusive or malicious traffic.

**Why**

- Blocks common attacks and scanners
- Reduces abusive traffic before it reaches API Gateway and Lambda
- Helps protect availability and control cost

**How it is used**

- A Web ACL is associated with the API Gateway stage
- Managed rule groups and a rate-based rule are enabled

**Trade-off**

- Fixed monthly cost for Web ACL and rules
- Still charged per inspected request

---
## How the system works in real time

### Request flow (runtime path)

1. The client sends a POST request to /orders.

2. The request reaches API Gateway (AWS WAF is attached and inspects the request first).

3. API Gateway validates the access token using the Cognito authorizer.

4. If the token is valid, API Gateway invokes the Lambda function.

5. Lambda validates the request and writes the order into DynamoDB.

6. Lambda returns a response (for example: 201 Created and an orderId).

7. API Gateway returns the response to the client.

8. Logs are stored in CloudWatch Logs for API Gateway, Lambda and WAF.


## Why the build order was different from the runtime flow

The build order follows **service dependencies**.  
The runtime flow shows the **real request path**.

### Build order

- IAM user group
- DynamoDB table
- IAM execution role for Lambda
- Lambda function
- API Gateway REST API and `/orders` method
- Cognito user pool and app client
- Cognito authorizer attached to API Gateway
- AWS WAF Web ACL attached to the API Gateway stage
- CloudWatch logging

### Runtime order

Client  
→ API Gateway (with WAF attached)  
→ Cognito authorizer  
→ Lambda  
→ DynamoDB  
→ back to the client

---

## Cost design and trade-offs

This project is designed to remain low-cost and scale only when needed.

### Main cost drivers

| Service      | Cost driver                               |
|-------------|--------------------------------------------|
| API Gateway | Number of requests                         |
| Lambda      | Invocations and execution time             |
| DynamoDB    | Read and write requests                    |
| Cognito     | Monthly active users                       |
| WAF         | Web ACL, rule count and inspected requests |

---

## Why this architecture is cost-efficient

- No always-running infrastructure
- No capacity reservation
- Automatic scaling
- Costs grow linearly with usage

---

## Example cost behaviour

### Small business workload

- Around 10,000 API requests per month
- Few active users
- One DynamoDB write per request

Result:

- Compute and database costs remain very small
- WAF becomes the main fixed monthly cost

---

### Traffic spike (promotion day)

- Sudden increase in requests

Result:

- No scaling actions required
- Cost increases only during the spike window

---

### Abuse or bot traffic

- High number of unwanted requests

Result:

- WAF blocks requests early
- Lambda and DynamoDB are protected from unnecessary usage

---

## Production cost-control decisions

- Enable WAF rate-based rules
- Keep Lambda functions lightweight and fast
- Use DynamoDB on-demand for unpredictable traffic
- Monitor traffic and errors using CloudWatch
- Keep the number of WAF managed rule groups minimal

---

## Summary

This project delivers a secure and cost-aware serverless backend that:

- Exposes a protected public REST API
- Uses managed authentication and edge protection
- Requires no server management
- Scales automatically
- Keeps operational and infrastructure costs aligned with real usage


