---
title : "Tạo user và token để xác thực để sử dụng API"
date :  "2024-10-27" 
weight : 4
chapter : false
pre : " <b> 6.4 </b> "
---

#### Mục tiêu
- Để có thể sử dụng được api thì chúng ta cần đưa một cái chứng thực (token) để API Gateway biết bạn là ai và cho phép sử dụng nó.

#### Thực hành
1. Truy cập dịch vụ Cognito, click vào User Pool của bạn
2. Click vào tab **Users**
![](mages/6-4/01.png?width=50pc)
    - Nhập email của bạn 
    - Nhập mật khẩu
![](mages/6-4/02.png?width=50pc)
    - Kết quả
![](mages/6-4/03.png?width=50pc)
    - Để có thể đăng nhập bằng user, password chúng ta cần bật như ảnh dưới 
![](mages/6-4/04.png?width=50pc)

2. Lấy id-token từ cli để sử dụng api
    - Nhập lệnh sau
    ```bash
    aws cognito-idp initiate-auth --auth-flow USER_PASSWORD_AUTH \
    --client-id [YOUR_CLIENT_ID] \
    --auth-parameters USERNAME=[YOUR_USER],PASSWORD=[YOUR_PASS] \
    --region ap-southeast-1
    ```
![](mages/6-4/05.png?width=50pc)
    - Tiếp tục nhập lệnh để đổi mật khẩu lần đầu
    ```bash
    aws cognito-idp respond-to-auth-challenge --challenge-name NEW_PASSWORD_REQUIRED --client-id 3i5aerjeech7l3kir5j8gkqs3t --challenge-responses USERNAME=hieuthptchuyenbl@gmail.com,NEW_PASSWORD=123456Aa@ --session "[DÁN_CHUỖI_SESSION]" --region ap-southeast-1
    ```
![](mages/6-4/06.png?width=50pc)

3. Copy Id-token đó rồi dán vào Postman như ảnh dưới
    - Cột Key nhập **`Authorization`**
    - Cột Value nhập Id-token của bạn
    - Click send và api đã phản hồi
![](mages/6-4/07.png?width=50pc)

    - Tương tự với api khác
![](mages/6-4/08.png?width=50pc)
    

