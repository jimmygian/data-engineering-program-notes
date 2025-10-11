
## Data Generation in Source Systems

In your role as a data engineer, you'll be consuming data from various sources.  These systems are typically created and maintained by other teams, including software engineers from other internal departments, external vendors or third party platforms. As a data engineer, they're largely out of your control. But it's important to understand how these systems work because data pipelines you build will rely on the data generated from these sources.

Let's start by taking a closer look at a few of the common source systems you'll be working with as a data engineer.

**Databases**
* Relational Databases
* NoSQL Databases

![[Pasted image 20251008211810.png]]
These databases might be part of the back end of a web or mobile application, or you could be storing data from some other system


**Files**
![[Pasted image 20251008211900.png]]
You might also be consuming data in the form of files, like text files, audio files like Mp3s, or even video or other types of files. While individual files like these might not sound like something you could call a source system, you'll find that in many cases, in your work as a data engineer, you need to download or be given access to files to begin your work.


**APIs**
![[Pasted image 20251008211932.png]]
Another common source system that you'll encounter as a data engineer is an API, which is short for application programming interface. Simply put, an API allows you to issue a web request for data and then get that data back in a certain format, such as XML or JSON.


**Data Sharing Platforms**
![[Pasted image 20251008212057.png]]
You might also source data through a sharing platform, which is something that organizations may set up to share data internally or with a third party.


**IoT devices**
![[Pasted image 20251008212123.png]]
IoT (Internet of Things) devices, as I mentioned earlier, represent yet another type of source system that is becoming increasingly common. With this type of source system, you might have many individual devices, what's known as a swarm of IoT devices, streaming data in real time. This streaming data is typically sent to a database, and the source system owner might make that data accessible via an API or a data sharing platform. 

In other cases, you might need to ingest and combine all those individual streams of data for use in your own downstream workflows.


**Source Systems Unpredictability**
In a perfect world, the source systems you depend on would deliver the data you need in a consistent and timely matter, such that you could build downstream systems that rely on the predictability of that source system. In the real world, however, **source systems are unpredictable**. These systems sometimes go down or the team managing the system changes the format or schema of the data, or maybe the schema stays the same, but the data itself changes.
![[Pasted image 20251008212356.png]]
When you're accessing data from source systems is essential to understand: 
* How those systems are set up  
* What changes in the data or system you can expect. 

This means that as a data engineer, you'll be more successful if you **work directly with source system owners** to understand: 
- how those systems work, 
- how they generate data, 
- how that data may be subject to change over time, and ultimately 
- how those changes will impact the downstream systems that you build. 

Developing good working relationships with the stakeholders of source systems is an underrated yet crucial part of successful data engineering.



## Ingestion

As a data engineer, you'll build architectures that begin with the step of ingesting data from source systems, which means moving raw data from source systems into your data pipeline for further processing.

In my experience, source systems and data ingestion represent the **biggest bottlenecks** of the data engineering life cycle.

 If you started by working directly with source system owners to understand how those systems work, how they generate data, how that data may be subject to change over time, and ultimately how those changes will impact the downstream systems you build, then you'll be well positioned to avoid common pitfalls in the ingestion phase.

#### Batch vs Streaming
One of the critical decisions that you need to make when designing data ingestion processes is how frequently you need to ingest the data, meaning how often you need to move data from these source systems shown here on the left, into your data pipeline for further processing. 
* You might choose to ingest data every so often in **batches**, like once every hour or once a day. 
* Another approaches becoming increasingly common is to ingest data as a **constant stream** of events in near real-time.

![[Pasted image 20251008213209.png]]

You can think of data being produced **as a continuous series of events** (e.g. clicks on a website or sensor measurements). Out in the world, events like these are happening continuously. In a sense, you could think of nearly all data as being **streamed at its source**. Batch ingestion is just a convenient way to process the stream of events in large chunks, either on a predetermined time interval or as data reaches a pre set size threshold. For example, you could handle an entire day's worth of data in one single batch.

For a long time, batch processing was the default way to ingest data and still remains a practical and popular way to ingest data, particularly in cases where the data is used in analytics and machine learning. Today, there are more options for ingestion. With streaming ingestion, you're ingesting and providing data to downstream systems in a continuous, near-real-time fashion. (Possibly less than one second later). In this case, you need to use specific tools, such as an **event streaming platform** or a **message queue** to continuously ingest streams of events.

**Considerations**
Streaming ingestion is not necessarily the best choice for all use cases and there are significant trade offs to consider when deciding whether streaming ingestion is an appropriate choice over batch ingestion. For working on a stream processing solution, you should be asking yourself, things like:
* What actions can you take on real time data that would be an improvement over batch data? 
* Would streaming ingestion costs more than batch in terms of time, money, maintenance, and potential downtime? 
* How will choosing stream ingestion over batch ingestion or vice versa influence the rest of your data pipeline? 

Adopt a streaming ingestion system **only after** you've taken the time to identify a **business use** **case** that justifies a trade offs of using it over batch.


It's also important to note that streaming ingestion generally coexists with batch processing. For example, machine learning models are usually trained on a batch basis. But that same training data might have been originally ingested via streaming because of some separate goal of your architecture like real time anomaly detection. Rarely do data engineers have the option to build a purely streaming data pipeline with no batch components. Instead, you'll choose where the boundaries between batch and streaming will occur. Beyond just deciding between a batch versus streaming approach, there are other important nuances to the ingestion stage, like how you might use change data capture or CDC, for short to **trigger** certain ingestion processes based on data changes in the source system, and whether you'll be adopting a **push or pull approach**, which is to say whether a source system will be pushing data to you or you'll be actively pulling it from the source. We'll get into all these details and more throughout the specialization. 


## Storage

With almost any action you take on a digital device or online, you're interacting with data storage systems, and you might run into the limits of the storage systems like running out of space to store pictures on your phone or attempting to send files that are too large.

The **function, performance, and limitations** you encounter will have a lot to do with how those systems are set up.

Similarly, in your work as a data engineer, the function, performance, and limitations of the systems you build will have a lot to do with the storage solutions you choose to support those systems.

**Raw Hardware Ingredients**
* Solid-state storage 
	* Flash memory cards
	* SSD drives
* Disk Storage 
	* Magnetic Disks - the Backbone of modern data storage systems
	* 2-3 times cheaper
* RAM
	* Faster read and write speeds
	* 30-50 times more expensive than solid-state storage
	* More Volatile
* Floppy Disks

**Cloud Storage**
![[Pasted image 20251008215555.png]]
The physical components of storage are just one aspect of how data storage is implemented throughout your architecture. Modern and Cloud data storage systems are commonly distributed across multiple clusters and data centers.

This means that things like networking, CPU, serialization, compression, and caching are all critical raw ingredients for storing data in modern data systems.


 As a data engineer, you typically won't be responsible for managing the granular details of how your data moves and is stored across a network of data centers and physical storage devices. Instead, you'll work with storage systems like database management systems or object storage platforms like Amazon S3. 
 
 Depending on the requirements for your architecture, you might also be working with systems like Apache Iceberg or Hoody, cache and memory based storage systems, or streaming storage. 
 ![[Pasted image 20251008215848.png]]
 All of these data storage systems are built on top of the physical and other wrong ingredients of storage that exist inside servers and clusters, allowing these systems to ingest and retrieve data using different access protocols.
![[Pasted image 20251008215908.png]]


**Storage Abstractions**
![[Pasted image 20251008215925.png]]
In most modern architectures, data will pass through magnetic storage, solid state drives and memory as it works its way through the various processing phases of the pipeline. With the storage abstraction tools, rather than worrying about the details of how the underlying components are arranged, you'll choose various configuration parameters that allow you to meet your system requirements in terms of latency, scalability, and cost.


**Hierarchy**
![[Pasted image 20251008220303.png]]
* at the bottom, you have the raw ingredients of data storage, including the various physical components like disc, RAM, and solid state storage, as well as the various non physical raw ingredients of storage, like networking and serialization.
* Then on top of that, you have storage systems that are built from these raw ingredients, and these include database systems, object storage, and much more. 
* Then finally, at the top of the hierarchy, you have storage abstractions, which are combinations of storage systems that allow you to achieve your high level data storage needs without worrying about as many of the low level details.

As a data engineer, it's entirely possible that you will spend much of your time operating at or near the top of this hierarchy, meaning that you won't be required to think about the details of exactly how your data is moving between different storage components and systems. However, you will be most effective in your work. If you take the time to understand the inner workings capabilities and limitations of your entire storage solutions right down to the wrong ingredients.



## Queries, Modeling, and Transformation

The transformation stage of the data engineering life cycle is really where you, as a data engineer, start to add value. This is because the stage that comes before transformation, namely ingesting and storing raw data from source systems, doesn't directly add any value from downstream stakeholders.

Transformation is the "turn it into something useful stage".
![[Pasted image 20251008220741.png]]

We call this part of the data engineering life cycle "transformation". But in reality, the stage is made up of three parts : 
1. queries, 
2. modeling, and 
3. transformation. 

I include queries and modeling as separate from transformation here because these are critical components of any data pipeline that really add value when done well and present risks when done poorly. 

1. Queries: Issuing a request to read records from a database or other storae system.
	* Quering may involve Data Cleaning, Data Joining, Data Aggregating.
	* There's more than one way to write queries and poor queries can create problems. For example, impacting the performance, causing row explosion in your database, and more.
	* Most data engineers know how to query but are unaware of the underlying functions of SQL and how to optimise it.
2. Data modeling: Choosing a coherent structure for your data to make it useful for the business.
	* Data normalization and denormalization to model the data in a way that they can be queried quickly.
3. Data Transformation: Data manipulated, enhanced and saved for downstream use.


## Serving Data
Once you've ingested, transformed, and stored your data, you're ready for the final stage of the data engineering lifecycle -- Serving data. 

This stage is about more than just making data available. It's really when you give stakeholders the opportunity to extract business value from data.

Generally, data has value when it's used for practical end use cases like analytics, machine learning, reverse ETL, or other use cases.

#### Analytics
**Definition:** Analytics is the process of identifying key insights and patterns within data.

As a data engineer, you're almost guaranteed to serve data that powers one or more of the three most common forms of analytics: 
1. business intelligence, 
2. operational analytics, and 
3. embedded analytics.

**Business Intelligence (BI):**
Explore historical and current business data to discover insights

**Operational Analytics:**
Monitoring real-time data for immediate action

**Embedded Analytics:**
External or customer-facing analytics

#### Machine Learning:
With the rise of machine learning in recent decades, it's quite likely that your role as a data engineer will involve serving data for machine learning use cases. And these courses will treat machine learning as separate from other serving use cases simply because it can involve additional complexities that we'll want to look at in detail. For example, with a machine learning use case, you may be responsible for serving feature stores of data that facilitate model training, and you might also need to serve data for real time inference, or support metadata and cataloging systems that track data history and lineage.

#### Reverse ETL
Apart from analytics and machine learning, another common use case for serving data is what's at least today called reverse ETL. 

With reverse ETL, you'll take *transformed data* as well as analytics and perhaps machine learning model output and feed it back into source systems. For example, let's say you've ingested data from a customer relationship management or CRM system, and this might include information like names, contact info, form data, or other relevant client information, and you've then transformed the data into the appropriate format and stored it in a data warehouse. Analysts can then retrieve the data to train a lead scoring model, which is a model that attempts to say which clients are the most promising for various engagements or product offerings. The model results could then be returned to the data warehouse and then pushed back into the CRM as an enhancement of the client data already stored there. 

Now, I'll just say that the name reverse ETL for this process is not so much an attempt to describe what's going on as it is for just a lack of a better name to describe this pretty longstanding process.



## Introduction to the Undercurrents
We've been looking at the data engineering life cycle and how you'll ingest data from source systems, transform it, store it, and serve it to end users. As a field, data engineering is rapidly maturing, whereas just a decade ago, the role of a data engineer was primarily focused on the technology layer. 

The continued abstraction and simplification of tools has expanded the scope of the role. Data engineering now encompasses far more than just tools and technologies.

Modern data engineering incorporates traditional enterprise practices such as data management, and cost optimization, as well as newer practices like DataOps. When it comes to work as a data engineer, there's a set of such practices that will apply to your work across the entire life cycle. 

In the fundamentals of Data Engineering book, we describe these practices as the undercurrents of the data engineering life cycle. These undercurrents include security, data management, DataOps, data architecture, orchestration, and software engineering. The next several videos, we'll take a closer look at each of these undercurrents. After that, you'll begin exploring how the data entering life cycle and undercurrents take shape in real life on the AWS Cloud. Let's get started.


### Security
When you're managing a data pipeline and serving data to end users, you'll need to give access to the data and system resources to other users or applications.

**Principle of Least Privilege**
Key security principle to keep in mind when doing so is the **principle of lease privilege**. This means you give users or applications access to only the essential data and resources they need to do their jobs and only for the duration that's required. 
	_You need to apply the principle of lease privilege, not only to others in your organization but also to yourself. That means, for example, just like you don't give everyone a team administrator access, when it comes to your own work, don't operate from the root shell when it's not necessary and don't use administrator or super user permissions unless it's absolutely essential._

Adhering to the principle of lease privilege means making sensitive information visible to users only when it's absolutely necessary.

 In today's Cloud centric world, security takes on yet another dimension, one that requires you to understand things like:
 * identity and access management, IAM, 
 * encryption methods, and 
 * networking protocols

**Security just in surface**
When it comes to security at the *organizational level*, I've seen that all too often, organizations are set up with a facade of security. Maybe a set of checklist to show they're compliance with regulations or compliance standards, but a deeper spirit of security is lacking in the organization's culture. This approach of adhering to the letter of security without a culture and spirit of security is what I call *security theater*. 

Security emerges from a culture where every team member recognizes the role in protecting data and everyone from top to bottom embraces security as a priority and a habit.



## Data Management

Whatever you're working on as a data engineer, you need to be thinking about *how you work adds value for stakeholders in your organization*.  Data can be an incredibly valuable business asset, **but only when it's managed properly**.

Data management is so important, in fact, that there's an international organization, the **Data Management Association International**, or **DAMA**, dedicated entirely to providing resources for companies and individuals to get data management right. DAMA provides this neat little publication, the Data Management Book of Knowledge or DMBOK for short.

 As a data engineer, you don't need to memorize everything in this book. Instead, you'll focus on a subset of data management tasks, and you'll share the full responsibility of data management with teams including software engineering, IT, and others.
 
**_DMBOK's Definition:** Data management is the development, execution, and supervision of plans, programs, and practices that deliver, control, protect and enhance the value of data and information assets throughout their lifecycles._

As a field, data management consists of many facets and disciplines, each with their own set of responsibilities. This can make the data management environment confusing. The DMBOK breaks down the different facets of data management into **11 data knowledge areas**. These include data governance, data modeling, data integration and interoperability, metadata, security, and more.

![[Pasted image 20251010193142.png]]

As you can see in this diagram, data governance touches all other areas of data management. As it happens, many of the other knowledge areas interact with each other through data governance practices. Here in this video, I'll just focus on data governance as it relates to a lot of the areas that will be important in your role as a data engineer.

According to another book called "_data governance, the definitive guide_": 
_**Data governance** is, first and foremost, a data management function to ensure the quality, integrity, security, and usability of the data collected from an organization._

From this definition, you can start to see that data governance covers a lot of ground, everything from data security and privacy to data quality and usability.


**Data Quality**
For the purposes of this video, I'd like to zero in on data quality, which is closely related to some of the other key terms you've seen here like integrity, usability, and reliability. Data quality is a deep and nuanced topic, but at its core, this concept is relatively straightforward. High quality data is accurate, complete, discoverable, and available in a timely manner.

Beyond that, high quality data represents exactly what stakeholders expect it to represent in terms of well defined schema and data definitions. Data that conforms to the standards of quality is a powerful tool in decision making and adds great value in our organization.


### Data Architecture
You can think of data architecture as a roadmap or blueprint for your data systems. In the first week of this course, we talked about requirements gathering and how to think about taking stakeholder needs and turning those into specific requirements that you can use to make design and technology choices.

 In order to map requirements to a successful design for your system, you need to think like an architect.
 
 In this chapter, I'll briefly introduce some of the key principles that are part of thinking like an architect. Throughout these courses, we'll be revisiting these principles so you can feel confident in your abilities to design and build robust data systems. 
 
_Definition: **Data architecture** is the design of systems to support the evolving data needs of an enterprise, achieved by flexible and reversible decisions reached through a careful evaluation of trade offs._ 

Let's take a minute to unpack that definition. 
* First off, you'll notice that data architecture needs to support the evolving data needs of the organization. In practice, this means that data architecture is an ongoing effort, rather than something you just do once and be done with.
* The next part of this definition says that a good design is achieved through flexible and reversible decisions. This is calling out the fact that the data needs of your enterprise may evolve in ways you hadn't anticipated and that you'll need to update your architecture over time.
* Finally, you'll see that in this last part of the definition. All this is achieved through a careful evaluation of trade offs, these might include trade offs in performance or cost or scalability or other parameters.

Nowadays, with most data architectures being built in the Cloud. You can in some sense, change your mind as often as you want about the technology choices you've made for your architecture.

**Principles of Good Architectures:**
1. Choose common components wisely: 
   Common components are the parts of your architecture that will be used by multiple individuals and teams across your organization. A good choice of common components is one that provides the right set of features for individual projects and simultaneously facilitates collaboration between teams.
2. Plan for failure:
   A good architecture is designed not only for the case where everything is working as expected, but also for when things break.
3. Architect for scalability
   When you build scalability into your architecture, you can be responsive to a changing demand and optimize for cost at the same time.
4. Architecture is leadership
    If you work towards thinking like an architect and seek mentorship from data architects, you will be better able to lead and mentor other team members as your skills develop and you become more senior.
5. Always be architecting
   Architecture design is not something that happens only once. Instead, you'll be constantly evaluating your systems against the evolving needs of your organization and re architecting on an ongoing basis.
6. Build loosely coupled systems
   A loosely coupled system is one that is built from individual components that can be easily swapped out for other ones without having to re architect the whole system.
7. Make reversible decisions
   Choosing to build with easily interchangeable components like this. You're making a set of reversible decisions, which is to say if you change your mind or the needs of your organization evolve, you can easily reverse your prior set of decisions and swap out components of your architecture to meet your new design specs.  
8. Prioritise security
   Security is central to your role as a data engineer. Apply all principles of security you learn in these courses.
9. Embrace FinOps
   FinOps is a movement to bring together the business priorities of finance and DevOps, or in this case, DataOps. On the Cloud, most data systems are pay as you go and readily scalable. By embracing FinOps, you can design your systems to be simultaneously optimized for cost and potential revenue generation


### DataOps

Around 2007, a framework called *DevOps* emerged in software development to break the silos between software development teams who write and test code and the software deployment teams who deploy and maintain code. DevOps borrows from other well-known methodologies including lean and agile, to accomplish things like the removal of bottlenecks, the reduction of waste, and the quick identification of problems, as well as rapid iteration.  The DevOps movement has resulted in increased release cycles and enhanced quality for software products.

As the data field has matured, we've adopted a similar approach, known as **DataOps**, to the development of data products. Similar to how DevOps improve the development process and quality or software products, DataOps aims to improve the development process and quality of data products.

 DataOps is first and foremost a set of cultural habits and practices that you can adopt. These include things like 
 * prioritizing communication and collaboration with other business stakeholders, 
 * continuously learning from your successes and failures, and 
 * taking an approach of rapid iteration to work toward improvements to your systems and processes.

**3 Pilars of DataOps**

![[Pasted image 20251010202623.png]]
 In terms of the *technical elements* of DataOps, there are three key pillars: 
 * automation. 
 * observability and monitoring. 
 * incident response. 
 
These are similar to the core components of DevOps, where the end goal is to provide specific functionality and features in a software product.

#### 1.  Automation: Continuous Integration and Delivery (CI/CD)
In the early stages of developing your data systems, you might choose to _manually_ execute the various tasks in this data pipeline, like manually starting each of these ingestion processes. This could be a reasonable approach to get started quickly and prototype some aspects of your data pipeline in the long run. However, this sort of multistage manual execution will be prone to errors and be inefficient because it requires you to manually run each task with a minimal level of automation.
![[Pasted image 20251010201525.png]]


**Scheduling**
You might choose to take a so called **pure scheduling approach**, meaning that you would set each task in your pipeline to start at a particular time of day. 
* Maybe you start all these ingestion tasks at midnight every night. 
* Then you would estimate how long it takes for all the data to be ingested and loaded into your storage system. 
* Then you could schedule the downstream transformation tasks to start after that, 
* and so on through all the tasks in your pipeline.  

This is called _scheduling_ because you create a schedule to automatically start each of the tasks in your data pipeline.

**Orchestration Workflow**
To take things to the next level of DataOps automation, you could adopt an **orchestration framework** like _Airflow_.

Orchestration frameworks **check the dependencies** between tasks within your data pipeline **before each task is run**. So you can decide the time and frequency you want the first task of your pipeline to start. Then the orchestration framework will automatically start subsequent tasks once the previous ones have been completed successfully.

The orchestration framework can also notify you when there's an error with any of the tasks. So that the downstream tasks that are dependent on the previous ones don't start when they're not supposed to. 

Many orchestration frameworks not only automate the execution of tasks in your data pipelines, but they also enhance the development of these pipelines by enabling _automatic verification and deployment of new aspects_ of your data pipeline -- similar to the CI/CD process for software deployment. 

#### 2. Observability and Monitoring
When it comes to the next pillar of _observability and monitoring_, the main thing you need to keep in mind is that any data pipeline you set up is bound to fail eventually. To quote Werner Vogels, the CTO of Amazon Web Services, _"everything fails all the time"_. This means that if you're not closely observing and monitoring your data systems, you'll be caught unaware when they fail.

In a worst case scenario, you might only become aware of these system failures when your downstream stakeholders discover these problems on their own, for example, in their reports or analytics dashboards. These sorts of failures can be a waste of time and money, lead to ill informed decisions, and ultimately could cost you your job if stakeholders lose trust in your work.

#### 3. Incident Response
The third pillar of DataOps is incident response, which is about using the observability and monitoring capabilities you set up to rapidly identify the root causes of an incident and resolve it as quickly and reliably as possible. As I said before, things will break and it's only a matter of time before they break.

With incident response, it's not just about the technology and tools you use to identify and respond to an issue. It's also about open and blameless communication and coordination of the efforts of members of the data team responding to the incident, as well as others across the organization.

As a data engineer, you should be proactively finding issues before they are reported to you by other stakeholders in your organization.


**End Goal of DataOps**
In DataOps, the end goal is to *provide high quality data products*, where you can think of a data product as any data or data system that you're providing to end users.


### Orchestration
 Like an orchestra, a data pipeline has a lot of moving parts that must be coordinated to get a good result. As a data engineer, you are the conductor in charge of coordinating and managing the tasks in your data pipelines.
 
As previously mentioned, if you're just getting started, maybe as the first data engineer at a small start up, or in the prototyping stages of a new project at any size organization, you might initially set up a data pipeline where you're manually executing each of the tasks at each stage.

**Problems with Pure Scheduling**
Pure scheduling is an approach that has been widely used historically for various data processing tasks. However, you can run into problems with this approach. For example, if the scheduled ingestion task failed to execute, this could cause downstream transformation tasks to fail as well. Or if this transformation task simply takes longer than expected and the next transformation task kicks off before the previous one finishes, you can end up with incomplete or stale or otherwise problematic data being propagated through your pipeline.

**Solution via Orchestration**
Not that long ago, orchestration frameworks that were more sophisticated than pure schedulers were really only available to large enterprises where they had the resources to build their own custom solutions. But nowadays, there are a number of open source orchestration frameworks that make it possible for you to build sophisticated orchestration into your own data pipelines, no matter what size team or company you're working with. The most popular framework right now is Apache airflow, but several other newer open source frameworks such as Dagster, Prefect, and Mage, are gaining traction as well. 

The most popular framework right now is **Apache airflow**, but several other newer open source frameworks such as Dagster, Prefect, and Mage, are gaining traction as well. What these frameworks allow you to do is automate your data pipelines and build in complex dependencies and monitoring capabilities. You could do time based scheduling, if you like, but you could for example, build in a dependency that verifies that this first transformation task has been completed before starting the next transformation task. Or instead of predefining a particular time of day or frequency, you want to start a task, you could have tasks that are triggered by events. For instance, you can trigger this suggestion step to start when there's a certain amount of new data available in the source database. You could set up monitoring within your orchestration framework and trigger alerts to let you know if, for example, this transformation task fails to execute or hasn't been completed by a certain time.

#### Directed Acyclic Graph (DAG)
Many orchestration frameworks require you to set up your data pipeline, as what's known as a **directed acyclic graph**, which is really just an overly complicated term to describe how data flows through your data pipeline.

Let's take a look at what a directed acyclic graph or DAG, for short, would look like for this pipeline we've been talking about.

In some sense, you could say this is already a DAG where each of these icons represents a different task in your pipeline, and the arrows show how data moves from one task to the next.
![[Pasted image 20251010203308.png]]


But now I'm going to modify the visual here just to make it work explicitly like a DAG, as you'll see them represented elsewhere. 

![[Pasted image 20251010203643.png]]

* Data Sources are source systems, e.g. source 1, source 2, source 3, and source 4, and we're ingesting or extracting data from all of these sources, and there's a transformation step happening in flight from source 4. 
* Then, you store all of the extracted data into storage. 
* After that, your pipeline splits, where along this top branch, you have two more transformation steps followed by storage that will serve the machine learning end use case. 
* On the bottom branch, you have a transformation step and then storage to serve the analytics end use case.

"**Directed**"* in _directed acyclic graph_ indicates that the flow of data goes only in one direction.
*"**Acyclic**"* indicates that there are no loops. Data doesn't flow back to a previous step.  
_"**Graph**"_, because it's composed of **nodes and edges**.


### Software Engineering
Of all the undercurrents, perhaps the most straightforward one to wrap your head around is the last one, software engineering. What I mean by that is, as a data engineer, you need to know how to read and write code.

So I don't just mean hacking together some code that does whatever you need it to do right now, but instead to write production-grade code that's clean, readable, testable, and deployable. And so software engineering is the design, development, deployment, and maintenance of software applications. And they're not too distant.

**Definition of Software Engineering:**
Software Engineering is the design, development, deployment, and maintenance of software applications.

**Coding as a Data Engineer**
You'll be required to write core data processing code at all stages of the data engineering lifecycle. From ingestion to transformation and serving, you'll need to be proficient in frameworks and languages such as **SQL**, **Spark**, or **Kafka**. You're also likely to encounter **Python** or maybe Java virtual machine languages like Java or Scala, as well as Bash, for operating at the **command line**. If you focus on building strong foundational software engineering skills, you won't have much trouble moving between languages.

Other efforts you'll be involved in or in the development of so called infrastructure as code, or pipeline as code solutions, which we'll talk about later in these courses.

Apart from these specific instances of where you'll write code in your role as a data engineer, you'll also need to write code for everyday general-purpose problem-solving at all stages of the data engineering lifecycle.


## AWS on Undercurrents

When it comes to how these undercurrents appear on AWS, there are some aspects that are more conceptual and others that are more tool oriented.

* For example, security as a concept will appear in many forms in your work on AWS. 
* While something like orchestration is more about choosing the right tool or service to meet your needs.

##### Security
![[Pasted image 20251010204850.png]]
 Let's start with security. Last week, I mentioned the shared responsibility model as it relates to security of data systems or other applications built in the AWS cloud.
 
 The shared responsibility model says that AWS is responsible for security of the data centers and services they provide. And you are responsible for the security of the systems you build with those resources.

**IAM (Indentity and Access Management)**
Through IAM you can set up roles and permissions which control access to your AWS resources.

* IAM Roles: Give users/applications access to *temporary* credentials
* Provide appropriate AWS API permissions to various tools or data storage areas.

**Amazon Virtual Cloud (VPC)**: Private Internet Connection
**Security Groups**: Instance level Firewalls

##### Data Management
With the undercurrent of Data Management in AWS in these courses, you'll use:
* *AWS Glue
* AWS Glue Crawler
* AWS Glue Data Catalog

These tools will allow you to Discover, Create, and Manage metadata for data stored in Amazon S3 or other storage and database systems.

* Central Formation. This service will let you centrally manage and scale fine-grained data access permissions.
##### DataOps
For DataOps, you'll use:

* Amazon CludWatch that collects metrics and provides monitoring features for cloud resources, applications, and on-premises resources.
* Amazon CloudWatch Logs to store and analyze operational logs.
* Amazon Simple Notification Services will also be used in Course 2
##### Orchestration
We'll be using Apache Airflow within AWS managed systems
##### Architecture
For Architecture, AWS Well-Architectured will be used 
##### Software Development
AWS CodeDeploy will be used.


# C1W2_L1

The main goal of this lab is to help you get started interacting with the data pipeline on AWS and have an end-to-end experience. We'll get more in-depth with all of these tools in the later courses.

## Introduction to the Lab

End-to-end data pipeline in AWS that involves all the stages

* For your source system, you'll be provided with a relational database. 
* After exploring this data source, you'll ingest the data into your data pipeline, where you'll transform and load it into the storage system before finally serving it to your end-users. 
* In the transformation stage, you will convert the structure of the source data into a form that is easier to understand and faster for your end-user to query. 
* The lab contains detailed instructions, showing you how to create and run the resources of your data pipeline.


### Pipeline Scenario

I work as a Data Engineer at a retailer for scale models of classic cars and other vehicles.

My customer stores data for historical purposes, and its customers' information in a relational database that consists of these tables.

![[Pasted image 20251010234414.png]]
https://www.mysqltutorial.org/getting-started-with-mysql/mysql-sample-database/

* Customers, 
* Products, 
* Product Lines, 
* Orders, 
* Order Details, 
* Payments, 
* Employees, and 
* Offices.

You're given access to this database, and you're asked to build a data pipeline to transform this data and serve it to a data analyst on the marketing team. 

The data analyst is interested in looking at historical purchase records to understand, for example, which product lines are more successful and how the sales are distributed across different countries.

Your job will be to extract the data that the analyst needs from the database, transform it into a structure that is easier to understand and faster to query for analytical purposes, and then store it in a separate storage system the analyst has access to.

> Transforming your data into a structure for downstream use like this is known as data modeling, which we'll cover in detail in Course 4 of the specialization. In this lab, the transformation script and structure of the transformed data are provided to you. 


So here's how your transformed data will be structured in the end. This is known as a star schema.

![[Pasted image 20251010224607.png]]

* The middle table contains some measurements related to a **sales order that** the data analyst might be interested in aggregating, such as the total number of sales or the average price.
* The surrounding tables provide more context, like customer locations and order details, enabling more specific aggregations, such as the total number of sales by country or the maximum quantities ordered for each product line.

 In the terminology of a **star schema data model**, this central table is known as the **fact table**, and the surrounding tables are referred to as **dimensions**. Here's the architectural diagram of the pipeline you'll be working with.

Here's the architectural diagram of the pipeline you'll be working with.

![[Pasted image 20251010224847.png]]

- The **RDS MySQL** database instance represents a source system that contains the tables. This resource will be provided to you in the lab. 
- Then you have **AWS Glue**, which is a tool that allows you to ingest data from the source database and apply transformations on the fly to the ingested data. 
- In the lab, you will define a **glue job**, which consists of: 
	- **connecting** to the RDS database, 
	- **extracting** the raw data, transforming the data by modeling it using the provided star schema, and finally 
	- **loading the transformed data** into AWS Object Storage in an **S3 bucket**.
	
The process of extracting, transforming, and then loading the data is known as ETL, which you'll learn more about in week 3 of this course and in the next course. 

- Now, the **Glue Crawler** you see here is a data management tool that can automatically crawl over your S3 storage to infer the structure of the data and then write the metadata to a table within the AWS Glue data catalog, which is just a database that contains information about the data used as source and targets in the ETL job.
- This enables the data analyst to discover the data stored in S3 by using a query service, such as **Amazon Athena**, to retrieve the data from S3 using SQL-like queries. 

In the lab, you will create the **AWS Glue ETL** and the **AWS Glue Crawler**. You can either manually create these resources using the **AWS console** or **programmatically** create them using code. In this lab, you will explore the programmatic way using **Terraform**, which is an infrastructurist code tool that allows you to set up and configure resources using code. 

The Terraform code will be provided to you in the lab, but you will learn more about Terraform in course 2 of the specialization. 

And finally, the data analyst will use **Amazon Athena** to query the data from S3. In this lab, to give you an end-to-end experience with the data pipeline, you'll put yourself in the shoes of the data analyst to perform queries on the transformed data. And so, after you create and run the resources of your data pipeline, you'll run a Jupyter notebook to perform some analysis on the transformed data.


# Resources

Fundamentals of Data Engineering, chapter 2:
https://www.redpanda.com/guides/fundamentals-of-data-engineering
