
This week, we'll focus on creating good Data Architecture:

- We'll first take a look at how data architecture fits within the broader context of **enterprise architecture**, which is to say, the architecture for your entire organization. 
- After that, we'll look at some specific architecture examples and how you can start to think about **translating stakeholder needs into technology choices for your data systems**. 
- I'll also share with you some **guiding principles** you can keep in mind as you're learning to think like an architect. 
- In the lab this week, you'll be evaluating the trade-offs when it comes to things like cost, performance, scalability and security for an actual data architecture on the AWS cloud. 
- You'll also get a chance to explore the **AWS Well-Architected framework**, which is a complimentary set of principles that can help you design robust and effective data systems.


## What is Data Architecture?

**Definition of "Enterprise Architecture":** *Enterprise Architecture is the design of systems to support change in an enterprise, achieved by flexible and reversible decisions reached through a careful evaluation of trade-offs.*

**Definition of "Data Architecture":** *Data Architecture is the design of systems to support the evolving data needs of an enterprise, achieved by flexible and reversible decisions reached through a careful evaluation of trade-offs.*

Enterprise Architecture is the architecture of the whole enterprise, and encompasses various domains:

![[Pasted image 20251012092638.png]]
1. **Business architecture**, which applies to the product or service **strategy** and **business model** of an enterprise. 
2. **Application architecture**, which describes the structure and **interaction** of **key applications** that serve business needs. 
3. **Technical architecture**, which relates to the **software and hardware technology components** that are required to support the deployment of business systems and applications. 
4. **Data architecture**, which, as you've already seen, is all about supporting the evolving **data needs** of the enterprise.

So you can think of data architecture as an element of enterprise architecture, and in this way you can start to see how your work as a data engineer ties in directly with the highest level goals and the structure of your organization.


**Change Management**
As the structure of an organization may change over time, this leads to another important concept, **"change management"**, which is right at the heart of enterprise and data architecture.

You can expect the needs of your organization to be constantly evolving in your data architecture will need to adapt to those changes.

One-way and Two-Way doors idea:
* A "one-way" door is a decision that is almost impossible to reverse. The door shuts and locks behind you and there's no way to go back. E.g. Breaking an egg is an one-way door decision.
* A "two-way" door is an easily reversable decision. You can walk through the door and if you don't like what you see you can go back. Of course, it takes "energy" to open and close these doors (a.k.a money).

### Conway's Law

There is one very important guiding principle that has affected every architecture and system I've ever encountered. I want to introduce you to what's known as Conways law.

Melvin Conway:

_"Any organization that designs a system will produce a design whose structure is a copy of the organization's communication structure."_

This means that if different departments of an organization are isolated from each other, then they will create isolated systems. If there's cross-functional communication, then the systems will be cross-functional.


## Key principles of Good Data Architecture

As we saw in previous lessons, these are the key principles:

1. Choose common components wisely
2. Plan for failure!
3. Architect for scalability
4. Architecture is leadership
5. Always be architecting
6. Build loosely coupled systems
7. Make reversible decisions
8. Prioritize security
9. Embrace FinOps


These principles can be broken down into three groups:

How data architecture impacts other teams and individuals:
* Choose common components wisely
* Architecture is leadership

Data architecture is an ongoing process:
* Always be architecting
* Build loosely coupled systems
* Make reversible decisions

Unspoken but understood priorities:
- Plan for failure"!
- Architect for scalability
- Prioritize security
- Embrace FinOps

Let's dive into these three groups.

## How data architecture impacts other teams and individuals

##### **Choose common components wisely**

 One of the primary jobs of a data architect, and potentially part of your job as a data engineer is to choose common components and practices that can be used widely across an organization. 
 
**Common Components**
![[Pasted image 20251012095114.png]]
 Common components can be anything that has **broad applicability** within an organization, including things like **object storage**, **version-control** systems, **observability**, monitoring and **orchestration** systems, and **data-processing** engines.

Cloud platforms are an ideal place to adopt common components. For example, the separation of computed storage in Cloud data systems means you can serve data to different teams in organization with a shared storage layer that allows users to query the data for their particular use case.

When you choose common components wisely, they become part of the fabric of the organization. 
- Facilitating team collaboration and breaking down silos.
- Breaking down silos

**Making Wise Choices**
Of course, not every common component is the appropriate one. Making a Wise Choice means:
- Identifying tools that benefit all teams
- Avoiding a one-size-fits-all approach

##### **Architecture is leadership**
As a data engineer, you can practice architecture leadership by identifying the right common components in consultation with members in your organization. As you become more senior and take on more responsibilities, you might be the one to mentor others and provide proper training around these components as well.

As I said before, as a data engineer, I also recommend you seek mentorship from data architects in your organization or elsewhere because eventually, you may well occupy the role of architect yourself.


## Data architecture is an ongoing process

Going back to one-way / two-way doors idea, systems built around reversible decisions allow you to always be architecting.

**The API Mandate:**
Another concept that came through Amazon, is the  **"API Mandate"**:

_All teams must use service interfaces, otherwise known as application programming interfaces, or APIs, to communicate as well as to serve data and functionality._

What this meant was that no matter what kind of complicated mess any particular team had going on in their own systems, they would be required to provide any data, functionality, and communications to other teams, **through a stable and predictable service interface**. This allowed teams at Amazon to function together as a loosely coupled system where individual teams plugged into one another through these service interfaces, and any reconfiguration or retooling within given team did not affect the other teams.

The other part of the API mandate was that _**all of the service interfaces or APIs had to be built from the ground up** to eventually **be public facing** to developers in the outside world. _

This reorientation toward service interfaces laid the foundation for what would eventually become Amazon web services or AWS, which much of the world now uses as a cloud platform. 

In this next group of principles, we have: 
* Make reversible decisions, 
* Build loosely coupled systems and 
* Always be architecting. 

##### **Make Revirsible Decisions**
As you already know, those reversible decisions are your two-way doors, The decisions that you can easily undo if you don't like the outcome. That's where building **"Loosely Coopled Components"** comes into play.

##### **Build loosely coupled systems**
By loosely coupled in the context of architecture, I mean components that can be swapped out relatively easily without having to redesign your whole system. 
![[Pasted image 20251012102021.png]]

##### **Always Be Architecting**
With a system built from loosely coupled components and reversible decisions, you will have the ability to **always be architecting**.

As a data engineer, your job is not only to build systems that serve the data needs of your organization today, but also to have an eye toward the future so that you can constantly be adapting to the changes and business requirements, as well as available technology. 

![[Pasted image 20251012102141.png]]


## Unspoken but understood priorities

 In the next group of principles, we have: 
 - plan for failure, 
 - architect for scalability, 
 - prioritized security, and 
 - embrace FinOps.
 
### When your Systems Fail

When it comes to **failure modes** in your systems, it's best to take a practical and quantitative approach, just like you would with any other aspect of the performance for your system. 

#### System Metrics
System metrics include: 
- availability, 
- reliability and 
- durability of your data systems. 

**Availability**
*Availability, often called **uptime**, is the percentage of time a service or component is expected to be **in an operable state**.*

E.g. If you take a look at the different storage classes in **Amazon S3** object storage, you'll see that they range in availability from 99.5% up to 99.99% over the course of the year. Now, 99.5% and 99.99% might sound like high availability and even very similar numbers, but keep in mind that a 99.5% annual availability means that you can expect your storage system to be unavailable for around 44 total hours each year. While 99.99% availability means you can expect just about one hour of downtime per year.

**Reliability**
*Similar to availability, "reliability" is the probability of a particular service or component performing its intended function during a particular time interval.*

**Durability**
*Durability is the ability of a storage system to **withstand** data loss due to hardware failure, software errors, or natural disasters.*

In the Cloud, durability is crucial as businesses rely on Cloud services to store and access to critical data. Example, Amazon S3 boasts very high durability at 99.99999999%.

#### RTO and RPO
![[Pasted image 20251012110647.png]]
Related to the concepts of availability, reliability and durability, are: 
- **recovery time objective** or **RTO**, for short. 
  The RTO is the maximum **acceptable time for a service or system outage**. To establish an RTO for your application, you want to consider the impact to internal and external customers during the time this service or application is unavailable.

- **recovery point objective** or **RPO**, for short. 
  RPO, on the other hand, is a definition of the **acceptable state after recovery**. For example, when talking about a data storage system, the RPO might refer to the maximum acceptable data loss your system can tolerate due to an outrage. 

Being deliberate about your RTO and RPO for these systems you build will help you choose components with the right availability, reliability, and durability specs to meet your needs. That's one aspect of what it means to plan for failure. 

#### Security Failures
Another way your system can fail is through **security breaches**. That's why the principle of planning for failure and the principle of **prioritizing security** go hand in hand.

There are three main categories of optimizing security for a business:
- Cultivate a Culture of Security
- Practice the Principle of least privilege
- Practice Zero-Trust security

We talked about the first two in previous lessons, so let's focus on Zero-Trust security now.


#### Zero-Trust Security
To understand what zero trust is all about, it's useful to take a look at what you might call a more traditional approach to security, which is known as **hardened perimeter** security. 

Taking a "hardened perimeter" approach is equivalent to building a big wall around your systems where everyone and everything outside the wall is untrusted, while everything and every one inside the wall is trusted in the sense of having access to sensitive data and systems. 

![[Pasted image 20251012111125.png]]

The problem with a hardened perimeter approach is that would be attackers **only need to get past the wall, to gain unfettered access** to all of your data and your systems. In the Cloud era, the idea of building a hardened perimeter has the additional problem of there really being no physical perimeter with data and systems connecting across the Internet. 

Zero-trust by contrast, means that **every action requires authentication**, and you build your system such that no people or applications, internal or external are trusted by default. Instead, 
access is granted as needed and when it's needed.

![[Pasted image 20251012111358.png]]


#### Unforeseen Costs
Your system can also fail when you do things like 
- incur large unforeseen costs, or 
- miss opportunities for revenue. 

For example, when it comes to unforeseen costs, I'm talking about things like accidentally running expensive Cloud services such that your entire annual budget is consumed in a month or less. Believe it or not, I've seen this happen a lot. 

Or conversely, a missed opportunity might be getting a **sudden spike in demand** for your products and having your whole system crash because you weren't well prepared to scale up quickly. In this way, the principles of e**mbracing FinOps** and **architecting for scalability**, are connecting to the principle of planning for failure. As a data engineer, you need to think about the cost structures of Cloud systems.


For example, when running a distributed cluster, what is the appropriate mix of AWS on demand, EC2 instances versus spot instances? What is the most appropriate approach for running a sizable daily job in terms of cost effectiveness and performance? 
In the Cloud era, most data systems are **pay as you go** and **readily scalable**. Systems can run at a **cost per query** model, **cost per processing capacity** model, or another variant of a pay as you go model. 

It's now possible to **scale up for high performance** and then **scale down to save money**. However, the pay as you go approach makes spending far more dynamic. 
![[Pasted image 20251012111828.png]]
The new challenge for data engineers is to manage budgets, priorities, and efficiency when building and maintaining their systems. 

The main takeaways here are that if you plan for failure, or architect for scalability, prioritized security, and embrace FinOps, you'll be better positioned to serve the needs of your organization, not only when the systems you're building are functioning as expected, but also when failures happen.


## Batch Architectures

Last week we looked briefly at the concepts of batch versus streaming in the context of data ingestion. Now I'd like to focus on how some of these concepts appear in some well-established architecture patterns that you'll run into as a data engineer.

The goal here is to get you **thinking about some of the trade offs** and implications of the different choices you can make with your architectures.

Batch data architecture is what you might call the "traditional approach" to data processing, where you ingest, transform and store data **in batches or chunks**. Batch processing is most practical when real-time analysis is not critical. Usually a batch of data consists of data collected over some fixed period of time, maybe over the course of a day.

![[Pasted image 20251012112111.png]]

#### Examples of Batch Architecture:

**On a ETL Pipeline:** 
![[Pasted image 20251012112416.png]]
- You first ingest or extract batches of data from one or multiple sources, 
- perhaps into a staging area. 
- Then, you apply some transformations to clean up, standardize, and model the data, 
- and then load it into a data warehouse for storage and serving. 

**On an ELT Pipeline:**
There's also a variation of this pattern, known as extract, load, and transform, or ELT. With ELT, the idea is that: 
- You first ingest or extract batches of data from one or multiple sources, 
- after you ingest the data, you first load it into your data warehouse 
- and then perform transformations directly inside of the data warehouse.

This ELT architecture or pattern is becoming more popular nowadays, given the expanded computational power in many modern **cloud** data warehouses and other storage abstractions.


**What hapeens after ETL?**
![[Pasted image 20251012113033.png]]
What happens next, whether you're working with an ETL or ELT architecture, is that you'll serve data for downstream use cases, which I'll represent on the right side of the data warehouse over here. These might typically be analytics or machine learning, but another possibility, as I mentioned before, is that your end use case is so called reverse ETL, where some analysis is performed and then the process data is actually sent back to the source systems that are at the start of your data pipeline.


#### Data Marts

![[Pasted image 20251012113126.png]]
You could also have an additional layer here between your data warehouse and your end use case for something called a **data mart**. A data mart is a **more refined subset of a data warehouse** that is focused on a specific department, function, or business area. A data mart is designed to serve analytics and reporting. So you might have a data mart focus on sales, another for marketing, and yet another for operations (and so on). This kind of setup can make data more easily accessible to analysts and those who need to create reports.

With data marts, you can also provide an **additional stage of transformation** beyond the one provided by the initial ETL or ELT pipelines. These additional transformations might be things like additional joins between tables or aggregations that can help improve the performance of live queries.

#### Principles of good data architecture in a Batch approach
If you are setting up an architecture like this for your organization, there are a number of things you could be considering in terms of the **principles of good batch data architecture**. 

For example, if you're serving multiple end use cases for different teams or departments, how might you choose **common components** for your data warehouse and data pipelines that help facilitate **collaboration** and interoperability between teams? 
- When it comes to planning for failure, you want to be thinking about what happens, for example, if a source system goes offline or the upstream data scheme changes. 
- You'd also be looking for the **availability and reliability specs** for each of the components in your pipeline, and you'd be figuring out how to build flexibility into your system. For example, if you decide later to change the **cadence of ingestion**, or if you expect the **volume** in each batch of data to be dramatically different over time 
- To embrace FinOps, you'd also be doing some cost benefit analysis to understand what kind of trade offs you might need to consider when it comes to performance of different components of your system, as well as what kind of value you can provide for the business under different scenarios. 

We'll be keeping these principles in mind throughout these courses, and remember that the technologies you choose when building your data systems will always present a certain set of risks, as well as opportunities that can add value to your organization. Next up, we'll take a look at some common streaming architectures. I'll see you in the next video.

## Streaming Architecture

You can think of data as being produced in a *series of events*, and these events might be clicks on a website or sensor measurements or something else. In this sense, at its source, nearly all data could be characterized as a **continuous stream of such events**. That is, data is generally produced and updated in a continuous fashion.

- With **batch** data pipelines, which we looked at in the last video, you're waiting for data to accumulate, then at a predefined time interval or once the data reaches a certain size threshold, you could process a batch of data, so you're just processing a stream of data in a series of chunks. 
- On a **streaming** data pipeline on the other hand. You're ingesting and providing data to downstream systems in a continuous near real-time fashion. When I say near real time, I mean, you're making data available to downstream systems a short time after it's produced. Possibly less than a second later. 

In the simplest sense, you can think of a streaming system as being composed of: 
1. **a producer**, 
   The data source, e.g. the click stream data coming from an application or measurements coming from an IOT device)
2. **a consumer**, 
   E.g. A service or an application that will process the data, or it could be a data lake or a warehouse. 
3. and a **streaming broker**
   In between the producer and consumer is your streaming broker that coordinates data between producers and consumers
- **use case**,
   Downstream of the consumer might be some real-time analytics or machine learning end use case. 

![[Pasted image 20251012120004.png]]

#### Streaming Frameworks
![[Pasted image 20251012131752.png]]
In the early to mid 2010s, the popularity of working with streaming data exploded with the emergence of **Kafka** as a highly scalable event streaming platform and other stream processing frameworks such as **Apache Storm** and **Samza** for streaming and real-time analytics. These technologies allowed companies to perform new types of analytics and modeling on large amounts of data, like user aggregation and ranking and product recommendations. 



### Lambda Architecture (Legacy)

This new demand for streaming data solutions didn't mean that batch processing went away. Instead, it meant that data engineers needed to figure out how to reconcile batch and streaming data **into a single architecture**. What's known as the *Lambda architecture* was one of the popular early responses to this problem. 

![[Pasted image 20251012132405.png]]

In a Lambda architecture, you have **batch**, **streaming**, and **serving** **systems**, operating independently of each other, and the source system is simultaneously streaming data to two destinations: 
- one for stream processing, where the process data might be stored in a no SQL database, and
- one for batch processing, which might use a data warehouse to transform and store the processed and aggregated data for analytical purposes. 

The **serving** layer in this architecture then provides a **combined view** by aggregating query results from the batch and streaming layers. 

I wanted to mention the Lambda architecture here just so you're aware of it, but this architecture comes with **various challenges and issues,** like **managing parallel systems** with different code bases among other things. In many ways, technology and practices have moved beyond the Lambda architecture.

### Kappa Architecture (Legacy but newer)
One of the original authors of Apache Kafka, J Kreps, propose an alternative to Lambda Architecture, called **Kappa architecture**,  as a response to the shortcomings of the primer. 

![[Pasted image 20251012132757.png]]

The central idea for Kappa architecture is to **use a stream processing platform as the backbone for all data handling, ingestion, storage, and serving**. This facilitates a **true event-based architecture**, meaning that rather than waiting for systems to periodically check for updates when things happen and when data is produced, information is _automatically sent to relevant consumers_ that need the update so that these consumers can react more immediately to this information. 

- With the stream processing platform as the backbone, you can apply **real-time processing** by reading the live event stream. 
- At the same time, you can configure the stream processor to **retain** a certain amount of historical data as it reads from the live stream. 

This effectively allows you to apply batch processing when you want to by replaying large chunks of the retained data from the same data stream.


Well, Lambda has fallen out of favor and Kappa was never widely adopted. Both of these architectures provided inspiration and groundwork for overcoming the central challenge of unifying batch and streaming data processing. 

### Unified Batch & Streaming
One of the central problems of managing batch and streaming processing is **unifying multiple code paths**. Today, engineers seek to solve this in several ways. 

- Google developed the **data flow model** and the **Apache beam framework** that implements this model. The core idea in the data flow model is to **view all data as events**. 
![[Pasted image 20251012133419.png]]

- Ongoing real-time event streams contain unbounded data. 
- Data batches are simply **bounded event streams**, and the boundaries provide a natural window, and so real-time and batch processing can happen in the same system using nearly identical code. 
![[Pasted image 20251012133553.png]]

Apache Flink and other stream processing tools are widely used these days, and we'll take a look at these and similar tools in this course. 



In data engineering today, the philosophy of batch as a special case of streaming is now more pervasive than ever. 

In your work as a data engineer, you can expect to be confronted with the challenges of managing both batch and streaming pipelines. As you face these challenges, you'll need to keep the principles of good data architecture top of mind as you choose the components of your systems, build for flexibility and scalability, and anticipate potential failure modes. 



## Architecting for Compliance

One thing you need to be thinking about, no matter what system you're building is **compliance**. 

In short, compliance means ensuring your data systems are in compliance with laws, regulations, and your own organization's privacy agreements, and terms of service policies. 

### GDPR (General Data Protection regulation)
One big regulation is the **General Data Protection regulation**, or GDPR, that was enacted in the European Union in 2018. In short, the GDPR is all about the protection of privacy and personal data **for individuals**. But what constitutes personal data under the GDPR is relatively broad, including not only personal identifiable information, or PII, but also other information that could collectively be used to identify an individual. 

To be in compliance with a GDPR, you need to ensure that **you have**
- **the appropriate consent from** the individuals you're collecting data from, 
- as well as **the ability to delete the data in a timely manner**. 

If an individual wants to have their data removed from your systems now, you might be thinking, what if my company isn't based in the EU? Or what if we don't serve customers in the EU? Well, technically, yes, the location of your company and your customers will at least play some part in determining whether the regulations apply to you. 

However, since the enactment of the GDPR, dozens of countries around the world, as well as individual states within the US, have adopted similar regulations. 

#### Forward-thinking
As a data engineer, you'll be responsible for **building systems that not only comply with today's regulations, but also those of tomorrow**. And those could be new laws that are enacted where you currently operate or regulations already in place in the areas your company expands to. In the future. It'll be your responsibility to keep your systems up to date, to stay in compliance with the set of regulations that apply to your business. The smart approach is to build systems that are in compliance with modern data protection regulations, like GDPR, even if your local regulations are less stringent. And to build flexible, loosely coupled systems that allow you to adapt to with regulatory changes. 

#### Industry Regulations
Apart from the **location** of your company and your customers, **the industry** you operate in might also come with its own set of regulations. If you work with healthcare data in the US, for example, you'll be required to adhere to the Health Insurance Portability and Accountability Act, or the **HIPAA** law, regarding sensitive patient data. Similar laws have been enacted regarding medical data in many countries around the world. 

Or if you work with financial data in your organization, you would need to adhere to the **Sarbanes Oxley** act in the US or similar laws elsewhere that mandate certain financial reporting and record keeping practices. 

So the main takeaway here is that no matter where you're located in the world or what industry you're in, there are laws and regulations that will apply to the systems that you build. As a data engineer, one way you can deliver value for your organization is to avoid the lawsuits and fines that come with failing to adhere to the necessary regulations. 


We won't be spending much time on the details of regulatory compliance in the rest of these courses, but I wanted to at least make you aware of this aspect in your role as a data engineer so you can keep it in mind along with the other principles of good data architecture.

## Choosing Tools and Technologies

In the field of data engineering, there's no shortage of options when it comes to tools and technologies to get the job done. Quite the opposite, in fact. As data engineers, we suffer instead from an embarrassment of riches. Whether you're considering solutions for ingestion, storage, transformation, or serving, you'll be faced with options, including open source, managed open source, proprietary software services, and much more. 

As you face these decisions, it's important to **keep your end goal in mind**, namely delivering high quality data products that meet the requirements of your end users. In other words, 
- Your **data architecture** is the **what**, the why, **and** the **when** of achieving the data needs of the business. 
- The **tools and technologies** you choose to make that architecture a reality is the **"how"**. 

At this point, you might be thinking, well, sure, that sounds logical. Choose the tools that will make for a successful outcome. That's great. Thanks, Joe. 

Well, unfortunately, there are a number of ways that this can go wrong, and that's what we're going to talk about in this lesson so that you're set up for success. 
- We'll be looking first at **location**, namely the trade-offs between building your systems on-premises, on the cloud or some hybrid of the two. 
- Then we'll look at c**ost optimization** and how you can think about whether to build your own tools for certain things or purchase off the shelf solutions, considering things like your team's size and capabilities and what sorts of activities that really drive value in your business. 
- We'll talk about building for the needs of today, while also having an eye towards the pot**ential future needs** of your organization. We'll be discussing all these things in the context of the principles of good data architecture that you saw in the previous lesson and the undercurrents of the data engineering life cycle that you saw last week.

### Location
Not that long ago, maybe just two decades or so in the past, building on premise data systems was really your only choice for any kind of data storage or processing needs. This was simply because modern Cloud data platforms didn't exist yet. 

**On-Pemises**
On-premises system is one where the company **owns and maintains** the hardware and software for the entire data stack. This means that a company is operationally responsible for 
- Provisioning
- Maintaining
- Updating
- Scaling
their hardware and the software that runs on it. 


**Cloud**
Nowadays, many companies build their entire data systems on the Cloud. With Cloud data systems, the Cloud provider, like AWS, for example, is responsible for building and maintaining the hardware and data centers to serve client needs. 

If you are building your data systems on the Cloud, you are essentially **renting the compute and storage resources needed for your system**. 

- The nice thing about Cloud computing and storage is that you can easily scale to meet demand or scale back down to save on costs when you don't need it. 
- You don't need to maintain or provision any hardware, and you can change your mind relatively easily about exactly what kind of tools or technologies you want to use in your system. 
- 

While many companies are now choosing to build data systems entirely **on the Cloud**, other still maintain **on-premises** systems, or some kind of **hybrid** system with some components on premises and others on the Cloud. The momentum in the industry is definitely in the direction of more companies choosing Cloud over on-premises data systems. Or migrating from on premises to the Cloud. 

![[Pasted image 20251012142243.png]]

This is because of all the obvious advantages the Cloud offers in terms of **flexibility and scalability**. However, there are some companies that either choose to or are required to keep some or all their data systems on premises due to the nature of their business or regulations or security and client **privacy concerns**. As a data engineer today, it's possible that you'll work at a company that has some on-premises systems or one that is in the process of migrating from on premises to the Cloud. 

In these courses, we'll be focused exclusively on building data systems on the Cloud. This is because for the vast majority of business use cases today, building your data systems on the Cloud is the best choice. The industry is moving in the direction of more Cloud and less on-premises. As an aspiring data engineer, I believe your time will be best spent learning about building data systems on the Cloud. 

### Monolithic vs Modular Architecture

#### Monolithic Systems
![[Pasted image 20251012142405.png]]

Monolithic systems are self-contained systems that are made up of **tightly coupled components**. In software development, monolithic architectures have been a technology mainstay for decades, where large teams work together to deliver a **single working code base**. All the components of that software product code base would be built and deployed as a single application. 

**Advantages of Monolithic Systems**
One advantage of a monolithic system is **simplicity**. It has all the functionality in one place. This means that it's easy to understand a monolithic system. Instead of dealing with dozens or hundreds of technologies, you have just one technology and typically one principal programming language. 

Monoliths are an excellent option if you want **simplicity** and **reasoning** about your architecture and your processes. 

**Disadvantages of Monolithic Systems**
However, monoliths are **very hard to maintain** as they grow, since a monolith consists of **tightly coupled components**. If you need to update one component, you may have to update other components as well. Oftentimes, a whole application has to be rewritten. 

>	E.g., I once worked at a company that had a monolithic ETL pipeline that took at least 48 hours to run. Now, if anything broke anywhere in this pipeline, the entire pipeline process had to be restarted. Meanwhile, anxious business users downstream were waiting for the reports, which were already two days late by default, and often arrived much later. The team responsible for this pipeline desperately wanted to replace this pipeline, but it would have required weeks of downtime for the entire system. They limped along and accepted sub optimal performance because updating the system was just too daunting and expensive of a task.* 

![[Pasted image 20251012143221.png]]

#### Modular Systems
By contrast, **modular systems** consists of **loosely-coupled components**. Instead of relying on a massive monolith that combines all the functionalities of an application, modular systems rely on breaking the application into **self contained areas of concern**. In software development, truly modular systems emerge with the rise of **micro services.** With micro services instead of combining the components that correspond to multiple services into a single deployable entity, each service is **deployed as a single unit**. Modern data engineering. 

Data processing technologies have shifted toward a modular model by providing strong support for **interoperability**. This means that most data processing tools available today can be easily integrated with tools that support the other stages of the data engineering life cycle. 

>	_For example, data stored in object storage in a standard format such as Parquet can be paired with any processing tool that supports the Parquet format._ 

The ability to swap out tools as technology changes is invaluable. It helps you build good data architecture by enabling flexible and reversible decisions and continuous improvement. 

![[Pasted image 20251012143247.png]]


### Cost Optimization and Business Value

At every stage of the data engineering life cycle, you'll have multiple tools and technologies to choose from in order to get the job done. Each of these choices **comes at a cost**, and I'm not just talking about the **price tag** and the software or service you're subscribing to. There's also a cost associated with **implementation**, namely **paying a team** to spend the time it takes to stand up and maintain the system, and there's also **opportunity costs**, meaning in choosing one tool, you are at least for a time for going the opportunity to choose another. And so your tool and technology choices will significantly impact your budget. 

As a data engineer, your job is to provide a **positive return on the investment your organization makes in its data systems**. In this video, we'll look at costs through three main lenses. 

I'll first focus on the total cost of ownership, or TCO for short. After that, we'll take a quick look at the total opportunity cost of ownership or TOCO or TOCO for short. Finally, we'll revisit FinOps, which we touched on briefly last week.
![[Pasted image 20251012143709.png]]


#### TCO (Total Cost of Ownership)

**Definition:** The total estimated cost of a solution, project, or initiative over its entire lifecycle.

The term TCO is not specific to data engineering, but rather it's a general business term used to describe the total investment in some project, including the direct and indirect costs of the products and services you're using.

Direct Costs
- Salaries
- Cloud bills
- Software Subs

Indirect Costs (Overhead)
- Network downtime
- Ongoing IT support
- Loss of productivity

**CAPES vs OPEX**
Hardware and Software costs fall into two groups:
- Capital Expenses (Capex): The payment made to purchase long-term fixed assets
- Operational Expenses (Opex): The expense associated with running the day-to-day operations



#### TOCO (Total opportunity Cost of Ownership)

**Definition:** The cost of *lost* opportunities that you incur in choosing a particular tool or technology.

Can be harder to quantify, but it essentially means that any choice you make inherently excludes other possibilities.


In order to ensure that your total cost of ownership is minimized, you'll need to build flexible and loosely coupled systems that are easy to update as your data needs change and the landscape of tools and technologies evolves.

![[Pasted image 20251014210701.png]]

In order to do this, it's important to distinguish the **Immutable technologies** versus the **Transitory technologies**. In other words, you need to identify which technologies will stick around more.

- **Immutable technologies:** Those that have stood the test of time. In Cloud storage, these are things like object storage and networking. Or as another example, SQL as a query language has been around for decades and isn't going away any time soon.
- **Transitory technologies:** Are those that are new at the cutting edge and in areas of the data stack that are rapidly evolving, like stream processing, orchestration, and AI, for example. 


#### FinOps 
**FinOps** as a concept is closely related to TCO and TOCO. FinOps is about minimizing the costs associated with your data systems, your TCO, and your TOCO, while simultaneously maximizing your opportunity for revenue generation. 

How do you do that? 
In short, you could choose Cloud based services that allow you to take an OpEx-first approach with flexible pay as you go technologies as well as modular options that allow you to iterate, improve, and grow quickly. 


#### Building vs Buying
Building technologies from scratch when off the shelf solutions already exist can amount to reinventing the wheel, so to speak. You'll hear me refer to this activity as undifferentiated heavy lifting in the sense that it's hard work, and it probably doesn't ultimately add value for your organization.

When it comes to existing solutions, these include fully Open-Source options, as well as commercial Open-Source options from vendors, which are essentially a managed version of some Open-Source tool. Then there may also be proprietary non Open-Source software and services to choose from. When it comes to choosing between these options, there are several key parameters you need to consider.

- Does your team have the bandwidth and capabilities to implement an open source solution?
- Are you a small team? Could using a manage or proprietary service free up your time?
- How much are licensing fees associated with managed or proprietary services?
- What is the total cost to build and maintain a system?
- Do you get some advantage by building you own system comapred to a managed system?
- Are you avoiing unifferentiated heavy lifting?

My suggestion would be to first look at Open-Source or commercial Open-Source solutions, and if you can't get what you need, look at buying a proprietary solution. Plenty of great modular services are available to choose from in either case. This will allow your team to focus on the unique opportunities that provide the most value to your organization.


## Server, Container, and Serverless Options

To host any software application, you need a server, which is essentially a computer. Or a collection of computers that powers your application by providing CPU, memory or ram as well as disk storage and maybe a GPU and networking.

Servers provide computing resources over a network, most often through the Internet. When it comes to cloud tools, depending on the service you're considering, you may be required to set up and manage the compute resources required to run the application. And in other cases, you may be able to choose between one or more of the following three computing options.

**Server**
 If you choose to go with the server version of a service, you will be **responsible for setting up and managing the server**, such as an Amazon **EC2** instance that the service will run on. 

> 	Including: 
		- updating the operating system, 
		- installing or updating packages, 
		- software packaging, 
		- networking, 
		- scaling and 
		- security.


**Container**
In contrast to a server, a container is a **more modular unit** that **wraps up your code and all its dependencies into a package** that can run on a serveR, whereas a traditional virtual machine wraps entire operating system. 

A container is more lightweight as it just packages up in an **isolated user space** such as a file system and a few processes. So for a containerized solution, you would still be responsible for setting up the essential elements of the application code and dependencies. But the underlying operating system, networking and all the rest would be provided apart from server and container options for services. It's becoming increasingly common in the world of cloud data tools to encounter the terminal serverless to describe a particular service.


**Serverless**
The term serverless doesn't actually mean there is no server. It just means that setting up and maintaining the server is not your responsibility for that particular service. You can interact with the application without managing the servers behind the scenes of or worrying about package installations and dependencies. 

So the server is essentially hidden from you. Typically, **serverless technologies run on containers**, so these services can **automatically scale**. They have **availability** and **fault tolerance** built in, and they offer **pay as you** go billing. 

**But in the case of serverless services, the containers they run on were also abstracted away.** In this way, serverless technologies can allow you to spend less time worrying about your compute infrastructure and more time focusing on developing data products.



## How the undercurrents impact your decisions

Last week we looked at each of these six undercurrents as they relate to the Data Engineering Lifecycle:

- Security
- Data Management
- DataOps
- Data Architecture
- Orchestration
- Software Engineering


**Security**
- Use tools from reputable sources.
- Research where your tools come from - if open-source it could contain spyware which is the last thing you'd want for sensitive data.

Data Management
- Ask the community / company: How are data governance practices are implemented by the tool provider?
	- How does the company deal with breaches?
	- What data protection does it provide?
	- How does the tool comply with GDPR
	- How does it handle data quality?

**DataOps**
- What featyres does the tool offer in terms of automation and monitoring?
- Check company's SLAs (Service Level Agreement) that describes their guaranties in availability and reliability

**Data Architecture**
- Does the tool provide modularity and interoperability with other tools?
- Flexibility / Loose coupling

**Orchestration**
- Apache Airflow is the default but there are also other tools that may be better for a specific industry / case.
- This field is evolving rapidly so be on the lookout

**Software Engineering**
- The question here is: How much do you want to do?
- Build vs Buy
- Avoid undifferentiated heavy lifting
- Check open-source tools first (best practice)


## Intro to the AWS Well-Architected Framework

![[Pasted image 20251014215700.png]]

This framework consists of a set of principles and best practices that will help you build scalable and robust architectures on AWS. The well architected framework complements the set of principles and practices that we've already been talking about in this course. In fact, in writing the fundamentals of data engineering book and articulating the key principles we felt were most important for designing good data architecture. My co author Matt Housley and I took inspiration from the AWS well architected framework as well as other sources.

If you do a quick search for the AWS well architecture framework, you'll find all kinds of great resources. Including white paper's focus on specific use cases and a tool that AWS provides to help you in applying this framework.

There are just so many options to choose from and different ways you can build out solutions on AWS. **So how do you know if you're getting it right?** Well, that's what the AWS Well-Architected Framework is all about, evaluating and improving solutions so you can get it right when you're building on AWS. 

AWS has constructed the Well-Architected Framework, which comprises a set of **best practices** and **core strategies** for architecting systems in the cloud.

## AWS Well-Architected Framework Key Pillars

The AWS Well-Architected Framework includes six key pillars: operational excellence, security, reliability, performance efficiency, cost optimization, and sustainability.
![[Pasted image 20251014220124.png]]


**Operational Excellence**
This pillar is focused on how you can develop and run your workloads on AWS more effectively, monitor your systems to gain insight into your operations, andcontinuously improve your processes and procedures to deliver business value.

**Security**
 This pillar is focused on how to take advantage of cloud technologies to protect your data, systems, and assets. You need to employ proper tools to secure your systems, as well as encourage a culture of security on your team.
 
**Reliability**
Reliable systems are those that perform their intended function correctly, consistently, and can recover quickly from a failure. So this pillar is about everything from designing for reliability to planning for failure and adapting to change. 

**Performance Efficiency**
The performance efficiency pillar is focused on taking a data-driven approach to building high performance architectures. When it comes to your system's performance efficiency, you'll be evaluating the ability of a set of computing resources to efficiently meet your system requirements, as well as how you can maintain that efficiency as demand changes and technologies evolve.

**Cost Optimization**
This one is pretty straightforward and closely related to the points Joe made earlier this week about embracing FinOps. Simply put, cost optimization means building systems to deliver maximum business value at the lowest possible price point, and AWS provides a range of services, including the AWS Cost Explorer and Cost Optimization Hub, where you can make comparisons and get recommendations about how to optimize for cost for your systems.

**Sustainability**
While things like performance, scalability, security, and cost might be top of mind when building out data systems, it's also important to consider the environmental impact of your workloads that you're running on the cloud. The sustainability pillar is focused on reducing energy consumption and increasing efficiency across all components of your system.


These six pillars of the Well-Architected Framework will not provide specific designs for you to just copy and apply to your solutions. Instead, you can think of them as a set of principles and questions that will help you have productive conversations about your existing solutions and help you design and operate reliable, secure, efficient, cost-effective, and sustainable systems in the cloud. It's almost like having access to your own personal AWS Solutions Architect, who can help you think through the pros and cons of different architecture choices. 

You can follow the links in the resources section at the end of this week to learn more about each of the pillars and explore the Well-Architected tool that allows you to evaluate your own architectures for potential risks and opportunities for improvement. 


**Lenses**
There are also domain-specific applications of the Well-Architected Framework called Lenses that you can explore. A lens is essentially an extension of the AWS Well-Architected Framework that focuses on a particular area, industry, or technology stack, and provides guidance specific to those contexts. Each lens has its own set of questions, best practices, notes, and improvement plan. 

In particular, I'd recommend you to check out the **Data Analytics Lens**, which is focused on data-specific considerations. The Data Analytics Lens will walk you through evaluating your data architectures for scalability, security, performance, and cost. It can help you evaluate your current data architectures, identify areas for improvement, and implement strategies that align with industry best practices. 


# C1W3_L1

Tools Used:
- Amazon Well-Architected Framework
- EC2
- Amazon CloudWatch

Your company **wants to serve some embedded dashboards** to its **clients**, and share some analytical data through third-party data sharing platforms.

- **EC2:** To serve this data to external clients, let's assume you built a web application that is running on these **EC2** instances. In this lab, you will work with this web application to ensure that it's capable of **scaling** to meet your client's needs, uses computing resources efficiently, and is designed in a **secure and reliable way**. 
- **Amazon CloudWatch:** You'll also **monitor** the performance of this application using Amazon CloudWatch, while applying the principles of good data architecture and following the **AWS Well-Architected Framework**. 

Before we start with the lab, let's take a closer look at these EC2 instances to understand the underlying architecture of the web application. Here's the architectural diagram of the web application:

![[Pasted image 20251014221642.png]]

This web app is designed based on a three-tier architecture that consists of a data tier, a logic tier, and a presentation tier. This type of three-tier architecture is a common way of deploying web application solutions.

- The **S3 bucket** you see here on the left represents the **data tier** from which your web application extracts data. 
- The **EC2 instances** and the **load balancer** in the middle represent the **logic tier**, which hosts the application logic that processes the client's requests. 
- The logic tier queries data from the data tier, and returns the results to the **presentation tier** here on the right, which consists of an interface that displays the results to the clients who interact with the web application through their devices. 

### Logic tier
![[Pasted image 20251014221941.png]]

Let's zoom in on the logic tier in the middle. There are two main components here, 
1. the Amazon EC2 Auto Scaling Group and
2. the Application Load Balancer, or ALB. 

**Auto Scaling Group**
The Auto Scaling Group consists of a collection of EC2 instances that run the same application logic. They are used to increase the computing capabilities of the application. So instead of having a single EC2 instance that processes all requests from clients, these requests are distributed across the EC2 instances. *Auto Scaling means that the number of EC2 instances can increase or decrease based on the number of client requests to the web app.* The group is configured to start with two instances, each launched in a different availability zone to enhance the availability and reliability of the application. 

**Application Load Balancer**
The Auto Scaling Group needs to be associated with an Application Load Balancer that **helps distribute the incoming traffic across the EC2 instances** and **serves as a single point of contact** for the clients. In this lab, you'll mainly interact with the computing resources on which the application logic is run. 

- You'll simulate traffic to the web application to assess its scalability, then monitor computing resources and network activity on the application using Amazon CloudWatch. 

- You will configure the EC2 instances to enable performance efficiency and cost optimization and adjust the security options of the Load Balancer to control the inbound traffic to the application. 


# Week 3 Resources

**Optional reading and reference material:**

- Chapters 3 and 4 of [Fundamentals of Data Engineering](https://go.redpanda.com/fundamentals-of-data-engineering)
    
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/).
    - [Documentation](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)    
    - [AWS Well-Architected Labs](https://www.wellarchitectedlabs.com/)
        
- Jay Kreps, [“Questioning the Lambda Architecture,”](https://www.oreilly.com/radar/questioning-the-lambda-architecture/) O’Reilly Radar, July 2, 2014.
    
- [“A Brief Introduction to Two Data Processing Architectures — Lambda and Kappa for Big Data”](https://towardsdatascience.com/a-brief-introduction-to-two-data-processing-architectures-lambda-and-kappa-for-big-data-4f35c28005bb) by Iman Samizadeh
    
- An article about [Amazon's API Mandate](https://medium.com/api-university/the-api-mandate-install-api-thinking-at-your-company-4335433b7d0b)

