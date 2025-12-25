# AWS Java Application Deployment 🚀

## Project Overview
This project documents the complete deployment of a **Java-based application** on **Amazon Web Services (AWS)**. The infrastructure was implemented manually using AWS services with a focus on **security, service isolation, scalability, and controlled deployments**.

The application is built using **Apache Maven**, stored as an artifact in **Amazon S3**, and deployed on **EC2 instances running Apache Tomcat 10**.

---

## Tech Stack 🛠️
- Java  
- Apache Maven  
- Apache Tomcat 10  
- Amazon EC2  
- Amazon S3  
- Amazon Route 53  
- Application Load Balancer  
- Auto Scaling Group  
- AWS IAM  

---

## Architecture Overview 🏗️
- Separate EC2 instances for each service  
- DNS-based internal communication using Route 53  
- Artifact-based deployment using Amazon S3  
- IAM role-based access without hardcoded credentials  
- High availability using Load Balancer and Auto Scaling  

---

## Deployment Process 📌

### Step 1: Create Security Groups and Key Pairs 🔐
1. Opened the AWS EC2 Console.
2. Created a security group for the project.
3. Created security group for each service and also configured 
so each service can connect to each other if needed 
3. Added inbound rules:
   - SSH access on port `22` restricted to my IP.
   - Required application and internal service ports.
4. Left outbound rules open to allow service communication.
5. Created an EC2 key pair and downloaded the `.pem` file.

---

### Step 2: Create IAM Role for EC2 with S3 Access 🛡️
1. Opened the AWS IAM Console.
2. Created an IAM role with EC2 as the trusted entity.
3. Attached a custom policy allowing:
   - `s3:AWSS3AllowFullAccess`
4. Restricted the policy to the application artifact S3 bucket.
5. Attached the IAM role to the application EC2 instances.
6. Verified that no static AWS credentials were used.

---

### Step 3: Provision EC2 Instances 💻
1. Launched EC2 instances using provisioning scripts.
2. Created separate instances for:
   - Database
   - RabbitMQ
   - Memcached
   - Application
3. Selected appropriate AMIs and instance types.
4. Attached security groups, IAM role, and key pair during launch.
5. Verified that all instances were running successfully.

---

### Step 4: Configure Instances Using User Data ⚙️
1. Added User Data scripts during instance creation.
2. Installed required system packages and dependencies.
3. Configured services to start automatically.
4. Verified services after instance boot.

---

### Step 5: Configure DNS Using Route 53 🌐
1. Opened the AWS Route 53 Console.
2. Created a hosted zone for the project domain.
3. Created DNS records for:
   - Database service
   - RabbitMQ service
   - Memcached service
   - Application service
4. Used DNS names for service communication instead of IP addresses.
5. Verified connectivity between services.

---

### Step 6: Build Application Artifact Using Maven 📦
1. Navigated to the application source directory.
2. Built the application using Maven:
   ```bash
   mvn clean package
   ```
3. Verified the `.war` file was generated inside the `target/` directory.

---

### Step 7: Upload Artifact to Amazon S3 ☁️
1. Configured AWS CLI on the build machine.
2. Created an S3 bucket for application artifacts.
3. Uploaded the artifact to S3:
   ```bash
   aws s3 cp target/app.war s3://my-java-app-artifacts/app.war
   ```
4. Confirmed the upload was successful.

---

### Step 8: Deploy Artifact on Application EC2 🖥️
1. Connected to the application EC2 instance using SSH.
2. Stopped the Tomcat service:
   ```bash
   sudo systemctl stop tomcat10
   ```
3. Downloaded the artifact from S3:
   ```bash
   aws s3 cp s3://my-java-app-artifacts/app.war /tmp/app.war
   ```
4. Moved the artifact to the Tomcat deployment directory:
   ```bash
   sudo mv /tmp/app.war /var/lib/tomcat10/webapps/
   ```
5. Updated file ownership:
   ```bash
   sudo chown tomcat:tomcat /var/lib/tomcat10/webapps/app.war
   ```

---

### Step 9: Restart and Verify Tomcat 🔄
1. Started the Tomcat service:
   ```bash
   sudo systemctl start tomcat10
   ```
2. Verified service status:
   ```bash
   sudo systemctl status tomcat10
   ```
3. Confirmed application deployment using logs or browser access.

---

### Step 10: Configure Application Load Balancer ⚖️
1. Opened the EC2 Load Balancer Console.
2. Created an Application Load Balancer.
3. Selected multiple Availability Zones.
4. Created a target group for application instances.
5. Registered EC2 instances with the target group.
6. Verified health checks were passing.

---

### Step 11: Configure Auto Scaling Group 📈
1. Created a launch configuration using the application AMI.
2. Created an Auto Scaling Group.
3. Set minimum, maximum, and desired instance capacity.
4. Attached the Auto Scaling Group to the load balancer.
5. Configured scaling policies based on resource utilization.
6. Verified scaling behavior.

---

## Security and Reliability Practices ✅
- IAM role-based access with least privilege  
- No static AWS credentials on servers  
- Restricted inbound access using security groups  
- High availability using ALB and Auto Scaling  

---

## Final Outcome 🎯
A secure, scalable, and production-ready AWS deployment for a Java application with controlled artifact-based deployments.
