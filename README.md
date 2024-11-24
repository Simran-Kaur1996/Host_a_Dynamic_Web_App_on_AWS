# Dynamic Website Hosting on AWS
Showcase of Cloud Infrastructure, Automation, Scalability, and Security
This project demonstrates the deployment and hosting of a dynamic website on Amazon Web Services (AWS), utilizing a robust, scalable, and highly available architecture. It highlights key aspects of cloud infrastructure, automation, security, and performance management.

![Project Diagram](project.png)

# 📦Project Overview
This project showcases how to build, deploy, and host a dynamic website on AWS. The architecture is designed to ensure high availability, fault tolerance, scalability, and security using various AWS services, such as:

- EC2 Instances for web hosting.
- VPC with public and private subnets for network isolation.
- Auto Scaling and Application Load Balancer (ALB) for dynamic scaling and traffic distribution.
- SSL/TLS certificates for secure communication.
- Route 53 for DNS management.

## Architecture Overview
![Project Diagram](3._Host_a_Dynamic_Web_App_on_AWS.gif)

# ⚙️ Architecture Diagram

- **Virtual Private Cloud (VPC)**: with Public and Private Subnets
- **Internet Gateway** to allow internet access
- **Application Load Balancer (ALB)** for traffic distribution
- **EC2 Instances** running the website in private subnets
- **Auto Scaling** Group for elasticity
- **AWS Certificate Manager** for SSL certificates
- **Route 53** for domain management
- **S3 Bucket** for storing assets and application code
- **RDS** for database management

# 🔧 Technologies Used
-**AWS EC2**: For hosting the web application.
-**AWS VPC**: For creating a secure and isolated network.
-**AWS Auto** Scaling: For automatic scaling of EC2 instances.
-**AWS Application Load Balancer**: For distributing traffic across instances.
-**AWS RDS**: For database management.
-**AWS S3**: For storing static files.
-**AWS Route 53**: For DNS management.
-**AWS Certificate Manager**: For securing communications via SSL.

# 🏗️ Deployment Instructions
Follow these steps to set up the project on AWS:
**1. Clone the Repository**
Clone this repository to your local machine:
```bash
git clone https://github.com/yourusername/aws-dynamic-website.git
```
**2. Set Up AWS Resources**
Follow the repository instructions to configure the necessary AWS resources. Ensure that your AWS credentials are properly set up.

**3. Deploy Website Code to S3**
Upload the website files to an S3 bucket:

```bash
aws s3 sync ./website-code/ s3://your-s3-bucket-name/
```
**4. Access the Website**
Once all resources are provisioned, access the website via the domain name you configured in **Route 53**.
Database Configuration
Update the .env file with your RDS credentials:

```bash

S3_URI=s3://your-sql-files/website.sql
RDS_ENDPOINT=your-rds-endpoint.amazonaws.com
RDS_DB_NAME=applicationdb
RDS_DB_USERNAME=your-username
RDS_DB_PASSWORD=your-password
```
Run the database migration using **Flyway**:

```bash
flyway -url=jdbc:mysql://"$RDS_ENDPOINT":3306/"$RDS_DB_NAME" \
  -user="$RDS_DB_USERNAME" \
  -password="$RDS_DB_PASSWORD" \
  -locations=filesystem:sql \
  migrate
```
EC2 Instance Setup for Web Hosting
To install the web server and application on an EC2 instance, use the following script:

```bash
# Update the system and install Apache web server
sudo yum update -y
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP and required extensions
sudo dnf install -y php php-pdo php-openssl php-mbstring

# Install MySQL Server
sudo dnf install -y mysql-community-server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Set environment variables for the S3 bucket and other configurations
S3_BUCKET_NAME=your-s3-bucket-name

# Sync application files from S3 to EC2
sudo aws s3 sync s3://"$S3_BUCKET_NAME" /var/www/html

# Set appropriate permissions
sudo chmod -R 777 /var/www/html
```
Restart Apache to apply changes:

```bash

sudo service httpd restart
```

## How It Works:
- EC2 Instances: Host the dynamic website within private subnets, ensuring high availability and security.
- Application Load Balancer (ALB): Distributes traffic to EC2 instances based on availability and traffic load.
- Auto Scaling Group: Automatically adjusts the number of EC2 instances in response to traffic demand, ensuring consistent performance.
- RDS Database: Manages application data with secure access controlled by IAM roles.
- CloudWatch & SNS: Provides monitoring and notifications for system performance and potential issues.
  
# Key Features
-Scalability: Automatically scales EC2 instances based on traffic.
-Fault Tolerance: Deploys across multiple availability zones for improved availability.
-Security: Uses VPC, security groups, and SSL/TLS certificates to protect data.
-Automation: Deployment and provisioning are automated through scripts and infrastructure-as-code (IaC) practices.

# Future Enhancements
-CI/CD Pipeline: Integrate with Jenkins or GitHub Actions for automated continuous integration and deployment.
-Containerization: Transition the application to AWS ECS or EKS for a microservices-based containerized approach.
-Advanced Monitoring: Implement CloudWatch Alarms for proactive monitoring and automated remediation.

# Troubleshooting & Solutions

**Problem 1: Unable to connect to EC2 instances**
-Solution: Verify that the Security Group allows inbound traffic on the necessary ports (80 for HTTP, 443 for HTTPS) and that the correct key pair is used for SSH access.

**Problem 2: Auto Scaling not working as expected**
-Solution: Check the Auto Scaling Group settings, ensuring the desired capacity, minimum, and maximum instances are properly configured. Also, ensure the ALB is properly registered with the target group.

**Problem 3: Database connection issues**
-Solution: Confirm that the RDS Security Group permits inbound traffic from the EC2 instances' security group and that database credentials are correctly set in the .env file.

**Problem 4: S3 sync fails**
-Solution: Ensure the IAM user has sufficient permissions to access the S3 bucket and that the correct bucket name and path are used in the sync command.

    
