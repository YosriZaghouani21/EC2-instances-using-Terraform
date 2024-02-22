# Project Terraform Setup

## Pre-requisites
Make sure you have the following pre-requisites installed before proceeding:

1. Install AWS CLI first on your OS or your EC2 instance.
2. Create a new access key if you don't have one. Download the keys to your local machine. 
   - Login to AWS console, click on your username, and go to My security credentials.
   - Continue to security credentials, click on access keys.

## Setup AWS Configuration

Run the following command in OS/EC2 where you have installed Terraform:

```bash

aws configure
```
## Setup Project Directory
```bash

cd ~
mkdir project-terraform
cd project-terraform
```
## Create Terraform Variables

```bash
variable "aws_region" {
  description = "The AWS region to create things in." 
  default     = "us-east-2" 
}

variable "key_name" { 
  description = "SSH keys to connect to ec2 instance" 
  default     =  "myJune2021Key" 
}

variable "instance_type" { 
  description = "instance type for ec2" 
  default     =  "t2.micro" 
}

variable "security_group" { 
  description = "Name of security group" 
  default     = "my-jenkins-security-group-2022" 
}

variable "tag_name" { 
  description = "Tag Name for Ec2 instance" 
  default     = "my-ec2-instance" 
} 

variable "ami_id" { 
  description = "AMI for Ubuntu Ec2 instance" 
  default     = "ami-0b9064170e32bde34" 
}
```


## Create Terraform Configuration
```bash

provider "aws" {
  region = var.aws_region
}

resource "aws_vpc" "main" {
  cidr_block       = "172.16.0.0/16"
  instance_tenancy = "default"
  tags = {
    Name = "main"
  }
}

resource "aws_security_group" "jenkins-sg-2022" {
  name        = var.security_group
  description = "security group for jenkins"

  ingress {
    from_port   = 8080
    to_port     = 8080
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 65535
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags= {
    Name = var.security_group
  }
}

resource "aws_instance" "myFirstInstance" {
  ami           = var.ami_id
  key_name      = var.key_name
  instance_type = var.instance_type
  vpc_security_group_ids = [aws_security_group.jenkins-sg-2022.id]
  tags= {
    Name = var.tag_name
  }
}
```

## Initialize Terraform
Run the following command to initialize Terraform:
```bash
terraform init
```

## Terraform Plan
Run the following command to see how many resources will be added:

```bash
terraform plan
```

## Terraform Apply
Run the following command to apply the changes:

```bash
terraform apply
```

Terraform will prompt for confirmation. Enter 'yes' to proceed.



## Also To Destroy the instance 
Run the following command to destroy the resources:


```bash
terraform destroy
```

# Be cautious when running terraform destroy as it will permanently delete the resources, and there is no way to undo this action.

## Terraform will then proceed to destroy the resources. Once the process is complete, you should see a message indicating that the destruction is finished.!!







