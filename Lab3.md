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

## Step 2 Configuring the Launch Template
