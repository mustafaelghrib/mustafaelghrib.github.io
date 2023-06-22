---
title: "Terraform and Microservices"
date: 2023-05-11T10:41:47+03:00
author: Mustafa El-Ghrib
location: Alexandria, Egypt
---

## Introduction
When I was [improving my skills](https://www.linkedin.com/posts/mustafaabdulluh_i-recently-improved-my-skills-in-microservices-activity-7050936883920388096-_n3h) 
and learning about Microservices, Cloud, and DevOps, I faced a problem when migrating from Monolithic 
Architecture to Microservices Architecture, specifically in the part where I had to provision the 
cloud infrastructure with Terraform. The issue was how to write the cloud infrastructure successfully 
for each service in our microservices program without duplicating code and ensuring that each service's 
infrastructure was standalone, without affecting other services. This was challenging because 
microservices follow the Single Responsibility Principle. Today, I am writing this article to 
share how I solved this issue for anyone who may wonder how to use Terraform with Microservices.

## What is Terraform?
Terraform is an infrastructure-as-code (IaC) tool that allows us to provision cloud infrastructure 
using code instead of relying on manual configuration through the cloud console. This can be very 
convenient, especially if you have a coding background. To learn more about Terraform, you can 
visit their [website](https://www.terraform.io).

## What is Microservices?
Microservices is a backend architecture that follows the Single Responsibility Principle. 
It involves breaking down a monolithic application into small services, each of which performs 
a specific function and communicates with each other via APIs and messaging services such as 
RabbitMQ. The benefits of microservices include the ability to use different programming languages 
to build these services and the flexibility of having different teams with different backgrounds 
and technical skills work on them. However, implementing and debugging microservices can be 
complex due to its distributed nature.

## Terraform and Monolith
When it comes to regular backend architecture or monolithic architecture, using Terraform 
is relatively straightforward. We can write the infrastructure code for one backend API in 
a single file or use modules if we have a more complex infrastructure. Ultimately, we're 
dealing with just one thing, so it's much simpler compared to implementing microservices architecture.

For example, in my latest project, I built a [PDF-to-HTML document converter](https://github.com/mstva/docorvter) 
using a monolithic architecture. I deployed this backend API on AWS infrastructure using 
Terraform, and I only needed to use AWS S3 and AWS RDS. As a result, my Terraform folder 
was quite simple, as shown below:

```markdown
├── aws.tf
├── main.tf
└── modules
    └── aws
        ├── RDS.tf
        ├── S3.tf
        └── main.tf
```

## Terraform and Microservices
On the other hand, when we divide a monolithic service into many microservices, 
each service has its own infrastructure. This can make things more complicated, 
especially if we try to do it the old-fashioned way, which can lead to code 
duplication and difficulties in managing each service's infrastructure.

The solution to this problem was to use modules to make each service standalone. First, 
we created a module for the common cloud infrastructure and then created a separate module 
for each service that used this common module. With this approach, we were able to avoid code 
duplication and manage services more easily.

For example, in my latest project, I built a [form builder](https://github.com/mstva/fuilder) 
using microservices architecture, and I needed to deploy the infrastructure on Google Cloud 
Platform (GCP). I had three services, each of which needed a Google Storage Bucket and a 
Google SQL Database. To accomplish this, I created modules for GCP to provision these 
common GCP services, and then created a separate module for each service under the 
services' folder. These modules used the common GCP modules, as shown below:

```markdown
├── README.md
├── main.tf
└── modules
    ├── gcp
    │   ├── sql_database
    │   │   └── main.tf
    │   └── storage_bucket
    │       └── main.tf
    └── services
        ├── form_builder_service
        │   └── main.tf
        ├── forms_service
        │   └── main.tf
        └── users_service
            └── main.tf
```

## Conclusion
Overall, Terraform is a great tool, and when it comes to microservices, it can become more complicated. 
However, with Terraform modules, things become easier to implement. I hope this article has been 
helpful for you, and feel free to follow me for more articles like this or ask me anything related on 
my [LinkedIn](https://linkedin.com/in/mustafaabdulluh).
