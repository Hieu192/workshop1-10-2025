---
title : "Create IAM Roles"
date :  "2024-10-27" 
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

## Create IAM Policies and Roles for Lambda Functions

We will create custom policies with minimal required permissions, then create roles and attach the policies.

### Step 1: Create Custom Policies

#### Create ProductLambdaPolicy

1. Navigate to **IAM** service in AWS Console.

2. Click **Policies** in the left sidebar, then **Create policy**.

3. Select **JSON** tab and paste the following policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:Scan",
                "dynamodb:Query"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/ProductTable"
        }
    ]
}
```

4. Click **Next**.

5. Policy name: **ProductLambdaPolicy**

6. Click **Create policy**.

#### Create BasketLambdaPolicy

1. Create a new policy with the following JSON:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:GetItem",
                "dynamodb:UpdateItem"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/BasketTable"
        },
        {
            "Effect": "Allow",
            "Action": [
                "events:PutEvents"
            ],
            "Resource": "arn:aws:events:*:*:event-bus/default"
        }
    ]
}
```

2. Policy name: **BasketLambdaPolicy**

#### Create OrderingLambdaPolicy

1. Create a new policy with the following JSON:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:GetItem",
                "dynamodb:Scan",
                "dynamodb:Query"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/OrderingTable"
        },
        {
            "Effect": "Allow",
            "Action": [
                "sqs:ReceiveMessage",
                "sqs:DeleteMessage",
                "sqs:GetQueueAttributes"
            ],
            "Resource": "arn:aws:sqs:*:*:OrderingQueue"
        }
    ]
}
```

2. Policy name: **OrderingLambdaPolicy**

### Step 2: Create IAM Roles

#### Create ProductLambdaRole

1. Click **Roles** in the left sidebar, then **Create role**.

2. Select **AWS service** and choose **Lambda**.

3. Click **Next**.

4. Search and select policy: **ProductLambdaPolicy**

5. Click **Next**.

6. Role name: **ProductLambdaRole**

7. Click **Create role**.

#### Create BasketLambdaRole

1. Repeat the same steps with:
   - Role name: **BasketLambdaRole**
   - Attach policy: **BasketLambdaPolicy**

#### Create OrderingLambdaRole

1. Repeat the same steps with:
   - Role name: **OrderingLambdaRole**
   - Attach policy: **OrderingLambdaPolicy**

### Verification

After completion, you will have:
- 3 custom policies with minimal required permissions
- 3 corresponding IAM roles for each Lambda function