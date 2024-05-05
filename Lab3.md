# Lab 3: Cloud Web Application Builder
## Step 1: Configuring the VPC

### 1.1 Create the VPC
1. Open VPC console and click **Create new VPC**
2. Name it Lab3
3. Set CIDR to 10.0.0.0/16
4. Create VPC
### 1.2 Create the subnets
1. From VPC dashboard click on **Subnets** to the left
2. Click **Create subnet**
3. Select Lab3 for the VPC ID
4. Create the following six subnets (Use add new subnet to make them all in one screen)
   
| Subnet | CIDR |
| --- | --- |
| public1a | 10.0.0.0/24 |
| private1b | 10.0.1.0/24 |
| public1c | 10.0.2.0/24 |
| private1d | 10.3.0.0/24 |
| public1e | 10.0.4.0/24 |
| private1f | 10.0.5.0/24 |

5. From the subnets page, one at a time, select each public subnet then click **actions > Edit subnet settings**
6. Check * *Enable auto-assign public IPv4 address* *

### 1.3 Create the security group
1. From the EC2 dashboard, on the lefthand side, select * *Security Groups* *
2. Click **Create security group**
3. Name: Lab3
4. VPC: Lab3
5. Click **Create security group**
6. From the Security Groups dashboard, select the Lab3 security group
7. Under * *Inbound rules* * click **Edit inbound rules**
8. Add the following rules. NOTE: For the all traffic rule source, type sg- then select the one that says "Lab3"

| Rule # | Type | Source Type | Source |
| --- | --- | --- | --- |
| 1 | HTTP | Anywhere-IPv4 | 0.0.0.0/0 |
| 2 | HTTPS | Anywhere-IPv4 | 0.0.0.0/0 |
| 3 | All Traffic | Custom | SEE_NOTE |


## Step 2 Configuring the database

### 2.1 Creating the RDS database
1. From the RDS dashboard click **Create database**
2. Select Standard create
3. Select MariaDB
4. Select Free tier
5. DB instance identifier: lab3db
6. Leave username as admin
7. Configure an appropriate password for the RDS
8. VPC: Lab3
9. Existing VPC security groups: select Lab3
10. Click **Create database**

### 2.2 Configuring the database secret

1. In the search bar, search for "Secrets Manager", open Secrets Manager
2. From the Secrets Manager dashboard click **Store a new secret**
3. Select Other type of secret
4. Add the following Key/value pairs (Case-sensitive, type exactly as shown, fill in password and RDS_endpoint)

| Key | Value |
| --- | --- |
| user | admin |
| password | your_password |
| host | RDS_endpoint |
| db | STUDENTS |


## Step 3 Configuring the Launch Template

### 2.1 Launch an EC2 instance
1. From the EC2 dashboard click **Launch instance**
2. Name: Lab3
3. Application and OS images: Ubuntu
4. On * *Network Settings* * click **edit**
5. VPC: Lab3
6. Choose existing security group: Lab3
7. Open * *Advanced Details* *
8. IAM instance profile: LabInstanceProfile
9. Scroll down to Metadata version: select "V1 and "V2 (token optional)"
10. In User data, either copy and paste the contents of [Userdata](UserdataScript-phase-3.sh) into the User data field or upload it via **Choose file**
11. Click **Launch Instance**
   

