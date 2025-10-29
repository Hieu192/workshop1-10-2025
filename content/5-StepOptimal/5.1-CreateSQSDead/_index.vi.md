---
title : "Cấu hình hàng đợi thư chết"
date :  "2024-10-27" 
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

#### Vì sao chúng ta cần tạo Dead-Letter Queue (DLQ)?

- Chúng ta cần Dead-Letter Queue (DLQ) để lưu lại các message bị xử lý lỗi nhiều lần, giúp:
    - Phân tích nguyên nhân lỗi mà không mất dữ liệu.
    - Tránh vòng lặp xử lý vô hạn trong Lambda hoặc SQS.
    - Đảm bảo độ tin cậy và khả năng khôi phục của hệ thống.

#### Mục tiêu
- Tạo một hàng đợi "rác" (DLQ). Cấu hình OrderingQueue1 chính của chúng ta để nếu một tin nhắn (đơn hàng) bị xử lý lỗi 3 lần, nó sẽ tự động bị "vứt" sang hàng đợi DLQ này. Điều này giúp các đơn hàng tốt khác vẫn được xử lý bình thường.

#### Thực hành
1. Truy cập vào dịch vụ SQS:
    - Click **Create queue**
    - Name nhập: **`OrderingQueue1_DLQ`**
    - Để mặc đinh hết, kéo xuống tận cùng và click **Create queue**
![AMI](/images/5-1/01.png?width=50pc)

2. Ở giao diện danh sách hàng đợi SQS, click vào **OrderingQueue1** và click **Edit** ở góc phải
    - Cuộn xuống tìm phần **Dead-letter queue** và click **Enable**
    - Chọn DLQ mà bạn vừa tạo ở bước 1
    - Maximum receives nhập 3

{{% notice note %}} 
Maximum receives có nghĩa là Lambda được phép thử xử lý một tin nhắn. Nếu nó báo lỗi (fail), SQS sẽ giữ lại và giao lại sau. Nếu quá trình này "thất bại" tổng cộng 3 lần, SQS sẽ từ bỏ và chuyển tin nhắn đó sang DLQ. {{% /notice %}}

![AMI](/images/5-1/02.png?width=50pc)

3. Test thử với DLQ
    - Chúng ta cần phải sửa một chút code lambda function để mô phỏng lỗi để test DLQ
    - Truy cập vào dịch vụ Lambda, click **OrderingFunction1**, mở tab Code và thêm dòng này như ảnh dưới và click **Deploy**
    ```js
        console.log("!!! ĐANG TEST DLQ - CỐ TÌNH GÂY LỖI !!!");
        throw new Error("Đây là lỗi giả lập để test DLQ");
    ```
    ![AMI](/images/5-1/03.png?width=50pc)
    - Mở Postman, gọi /basket/checkout 4 lần để test
    ![AMI](/images/5-1/04.png?width=50pc)
    - Truy cập lại SQS, và thấy tin nhắn của OrderingQueue có 3 tin nhắn đang chuyển đi, và OrderingQueue1_DLQ đang giữ 1 tin nhắn thành công.
    ![AMI](/images/5-1/05.png?width=50pc)
    - Đợi một lúc, tất cả tin nhắn lỗi đã được chuyển thành công an toàn vào DLQ.

4. Xóa code mô phỏng lỗi trên lambda 