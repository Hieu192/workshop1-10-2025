---
title : "Create User Pool"
date :  "2024-10-27" 
weight : 1
chapter : false
pre : " <b> 6.1 </b> "
---

#### Practice
1. In AWS Console, enter **Cognito** and click.
![](/images/6-1/01.png?width=50pc)

2. From the interface, click User Pool from the left slider and click **Create user pool**
![](/images/6-1/02.png?width=50pc)

3. From the interface below, enter
    - Application type select **Single-page application (SPA)**
    - Name your application enter **`ECommerceAppClient`**
    - Options for sign-in identifiers click **Email**
    - Self-registration click Enable **self-registration**
    - Return URL enter **`http://localhost:3000`**
    - Click **Create user directory**
![](/images/6-1/03.png?width=50pc)