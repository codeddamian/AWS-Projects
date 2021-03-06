# Jumpbox setup on AWS Cloud

## Objective: build and setup a Jumpbox (public EC2 instance) to access a private EC2 instance located in a private subnet with no direct access to Internet.

## Environment requirements:
- 1 VPC
- 2 subnets (1 public and 1 private) in one Availability Zone
- 1 Internet Gateway associated with the public subnet
- 3 Amazon EC2 instances (Jumpbox, NAT, FI)
- 2 EIPs (one for each instance in the public subnet)
- 2 Route tables (one for each subnet)
- 3 Security groups (one for each instance)

## Architecture
![Architecture_diagram](https://github.com/codeddamian/AWS-Projects/blob/main/Jump%20Box%20Project/screenshot%20/Architecture_diagram.png)

## Steps to create the architecture:
### 1. VPC
Create a VPC with <IP>/16 IPv4 CIDR block.

### 2. Subnets
Create two subnets linked to the VPC with <IP>/24 IPv4 CIDR block within one AZ:
- One public subnet which will be routed to the Internet
- One private subnet which will **NOT** have direct access to the Internet

### 3. Internet Gateway
Create an Internet Gateway and attach it to the VPC.

### 4. EC2 instances
Create 3 instances:
- **Jumpbox** *(public)* type Amazon Linux 2 AMI HVM *(ami-00068cd7555f543d5)*.

![Jumpbox_instance](https://github.com/codeddamian/AWS-Projects/blob/main/Jump%20Box%20Project/screenshot%20/Nat.png)

- **NAT instance** *(public)* amzn-ami-vpc-nat *(ami-00a9d4a05375b2763)* community AMI and **disable Change Source/Dest**. Check (Actions > Networking > Change Source/Dest. Check > Yes, Disable)

![NAT_instance](https://github.com/codeddamian/AWS-Projects/blob/main/Jump%20Box%20Project/screenshot%20/Nat.png)

- **Final Instance** *(private)* type Amazon Linux 2 AMI HVM *(ami-00068cd7555f543d5)*.

![Private_instance](https://github.com/codeddamian/AWS-Projects/blob/main/Jump%20Box%20Project/screenshot%20/privateinstance.png)

### 5. Elastic IPs
Create 2 Elastic IPs and assign each of them to the 2 instances in the public subnet: Jumpbox and NAT instance.

### 6. Route Tables
Create 2 Route Tables:
#### Public Route Table associated to the public subnet:

| Destination        | Target                 | Status   | Propagated  |      
| ------------------ | ---------------------- | -------- | ----------- |
| `VPC IPv4 CIDR`    | local                  | active   | No          |
| 0.0.0.0/0          | `IGW id`               | active   | No          |

#### Private Route Table associated to the private subnet:

| Destination        | Target                       | Status   | Propagated  |        
| ------------------ | ---------------------------- | -------- | ----------- |
| `VPC IPv4 CIDR`    | local                        | active   | No          |
| 0.0.0.0/0          | `eni (NAT) instance id`      | active   | No          |

### 7. Security Groups
Create 3 Security Groups and associate each one to its corresponding instance:
#### **Jumpbox Security Group**:
##### Inbound rules:
| Type            | Protocol   | Port Range  | Source             |
| --------------- | ---------- | ----------- | ------------------ |
| SSH             | TCP        | 22          | 0.0.0.0/0          |

##### Outbound rules:
| Type            | Protocol   | Port Range  | Destination        |
| --------------- | ---------- | ----------- | ------------------ |
| All traffic     | All        | All         | 0.0.0.0/0          |

#### **NAT instance Security Group**:
##### Inbound rules:
| Type            | Protocol   | Port Range  | Source               |
| --------------- | ---------- | ----------- | -------------------- |
| All traffic     | All        | 0 - 65535   | `SG Private instance`  |

##### Outbound rules:
| Type            | Protocol   | Port Range  | Destination        |
| --------------- | ---------- | ----------- | ------------------ |
| All traffic     | All        | All         | 0.0.0.0/0          |

#### **Private Instance Security Group**:
##### Inbound rules:
| Type            | Protocol   | Port Range  | Source             |
| --------------- | ---------- | ----------- | ------------------ |
| SSH             | TCP        | 22          | `SG Jumpbox`       |

##### Outbound rules:
| Type            | Protocol   | Port Range  | Destination        |
| --------------- | ---------- | ----------- | ------------------ |
| All traffic     | All        | All         | 0.0.0.0/0          |

## Test connection by pinging google.com on Terminal console:
- Go to the local directory where *<KeyPair.pem>* is located
  ```
  cd Downloads
  ```
- Copy *<KeyPair.pem>* file to terminal home directory and then change directory
  ```
  cp -i <KeyPair.pem> ~
  cd ~
  ```
- Use SSH-agent to allow instances to access *<KeyPair.pem>*
  ```
  chmod 600 <KeyPair.pem>
  eval `ssh-agent -s`
  ssh-add <KeyPair.pem>
  ```
- Connect to Jumpbox instance
  ```
  ssh -A ec2-user@<Jumpbox public IP>
  ```
- Connect to Final instance
  ```
  ssh ec2-user@<FI private IP>
  ```
- Run test via pinging google.com
  ```
  ping google.com
  ```



![Terinal_console_screenshot](https://github.com/codeddamian/AWS-Projects/blob/main/Jump%20Box%20Project/screenshot%20/Terminal.png)