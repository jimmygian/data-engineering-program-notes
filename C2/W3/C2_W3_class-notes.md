
This week is all about DataOps, but really it includes elements of the software engineering and data management undercurrents as well. 

As you learned in the previous course, Dataops is a set of practices and cultural habits centered around building robust data systems and delivering high quality data products. 

![[Pasted image 20251207184103.png]]

And DataOps really emerge from DevOps, which is the set of practices and cultural habits that allow software engineers to efficiently deliver and maintain high quality software products. 

![[Pasted image 20251207184114.png]]

This week, we're going to get into the details of each of the three pillars of data ops, namely automation, observability and monitoring, and incident response. 
![[Pasted image 20251207184126.png]]

Well, actually, to be fair, we're going to spend more time looking at automation and observability and monitoring, and not as much time with incident response. But that's not because incident response is less important. It's just that incident response has more to do with the cultural habits side of data ops, and it's harder to build practical exercises around that in an online course. 



Anyhow, when it comes to automation, we'll be revisiting some of the ideas covered in the previous course, like continuous integration and continuous delivery or CI/CD, and then zeroing in on the concept of infrastructure as code, which is to say, writing code that when you run it deploys the resources required to run your data pipeline. 
![[Pasted image 20251207184229.png]]

- In the previous labs in these courses, you've gotten some experience spinning up certain resources for your data infrastructure using the AWS console. 

- On the job as a data engineer, however, it's becoming common practice to do this deployment through infrastructure as code frameworks rather than manually spinning up instances and installing software. 


![[Pasted image 20251207184328.png]]
- In the first lab this week, you'll get hands on experience actually writing the code in terraform to deploy your infrastructure. 
- And after that, in the labs that follow, you'll build on top of your infrastructure to include monitoring of your data quality and other important observability metrics. 

Another exciting thing we have in store for you this week is a set of interviews with industry experts from the field of data ops. In these interviews, you'll be hearing about the key aspects of **data quality** and **data observability** from people who are actually building products to serve data engineers in these areas. 

## DataOps Automation
![[Pasted image 20251207184126.png]]
In this video, we're going to look at the **automation** pillar of DataOps. And as I said in the previous video, DataOps emerged from DevOps. 

And while there are significant differences between the requirements for delivering high quality software applications and those for delivering high quality data products, there's still a lot of overlaps. And so here, we'll be looking at some of the concepts that DataOps borrows from DevOps when it comes to automation. 

**CI/CD**
Within software engineering, one key aspect of automation is the practice of continuous integration and continuous delivery, or **CI/CD** for short. In the context of software, the CI/CD process involves 
- setting up systems for **automatic review** and **testing of new code**. 
- And then the **automatic delivery or deployment into production** of code that has been reviewed and tested. 

![[Pasted image 20251207190202.png]]



When it comes to DataOps, the practice of CI/CD can be applied directly to code and data within your data pipeline, much as it would be for a software product. 

![[Screenshot 2025-12-10 at 14.26.06.png]]
- whether that's code for applying a particular set of data transformations or populating a database or the data itself, you can maintain it just like you would any other piece of software application code. 


When it comes to actually *running* your data pipelines, as I talked about in the previous course, this is something you could do in a number of different ways. 

1. Have no automation and just run all the processes in your data pipeline manually. ![[Screenshot 2025-12-10 at 14.32.15.png]]
- Or you could set up the stages of your pipeline to run according to a particular schedule. ![[Screenshot 2025-12-10 at 14.32.30.png]]
- Or you could orchestrate your pipeline by defining it as a directed acyclic graph, or DAG, using an orchestration tool like Airflow. ![[Screenshot 2025-12-10 at 14.32.40.png]]

>	*We'll look at DAGs and the automation of testing and deployment more closely next week when we get into orchestration.* 

Now, one key underpinning **of any CI/CD system**, whether for a software product or a data product, is "**version control**", where each new code version of the code is *recorded*. ![[Screenshot 2025-12-10 at 14.33.56.png]]

This makes it possible to easily revert back to a previous version if for some reason the current version isn't working as expected or other problems occur. ![[Screenshot 2025-12-10 at 14.34.10.png]]

You might already be familiar with version control in the context of your own code, maybe using a platform like GitHub. And so within DataOps, the concept of version control also applies to data. Just like you can track changes in your code and roll back to a previous version, with DataOps, you can track changes in the data moving through your pipelines. And be able to roll back to a previous version of the data if you run into problems. 



**Infrastructure as Code**
Another concept that DataOps borrows from DevOps in terms of automation is *infrastructure as code*. Whether you're building software applications or data pipelines with cloud platform resources, it's possible to **maintain the design of your infrastructure as a code base**, just like you would for any other application code. 

You can run that code to deploy your infrastructure, or modify the code to redefine your infrastructure, and then run it again to deploy the updated infrastructure. Now, by defining your infrastructure programmatically using code, you can then maintain version control over your entire infrastructure, just like you would for any other piece of code or for your data. 
![[Screenshot 2025-12-10 at 14.36.26.png]]
And that way, if you need to roll back to a previous version of your infrastructure, it's as simple as rolling back to a previous version of your code. 

And so there are a number of ways in which DataOps automation practices will be part of your work as a data engineer. You can start to see how DataOps begins to overlap with the other undercurrents of the data engineering lifecycle. 
![[Screenshot 2025-12-10 at 14.37.16.png]]


## Infrastructure as Code

As I mentioned in the previous video, you can use Infrastructure as Code to programmatically define, deploy, and maintain your cloud infrastructure. This means you can automate the creation of all the resources you need for your cloud data pipelines, including networking, security, computing, storage, and other data management and analytics resources. 

But the concept of Infrastructure as Code actually **precedes** cloud computing and has much older roots in configuration management, dating back to the 1970s. Even back then, engineers struggled to efficiently configure and manage a series of physical machines. They would write BAS scripts to automate some configuration tasks, and looking back, you can think of this as the sort of primordial roots of Infrastructure as Code. 
![[Screenshot 2025-12-10 at 14.38.47.png]]

With AWS's release of EC2 in 2006, anyone could easily spin up cloud computing resources whenever they needed them, so, engineers were able to build more scalable applications with many components and complex dependencies. 
![[Screenshot 2025-12-10 at 14.39.14.png]]

In the early 2010s, software engineers developed **Infrastructure as Code** tools like *Terraform*, *CloudFormation*, and *Ansible* that allowed them to provision and configure their infrastructure using code-based configuration files. 
![[Screenshot 2025-12-10 at 14.40.42.png]]


Nowadays, you can use these tools to easily manage infrastructure resources on the cloud with lines of code, rather than manually clicking through resource setup windows or writing tedious BASH scripts. 
![[Screenshot 2025-12-10 at 14.41.02.png]]

![[Screenshot 2025-12-10 at 14.41.55.png]]



### Terraform config files

Let's take a look at some specific Terraform configuration files that you previously ran to create the **Glue ETL** and **S3 bucket** components of your data pipeline in previous labs. 

Let's zoom in on a part of the S3 configuration file to take a closer look. 

- With this first block of code, you set up the S3 bucket. 
  This bucket has a unique name with the specified prefix based on the variables defined for you in this lab. ![[Screenshot 2025-12-10 at 14.43.13.png]]

- You use a second block of code to configure that bucket and provide public access to it. Notice that the language used in this configuration file is relatively easy to interpret. ![[Screenshot 2025-12-10 at 14.43.31.png]]


It follows a simple pattern where you start with the **keyword** `resource`, then you specify the **resource type** and the **name** you want to give this resource. 

```
resource <resource-type> <resource-name> { config_option_key = "value" }
```


So in this example here, where it says `"aws_s3_bucket"`, you're telling Terraform you want to set AWS as the provider and S3 as a resource you want to provision. You'll refer to this whole string as the **resource type**: 
![[Screenshot 2025-12-10 at 14.43.59.png]]

and `data_lake` is the **name** you want to give this newly created resource. 
![[Screenshot 2025-12-10 at 14.44.55.png]]

Then within the curly braces, you can specify these configuration options using key value pairs. 
![[Screenshot 2025-12-10 at 14.44.20.png]]

### The HCL Syntax
This configuration file is written in a domain specific language called "**HCL**" or "**HashiCorp configuration language**", named after *"HashiCorp"*, the company that created Terraform. You can use the HCL syntax with Terraform to manage infrastructure resources across many cloud vendors. 

For example, here's how you can use HCL to create or update a VPC and an EC2 instance in AWS. ![[Screenshot 2025-12-10 at 14.55.16.png]]
- Notice this code follows the same pattern you saw in the S3 bucket configuration file. 


And here's how you could provision a GCP compute instance using Terraform. 
![[Screenshot 2025-12-10 at 14.55.58.png]]
- As you see, again, the code follows a similar pattern. But in this case, you specify GCP as a provider. 

### Declarative VS Imperative/Procedural Languages

HCL is what's called a **declarative** language, meaning that you just have to declare what you want the infrastructure to look like. For example, 
- what resources you want to create, 
- what values you want the configuration parameters to take. 
- etc..

This is also known as **the desired end-state** of the infrastructure. 

Terraform will then figure out the exact steps needed to achieve this desired end state. 

> 	*This makes Terraform highly idempotent, which means that if you repeatedly execute the same HCL commands, your infrastructure will maintain the same desired end state as the first time you ran the commands.* 


For example, let's say you run a Terraform configuration file that creates five EC2 instances. 

Terraform will first check the existing infrastructure to see if those EC2 instances (with the specific configurations mentioned for each instance) already exist. 
![[Screenshot 2025-12-10 at 15.00.32.png]]
- Here let's say it can only find two EC2 instances that match the config file, not 5.


If they don't exist, Terraform will create the missing EC2 instances. 
![[Screenshot 2025-12-10 at 15.02.13.png]]


If there are already EC2 instances that exist but don't match the configurations you specified, Terraform will ***update*** the existing EC2 instances to match the desired state rather than deleting or appending. And if these exact EC2 instances already exist, Terraform will do nothing and let you know that it didn't make any changes. 


**Imperative/Procedural Language**![[Screenshot 2025-12-10 at 15.03.58.png]]
This is in contrast to the imperative or procedural language used in *bash scripts* and some configuration management tools, where you need to *specify* **the exact sequence of configuration tasks.** 

Using the same example as before, if you repeatedly run a set of commands to provision five EC2 instances using an imperative language, then you will create five new EC2 instances each time regardless of whether they already exist. 
![[Screenshot 2025-12-10 at 15.04.11.png]]


And so like I mentioned earlier, in these courses, we'll be mainly focusing on Terraform when talking about infrastructure as code, because it allows you to manage infrastructure across many cloud providers. And it's a very popular tool among software and data engineers. 


## Terraform - Creating an EC2 Instance

To create your resources in Terraform, there's a **consistent workflow** that you'll always follow. 

```
- You first write the configuration files to define your resources, 
- then ask Terraform to prepare your workspace. 
	- It installs the necessary files that enable 
	  communication with the cloud APIs 
	- creates an execution plan describing 
	  the resources it will create, update, or destroy. 
- We then need to **approve** that plan, 
- Terraform applies the proposed steps and provisions your infrastructure. 
```

![[Screenshot 2025-12-10 at 15.27.17.png]]

In this first tutorial, we'll go into the details of how you can prepare your configuration files to create an EC2 instance. In the process, you'll learn more about the inner workings of Terraform and be ready to create any other resources in Terraform. 

>	*And just a little warning before we jump into the details here. This is probably going to feel like a firehose of information, but just bear with me. You don't need to master all the details the first time through, and you'll get a chance to practice all this yourself very soon.* 


So let's say you want to create an EC2 instance and launch it in the default VPC of your selected region, as shown in this diagram. 
![[Screenshot 2025-12-10 at 15.28.06.png]]
- In this example, I'm going to go with the West Virginia region, or US East 1. 
- I'm choosing to launch the EC2 instance in a **default VPC** here for simplicity. But recall that in week 1 of this course, Morgan said that you typically want to launch resources in your *custom VPCs* and reserve the default VPC for quick experimentation only. 

Let's start with creating the configuration file to define this EC2 instance. 
- To write your configuration files, you can use any IDE of your choice. 
- Install Terraform in your environment 
- and use your AWS credentials to authenticate Terraform. 

	*You can find more information about those steps on the Terraform website, which is linked in the resources section at the end of this week. In the lab, this will all be set up for you.* 

https://developer.hashicorp.com/terraform/intro


Here I'm using VS Code as the IDE. I already installed Terraform and created this folder that represents the root directory where I'm going to save the configuration files. So let's go ahead and create the first configuration file and call it `main.tf`. 

>	Any file that has `.tf `as its extension will be recognized by Terraform as a configuration file. 

You can structure your configuration files as 5 sections. 

```terraform

# terraform settings

< Block of code>


# providers

< Block of code>


# resources

< Block of code>


# input

< Block of code>


# output

< Block of code>

```


- You specify the Terraform settings and providers in the first two sections. 
- Then you'll define all the resources you want to set up in the next section. 
- And you can optionally define any input variables and output values in the final two sections. 
-> You'll then create blocks of code to represent entities within each section. 


In Terraform, a code block has the following JSON-like structure: 
- It starts with a **"keyword"** that tells you what type of block it is. 
- Then the block can include "**labels**" as strings if needed, depending on the type of the block. 
- Each block has a **"body"** delimited by  curly braces. 
	- Within the body, you can define the block arguments "or" further blocks. 

```pseudocode
keyword labels {
	arguments / other blocks
}
```



**Terraform Settings**

Let's start with the first section. The Terraform block you see here specifies the *Terraform settings*, including the required providers that Terraform will use to create your resources. 

```terraform
terraform {
	required_providers {
		aws = {
			source = "hashicorp/aws"
			version = ">= 4.16"
		}
	}
	
	required_version = ">= 1.2.0"
}
```

I'd like to pause for a moment to say a little bit more about providers because I feel this can be a bit confusing the first time you're using Terraform. 

- In the language of Terraform, a provider is a *plugin file* or a *binary file* that Terraform needs to install in order to interact with external resources. 
- You can find all the available providers in the Terraform registry https://registry.terraform.io/. 
- Some providers allow Terraform to interact with the cloud platform, and others are utilities providers that allow you to use additional functionalities in Terraform. 
- So just to be clear, in Terraform, the word ***provider*** is not a reference to the cloud provider where you're setting up resources. Instead, a provider in this context is a plugin or binary file that Terraform needs in order to interact with external resources. 

Let's check the AWS provider for example: https://registry.terraform.io/providers/hashicorp/aws/latest. 
	- And again, AWS provider here is not referring to AWS as the cloud provider. Instead, this is the file that allows Terraform to interact with resources on AWS. 
	
So here you can see the version of the provider, the source code, and a link to its documentation. 
![[Screenshot 2025-12-10 at 17.26.15.png]]

If you click on the documentation (upper right), you'll see a list of all the resources available by this provider and examples of their usage. When working with Terraform, this documentation can be really helpful because it shows you the arguments that you need to specify, in this case for any AWS resource. 

Now let's go back to the code. Since we're only using AWS resources in this example, you need to declare only the AWS provider as a required provider. For each provider, you need to specify a local name, the source location, and optionally a version constraint. ![[Screenshot 2025-12-10 at 18.40.34.png]]
- The local name is the unique identifier that you can use everywhere in the configuration file to refer to the provider. For example, here I'm using AWS as a local name for the AWS provider, which is the preferred name that is used in the AWS provider documentation. 
- The source, which is `hashicorp/aws`, is the global identifier of the AWS provider. It specifies where Terraform can download this provider when you run this configuration file. 
- You can also set a `version` constraint for Terraform itself by specifying the required version within the Terraform block. 


**Providers**

In the next section, you can create a *provider* block to c**onfigure the providers that you just declared**. 

```terraform
provider "aws" {
	region = "us-east-1"
}
```

- So in this provider block here, I've specified the AWS region. 
- Note that the name that I used next to the provider keyword is AWS, which is the *local* name I assigned for this provider within the Terraform settings block. 
![[Screenshot 2025-12-10 at 18.49.22.png]]


**Resources**

Next let's define the EC2 instance. 

In the resource block, you start with the keyword `resource`. Then you specify what is called the "resource type", which is a string that contains a **provider** and the **resource** separated by an **underscore**. 

```terraform
resource "aws_instance" ..
```

- So the **prefix** "aws" refers to the AWS provider we specified earlier, and the resource type "\_instance" refers to the AWS EC2 instance that I want Terraform to manage. You can always search for the resource type in the Terraform documentation of the AWS provider to find the name of the desired resource type you want to configure with Terraform.![[Screenshot 2025-12-10 at 18.52.30.png]]


The next string after the resource-type represents the **name** you chose to give to this resource. 

```terraform
resource "aws_instance" "webserver"
```

Together these two strings form a ***unique ID***, which you can use to reference your resource and other blocks of your configuration files. 

For example, you can refer to this EC2 instance using `aws_instance.webserver`. 
![[Screenshot 2025-12-10 at 18.55.31.png]]

Now, inside the resource block, you need to specify the arguments of the resource. And again, you can find the list of the arguments for each resource in the documentation. So for example, for the EC2 instances, you have this long list of arguments: https://registry.terraform.io/providers/hashicorp/aws/latest/docs#argument-reference

But just like when you're writing Python code, you don't need to specify every single argument as most of them are optional. The two *required* arguments for the EC2 instance are 
- the "AMI", and the 
- "instance type". 

```terraform
resource "aws_instance" "webserver" {
  ami           = "ami-0453ec754f44f9a4a"
  instance_type = "t2.micro"
  tags = {
	Name = "ExampleServer"	  
  }
}
```

- The AMI is a software template that contains information about the instances operating environment, such as the operating system and system architecture. AWS provides a long list of AMIs that you can find in the AMI catalog in the AWS console. In this example, I grabbed the ID of the most recent Linux based AMI. 
- For the second argument, I'm using `t2.micro` as the instance type. 
- I also set the optional `tag` to give the instance name "ExampleServer". 
	- Note that I did not specify the subnet in which I want to launch the EC2 instance. And this is because I want to launch it in any subnet of the default VPC. 
	

What you see here in this configuration file is enough to create an EC2 instance. So let's actually create this EC2 instance. 


In the example here, I'm going to start with the Terraform init command. 

```terminal
terraform init
```

When you run this command, Terraform installs the providers defined in the configuration file. So in this example, Terraform downloads the AWS provider and stores it in a hidden subdirectory named  `.terraform`. 
![[Screenshot 2025-12-10 at 19.11.45.png]]

Because Terraform has been successfully initialized, you can run the `terraform plan` command. 

```terminal
terraform plan
```

When you run this command, Terraform creates an execution plan that details what Terraform is planning to create, update, or destroy based on your configuration files. ![[Screenshot 2025-12-10 at 19.13.00.png]]
- So in this case, the plus signs mean that Terraform plans to create all of these components. In other cases, you might see a minus sign, which indicates things that will be destroyed, or a tilde symbol, which indicates things that will be updated. 



Finally, you can run the `terraform apply` command. 

```terminal
terraform apply
```

Terraform will show you the execution plan again, but then stops to wait for your approval. So here I'm going to type yes and wait for the creation of the EC2 instance.
![[Screenshot 2025-12-10 at 19.14.15.png]]And there you go. Terraform just created the EC2 instance based on these configuration specifications. If you want, you can check the instance in the console. 


## Terraform - Defining Data Variables and Outputs

In this section, I'd like to continue working on the same Terraform configuration file we started with in the previous section. 

Here we'll look at: 
- how to create input variables for your configuration, 
- how you can export your resource information using output values, and 
- how you can organize your Terraform workspace. 

In the previous section, I incorporated some hard-coded values inside the configuration file, such as the region name and the EC2 instance name. 
![[Screenshot 2025-12-10 at 19.16.56.png]]

- Instead of hard-coded values inside your code blocks, you can create input variables to parametrize your configuration. 


**Input**

The use of **input variables** allows you to *customize* your infrastructure and specify different values for your variables when you want to create your resources, instead of manually editing your configuration files. 

In this example, under the Input Variables section, I'm going to create one variable that represents the region name, and another one that represents the server's name. You can declare these variables like this using the `variable` keyword. 

```terraform
# input

variable "region" {
	description = "region for aws resources"
	type = string
	default = "us-east-1"
}

variable = "server_name" {
	description = "name of the server running the website"
	type = string
}
```


- Here the strings region and serverName represent the name of each variable, which acts as identifiers for the variables inside the Terraform configuration file. 
- Each variable has **three optional arguments**, 
	- a **"description"** that documents the purpose of the variable, 
	- the **"type"** of the variable, 
	- and a "**default value**" assigned to the variable. 
		- If you don't assign a default value for the variable, then you'll be prompted to specify its value before Terraform applies the configuration. 


Now to use these variables in other blocks, you can refer to each variable using the syntax 
```terraform
var.<variable_name>
```

So here in the Provider block, I replace the hard-coded value of the AWS region with `var.region`. 

```terraform
provider "aws" {
	region = var.region
}
```

And in the Resource block, I replace the hard-coded value of the instance name with `var.server_name`. 

```terraform
resource "aws_instance" "webserver" {
  ami           = "ami-0453ec754f44f9a4a"
  instance_type = "t2.micro"
  tags = {
	Name = var.server_name  
  }
}
```


To automatically assign values to the variables without being prompted, you can use the command line flag `–var` as shown here. 

```terminal
terraform apply -var server_name=ExampleServer
```

Or more conveniently, you can define the values of the variables in a specific file that has `.tfvars` as its extension. 

So here in the same directory that contains the configuration file, I'm going to create the file `terraform.tfvars`, and inside this file, I'm going to assign a value to the variable `server_name`. 

```terraform.tfvars
server_name="ExampleServer"
```

- Now to update the file, you can just use the command terraform apply. Terraform will use the file that ends with .tfvars to extract the values of the variables and update the configuration with the provided values. 

**Attributes / Characteristics**
Now, any resource that you create will contain a list of characteristics or attributes. For example, you can check the documentation of the EC2 instance. 
https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance#attribute-reference

You can see a list of all of its attributes, such as the id instance, its ARN, which stands for Amazon Resource Name, and its public IP address.![[Screenshot 2025-12-10 at 19.30.25.png]]![[Screenshot 2025-12-10 at 19.34.28.png]]


**Output Values**

In some cases, you might want to export these attributes. For example, you could print them in the command line, use them in other parts of your infrastructure, or reference them in other Terraform workspaces. To do this, you need to declare them as `output` values. 

So I'm going to create two output values to export: the id and ARN of the EC2 instance. 

```terraform
# Output

output "server_id" {
	value =
}

output "server_arn" {
	value = 
}
```

- The strings that come after the `output` keyword represent the names or the identifiers for these output values. 
- For each output value, you need to specify the `value` argument by assigning it to the attribute of the EC2 instance. 

You can access the attributes by specifying the identifier for the resource, which is

```terraform
resource_type.resource_name.attribute
```

So in this example, we need to do the following:

```terraform
# Output

output "server_id" {
	value = aws_instance.webserver.id
}

output "server_arn" {
	value = aws_instance.webserver.arn
}

```


Now let's see how you can access the output values in the command line. 

First, let's apply the updates by running the `terraform apply` command. 
![[Screenshot 2025-12-10 at 19.40.42.png]]
- Here you can see that Terraform detected the changes to outputs and it's telling you that it's going to create those output values. 

So here I'll type yes. 
![[Screenshot 2025-12-10 at 19.40.55.png]]
- You can see the outputs displayed in the Terraform message. 

- After you create the outputs, you can use the `terraform output` command in terminal to query all of them. 
- Or you can query an individual output by specifying its name, e.g. `terraform output server_arn` 

Right now I have several blocks in this configuration file where each block has a different purpose. But what if you have more than one resource with several input variables and several output values? If you declare all the blocks in one file, then at some point managing the file will become very cumbersome. 

> The better practice is to **split** this configuration file into several files. 

- You declare all your input variables in `variables.tf`
- all your output values in `outputs.tf`, 
- all of your providers in `providers.tf`, 
- and all of your resources in `main.tf`. 
	- You can also further divide main dot tf where you declare each resource in a separate `tf` file. 

Terraform will automatically concatenate all the files that end with `.tf` as if you've written all of them in one file.
![[Screenshot 2025-12-10 at 19.46.41.png]]


## Terraform - Defining Data Sources and Modules

In addition to the *resource* variable and *output* blocks that you saw in the previous videos, you can also declare **"data"** blocks in Terraform. By using Data blocks in your configuration file, you can **reference resources created outside of Terraform or in another Terraform workspace**. 

Terraform refers to these resources as data sources. 
![[Screenshot 2025-12-12 at 13.20.00.png]]

You can check the *provider* documentation in the Terraform registry to see how to declare these resources. 
https://registry.terraform.io/browse/providers
(Also read: https://developer.hashicorp.com/terraform/language/block/data)

You'll notice that for each resource available in the [AWS provider](https://registry.terraform.io/providers/hashicorp/aws/latest) for example, you can declare it in Terraform either as a *resource* or as a *data source* depending on whether you want to create that resource or read from an external resource.
![[Screenshot 2025-12-12 at 13.27.49.png]]



Let's go over two examples that use the data block. 

In a previous section, I created a configuration file that launches an EC2 instance inside the default VPC. 
![[Screenshot 2025-12-12 at 13.28.31.png]]


But now let's say you want to launch the EC2 instance inside a subnet of a VPC that you already created outside this current workspace.
![[Screenshot 2025-12-12 at 13.28.49.png]]

To access a subnet, I'm going to declare a data block as shown here. 

```terraform
# data source
data "aws_subnet" "selected_subnet" {
	...
}

# resources
resource "aws_instance" "webserver" {
	ami = "ami-0453c756f44f9a4a"
	instance_type = "t2.micro"
	tage = {
		Name = var.server_name
	}
}
```


Similar to the resource block, next to the data keyword, I'm going to specify two strings. ![[Screenshot 2025-12-12 at 13.32.45.png]]
- The first string represents the **resource type**, which you can get from the provider's documentation. 
- And the second string is a name you choose to give to this **data source**. 
	- You can now refer to this name throughout the configuration file. 

Inside the data block, you need to specify the arguments that identify the subnet that you want to use. 

> 	*Make sure to check the documentation in order to know what arguments this data source expects.* 


Here I am assuming that I know the ID of the subnet, and I'm assigning it to the id argument. 

```terraform
# data source
data "aws_subnet" "selected_subnet" {
	id = "subnet-0fd234efsdft4334rtfds"
}

# resources
resource "aws_instance" "webserver" {
	ami = "ami-0453c756f44f9a4a"
	instance_type = "t2.micro"
	tage = {
		Name = var.server_name
	}
}
```


Now that the data source has been declared, **you can use its attributes**. 

```syntax
data.resource_type.resource_name.attribute
```


So, we can now reference our data source id inside our resource block:

```terraform
# data source
data "aws_subnet" "selected_subnet" {
	id = "subnet-0fd234efsdft4334rtfds"
}

# resources
resource "aws_instance" "webserver" {
	ami = "ami-0453c756f44f9a4a"
	instance_type = "t2.micro"
	subnet_id = data.aws_subnet.selected_subnet.id       # ADDED
 	tage = {
		Name = var.server_name
	}
}
```



As another example, you could also use a data source to automatically identify the AMI argument of the EC2 instance. 
![[Screenshot 2025-12-12 at 13.38.21.png]]



In the first video, I explained that I grabbed this AMI from the AWS console. If I want to automate this process by asking Terraform to do the search on my behalf and retrieve the latest Linux AMI, I can use the following data block. 

```terraform
data "aws_ami" "latest_amazon_linux" {
	most_recent = true
	owners = ["amazon"]
	filter {
		name = "architecture"
		values = ["x86_64"]
	}
	filter {
		name = "name"
		values = ["al202*-ami-202*"]
	}
}

...
```
- So here I'm asking Terraform to find the most recent AMI, owned by Amazon, that has a specified system architecture, and that is Linux-based. 

And now, inside the EC2 instance resource block, you can specify the AMI as shown here. 
```terraform
# resources
resource "aws_instance" "webserver" {
	ami = data.aws_ami.latest_amazon_linux               # MODIFIED
	instance_type = "t2.micro"
	subnet_id = data.aws_subnet.selected_subnet.id       # ADDED
 	tage = {
		Name = var.server_name
	}
}
```




Let's now update the configuration by running the `terraform apply` command. 

- You see that Terraform is planning to destroy the previous instance to create a new one with the updated network settings. 
  ![[Screenshot 2025-12-12 at 13.43.39.png]]
- You can also see that it performed the search to find the latest AMI. ![[Screenshot 2025-12-12 at 13.44.16.png]]
- So now I'm going to type yes and wait for the updates to take place. ![[Screenshot 2025-12-12 at 13.44.49.png]]


### Terraform - Modules
The last topic that I'd like to cover in this tutorial is the use of **"modules"** in Terraform. 

>	*A module is a subdirectory inside your main directory that you can use to group resources that are used together.* 

	You can think of it as a way to package those resources. 


I'm going to create this `website` folder which will be my module that groups together all the resources that are used to create the website. 

```bash
mkdir website
```


So let's move the file `main.tf` that contains the definition of the web server into the `website` module. 

>	*A module is like a **regular root directory**, so it also expects you to declare the providers, the input variables, and the output values inside of it. So I need to move those files into the website module.* 

![[Screenshot 2025-12-12 at 13.48.52.png]]

The *module* we created is a self-contained folder (kind of like a mini-terraform project) that contains the resource, the input variables, and output values for that module. So, you can't directly access this information in the root directory, meaning that if you run `terraform apply` now, it will give you errors because the root directory won't be able to see the values assigned to the input variables. 

To solve this, you need to create a `main.tf` in the root directory, and declare a **module** block to call that module:

_(Root's `main.tf` file)_
```terraform
module "website" {
	source = "./website"
}
```

Now inside this module block, I can also include the *input* variables of that module with specific values. 
```terraform
module "website" {
	source = "./website"
	server_name = [..]
}
```


Here `[..]` I need to specify a value for the input variable server underscore name. And for that, I'm going to create this `variables.tf` file in the main directory and declare the `server_name_root` variable. 

_(Root's `variables.tf` file)_
```terraform
variable "server_name_root" {
	description = "name of the server running the website"
	type = string
}
```


Then, back over in the module block, I can assign to the module's input variable the value of this variable. 
```terraform
module "website" {
	source = "./website"
	server_name = var.server_name_root
}
```

And then finally, in the `terraform.tfvars` file of the root directory, I need to specify the actual value of the server underscore name underscore root variable. 

```terraform.tfvars
server_name_root="ExampleServer"
```

And finally, if you want to export the module's outputs server ID and server ARN also as outputs in the root directory, you need to create an `outputs.tf` file inside the root directory. 
- In this file, you declare the outputs and assign them to the values of the module's outputs using this syntax. 
- You start with the word `module`, then you specify the `name` of the module that contains these output values. And then you specify the name of the `output values` you want to access. 

_(Root's `output.tf` file)_
```terraform
output "server_id" {
	value = module.website.server_id
}

output "server_arn" {
	value = module.website.server_arn
}
```


>	Whenever you add, remove, or modify module blocks, you need to rerun `terraform init `to allow Terraform to install new modules or adjust the installed modules. 


## Data Observability

In addition to the principles of automation that data engineers borrow and build on from DevOps, the principles and practices of observability and monitoring are a key pillar of DataOps that also has its origins in software development. 

![[Screenshot 2025-12-17 at 15.36.44.png]]



Over the past decade or so, with the advent of the cloud and the move toward distributed systems, software engineers have developed observability tools help their teams gain visibility into the health of their systems. 

With these observability tools, teams are able to monitor metrics such as CPU and RAM usage and response time, which helps to quickly detect anomalies, identify problems, prevent downtime, and ensure reliable software products. 

![[Screenshot 2025-12-17 at 15.37.36.png]]


When it comes to data observability and monitoring the health of your data systems, some of the same tools that software teams rely on can also be helpful to you as a data engineer. With that being said, in addition to monitoring for things like CPU usage or system response times, as a data engineer, y**ou also need visibility into the health, or in other words, the quality of your data**. 

As a reminder, in the previous course, we defined high quality data as *data that is accurate, complete, discoverable, and available in a timely manner.*  And beyond that, high quality data represents exactly what stakeholders expect it to represent in terms of a well defined schema and data definitions. 

![[Screenshot 2025-12-17 at 15.39.10.png]]

Low quality data is the opposite,, and it might be inaccurate, incomplete, or otherwise unusable. 


- When you are able to provide high quality data to stakeholders in your organization, you're providing value for those stakeholders. But believe it or not, providing low quality data is actually worse than providing no data at all. 
- When business decisions get made based on low quality data, it can be extremely costly for an organization, and it can leave stakeholders second guessing the value of the data team. 

![[Screenshot 2025-12-17 at 15.40.18.png]]



To complicate matters further, data systems that provide poor quality data might look perfectly healthy on the outside. For example, let's say you have a software application like a mobile app or a website. If the app crashes, or if you get a 404 error when trying to load a webpage, it's pretty obvious that something has gone wrong. And those are the kinds of things that can trigger an alert to a software engineer so they can go fix the problem. 

With the data system, if your system simply stops working and you're able to recognize immediately that it's not working, that's actually your best case scenario in terms of potential failure modes. In this case, you can debug the problem and get your system up and running again. If, on the other hand, your data suffers a breaking change such that your system still works but is no longer providing high quality output, that's when things can get really ugly. 

*Example: (Left, expected, right, reported)*
![[Screenshot 2025-12-17 at 15.42.09.png]]

And so in terms of data quality, this would be an example of where, in the blink of an eye, your system went from serving high quality data to serving data that was no longer accurate, or at least was not what stakeholders expected it to be. 

Now, you may think that these errors may not be your responsibility since they originated from a software error or similar, but upstream changes that disrupt or break your data systems should be expected and mitigated. Really, no matter where it comes from, once the data is in your hands, it's your responsibility to ensure its quality. So when there's a disruption to your data system, how do you make sure you know what happened as soon as possible? Well, that's where data observability and monitoring comes in.

>	*Data observability is the ability to understand the health of your data systems, be able to improve it over time.*

--- 

Data issues are inevitable and they could occur at any stage of your data pipeline. The earlier you are able to detect them, the less damage to the organization it will cause. To detect data issues, you need to first choose metrics that assess the data quality, similarly to how software teams monitor metrics that assess the health of their software's infrastructure.

In her book ([Data Quality Fundamentals](https://www.oreilly.com/library/view/data-quality-fundamentals/9781098112035/ch02.html#what_are_data_quality_metricsquestion_m)), Barr Moses suggests to start with the following questions:

- Is the data up-to-date?
- Is the data complete?
- Are fields within expected ranges?
- Is the null rate higher or lower than it should be?
- Has the schema changed?

She formulated these questions into 5 pillars for data observability, which aim to fully describe the state of the data.

#### [**Barr Moses 5 Pillars**](https://www.montecarlodata.com/blog-what-is-data-observability/)

1. **Distribution/ Internal Quality**: The quality pillar refers to the internal characteristics of the data, and checks metrics such as the percentage of NULL elements, percentage of unique elements, summary statistics and if your data is within the expected range. It helps you ensure that your data is trusted based on your data expectation.

2. **Freshness**: Data freshness refers to how “fresh” or “up-to-date” the data is within the final asset (table, BI report), i.e., when the data was last updated, and how frequently it is updated. Stale data results in wasted time and money.

3. **Volume**: Data volume refers to checking the amount of data ingested and looking for unexpected spikes or drops. Sudden drops in data volume can indicate issues like lost data or system outages, and sudden increases may indicate unexpected surges in usage.

4. **Lineage**: According to [Barr](https://towardsdatascience.com/introducing-the-five-pillars-of-data-observability-e73734b263d5), “When data breaks, the first question is always “where?” Data lineage helps you trace the data journey from its source to its destination, visualizing how data was transformed and where it was stored. This way, you can identify the source of errors or anomalies.

5. **Schema**: Data schema refers to monitoring changes in data structure or types. This pillar helps avoid the failure of the data pipeline.


##### Additional Resources

If you'd like to learn more about data observability, you can check the following additional resources.

- [Data quality fundamentals](https://learning.oreilly.com/library/view/data-quality-fundamentals/9781098112035/) , by Barr Moses, Lior Gavish, Molly Vorweck [book]
- [The rise of data downtime](https://towardsdatascience.com/the-rise-of-data-downtime-841650cedfd5), by Barr Moses [article]
- [What is data observability?](https://learning.oreilly.com/library/view/what-is-data/9781098120993/ch02.html#characteristics_of_a_data_incident) , by Andy Petrella [book]


## Monitoring Data Quality


By this point, I'm guessing you're feeling pretty well convinced that data observability and monitoring are important. But when it comes to actually doing it, *where do you start?* 

Like a lot of the other things we've been discussing so far in these courses, it all starts with what the **stakeholders** need. 

Practically speaking, there's a wide range of metrics or quality criteria you could decide to monitor when it comes to your data pipeline. For example, 
- you can monitor something like the total number of records adjusted in each batch, or over some time interval, or 
- whether the range of values in a particular column stays within some thresholds. 
- Or you could count the total number of null values in a table, or the difference in time between now and the timestamp of the most recent record in your data. 

There are a whole bunch of things you could decide to monitor. However, rather than setting up monitoring and alerts for every conceivable thing you could measure or observe about your data, **you'll want to identify the most important things and focus on those.** If instead you try to monitor every imaginable aspect of your data, you can end up creating confusion and alert fatigue, and the really important stuff will get lost in the noise. 

![[Screenshot 2025-12-17 at 16.11.18.png]]

When it comes to deciding what metrics or aspects of your data to monitor, the first question should be *what do stakeholders care about the most for this particular use case?* 

![[Screenshot 2025-12-17 at 16.13.56.png]]




I've been emphasizing that you should communicate with source system owners to be sure you understand what kinds of changes you'll need to anticipate or mitigate in the future. While there's no replacement for good communication, you should also take steps to build in checks or tests in your data monitoring to verify that things like the schema and types for the data you are ingesting stay consistent. If all goes well, these can just be nice sanity checks to ensure the data you're ingesting is still in the format you expect. But these checks can also help to identify problems early, before they are propagated further down your data pipelines. 

![[Screenshot 2025-12-17 at 16.15.39.png]]

Like a lot of things in data engineering, there are many different ways to monitor your data quality. You could do some things manually, or maybe write some custom code to perform a set of tests or trigger alerts. Those approaches might make sense in certain scenarios, like when you're first setting up or prototyping your pipelines. But nowadays there are a number of **tools** you can use to ensure data quality and also spare you from any *undifferentiated heavy lifting*.




## Resources

**Optional reading and reference material:**

- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
- [AWS CloudFormation (User Guide)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
- [Great Expectations Documentation](https://docs.greatexpectations.io/docs/core/introduction/)

- To read about the third pillar "Incident Response", check the following two articles:
    - [article 1](https://medium.com/@mikldd/incident-management-for-data-teams-5a14acd4e3d8)
    - [article 2](https://cloud.google.com/docs/security/incident-response)

- [Data Quality Fundamentals](https://learning.oreilly.com/library/view/data-quality-fundamentals/9781098112035/) , by Barr Moses, Lior Gavish, Molly Vorweck [book]
- [The rise of Data contracts](https://dataproducts.substack.com/p/the-rise-of-data-contracts), by Chad Sanderson [blog]
- [What is data observability?](https://learning.oreilly.com/library/view/what-is-data/9781098120993/ch02.html#characteristics_of_a_data_incident) , by Andy Petrella [book]
- [Driving data quality with data contracts](https://learning.oreilly.com/library/view/driving-data-quality/9781837635009/B19790_02.xhtml#_idParaDest-34), by Andrew Jones [book]
- [Data Contracts: Why Thought Leaders Opt for Ounces Over Pounds](https://www.gable.ai/blog/data-contracts), by Chad Sanderson