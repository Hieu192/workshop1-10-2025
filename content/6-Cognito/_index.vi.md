---
title : "Triển khai Cognito để bảo vệ API"
date :  "2024-10-27" 
weight : 6
chapter : false
pre : " <b> 6. </b> "
---

#### Giới thiệu
Amazon Cognito là dịch vụ của AWS dùng để xác thực (authentication) và quản lý người dùng (user management) cho ứng dụng web hoặc mobile.

Cognito giúp bạn dễ dàng:
    - Tạo và lưu trữ tài khoản người dùng trong User Pool.
    - Hỗ trợ đăng nhập bằng email, số điện thoại, hoặc tài khoản mạng xã hội (Google, Facebook...).
    - Cung cấp Hosted UI để người dùng đăng ký, đăng nhập mà không cần tự xây dựng trang login.
    - Tích hợp dễ dàng với API Gateway và AWS Lambda để bảo vệ API bằng JWT token (ID token, Access token).
Nhờ đó, bạn có thể nhanh chóng thêm tính năng đăng ký, đăng nhập, xác thực bảo mật cho hệ thống microservice mà không cần quản lý server hay cơ sở dữ liệu người dùng thủ công.

#### Nội dung:
1. [Tạo user pool](6.1-CreateUserPool/)
2. [Cấu hình API Gateway](6.2-ConfigureAPIGateway/)
3. [Cập nhật code lambda](6.3-UpdatecodeLambda/)
4. [Tạo User Token](6.4-CreateUserToken/)
