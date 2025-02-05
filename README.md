# Project-2---Three-tier-architecture
AWS- Three-Tier-Architecture-deployment

![image](https://github.com/user-attachments/assets/8ad89f94-cbfc-43e5-93b3-642b6fdf172a)

Purpose of Three-tier Architecture:

Three-tier-Architecture provides increased scalability, availability, and security by spreading the application into multiple Availability Zones and separating it into three layers that serve different functions, independent of each other. If an AZ does down for some reason, the application has the ability to automatically scale resources to another AZ, without affecting the rest of the application tiers. Each tier has its own security group that only allows the inbound/outbound traffic needed to perform specific tasks.

1. Web/presentation tier: It is a user-interface frontend tier.

2. Application tier: It is backend tier. It has all the source code to process the data and run functions.

3. Database tier: It manages the application data and stores the data.

![image](https://github.com/user-attachments/assets/f9085b83-88cd-4a46-b3e6-d92755f4ae69)

This project is done using the AWS Console.

we follow few steps to complete the project.

Step 0: Git clone and setup

Step1: Networking and Security

Step2: Database Deployment

Step3: App tier Instance Deployment

Step4: Internal Load balancing and AutoScaling

Step5: Web tier Instance Deployment

Step6: External Load Balancing and AutoScaling

Lets's begin the project

Step 0: Download code from Github, S3 bucket creation, IAM role creation.


git clone https://github.com/aws-samples/aws-three-tier-web-architecture-workshop.git using git bash.


![image](https://github.com/user-attachments/assets/ad90113f-1dc8-4573-833d-c8b748d15449)


Create S3 bucket to upload files and folders. Select region before creating the bucket and give a unique name to the S3 bucket. 


![image](https://github.com/user-attachments/assets/a19b0c50-0a20-44d2-b04d-09da61c261e6)


Create IAM role for Ec2 Instances. Select Ec2 role trusted identity and create roles. In permission policy select S3 read only access, SSMManagedInstance core and give a role to create.


![image](https://github.com/user-attachments/assets/89c3e76f-10a0-4e99-8f6b-67cdd0d77fa7)

Initial setup for the project is done.

Step1: Networking and Security

Create VPC. Will create an isolated network. select VPC only and give a name to it. I have used 10.0.0.0/16. 

![image](https://github.com/user-attachments/assets/86dac7ad-faf4-43bc-a0d0-5ce71bdd0fe0)

create 6 subnets- 2 for public web subnets, 2 for private subnets, 2 for database private subnets in two availability zones AZ1, AZ2. while creating subnets select custom VPC. 

![image](https://github.com/user-attachments/assets/84372ef2-dd21-4de0-8375-0ae7caf69c10)

Create Internet gateway for Internet connection. Select the custom VPC to the internet gateway.

![image](https://github.com/user-attachments/assets/10ac9337-12d7-4294-8190-f2574fc4fbe2)

Create Nat Gateway. It gives access to the internet for private subnets. App tier private subnets need to access the internet they will go through the NAT Gateway. Will deploy NAT Gateway in each public subnets.
Means we have to create two Nat Gateways using two public subnets in two Availability zones. Allocate Elastic IP to it. Its a secured internet channel. 

![image](https://github.com/user-attachments/assets/5c91b8c8-19f4-427a-8937-a69de27d72ed)

Create Route tables and attach to the subnets. public route table - select custom vpc. Edit routes to give access to the internet. destination should be 0.0.0.0/0 and target should be igw. This will make public instances accessible to the internet. Associate with subnet as well. Edit subnet association. select public subnets AZ1 and AZ2. 

Will create two more route tables. For App layer for two availability zones. This route will help the traffic destined outside the vpc through the NAT gateway. click edit routes, destination 0.0.0.0/0 means outside the world and target will be NAT Gateway. edit subnet association select private subnet AZ1 and AZ2.

![image](https://github.com/user-attachments/assets/1015a2bb-d2ea-4f58-ad67-6ab4e20bfb6d)

Create Security Groups. Security groups will allow public internet traffic to the elastic load balancers and ec2 instances. 

First security group will create for public, internet facing load balancer sg. select custom vpc. Add inbound rule hhtp access on port 80, source is 0.0.0.0/0.

Second security group for web tier, name it as Web tier security group. Add inbound rule http acees on port 80 and source is loadbalancer security group. Add one more inbound rule as http on port 80, custom as my IP.

Third security group for internal load balancer, select cutom vpc, inbound rule would be http on port 80, source is webtier security group.

Fourth security group for private instances, inbound rule would be custom tcp on port 4000, source is internal load balancer sg. add one more inbound rule as myIP port 4000.This is the port our app tier application is running on and allows our internal load balancer to forward traffic on this port to our private instances. another route for port 4000 that allows your IP for testing.

Fifth security group for mysql, inbound rule would be mysql on port 3306, source is private instance sg.

![image](https://github.com/user-attachments/assets/1cde7839-5ed5-419f-8d96-44d73349325a)


Step2: Database Deployment

To provision RDS (Relational Database) we need to create subnet groups. Navigate to RDS in AWS console and click subnet groups. Name it as three-tier-db-subnetgroup. select custom VPC. select availability zones as 1a and 1b. select subnets. subnets should be our database private subnets and click create.

![image](https://github.com/user-attachments/assets/f935715e-e26f-4cf3-9fc1-73d3f0b82537)

Now Database deployment, click on data bases, create database. Select mysql Aurora, template should be dev/test, give master password. click on create Aurora replica in a different AZ. This will help high availability and fault tolerance. select custom vpc, No public access, select database security group, Authentication should be password authentication and create database.

![image](https://github.com/user-attachments/assets/7d378272-ccc8-41c1-b99f-295759810a8f)

Step3: 





















































