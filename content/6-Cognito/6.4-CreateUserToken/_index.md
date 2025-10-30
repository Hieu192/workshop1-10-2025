---
title : "Create user and token for authentication to use API"
date :  "2024-10-27" 
weight : 4
chapter : false
pre : " <b> 6.4 </b> "
---

#### Objective
- To be able to use the API, we need to provide an authentication (token) so that API Gateway knows who you are and allows you to use it.

#### Practice
1. Access Cognito service, click on your User Pool
2. Click on the **Users** tab
![](/images/6-4/01.png?width=50pc)
    - Enter your email 
    - Enter password
![](/images/6-4/02.png?width=50pc)
    - Result
![](/images/6-4/03.png?width=50pc)
    - To be able to login with user, password we need to enable as shown below 
![](/images/6-4/04.png?width=50pc)

2. Get id-token from CLI to use API
    - Enter the following command
    ```bash
    aws cognito-idp initiate-auth --auth-flow USER_PASSWORD_AUTH \
    --client-id [YOUR_CLIENT_ID] \
    --auth-parameters USERNAME=[YOUR_USER],PASSWORD=[YOUR_PASS] \
    --region ap-southeast-1
    ```
![](/images/6-4/05.png?width=50pc)
    - Continue entering command to change password for the first time
    ```bash
    aws cognito-idp respond-to-auth-challenge --challenge-name NEW_PASSWORD_REQUIRED --client-id 3i5aerjeech7l3kir5j8gkqs3t --challenge-responses USERNAME=hieuthptchuyenbl@gmail.com,NEW_PASSWORD=123456Aa@ --session "[PASTE_SESSION_STRING]" --region ap-southeast-1
    ```
![](/images/6-4/06.png?width=50pc)

3. Copy that Id-token and paste it into Postman as shown below
    - Key column enter **`Authorization`**
    - Value column enter your Id-token
    - Click send and the API has responded
![](/images/6-4/07.png?width=50pc)

    - Similarly with other APIs
![](/images/6-4/08.png?width=50pc)