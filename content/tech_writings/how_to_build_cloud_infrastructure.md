---
title: "How to Build Cloud Infrastructure"
date: 2023-06-08T10:56:05+03:00
author: Mustafa Abdallah
location: Alexandria, Egypt
---

# Introduction
With the age of cloud computing and public cloud like AWS, GCP, and Azure, building a cloud infrastructure become a regular thing that we do in software development, but doing it right with defined steps and framework is what will make you produce high-quality and efficient work. Today I am writing about those steps that make us build an efficient cloud infrastructure.

## Table of Contents:
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

# Steps of Building a Cloud Infrastructure
These steps define what we should follow when building cloud infrastructure, and it's applicable on any cloud provider, here is a detailed overview about each one:

## Define
You first need to define your requirements and goals, so you could have an outline about what you want to achieve, you should study your application and decide how you want to deploy it, there are a lot of deployments methods and I wrote about some of them before [in this article](). after choosing the deployment method you will need to know the services that you will need in the cloud.

For example, if you have a Python Backend API built with Django and you chose to deploy it using Docker on a remote server, you will need to consider services like that:
- For remote server, you will need a virtual machine on the cloud and that could be done using something like AWS EC2, Google Compute Engine or Azure Virtual Machine
- For Docker, you will need a docker registry that you will push and pull your docker image form it, and that could be done using services like Docker Hub, AWS ECR, or Azure ACR
- For the Django you will need a database and storage services, and that could be done using services like AWS RDS, Google SQL, or Azure Database for database, and for storage you could use services like AWS S3, Google Storage, or Azure Blob Storage

So up till now you defined your deployment method and the services you may need from the cloud, next step is to choose the cloud provider.

## Choose
After you define your requirements and goals, you have to choose one of the public cloud providers that satisfy your requirements and your budget, even each one may follow the pay per use payment strategy, the prices of services may be expansive in one and cheaper in others, you could actually benefit from them all together in something called multi or hybrid cloud, the most important thing is to choose a cloud provider, so you could provision the services you want for your application on it. 

The three public and popular cloud providers right now are AWS, GCP, and Azure, all of them provide services that are the same in the concept and different in the configration, for example if you want a remote server you could find it on AWS under AWS EC2 service, on GCP under Google Compute Engine, and on Azure under Azure Virtual Machine, all of them will give you the same remote server.

For example, for our previous Python Backend API that we talked about it in the define step above, based on the requirements we could choose AWS as it provide services for all what we want and the pay per use payment model could be good for us, so we could pay only for our use.

All of the above cloud providers provide free tier that you could benefit from it if you are not sure what to choose, check those cloud providers websites to know more.

## Plan
So after we defined the services that we want, and chose the cloud provider, we need to plan our cloud infrastructure, so we just need to choose the cloud services that match our needs from the cloud provider that we chose and select the configuration for them.

For example, for backend api, we need a remote server, docker registry, database and storage. we have chose AWS as our cloud provider so let's see what AWS services that match our requirements

- Remote Server: AWS provide AWS EC2 service that help us configure ubuntu server to host our code, and the configuration we could choose Ubuntu for the OS, t2.micro for the CPU, and 20G general purpose SSD for the server volume storage size and type.
- Doker Registry: AWS provide AWS ECR service that let us push and pull docker images, and the configuration we could choose public or private repository.
- Database: AWS provide AWS RDS service that let us provision PostgresSQl managed database, and the configuration we could choose Postgres for the database engine, db.t3.micro for the database CPU and 20G for storage of the managed database.
- Storage: AWS provide AWS S3 service that let us store our static and media files, and the configuration we could choose how much storage and what type of access to it, between public or private.

## Design
After planning, we need something visually so we could have an overview of the big picture of our cloud infrastructure, and it's the best way to make changes before implementation, what we actually need is to design our solution based on the cloud provider we chose, this will show how those services connected together and how the workflow of our cloud infrastructure will work, this step actually need more work and taking in consideration networking and security, and there a frameworks for helping in doing it effectively like [AWS Well-Architected](https://aws.amazon.com/architecture/well-architected) that are beyond the scope of this article.

For example, for our backend API, we may design and draw a diagram for our cloud infrastructure using a tool like [draw.io](https://app.diagrams.net/?splash=0&libs=aws4), as you see in the picture below, our infrastructure is very simple, the user go to the IP address of the AWS EC2 instance, the instance is actually running a docker container from our image that are pulled from AWS ECR, and our docker image that pushed and saved in the AWS ECR will access the AWS S3 and AWS RDS for our code to use them.

{{< figure src="/images/cloud_infrastructure_diagram.png" width="100%" >}}

## Implement
All the previous steps is just the basic blocks that we need to do before touching anything, that will help us do the things correctly, after that we need to implement our solution and provisioning this services on the cloud provider, for AWS we could use the console dashboard, the AWS CLI, or Infrastructure as code tool like AWS CloudFormation or Terraform.

Based on the planning step we will have the services and what configurations for them, and we could go and do them, and based on the design we could know the relationship between services and how the workflow will behave on the cloud, you may consider something like VPC and security groups for the EC2 instance, but for now let's keep things simple.

## Deploy
So everything is setup correctly? right let's use them to deploy our code, so for our backend api, our code is containerized in docker image, we will push the image to ECR and update our code to use the AWS RDS and AWS S3 for the database and storage, and by ssh to our server we could pull the docker image and make a container from it so our backend api could run on the server, and we visit it through the IP address of the EC2 instance.

For deployment, we could actually do that manually, or use a CI/CD tool like GitHub Actions or Jenkins to do them automatically, both ways works, and we could do them together, first do manual deployment to ensure that everything is working correctly, and then write a CI/CD pipeline to do the steps you did automatically.

## Monitor
We may need to setup monitoring tools, so you could have an overview about your code and cloud infrastructure performance in case you want to improve them and has some data to make decisions.

## Maintain
You will need this as this cloud provider frequently update and remove stuff from their services, so you will need to keep your cloud infrastructure working by maintaining it regularly.

# What is Next in This Series?
I will continue and write other articles about how to build cloud infrastructure on AWS, Azure, GCP and Digital Ocean. I will use real world examples and projects and I will follow the steps above and write more detailed approach, so you could know how to build your own.

# Conclusion
Cloud computing has changed the game of software development, and companies goes toward adapting them in their organization, so knowing how to use them effectively will help you create efficient solutions, so going to the cloud provider directly is not enough, you should have a detailed plan and defined steps to follow to build cloud infrastructure solutions for your applications.  I hope this article has been helpful for you, and feel free to follow me for more articles like this or ask me anything related on my [LinkedIn](https://linkedin.com/in/mustafaabdulluh). 
