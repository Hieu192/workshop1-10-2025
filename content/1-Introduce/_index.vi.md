---
title : "Giới thiệu"
date :  "2024-10-27" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

#### Giới thiệu kiến trúc Microservice trên AWS

Hệ thống được thiết kế theo mô hình **Serverless Microservices Architecture** trên **AWS Cloud**, gồm ba microservice chính:

- **Product Microservice** – quản lý danh sách và thông tin sản phẩm  
- **Basket Microservice** – quản lý giỏ hàng của người dùng  
- **Order Microservice** – xử lý đơn hàng và giao dịch thanh toán  

Mỗi microservice hoạt động độc lập, được triển khai hoàn toàn bằng các dịch vụ serverless như **API Gateway**, **AWS Lambda**, và **Amazon DynamoDB**.

![](mages/1/image.png?featherlight=false&width=50pc)

**Luồng hoạt động của hệ thống**:
1. Người dùng (User) truy cập ứng dụng và đăng nhập thông qua **Amazon Cognito**, dịch vụ xác thực và quản lý người dùng của AWS.  
2. Sau khi xác thực, người dùng gửi các yêu cầu (API request) đến **AWS API Gateway**.  
3. **API Gateway** định tuyến yêu cầu đến đúng microservice tương ứng.  
4. **Product Microservice** nhận yêu cầu lấy dữ liệu sản phẩm, Lambda xử lý logic và truy xuất dữ liệu trong **DynamoDB Table**.  
5. **Basket Microservice** quản lý các thao tác giỏ hàng (thêm, xoá, cập nhật sản phẩm). Khi người dùng nhấn **Checkout**, dịch vụ này sẽ phát một **Checkout Event** lên **AWS EventBridge Event Bus**.  
6. **AWS EventBridge** nhận sự kiện và dựa vào **EventBridge Rule** để định tuyến sự kiện sang **AWS SQS Queue**.  
7. **Order Microservice** lắng nghe hàng đợi **SQS**, lấy sự kiện mới và thực thi Lambda để tạo đơn hàng trong **DynamoDB Table**.  
8. Toàn bộ quá trình được giám sát bởi **Amazon CloudWatch**, và quyền truy cập của các thành phần được quản lý thông qua **AWS IAM**.  


**Lợi ích của kiến trúc:**
- **Tách biệt dịch vụ:** Mỗi microservice có logic và cơ sở dữ liệu riêng, giúp dễ mở rộng và bảo trì.  
- **Tự động mở rộng (Auto Scaling):** Lambda có khả năng tự động mở rộng theo số lượng yêu cầu mà không cần quản lý hạ tầng.  
- **Tích hợp sự kiện (Event-driven):** EventBridge và SQS giúp các microservice giao tiếp bất đồng bộ, giảm độ trễ và tăng tính linh hoạt.  
- **Giám sát và bảo mật mạnh mẽ:** CloudWatch và IAM giúp đảm bảo an toàn và theo dõi hiệu năng toàn hệ thống.  
- **Không máy chủ (Serverless):** Toàn bộ hệ thống không cần quản lý server, tiết kiệm chi phí và tối ưu vận hành.  
