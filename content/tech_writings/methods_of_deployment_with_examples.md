---
title: "Methods of Deployment with Examples"
date: 2023-05-12T10:43:29+03:00
author: Mustafa Abdallah
location: Alexandria, Egypt
---

## Table of Contents:
- [Introduction]({{< ref "#introduction" >}})
- [Deploy using Nginx]({{< ref "#deploy-using-nginx" >}})
    - [What is Nginx?]({{< ref "#what-is-nginx" >}})
    - [Deploying Manually]({{< ref "#deploying-manually" >}})
    - [Deploying Automatically With Linux Bash Script]({{< ref "#deploying-automatically-with-linux-bash-script" >}})
- [Deploy using Docker]({{< ref "#deploy-using-docker" >}})
    - [What is Docker?]({{< ref "#what-is-docker" >}})
    - [How to deploy Docker Image?]({{< ref "#how-to-deploy-docker-image" >}})
- [Deploy using Kubernetes]({{< ref "#deploy-using-kubernetes" >}})
    - [What is Kubernetes?]({{< ref "#what-is-kubernetes" >}})
    - [How to deploy to Kubernetes?]({{< ref "#how-to-deploy-to-kubernetes" >}})
- [Conclusion]({{< ref "#conclusion" >}})
---

## Introduction
Deployment is an important step in the software development lifecycle, as it involves shipping our code to the outside world. Making the process smooth, seamless, and error-free is crucial for tech people. During my journey as a Software Engineer, I've worked on various deployment methods - from the basic to the most modern and advanced ones. Today, I am writing about these different ways of deployment and how to implement them.

## Deploy using Nginx

### What is Nginx?
Nginx is a web server that searches for our code in a specific folder and executes the command to run it, before serving it to the internet through port 80.

### Deploying Manually
To deploy our code, we need a VPS or virtual machine, which are available on many cloud platforms such as AWS EC2, Azure Virtual Machine, Google Cloud Compute Engine, or Digital Ocean Droplet. These platforms provide a fresh Ubuntu server that can be customized as needed. In our case, we will need to install Nginx and any required packages for our code. If our code requires a database, we can also set it up on the same server and connect it to our code.

For instance, I recently worked on a project that involved building a backend API using Django and PostgresSQL as the database. To deploy the API, I first set up an Ubuntu server on a Digital Ocean Droplet, installed Nginx, required Python packages, and PostgresSQL. Then, I uploaded my code to the server using Git, configured PostgresSQL and Nginx, and voilà! Our backend API was live and accessible via the Digital Ocean Droplet IP address. For more information and a step-by-step guide, I highly recommend reading [this Digital Ocean tutorial](https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-22-04), as it was my reference for all similar deployments.

### Deploying Automatically With Linux Bash Script
All of the above is great, but when entering the world of automation, we want everything to be automated! That's exactly what I did when I built a new project. Instead of following a step-by-step guide every time, I wrote a Linux Bash script to automate these steps for me!

[My project](https://github.com/mstva/scraple) was a backend API built with Django and PostgresSQL, and I needed to deploy it with Nginx on Google Cloud Compute Engine. I set up an instance using Terraform [as shown here](https://github.com/mstva/scraple/blob/main/infrastructure/main.tf), and then wrote a Linux Bash script that automates all the necessary steps, [as shown here](https://github.com/mstva/scraple/tree/main/infrastructure/scripts). I attached the script to the instance so that when it launches, it runs the script and sets everything up for me automatically.

## Deploy using Docker

### What is Docker?
Docker is a containerization technology that allows us to package our code and required packages into an image called a Docker image. We can then launch containers from this image as many times as we want.

### How to deploy Docker Image?
There are many ways to deploy Docker images, but in this section, I will talk about deploying a Docker image to a virtual machine. As mentioned above, we could launch an Ubuntu server or virtual machine on any public cloud provider like AWS, GCP, or Azure. What we care about is that we need to install Docker on this Ubuntu server, then we push our Docker image to the Docker registry. After that, we pull the Docker image on our server and run some scripts to create a container from it, which makes our code accessible via port 80. Then, we can access the IP address of the server and see our code served.

For example, when I was developing the [CrewTech](https://github.com/mstva/crewtech) project, I needed to deploy it on AWS EC2 using Docker. First, I built and packaged a Docker image from the code and then pushed it to AWS ECR. After that, I set up AWS EC2 to install Docker once it launched using Terraform. Finally, I just ran a script that pulled this Docker image and created a container from it that was accessible via port 80. This made our backend API or Docker image accessible via the AWS EC2 IP address.

This process could be done manually, [as I did here](https://github.com/mstva/crewtech#deploy-manually), or by writing a CI/CD pipeline that does all of these steps automatically every time we push new code, [as I did here](https://github.com/mstva/crewtech/blob/main/.circleci/config.yml) with CircleCI.

## Deploy using Kubernetes

### What is Kubernetes?
Kubernetes is an orchestration tool that manages multiple containers, and it is very handy when it comes to microservices because each service has its own Docker image, and the number of containers can become larger as the number of microservices increases.

### How to deploy to Kubernetes?
Kubernetes requires that the Docker image be pushed to any Docker registry, and it takes care of all the other parts. While this may seem simple, the Kubernetes system is more complex. Kubernetes is availabe on my cloud providers as a manged service like AWS EKS, Azure AKS, and GCP GKE. All of them gives you the same Kubernetes instance.

For example, when I built [Docorvter](https://github.com/mstva/docorvter), which is a PDF-to-HTML document converter, I needed to deploy it on AWS using Kubernetes. After building and pushing the Docker image of the backend, I wrote [deployment and service files](https://github.com/mstva/docorvter/tree/main/kubernetes) that pulled this image. Then, Kubernetes took care of the rest and gave me an accessible IP address that I could visit to see my backend API.

Kubernetes is a very efficient tool when it comes to microservices, as we often have many microservices, each with its own Docker image, and we need a container for each one. As our services grow, things can become complicated, but with Kubernetes, we can manage them easily. For example, in the [Fuilder](https://github.com/mstva/fuilder) project, which is a dynamic form builder, each service has its own Kubernetes deployment and service files that are managed by one Kubernetes instance.

## Conclusion
All methods of deployment work, and any one of them could be ideal for you based on your requirements. I have seen many projects that only require Nginx and an Ubuntu server to be deployed and don't actually need advanced technologies like Docker and Kubernetes. I hope this article has been helpful for you, and feel free to follow me for more articles like this or to ask me anything related on my [LinkedIn](https://linkedin.com/in/mustafaabdulluh).


