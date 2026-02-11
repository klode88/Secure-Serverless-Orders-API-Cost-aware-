The user opens the app (or browser) and clicks Sign in.

The user signs in using the Cognito hosted login page (for example the user admin).

After successful login, Amazon Cognito gives the app a login token (this proves the user is logged in).

The app sends a request to your API to create an order:

POST /orders

and it sends the login token with the request.

AWS WAF checks the request first (blocks bad or suspicious traffic).

API Gateway receives the request.

API Gateway asks Cognito to check the login token and confirm the user is allowed.

If the user is valid, API Gateway calls the Lambda function
(CreateOrder).

The Lambda function checks the request data and saves the order in DynamoDB.

Lambda returns a success response (for example: order created and order ID).

API Gateway sends the response back to the user.

CloudWatch Logs stores logs for:

API Gateway

Lambda

WAF
(for monitoring and troubleshooting).

