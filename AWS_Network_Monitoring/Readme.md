# Network Monitoring System

## Objective: Create a Network Monitoring System Using AWS VPC Flow Logs and CloudWatch.


## Environment requirements:
- Log in to the AWS Management Console.
- Create a VPC and its sub-resources.
- Create a VPC Log Flow and integrate it with the CloudWatch.
- Create an EC2 Instance.
- Verify the logs in Log Group.
- Configure CloudWatch Metric Filter and CloudWatch Alarm to monitor traffic.
- Test the CloudWatch Alarm.

## Architecture
![Architecture_diagram](https://github.com/codeddamian/AWS-Projects/blob/main/AWS_Network_Monitoring/Architecture_Diagram%20.png)

## Steps to create the architecture: 
                 ## Create a VPC and its sub-resources.
### 1. VPC
Create a VPC with <IP>/24 IPv4 CIDR block.

### 2. Subnets
Create one subnet linked to the VPC with <IP>/26 IPv4 CIDR block within one AZ in the same region as the VPC:
- One public subnet which will be routed to the Internet


### 3. Internet Gateway
Create an Internet Gateway and attach it to the VPC.

### 4 Route table
- Create a route for this Internet Gateway in the main Route Table of the VPC.
- Click on the Route Tables on the left side panel and then choose the main route table for your VPC,i.e.,. 
- Now click on the Routes tab and then click on Edit Routes.
- Add route 
      - Destination: Enter 0.0.0.0/0
	  - Target: Select Internet Gateway and then select the name of the internet gateway you named 

                 ## Create a VPC Flow Logs and integrate it with the CloudWatch

