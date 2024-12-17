# terraform-aws-demo
This project demonstrates how I used Terraform to provision an EC2 instance and an S3 bucket on AWS. It also includes enabling remote state storage in S3 and state locking with DynamoDB for consistency and collaboration.
Steps Taken

# Project Setup
Created a folder to store Terraform configuration files:
main.tf for infrastructure code.
terraform.tfstate and terraform.tfstate.backup for state management.

# AWS Provider Configuration
Specified the AWS region for the provider:

provider "aws" {  
  region = "us-west-2"  
}  

# S3 Bucket Creation

resource "aws_s3_bucket" "example_bucket" {  
  bucket = "nanuka-aws-bucket-7878"  
  acl    = "private"  

  versioning {  
    enabled = true  
  }  

  tags = {  
    Name        = "MyTerraformS3Bucket"  
    Environment = "Dev"  
  }  
}  
 

# EC2 Instance Launch

Launched a t2.micro EC2 instance with a valid Ubuntu AMI:

resource "aws_instance" "example_instance" {  
  ami           = "ami-08e2d37b6a0129927"  # ubuntu ami 
  instance_type = "t2.micro"  

  tags = {  
    Name = "MyTerraformEC2"  
  }  
}  
 

# Remote State Storage and Locking

Set up remote state storage in the S3 bucket and enabled state locking using DynamoDB:

terraform {  
  backend "s3" {  
    bucket         = "nanuka-aws-bucket-7878"  
    key            = "terraform/state"  
    region         = "us-west-2"  
    dynamodb_table = "terraform-locks"  
    encrypt        = true  
  }  
}  

# Created the DynamoDB table for state locking using the AWS CLI:

aws dynamodb create-table \  
  --table-name terraform-locks \  
  --attribute-definitions AttributeName=LockID,AttributeType=S \  
  --key-schema AttributeName=LockID,KeyType=HASH \  
  --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1  
  
# Reinitializing Terraform

terraform init  

# Deployment

terraform apply  

# Screenshots

AWS Console: EC2 instance and S3 bucket.
Terminal Output: Successful terraform apply and DynamoDB table creation.

# Key Features

Infrastructure as Code (IaC) with Terraform.
Remote state storage with S3 for team collaboration.
State locking with DynamoDB for consistency.
