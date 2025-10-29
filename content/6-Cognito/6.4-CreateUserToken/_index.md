---
title : "Create EC2 instance"
date : "2024-10-27"
weight : 4
chapter : false
pre : " <b> 6.4 </b> "
---

#### Create EC2 instance
1. Similar to creating an instance for the App server, create an instance for the **web server** with the following changes:
    - **Instance name** fill in **`My Web Server 1`**
    - **Subnet** select **Public Subnet 1**
    - **Security group** select **WebTier-SG**
    - **Advanced details**, **IAM instance profile** select **ec2role**
    - Finally, select **Launch instance**
![](mages/6-3/01.png?width=50pc)
![](mages/6-3/02.png?width=50pc)

2. Complete creating an EC2 instance for the **web server**
![](mages/6-3/03.png?width=50pc)
