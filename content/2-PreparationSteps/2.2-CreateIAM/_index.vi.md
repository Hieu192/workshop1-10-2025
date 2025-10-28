---
title : "Tạo IAM Roles"
date :  "2024-10-27" 
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

## Tạo IAM Roles cho Lambda Functions

Chúng ta cần tạo các IAM role cho các Lambda function để truy cập DynamoDB và các dịch vụ AWS khác.

### Bước 1: Tạo Custom Policies

#### Tạo AllowReadProductTablePolicy1

1. Truy cập AWS Console và điều hướng đến dịch vụ **IAM** trong AWS Console.

![](/workshop01-AWS-FCJ-2025/images/2-2/01.png?featherlight=false&width=50pc)

2. Click **Policies** ở sidebar bên trái, sau đó **Create policy**.

![](/workshop01-AWS-FCJ-2025/images/2-2/02.png?featherlight=false&width=50pc)

3. Chọn tab **JSON** và paste policy sau:
![](/workshop01-AWS-FCJ-2025/images/2-2/03.png?featherlight=false&width=50pc)

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:Scan",
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/ProductTable"
        }
    ]
}
```

4. Click **Next**.
![](/workshop01-AWS-FCJ-2025/images/2-2/04.png?featherlight=false&width=50pc)

5. Tiếp theo chúng ta nhập như bên dưới
   - Policy name nhập: **`AllowReadProductTablePolicy1`**
   - Nhấn **Create policy**
![](/workshop01-AWS-FCJ-2025/images/2-2/05.png?featherlight=false&width=50pc)

6. Click **Create policy**.

#### Tạo AllowReadWriteBasketTablePolicy1

1. Tương tự như trên, tạo policy mới với JSON sau:
   - Policy name nhập: **`AllowReadWriteBasketTablePolicy1`**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:GetItem",
                "dynamodb:UpdateItem"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/BasketTable"
        },
    ]
}
```
![](/workshop01-AWS-FCJ-2025/images/2-2/06.png?featherlight=false&width=50pc)


#### Tạo AllowWriteOrderingTablePolicy1

1. Tạo policy mới với JSON sau:
   - Policy name nhập: **`AllowWriteOrderingTablePolicy1`**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
               "dynamodb:PutItem",
               "dynamodb:Scan",
               "dynamodb:Query"
            ],
            "Resource": [
               "arn:aws:dynamodb:*:*:table/OrderingTable",
            	"arn:aws:dynamodb:*:*:table/Ordering/index/UserOrdersIndex"
            ]
        },
    ]
}
```

![](/workshop01-AWS-FCJ-2025/images/2-2/07.png?featherlight=false&width=50pc)

#### Tạo AllowPutEventsToDefaultBusPolicy1

1. Tạo policy mới với JSON sau:
   - Policy name nhập: **`AllowPutEventsToDefaultBusPolicy1`**

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": "events:PutEvents",
			"Resource": "arn:aws:events:*:*:event-bus/default"
		}
	]
}
```

![](/workshop01-AWS-FCJ-2025/images/2-2/08.png?featherlight=false&width=50pc)


### Bước 2: Tạo IAM Roles 

#### Tạo ProductLambdaRole

1. Click **Roles** ở sidebar bên trái, sau đó **Create role**.
![](/workshop01-AWS-FCJ-2025/images/2-2/14.png?featherlight=false&width=50pc)

2. Chọn **AWS service** và chọn **Lambda**. Click **Next**.
![](/workshop01-AWS-FCJ-2025/images/2-2/09.png?featherlight=false&width=50pc)

4. Tìm và chọn policy: **`AllowReadProductTablePolicy1`**, **`AWSLambdaBasicExecutionRole`** và chọn **Next**
![](/workshop01-AWS-FCJ-2025/images/2-2/10.png?featherlight=false&width=50pc)

5. Role name nhập: **`ProductLambdaRole1`**
![](/workshop01-AWS-FCJ-2025/images/2-2/11.png?featherlight=false&width=50pc)
7. Click **Create role**.

#### Tạo BasketLambdaRole

1. Lặp lại các bước tương tự với:
   - Role name: **`BasketLambdaRole1`**
   - Gắn policy: **`AllowPutEventsToDefaultBusPolicy1`**, **`AllowReadWriteBasketTablePolicy1`**, **`AWSLambdaBasicExecutionRole`**
![](/workshop01-AWS-FCJ-2025/images/2-2/12.png?featherlight=false&width=50pc)

#### Tạo OrderingLambdaRole

1. Lặp lại các bước tương tự với:
   - Role name: **`OrderingLambdaRole1`**
   - Gắn policy: **`AllowWriteOrderingTablePolicy1`**, **`AWSLambdaBasicExecutionRole`**, **`AWSLambdaSQSQueueExecutionRole`**
![](/workshop01-AWS-FCJ-2025/images/2-2/13.png?featherlight=false&width=50pc)

### Xác nhận

Sau khi hoàn thành, bạn sẽ có:
- 4 custom policies với quyền tối thiểu cần thiết
- 3 IAM roles tương ứng cho từng Lambda function