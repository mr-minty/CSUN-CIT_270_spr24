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
6. Check *Enable auto-assign public IPv4 address*

### 1.3 Create the security group
1. From the EC2 dashboard, on the lefthand side, select *Security Groups*
2. Click **Create security group**
3. Name: Lab3
4. VPC: Lab3
5. Click **Create security group**
6. From the Security Groups dashboard, select the Lab3 security group
7. Under *Inbound rules* click **Edit inbound rules**
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

## Step 4 Create a Cloud9 environment
1. From the aws dashboard search for "Cloud9", open the Cloud9 dashboard
2. Click on **Create environment**
3. Name: Lab3-env
4. Select *New EC2 instance*
5. Under Network settings, Connection, select *Secure Shell(SSH)*
6. Click **Create**
7. *You will come back to AWS Cloud9 Environments in a later task*

## Step 5 Configuring the Launch Template

### 2.1 Launch an EC2 instance
1. From the EC2 dashboard click **Launch instance**
2. Name: Lab3
3. Application and OS images: Ubuntu
4. On *Network Settings* click **edit**
5. VPC: Lab3
6. Choose existing security group: Lab3
7. Open *Advanced Details*
9. Scroll down to Metadata version: select "V1 and "V2 (token optional)"
10. In User data, either copy and paste the contents of [Userdata](UserdataScript-phase-2.sh) into the User data field or upload it via **Choose file**
11. Click **Launch Instance**
12. Wait for the Lab3 instance to finish initializing
13. From EC2 > Instances, select the Lab3 instance
14. In the *Details* tab click *open address* under Public IPv4 Address
15. You should see a website titled "XYZ University", if you don't click on the address bar and scoll all the way left, then change "https://*EC2-PublicIPv4*" to "http://*EC2PublicIPv4"*
   
*If you still don't see the website ensure that the user data was properly entered during the Lab3 EC2's configuration*

16. From the website, click "List of students"
17. Add a new student for database testing purposes

### 2.2 Creating database credentials Secret

1. Navigate to the Cloud9 dashboard
2. Select My Environment from pop-out menu on the left
3. Under *Cloud9 IDE* for LAB3-env click *Open*
4. In the upper left, select File > New File
5. From your host computer, open [Cloud9Scripts](cloud9-scripts.yml)
 
   

