---
title : "Tạo EC2 instance"
date :  "2024-10-27" 
weight : 3
chapter : false
pre : " <b> 6.3 </b> "
---

#### Tạo EC2 instance
1. Tương tự như tạo instance cho App server, ta tạo một instance cho **web server** với những thay đổi sau:
    - **Instance name** điền **`My Web Server 1`**
    - **Subnet** chọn **Public Subnet 1**
    - **Security group** chọn **WebTier-SG**
    - **Advanced details**, **IAM instance profile** chọn **ec2role**
    - Cuối cùng chọn **Launch instance**
![](/workshop01-AWS-FCJ-2025/images/6-3/01.png?width=50pc)
![](/workshop01-AWS-FCJ-2025/images/6-3/02.png?width=50pc)

2. Hoàn thành tạo EC2 instance cho **web server**
![](/workshop01-AWS-FCJ-2025/images/6-3/03.png?width=50pc)
