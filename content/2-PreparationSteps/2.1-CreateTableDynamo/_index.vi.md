---
title : "Tạo DynamoDB Tables"
date :  "2024-10-27" 
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

## Tạo DynamoDB Tables

Trong bước này, chúng ta sẽ tạo các bảng DynamoDB cần thiết cho ứng dụng microservice.

### Tạo ProductTable

1. Truy cập AWS Console và điều hướng đến dịch vụ **DynamoDB**.

![](/workshop01-AWS-FCJ-2025/images/2-1/01.png?featherlight=false&width=50pc)

2. Từ giao diện DynamoDB, click **Create table**.
![](/workshop01-AWS-FCJ-2025/images/2-1/02.png?featherlight=false&width=50pc)
3. Tạo bảng ProductTable
   - Table name nhập: **`ProductTable`**
   - Partition key nhập: **`productId`** (String)

![](/workshop01-AWS-FCJ-2025/images/2-1/03.png?featherlight=false&width=50pc)

4. Giữ các cài đặt khác mặc định và click **Create table**.

### Tạo BasketTable

1. Tương tự như trên, cấu hình bảng:
   - Table name nhập: **`BasketTable`**
   - Partition key: **`userId`** (String)

![](/workshop01-AWS-FCJ-2025/images/2-1/04.png?featherlight=false&width=50pc)

2. Giữ các cài đặt khác mặc định và click **Create table**.

### Tạo OrderingTable

1. Click **Create table** lần nữa.

2. Cấu hình bảng:
   - Table name: **`OrderingTable`**
   - Partition key: **`orderId`** (String)

![](/workshop01-AWS-FCJ-2025/images/2-1/05.png?featherlight=false&width=50pc)

3. Giữ các cài đặt khác mặc định và click **Create table**.

4. Đợi tất cả các bảng được tạo thành công và có dạng như hình dưới với Status là **Active**.
![](/workshop01-AWS-FCJ-2025/images/2-1/06.png?featherlight=false&width=50pc)