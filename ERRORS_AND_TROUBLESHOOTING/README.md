# Errors and Troubleshooting

This folder documents real issues I encountered while building the Secure Serverless Orders API
and how I diagnosed and fixed them.

The goal is to show practical problem-solving, not only successful results.

---

## 1. Cognito Hosted UI – "Invalid request"

File:
error-1-cognito-invalid-request.png

Service:
Amazon Cognito

Problem:
The hosted login page returned "Invalid request".

Root cause:
The app client did not have a valid redirect (callback) URL configured

Fix:
Configured the correct callback URL in the Cognito app client settings and regenerated
the login URL using the allowed redirect URI.

Learning:
OAuth configuration must exactly match the allowed callback URLs configured in the app client.

