Terraform Jenkins Deployment on AWS — Assignment Documentation
Overview

This assignment demonstrates the implementation of a production-style Jenkins deployment on AWS using Terraform with a modular architecture. Jenkins is deployed using Docker Compose, with persistent storage and automated EBS snapshot backups.

The solution includes:

Modular Terraform code
AWS EC2 instance provisioning
Elastic IP association
Docker & Docker Compose installation
Jenkins deployment using Docker Compose
Jenkins data persistence
GitHub repository integration
Automated daily EBS snapshots using AWS DLM
Architecture Overview
Terraform
   ↓
AWS Infrastructure Creation
   ↓
VPC + Security Group + IAM + EC2 + EIP
   ↓
EC2 User Data Execution
   ↓
Docker + Docker Compose Installation
   ↓
Clone Docker Compose Repo from GitHub
   ↓
docker compose up -d
   ↓
Jenkins Container Running
   ↓
Persistent Jenkins Data Volume
   ↓
Daily EBS Snapshot Automation
Objective

The objective of this assignment is to automate Jenkins deployment infrastructure using Terraform while following Infrastructure as Code (IaC) best practices and modular design principles.

Technologies Used
Technology	Purpose
Terraform	Infrastructure provisioning
AWS EC2	Jenkins hosting
AWS EIP	Static public IP
AWS DLM	Automated EBS snapshots
Docker	Containerization
Docker Compose	Jenkins orchestration
GitHub	Docker Compose repository
Amazon Linux 2023	Operating system
Folder Structure
terraform-jenkins/
│
├── environments/
│   └── prod/
│       ├── main.tf
│       ├── provider.tf
│       ├── variables.tf
│       ├── terraform.tfvars
│       ├── outputs.tf
│       └── versions.tf
│
├── modules/
│   ├── ec2/
│   ├── ebs/
│   ├── eip/
│   ├── iam/
│   ├── security-group/
│   └── snapshot/
│
└── user-data/
    └── install.sh
GitHub Repository Setup
Step 1 — Create GitHub Repository

A GitHub repository was created to maintain the Docker Compose configuration.

Repository Name:

jenkins-docker-compose
Step 2 — Create docker-compose.yml

The Docker Compose file was created to deploy Jenkins inside a Docker container.

File:

docker-compose.yml

Content:

version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts

    container_name: jenkins

    restart: always

    ports:
      - "8080:8080"
      - "50000:50000"

    volumes:
      - jenkins_data:/var/jenkins_home

volumes:
  jenkins_data:
Explanation of Docker Compose
Component	Purpose
jenkins/jenkins:lts	Official Jenkins image
ports	Exposes Jenkins UI
restart: always	Auto restart container
jenkins_data volume	Ensures Jenkins persistence
Jenkins Data Persistence

Jenkins stores all critical data inside:

/var/jenkins_home

This directory is mounted to a Docker volume:

volumes:
  - jenkins_data:/var/jenkins_home

This ensures:

Jenkins jobs persist
Plugins persist
Credentials persist
Data survives container restart or recreation
Terraform Modular Architecture

Terraform modules were implemented to follow best practices.

Each module handles a specific infrastructure component.

Modules created:

Module	Responsibility
ec2	EC2 instance
ebs	EBS volume
eip	Elastic IP
iam	IAM role/profile
security-group	Security rules
snapshot	Daily snapshot automation
Terraform Workflow
Step 1 — Provider Configuration

Terraform AWS provider configured in:

provider.tf
Step 2 — Variables Configuration

Variables were defined in:

variables.tf
terraform.tfvars

Example:

aws_region  = "ap-south-1"
instance_type = "t2.small"
key_name = "jenkins-key"
EC2 Provisioning

Terraform provisions:

Jenkins EC2 instance
Security group
IAM role
Elastic IP

The EC2 instance uses Amazon Linux 2023.

Elastic IP Configuration

An Elastic IP was attached to the EC2 instance to provide a static public IP address for Jenkins access.

Terraform resource used:

aws_eip
User Data Automation

A bootstrap script was created:

user-data/install.sh

This script automatically:

installs Docker
installs Docker Compose
installs Git
clones GitHub repository
starts Jenkins container
User Data Script Workflow
EC2 Launch
   ↓
Install Docker
   ↓
Install Docker Compose
   ↓
Clone GitHub Repo
   ↓
docker compose up -d
   ↓
Jenkins Running
Automated EBS Snapshots

AWS Data Lifecycle Manager (DLM) was configured to automate EBS snapshots.

Snapshot policy:

Daily snapshots
24-hour interval
Automatic retention

This ensures backup and disaster recovery capability.

Terraform Execution Steps
Initialize Terraform
terraform init
Validate Configuration
terraform validate
Review Execution Plan
terraform plan
Deploy Infrastructure
terraform apply
Successful Deployment Output

Terraform successfully created:

EC2 instance
EBS volume
Elastic IP
Security group
Snapshot policy

Example output:

Apply complete! Resources: 9 added, 0 changed, 0 destroyed.

Public IP output:

jenkins_public_ip = "3.xxx.xxx.xxx"
Jenkins Access

Jenkins was accessed using:

http://<elastic-ip>:8080
Verification Steps Performed
1. Verify Jenkins Container

SSH into EC2:

docker ps

Expected output:

jenkins/jenkins:lts
2. Verify Docker Compose
docker compose ps
3. Verify Jenkins Persistence

Test performed:

Created Jenkins job
Restarted Jenkins container
docker restart jenkins

Result:

Jenkins job remained intact
Confirms persistence working successfully
4. Verify GitHub Clone

Verified repository cloned successfully:

cd /opt/jenkins

ls

Expected:

jenkins-docker-compose
5. Verify EBS Snapshots

AWS Console verification:

EC2 → Lifecycle Manager

Confirmed:

Snapshot policy active
Daily snapshot schedule configured
Security Implemented

Security Group configured with:

Port	Purpose
22	SSH Access
8080	Jenkins UI
Best Practices Followed
Best Practice	Implemented
Modular Terraform	Yes
Infrastructure as Code	Yes
Automated Deployment	Yes
Persistent Storage	Yes
Backup Automation	Yes
GitHub Integration	Yes
Elastic IP	Yes
Dockerized Jenkins	Yes
