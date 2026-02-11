# Project build – evidence screenshots

This folder contains step-by-step evidence of building the
Secure Serverless Orders API (cost-aware) using AWS services.

The screenshots follow the real build order.

---

## 1. IAM user group created

**File:** `1 usergroup created.png`

Purpose:
Create a user group to manage access for this project instead of attaching permissions directly to individual users.

Why:
Using groups improves access control, reduces operational risk and scales better for teams.

---

## 2. DynamoDB table created

**File:** `2 dynamodb table created(655).png`

Purpose:
Create the Orders table to store customer orders.

Why:
DynamoDB was chosen for its serverless scaling, low operational overhead and predictable on-demand pricing.

---

## 3. Lambda execution role (least-privilege)

**File:** `3 Lambda execution role with least-privilege.png`

Purpose:
Create an IAM role for the Lambda function.

Why:
The role only allows required actions (PutItem on the Orders table and CloudWatch logging).

Trade-off:
More secure, but requires careful policy design compared to using broad managed policies.

---

## 4. API Gateway permission to invoke Lambda

**File:** `4 grant api to call lambda function.png`

Purpose:
Allow API Gateway to invoke the CreateOrder Lambda function.

Why:
This enables secure service-to-service integration without exposing credentials.

---

## 5. API Gateway resource created

**File:** `5 resource creation customer orders.png`

Purpose:
Create the `/orders` resource in API Gateway.

Why:
This defines the REST path used by clients to submit new orders.

---

## 6. POST method created for /orders

**File:** `6 POST method created for the orders resource.png`

Purpose:
Create a POST method and integrate it with the Lambda function.

Why:
POST is used to create new orders and invoke backend processing.

---

## 7. Cognito app client created

**File:** `7 application client OrdersUserPool created inside.png`

Purpose:
Create an application client for the user pool.

Why:
The app client is required for authentication flows and token issuance.

---

## 8. Cognito user pool and app client created

**File:** `8 Cognito user pool + app client creation confirmation.png`

Purpose:
Create a Cognito user pool to manage users for the API.

Why:
Cognito removes the need to build and operate a custom identity system.

Cost trade-off:
Cognito charges per monthly active user, but avoids infrastructure and maintenance costs.

---

## 9. Managed user directory (Cognito user pool)

**File:** `9 managed user directory instead of storing users.png`

Purpose:
Use Cognito as the managed user directory instead of storing users in a database.

Why:
Improves security, compliance and reduces development complexity.

---

## 10. Cognito managed login pages

**File:** `10 cognito app client managed login pages.png`

Purpose:
Enable hosted login pages for authentication.

Why:
Reduces frontend complexity and security risk when handling credentials.

---

## 11. Test user created

**File:** `11 test user (admin) created inside the Cognito.png`

Purpose:
Create a test user for authentication testing.

Why:
Used to validate the authentication and API authorizer flow.

---

## 12. Successful sign-in using Cognito

**File:** `12 successful sign-in using the Amazon Cognito hosted UI.png`

Purpose:
Verify that authentication works and tokens are issued correctly.

Why:
Confirms that the identity layer is correctly configured before securing the API.

---

## 13. CloudWatch log group for WAF logs

**File:** `13 cloudwatch group created to store waf logs for the orders api.png`

Purpose:
Create a log group to store AWS WAF request logs.

Why:
Enables monitoring, security investigation and cost-controlled log retention.

Trade-off:
Logging increases cost depending on volume and retention.

---

## 14. AWS WAF Web ACL created and associated

**File:** `14 AWS WAF Web ACL successfully associated.png`

Purpose:
Create and associate a Web ACL with the API Gateway stage.

Why:
Protects the API against common web attacks and abusive traffic.

Cost trade-off:
WAF has a fixed monthly cost plus request-based charges.

---

## 15. WAF protection attached to API

**File:** `15 waf protection to API.png`

Purpose:
Confirm that the Orders API stage is protected by WAF.

Why:
Ensures that all requests pass through security rules before reaching the backend.

