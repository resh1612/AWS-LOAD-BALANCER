# EX-5 : AWS-LOAD-BALANCER
## Aim

To create a scalable and highly available web application architecture using **Amazon Elastic Load Balancing (ELB)**, **Amazon EC2 Auto Scaling**, and **Amazon CloudWatch**, and to verify automatic load balancing and scaling based on CPU utilisation.

## Algorithm

1. Open the AWS Management Console and access the EC2 service.
2. Verify that `Web Server 1` is running and has passed both status checks.
3. Create an AMI named `WebServerAMI` from `Web Server 1`.
4. Create a target group named `LabGroup` in `Lab VPC`.
5. Create an internet-facing Application Load Balancer named `LabELB`.
6. Configure the Load Balancer to use Public Subnet 1 and Public Subnet 2.
7. Associate the `Web Security Group` with the Load Balancer.
8. Configure the HTTP port `80` listener to forward traffic to `LabGroup`.
9. Create a launch template named `LabConfig`.
10. Use `WebServerAMI` as the AMI and `t2.micro` as the instance type.
11. Configure the launch template to use the `vockey` key pair and `Web Security Group`.
12. Enable detailed CloudWatch monitoring.
13. Create an Auto Scaling group named `Lab Auto Scaling Group`.
14. Configure the Auto Scaling group to use `Private Subnet 1` and `Private Subnet 2`.
15. Attach the Auto Scaling group to the `LabGroup` target group.
16. Set the desired capacity to `2`, minimum capacity to `2`, and maximum capacity to `6`.
17. Create a target tracking scaling policy named `LabScalingPolicy`.
18. Configure the scaling policy to maintain average CPU utilisation at `60%`.
19. Verify that two `Lab Instance` instances are launched automatically.
20. Verify that both instances become healthy in the target group.
21. Access the application using the DNS name of `LabELB`.
22. Open CloudWatch and verify the automatically created scaling alarms.
23. Use the application's **Load Test** option to generate CPU load.
24. Monitor the CloudWatch alarms and wait for the high CPU alarm to enter the **In alarm** state.
25. Verify that Auto Scaling launches additional EC2 instances.
26. Terminate the original `Web Server 1` instance because it is no longer required.

## Program

### AMI Configuration

```text
AMI Name        : WebServerAMI
Description     : Lab AMI for Web Server
Source Instance : Web Server 1
```

The AMI was created from the existing web server so that identical EC2 instances could be launched automatically by Auto Scaling.

### Target Group Configuration

```text
Target Group Name : LabGroup
Target Type       : Instances
VPC               : Lab VPC
Protocol          : HTTP
Port              : 80
```

The target group contains the EC2 instances launched by the Auto Scaling group.

### Load Balancer Configuration

```text
Load Balancer Type : Application Load Balancer
Name               : LabELB
Scheme             : Internet-facing
VPC                : Lab VPC
Subnets            : Public Subnet 1
                     Public Subnet 2
Security Group     : Web Security Group
Listener           : HTTP : 80
Target Group       : LabGroup
```

The Application Load Balancer distributes incoming HTTP requests across healthy EC2 instances.

### Launch Template Configuration

```text
Launch Template Name : LabConfig
AMI                   : WebServerAMI
Instance Type         : t2.micro
Key Pair              : vockey
Security Group        : Web Security Group
CloudWatch Monitoring : Enabled
```

### Auto Scaling Group Configuration

```text
Auto Scaling Group : Lab Auto Scaling Group
Launch Template    : LabConfig
VPC                : Lab VPC

Subnets:
- Private Subnet 1
- Private Subnet 2

Desired Capacity : 2
Minimum Capacity : 2
Maximum Capacity : 6
```

### Scaling Policy

```text
Policy Name       : LabScalingPolicy
Policy Type       : Target Tracking
Metric            : Average CPU Utilization
Target Value      : 60%
```

Auto Scaling automatically increases or decreases the number of EC2 instances to maintain the configured average CPU utilisation while keeping the instance count between 2 and 6.

### CloudWatch

CloudWatch alarms were automatically created by the Auto Scaling policy.

```text
High CPU:
CPU Utilization > 60%
Action:
Launch additional instances

Low CPU:
CPU Utilization < target
Action:
Reduce capacity when appropriate
```

## Output

### 1. AMI Created

The `WebServerAMI` image was successfully created from `Web Server 1`.

<img width="1757" height="927" alt="image" src="https://github.com/user-attachments/assets/d573bb77-bb92-4720-a0ab-1b3f098d30de" />

---

### 2. Target Group Created

The `LabGroup` target group was successfully created for the EC2 instances.

<img width="1263" height="668" alt="image" src="https://github.com/user-attachments/assets/a1f10aac-0a48-4855-8a28-078c67fb6b2a" />


---

### 3. Application Load Balancer

The `LabELB` Application Load Balancer was successfully created using the two public subnets.

<img width="1260" height="662" alt="image" src="https://github.com/user-attachments/assets/55223c8d-a73a-4f5d-b4f9-caa1aef5d128" />


---

### 4. Launch Template

The `LabConfig` launch template was successfully created using `WebServerAMI`, `t2.micro`, and `Web Security Group`.

<img width="1259" height="666" alt="image" src="https://github.com/user-attachments/assets/b0317b03-404b-422f-b6a7-a0b6cac31c66" />
<img width="1260" height="663" alt="image" src="https://github.com/user-attachments/assets/9d3de791-9977-45c5-b5b0-72019813db85" />

---

### 5. Auto Scaling Group

The `Lab Auto Scaling Group` was successfully created with:

```text
Desired : 2
Minimum : 2
Maximum : 6
```

Two EC2 instances were automatically launched.

<img width="1259" height="665" alt="image" src="https://github.com/user-attachments/assets/002ca173-c9a1-44d4-8953-cb5462ae77d9" />
<img width="1261" height="666" alt="image" src="https://github.com/user-attachments/assets/c1df20ef-7428-4d8f-a6e6-1b4c9c13e969" />


---

### 6. Healthy Target Instances

Both automatically launched instances became **healthy** in the `LabGroup` target group.

<img width="1263" height="662" alt="image" src="https://github.com/user-attachments/assets/aadc1861-e21b-414e-a5e6-49c5bb7ba70c" />

---

### 7. Load Balancer Application

The application was successfully accessed through the DNS name of `LabELB`.

The request was received by the Application Load Balancer and forwarded to one of the healthy EC2 instances.


---

### 8. CloudWatch Scaling

The application **Load Test** was used to increase CPU utilisation.

The CloudWatch high CPU alarm eventually entered the **In alarm** state, causing the Auto Scaling group to launch additional instances.


---

### 9. Automatic Scaling

Additional `Lab Instance` EC2 instances were launched automatically after the CPU utilisation exceeded the configured target.

<img width="1262" height="663" alt="image" src="https://github.com/user-attachments/assets/9c68aa8e-ff2c-451f-8fe0-a04f38df3db8" />

## Result

The web application architecture was successfully made **scalable and highly available** using an Application Load Balancer and EC2 Auto Scaling.

The `LabELB` successfully distributed HTTP requests across healthy EC2 instances. The `Lab Auto Scaling Group` maintained a minimum of two instances and automatically launched additional instances when CPU utilisation increased beyond the configured target of **60%**.

CloudWatch successfully monitored CPU utilisation and triggered the scaling process. Therefore, the objectives of **load balancing, automatic scaling, health checking, and CloudWatch monitoring** were successfully achieved.

