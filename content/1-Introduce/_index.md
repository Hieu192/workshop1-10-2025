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
- **Basket Microservice** – manages users' shopping carts  
- **Order Microservice** – processes orders and payment transactions  

Each microservice operates independently and is fully implemented using serverless services such as **API Gateway**, **AWS Lambda**, and **Amazon DynamoDB**.

![](/images/1/image.png?featherlight=false&width=50pc)

**System Workflow:**
1. Users access the application and sign in through **Amazon Cognito**, AWS's authentication and user management service.  
2. After authentication, users send requests (API requests) to **AWS API Gateway**.  
3. **API Gateway** routes requests to the appropriate corresponding microservice.  
4. **Product Microservice** receives requests to retrieve product data, Lambda processes the logic and accesses data in the **DynamoDB Table**.  
5. **Basket Microservice** manages shopping cart operations (add, delete, update products). When users click **Checkout**, this service will emit a **Checkout Event** to the **AWS EventBridge Event Bus**.  
6. **AWS EventBridge** receives the event and based on the **EventBridge Rule** routes the event to the **AWS SQS Queue**.  
7. **Order Microservice** listens to the **SQS** queue, retrieves new events and executes Lambda to create orders in the **DynamoDB Table**.  
8. The entire process is monitored by **Amazon CloudWatch**, and access permissions for components are managed through **AWS IAM**.  

**Benefits of the Architecture:**
- **Service Separation:** Each microservice has its own logic and database, making it easy to scale and maintain.  
- **Auto Scaling:** Lambda has the ability to automatically scale according to the number of requests without needing to manage infrastructure.  
- **Event-driven Integration:** EventBridge and SQS help microservices communicate asynchronously, reducing latency and increasing flexibility.  
- **Strong Monitoring and Security:** CloudWatch and IAM help ensure safety and monitor system-wide performance.  
- **Serverless:** The entire system requires no server management, saving costs and optimizing operations.  
