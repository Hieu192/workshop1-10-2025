---
title : "Chuẩn bị Trung tâm điều phối (Amazon EventBridge)"
date :  "2024-10-27" 
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---

#### EventBridge Rule là gì 
SQS là viết tắt của Amazon Simple Queue Service – một dịch vụ hàng đợi tin nhắn (message queue) được quản lý hoàn toàn bởi AWS. Nó là dịch vụ cho phép bạn gửi, lưu trữ và nhận tin nhắn giữa các thành phần phần mềm một cách bất đồng bộ (asynchronous), mà không cần các thành phần phải chạy cùng lúc hoặc biết vị trí của nhau.

#### Mục tiêu
1. Nhập **SQS** và truy cập vào dịch vụ **Simple Queue Service**
![](mages/3-1/01.png?width=50pc)

2. Ở giao diện **Amazon SQS**, chọn **Create queue**
![](mages/3-1/02.png?width=50pc)

3. Ở giao diện tạo queue:
   - Type chọn **Standard**
   - Name nhập: **`OrderingQueue1`**
   - Kéo xuống và nhấn **Create queue**
![](mages/3-1/03.png?width=50pc)


4. Hoàn thành tạo SQS.
![](mages/3-1/04.png?width=50pc)

#### Thực hành
1. Truy cập AWS Console, nhập **EventBridge** và truy cập vào dịch vụ **Amazon EventBridge**
![](mages/3-2/01.png?width=50pc)

2. Từ giao diện, chọn **Rule** ở slider bên trái và click **Create rule**
![](mages/3-2/02.png?width=50pc)

3. Từ giao diện Create rule
   - Name nhập: **`CheckoutToOrderingRule1`**
   - Click **Next**
![](mages/3-2/03.png?width=50pc)

4. Trong bước Build event pattern
   - Event source chọn **Other**
   - Event pattern chọn **Custom pattern (JSON editor)**
   - Click **Next**

```json
{
  "source": ["com.ecommerce.basket"],
  "detail-type": ["CheckoutEvent"]
}
```
![](mages/3-2/04.png?width=50pc)

5. Trong bước Select target
   - Target type chọn **AWS service**
   - Select a target chọn **SQS queue**
   - Queue chọn **OrderingQueue1**
   - Click **Next**
![](mages/3-2/05.png?width=50pc)

6. Kiểm tra lại và click **Create rule**
![](mages/3-2/06.png?width=50pc)

7. Tạo thành công EventBridge Rule bằng cách check **Status** là **Enabled**
![](mages/3-2/07.png?width=50pc)