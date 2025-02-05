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

Step3: App tier Instance Deployment

Navigate to Ec2 Instance. click on Launch Instance. Name it as MyAPPserver1. select Amazon linux image, select instance type as t2.micro. no key pair. select custom vpc, subnet as private subnet AZ1, select private instance sg, In Advance settings, select created IAM role and launch instance. 

![image](https://github.com/user-attachments/assets/380e4eae-d4b8-4b21-be06-843a42d1fdbe)


To connect the instance, select instance and click on session manager. login to ec2 user. install mysql download related packages. 

![image](https://github.com/user-attachments/assets/bf48cebb-c0bb-4ce6-b5fc-5b511c290380)


![image](https://github.com/user-attachments/assets/9637b35a-7c22-4d8a-ac13-1daaf9675944)


Before running the mysql commands will change it to RDS endpoint use the writer instance endpoint. give password to login.
run command to create database. create table.

![image](https://github.com/user-attachments/assets/32b35507-6aab-4153-b955-9dd54a7ed7f0)

![image](https://github.com/user-attachments/assets/6eb8d925-94d0-42ac-83ac-ab96a108257d)


now upload the application code to S3.

![image](https://github.com/user-attachments/assets/4ada4bd3-4eec-4762-9279-191ecc718b7c)

go back session manager and install nvm 16 and install pm2 to make the app keep on running. Will copy the files and folder from s3 bucket to the instance and test the app tier with localhost 4000.

![image](https://github.com/user-attachments/assets/32b07817-30d0-4ce0-bcba-3b8511d1a447)

Step4: Internal loadbalancing and Autoscaling

steps for loadbalacer and Autoscaling:

1. AMI creation for APP tier

2. Create a Launch template

3. Create Autoscaling

4. Deploy internal load balancer

we will create an Amazon Machine Image (AMI) of the app tier instance we just created, and use that to set up autoscaling with a load balancer in order to make this tier highly available.

Select the App tier instance and under the Actions click on Image and templates to create image.

![image](https://github.com/user-attachments/assets/00b11452-3587-42df-941b-6ce0523a9dd6)

create a target group. the target group is to use with loadbalancer to balance the traffic across the private app tier instance. Set the protocol as http on port 4000. As Node.js runs on port 4000.select custom vpc. Health check path to be /health. click create.

To create the Internal load balancer, Go to Ec2 dashboard select load balancer. Will create application load balancer, name it as internal load balancer, select VPC and select private subnet-AZ1 and private subnet AZ2, select internal lb sg, http on port 80, target group is app tier target group. click on create load balancer.

![image](https://github.com/user-attachments/assets/94e4ef5a-5ca8-445d-bf25-f0644ccb168b)

Before creating ASG to need to create Launch template, using AMI. Name it as App tier launch template. use custom AMI, select private instance sg, In Advance details use IAM role. 

![image](https://github.com/user-attachments/assets/f7cdfd45-8949-4cb0-8676-92be4d86b6c1)

Go to Auto scaling group, create autoscaling, use custom vpc, and attach to the internal load balancer by selecting target group, configure scaling policies desired -2, min-2 and max-2

![image](https://github.com/user-attachments/assets/04986e0b-cd3a-4e74-9a3b-406ca287a8fa)

Step 5: Web-tier Instance Deployment

We will deploy an EC2 instance for the web tier and make all necessary software configurations for the NGINX web server and React.js website.

Before we create and configure the web instances, open up the application-code/nginx.conf file from the repo we downloaded. Scroll down to line 58 and replace [INTERNAL-LOADBALANCER-DNS] with your internal load balancer’s DNS entry.


![image](https://github.com/user-attachments/assets/432bd714-14d6-434b-8eee-9ca065c1e9f4)


Step6: External Load balancing and autoscaling

Will repeat the same steps as above for external load balancer and autoscaling by creating AMI, Target group, Launch template, load balancer and ASG. It is just we use public subnets and internet facing security group.

![image](https://github.com/user-attachments/assets/50578a04-1e2a-4d21-bc0c-56b2d9d3865d)


Use the External load balancer DNS endpoint for the results.


![image](https://github.com/user-attachments/assets/cf8f0307-f6eb-474a-9723-bf29c23fbb24)










































































