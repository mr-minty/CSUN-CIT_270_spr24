# Lab 3: Cloud Web Application Builder

***See bottom of document for information on grading***

**NOTE: If you are using vockey for this lab, it is a different key file than the "labsuser.pem" we used for Saturday labs you must download the proper "labsuser.pem" from the "AWS details" tab on the "Lab Instructions: Building a Highly Available, Scalable Web Application" module within the "AWS Academy Lab Project - Cloud Web Application Builder" blue colored class on the [AWS Canvas dashboard](https://awsacademy.instructure.com/login/canvas).**

**tldr; You need to make your own ssh key or download the correct pem file from the "AWS details" tab, which is located where you launch this lab.**

## ATTENTION: Please make sure you provide unique names for the components of this lab, do not use "Lab3" and so on. Thank you.

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

| Subnet | CIDR | AZ |
| --- | --- | --- |
| public1a | 10.0.0.0/24 | us-east-1a |
| private1b | 10.0.1.0/24 | us-east-1b |
| public1c | 10.0.2.0/24 | us-east-1c |
| private1d | 10.0.3.0/24 | us-east-1d | 
| public1e | 10.0.4.0/24 | us-east-1e |
| private1f | 10.0.5.0/24 | us-east-1f |

5. From the subnets page, one at a time, select each public subnet then click **actions > Edit subnet settings**
6. Check *Enable auto-assign public IPv4 address*

### 1.3 Create the Internet Gateway
1. From VPC dashboard, on the lefthand side, select "Internet gateways"
2. Click **Create internet gateway**
3. Name: Lab3-IG
4. Click **Create internet gateway**
5. From the Internet gateways screen select Lab3-IG
6. Click Actions > Attach to VPC
7. Under Available VPCs, select Lab3
8. Click **Attach internet gateway**

### 1.4 Editing VPC route table
1. From VPC > Your VPCs, select Lab3
2. Within the Resource Map, open the link within the route table "rtb"
3. Once you're on the route table screen, click on **Edit routes**
4. Click **Add route**
5. Destination: 0.0.0.0/0
6. Target: Internet Gateway: Select Lab3-IG
7. Click **Save changes**

### 1.5 Create the security group
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
| 4 | SSH | My IP | |


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

## Step 3 Create a Cloud9 environment
1. From the aws dashboard search for "Cloud9", open the Cloud9 dashboard
2. Click on **Create environment**
3. Name: Lab3-env
4. Select *New EC2 instance*
5. Under Network settings, Connection, select *Secure Shell(SSH)*
6. Under VPC settings select Lab3
7. Click **Create** *You will come back to AWS Cloud9 Environments in a later task*
8. Navigate to EC2 > Security Groups
9. Select the Lab3 security group
10. Under the Inbound rules tab, click **Edit inbound rules**
11. Add the following rule, NOTE: Set source as the security group of your Cloud9 environment, type "sg-" into the source and it will look something like "aws-Cloud9-Lab3-env-..."

| Rule # | Type | Source Type | Source |
| --- | --- | --- | --- |
| 1 | All traffic | Custom | SEE_NOTE |

## Step 4 Configuring the Launch Template

### 4.1 Launch an EC2 instance
1. From the EC2 dashboard click **Launch instance**
2. Name: Lab3
3. Application and OS images: Ubuntu
4. Key pair (login): Either use vockey or create your own key pair
5. On *Network Settings* click **edit**
6. VPC: Lab3
7. Choose existing security group: Lab3
8. Open *Advanced Details*
9. Scroll down to Metadata version: select "V1 and "V2 (token optional)"
10. In User data, either copy and paste the contents of [Userdata](UserdataScript-phase-2.sh) into the User data field or upload it via **Choose file**
11. Click **Launch Instance**
12. Wait for the Lab3 instance to finish initializing
13. From EC2 > Instances, select the Lab3 instance
14. In the *Details* tab click *open address* under Public IPv4 Address
15. You should see a website titled "XYZ University", if you don't click on the address bar and scroll all the way left, then change "https://*EC2-PublicIPv4*" to "http://*EC2PublicIPv4"*
   
*If you still don't see the website ensure that the user data was properly entered during the Lab3 EC2's configuration*

16. From the website, click "List of students"
17. Add a new student for database testing purposes

### 4.2 Creating database credentials Secret

1. Navigate to the Cloud9 dashboard
2. Select My Environment from pop-out menu on the left
3. Under *Cloud9 IDE* for LAB3-env click *Open*
4. In the upper left, select File > New File
5. From your host computer, open [Cloud9Scripts](cloud9-scripts.yml) with any text editor
6. Modify the following section as shown using your own host (endpoint) and password for the lab3-db RDS configured in step 2.1, the username should be admin unless changed by you, and the **database \<dbname\> name must be all-caps STUDENTS.** **Do not include the angle brackets <>**

   ***Before***
   ```
   aws secretsmanager create-secret \
    --name Mydbsecret \
    --description "Database secret for web app" \
    --secret-string "{\"user\":\"<username>\",\"password\":\"<password>\",\"host\":\"<RDS Endpoint>\",\"db\":\"<dbname>\"}"
   ```
   ***After***
   ```
   aws secretsmanager create-secret \
    --name Mydbsecret \
    --description "Database secret for web app" \
    --secret-string "{\"user\":\"admin\",\"password\":\"speedpass\",\"host\":\"speed.cat0bj30vjn9.us-east-1.rds.amazonaws.com\",\"db\":\"STUDENTS\"}"
   ```
7. Copy the modified section from the previous step
8. Back on the Cloud9 environment, paste this selection into the Untitled1 new file created earlier
9. Then click File > Save As > Filename: script1 > Save
10. Within the terminal below your file, enter```chmod u+x script1```, then run the script with ```./script1```
11. From the aws dashboard search for and navigate to "Secrets Manager"
12. Open Secrets from the pop-up menu on the left
13. You should now see a secret called "Mydbsecret", select it
14. In the *Overview* tab under Secret Value, click **Retrieve secret value**
15. Verify that the information is correct and that dbname is all-caps STUDENTS

### 4.3 Create a second web server with role

1. From the EC2 dashboard click **Launch instance**
2. Name: Lab3-server
3. Application and OS images: ***Ubuntu*** Must be Ubuntu, or the app will not work.
4. Key pair (login): Either use vockey or your own key pair
5. On *Network Settings* click **edit**
6. VPC: Lab3
7. Choose existing security group: Lab3
8. Open *Advanced Details*
9. Under IAM instance profile, select LabInstanceProfile
10. Scroll down to Metadata version: select "V1 and "V2 (token optional)"
11. In User data, either copy and paste the contents of [Userdata](UserdataScript-phase-3.sh) into the User data field or upload it via **Choose file**
12. Click **Launch Instance**

### 4.4 Migrating the database

1. Navigate back to your open Cloud9 enviroment
2. Run the follwing command from the terminal (For the myqldump it must be the private IP of Lab3, the first EC2 created, bc that's where the OG database is)

```mysqldump -h [Lab3-EC2_Private_IP] -u nodeapp -p --databases STUDENTS > data.sql``` **Password is "student12"**

3. Open the data.sql file from the left
4. On line 22 of data.sql change the text **DO NOT DELETE THE ENTIRE LINE, ONLY REPLACE THE KEY WORDS WRITTEN BELOW**

"DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci"

**to**

"DEFAULT CHARACTER SET *utf8* COLLATE *utf8_general_ci*"

5. On line 42 of data.sql change the text **DO NOT DELETE THE ENTIRE LINE, ONLY REPLACE THE KEY WORDS WRITTEN BELOW**

"DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;" **to**

"DEFAULT CHARSET=*utf8* COLLATE=*utf8_general_ci*;"

6. Save the data.sql file, ctrl + s

7. Run the following command from the terminal, enter your RDS password when prompted

```mysql -h RDSEndpoint -u admin -p < data.sql``` **Password is whatever you set for RDS**

8. From the EC2 > Intances screen open a tab with the public Ipv4 of Lab3-server, you should see the same entry/entries on the list students page.

*If you see an upside down smiley face and red error message, ensure the commands ran successfully and that you made the proper edits to data.sql before running the second mysql command*

## Step 5 Creating the Load Balancer and Auto Scaling Group

### 5.1 Create an Application Load Balancer

1. Navigate to EC2 > Load Balancers
2. Click **Create load balancer**
3. Select **Create** for Application load balancer
4. Name: Lab3LB
5. VPC: Lab3
6. Under Mappings, select subnets 1b, 1d, and 1f
7. Under Security groups, deselect default and select Lab3
8. Under Listeners and routing, open *Create target group*
      a. Leave instances selected
      b. Name: Lab3targetgroup
      c. VPC: Lab3
      d. Next
      e. Don't register any targets
      f. Click *Create target group*
9. Back on the Load balancer configuration, refresh target groups and select Lab3targetgroup
10. Scroll down and click *Create load balancer*

### 5.2 Create an autoscaling group

1. Navigate to EC2 > Instances and select the Lab3-server instance
2. With Lab3-server selected, click *Actions* > Image and templates > create template from instance
3. Name: Lab3template
4. Instance type: t2.micro 
5. Key pair (login): Either use vockey or your own key pair
6. Ensure that the LabInstanceprofile, Metadata and userdata settings match the settings from earlier
7. Scroll down and click *Create launch template*
8. Click *View launch templates*
9. Select Lab3template then click > *Actions* > Create Auto Scaling group
10. Name: Lab3AutoScalingGroup
11. Next
12. Under Network,
13. VPC: Lab3
14. Availability Zones and subnets: select 1b, 1d, and 1f
15. Next
16. Select Attach to an existing load balancer
17. Select Choose from your load balancer target groups
18. Select Lab3targetgroup
19. Next
20. Set Desired capacity and Min desired capacity to 1, set Max desired capacity to 3
21. Under Automatic scaling, select Target tracking scaling policy, leave default values
22. Next
23. Next
24. Next
25. Click *Create Auto Scaling group*
26. Wait for your Auto Scaling group to finish provisioning a  healthy instance, then navigate to EC2 > Load Balancers
27. Select Lab3LB then copy and paste the DNS name into a new tab

***If you don't see the website, ensure that the target group instance is healthy in EC2 > target groups > Lab3targetgroup.*** *This may take some time and fix itself, give at least ten minutes for website to show up via the load Balancer DNS name after creating auto scaling group. Additionally, you can verify that the instance itself is not the issue by opening a tab with the public IPv4 address of the active Lab3targetgroup instance.*
   
## Step 6 Test the Auto Scaling policy

1. Navigate to EC2 > Target groups
2. Select the instance ID of your Auto Scaling group instance (Lab3-server)
3. Copy the public IPv4 of Lab3-server ***make sure this is the target group instance and not the original *Lab3-server***
4. Open a terminal/command prompt on your computer
5. Navigate to the directory where you key pair is downloaded
6. Run the command ```ssh -i *key_pair* -l ubuntu *Lab3-server_PublicIpv4*```
7. Enter *yes* to the fingerprint question
8. When you see a green ubuntu username in your prompt, you have successfully logged in
9. Run the command ```sudo apt install stress s-tui -y```
10. After installation run ```s-tui```
11. You should see a graphical display of system resources
12. Move the cursor down to Stress and press Enter
13. You should see green and purple bars slowly increasing in size

*Within ten minutes or so you should see a new instance, also called Lab3-server, start initializing. You can also confirm by looking at EC2 > Target groups > Lab3targetgroup and seeing a second target and/or EC2 > Auto Scaling groups > Lab3AutoSclaingGroup > Activity and you'll see a new instance being launched.*

14. Once you have verified that the auto scaling policy is working, you can press 'q' or scroll down to quit out of s-tui on the SSH terminal.

*The second instance that was created will be terminated once the stress test ends because desired capacity is set to 1*

**This concludes Lab 3: CLoud Web Application Builder**

## Information on Grading

From Professor Rabinovich on Canvas:
Grading:

Task 2 - C

Task 3 - B

Task 4 - A

Note, you need to demonstrate Task 3 and 4 to me. To prepare for demonstration, ensure that in task 3 records on the web page match the records in the database. For task 4 you need to show me that new instances get launched when the load reaches specified value

### Details

**Task 2 - C:** I'm not totally sure what he's expecting at this stage, because there are multiple "task 2"'s in the instructions. I believe you just need a working web server (Lab3-server) with the site and an autoscaling group to be created with the launch template created from the working web server.

**Task 3 - B:** To verify that the information is the same on the website and your RDS

1. Open the XYZ university website and navigate to the list of students pages for reference
2. Open you Cloud9 environment and enter the following commands in the terminal
           
```mysql -u admin -p -h [Lab3db_Endpoint]``` When prompted, enter the password for RDS.

*If successful you should now see mysql> on the left*

```use database STUDENTS; SELECT * FROM students;```

*If successful you should see command line output of the same information that you see on XYZ University's student list*



           
**Task 4 - A:** If you are able to complete all the steps of the walkthrough, you have earned the A, verifying the auto scaling policy is covered in (Step 6).
