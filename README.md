# Dynamic Website Hosting on AWS

This project demonstrates the deployment and hosting of a dynamic website on AWS, utilizing various services and components to ensure high availability, scalability, security, and fault tolerance.

## Architecture Overview
![Project Diagram](3._Host_a_Dynamic_Web_App_on_AWS.gif)

🌐 Dynamic Website Hosting on AWS
This project showcases the deployment of a dynamic website on AWS, using a highly available, scalable, and secure architecture. It leverages key AWS services such as EC2, VPC, Auto Scaling, Application Load Balancer, RDS, and more to ensure the website's smooth operation and resilience.


📦 Project Overview
The dynamic website is hosted using Amazon Web Services (AWS), ensuring scalability, fault tolerance, and high availability. This infrastructure includes a multi-tier architecture with:

EC2 instances for web hosting
A VPC with public and private subnets
Auto Scaling and Load Balancing for dynamic scaling
Secure connections using SSL/TLS certificates
Managed DNS via Route 53
The website is fully automated for deployment and includes detailed deployment scripts and reference diagrams.

⚙️ Architecture Diagram

Virtual Private Cloud (VPC) with Public and Private Subnets
Internet Gateway to allow internet access
Application Load Balancer (ALB) for traffic distribution
EC2 Instances running the website in private subnets
Auto Scaling Group for elasticity
AWS Certificate Manager for SSL certificates
Route 53 for domain management
S3 Bucket for storing assets and application code
RDS for database management
🔧 Technologies Used
AWS EC2: For hosting the web application.
AWS VPC: For creating a secure and isolated network.
AWS Auto Scaling: For automatic scaling of EC2 instances.
AWS Application Load Balancer: For distributing traffic across instances.
AWS RDS: For database management.
AWS S3: For storing static files.
AWS Route 53: For DNS management.
AWS Certificate Manager: For securing communications via SSL.
🏗️ Deployment Instructions
Follow these steps to set up the project on AWS:

1. Clone the Repository
Clone this repository to your local machine:

bash
Copy code
git clone https://github.com/yourusername/aws-dynamic-website.git
2. Set Up AWS Resources
Follow the instructions in the repository to configure the necessary AWS resources. Make sure your AWS credentials are set up properly.

3. Deploy Website Code
Deploy your website files to an S3 bucket:

bash
Copy code
aws s3 sync ./website-code/ s3://your-s3-bucket-name/
4. Access the Website
Once all resources are provisioned, access the dynamic website by visiting the domain name you configured with Route 53.

🔑 Database Configuration
Make sure to update the .env file with your RDS credentials:

bash
Copy code
S3_URI=s3://your-sql-files/website.sql
RDS_ENDPOINT=your-rds-endpoint.amazonaws.com
RDS_DB_NAME=applicationdb
RDS_DB_USERNAME=your-username
RDS_DB_PASSWORD=your-password
Run the database migration using Flyway:

bash
Copy code
flyway -url=jdbc:mysql://"$RDS_ENDPOINT":3306/"$RDS_DB_NAME" \
  -user="$RDS_DB_USERNAME" \
  -password="$RDS_DB_PASSWORD" \
  -locations=filesystem:sql \
  migrate
📄 EC2 Instance Setup for Web Hosting
To install the web server and application on an EC2 instance, use the following commands:

bash
Copy code
#!/bin/bash

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
Restart Apache to apply changes:

bash
Copy code
sudo service httpd restart
🚀 How It Works
EC2 Instances: Hosting the website within secure private subnets.
Application Load Balancer: Distributes traffic across EC2 instances to ensure high availability.
Auto Scaling Group: Ensures that the number of instances adjusts based on demand, maintaining consistent performance.
RDS Database: Manages application data with secure access from EC2 instances.
CloudWatch and SNS: Monitor system performance and notify administrators of issues.
🌟 Features
Scalability: Automatically scales EC2 instances based on traffic load.
Fault Tolerance: Multiple Availability Zones for enhanced availability.
Security: Uses VPC, security groups, and SSL/TLS certificates for secure communication.
Automation: Deployment and provisioning scripts automate the entire setup.
📅 Future Enhancements
CI/CD Pipeline: Integrate with Jenkins or GitHub Actions for continuous integration and deployment.
Containerization: Migrate the application to use AWS ECS or EKS for containerized deployment.
Advanced Monitoring: Implement CloudWatch Alarms for automated issue resolution.

## Common Problems & Solutions

### Problem 1: **Unable to connect to EC2 instances**
- **Solution**: Ensure that the Security Group for your EC2 instances allows inbound traffic on the necessary ports (e.g., port 80 for HTTP, port 443 for HTTPS). Also, verify that the correct key pair is being used for SSH access.

### Problem 2: **Auto Scaling not working as expected**
- **Solution**: Check the Auto Scaling Group settings, ensuring that the desired capacity, minimum, and maximum instance settings are correctly configured. Also, ensure that the Application Load Balancer is properly registered with the target group.

### Problem 3: **Database connection issues**
- **Solution**: Verify that the database security group allows inbound traffic from the EC2 instances' security group. Check the database credentials in the `.env` file of your application.

### Problem 4: **S3 sync fails**
- **Solution**: Ensure that the IAM user has the appropriate permissions to access the S3 bucket. Verify that the correct S3 bucket name and path are used in the script.
    
