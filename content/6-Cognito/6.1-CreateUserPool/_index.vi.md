---
title : "Tạo User Pool"
date :  "2024-10-27" 
weight : 1
chapter : false
pre : " <b> 6.1 </b> "
---

#### Thực hành
1. Trong AWS Console, nhập **Cognito** và click.
![](/images/6-1/01.png?width=50pc)

2. Từ giao diện, click User Pool từ slider bên trái và click **Create user pool**
![](/images/6-1/02.png?width=50pc)

3. Từ giao diện dưới nhập
    - Application type chọn **Single-page application (SPA)**
    - Name your application nhập **`ECommerceAppClient`**
    - Options for sign-in identifiers click **Email**
    - Self-registration click Enable **self-registration**
    - Return URL nhập **`http://localhost:3000`**
    - Click **Create user directory**
![](/images/6-1/03.png?width=50pc)