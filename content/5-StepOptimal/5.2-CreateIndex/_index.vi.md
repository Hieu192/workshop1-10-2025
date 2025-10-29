---
title : "Tạo Global Secondary Index cho OrderingTable"
date :  "2024-10-27" 
weight : 2
chapter : false
pre : " <b> 5.2 </b> "
---
#### Tại sao cần tạo Global Secondary Index(GSI) ?
- Hiện tại, **API GET /orders** của chúng ta đang dùng lệnh Scan. Scan sẽ đọc toàn bộ bảng OrderingTable rồi lọc ra kết quả. Khi bạn chỉ có 10 đơn hàng, nó rất nhanh. Nhưng khi bạn có 10 triệu đơn hàng, nó sẽ cực kỳ chậm và rất tốn kém. 
- Chính vì vậy chúng ta cần tạo một "bản sao" của bảng, nhưng được sắp xếp theo userId để có thể "nhảy" thẳng đến đơn hàng của một người dùng cụ thể mà không cần quét. Cái này gọi là Global Secondary Index (GSI).


#### Tạo Global Secondary Index(GSI)
1. Tại giao diện DynamoDB, chọn **Table** ở sidebar bên trái, sau đó chọn **OrderingTable**
![Target group](/images/5-2/01.png?width=50pc)

2. Tại giao diện OrderingTable
    - Mở tab Indexs và click.
![Target group](/images/5-2/02.png?width=50pc)

3. Tiếp theo click **Create index**
![Target group](/images/5-2/03.png?width=50pc)

4. Từ giao diện
    - Partition key nhập: **`userId`**
    - Index name: **`UserOrdersIndex`**
    - Attribute projections chọn **All**
![Target group](/images/5-2/04.png?width=50pc)

5. Tiếp theo click **Create index** và chờ đến khi nó active
![Target group](/images/5-2/05.png?width=50pc)

6. Tại giao diện Lambda function OrderingFunction1, hãy sửa code để sử dụng index và click deploy
```js
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand, ScanCommand, QueryCommand } from "@aws-sdk/lib-dynamodb";
import { randomUUID } from "crypto";

const ddbClient = new DynamoDBClient({});
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient);

const orderTable = "OrderingTable";
const indexName = "UserOrdersIndex"; // Tên GSI của chúng ta

export const handler = async (event) => {
    console.log("Event received:", JSON.stringify(event));

    // ----- XỬ LÝ KHI ĐƯỢC KÍCH HOẠT BỞI SQS -----
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
    // ----- XỬ LÝ KHI ĐƯỢC KÍCH HOẠT BỞI API GATEWAY -----
    else if (event.httpMethod) {
        console.log("Processing API Gateway request...");

        if (event.httpMethod === "GET") {
            let responseBody;
            try {
                // KIỂM TRA XEM CÓ TRUY VẤN BẰNG userId KHÔNG
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

    // Trường hợp không xác định
    console.warn("Unknown event type");
    return {
        statusCode: 400,
        body: JSON.stringify({ message: "Unsupported event source" })
    };
};
```
![Target group](/images/5-2/06.png?width=50pc)

7. Test API với Postman để kiểm tra tốc độ
    - Test với /orders và xem tốc độ phản hồi
    ![Target group](/images/5-2/07.png?width=50pc)
    - Test với /orders?userId=user-test-02 và xem tốc độ phản hồi
    ![Target group](/images/5-2/08.png?width=50pc)
    - Từ hình ảnh trên a thấy index thì tốc độ phản hồi nhanh hơn, mặc dù không quá lớn đối với dữ liệu ít (vài chục order). Nhưng khi dữ liệu đến hàng triệu, hàng tỷ thì index này có tốc độ nhanh hơn gấp trăm lần thậm chí có thể hàng nghìn lần.
