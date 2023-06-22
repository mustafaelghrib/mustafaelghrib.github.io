---
title: "How to Build a Cloud Infrastructure"
date: 2023-06-16T10:56:05+03:00
author: Mustafa Abdallah
location: Alexandria, Egypt
---

# Introduction
With the age of cloud computing and public clouds like AWS, GCP, and Azure, building a cloud infrastructure has become a regular practice in software development. However, doing it correctly with defined steps is what will enable you to produce high-quality and efficient work. Today, I am writing about those steps that help us build an efficient cloud infrastructure.

# Table of Contents
- [What is Cloud Infrastructure?](#what-is-cloud-infrastructure)
- [Steps of Building a Cloud Infrastructure](#steps-of-building-a-cloud-infrastructure)
    - [Define](#define)
    - [Choose](#choose)
    - [Plan](#plan)
    - [Design](#design)
    - [Implement](#implement)
    - [Deploy](#deploy)
    - [Monitor](#monitor)
    - [Maintain](#maintain)
- [What is Next in This Series?](#what-is-next-in-this-series)
- [Conclusion](#conclusion)

---

# What is Cloud Infrastructure?
It's basically the resources that your app needs to run your code on the internet. These resources are provided as services by cloud computing providers like AWS, GCP, or Azure. So, to make your app work, you need to build a cloud infrastructure.

# Steps of Building a Cloud Infrastructure
These steps define what we should follow when building cloud infrastructure, and they are applicable to any cloud provider. Here is a detailed overview of each one:

## Define
You first need to define your requirements and goals, so that you can have an outline of what you want to achieve. You should study your application and decide how you want to deploy it. There are various deployment methods, some of which I discussed in previous sections of [this article](https://mstva.github.io/methods_of_deployment_with_examples/). After choosing the deployment method, you will need to determine the cloud services you will require.

For example, if you have a Python backend API built with Django and you have chosen to deploy it using Docker on a remote server, you will need to consider services such as:

- For the remote server, you will need a virtual machine on the cloud. This can be achieved using services like AWS EC2, Google Compute Engine, or Azure Virtual Machine.
- For Docker, you will require a Docker registry where you can push and pull your Docker image. Services such as Docker Hub, AWS ECR, or Azure ACR can fulfill this need.
- For Django, you will need database and storage services. Services like AWS RDS, Google SQL, or Azure Database can provide the necessary database services, while storage needs can be met using services like AWS S3, Google Storage, or Azure Blob Storage.

So far, you have defined your deployment method and identified the cloud services you may require. The next step is to choose the cloud provider.

## Choose
After you define your requirements and goals, you have to choose one of the public cloud providers that satisfies your requirements and fits your budget. While each provider may follow the pay-per-use payment strategy, the prices of services can vary, with some being more expensive than others. You can also benefit from using multiple or hybrid clouds, leveraging the strengths of different providers. The crucial step is to select a cloud provider that allows you to provision the services you need for your application.

The three current popular public cloud providers are AWS, GCP, and Azure. While the underlying concept of their services is the same, their configurations may differ. For instance, if you need a remote server, you can find it on AWS as part of the AWS EC2 service, on GCP as Google Compute Engine, and on Azure as Azure Virtual Machine. These providers offer similar functionality with slight variations.

For our earlier example of the Python Backend API discussed in the previous step, based on our requirements, we could choose AWS as it provides all the necessary services, and the pay-per-use payment model aligns well with our needs, allowing us to pay only for our usage.

It's worth noting that all of these cloud providers offer a free tier, which you can take advantage of if you're unsure about which one to choose. Visit their respective websites for more information.

## Plan
After defining the desired services and selecting the cloud provider, the next step is to plan the cloud infrastructure. This involves choosing the specific cloud services that align with our needs from the chosen provider and configuring them accordingly.

For instance, in the case of a backend API, we require a remote server, a Docker registry, a database, and storage. Since we have chosen AWS as our cloud provider, let's explore the AWS services that meet our requirements.

- Remote Server: AWS provides the AWS EC2 service, which allows us to configure an Ubuntu server to host our code. We can choose Ubuntu as the operating system, t2.micro as the CPU configuration, and allocate 20GB of general-purpose SSD for the server volume storage.

- Docker Registry: AWS offers the AWS ECR service, which enables us to push and pull Docker images. We have the option to choose between a public or private repository for our Docker registry.

- Database: AWS provides the AWS RDS service, allowing us to provision a managed PostgreSQL database. We can configure the database engine as PostgreSQL, choose the db.t3.micro instance type for CPU configuration, and allocate 20GB of storage for the managed database.

- Storage: AWS offers the AWS S3 service, which enables us to store static and media files. We can configure the storage capacity and choose whether to have public or private access to the stored data.

## Design
After planning, we need a visual representation to provide an overview of our cloud infrastructure's big picture. This is crucial as it allows for changes to be made before implementation. Designing our solution based on the chosen cloud provider demonstrates how the services are interconnected and how the workflow of our cloud infrastructure will function. This step requires additional work and must take networking and security into consideration. Frameworks like [AWS Well-Architected](https://aws.amazon.com/architecture/well-architected) can assist in effectively accomplishing this task, but they are beyond the scope of this article.

For example, in the case of our backend API, we can design and create a diagram of our cloud infrastructure using a tool like [draw.io](https://app.diagrams.net/?splash=0&libs=aws4). As seen in the picture below, our infrastructure is relatively simple. Users access the IP address of the AWS EC2 instance, which runs a Docker container using an image retrieved from AWS ECR. Our Docker image, stored and pushed to AWS ECR, connects to AWS S3 and AWS RDS, allowing our code to utilize these services.

{{< figure src="/images/cloud_infrastructure_diagram.png" width="100%" >}}

## Implement
All the previous steps are just the basic blocks that we need to complete before proceeding further. These steps ensure that we approach the tasks correctly. After completing these initial steps, we can move on to implementing our solution and provisioning the necessary services on the chosen cloud provider. For AWS, we have several options such as using the console dashboard, the AWS CLI, or utilizing Infrastructure as Code tools like AWS CloudFormation or Terraform.

Based on the planning stage, we have identified the required services and their configurations. Now, we can proceed with their implementation. The design we created earlier helps us understand the relationships between services and how the workflow will function in the cloud environment. For instance, you might consider aspects like Virtual Private Cloud (VPC) and security groups for the EC2 instance. However, for simplicity, let's focus on keeping things straightforward for now.

## Deploy
Is everything set up correctly? Great! Now, let's utilize the configured services to deploy our code. In the case of our backend API, our code is containerized in a Docker image. We will push this image to ECR and update our code to utilize AWS RDS for the database and AWS S3 for storage. By using SSH to access our server, we can pull the Docker image and create a container to run our backend API. To access the API, we simply visit the IP address of the EC2 instance.

For deployment, we have the option to do it manually or leverage a CI/CD tool such as GitHub Actions or Jenkins for automated deployments. Both methods are effective, and we can even combine them. Initially, performing a manual deployment ensures that everything is functioning correctly. Then, we can proceed to write a CI/CD pipeline that automates the steps performed during manual deployment.

## Monitor
We may need to set up monitoring tools so that you can gain an overview of your code and the performance of your cloud infrastructure. This will be beneficial if you aim to make improvements and require data-driven insights to make informed decisions.

## Maintain
You will need to do this because cloud providers frequently update and remove features from their services. Therefore, it is important to regularly maintain your cloud infrastructure to ensure its continued functionality.

# What is Next in This Series?
I will continue and write other articles about how to build cloud infrastructure on AWS, Azure, GCP, and Digital Ocean. I will use real-world examples and projects, following the steps mentioned above, and providing a more detailed approach so that you can learn how to build your own infrastructure.

# Conclusion
Cloud computing has changed the game of software development, and companies are moving towards adapting it in their organizations. Knowing how to use it effectively will help you create efficient solutions. Therefore, relying solely on the cloud provider is not enough. You should have a detailed plan and defined steps to follow when building cloud infrastructure solutions for your applications. I hope this article has been helpful for you, and feel free to follow me for more articles like this or ask me anything related on my [LinkedIn](https://linkedin.com/in/mustafaabdulluh). 
