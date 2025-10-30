---
title : "Configure Authorizer for API Gateway"
date :  "2024-10-27" 
weight : 2
chapter : false
pre : " <b> 6.2 </b> "
---

#### Objective
Create an "Authorizer" (Gatekeeper) in API Gateway. This Authorizer will use your Cognito User Pool to check. Anyone who "knocks on the door" (calls API) without a valid "Token" (pass) will be blocked.

#### Practice
1. Access API Gateway, from the interface click on **EcommerceAPI1**
    - From the left slider click on **Authorizers**
    - Click on **Create authorizers**
![](/images/6-2/01.png?width=50pc)

2. From the Create authorizer interface 
    - Authorizer name enter: **`ECommerceAuthorizer`**
    - Authorizer type select: **Cognito**
    - Cognito user pool select the **user pool** you just created in the previous step
    - Token source enter: **`Authorization`**
![](/images/6-2/02.png?width=50pc)

3. Complete creating the authorizer, we attach it to important APIs
![](/images/6-2/03.png?width=50pc)
![](/images/6-2/04.png?width=50pc)
    - Similarly with APIs /basket/checkout, /orders

4. Since we just attached the authorizer to the API, we need to redeploy and test the API on Postman.
![](/images/6-2/06.png?width=50pc)