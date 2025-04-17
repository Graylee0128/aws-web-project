# aws-web-project
WordPress
http://18.162.194.92/wp-admin/

帳號: admin
密碼: xxxxxxxx

## 使用技術

- AWS VPC / Subnet / IGW
- AWS EC2
- AWS RDS 
- AWS Security Group


## 架構概述

### 🕸 VPC & Subnet
- 建立自訂 VPC，區分 Public 與 Private Subnet。
- 公開子網部署 EC2，私有子網部署 RDS。
- 使用單一 AZ: ap-east-1a(香港)。

### ☁ EC2 (Web Server)
- 位於 Public Subnet，安裝 WordPress。
- 使用free-tier的t3.micro虛擬機。
- 開放 HTTP (80)、SSH (22)。
- 指定對應 Security Group。

### 🗄 RDS (mariadb)
- 位於 Private Subnet，不對外開放。
- 僅允許來自 EC2 的安全群組連線。

### 🔐 Security Groups
- **Web SG**：只允許 22/80 端口來自網際網路的連線。
- **DB SG**：只允許 Web SG 的內部流量進入 3306。


## 架構圖


                   +----------------------------+
                   |          Internet          |
                   +-------------+--------------+
                                 |
         +-----------------------------------------------------------------------------+
         | VPC             +------v------+                                             |
         |                 |   IGW (NAT) |                                             |
         |                 +------+------+                                             |
         |                        |                                                    |
         |                        |                                                    |     
         |                        |                                                    |
         |                +--Public Subnet-+         +------------------+              |
         |                |     EC2        | <---->  |SG: Web (HTTP/SSH)|              |
         |                +-------+--------+         +------------------+              |
         |                        |                                                    |
         |                        |                                                    |   
         |                        |                                                    |
         |                +--Public Subnet--+        +-------------------+             | 
         |                |     RDS         | <----> | SG: DB (from EC2) |             |
         |                +-----------------+        +-------------------+             |
         +-----------------------------------------------------------------------------+

## 部署步驟

1. 登入 AWS 控制台。
2. 創建 VPC、子網及路由表。
3. 創建並配置 EC2，並選擇適當的安全群組。
4. 創建 RDS，配置適當的資料庫設置。
5. 配置安全群組規則以確保 EC2 和 RDS 之間的通信。
6. 部署應用程式並配置資料庫連接。


## 未來擴展

### 🔁 多 AZ 部署（提高可用性）
- 將 RDS 設為 Multi-AZ
- 在另一 AZ 部署 EC2，並設 Application Load Balancer (ALB)
- 如果流量上去了，設置 Auto Scaling Group (ASG)

### 加上 Domain Name，整合DNS (Route53)

### 📦 自動化基礎建設：Terraform
使用 Terraform 將上述資源程式碼化（Infrastructure as Code），方便做版本管理
