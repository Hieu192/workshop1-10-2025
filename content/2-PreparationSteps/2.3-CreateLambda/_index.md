---
title : "Create Lambda Functions"
date :  "2024-10-27" 
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---

## Create Lambda Functions

In this step, we will create Lambda functions for our microservices.

### Create ProductFunction

1. Navigate to **Lambda** service in AWS Console.

2. Click **Create function**.

3. Configure function:
   - Choose **Author from scratch**
   - Function name: **ProductFunction**
   - Runtime: **Node.js 20.x**
   - Architecture: **x86_64**
   - Execution role: **Use an existing role** → **ProductLambdaRole**

4. Click **Create function**.

5. Replace the default code with:

```javascript
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, ScanCommand, GetCommand } from "@aws-sdk/lib-dynamodb";

const client = new DynamoDBClient({});
const ddbDocClient = DynamoDBDocumentClient.from(client);
const tableName = "ProductTable";

export const handler = async (event) => {
    console.log("Event received:", JSON.stringify(event));
    
    let responseBody;
    let statusCode = 200;
    
    const httpMethod = event.httpMethod;
    const pathParams = event.pathParameters;
    
    try {
        if (httpMethod === "GET") {
            if (pathParams && pathParams.productId) {
                // Get specific product
                const params = {
                    TableName: tableName,
                    Key: { productId: pathParams.productId }
                };
                const { Item } = await ddbDocClient.send(new GetCommand(params));
                responseBody = Item ? Item : { message: "Product not found." };
                if (!Item) statusCode = 404;
            } else {
                // Get all products
                const params = { TableName: tableName };
                const { Items } = await ddbDocClient.send(new ScanCommand(params));
                responseBody = Items;
            }
        } else {
            statusCode = 400;
            responseBody = { message: "Unsupported method" };
        }
    } catch (err) {
        console.error(err);
        statusCode = 500;
        responseBody = { message: "Internal server error", error: err.message };
    }
    
    return {
        statusCode: statusCode,
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(responseBody)
    };
};
```

6. Click **Deploy**.

### Create BasketFunction

1. Create another function with:
   - Function name: **BasketFunction**
   - Runtime: **Node.js 20.x**
   - Execution role: **BasketLambdaRole**

2. Replace code with:

```javascript
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
    
    try {
        if (httpMethod === "POST" && path.includes("/checkout")) {
            // Handle checkout
            const checkoutData = JSON.parse(body || '{}');
            
            if (!checkoutData.userId) {
                return { 
                    statusCode: 400, 
                    body: JSON.stringify({ message: "userId is required for checkout." }) 
                };
            }
            
            // Send event to EventBridge
            const eventParams = {
                Entries: [{
                    Source: "com.ecommerce.basket",
                    DetailType: "CheckoutEvent",
                    Detail: JSON.stringify(checkoutData),
                    EventBusName: "default"
                }]
            };
            
            await ebClient.send(new PutEventsCommand(eventParams));
            console.log("Checkout event sent to EventBridge.");
            responseBody = { message: "Checkout processing started", eventData: checkoutData };
            
        } else if (httpMethod === "POST" && path.includes("/basket")) {
            // Update basket
            const basketData = JSON.parse(body || '{}');
            
            if (!basketData.userId) {
                return { 
                    statusCode: 400, 
                    body: JSON.stringify({ message: "userId is required." }) 
                };
            }
            
            const params = {
                TableName: basketTable,
                Item: basketData
            };
            await ddbDocClient.send(new PutCommand(params));
            responseBody = { message: "Basket updated", basket: basketData };
            
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
        body: JSON.stringify(responseBody)
    };
};
```

3. Click **Deploy**.

### Create OrderingFunction

1. Create another function with:
   - Function name: **OrderingFunction**
   - Runtime: **Node.js 20.x**
   - Execution role: **OrderingLambdaRole**

2. Replace code with:

```javascript
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand, ScanCommand } from "@aws-sdk/lib-dynamodb";
import { randomUUID } from "crypto";

const ddbClient = new DynamoDBClient({});
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient);
const orderTable = "OrderingTable";

export const handler = async (event) => {
    console.log("Event received:", JSON.stringify(event));
    
    // Handle SQS messages
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
    // Handle API Gateway requests
    else if (event.httpMethod) {
        console.log("Processing API Gateway request...");
        
        if (event.httpMethod === "GET") {
            try {
                const params = { TableName: orderTable };
                const { Items } = await ddbDocClient.send(new ScanCommand(params));
                return {
                    statusCode: 200,
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify(Items)
                };
            } catch (err) {
                console.error(err);
                return {
                    statusCode: 500,
                    body: JSON.stringify({ message: "Internal server error" })
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

3. Click **Deploy**.

All Lambda functions have been created successfully!