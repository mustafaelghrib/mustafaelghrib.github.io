---
title: "AWS Cloud Infrastructure: Step-by-Step Guide"
date: 2023-07-10T11:07:56+03:00
author: Mustafa Elghrib
location: Minya, Egypt
---

# Introduction
After I wrote about [how to build a cloud infrastructure](https://mustafaelghrib.github.io/how_to_build_cloud_infrastructure/), I decided to implement real-world projects to demonstrate how you could build your own and follow best practices. Today, I am writing about how to build a cloud infrastructure for a backend API on AWS.

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
The project we will work on is a backend API built with Python, Django, and PostgreSQL, and containerized with Docker. You can find it [in this repository](https://github.com/mustafaelghrib/crewtech/tree/main/backend), clone it to your local computer, and let's begin building.

Note: This project could be built with any tech stack, but the steps will remain the same.

# Building The Cloud Infrastructure
So, we have the project set up and ready to go for production. Now, our goal is to deploy it to the cloud. To achieve that, we need to build the cloud infrastructure. Let's follow these steps to build it.

## Define
Our goal here is to deploy this simple backend API, making it available on the internet for others to access.

To accomplish this, we need to choose a deployment method. I have written about some of them [in this article](https://mustafaelghrib.github.io/methods_of_deployment_with_examples/), but let's assume we want to deploy it using Docker on a remote server.

This outlines our requirements from the cloud, and based on the information above, we need:

- Docker registry to push and pull Docker images.
- Remote server to host our backend API.
- PostgreSQL database for our backend API.
- Storage for saving and retrieving static and media files used by our backend API.

## Choose
For this article, we have chosen AWS as the cloud platform to deploy our backend. You will need to create an AWS account as we will be using it during the implementation step.

## Plan
Let's align the services we require with the services provided by AWS and document the initial configuration we need for each of those services.

| Our Service          | AWS Service | Configration                                    |
|----------------------|-------------|-------------------------------------------------|
| Docker Registry      | AWS ECR     | Repository Type: Public                         |
| Remote Server        | AWS EC2     | OS: Ubuntu, CPU: t2.micro, Volume: 20G          |
| PostgresSQL Database | AWS RDS     | Engine: Postgres, CPU: db.t3.micro, Volume: 20G |
| Storage              | AWS S3      | Access Type: Public                             |

## Design
Our design is intentionally kept simple for now, without considering networking or security aspects, to avoid unnecessary complexity. Let's focus on the services we currently need.

The design, as shown below, involves users accessing the IP address of the AWS EC2 instance. The EC2 instance runs a Docker container from the Docker image pulled from the AWS ECR repository. This Docker image contains our code, which is configured to access the PostgreSQL database in AWS RDS and the storage in AWS S3.

{{< figure src="/images/cloud_infrastructure_diagram.png" width="100%" >}}

I used [draw.io](https://app.diagrams.net/?splash=0&libs=aws4) to do this diagram.

## Implement
We have multiple options for implementing this cloud infrastructure on AWS, all of which are effective. However, the best approach will depend on your specific requirements and the complexity of your project.

- **AWS Management Console**: The web interface provided by AWS, where you can manually create the resources one by one through their dashboard.
- **AWS CLI**: The AWS Command Line Interface allows you to create resources using the command line in a terminal.
- **AWS CloudFormation**: AWS CloudFormation is an Infrastructure as Code (IaC) tool provided by AWS, where you can provision services using YAML.
- **AWS CDK**: AWS CDK is another IaC tool by AWS that enables you to provision services using programming languages like Python, TypeScript, or Java.
- **Terraform**: Terraform is an IaC tool provided by HashiCorp. It has an AWS provider that allows you to provision AWS resources.

In this article, we will use Terraform to create the AWS resources. Make sure to install it.

### The infrastructure folder
Let's create a new folder named **infrastructure** in the root directory of our project. Here is what it will contain:
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
We need to configure the AWS provider so that we can use it in Terraform. In the **main.tf** file, we will add the following configuration:
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

To securely store AWS credentials, we will create a file named **secrets.auto.tfvars**. Please ensure that this file is ignored and not committed to version control. In the **secrets.auto.tfvars** file, we will add the following content:
```hcl
aws = {
  region     = "" # your aws region
  access_key = "" # your aws access key
  secret_key = "" # your aws secert key
} 
```

Run the following command to initialize Terraform and ensure that everything is working correctly:
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
Now that all the necessary resources are provisioned, we can apply them using Terraform to create them on AWS.
```shell
terraform apply
```

### Get the output
If everything is working correctly, your infrastructure should be set up. You can now retrieve the output values from Terraform, which can be used when deploying your code.
```shell
terraform output aws
```

## Deploy
You can deploy the project manually or automatically using any CI/CD tool. For instructions on how to do this, please refer to [this section](https://github.com/mustafaelghrib/crewtech#deployment) in this project's README file.

# Conclusion
AWS is a popular cloud provider and an industry trend. Many organizations utilize AWS to build their cloud infrastructure. Knowing how to build on AWS is valuable, but it's crucial to follow best practices and defined steps to ensure high-quality work. I hope this article has been helpful for you, and feel free to follow me for more articles like this or ask me anything related on my [LinkedIn](https://linkedin.com/in/mustafaelghrib). 
