---
title : "Kết nối SQS với Lambda"
date :  "2024-10-27" 
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---


#### Mục tiêu
Cấu hình hàm OrderingFunction1 của chúng ta để nó tự động được "kích hoạt" (trigger) mỗi khi có tin nhắn mới trong OrderingQueue1.

#### Thực hành
1. Truy cập AWS Console, nhập **EventBridge** và truy cập vào dịch vụ **Amazon EventBridge**
![](/workshop01-AWS-FCJ-2025/images/2-3/01.png?width=50pc)

2. Từ danh sách các hàm lambda, click **OrderingFunction1** và chọn **Add trigger**
![](/workshop01-AWS-FCJ-2025/images/3-3/02.png?width=50pc)

3. Trong giao diện add trigger
   - Select a source chọn: **SQS**
   - SQS Queue chọn **OrderingQueue1**
   - Click **Add**
![](/workshop01-AWS-FCJ-2025/images/3-3/03.png?width=50pc)

4. Kiểm tra đã trigger thành công 
![](/workshop01-AWS-FCJ-2025/images/3-3/04.png?width=50pc)
