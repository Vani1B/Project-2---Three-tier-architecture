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















