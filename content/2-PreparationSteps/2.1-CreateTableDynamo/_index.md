---
title : "Create DynamoDB Tables"
date :  "2024-10-27" 
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

## Create DynamoDB Tables

In this step, we will create the necessary DynamoDB tables for our microservice application.

### Create ProductTable

1. Access AWS Console and navigate to **DynamoDB** service.

2. Click **Create table**.

3. Configure table settings:
   - Table name: **ProductTable**
   - Partition key: **productId** (String)

4. Keep other settings as default and click **Create table**.

### Create BasketTable

1. Click **Create table** again.

2. Configure table settings:
   - Table name: **BasketTable**
   - Partition key: **userId** (String)

3. Keep other settings as default and click **Create table**.

### Create OrderingTable

1. Click **Create table** again.

2. Configure table settings:
   - Table name: **OrderingTable**
   - Partition key: **orderId** (String)

3. Keep other settings as default and click **Create table**.

4. Wait for all tables to be created successfully.