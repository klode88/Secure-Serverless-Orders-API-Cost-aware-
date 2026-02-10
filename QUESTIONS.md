# Questions related to Project 3 – Secure Serverless Orders API

This file contains common interview-style questions and short, clear answers based on the architecture:

API Gateway + Lambda + DynamoDB + Cognito + AWS WAF.

---

## 1. Can you make this API private? How would it work?

Yes.

The API can be created as a **Private API** and accessed only through a **VPC interface endpoint**.

Flow becomes:

Client inside VPC  
→ VPC endpoint  
→ API Gateway (private)  
→ Cognito authorizer  
→ Lambda  
→ DynamoDB

**Trade-off:** more networking complexity and the API is no longer publicly accessible.

---

## 2. Why did you use API Gateway instead of an Application Load Balancer?

Because API Gateway integrates natively with:

- Lambda
- Cognito authorizers
- AWS WAF

It is built specifically for APIs.

**Trade-off:** API Gateway is more expensive per request than ALB.

---

## 3. Why did you choose REST API instead of HTTP API?

REST API works cleanly with:

- Cognito authorizers
- AWS WAF
- mature enterprise features

**Trade-off:** HTTP API is cheaper and faster.  
For a cost-optimised production system, HTTP API would be a good upgrade.

---

## 4. Why do you need both Cognito and WAF?

They solve different problems.

- **Cognito** controls who can call the API.
- **WAF** controls what traffic is allowed to reach the API.

Bots and scanners can still hit the endpoint even if authentication exists.

---

## 5. Why did you use DynamoDB instead of RDS?

The access pattern is simple:

- create order
- retrieve order by ID

DynamoDB fits this very well.

**Trade-off:**

- no joins
- limited query patterns

**Cost advantage:**

- no running database instances
- no idle cost
- automatic scaling

---

## 6. How does cost scale when traffic increases?

In this project:

1 API request  
→ 1 Lambda invocation  
→ 1 DynamoDB write

Cost grows mainly with:

- API requests
- Lambda duration
- DynamoDB writes
- WAF inspected requests
- Cognito monthly active users

---

## 7. Which service is usually the most expensive at low traffic?

Usually **AWS WAF**.

Because it has a base monthly cost even when traffic is very small.

---

## 8. How does WAF help control cost?

WAF blocks bad or abusive traffic before it reaches:

- Lambda
- DynamoDB

This prevents unnecessary compute and database usage.

---

## 9. What happens if the Lambda function becomes slow?

- Lambda cost increases (duration-based billing)
- API latency increases

**Production controls:**

- keep functions small
- avoid heavy libraries
- monitor duration metrics

---

## 10. How would you restrict who can create orders (admin vs normal user)?

Use **Cognito groups**.

The token contains the claim:


You can enforce group checks:

- inside the Lambda function
- or using a Lambda authorizer

---

## 11. What are the main security risks in this design?

- overly permissive IAM roles
- missing rate limits
- leaving WAF rules in COUNT mode permanently
- missing monitoring and alerts

---

## 12. How would you monitor this system in production?

At minimum:

- API Gateway 4XX and 5XX alarms
- Lambda error and duration metrics
- DynamoDB throttle metrics
- WAF blocked request metrics

---

## 13. Can this architecture handle sudden traffic spikes?

Yes.

- API Gateway scales automatically
- Lambda scales automatically
- DynamoDB on-demand scales automatically

No capacity planning is required.

---

## 14. What is the biggest trade-off of this serverless design?

You trade **control for simplicity**.

You gain:

- no servers
- no patching
- no manual scaling

But:

- less control over runtime behaviour
- more distributed monitoring and debugging

---

## 15. Why was DynamoDB created before Lambda and API Gateway?

Because of dependency order.

- Lambda needs the table name and permissions
- API Gateway needs the Lambda
- Cognito and WAF attach to the API

Build order:


Runtime order is different.

---

## 16. If this was a production system, what would you change first?

- Replace wide IAM permissions with least-privilege roles
- Consider HTTP API instead of REST API
- Add usage plans and throttling
- Move selected WAF rules from COUNT to BLOCK
- Add monitoring and alarms
- Separate dev and prod environments

---

## 17. Can this API be restricted to only your own frontend?

Yes.

You would:

- place CloudFront in front of the API
- restrict API Gateway using a resource policy
- only allow requests coming from CloudFront

**Trade-off:** more services and slightly higher cost.

---

## 18. Give one short explanation of how the security layers work together.

Cognito controls **who** can access the API,  
WAF controls **what traffic** is allowed to reach it,  
and API Gateway enforces both before any compute or database resources are used.
