
## Course Overview

 You've just been hired as a data engineer, and your company is looking for you to build **data systems** that can deliver on their goals. To accomplish this, you'll need 
 * to be able to translate the needs of the stakeholders in your company into system requirements and then 
 * choose the right set of tools and technologies to build that system. 

Now, that might sound relatively straightforward. But all too often, I've seen data engineers dive straight into the implementation of a system, choosing tools and technologies before they deeply understood how their systems will deliver value for the organization. Taking such an approach can lead to disaster, like wasting time, resources at your company, and could even cost your job. 

Rather than diving straight into implementation here, in these courses, particularly in this first course, we'll be spending a fair amount of time looking at the big picture. Talking about **a set of principles** that will apply to all of your data engineering projects, and forming that mental framework for building successful data systems.


## Data Engineering Defined


*Definition:  Data engineering is the development, implementation, and maintenance of systems and processes that take in raw data and produce high quality consistent information that supports downstream use cases, such as analysis and machine learning. Data engineering is the intersection of security, data management, DataOps, data architecture, orchestration, and software engineering.* 

To visualize the life cycle of data engineering in our book, we put together this diagram. . You can think of the data engineering life cycle as comprising a **series of stages**. 

![[Pasted image 20251005114759.png]]

* Life cycle starts with Data Generation: One way or another, the life cycle starts with data generation from various source systems. These source systems might be any kind of software application or user generated data or sensor measurements, or something else. 
* Ingestion / Transformacion / Storage / Serving:  These are the stages of the life cycle that **will be your focus as a data engineer**.  storage sits underneath ingestion, transformation, and serving and spans the entire width of the box. This is to indicate that data storage is an integral part of each of the stages above. In practice, the data systems you build may not look quite as simple as a life cycle diagram might appear. 
* End use cases:  These include things like analytics, machine learning, or something called Reverse ETL, which is basically sending the transformed or processed data back to the source systems to provide additional value for individuals in an organization who use these systems.


**Data Pipeline term Definition:**  
Data Pipeline describes the various steps data passes through on its way from generation and source systems on through to end use cases. You can think of the data pipeline as a combination of architecture systems and processes that move data through the stages of the data engineering life cycle. As a data engineer, you'll manage the data engineering life cycle, beginning with getting data from source systems, and ending with serving data for use cases, including analytics and machine learning.* 

**Undercurrents of Data Engineering:**
There are six components of data engineering as what we will call the **undercurrents of the data engineering life cycle**. These undercurrents are not stages of the life cycle like the elements above, but rather they span the entire life cycle. The way to read this diagram now is that you have these stages of the data engineering life cycle up here, including data generation, ingestion, storage, transformation, and serving.


**Big Data Definition:**
The Oxford Dictionary defines big data as extremely large datasets that may be analyzed computationally to reveal patterns, trends, and associations, especially relating to human behavior and interactions. 

Another famous and succinct description of big data is the three Vs of data, velocity, variety, and volume, which is to say that big data comes at high velocity, in wide variety, in large volume. In 2004, Google published a paper on MapReduce, an ultra-scalable data processing paradigm. This publication constituted a big bang moment for data technologies and the cultural roots of data engineering as we know it today. 

#### Stages of the Data Engineering cycle

**Data ingestion** incorporates data **from** generating sources **into** the processing system. For instance, in the **push model**, data from the source system gets written to the desired destination, while in the **pull model**, it is the other way around. The line separating push and pull methodologies blurs as data transits through numerous stages in a pipeline. Nevertheless, *mastering data ingestion* is paramount to ensuring the seamless flow and preparation of data for subsequent analytical stages.

**Data transformation** **refines raw data through operations** that enhance its quality and utility. For example, it normalizes values to a standard scale, fills gaps where data might be missing, converts between data types, or adds even more complex operations to extract specific data features. The goal is to mold the data into a structured, standardized format primed for analytical operations.

**Data serving** makes processed and transformed **data available for end-users**, applications, or downstream processes. It delivers data in a structured and accessible manner, often through APIs. It ensures that data is timely, reliable, and accessible to support various analytical, reporting, and operational needs of an organization.

**Data storage** is **the underlying technology** that stores data through the various data engineering stages. It bridges diverse and often isolated data sources—each with its own fragmented data sets, structure, and format. Storage merges the disparate sets to offer a cohesive and consistent data view. The goal is to ensure data is reliable, available, and secure.


## The Data Engineer Among Other Stakeholders

![[Pasted image 20251005200033.png]]

We've already mentioned some of these potential end use cases, namely, analytics and machine learning. When it comes to **who the downstream data consumers are.** In each of these cases, these could be analysts, data scientists, machine learning engineers, or others in your organization who need to make **data driven decisions** like salespeople, product or marketing professionals, or executives. 

 As a data engineer, it's important to be engaged in understanding the overall strategy of your company so you can better understand what business value could be extracted from the data you'll serve as well as what business metrics or downstream stakeholders care about. 

In your efforts to get raw data and turn it into something useful and serve it to end use cases, you'll have stakeholders both **downstream** and **upstream** of the systems you build. 

* Take the time to connect with those upstream source system owners to better understand the data you're ingesting, as well as anything that might disrupt your data pipelines like outages or changes in the data. 
* And when it comes to downstream stakeholders, take the time to understand *how the data you're serving adds **value** for your organization* and how that relates to the individual goals of the stakeholders you serve. 

## Business Value

Any company that hires you as a data engineer does so because they believe you will add value for the organization just the same as any other employee, really.

**What advice do you have for somebody who's getting started in the field of data?** 
> Go to where the money is if you want to have long term, great success in our industry, find business value. Because at the end of the day, business value drives everything we do in technology.

**But what does it actually mean to add value as a data engineer?**
>When you're fulfilling stakeholder needs, you're providing them with value, whether that comes in the form of increased revenue, cost savings, work made more efficient, or something else, like helping with a successful product launch.


## System Requirements

The way to be sure you're delivering value for stakeholders is first **understand their needs** and then **translate those needs into requirements** for the systems you build.

**What does it means to gather requirements for your system?**
First off, the word requirements can mean a number of different things in the context of business and engineering. 

* **Business Requirements**:  Business requirements might broadly include things like growing revenue or increasing the user base. 
* **Stakeholder Requirements**: Needs of individuals within the organization, namely those things they need to get their job done well.

When it comes to data engineering or software development in general, *Engineers need to define system requirements that describe what a system needs to be able to do in order to meet the business and stakeholder requirements*. 

**Functional / Non-functional Requirements**
System requirements are often split into two categories: Functional and Non-functional requirements.

* **Functional requirements** are **the what**, meaning, those things that the system needs to be able to do. In the context of data engineering, these might be things like: 
	* Providing regular updates to a database that serves analytics dashboards or 
	* Alerting a user when there's an anomaly in the data.

* **Non-functional Requirements**: You can think of non-functional requirements as **how** your system will accomplish what it needs to do. This could include things like: 
	* Technical specifications for ingestion, orchestration, or storage that you plan to use within your data pipelines to meet the end users' needs. 


**Requirements Gathering**

![[Pasted image 20251005202214.png]]

To build any data system, you need to **start with a set of requirements for that system**. These could include everything from high level business and stakeholder requirements, to features and attributes of the data products you provide, right down to the memory and storage capacity you need for your computation and database resources. You'll also have cost constraints, as well as security and regulatory requirements to consider.

The first and most important step in any data engineering project is to gather the requirements for your system. In general, those requirements will come from your **downstream stakeholders,** those that hope to achieve their goals as a result of your work.

 The problem is your stakeholders will most often not be speaking to you in the form of concrete system requirements. Instead, what they have on their minds are **business goals**, and it's your job to figure out how to **translate their need**s as they relate to the business goals into the requirements for your system. 
 
 Requirements gathering is going to look a little different for every system you build., but this process always starts with having conversations with your stakeholders.  The way you go about having these conversations will vary depending on the stakeholders technical level when it comes to data and data systems and the role within the organization. 
 
 The information that you're going to know and how things flow in and out of any ecosystem is so powerful. It's art and science combined.

### Requirements Gathering Conversation

 The goal here is a layout and approach you can use for conversations, to extract the requirements for your data systems, as well as identify other stakeholders you'll need to talk to.
1. Learn what existing data systems or solutions are already in place
2. Learn waht pain points or problmes rhere are with the existing solutions
3. Learn what actions stakeholders plan to take with the data
4. Identify any other staeholders you'll need to talk to if you're still missing information.

In those conversations, you'll aim to understand what an ingestion solution might look like that is better than the daily data dumps, as well as what kind of disruptions or changes you can expect and how you might be given advanced notice of when to anticipate those changes.

So I want to highlight a key tactic in requirements gathering, which is to ask your stakeholders what action they plan to take based on the data you serve them.  what action they plan to take is not the same as asking what they need.  You can then map their plans to their needs. In many cases, once you've understood their needs, you'll discover there are different or additional functional requirements they haven't yet identified, and getting aligned on those will be the next step.

## Thinking like a Data Engineer (Framework)


Requirements gathering as a process is not unique to data engineering. In fact, it's something that you might run into in various product development or product management scenarios.  But in general, the next step after requirements gathering is to decide on the details of how you'll actually build your product or system to meet the requirements.

I'd like to share a **framework** for what I call "*thinking like a data engineer*" that you can use for whatever project you're working on.

![[Pasted image 20251006215408.png]]


#### **1. Identify business goals and stakeholder needs**
![[Pasted image 20251006214939.png]]
In this first stage, your main objective is to determine *what the business goals and stakeholder needs are*, that will be driving a project.
* You'll first need to **get clear** on the **high level business goals** for the company. 
* You'll **identify who all of the stakeholders are** that have needs related to your project and how their needs tie into those higher level business goals. 
* You'll need to have conversations with individual stakeholders, **learn what systems are currently in place** that you'll either be building or replacing and what needs stakeholders have beyond what the current systems are providing. 
* It's also important at the stage to **ask your stakeholders what action they plan to take with the data products you provide them**. This can help you zero in on the actual functional requirements of the system you'll build. 

#### **2. Define system requirements**
![[Pasted image 20251006215139.png]]
In the second stage, your objective is to turn stakeholder needs in the **functional** and **non-functional** requirements for your system.

* **Functional Requirements:** Describe **WHAT the system must be** able to do to meet the needs of stakeholders.
* **Non-Functional Requirements:** Describe the technical specifications of **HOW the system will do what it needs to do**. 

Once you've arrived at a set of functional and non functional requirements, you'll document your conclusions and **confirm with stakeholders** that a system designed to do what you have described in these requirements will meet their needs.


#### **3. Choose tools and technologies**
![[Pasted image 20251006215230.png]]
In the third stage, you'll **choose the tools and technologies to build your system**. 

* This stage begins with **identifying the tools and technologies** that can meet the requirements of your system. In general, there will be multiple tools and technologies that can meet any individual requirement.
* you'll have to do a **cost-benefit analysis** to **evaluate the trade offs between them**. That way you can choose the best components among these tools. In this analysis, you may consider things like licensing fees, estimates of spending on Cloud resources, as well as other resources needed to build and maintain a system with these components.
* And finally, having chosen what you believe to be the best set of tools and technologies, you'll **set up a prototype of your system** to test whether it meets your expectations and looks like it can deliver value for your stakeholders.

 This last step of setting up a prototype and testing it is very important. Before investing the time and energy into building out the full data system, you need to test whether the system you've designed will actually be able to meet the needs of your stakeholders. At this stage, you'll check back with stakeholders and **let them evaluate whether the system you've designed will deliver value for them**. And I would definitely recommend that you spend as much time as it takes iterating on your prototype to be sure your system is going to be successful once you move into production.


####  **4. Build, evaluate, iterate, and evolve**
![[Pasted image 20251006215312.png]]
As a last step in this final stage, you'll build and deploy your data system. 

* Once it's up and running, you'll continuously **monitor and evaluate** your system's performance and iterate on it to keep improving it where you can. 

#### **Cyclical Process**
![[Pasted image 20251006215338.png]]Any data system you build will need to evolve over time. This can be due to changing stakeholder needs or in some cases, the emergence of a new tool or technology that can improve your system's performance, reduce your costs, or provide some other advantage. And so while I presented this framework as a sequence of four stages that happened one after another, in reality, this will be an ongoing and sort of cyclical process. 

As business goals and stakeholder needs evolve, your data systems will need to evolve with them. So you can think of this as something where, as a data engineer you're always communicating with stakeholders about their needs and expectations, evaluating those needs in terms of requirements for your data systems, and updating your systems as needed to meet the needs of your stakeholders. 


## Intro to AWS Cloud

At AWS, we describe the cloud as the on-demand delivery of IT resources over the Internet with pay-as-you-go pricing.

IT Resources are now in the cloud instead of on-premises: Compute / Storage / Networking / and more.

![[Pasted image 20251006222111.png]]

**Compute Resources:**
On AWS, compute resources are places to run code like virtual machines, container hosting services, or serverless functions.

**Storage Resources:**
Storage resources are places to store your data, including services like the Amazon Simple Storage Service (S3), or Amazon Elastic Block Store, as well as database services, where you can run relational databases, NoSQL databases, graph databases, and more.

**Networking Resources:**
Networking resources allow you to connect other resources to each other and to the outside Internet. An example of a networking resource is Amazon Virtual Private Cloud, where you have your own private network in the cloud.

**More:**
Beyond compute, storage, and networking, there are many more categories of AWS resources and services in areas like security, data streaming, ingestion, transformation, and others.


#### **Scalable and Elastic Solution**
The advantage of building your data systems on the cloud is that cloud resources are **scalable** and **elastic**. you don't need to worry ahead of time about exactly how much storage capacity you'll need. You don't need to manage the scaling operations as your dataset grows or shrinks. Instead, the service automatically scales up and down. It's elastic. This means you don't need to guess capacity ahead of time.

#### AWS Regions 
AWS resources and services are hosted in physical data centers that AWS has built out all over the world, and you use these services over the Internet through APIs, which allows you to build solutions without needing to build out and manage your own data centers. 

When you're using AWS, you won't be thinking in terms of single data centers. Instead, you'll deploy resources into what are called A**WS regions**, which are **collections of data centers within geographical areas** where you can use AWS services. 

AWS hosts its data centers across many geographical areas known as regions. At the time of the creation of this course, AWS has 34 launched regions around the world. Each dot on this map represents a region, which contains a cluster of data centers spread across the geographical area.

![[Pasted image 20251006223107.png]]

Hosting your solutions in a geographically distributed manner like this ensures the reliability and availability of your systems, and allows for resilience in the event that an individual data center goes offline due to a natural disaster or some other problem. AWS regions are named after their geographic area, like US East Northern Virginia, or Asia Pacific Mumbai, or Europe Frankfurt as examples. 

To host your applications or data pipelines, you need to choose an AWS region. Consider these four main factors:
- **Latency:** choose a region close to where your end users are located to minimize latency;
- **Cost:** the resource costs may differ between regions;
- **Compliance:** certain regulations may require hosting your data in a specific geographic region;
- **Service availability:** not all services are available in all regions.

#### Availability Zones (AZs)
**Each AWS region comprises multiple availability zones**, otherwise known as **AZs**, which are **smaller groupings of data centers within the region**. 

So, the way it works is that **multiple data centers make up one availability zone**, and **multiple availability zones make up an AWS region**. 

![[Pasted image 20251006223316.png]]

* AZs are physically separated by a meaningful distance, many kilometers, from any other AZ, although all are within 100 km (60 miles) of each other”(1). They are designed to be far enough away from each other that if something happens like an electrical outage, a flood, or other AZ-level failure, they can fail over to the other AZs in the region, which can then absorb the traffic so your systems are unaffected by the issue. 
* AZs are assigned a code name. The code name consists of the region code followed by a letter. For example, _us-east-1a_ represents an availability zone in us-east-1 (Northern Virginia Region).

When you create resources on AWS, you select the region and also potentially the availability zone, depending on the service you're using. Then behind the scenes, these data centers and availability zones are connected to each other with low latency links that run on the AWS global network of fiber cables connecting all of these facilities. 

In your work as a data engineer, you will often combine multiple AWS resources and services together, like building blocks to form solutions. AWS offers **over 200 services** with some being more general purpose and others being more specific in their intended use. There is more to the AWS global infrastructure, but this is a good start. 

**References**
1. [Regions and Availability zones](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/)
2. [AWS Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/)


## Intro to AWS Core Services

The core AWS services can be split up into five main categories: 
1. compute
2. network
3. storage
4. databases
5. security

There is of course much more to the AWS cloud than you can capture in five categories of services. But starting with these core categories makes the rest easier to conceptualize.

#### Compute
There are many different compute services on AWS, but there is one in particular that you are going to see a lot of in these courses, and that is Amazon elastic compute cloud or EC2. EC2, simply put, is the service that provides virtual machines or VMs on AWS.

You can think of a VM as a virtual computer or server where you can run whatever operating system you want, like Linux, macOS or Windows, as well as any applications, just like you would on any computer. 

On the AWS cloud, when you launch a single VM using EC2, we call that an **Amazon EC2 instance**.

#### Networking
Moving on to networking, whenever you create an EC2 instance or many other types of AWS resources, you need to place it into a network of some kind. 
 
In AWS, we call the private network you can create and place resources into, an "Amazon Virtual Private Cloud" or VPC for short. VPCs are private networks in the cloud that you can create and control that are isolated from other networks in the AWS cloud. You choose the size of the private IP space you want and you can partition it into smaller networks called sub networks or subnets for short. 
 
A single VPC spans all availability zones within a region, but you cannot span across regions. So you need to create a VPC in every region you want to operate in. So whenever you create certain AWS resources, like EC2 instances or instance based databases, you need to select which VPC you want and which AZ you want to place it in.

#### Storage
When it comes to storage on AWS, there are **multiple different types of storage** to be aware of, like **object, block, and file storage**, and AWS has services for each of these. 
- **Object storage** is the most often used for storing unstructured data like documents, logs, photos, or videos. But really, you can put any kind of data into object storage. 
- In contrast to object storage, there is **block storage**, which is typically used for database storage, virtual machine file systems, and other environments where low latency and high performance are critical. Within AWS, you can attach block storage devices called Amazon elastic block store volumes to EC2 instances which mount to the os, and you can then store and access data using programs running on EC2. 
- Last but not least, here we have **file storage**, which is the most familiar type of storage for your average non technical user. With file storage, data is organized into files and directories in a hierarchical structure, just like your file system on your laptop or a shared file system at work. 
- AWS has a managed service called **Amazon Elastic file system**, which provides a scalable file storage solution that can be mounted to multiple different systems at once for file access. 

In some sense, you could say databases are just another kind of storage service, but databases fall into a separate core category. Though databases use block storage behind the scenes to store data, they also provide special functionality for managing structured data, like enabling complex querying, data indexing, and other features which are not typically offered by general storage services.

#### Security
When it comes to security in the cloud, AWS follows what we call the shared responsibility model. The shared responsibility model states that AWS is responsible for security of the cloud and you are responsible for security in the cloud.

Each AWS service works a little differently and draws the line in a different place as far as where AWS's responsibility ends and yours begins. 


 

## Compute - Amazon Elastic Compute Cloud (EC2)

One of the basic services provided on the cloud is compute service, which means that AWS provides you with the compute resources needed to run your applications. An example of a compute service is _Amazon EC2_ (Elastic Compute Cloud), which represents a _virtual server_ or _virtual machine_ (the two terms are used interchangeably). 

**What is a server? How is a virtual server different from a regular server?**

A _server_ is like a computer or a set of computers that hosts and runs your applications. It consists of physical hardware (CPU, RAM, storage, networking components), an operating system installed on top of the hardware, and finally the applications that run on top of the operating system. 

When you run your application on the cloud, your application doesn't interact directly with the actual hardware. Instead, it interacts with virtual hardware, which is a software representation of the actual hardware that can emulate its behavior. So on top of the virtual hardware, an operating system can be installed to run your application. The virtual hardware, operating system and application are known as the components of a virtual machine or virtual server (a software representation or emulation of an actual server).

The benefit of this virtualization or abstraction is that **you can create more than one virtual machine that shares the same underlying physical resources**. This helps achieve efficient and cost-effective use of resources. The sharing of these resources is done through a software component called the _hypervisor_, which enables the sharing of the underlying hardware. **The hypervisor distributes the underlying physical computing resources, such as CPU and memory, to individual virtual machines as required.**
![[Pasted image 20251006225139.png]]

**Amazon EC2**

In AWS, these virtual machines or virtual servers are called Amazon Elastic Compute Cloud or Amazon EC2. EC2 is one of the primary building blocks that you may directly use to run your applications or indirectly use by interacting with other services built on top of EC2 instances.

“Elastic” in EC2 means that you can acquire the necessary compute and memory resources that you need for your work. When you run your applications on EC2 instances, you can configure as many instances as you need, and you only pay for what you use. When you no longer need an instance, you can stop or terminate it. You can also pick the size of an EC2 instance, where size corresponds to the amount of compute, memory, and network capabilities for a given instance. It’s easy to resize based on your needs. 

EC2 instances are grouped into several types, such as 
* general purpose, 
* compute optimized, 
* memory optimized, 
* storage optimized, and 
* accelerated computing
which you can choose based on your use case. 

AWS uses a **specific naming convention for the instance types**. For example, _t3a.micro_ breaks down as follows:
- t: family name
- 3: generation
- a: optional capabilities
- micro: size

AWS offers a few different pay-as-you-go purchasing options for EC2 instances. By default, you can choose to set up and launch _on-demand_ EC2 instances that give you compute capacity with no long-term commitments. If you want to save on cost, you can opt for EC2 _spot instances_, which are unused EC2 computing resources in the AWS cloud available at a discount compared to on-demand prices.

![[Pasted image 20251006225416.png]]

**References**

1. [Hardware virtualization](https://www.techtarget.com/searchitoperations/definition/hardware-virtualization)
2. [Virtual servers vs physical servers](https://www.techtarget.com/searchitoperations/tip/Virtual-servers-vs-physical-servers-What-are-the-differences)
3. [Physical servers vs. Virtual machines](https://www.veeam.com/blog/why-virtual-machine-backups-different.html)
4. [What is hypervisor?](https://aws.amazon.com/what-is/hypervisor/)
5. [What is virtualization?](https://aws.amazon.com/what-is/virtualization/)
6. [Traditional virtualization primer](https://docs.aws.amazon.com/whitepapers/latest/security-design-of-aws-nitro-system/traditional-virtualization-primer.html)
7. [What is amazon EC2?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html)
8. [Amazon EC2 instance types](https://aws.amazon.com/ec2/instance-types/)
9. [Instance type naming conventions](https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-type-names.html)
10. [EC2 instance purchasing options](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html)


## Networking - Virtual Private Cloud (VPC) & Subnets

Networking is another building block for hosting your work on the cloud.

#### What is a network?

A network is simply **a collection of devices connected together**, where each connection can be a **request** sent from one device to another or a response to a request. When you create and use resources on AWS, you want these resources to communicate with each other and possibly with the outside internet. Enabling the communication between resources and with the outside world requires an understanding of some basic cloud networking concepts. This includes understanding what an IP address is and how to create a network for your resources on AWS using VPC (Virtual Private Cloud) and subnets.

#### What is an IP address?

In a given network, each device is assigned an IP (Internet Protocol) address, which is a series of digits that uniquely identifies each device within the network. These addresses ensure that responses and requests are sent to the correct device. 

There are many types of IP addresses. IPv4 is the most widely integrated version of the IP address system. An IPv4 address is a 32-bit integer expressed in the form x.x.x.x, where each x is an 8-bit number that can take a value between 0 and 255. For example, 192.101.0.2 is a valid IPv4 address.

Another related term you will encounter when working on the cloud is CIDR (Classless Inter-Domain Routing) notation. A CIDR notation represents a range of IP addresses that could be assigned to devices within a particular network. CIDR is used to provision the required number of IP addresses for a particular network and reduce wastage of IP addresses. The following is an example of CIDR notation:

192.101.0.0/24

This notation means that the first 24 bits are fixed and the last 8 bits can be any bits. In other words, 192.101.0.0/24 represents all IP addresses between 192.101.0.0 and 192.101.0.255.

#### What is a VPC?

A **VPC (Virtual Private Cloud)** is an isolated private network where you can launch your AWS resources. **A VPC exists inside a region**, which can contain more than one VPC, and **a VPC spans multiple availability zones** inside the region. VPC is a way to isolate your resources (for example EC2) from the outside world. Think of it as a box or a wall that protects and organizes your resources. Resources within the same VPC can communicate with each other. By default, there’s no communication between resources from different VPCs or with the internet unless you allow it to happen by properly configuring the VPC.  

When you create a VPC, you need to **specify the range of IP addresses** or the **CIDR block** for the network, which determines the size of the network. Each resource created inside the VPC will be assigned an IP address from the specified range. When you launch resources such as EC2, you need to make sure they’re launched inside a VPC.

#### What is a subnet?

Inside your VPC, you may need some resources to be public and others to be private. You can achieve this by creating subnets within your VPC. Subnets provide you with more detailed control over access to your resources. Each VPC consists of subnets created inside availability zones. You can create a public subnet if you want to allow for outside traffic to access your resources, and you can create a private subnet if you don’t want to allow for outside traffic to access your resources.

You can think of a subnet as a smaller network inside your base network. Each subnet is assigned a CIDR block, which must be a subset of the VPC CIDR block. Resources in multiple subnets of the same VPC can communicate because they are part of the same VPC.

![[Pasted image 20251006225900.png]]
You will learn more about cloud networking concepts in later courses.

**References**

1. [What is IPv4?](https://www.whatismyip.com/ipv4/)
2. [What is my IP?](https://www.whatismyip.com/)
3. [What is CIDR?](https://aws.amazon.com/what-is/cidr/)
4. [What is Amazon VPC?](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)


## Security - AWS Shared Responsibility Model

When you host your applications and resources in the cloud, you’re offloading the heavy duty of managing the physical hardware to the cloud provider. The security of the physical facility is the responsibility of the cloud provider. However, you still own your data in AWS and you have complete control of it, so you are responsible for managing its security. This is known as the shared responsibility model on AWS.

- **AWS** is responsible for **security of the cloud:** this means AWS is responsible for maintaining, protecting, and securing the physical facilities that contain the compute, storage, and networking equipment. It is also responsible for protecting the global infrastructure -- the cables that connect the regions, as well as the software and hardware that run AWS services.

- **You** are responsible for **security in the cloud**: this means you’re responsible for protecting the data, ensuring it’s secured whether it is at rest or in transit, and managing who can access the data and for how long. Depending on the type of AWS services you choose, you might be responsible for additional configurations. 


It’s essential to keep this principle in mind because it’s your responsibility to manage who has access to the data you’re storing in cloud storage systems, and who has access to any pipeline you’re designing on the cloud.

**References**

1. [AWS shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/)
2. [Understanding the security scope](https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-accelerating-security-maturity/understanding-the-security-scope.html)

## [Optional] Creating an AWS Account

If you are a first-time AWS user and would like to get more practice with AWS services outside of the labs, feel free to create a free AWS account by following the three steps outlined in this [AWS getting started guide](https://docs.aws.amazon.com/accounts/latest/reference/welcome-first-time-user.html). Once you've created your account, you can try some AWS services free of charge within certain usage limits. 

However, you do NOT need your own AWS account to complete the labs for this specialization. For each lab, we will provide the required AWS environment and detailed instructions on how to complete the lab with the provided resources. Please note that there is no lab exercise for this week's content. Your first lab in this specialization will start next week.

**Important notes if you choose to create an AWS account:**

- Check out [this AWS article](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-free-tier.html) to understand which services are free so that you avoid incurring any costs.
- Here are some [additional instructions for monitoring AWS account costs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html).



## W1 Summary

 You could be the very first data engineer hired at your company tasked with building data systems from the ground up, or you might be joining an established data team. You might even find yourself at a company where someone else built the existing data systems, but they're no longer with the organization, and you've got to figure out how to take the existing infrastructure and modify it, expand it, and replace it to meet your business needs.

 ![[Pasted image 20251007224634.png]]
Whatever situation you find yourself in as a data engineer, the elements we discussed this week will be relevant. The first step to building successful data systems, whether you're starting from scratch, updating existing systems, working alone or with a team, is to understand the needs of your stakeholders and how they will derive value from the data you serve. When you can turn those needs into requirements for your systems and set up the appropriate tools and technologies to deliver on those needs, you'll be well on your way to adding value for your organization.


## Week 1 Resources

**Optional reading and reference material:**

- Chapter 1 of [Fundamentals of Data Engineering](https://go.redpanda.com/fundamentals-of-data-engineering)
- [What is a recovering data scientist?](https://www.linkedin.com/pulse/what-recovering-data-scientist-joe-reis/)
- [The Present and Future of Data Engineering](https://datastackshow.com/podcast/29-the-present-and-future-of-data-engineering-with-joe-reis-and-matthew-housley-from-ternary-data/)
- [Podcast with Sol Rashidi - Getting business value from Data](https://www.listennotes.com/podcasts/monday-morning/154-sol-rashidi-getting-b2Bjy6Fr-4P/#google_vignette)

**AWS Resources:**

- [Getting started with AWS](https://aws.amazon.com/getting-started/)
- [AWS Cloud Practitioner Essentials](https://www.coursera.org/learn/aws-cloud-practitioner-essentials)
- [AWS Cloud Technical Essentials](https://www.coursera.org/learn/aws-cloud-technical-essentials)
- [AWS Certified Data Engineer - Associate Certificate](https://aws.amazon.com/certification/certified-data-engineer-associate/)