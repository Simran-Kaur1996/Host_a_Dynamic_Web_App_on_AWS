# Dynamic Website Hosting on AWS

This project demonstrates the deployment and hosting of a dynamic website on AWS, utilizing various services and components to ensure high availability, scalability, security, and fault tolerance.

## Architecture Overview
![Project Diagram](3._Host_a_Dynamic_Web_App_on_AWS.gif)

The website is hosted on EC2 instances within a Virtual Private Cloud (VPC) configured with public and private subnets spanning two Availability Zones. The infrastructure leverages the following AWS resources:

- **Virtual Private Cloud (VPC)**: A logically isolated section of the AWS cloud where AWS resources are launched.
- **Internet Gateway**: Enables communication between the VPC instances and the internet.
- **Security Groups**: Acts as virtual firewalls to control inbound and outbound traffic.
- **Availability Zones**: Multiple Availability Zones are used to increase reliability and fault tolerance.
- **Public Subnets**: Hosts infrastructure components like the NAT Gateway and Application Load Balancer.
- **Private Subnets**: Hosts the web servers (EC2 instances) for enhanced security.
- **NAT Gateway**: Allows instances in private subnets to access the internet.
- **EC2 Instance Connect Endpoint**: Enables secure connections to EC2 instances within both public and private subnets.
- **Application Load Balancer**: Distributes incoming web traffic across multiple EC2 instances in an Auto Scaling group.
- **Auto Scaling Group**: Automatically manages the EC2 instances hosting the website, ensuring availability, scalability, fault tolerance, and elasticity.
- **AWS Certificate Manager**: Secures application communications with SSL/TLS certificates.
- **Simple Notification Service (SNS)**: Sends notifications about activities within the Auto Scaling Group.
- **Route 53**: Provides DNS services for registering and managing the website's domain name.
- **S3 Bucket**: Stores the application code and assets.

## Deployment

The deployment of this infrastructure is automated using scripts and configuration files stored in a GitHub repository. The repository includes the following:

- **Reference Diagram**: A visual representation of the AWS infrastructure and its components.
- **Deployment Scripts**: Scripts to provision and configure the necessary AWS resources.

### Prerequisites

- AWS account with appropriate permissions to create VPC, EC2 instances, Load Balancers, and other AWS resources.
- AWS CLI and Terraform (or CloudFormation) installed on your local machine.
- GitHub account to clone the repository.

## Steps to Deploy

### EC2 Instance Setup and Website Installation

1. Clone the GitHub repository to your local machine.

   ```bash
   git clone <repository-url>
Navigate to the repository directory.

bash
Copy code
cd <repository-directory>
Install necessary software on EC2 instance:

Update all packages:
bash
Copy code
sudo yum update -y
Install Apache web server:
bash
Copy code
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd
Install PHP and required extensions:
bash
Copy code
sudo dnf install -y \
php \
php-pdo \
php-openssl \
php-mbstring \
php-exif \
php-fileinfo \
php-xml \
php-ctype \
php-json \
php-tokenizer \
php-curl \
php-cli \
php-fpm \
php-mysqlnd \
php-bcmath \
php-gd \
php-cgi \
php-gettext \
php-intl \
php-zip
Install MySQL version 8:
bash
Copy code
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server
sudo systemctl start mysqld
sudo systemctl enable mysqld
Enable mod_rewrite module in Apache for URL rewriting:

bash
Copy code
sudo sed -i '/<Directory "\/var\/www\/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf
Download website files from S3:

Set environment variables for your S3 bucket:
bash
Copy code
S3_BUCKET_NAME=simran-project-web-files
Sync the S3 bucket to the Apache web directory:
bash
Copy code
sudo aws s3 sync s3://"$S3_BUCKET_NAME" /var/www/html
Extract and configure website files:

Change to the web directory:
bash
Copy code
cd /var/www/html
Unzip the website application files:
bash
Copy code
sudo unzip shopwise.zip
Copy the extracted files:
bash
Copy code
sudo cp -R shopwise/. /var/www/html/
Remove the temporary files:
bash
Copy code
sudo rm -rf shopwise shopwise.zip
Set appropriate permissions for web files:

bash
Copy code
sudo chmod -R 777 /var/www/html
sudo chmod -R 777 storage/
Edit .env file for database credentials:

bash
Copy code
sudo vi .env
Restart Apache to apply changes:

bash
Copy code
sudo service httpd restart
S3 and Flyway Database Migration
Configure the Flyway migration for the database:

Set the environment variables for S3 URI and RDS:
bash
Copy code
S3_URI=s3://simran-sql-files/V1__shopwise.sql
RDS_ENDPOINT=dev-rds-db.czaskymeyjkw.us-east-1.rds.amazonaws.com
RDS_DB_NAME=applicationdb
RDS_DB_USERNAME=azeezs
RDS_DB_PASSWORD=waheguru12345
Install Flyway and run the migration:

Update the EC2 packages and install Flyway:

bash
Copy code
sudo yum update -y
sudo wget -qO- https://download.red-gate.com/maven/release/com/redgate/flyway/flyway-commandline/10.9.1/flyway-commandline-10.9.1-linux-x64.tar.gz | tar -xvz
sudo ln -s $(pwd)/flyway-10.9.1/flyway /usr/local/bin
Download the migration script from S3:

bash
Copy code
sudo mkdir sql
sudo aws s3 cp "$S3_URI" sql/
Run the Flyway migration:

bash
Copy code
flyway -url=jdbc:mysql://"$RDS_ENDPOINT":3306/"$RDS_DB_NAME" \
  -user="$RDS_DB_USERNAME" \
  -password="$RDS_DB_PASSWORD" \
  -locations=filesystem:sql \
  migrate
Problems and Solutions
Problem 1: EC2 instance cannot access the internet
Solution:

Ensure that the EC2 instance is in a public subnet and that the Internet Gateway is attached to the VPC.
Verify that the Security Group associated with the EC2 instance allows outbound traffic on HTTP/HTTPS ports (80/443).
Ensure that the NAT Gateway is properly configured if the EC2 instance is in a private subnet.
Problem 2: Unable to connect to MySQL database from the application
Solution:

Ensure that the Security Group associated with the EC2 instance allows inbound traffic on the MySQL port (3306).
Verify that the RDS instance has the correct security group settings allowing inbound traffic from the EC2 instances on port 3306.
Ensure that the correct RDS endpoint, username, and password are configured in the .env file.
Problem 3: Website is not loading after deployment
Solution:

Check the Apache error logs located at /var/log/httpd/error_log to identify any issues.
Verify that the correct permissions are set for the /var/www/html directory and the application files.
Ensure that the Apache service is running and restarted after any changes to configurations.
Problem 4: Flyway migration fails
Solution:

Check the Flyway logs to identify any issues during the migration.
Ensure that the RDS endpoint and database credentials are correct in the Flyway migration command.
Verify that the SQL file being applied is valid and correctly formatted.

