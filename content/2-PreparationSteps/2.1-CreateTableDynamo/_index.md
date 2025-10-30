---
title : "Create DynamoDB Tables"
date :  "2024-10-27" 
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

In this step, we will create the necessary DynamoDB tables for the microservice application.

### Create ProductTable

1. Access AWS Console and navigate to the **DynamoDB** service.

![](/images/2-1/01.png?featherlight=false&width=50pc)

2. From the DynamoDB interface, click **Create table**.
![](/images/2-1/02.png?featherlight=false&width=50pc)
3. Create ProductTable
   - Table name: **`ProductTable`**
   - Partition key: **`productId`** (String)

![](/images/2-1/03.png?featherlight=false&width=50pc)

4. Keep other settings as default and click **Create table**.

### Create BasketTable

1. Similar to above, configure the table:
   - Table name: **`BasketTable`**
   - Partition key: **`userId`** (String)

![](/images/2-1/04.png?featherlight=false&width=50pc)

2. Keep other settings as default and click **Create table**.

### Create OrderingTable

1. Click **Create table** again.

2. Configure the table:
   - Table name: **`OrderingTable`**
   - Partition key: **`orderId`** (String)

![](/images/2-1/05.png?featherlight=false&width=50pc)

3. Keep other settings as default and click **Create table**.

4. Wait for all tables to be created successfully and appear as shown below with Status as **Active**.
![](/images/2-1/06.png?featherlight=false&width=50pc)