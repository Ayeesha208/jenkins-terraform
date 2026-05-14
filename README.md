# Jenkins Infrastructure Deployment using Terraform and Docker Compose

## Project Overview

This project provisions a Jenkins Server on AWS using Terraform with a modular architecture approach. Jenkins is deployed using Docker Compose with persistent storage backed by an EBS volume and automated daily EBS snapshots.

---

## Architecture Components

- AWS EC2 Instance
- Elastic IP
- Security Group
- IAM Role & Instance Profile
- EBS Volume
- Docker & Docker Compose
- Jenkins Container
- AWS DLM Snapshot Policy

---

## Project Structure

```bash
terraform-jenkins/
├── modules/
│   ├── ec2/
│   ├── eip/
│   ├── ebs/
│   ├── iam/
│   ├── security-group/
│   └── snapshot/
│
├── environments/
│   └── prod/
│       ├── main.tf
│       ├── variables.tf
│       ├── terraform.tfvars
│       ├── outputs.tf
│       ├── provider.tf
│       └── versions.tf
│
└── user-data/
    └── install.sh
```

---

## Jenkins Deployment

Jenkins is deployed using Docker Compose from a GitHub repository cloned automatically during EC2 bootstrap.

Persistent data path:

```bash
/data/jenkins
```

Container mount:

```yaml
/data/jenkins:/var/jenkins_home
```

---

## Features Implemented

- Terraform modular infrastructure
- Automated EC2 provisioning
- Elastic IP association
- IAM role attachment
- Docker automated installation
- Jenkins automated deployment
- Persistent EBS storage
- Automated daily EBS snapshots
- GitHub repository cloning
- Jenkins accessible via browser

---

## Deployment Steps

### Initialize Terraform

```bash
terraform init
```

### Validate Configuration

```bash
terraform validate
```

### Review Plan

```bash
terraform plan
```

### Apply Infrastructure

```bash
terraform apply
```

---

## Access Jenkins

```bash
http://<Elastic-IP>:8080
```

---

## Verify Docker Container

```bash
docker ps
```

---

## Verify EBS Mount

```bash
df -h
```

---

## Destroy Infrastructure

```bash
terraform destroy
```

---

## Security Best Practices

- Restricted SSH access using personal public IP
- IAM Instance Profile used
- Persistent storage separation
- Infrastructure as Code approach
- Automated backup policy enabled

---

## Author

Implemented by Charan Sai
