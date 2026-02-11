## Request flow (step-by-step)

1. **User opens the client (browser/app)** and clicks **Sign in**.
2. **Cognito Hosted UI** authenticates the user (e.g., `admin`).
3. After login, **Cognito issues JWT tokens** (ID token / Access token).
4. The client sends a request to the API:
   - `POST /orders`
   - includes `Authorization: Bearer <access_token>`
5. **API Gateway** receives the request and runs the **Cognito Authorizer** to validate the JWT.
6. If the token is valid, **API Gateway invokes Lambda** (`CreateOrder`).
7. **Lambda** validates the request body and writes the order into **DynamoDB**.
8. **Lambda returns a response** (example: `201 Created` + `orderId`).
9. **API Gateway forwards the response** back to the client.
10. **WAF** filters/block malicious requests before they reach the API (rate limits, bad IPs, etc.).
11. **CloudWatch Logs** stores logs for Lambda / API Gateway / WAF (for monitoring + troubleshooting).

