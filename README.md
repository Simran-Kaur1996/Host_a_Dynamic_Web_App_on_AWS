# Dynamic Website Hosting on AWS

This project demonstrates the deployment and hosting of a dynamic website on AWS, utilizing various services and components to ensure high availability, scalability, security, and fault tolerance.

## Architecture Overview

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

1. Clone the GitHub repository to your local machine.

   ```bash
   git clone <repository-url>

