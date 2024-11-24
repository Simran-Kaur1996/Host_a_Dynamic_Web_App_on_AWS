AWS Dynamic Website Hosting with DevOps Practices
This project demonstrates the deployment and hosting of a dynamic website on AWS, utilizing various AWS services to ensure high availability, scalability, security, and fault tolerance. The infrastructure includes a combination of EC2, VPC, load balancing, auto-scaling, and monitoring services to provide a robust and scalable web hosting environment.

Table of Contents
Architecture Overview
Deployment Instructions
Prerequisites
Steps
Challenges Faced and Solutions
Conclusion
Repository Contents
Architecture Overview
The website is hosted on EC2 instances within a Virtual Private Cloud (VPC) configured with both public and private subnets across two Availability Zones. The infrastructure utilizes the following AWS resources:

Virtual Private Cloud (VPC): A logically isolated section of AWS cloud where AWS resources are deployed.
Internet Gateway: Provides internet connectivity to VPC instances.
Security Groups: Acts as a firewall to control inbound and outbound traffic to instances.
Availability Zones: The infrastructure spans two availability zones for increased fault tolerance and high availability.
Public Subnets: Hosts infrastructure components like the NAT Gateway and Application Load Balancer.
Private Subnets: Hosts EC2 instances (web servers) to enhance security.
NAT Gateway: Allows instances in private subnets to access the internet for updates or other communications.
EC2 Instance Connect Endpoint: Enables secure connections to EC2 instances within both public and private subnets.
Application Load Balancer (ALB): Distributes incoming web traffic to EC2 instances within an Auto Scaling group.
Auto Scaling Group: Automatically adjusts the number of EC2 instances based on traffic load, ensuring elasticity and fault tolerance.
AWS Certificate Manager (ACM): Secures application traffic using SSL/TLS certificates.
Simple Notification Service (SNS): Sends notifications regarding activities within the Auto Scaling Group.
Route 53: Manages DNS records and domain name registration.
Amazon S3: Stores application code and assets.
Deployment Instructions
Prerequisites
Before you begin, ensure the following:

AWS account with appropriate IAM permissions.
AWS CLI installed and configured.
GitHub repository cloned to your local machine.
Steps
Clone the GitHub Repository: Clone the repository containing the deployment scripts and reference diagrams.

bash
Copy code
git clone <repository_url>
cd <repository_directory>
Set Up the Required AWS Resources: Follow the instructions in the repository’s documentation to set up the AWS resources, including the VPC, subnets, EC2 instances, Auto Scaling Group, and Load Balancer.

Deploy Website Code to S3: Store your application code and assets in an S3 bucket. The application files will be synced with EC2 instances from this bucket.

bash
Copy code
aws s3 sync ./app-code s3://<your-s3-bucket-name>/
Deploy Website on EC2: After the EC2 instances are set up, use the following script to install the necessary software (Apache, PHP, MySQL) and deploy the application on EC2.

bash
Copy code
#!/bin/bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP and required extensions
sudo dnf install -y php php-pdo php-openssl php-mbstring php-exif php-fileinfo php-xml php-ctype php-json php-tokenizer php-curl php-cli php-fpm php-mysqlnd php-bcmath php-gd php-cgi php-gettext php-intl php-zip

# Install and configure MySQL 8
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf install -y mysql-community-server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Download and extract application code from S3
sudo aws s3 sync s3://<your-s3-bucket-name> /var/www/html
sudo unzip /var/www/html/shopwise.zip -d /var/www/html/

# Set appropriate permissions
sudo chmod -R 777 /var/www/html
sudo chmod -R 777 /var/www/html/storage

# Restart Apache server to apply changes
sudo service httpd restart
Configure the Database: Use Flyway to migrate the database schema from the SQL file stored in S3 to your RDS instance.

bash
Copy code
# Configure Flyway
sudo wget https://download.red-gate.com/maven/release/com/redgate/flyway/flyway-commandline/10.9.1/flyway-commandline-10.9.1-linux-x64.tar.gz | tar -xvz
sudo ln -s $(pwd)/flyway-10.9.1/flyway /usr/local/bin

# Migrate Database
flyway -url=jdbc:mysql://<RDS_ENDPOINT>:3306/<RDS_DB_NAME> -user=<RDS_DB_USERNAME> -password=<RDS_DB_PASSWORD> -locations=filesystem:/sql migrate
Configure Auto Scaling & Load Balancer: Set up the Application Load Balancer and Auto Scaling Group to ensure high availability and load balancing.

Set Up SNS Notifications: Configure Amazon SNS to send notifications about scaling activities.

DNS Configuration: Use Route 53 to register a domain name and point it to the Application Load Balancer.

bash
Copy code
aws route53 change-resource-record-sets --hosted-zone-id <zone_id> --change-batch file://dns_config.json
Challenges Faced and Solutions
1. EC2 Instances in Private Subnets Not Accessible for Web Traffic
Issue: EC2 instances deployed in private subnets couldn’t receive web traffic because the load balancer was not correctly routing traffic.
Solution: I configured the Application Load Balancer to route traffic to EC2 instances in private subnets and ensured that the NAT Gateway in the public subnet allowed EC2 instances to access the internet.
2. Insufficient Database Connections
Issue: The MySQL database hosted on RDS experienced connection issues due to excessive concurrent requests.
Solution: Increased the max_connections parameter in MySQL and optimized queries to handle higher load. I also used Auto Scaling for EC2 instances to balance the web traffic.
3. Incorrect Permissions for S3 Bucket
Issue: EC2 instances failed to access application files stored in S3 due to restrictive bucket permissions.
Solution: Modified the S3 Bucket Policy to allow EC2 instances access by creating an appropriate IAM Role with S3 read permissions.
4. Application Not Available After Deployment
Issue: The website was inaccessible after deploying code due to missing dependencies and misconfigurations.
Solution: Created a script to automatically install necessary software (Apache, PHP, MySQL) and ensure the EC2 instance was correctly configured to serve the website.
5. SSL Certificate Installation Failures
Issue: SSL/TLS certificates failed to install properly for HTTPS communication.
Solution: Used AWS Certificate Manager (ACM) to manage and validate SSL certificates. Configured the Load Balancer to handle HTTPS traffic and applied the correct certificate.
Conclusion
This project showcases how to leverage AWS services for hosting a scalable, high-availability dynamic website. By using services like EC2, VPC, Auto Scaling, and Route 53, this setup ensures the website can handle varying levels of traffic while maintaining security and uptime.

Repository Contents
Reference Diagram: Visual representation of the AWS infrastructure.
Deployment Scripts: Scripts to provision and configure AWS resources.
SQL Migration Script: Flyway script to migrate the database schema to an RDS instance.
Web Hosting Scripts: Scripts to install and configure the web server and application on EC2 instances.
