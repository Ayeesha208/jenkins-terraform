# Terraform Jenkins Deployment on AWS — Assignment Documentation

## Overview

This assignment demonstrates the implementation of a production-style Jenkins deployment on AWS using Terraform with a modular architecture. Jenkins is deployed using Docker Compose, with persistent storage and automated EBS snapshot backups.

The solution includes:

- Modular Terraform code
- AWS EC2 instance provisioning
- Elastic IP association
- Docker & Docker Compose installation
- Jenkins deployment using Docker Compose
- Jenkins data persistence
- GitHub repository integration
- Automated daily EBS snapshots using AWS DLM

---

# Architecture Overview

```text
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
```

---

# Objective

The objective of this assignment is to automate Jenkins deployment infrastructure using Terraform while following Infrastructure as Code (IaC) best practices and modular design principles.

---

# Technologies Used

| Technology | Purpose |
|---|---|
| Terraform | Infrastructure provisioning |
| AWS EC2 | Jenkins hosting |
| AWS EIP | Static public IP |
| AWS DLM | Automated EBS snapshots |
| Docker | Containerization |
| Docker Compose | Jenkins orchestration |
| GitHub | Docker Compose repository |
| Amazon Linux 2023 | Operating system |

---

# Folder Structure

```text
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
```

---

# GitHub Repository Setup

## Step 1 — Create GitHub Repository

A GitHub repository was created to maintain the Docker Compose configuration.

Repository Name:

```text
jenkins-docker-compose
```

---

## Step 2 — Create docker-compose.yml

The Docker Compose file was created to deploy Jenkins inside a Docker container.

File:

```text
docker-compose.yml
```

Content:

```yaml
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
```

---

# Explanation of Docker Compose

| Component | Purpose |
|---|---|
| jenkins/jenkins:lts | Official Jenkins image |
| ports | Exposes Jenkins UI |
| restart: always | Auto restart container |
| jenkins_data volume | Ensures Jenkins persistence |

---

# Jenkins Data Persistence

Jenkins stores all critical data inside:

```text
/var/jenkins_home
```

This directory is mounted to a Docker volume:

```yaml
volumes:
  - jenkins_data:/var/jenkins_home
```

This ensures:

- Jenkins jobs persist
- Plugins persist
- Credentials persist
- Data survives container restart or recreation

---

# Terraform Modular Architecture

Terraform modules were implemented to follow best practices.

Each module handles a specific infrastructure component.

Modules created:

| Module | Responsibility |
|---|---|
| ec2 | EC2 instance |
| ebs | EBS volume |
| eip | Elastic IP |
| iam | IAM role/profile |
| security-group | Security rules |
| snapshot | Daily snapshot automation |

---

# Terraform Workflow

## Step 1 — Provider Configuration

Terraform AWS provider configured in:

```text
provider.tf
```

---

## Step 2 — Variables Configuration

Variables were defined in:

```text
variables.tf
terraform.tfvars
```

Example:

```hcl
aws_region  = "ap-south-1"
instance_type = "t2.small"
key_name = "jenkins-key"
```

---

# EC2 Provisioning

Terraform provisions:

- Jenkins EC2 instance
- Security group
- IAM role
- Elastic IP

The EC2 instance uses Amazon Linux 2023.

---

# Elastic IP Configuration

An Elastic IP was attached to the EC2 instance to provide a static public IP address for Jenkins access.

Terraform resource used:

```hcl
aws_eip
```

---

# User Data Automation

A bootstrap script was created:

```text
user-data/install.sh
```

This script automatically:

- installs Docker
- installs Docker Compose
- installs Git
- clones GitHub repository
- starts Jenkins container

---

# User Data Script Workflow

```text
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
```

---

# Automated EBS Snapshots

AWS Data Lifecycle Manager (DLM) was configured to automate EBS snapshots.

Snapshot policy:

- Daily snapshots
- 24-hour interval
- Automatic retention

This ensures backup and disaster recovery capability.

---

# Terraform Execution Steps

## Initialize Terraform

```bash
terraform init
```

---

## Validate Configuration

```bash
terraform validate
```

---

## Review Execution Plan

```bash
terraform plan
```

---

## Deploy Infrastructure

```bash
terraform apply
```

---

# Successful Deployment Output

Terraform successfully created:

- EC2 instance
- EBS volume
- Elastic IP
- Security group
- Snapshot policy

Example output:

```text
Apply complete! Resources: 9 added, 0 changed, 0 destroyed.
```

Public IP output:

```text
jenkins_public_ip = "3.xxx.xxx.xxx"
```
<img width="1388" height="528" alt="image" src="https://github.com/user-attachments/assets/2208ba06-da40-46c3-83b3-beba03abb5e8" />

---

# Jenkins Access

Jenkins was accessed using:

```text
http://<elastic-ip>:8080
```
<img width="1889" height="841" alt="image" src="https://github.com/user-attachments/assets/b708047a-19ee-40e4-aacf-39b16a6ce280" />

---

# Verification Steps Performed

## 1. Verify Jenkins Container

SSH into EC2:

```bash
docker ps
```

Expected output:

```text
jenkins/jenkins:lts
```
<img width="1919" height="554" alt="image" src="https://github.com/user-attachments/assets/aa7d1823-0dc3-49fd-bbf0-bd2ea500dc8b" />

---


## 2. Verify Jenkins Persistence

Test performed:

- Created Jenkins job
- Restarted Jenkins container
<img width="1916" height="756" alt="image" src="https://github.com/user-attachments/assets/35ec4776-a1bf-4aee-9ecf-f64f0e8dc5b4" />
```bash
docker restart jenkins
```
<img width="840" height="114" alt="image" src="https://github.com/user-attachments/assets/f338facf-7e70-4239-a91e-b50c62e4d3db" />

Result:

- Jenkins job remained intact
- Confirms persistence working successfully

<img width="1919" height="575" alt="image" src="https://github.com/user-attachments/assets/6a6fdc87-a6e0-4f39-b0d0-589272d8da3d" />

---

## 3. Verify GitHub Clone

Verified repository cloned successfully:

```bash
cd /opt

ls
```

Expected:

```text
jenkins-docker-compose
```
<img width="679" height="96" alt="image" src="https://github.com/user-attachments/assets/e1b8ab95-6c52-42f8-b32d-a1ed832eef7c" />

---

## 4. Verify EBS Snapshots

AWS Console verification:

```text
EC2 → Lifecycle Manager
```

Confirmed:

- Snapshot policy active
- Daily snapshot schedule configured

<img width="1897" height="853" alt="image" src="https://github.com/user-attachments/assets/e6678c2d-0a54-463e-9514-9d119181a259" />

---

# Security Implemented

Security Group configured with:

| Port | Purpose |
|---|---|
| 22 | SSH Access |
| 8080 | Jenkins UI |

---

# Best Practices Followed

| Best Practice | Implemented |
|---|---|
| Modular Terraform | Yes |
| Infrastructure as Code | Yes |
| Automated Deployment | Yes |
| Persistent Storage | Yes |
| Backup Automation | Yes |
| GitHub Integration | Yes |
| Elastic IP | Yes |
| Dockerized Jenkins | Yes |

---

# What To Demonstrate During Technical Discussion

## Terraform Structure

Show:

- modular folder structure
- separate modules
- variables and outputs

---

## Terraform Deployment

Run:

```bash
terraform init
terraform plan
terraform apply
```
<img width="1328" height="741" alt="image" src="https://github.com/user-attachments/assets/d6f1e4fd-9bfb-4119-ad45-024a767ef1ae" />

<img width="1360" height="648" alt="image" src="https://github.com/user-attachments/assets/f98de635-0623-4c0e-a927-36efe88ab76f" />

---

## Jenkins Running

Show:

```bash
docker ps
```

---

## Docker Compose File

Show GitHub repository:

- docker-compose.yml
- Jenkins configuration

---

## Jenkins UI

Open:

```text
http://<elastic-ip>:8080
```

---

## Persistence Validation

Show:

- Jenkins job exists
- restart container
- job still exists

---

## Snapshot Verification

Show:

- AWS DLM policy
- scheduled snapshots

---

# Conclusion

- modular Terraform implementation
- automated AWS infrastructure provisioning
- containerized Jenkins deployment
- persistent storage implementation
- GitHub-integrated deployment workflow
- automated EBS backup strategy

The infrastructure was deployed successfully and validated end-to-end.
````
