
Using Terraform, you'll create a ***database instance*** and deploy it via a ***bastion host***, also known as a ***"jump server"***. This server acts as a *bridge* that connects *authorized users* from the public internet to the *resources hosted within a private network*. 
![[Screenshot 2025-12-12 at 16.54.34.png]]


When an authorized user wants to access the private network, they have to establish an ***SSH (secure shell) connection*** *to the bastion host* to securely access the private resources. Using an SSH key, the external users can prove their identity to connect to the bastion host. Once the users are authenticated, the bastion host can forward their requests to the internal network. 
![[Screenshot 2025-12-12 at 17.03.21.png]]


Here's a diagram of the bastion host architecture that you'll implement in this lab.
![[Screenshot 2025-12-12 at 17.04.06.png]]

It consists of: 
1. An **RDS database instance** 
	- hosted inside **a private subnet** 
		- of a given **VPC**
2. and an **EC2 instance** that acts as the *bastion host*. 
	- hosted inside in a **public subnet** 
		- within the same VPC 
		  so that it can receive external traffic from the *public internet* before **relaying** the secure traffic to the internal database. 

![[Screenshot 2025-12-12 at 17.03.43.png]]


In this lab, the VPC with its private and public subnets are already created and provided for you. 
- You will define them as data blocks within your Terraform configuration files, 
- and then use their information to create the RDS database and the EC2 instances. 

When you create the EC2 instance, you will also generate an SSH key pair, which consists of a **"public key"** that you'll store in the EC2 instance, and a corresponding **"private key"** that you'll save in a separate file. 
- The pair of public and private keys are used to encrypt and decrypt the messages between the *external user* and the *bastion host* **during an SSH connection** in order for the external connection to prove its identity. 

![[Screenshot 2025-12-12 at 17.10.59.png]]
After you create all the resources in Terraform, you can use the private key to connect to the RDS database via an SSH connection through the bastion host. 
![[Screenshot 2025-12-12 at 17.11.25.png]]

However, since SSH is unfortunately not supported in the provided lab environment, it will only be shown the syntax of the SSH connection, which could be used from any external environment. *The main goal of the lab is for you to practice the Terraform syntax to create the resources of the lab.*


To create the database in EC2 instances, you're provided with the Terraform files that you'll need to complete. For that, you'll first need **the IDs of the VPC** and its **subnets** that are provided to you. These resources are created using **CloudFormation** as part of the lab setup. To access them, you can go to the console and look for CloudFormation in the search bar, and then click on the ID of the provided stack. On the right, click on the tab Outputs. 

Here you'll see a list of the resources created using CloudFormation behind the scenes. If you scroll down, you can find the IDs of the VPC and its subnets under the column value.  

---


### 2.2 - Backend

When you apply a Terraform configuration, Terraform generates a `tfstate` file. This file specifies the state of the configuration - showing which resources have been created. You can share the `tfstate` file with teams working on the same collection of resources using the [Terraform Cloud](https://cloud.hashicorp.com/products/terraform "https://cloud.hashicorp.com/products/terraform") service and AWS services, and you can protect it from simultaneous editing.

---
---


Terraform concatinates all config files it sees in a project. We split our config to different files for readability and modularity.

![[Screenshot 2025-12-17 at 15.18.10.png]]

Let's check some of these files:

`providers.tf`

```terraform
# Define the versions and configurations of the providers

terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.39.1"
    }
    local = {
      source  = "hashicorp/local"
      version = "2.4.1"
    }
    random = {
      source  = "hashicorp/random"
      version = "= 3.6.0"
    }
    tls = {
      source  = "hashicorp/tls"
      version = "4.0.5"
    }
  }
}

# Add to the provider configuration the tags and
# parameters needed
provider "aws" {
  region = var.region
  default_tags {
    tags = {
      comments  = "this resource is managed by terraform"
      terraform = "true"
      project   = var.project
    }
  }
}

```


We can see that in addition to the already-discussed "aws" provider, we also include the "local", "random", and "tls" providers.
![[Screenshot 2025-12-17 at 15.22.25.png]]

These are *utility* providers that provide extra functionalities you can use when creating your resources. 

- You will use [`random`](https://registry.terraform.io/providers/hashicorp/random/latest) to generate a random value for the database password, 
- [`TLS`](https://registry.terraform.io/providers/hashicorp/tls/latest)to create the SSH key pair, 
- and [`local`](https://registry.terraform.io/providers/hashicorp/local/latest) to create a file to store the private key for the SSH key pair. 

Now in the AWS block, note that the region and project name are specified through the use of variables. ![[Screenshot 2025-12-17 at 15.29.44.png]]


`network.tf`
The network file contains the **data blocks** for the VPC and its subnets. 


---
SSH:
https://linuxize.com/post/how-to-setup-ssh-tunneling/#remote-port-forwarding
