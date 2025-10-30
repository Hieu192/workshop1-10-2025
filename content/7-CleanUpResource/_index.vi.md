---
title : "Dọn dẹp tài nguyên"
date :  "2024-10-27" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---


#### Dọn dẹp tài nguyên
Chúng ta sẽ tiến hành xóa các tài nguyên theo thứ tự sau:

1. Truy cập dịch vụ Cognito, click vào **Appclient** từ sliderbar và nhấn **Delete**
![](/images/7/01.png?width=50pc)

2. Xác nhận xóa
![](/images/7/02.png?width=50pc)

3. Xóa **User pool**
![](/images/7/03.png?width=50pc)

4. Xóa **SQS**
    - Truy cập dịch vụ SQS
    - Xóa **OrderingQueue1**
![](/images/7/04.png?width=50pc)

    - Xóa **OrderingQueue1_DLQ**
![](/images/7/05.png?width=50pc)

5. Xóa **EventBridge Rule**
    - Truy cập dịch vụ EventBridge
![](/images/7/06.png?width=50pc)

6. Xóa **Lambda**
    - Truy cập dịch vụ Lambda
    - Xóa ProductFunction1
![](/images/7/07.png?width=50pc)
    - Xóa BasketFunction1, OrderingFunction1
![](/images/7/08.png?width=50pc)

7. Xóa **API Gateway**
    - Truy cập dịch vụ API Gateway
    - Xóa api EcommerceAPI1
![](/images/7/09.png?width=50pc)


8. Xóa **DynamoDB**
    - Truy cập dịch vụ DynamoDB
    - Xóa Table BasketTable, ProductTable, OrderingTable
![](/images/7/10.png?width=50pc)

9. Xóa **IAM**
    - Truy cập dịch vụ IAM
    - Xóa Role ProductLambdaRole1, BasketLambdaRole1, OrderingLambdaRole1
![](/images/7/11.png?width=50pc)

    - Xóa Policies AllowReadProductTablePolicy1, AllowReadWriteBasketTablePolicy1, AllowWriteOrderingTablePolicy1, AllowPutEventsToDefaultBusPolicy1
![](/images/7/12.png?width=50pc)
