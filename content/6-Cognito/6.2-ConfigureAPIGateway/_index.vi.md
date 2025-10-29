---
title : "Cấu hình Authorizer cho API Gateway"
date :  "2024-10-27" 
weight : 2
chapter : false
pre : " <b> 6.2 </b> "
---

#### Mục tiêu
Tạo một "Authorizer" (Người gác cổng) trong API Gateway. Authorizer này sẽ sử dụng Cognito User Pool của bạn để kiểm tra. Bất kỳ ai "gõ cửa" (gọi API) mà không có "Token" (giấy thông hành) hợp lệ sẽ bị chặn lại.

#### Thực hành
1. Truy cập vào API Gateway, từ giao diện click vào **EcommerceAPI1**
    - Từ slider bên trái click vào **Authorizers**
    - Click vào **Create authorizers**
![](/images/6-2/01.png?width=50pc)

2. Từ giao diện Create authorizer 
    - Authorizer name nhập: **`ECommerceAuthorizer`**
    - Authorizer type chọn: **Cognito**
    - Cognito user pool chọn **user pool** mình vừa tạo ở bước trước
    - Token source nhập: **`Authorization`**
![](/images/6-2/02.png?width=50pc)

3. Hoàn thành tạo authorizer, chúng ta gắn cho api quan trọng
![](/images/6-2/03.png?width=50pc)
![](/images/6-2/04.png?width=50pc)
    - Tương tự với api /basket/checkout, /orders

4. Vì chúng ta vừa gắn authorizer cho API nên chúng ta cần phải deploy lại và test api trên Postman.
![](/images/6-2/06.png?width=50pc)

