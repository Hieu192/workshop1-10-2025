---
title : "Create Global Secondary Index for OrderingTable"
date :  "2024-10-27" 
weight : 2
chapter : false
pre : " <b> 5.2 </b> "
---
#### Why do we need to create Global Secondary Index(GSI)?
- Currently, our **API GET /orders** is using the Scan command. Scan will read the entire OrderingTable and then filter out the results. When you only have 10 orders, it's very fast. But when you have 10 million orders, it will be extremely slow and very expensive.
- That's why we need to create a "copy" of the table, but sorted by userId so we can "jump" directly to a specific user's orders without needing to scan. This is called Global Secondary Index (GSI).

#### Create Global Secondary Index(GSI)
1. In the DynamoDB interface, select **Table** in the left sidebar, then select **OrderingTable**
![Target group](/images/5-2/01.png?width=50pc)

2. In the OrderingTable interface
    - Open the Indexes tab and click.
![Target group](/images/5-2/02.png?width=50pc)

3. Next click **Create index**
![Target group](/images/5-2/03.png?width=50pc)

4. From the interface
    - Partition key enter: **`userId`**
    - Index name: **`UserOrdersIndex`**
    - Attribute projections select **All**
![Target group](/images/5-2/04.png?width=50pc)

5. Next click **Create index** and wait until it's active
![Target group](/images/5-2/05.png?width=50pc)

6. In the Lambda function OrderingFunction1 interface, edit the code to use the index and click deploy
```js
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand, ScanCommand, QueryCommand } from "@aws-sdk/lib-dynamodb";
import { randomUUID } from "crypto";

const ddbClient = new DynamoDBClient({});
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient);

const orderTable = "OrderingTable";
const indexName = "UserOrdersIndex"; // Our GSI name

export const handler = async (event) => {
    console.log("Event received:", JSON.stringify(event));

    // ----- PROCESS WHEN TRIGGERED BY SQS -----
    if (event.Records) {
        console.log("Processing SQS message...");

        try {
            for (const record of event.Records) {
                const sqsBody = JSON.parse(record.body);
                const checkoutEventData = sqsBody.detail; 
                console.log("Checkout data from SQS:", checkoutEventData);

                const orderId = randomUUID();
                const orderParams = {
                    TableName: orderTable,
                    Item: {
                        orderId: orderId,
                        userId: checkoutEventData.userId,
                        items: checkoutEventData.items,
                        status: "PENDING",
                        createdAt: new Date().toISOString()
                    }
                };
                await ddbDocClient.send(new PutCommand(orderParams));
                console.log(`Order ${orderId} saved successfully.`);
            }
            return { message: "SQS messages processed successfully." };

        } catch (err) {
            console.error("Error processing SQS message:", err);
            throw err; 
        }

    } 
    // ----- PROCESS WHEN TRIGGERED BY API GATEWAY -----
    else if (event.httpMethod) {
        console.log("Processing API Gateway request...");

        if (event.httpMethod === "GET") {
            let responseBody;
            try {
                // CHECK IF QUERYING BY userId
                if (event.queryStringParameters && event.queryStringParameters.userId) {

                    const userId = event.queryStringParameters.userId;
                    console.log(`Querying orders for userId: ${userId} using GSI`);

                    const params = {
                        TableName: orderTable,
                        IndexName: indexName,
                        KeyConditionExpression: "userId = :uid", 
                        ExpressionAttributeValues: {
                            ":uid": userId
                        }
                    };
                    const { Items } = await ddbDocClient.send(new QueryCommand(params));
                    responseBody = Items;

                } else {
                    console.log("Scanning all orders (no userId provided)");
                    const params = {
                        TableName: orderTable,
                    };
                    const { Items } = await ddbDocClient.send(new ScanCommand(params));
                    responseBody = Items;
                }

                return {
                    statusCode: 200,
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify(responseBody)
                };
            } catch (err) {
                 console.error(err);
                 return {
                    statusCode: 500,
                    body: JSON.stringify({ message: "Internal server error", error: err.message })
                 };
            }
        }
    }

    // Unknown case
    console.warn("Unknown event type");
    return {
        statusCode: 400,
        body: JSON.stringify({ message: "Unsupported event source" })
    };
};
```
![Target group](/images/5-2/06.png?width=50pc)

7. Test API with Postman to check speed
    - Test with /orders and see response speed
    ![Target group](/images/5-2/07.png?width=50pc)
    - Test with /orders?userId=user-test-02 and see response speed
    ![Target group](/images/5-2/08.png?width=50pc)
    - From the image above, we can see that the index has faster response speed, although not too significant for small data (a few dozen orders). But when data reaches millions or billions, this index can be hundreds or even thousands of times faster.