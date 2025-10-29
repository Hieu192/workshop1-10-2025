---
title : "Introduction"
date : "2024-10-27"
weight : 1
chapter : false
pre : " <b> 1. </b> "
---

#### Introduction to Microservice Architecture on AWS

The system is designed based on the **Serverless Microservices Architecture** on **AWS Cloud**, consisting of three main microservices:

- **Product Microservice** – manages product lists and product information  
- **Basket Microservice** – manages users’ shopping carts  
- **Order Microservice** – processes orders and payment transactions  

Each microservice operates independently and is fully implemented using serverless services such as **API Gateway**, **AWS Lambda**, and **Amazon DynamoDB**.

![](images/1/image.png?featherlight=false&width=50pc)

**System Workflow:**
1. The **User** accesses the application and signs in through **Amazon Cognito**, the authentication and user management service provided by AWS.  
2. After authentication, the user sends **API requests** to **AWS API Gateway**.  
3. **API Gateway** routes each request to the corresponding microservice.  
4. The **Product Microservice** handles requests for product data — the Lambda function processes the logic and retrieves data from the **DynamoDB Table**.  
5. The **Basket Microservice** manages cart operations (add, remove, or update items). When the user clicks **Checkout**, this service publishes a **Checkout Event** to the **AWS EventBridge Event Bus**.  
6. **AWS EventBridge** receives the event and, based on its **EventBridge Rule**, routes it to an **AWS SQS Queue**.  
7. The **Order Microservice** listens to the **SQS Queue**, consumes new events, and triggers its Lambda function to create an order in the **DynamoDB Table**.  
8. The entire process is monitored through **Amazon CloudWatch**, while access and permissions are managed by **AWS IAM**.  

**Benefits of the Architecture:**
- **Service Isolation:** Each microservice has its own logic and database, making it easier to scale and maintain.  
- **Automatic Scaling:** Lambda automatically scales based on incoming request volume, with no infrastructure management required.  
- **Event-Driven Integration:** EventBridge and SQS enable asynchronous communication between services, reducing latency and increasing flexibility.  
- **Strong Monitoring and Security:** CloudWatch and IAM ensure system security and provide detailed monitoring for all components.  
- **Serverless Operation:** The entire system is serverless, reducing operational costs and improving overall efficiency.  
