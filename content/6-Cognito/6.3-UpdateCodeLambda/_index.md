---
title : "Update Lambda code to use token"
date :  "2024-10-27" 
weight : 3
chapter : false
pre : " <b> 6.3 </b> "
---

#### Objective
- When Cognito allows a request to pass through, it will "attach" user information (like userId, email...) to the request. We will update the Lambda code to read userId from this secure information, instead of reading from the body (which can be forged).

#### Practice

1. Access Lambda service, update BasketFunction1 code and deploy

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

    // --- NEW LOGIC: GET userId FROM TOKEN ---
    // When using Cognito Authorizer, user information will be here
    // "sub" (subject) is the unique userId from Cognito
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
                userId: userId // Override userId with userId from token
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
                    userId: userId // Override userId with userId from token
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

2. Update OrderingFunction1 code and deploy
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

    // ----- PROCESS API GATEWAY -----
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
                // --- NEW LOGIC: Prioritize userId from token ---
                // We don't allow this user to see other users' orders
                console.log(`Querying orders for authenticated user: ${userIdFromToken} using GSI`);

                const params = {
                    TableName: orderTable,
                    IndexName: indexName,
                    KeyConditionExpression: "userId = :uid",
                    ExpressionAttributeValues: {
                        ":uid": userIdFromToken // ONLY use userId from token
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