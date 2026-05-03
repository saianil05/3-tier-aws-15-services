# AWS Three Tier Web Architecture Workshop
## For more projects, check out  
## [https://harishnshetty.github.io/projects.html](https://harishnshetty.github.io/projects.html)
## [Video-Link](https://youtu.be/wPrktKBkBQk?si=uGN33u_EWJmtoY8m)
## Architecture Overview

[![Video Tutorial](https://github.com/harishnshetty/image-data-project/blob/38fd34e5aedeeaa9ba61263d6a9d0c1b3421ac80/3tieraws-project-15services%20(1).jpg)](https://youtu.be/wPrktKBkBQk?si=uGN33u_EWJmtoY8m)


---
```
1. VPC (12 Subnets, 10 Route Tables, 1 IGW, 3 NAT)
2. Security Group (Cross-connection)
3. EC2
4. Auto Scaling Group (ASG with IAM & Launch Template)
5. ALB (2 Target Groups)
6. IAM (for Image Build & Access Control)
7. RDS (MySQL)
8. Secrets Manager
9. S3 (Data + VPC Flow Logs)
10. Cloud Watch
11. SNS
12. Cloud Trail
13. Cloud Front
14. ACM (SSL Certificates)
15. WAF 
16. Route 53
```


---

## Create a Security Group

| SG name      | inbound        | Access         | Description                                  |
|--------------|----------------|---------------|----------------------------------------------|
| Jump Server  | 22             | MY-ip         | access from my laptop                        |
| 1. web-frontend-alb     | 80,         | 0.0.0.0/24    | all access from internet                     |
| 2. Web-srv-sg      | 80,  22    | 1. web-frontend-alb       | only front-alb and jump server access        |
|              |                | jump-server   |                                              |
| 3. app-Internal-alb-sg     |  80,  | 2. Web-srv-sg      | only web-srv                                 |
| 4. app-Srv-sg      | 4000,  22 | 3. app-Internal-alb-sg | only 3. app-Internal-alb-sg and jump server access          |
|              |                | jump-server   |                                              |
| 5. DB-srv       | 3306, 22       | 4. app-Srv-sg       | only app-srv and jump server access          |
|              |      3306          | jump-server   |                                              |

---

## Create a VPC

| #  | Component         | Name                  | CIDR / Details                                |
|----|-------------------|-----------------------|-----------------------------------------------|
| 1  | VPC              | 3-tier-vpc            | 10.75.0.0/16                                  |
| 12 | Subnets          | Public-Subnet-1a      | 10.75.1.0/24                                  |
|    |                  | Public-Subnet-1b      | 10.75.2.0/24                                  |
|    |                  | Public-Subnet-1c      | 10.75.3.0/24                                  |
|    |                  | Web-Private-Subnet-1a | 10.75.4.0/24                                  |
|    |                  | Web-Private-Subnet-1b | 10.75.5.0/24                                  |
|    |                  | Web-Private-Subnet-1c | 10.75.6.0/24                                  |
|    |                  | App-Private-Subnet-1a | 10.75.7.0/24                                  |
|    |                  | App-Private-Subnet-1b | 10.75.8.0/24                                  |
|    |                  | App-Private-Subnet-1c | 10.75.9.0/24                                  |
|    |                  | DB-Private-Subnet-1a  | 10.75.10.0/24                                 |
|    |                  | DB-Private-Subnet-1b  | 10.75.11.0/24                                 |
|    |                  | DB-Private-Subnet-1c  | 10.75.12.0/24                                 |

| #   | Component         | Name/Route Table                | CIDR/Details      | NAT Gateway | Notes                                         |
|-----|-------------------|---------------------------------|-------------------|-------------|-----------------------------------------------|
| 1   | Internet Gateway  | 3-tier-igw                      |                   |             |                                               |
| 3   | Nat gateway       | 3-tier-1a                       |                   |             |                                               |
|     |                   | 3-tier-1b                       |                   |             |                                               |
|     |                   | 3-tier-1c                       |                   |             |                                               |
| 10  | Route-Table       | 3-tier-Public-rt                |                   |             |                                               |
|     |                   | 3-tier-web-Private-rt-1a        | 10.75.4.0/24      | nat-1a      |                                               |
|     |                   | 3-tier-web-Private-rt-1b        | 10.75.5.0/24      | nat-1b      |                                               |
|     |                   | 3-tier-web-Private-rt-1c        | 10.75.6.0/24      | nat-1c      |                                               |
|     |                   | 3-tier-app-Private-rt-1a        | 10.75.7.0/24      | nat-1a      |                                               |
|     |                   | 3-tier-app-Private-rt-1b        | 10.75.8.0/24      | nat-1b      |                                               |
|     |                   | 3-tier-app-Private-rt-1c        | 10.75.9.0/24      | nat-1c      |                                               |
|     |                   | 3-tier-db-Private-rt-1a         | 10.75.10.0/24     | nat-1a      |                                               |
|     |                   | 3-tier-db-Private-rt-1b         | 10.75.11.0/24     | nat-1b      |                                               |
|     |                   | 3-tier-db-Private-rt-1c         | 10.75.12.0/24     | nat-1c      |                                               |
 
---

## Setup the Ec2-instance and create the IAM (WEB Tier)
**REF:** [web-tier](https://github.com/harishnshetty/3-tier-aws-15-services/edit/main/application-code/web-tier)

**Only Setup the Packages:**  
- Nginx  
- nvm  

---

## Setup the Ec2-instance and create the IAM (APP Tier)
**REF:** [app-tier](https://github.com/harishnshetty/3-tier-aws-15-services/tree/main/application-code/app-tier)

**Only Setup the Packages:**  
- install  
    - mysql client  
    - nvm  
    - pm2  

---

## Create images for both web and app Tier
- Web-Tier-IAM-IMAGE  
- APP-Tier-IAM-IMAGE  


## Create a Cloud-Trail
- Name: my-aws-Account-Activity

---

## Create the  S3 Buckets

```bash
git clone https://github.com/harishnshetty/3-tier-aws-15-services.git
```

1. 3-tier-aws-project-8745 (upload your content)  
2. 3tier-vpc-flow-log-8745 (attach this bucket this immediately) with arn Value [ arn:aws:s3:::3tier-vpc-flow-log-8745 ]
---

----
This looks like instructions from a project or assignment. Let’s decode it in simple DevOps terms 👇

🧠 What this means

You have two S3 buckets:

1️⃣

👉 3-tier-aws-project-8745
➡️ This is for:

Uploading your project files
Example:
Terraform code
Architecture diagrams
Scripts

✔️ Think: “my project storage bucket”

2️⃣

👉 3tier-vpc-flow-log-8745
➡️ This is NOT for manual uploads

✔️ This is used for:

VPC Flow Logs storage
📊 What is VPC Flow Log?

In AWS, VPC Flow Logs record:

Incoming traffic (IP, port)
Outgoing traffic
Allowed / denied traffic

👉 Basically: network monitoring logs

🔗 What does "attach this bucket immediately" mean?

This line:

attach this bucket this immediately with ARN:
arn:aws:s3:::3tier-vpc-flow-log-8745

👉 Means:

You must configure your VPC to send flow logs to this S3 bucket

⚙️ What you actually need to do (real task)

You need to:

Step 1: Go to VPC
Open AWS Console
Go to VPC
Step 2: Create Flow Logs
Select your VPC
Click Flow Logs → Create flow log
Step 3: Configure
Destination → Send to S3

S3 ARN →

arn:aws:s3:::3tier-vpc-flow-log-8745
Traffic type → ALL (important for assignments)

🎯 Simple Understanding

| Item                      | Purpose                             |
| ------------------------- | ----------------------------------- |
| `3-tier-aws-project-8745` | Store your project files            |
| `3tier-vpc-flow-log-8745` | Store network logs (auto-generated) |

----

## Create a Mysql in the RDS

### First Create the subnet Group

| Name    | three-subnet-gp-rds   |
|---------|-----------------------|
| VPC     | three-tier-rds-subnetgroup            |
| AZ      | 1a, b, c              |
| Subnets | DB-Private-Subnet-1a  |
|         | DB-Private-Subnet-1b  |
|         | DB-Private-Subnet-1c  |

| Parameter                          | Value                |
|-------------------------------------|----------------------|
| DB instance identifier              | db-3tier             |
| Master username                     | admin                |
| Self managed password               | SuperadminPassword   |
| Instance class (Burstable)          | db.t3.small          |
| Storage                            | 20 GB                |
| Virtual private cloud VPC           | 3-tier-vpc           |
| Security Group (SG)                 | db-srv               |
| Enable Enhanced monitoring          | Unchecked            |
---

### Move on to the Secret manager

Other type of secret:

```
DB_HOST = your rds Endpoint
DB_USER = admin
DB_PWD =  SuperadminPassword
DB_DATABASE = webappdb
```
- Secret name: rds-mysql-secret

---

## Now add the Database into the RDS-MYSQL

**Ref:** https://catalog.us-east-1.prod.workshops.aws/workshops/85cd2bb2-7f79-4e96-bdee-8078e469752a/en-US/part3/configuredatabase


Log into the app ec2 instance (jump server) where we already installed my sql , make sure 3306 port is open in the jump server inbound server vopy the end point of database / rds and run in app ec2 instance as below

```bash
mysql -h CHANGE-TO-YOUR-RDS-ENDPOINT -u admin -p
```

```sql
CREATE DATABASE webappdb;
SHOW DATABASES;
USE webappdb;
CREATE TABLE IF NOT EXISTS transactions(id INT NOT NULL AUTO_INCREMENT, amount DECIMAL(10,2), description VARCHAR(100), PRIMARY KEY(id));
SHOW TABLES;
INSERT INTO transactions (amount,description) VALUES ('400','groceries');
SELECT * FROM transactions;
```

---

## Create SNS

- name: web-tier-sns  
- name: app-tier-sns  
- name: Cloudwatch-sns  
🧠 What is SNS?

Amazon SNS (Simple Notification Service) is used for:

Sending alerts
Triggering notifications (email, SMS, Lambda, etc.)
📌 Your Task

Create 3 SNS topics:

web-tier-sns → for web layer alerts
app-tier-sns → for application layer alerts
Cloudwatch-sns → for monitoring/alarms
⚙️ Method 1: AWS Console (easy way)
Step-by-step:
Go to AWS Console
Search SNS
Click Topics → Create topic
Create Topic 1:
Type: Standard
Name: web-tier-sns
Click Create
Repeat for:
app-tier-sns
Cloudwatch-sns
📧 Add Subscription (Important)

After creating topics:

Open topic
Click Create subscription
Choose:
Protocol: Email
Endpoint: your email

👉 Then confirm via email

⚙️ Method 2: CLI (DevOps way)
aws sns create-topic --name web-tier-sns
aws sns create-topic --name app-tier-sns
aws sns create-topic --name Cloudwatch-sns


🔗 How these are used in your 3-tier project

| Layer      | Usage                                |
| ---------- | ------------------------------------ |
| Web Tier   | Alerts if web server fails           |
| App Tier   | Alerts if API/service fails          |
| CloudWatch | Trigger alarms (CPU, memory, errors) |

---

## Create a role for both web and app tier

- 3-tier-web-role:
    - AmazonS3ReadOnlyAccess
    - AmazonSSMManagedInstanceCore

- 3-tier-app-role:
    - AmazonS3ReadOnlyAccess
    - AmazonSSMManagedInstanceCore
    - SecretsManagerReadWrite  // by adding this policy the app instance fetch the db details via SecretManager

---

## Create web launch template

| Parameter              | Value                |
|------------------------|----------------------|
| Name                   | web-tier-lt          |
| My AMI's               | Web-Tier-IAM-IMAGE   |
| Security Groups        | Web-srv-sg           |
| IAM Instance Profile   | 3-tier-web-role      |

**User Data:**
```bash
#!/bin/bash
# Log everything to /var/log/user-data.log
exec > >(tee /var/log/user-data.log|logger -t user-data -s 2>/dev/console) 2>&1

# Install AWS CLI v2 (if not already)
yum install -y awscli

# Download application code from S3
aws s3 cp s3://<YOUR-S3-BUCKET-NAME>/application-code /home/ec2-user/application-code --recursive

# Go to app directory
cd /home/ec2-user/application-code

# Make script executable and run it
chmod +x web.sh
sudo ./web.sh
```

---

## Create app launch template

| Parameter              | Value                |
|------------------------|----------------------|
| Name                   | app-tier-lt          |
| My AMI's               | app-Tier-IAM-IMAGE   |
| Security Groups        | app-Srv-sg           |
| IAM Instance Profile   | 3-tier-web-role      |

**User Data:**
```bash
#!/bin/bash
# Log everything to /var/log/user-data.log
exec > >(tee /var/log/user-data.log|logger -t user-data -s 2>/dev/console) 2>&1

# Install AWS CLI v2 (if not already)
yum install -y awscli

# Download application code from S3
aws s3 cp s3://<YOUR-S3-BUCKET-NAME>/application-code /home/ec2-user/application-code --recursive

# Go to app directory
cd /home/ec2-user/application-code

# Make script executable and run it
chmod +x app.sh
sudo ./app.sh
```

---

## Create target group 

| Tier      | Name      | Port  | VPC         | Health-check   |
|-----------|-----------|-------|-------------|---------------|
| Web Tier  | Web-tier  | 80    | 3-tier-vpc  |               |
| App Tier  | App-tier  | 4000  | 3-tier-vpc  | /health        |
---

## Create Load balancers

### Application Load Balancers
| Load Balancer | Name     | Type            | VPC        | Availability Zones                                 | Security Groups | Listeners & Routing   |
|---------------|----------|-----------------|------------|---------------------------------------------------|-----------------|----------------------|
| app-alb       | app-alb  | Internal-facing | 3-tier-vpc | App-Private-Subnet-1a, 1b, 1c                     | app-Internal-alb-sg         | 80 app-tier          |
| web-alb       | web-alb  | Internet-facing | 3-tier-vpc | Public-Subnet-1a, 1b, 1                           | web-frontend-alb        | 80 web-tier          |
---

## Immediately update the `nginx.config` of your internal load balancer Address
---
## Create Auto Scaling

| Name            | Launch template | Instance types | VPC        | Subnets (AZs)                       | Load balancer | Desired | min | max | Scaling policy | Notifications    | Tag      |
|-----------------|----------------|---------------|------------|--------------------------------------|---------------|---------|-----|-----|---------------|-----------------|----------|
| web-tier-asg    | web-tier-lb    | t2.micro      | 3-tier-vpc | Web-Private-Subnet-1a, 1b, 1c        | web-tier      | 3       | 3   | 6   | 60            | web-tier-sns    | web-asg  |
| app-tier-asg    | app-tier-lb    | t2.micro      | 3-tier-vpc | app-Private-Subnet-1a, 1b, 1c        | app-tier      | 3       | 3   | 6   | 60            | app-tier-sns    | app-asg  |

---

## Create the Cloudwatch 
- all alarms --> ec2 --> ASG --> Cpuutlization

---

## Create the Cloudfront  
## Create the ACM for the Cloudfront  
## Configure the WAF  
## Configure the Route53  
