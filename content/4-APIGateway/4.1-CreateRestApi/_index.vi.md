---
title : "Tạo Rest API"
date :  "2024-10-27" 
weight : 1 
chapter : false
pre : " <b> 4.1 </b> "
---

#### Rest API là gì
REST API (Representational State Transfer API) là giao diện cho phép các hệ thống giao tiếp qua HTTP bằng cách trao đổi dữ liệu (thường ở dạng JSON).Giúp các microservice (như Order, Payment, Inventory...) giao tiếp thống nhất, tách biệt logic xử lý, dễ mở rộng và tích hợp với các cơ chế bất đồng bộ như SQS hoặc EventBridge trong hệ thống order.

#### Mục tiêu
Mục tiêu của phần thực hành là tạo và cấu hình REST API trên Amazon API Gateway, giúp các microservice (như Product, Basket, Order) có thể giao tiếp thông qua HTTP endpoint, phục vụ cho luồng xử lý bất đồng bộ trong Order Microservice.

#### Thực hành

1. Truy cập AWS Console, nhập **API Gateway** và truy cập vào dịch vụ **API Gateway**
![Git Bash](images/4-1/01.png?width=50pc)
2. Chọn **APIs** ở sidebar bên trái, sau đó click **Create API**
![Git Bash](images/4-1/02.png?width=50pc)
3. Lướt xuống tìm **REST API** rồi click **Build**
![Git Bash](images/4-1/03.png?width=50pc)
4. Trong giao diện Create REST API
   - Chọn **New API** 
   - API name nhập: **`EcommerceAPI1`**
   - API endpoint type: **Regional**
   - Click **Create API**
![Git Bash](images/4-1/04.png?width=50pc)
5. Sau khi tạo xong click vào **/** và click tiếp vào **Create resource**
![Git Bash](images/4-1/05.png?width=50pc)
6. Ở phần Resource name nhập **`products`** và click **Create resource**
![Git Bash](images/4-1/06.png?width=50pc)
7. Click vào **/products** vừa tạo, click **Create method**
![Git Bash](images/4-1/07.png?width=50pc)
8. Trong giao diện Method details
   - Method type chọn **GET**
   - Integration type chọn **Lambda function**
   - Click **Lambda proxy integration**
   - Lambda function chọn **ProductFunction1**

![Git Bash](images/4-1/08.png?width=40pc)
9. Hoàn thành tạo một api /products
![Git Bash](images/4-1/09.png?width=50pc)

10. Tạo /basket
![Git Bash](images/4-1/10.png?width=50pc)

![Git Bash](images/4-1/11.png?width=50pc)
11. Tạo /basket/checkout
![Git Bash](images/4-1/12.png?width=50pc)

![Git Bash](images/4-1/13.png?width=50pc)
12. Tạo /orders
![Git Bash](images/4-1/14.png?width=50pc)

![Git Bash](images/4-1/15.png?width=50pc)

13. Kết quả
![Git Bash](images/4-1/16.png?width=50pc)

14. Deploy API
   - Click **Deploy API**
![Git Bash](images/4-1/17.png?width=50pc)

   - Stage chọn **New stage**
   - Stage name nhập: **`prod`**
   - Click **Deploy**
![Git Bash](images/4-1/18.png?width=50pc)
