---
title : "Deploy Cognito to Protect API"
date :  "2024-10-27" 
weight : 6
chapter : false
pre : " <b> 6. </b> "
---

#### Introduction
Amazon Cognito is an AWS service used for authentication and user management for web or mobile applications.

Cognito helps you easily:
    - Create and store user accounts in User Pool.
    - Support login with email, phone number, or social media accounts (Google, Facebook...).
    - Provide Hosted UI for users to register and login without building your own login page.
    - Easily integrate with API Gateway and AWS Lambda to protect APIs with JWT tokens (ID token, Access token).
Thanks to this, you can quickly add registration, login, and security authentication features to your microservice system without needing to manually manage servers or user databases.

#### Content:
1. [Create user pool](6.1-CreateUserPool/)
2. [Configure API Gateway](6.2-ConfigureAPIGateway/)
3. [Update lambda code](6.3-UpdatecodeLambda/)
4. [Create User Token](6.4-CreateUserToken/)