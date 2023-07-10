---
title: "AWS Cloud Infrastructure: Step-by-Step Guide"
date: 2023-07-10T11:07:56+03:00
author: Mustafa Elghrib
location: Minya, Egypt
---

# Introduction
After I wrote on [how to build a cloud infrastructure](https://mustafaelghrib.github.io/how_to_build_cloud_infrastructure/), I decided to implement real world projects to show how you could build your own and follow best practices. Today I am writing on how to build a cloud infrastructure for backend API on AWS.

{{< figure src="/posts/aws_cloud_infrastructure.png" width="100%" >}}

# Contents
- [Our Project](#our-project)
- [Building The Cloud Infrastructure](#building-the-cloud-infrastructure)
    - [Define](#define)
    - [Choose](#choose)
    - [Plan](#plan)
    - [Design](#design)
    - [Implement](#implement)
    - [Deploy](#deploy)
- [Conclusion](#conclusion)

# Our Project
The project that we will work on it is a backend API that is built with Python, Django and PostgresSQL and conteriezied with Docker, you could find it [at this repo](https://github.com/mustafaelghrib/crewtech/tree/main/backend), clone it on you local computer and let's start building.

Note: This project could be any with any tech stack, the steps will be the same after all.

# Building The Cloud Infrastructure
So we have the project is set up and ready to go for production, and we want to deploy it to the cloud, and to do that we need to build the cloud infrastructure, so let's follow this steps to build it.

## Define
Our goal here is to just deploy this simple backend API, so it could be available on the internet and other people could access it.

To deploy it, we need to choose a deployment method, I wrote about some of them [at this article](https://mustafaelghrib.github.io/methods_of_deployment_with_examples/), but let's say we want to deploy it using docker on remote server.

So this defines what we actually need from the cloud, and from the things above we need:
- Docker registry so we could push and pull docker images from it.
- Remote server so we could access our backend api from it.
- PostgresSQL database as our backend API uses it.
- Storage as our backend API needs to save and retrieve static and media files.

## Choose
For this article we have chosen AWS as the cloud platform that will deploy our backend on it.
You need to create an AWS account as we will use it in the implementation step.

## Plan
Let's match the services we want with the services provided by AWS and wrote the initial configration that we want for those services.

| Our Service          | AWS Service | Configration                                    |
|----------------------|-------------|-------------------------------------------------|
| Docker Registry      | AWS ECR     | Repository Type: Public                         |
| Remote Server        | AWS EC2     | OS: Ubuntu, CPU: t2.micro, Volume: 20G          |
| PostgresSQL Database | AWS RDS     | Engine: Postgres, CPU: db.t3.micro, Volume: 20G |
| Storage              | AWS S3      | Access Type: Public                             |

## Design
Our design is very simple, we won't consider networking or security for now as it will make our design very complex, so let's focus on the services that we need only for now.

The design as shown below, is just the user access the IP address of the AWS EC2 instance and the AWS EC2 instance should run a Docker container from the Docker image that puled from the AWS ECR repository, this Docker image contain our code, and our code is set up to access the postgres database in the AWS RDS and the storage in the AWS S3.

{{< figure src="/images/cloud_infrastructure_diagram.png" width="100%" >}}

I used [draw.io](https://app.diagrams.net/?splash=0&libs=aws4) to do this diagram.

## Implement
We have more than one way to implement this cloud infrastructure on AWS, all of them work but one will be best based on your requirements and complexity of your project.

- **AWS Management Console**: The web interface of AWS, where you could create them one by one on their dashboard.
- **AWS CLI**: The command line, where you could create them using terminal.
- **AWS CloudFormation**: The IaC tool by AWS where you could provision services using YML.
- **AWS CDK**: Another IaC tool by AWS where you could provision services using programming languages like Python, TypeScript or Java.
- **Terraform**: IaC tool provided by HashiCorp, and they have AWS provider where we could use it to provision our AWS resources.

In this article we will use Terraform to create those AWS resources. Make sure to install it.

### The infrastructure folder
Let's create a new folder and name it **_infrastructure_** in the root directory of our project, and here is what it will contain.
```shell
├── secrets.auto.tfvars  # contains secrets, make sure to ignore it
├── main.tf
├── aws                  # aws module that contains aws resources
│    ├── EC2.tf
│    ├── ECR.tf
│    ├── RDS.tf
│    ├── S3.tf
├── ssh                  # bring your ssh keys, make sure you ignore them
│    ├── id_rsa.pub
│    ├── id_rsa
└── scripts
    └── install_docker.sh
```

### Set up AWS provider on Terraform
We need to set up the AWS provider, so we could use it in Terraform, so in the **_main.tf_** file we will write this:
```hcl
terraform {
  required_version = "1.3.1"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

provider "aws" {
  region     = var.aws.region
  access_key = var.aws.access_key
  secret_key = var.aws.secret_key
}
```

We need to add AWS credential as secrets, make sure to ignore this file, in a file named **_secrets.auto.tfvars_** we write this:
```hcl
aws = {
  region     = "" # your aws region
  access_key = "" # your aws access key
  secret_key = "" # your aws secert key
} 
```

Run the terraform init command to ensure that everything is working
```shell
terraform init
```

### Provision AWS EC2
```hcl
# aws/EC2.tf

terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }
  }
}

resource "aws_key_pair" "main" {
 key_name   = "example-project-ssh-key"
 public_key = file("${path.module}/ssh/id_rsa.pub")
}

resource "aws_instance" "main" {
 tags          = { "Name" = "example-project-instance" }
 ami           = "ami-08c40ec9ead489470"
 instance_type = "t2.micro"
 key_name      = aws_key_pair.main.key_name

 root_block_device {
   volume_size = "20"
   volume_type = "gp2"
 }

 connection {
   type        = "ssh"
   user        = "ubuntu"
   host        = self.public_dns
   private_key = file("${path.module}/ssh/id_rsa")
 }

 provisioner "remote-exec" {
   scripts = ["${path.module}/scripts/install_docker.sh"]
 }

}

output "INSTANCE_USER" { value = var.instance_user }
output "INSTANCE_IP" { value = aws_instance.main.public_dns }
output "INSTANCE_SSH_CONNECT" { value = "ssh ${var.instance_user}@${aws_instance.main.public_dns}" }
```

### Provision AWS RDS
```hcl
# aws/RDS.tf

terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }
  }
}

resource "aws_db_instance" "main" {

  identifier = "example-project-database"

  db_name  = "example-project-db"
  username = "example-project-user"
  password = var.database_password # save it in the secrets.auto.tfvars file
  port     = 5432

  engine         = "postgres"
  engine_version = "13.7"

  instance_class    = "db.t3.micro"
  storage_type      = "gp2"
  allocated_storage = "20"

  publicly_accessible      = true
  delete_automated_backups = true
  skip_final_snapshot      = true
  deletion_protection      = false
}

output "POSTGRES_DB" { value = aws_db_instance.main.db_name }
output "POSTGRES_USER" { value = aws_db_instance.main.username }
output "POSTGRES_PASSWORD" { value = aws_db_instance.main.password }
output "POSTGRES_HOST" { value = aws_db_instance.main.address }
output "POSTGRES_PORT" { value = aws_db_instance.main.port }
```

### Provision AWS S3
```hcl
# aws/S3.tf

terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }
  }
}

resource "aws_s3_bucket" "main" {
  bucket        = "example-project-bucket"
  force_destroy = true
}

resource "aws_s3_bucket_acl" "main" {
  bucket = aws_s3_bucket.main.id
  acl    = "public"
}

resource "aws_s3_bucket_public_access_block" "main" {
  bucket                  = aws_s3_bucket.main.id
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

output "AWS_ACCESS_KEY_ID" { value = var.bucket_access_key }
output "AWS_SECRET_ACCESS_KEY" { value = var.bucket_secret_key }
output "AWS_STORAGE_BUCKET_NAME" { value = aws_s3_bucket.main.bucket }
output "AWS_BUCKET_DOMAIN_NAME" { value = aws_s3_bucket.main.bucket_domain_name }
```

### Provision AWS ECR 
```hcl
# aws/ECR.tf

terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }
  }
}

resource "aws_ecr_repository" "main" {
  name = "example-project-repository"
  force_delete = true
}

output "REPOSITORY_URL" { value = aws_ecr_repository.main.repository_url }


```

### Add them to the main
```hcl
# main.tf

module "aws" {
  source = "./aws"
}

output "aws" {
  value     = module.aws
  sensitive = true
}
```

### Apply our infrastructure
Now as all our resources we need is provisioned, we need to apply them so Terraform could create them on AWS, so run terraform apply.
```shell
terraform apply
```

### Get the output
If everything is working correctly, then our infrastructure is set up, and now we could get the output, so we could use those values when we deploy our code.
```shell
terraform output aws
```

## Deploy
You could actually deploy manually and automatically using any CI/CD tool and I did both. For instructions on how to do that you could read [this section](https://github.com/mustafaelghrib/crewtech#deployment) from the README file of this project.

# Conclusion
AWS is a cloud provider and become an industry trend and many organizations may use AWS to build their cloud infrastructure so knowing how to build it is a great, but you should follow best practices and defined steps, so you could produce high quality work. I hope this article has been helpful for you, and feel free to follow me for more articles like this or ask me anything related on my [LinkedIn](https://linkedin.com/in/mustafaelghrib). 
