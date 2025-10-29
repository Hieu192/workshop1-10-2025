---
title : "Tạo Lambda Functions"
date :  "2024-10-27" 
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---


Trong bước này, chúng ta sẽ tạo các Lambda function cho microservices.

### Tạo ProductFunction1

1. Truy cập AWS Console và điều hướng đến dịch vụ **Lambda** trong AWS Console.
![](images/2-3/01.png?featherlight=false&width=50pc)

2. Từ slider bên trái chọn **Functions** và click **Create function**.
![](images/2-3/02.png?featherlight=false&width=50pc)

3. Cấu hình function:
   - Chọn **Author from scratch**
   - Function name: **`ProductFunction1`**
   - Runtime: **Node.js 22.x**
   - Architecture: **arm64**
   - Execution role: **Use an existing role** → **ProductLambdaRole1**
   - Click **Create function**.
![](images/2-3/03.png?featherlight=false&width=50pc)


4. Thay thế code mặc định bằng:

```js
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, ScanCommand, GetCommand } from "@aws-sdk/lib-dynamodb";

// Khởi tạo client DynamoDB
const client = new DynamoDBClient({});
const ddbDocClient = DynamoDBDocumentClient.from(client);

const tableName = "ProductTable"; // Tên bảng của bạn

export const handler = async (event) => {
    console.log("Event received:", JSON.stringify(event));

    let responseBody;
    let statusCode = 200;

    const httpMethod = event.httpMethod;
    const pathParams = event.pathParameters;

    try {
        if (httpMethod === "GET") {
            if (pathParams && pathParams.productId) {
                // Lấy 1 sản phẩm cụ thể: GET /products/{productId}
                const params = {
                    TableName: tableName,
                    Key: {
                        productId: pathParams.productId,
                    },
                };
                const { Item } = await ddbDocClient.send(new GetCommand(params));
                responseBody = Item ? Item : { message: "Product not found." };
                if (!Item) statusCode = 404;

            } else {
                // Lấy tất cả sản phẩm: GET /products
                const params = {
                    TableName: tableName,
                };
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

    // Trả về kết quả cho API Gateway (định dạng Lambda Proxy)
    return {
        statusCode: statusCode,
        headers: {
            "Content-Type": "application/json"
        },
        body: JSON.stringify(responseBody),
    };
};
```
5. Click **Deploy**.
![](images/2-3/04.png?featherlight=false&width=50pc)

### Tạo BasketFunction1

1. Tạo function khác tương tự như trên với:
   - Function name: **`BasketFunction1`**
   - Runtime: **Node.js 22.x**
   - Architecture: **arm64**
   - Execution role: **BasketLambdaRole1**
![](images/2-3/05.png?featherlight=false&width=50pc)

2. Thay thế code bằng:

```javascript
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand, GetCommand } from "@aws-sdk/lib-dynamodb";
import { EventBridgeClient, PutEventsCommand } from "@aws-sdk/client-eventbridge";

// Khởi tạo các client
const ddbClient = new DynamoDBClient({});
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient);
const ebClient = new EventBridgeClient({}); // Client cho EventBridge

const basketTable = "BasketTable";

export const handler = async (event) => {
    console.log("Event received:", JSON.stringify(event));

    const { httpMethod, path, body } = event;
    let responseBody;
    let statusCode = 200;

    try {
        if (httpMethod === "POST" && path.includes("/checkout")) {
            // ---- XỬ LÝ CHECKOUT VÀ GỬI EVENT ----
            console.log("Processing checkout...");

            const checkoutData = JSON.parse(body || '{}');

            // Validate sơ bộ
            if (!checkoutData.userId) {
                return { statusCode: 400, body: JSON.stringify({ message: "userId is required for checkout." }) };
            }

            // 1. Tạo sự kiện để gửi đi
            const eventParams = {
                Entries: [
                    {
                        Source: "com.ecommerce.basket",     // Tên nguồn (phải nhớ để tạo Rule)
                        DetailType: "CheckoutEvent",        // Tên sự kiện (phải nhớ để tạo Rule)
                        Detail: JSON.stringify(checkoutData), // Nội dung sự kiện (toàn bộ giỏ hàng)
                        EventBusName: "default"             // Dùng event bus mặc định
                    },
                ],
            };

            // 2. Gửi sự kiện đến EventBridge
            await ebClient.send(new PutEventsCommand(eventParams));

            console.log("Checkout event sent to EventBridge.");
            responseBody = { message: "Checkout processing started", eventData: checkoutData };

        } else if (httpMethod === "POST" && path.includes("/basket")) {
            // ---- XỬ LÝ CẬP NHẬT GIỎ HÀNG ----
            const basketData = JSON.parse(body || '{}');

            if (!basketData.userId) {
                 return { statusCode: 400, body: JSON.stringify({ message: "userId is required." }) };
            }

            const params = {
                TableName: basketTable,
                Item: basketData // Lưu toàn bộ giỏ hàng
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
        body: JSON.stringify(responseBody),
    };
};
```

3. Click **Deploy**.
![](images/2-3/06.png?featherlight=false&width=50pc)

### Tạo OrderingFunction1

1. Tạo function khác với:
   - Function name: **OrderingFunction1**
   - Runtime: **Node.js 22.x**
   - Architecture: **arm64**
   - Execution role: **OrderingLambdaRole**
![](images/2-3/07.png?featherlight=false&width=50pc)

2. Thay thế code bằng:

```javascript
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand, ScanCommand } from "@aws-sdk/lib-dynamodb";
import { randomUUID } from "crypto";

const ddbClient = new DynamoDBClient({});
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient);

const orderTable = "OrderingTable";

export const handler = async (event) => {
    console.log("Event received:", JSON.stringify(event));

    // ----- XỬ LÝ KHI ĐƯỢC KÍCH HOẠT BỞI SQS -----
    // event.Records là một mảng, SQS có thể gửi nhiều tin nhắn 1 lúc
    if (event.Records) {
        console.log("Processing SQS message...");

        try {
            for (const record of event.Records) {
                const sqsBody = JSON.parse(record.body);

                // Sự kiện từ EventBridge sẽ nằm trong trường 'detail' của SQS body
                const checkoutEventData = sqsBody.detail; 
                console.log("Checkout data from SQS:", checkoutEventData);

                // 1. Tạo một orderId mới
                const orderId = randomUUID();

                // 2. Tạo đối tượng đơn hàng để lưu
                const orderParams = {
                    TableName: orderTable,
                    Item: {
                        orderId: orderId,
                        userId: checkoutEventData.userId,
                        items: checkoutEventData.items,
                        status: "PENDING", // Trạng thái ban đầu
                        createdAt: new Date().toISOString()
                    }
                };

                // 3. Lưu đơn hàng vào DynamoDB
                await ddbDocClient.send(new PutCommand(orderParams));
                console.log(`Order ${orderId} saved successfully.`);
            }
            // Hàm Lambda được SQS kích hoạt không cần trả về gì
            return { message: "SQS messages processed successfully." };

        } catch (err) {
            console.error("Error processing SQS message:", err);
            // Quan trọng: Nếu ném lỗi, SQS sẽ thử gửi lại tin nhắn sau
            throw err; 
        }

    } 
    // ----- XỬ LÝ KHI ĐƯỢC KÍCH HOẠT BỞI API GATEWAY -----
    else if (event.httpMethod) {
        console.log("Processing API Gateway request...");

        // Logic cho API GET /orders (lấy tất cả đơn hàng)
        if (event.httpMethod === "GET") {
             try {
                const params = {
                    TableName: orderTable,
                };
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

    // Trường hợp không xác định
    console.warn("Unknown event type");
    return {
        statusCode: 400,
        body: JSON.stringify({ message: "Unsupported event source" })
    };
};
```

3. Click **Deploy**.
![](images/2-3/08.png?featherlight=false&width=50pc)

Tất cả Lambda functions đã được tạo thành công!

![](images/2-3/09.png?featherlight=false&width=50pc)