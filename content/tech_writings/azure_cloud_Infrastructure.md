---
title: "Azure Cloud Infrastructure: Step-by-Step Guide"
date: 2023-07-11T17:06:19+03:00
author: Mustafa Elghrib
location: Minya, Egypt
---

# Introduction
After I wrote about [how to build a cloud infrastructure](https://mustafaelghrib.github.io/how_to_build_cloud_infrastructure/), I decided to implement real-world projects to demonstrate how you could build your own and follow best practices. Today, I am writing about how to build a cloud infrastructure for a backend API on Microsoft Azure.

{{< figure src="/posts/azure_cloud_infrastructure.png" width="100%" >}}

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
The project we will work on is a backend API built with Python, Flask, and PostgreSQL, and containerized with Docker. You can find it [in this repository](https://github.com/mustafaelghrib/kodeec/tree/main/backend), clone it to your local computer, and let's begin building.

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
For this article, we have chosen Microsoft Azure as the cloud platform to deploy our backend. You will need to create an Azure account as we will be using it during the implementation step.

## Plan
Let's align the services we require with the services provided by Azure and document the initial configuration we need for each of those services.

| Our Service          | Azure Service                     | Configration                           |
|----------------------|-----------------------------------|----------------------------------------|
| Docker Registry      | Azure Container Registry (ACR)    | Tier or SKU: Basic                     |
| Remote Server        | Azure Virtual Machine             | OS: Ubuntu, Size: DS1 v2, Storage: LRS |
| PostgresSQL Database | Azure Database PostgreSQL Server  | SKU: B_Gen5_2, Storage: 5120           |
| Storage              | Azure Blob Storage                | Tier: Standard, Type: LRS              |

## Design
Our design is intentionally kept simple for now, without considering networking or security aspects, to avoid unnecessary complexity. Let's focus on the services we currently need.

The design, as shown below, involves users accessing the IP address of the Azure Virtual Machine instance. The Azure Virtual Machine instance runs a Docker container from the Docker image pulled from the Azure Container Registry repository. This Docker image contains our code, which is configured to access the PostgreSQL database in Azure Database PostgreSQL Server and the storage in Azure Blob Storage .

{{< figure src="/images/azure_infra_diagram.png" width="100%" >}}

I used [draw.io](https://draw.io) to do this diagram.

## Implement
We have multiple options for implementing this cloud infrastructure on Azure, all of which are effective. However, the best approach will depend on your specific requirements and the complexity of your project.

- **Microsoft Azure Portal**: The web interface provided by Azure, where you can manually create the resources one by one through their portal.
- **Azure CLI**: The Azure Command Line Interface allows you to create resources using the command line in a terminal.
- **Azure Resource Manager**: Azure Resource Manager is an Infrastructure as Code (IaC) tool provided by Azure, where you can provision services using JSON.
- **Terraform**: Terraform is an IaC tool provided by HashiCorp. It has an Azure provider that allows you to provision Azure resources.

In this article, we will use Terraform to create the Azure resources. Make sure to install it.

### The infrastructure folder
Let's create a new folder named **infrastructure** in the root directory of our project. Here is what it will contain:
```shell
├── secrets.auto.tfvars              # contains secrets, make sure to ignore it
├── main.tf
├── azure                            # azure module that contains azure resources
│   ├── main.tf
│   ├── blob_storage.tf
│   ├── container_registry.tf
│   ├── postgres_database.tf
│   ├── virtual_machine.tf
├── ssh                               # bring your ssh keys, make sure you ignore them
│   ├── id_rsa.pub
│   ├── id_rsa
└── scripts
    └── install_docker.sh
```

### Set up Azure provider on Terraform
We need to configure the Azure provider so that we can use it in Terraform. In the **main.tf** file, we will add the following configuration:
```hcl
# main.tf

terraform {
  required_version = "1.3.1"
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "3.30.0"
    }
  }
}

provider "azurerm" {
  features {}
  subscription_id = var.azure.subscription_id
  tenant_id       = var.azure.tenant_id
  client_id       = var.azure.client_id
  client_secret   = var.azure.client_secret
}
```

To securely store Azure credentials, we will create a file named **secrets.auto.tfvars**. Please ensure that this file is ignored and not committed to version control. In the **secrets.auto.tfvars** file, we will add the following content:
```hcl
# secrets.auto.tfvars

azure = {
  subscription_id = ""
  tenant_id       = "" 
  client_id       = ""
  client_secret   = ""
}
```

Run the following command to initialize Terraform and ensure that everything is working correctly:
```shell
terraform init
```

We will need to create a resource group, so it hold all our resources that we create, so in the **azure/main.tf** file add those:
```hcl
# azure/main.tf

terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
    }
  }
}

resource "azurerm_resource_group" "main" {
  name     = "example-project-resource-group"
  location = "East US"
}
```

### Provision Azure Virtual Machine
```hcl
# azure/virtual_machine.tf

resource "azurerm_linux_virtual_machine" "main" {
  name                            = "example-project-machine"
  computer_name                   = "example-project-machine"
  admin_username                  = "example-project-user"
  size                            = "Standard_DS1_v2"
  resource_group_name             = azurerm_resource_group.main.name
  location                        = azurerm_resource_group.main.location
  network_interface_ids           = [azurerm_network_interface.main.id]
  disable_password_authentication = true

  admin_ssh_key {
    username   = "example-project-user"
    public_key = file("${path.module}/ssh/id_rsa.pub")
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-focal"
    sku       = "20_04-lts-gen2"
    version   = "latest"
  }

  provisioner "remote-exec" {
    connection {
      type        = "ssh"
      user        = "example-project-user"
      host        = self.public_ip_address
      private_key = file("${path.module}/ssh/id_rsa")
    }
    scripts = ["${path.module}/scripts/install_docker.sh"]
  }

}

output "MACHINE_SSH_CONNECT" { 
      value = "ssh example-project-user@${azurerm_linux_virtual_machine.main.public_ip_address}" 
}
output "MACHINE_USER" { value = "example-project-user" }
output "MACHINE_IP" { value = azurerm_linux_virtual_machine.main.public_ip_address }
```

### Provision Azure Database PostgreSQL Server
```hcl
# azure/postgres_database.tf

resource "azurerm_postgresql_server" "main" {
  name                = "example-project-database-server"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location

  administrator_login          = "example-project-db-user"
  administrator_login_password = var.database_server_password

  sku_name   = "B_Gen5_2"
  storage_mb = "5120"
  version    = "11"

  ssl_enforcement_enabled          = true
  ssl_minimal_tls_version_enforced = "TLS1_2"
}

resource "azurerm_postgresql_database" "main" {
  name                = "example-project-db"
  resource_group_name = azurerm_resource_group.main.name
  server_name         = azurerm_postgresql_server.main.name
  charset             = "UTF8"
  collation           = "English_United States.1252"
}

output "POSTGRES_DB" { value = "example-project-db" }
output "POSTGRES_USER" { value = "example-project-db-user@${azurerm_postgresql_server.main.fqdn}" }
output "POSTGRES_PASSWORD" { value = var.database_server_password }
output "POSTGRES_HOST" { value = azurerm_postgresql_server.main.fqdn }
output "POSTGRES_PORT" { value = "5432" }
```

### Provision Azure Blob Storage 
```hcl
# azure/blob_storage.tf

resource "azurerm_storage_account" "main" {
  name                     = "example-project"
  account_tier             = "Standard"
  account_replication_type = "LRS"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
}

resource "azurerm_storage_container" "main" {
  name                  = "example-project-container"
  container_access_type = "container"
  storage_account_name  = azurerm_storage_account.main.name
}

output "AZURE_ACCOUNT_NAME" { value = "example-project" }
output "AZURE_ACCOUNT_KEY" { value = azurerm_storage_account.main.primary_access_key }
output "AZURE_CUSTOM_DOMAIN" { value = azurerm_storage_account.main.primary_blob_host }
output "AZURE_CONTAINER" { value = "example-project-container" }
```

### Provision Azure Container Registry
```hcl
# azure/container_registry.tf

resource "azurerm_container_registry" "main" {
  name                = "example-project-registry"
  sku                 = "Basic"
  admin_enabled       = true
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
}

output "ACR_URL" { value = azurerm_container_registry.main.login_server }
output "ACR_USERNAME" { value = azurerm_container_registry.main.admin_username }
output "ACR_PASSWORD" { value = azurerm_container_registry.main.admin_password }

```

### Add them to the main
```hcl
# main.tf

module "azure" {
  source = "./azure"
}

output "azure" {
  value     = module.azure
  sensitive = true
}
```

### Apply our infrastructure
Now that all the necessary resources are provisioned, we can apply them using Terraform to create them on Azure.
```shell
terraform apply
```

### Get the output
If everything is working correctly, your infrastructure should be set up. You can now retrieve the output values from Terraform, which can be used when deploying your code.
```shell
terraform output azure
```

## Deploy
You can deploy the project manually or automatically using any CI/CD tool. For instructions on how to do this, please refer to [this section](https://github.com/mustafaelghrib/kodeec#deployment) in this project's README file.

# Conclusion
Azure is a popular cloud provider and an industry trend. Many organizations utilize Azure to build their cloud infrastructure. Knowing how to build on Azure is valuable, but it's crucial to follow best practices and defined steps to ensure high-quality work. I hope this article has been helpful for you, and feel free to follow me for more articles like this or ask me anything related on my [LinkedIn](https://linkedin.com/in/mustafaelghrib). 
