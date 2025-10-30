---
title : "Configure Dead Letter Queue"
date :  "2024-10-27" 
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

#### Why do we need to create Dead-Letter Queue (DLQ)?

- We need Dead-Letter Queue (DLQ) to store messages that fail processing multiple times, helping to:
    - Analyze error causes without losing data.
    - Avoid infinite processing loops in Lambda or SQS.
    - Ensure system reliability and recovery capability.

#### Objective
- Create a "trash" queue (DLQ). Configure our main OrderingQueue1 so that if a message (order) fails processing 3 times, it will automatically be "thrown" to this DLQ queue. This helps other good orders continue to be processed normally.

#### Practice
1. Access the SQS service:
    - Click **Create queue**
    - Name enter: **`OrderingQueue1_DLQ`**
    - Leave everything as default, scroll down to the bottom and click **Create queue**
![AMI](/images/5-1/01.png?width=50pc)

2. In the SQS queue list interface, click on **OrderingQueue1** and click **Edit** in the top right corner
    - Scroll down to find the **Dead-letter queue** section and click **Enable**
    - Select the DLQ you just created in step 1
    - Maximum receives enter 3

{{% notice note %}} 
Maximum receives means Lambda is allowed to try processing a message. If it reports an error (fail), SQS will keep it and deliver it again later. If this process "fails" a total of 3 times, SQS will give up and move that message to DLQ. {{% /notice %}}

![AMI](/images/5-1/02.png?width=50pc)

3. Test with DLQ
    - We need to modify the lambda function code a bit to simulate errors to test DLQ
    - Access the Lambda service, click **OrderingFunction1**, open the Code tab and add this line as shown below and click **Deploy**
    ```js
        console.log("!!! TESTING DLQ - INTENTIONALLY CAUSING ERROR !!!");
        throw new Error("This is a simulated error to test DLQ");
    ```
    ![AMI](/images/5-1/03.png?width=50pc)
    - Open Postman, call /basket/checkout 4 times to test
    ![AMI](/images/5-1/04.png?width=50pc)
    - Access SQS again, and see that OrderingQueue messages have 3 messages in transit, and OrderingQueue1_DLQ is successfully holding 1 message.
    ![AMI](/images/5-1/05.png?width=50pc)
    - Wait a moment, all error messages have been successfully transferred safely to DLQ.

4. Remove the error simulation code from lambda