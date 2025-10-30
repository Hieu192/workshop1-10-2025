---
title : "Clean Up Resources"
date :  "2024-10-27" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

#### Clean Up Resources
We will proceed to delete resources in the following order:

1. Access Cognito service, click on **App client** from the sidebar and click **Delete**
![](/images/7/01.png?width=50pc)

2. Confirm deletion
![](/images/7/02.png?width=50pc)

3. Delete **User pool**
![](/images/7/03.png?width=50pc)

4. Delete **SQS**
    - Access SQS service
    - Delete **OrderingQueue1**
![](/images/7/04.png?width=50pc)

    - Delete **OrderingQueue1_DLQ**
![](/images/7/05.png?width=50pc)

5. Delete **EventBridge Rule**
    - Access EventBridge service
![](/images/7/06.png?width=50pc)

6. Delete **Lambda**
    - Access Lambda service
    - Delete ProductFunction1
![](/images/7/07.png?width=50pc)
    - Delete BasketFunction1, OrderingFunction1
![](/images/7/08.png?width=50pc)

7. Delete **API Gateway**
    - Access API Gateway service
    - Delete API EcommerceAPI1
![](/images/7/09.png?width=50pc)

8. Delete **DynamoDB**
    - Access DynamoDB service
    - Delete Tables BasketTable, ProductTable, OrderingTable
![](/images/7/10.png?width=50pc)

9. Delete **IAM**
    - Access IAM service
    - Delete Roles ProductLambdaRole1, BasketLambdaRole1, OrderingLambdaRole1
![](/images/7/11.png?width=50pc)

    - Delete Policies AllowReadProductTablePolicy1, AllowReadWriteBasketTablePolicy1, AllowWriteOrderingTablePolicy1, AllowPutEventsToDefaultBusPolicy1
![](/images/7/12.png?width=50pc)