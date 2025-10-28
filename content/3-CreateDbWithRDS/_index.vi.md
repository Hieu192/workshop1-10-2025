---
title : "Tạo luồng xử lý bất đồng bộ"
date :  "2024-10-27" 
weight : 3 
chapter : false
pre : " <b> 3. </b> "
---

#### Luồng xử lý bất đồng bộ là gì?
Luồng xử lý mà người gửi (producer) không cần chờ người nhận (consumer) xử lý xong ngay lập tức, mà tiếp tục hoạt động, còn công việc được xử lý ở nền (background) thông qua cơ chế trung gian (queue, event bus, callback…).

![](/workshop01-AWS-FCJ-2025/images/3/01.png?featherlight=false&width=50pc)

#### Nội dung

1. [Tạo SQS](3.1-CreateSQS/)
2. [Tạo EventBridge](3.2-CreateEventBridgeRule/)
