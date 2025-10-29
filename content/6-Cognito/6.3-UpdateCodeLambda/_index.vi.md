---
title : "Cập nhật code Lambda để sử dụng token"
date :  "2024-10-27" 
weight : 3
chapter : false
pre : " <b> 6.3 </b> "
---

#### Mục tiêu
- Khi Cognito cho phép một yêu cầu đi qua, nó sẽ "đính kèm" thông tin của người dùng (như userId, email...) vào request. Chúng ta sẽ cập nhật code Lambda để đọc userId từ thông tin an toàn này, thay vì đọc từ body (thứ có thể bị giả mạo).


#### Thực hành

1. Truy cập dịch vụ Lambda, cập nhật code BasketFunction1 và deploy

```js
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand } from "@aws-sdk/lib-dynamodb";
import { EventBridgeClient, PutEventsCommand } from "@aws-sdk/client-eventbridge";

const ddbClient = new DynamoDBClient({});
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient);
const ebClient = new EventBridgeClient({});

const basketTable = "BasketTable";

export const handler = async (event) => {
    console.log("Event received:", JSON.stringify(event));

    const { httpMethod, path, body } = event;
    let responseBody;
    let statusCode = 200;

    // --- LOGIC MỚI: LẤY userId TỪ TOKEN ---
    // Khi dùng Cognito Authorizer, thông tin user sẽ nằm trong đây
    // "sub" (subject) chính là userId duy nhất của Cognito
    const userId = event.requestContext?.authorizer?.claims?.sub;

    if (!userId) {
        return { statusCode: 401, body: JSON.stringify({ message: "Unauthorized - No user ID found in token." }) };
    }
    // ----------------------------------------

    try {
        if (httpMethod === "POST" && path.includes("/checkout")) {
            console.log(`Processing checkout for user: ${userId}`);
            const basketData = JSON.parse(body || '{}');

            const eventDetail = {
                ...basketData,
                userId: userId // Ghi đè userId bằng userId từ token
            };

            const eventParams = {
                Entries: [
                    {
                        Source: "com.ecommerce.basket",
                        DetailType: "CheckoutEvent",
                        Detail: JSON.stringify(eventDetail),
                        EventBusName: "default"
                    },
                ],
            };
            await ebClient.send(new PutEventsCommand(eventParams));
            responseBody = { message: "Checkout processing started", eventData: eventDetail };

        } else if (httpMethod === "POST" && path.includes("/basket")) {
            const basketData = JSON.parse(body || '{}');

            const params = {
                TableName: basketTable,
                Item: {
                    ...basketData,
                    userId: userId // Ghi đè userId bằng userId từ token
                }
            };
            await ddbDocClient.send(new PutCommand(params));
            responseBody = { message: "Basket updated", basket: params.Item };

        } else {
            statusCode = 400;
            responseBody = { message: "Unsupported method or path" };
        }

    } catch (err) {
        console.error(err);
        statusCode = 500;
        responseBody = { message: "Internal server error", error: err.message };
    }

    return {
        statusCode: statusCode,
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(responseBody),
    };
};
```

2. Cập nhật code OrderingFunction1 và deploy
```js
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand, ScanCommand, QueryCommand } from "@aws-sdk/lib-dynamodb";
import { randomUUID } from "crypto";

const ddbClient = new DynamoDBClient({});
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient);

const orderTable = "OrderingTable";
const indexName = "UserOrdersIndex";

export const handler = async (event) => {
    console.log("Event received:", JSON.stringify(event));

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

    // ----- XỬ LÝ API GATEWAY -----
    else if (event.httpMethod) {
        console.log("Processing API Gateway request...");

        const userIdFromToken = event.requestContext?.authorizer?.claims?.sub;
        if (!userIdFromToken) {
            return { statusCode: 401, body: JSON.stringify({ message: "Unauthorized - No user ID found in token." }) };
        }
        // ----------------------------------------

        if (event.httpMethod === "GET") {
            let responseBody;
            try {
                // --- LOGIC MỚI: Ưu tiên userId từ token ---
                // Chúng ta không cho phép user này xem đơn hàng của user khác
                console.log(`Querying orders for authenticated user: ${userIdFromToken} using GSI`);

                const params = {
                    TableName: orderTable,
                    IndexName: indexName,
                    KeyConditionExpression: "userId = :uid",
                    ExpressionAttributeValues: {
                        ":uid": userIdFromToken // CHỈ dùng userId từ token
                    }
                };
                const { Items } = await ddbDocClient.send(new QueryCommand(params));
                responseBody = Items;

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

    console.warn("Unknown event type");
    return {
        statusCode: 400,
        body: JSON.stringify({ message: "Unsupported event source" })
    };
};
```
