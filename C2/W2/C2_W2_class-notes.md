
## Week Overview

In Week 1, you looked at source systems in detail and got some practice playing with databases and object storage. In some sense, the focus of the last week of materials was on 

	how you will connect to and interact with source systems. 

These source systems are typically not something you set up or control directly as a data engineer. However, connecting to and interacting with source systems is really the beginning of the next stage of the data engineering life cycle, which is **data ingestion**. 

As I said before, your work as a data engineer is: 
- you get data from somewhere, 
- turn it into something useful, 
- and then make it available for downstream use cases. 

**Data ingestion** is the *"get raw data from somewhere"* part of your work. ![[Screenshot 2025-12-05 at 14.22.38.png]]

As you already know, that somewhere could be:
- a database, 
- an API, 
- a set of files, 
- or even a streaming system. 

This week, we're going to add depth and detail to our existing knowledge about data ingestion. 

This week: 
- We'll start with a closer look at the details of some **batch** and **streaming** *ingestion patterns,* which is to say ingestion patterns where you process data either in chunks or batches versus processing a continuous stream of data. ![[Screenshot 2025-12-05 at 14.23.36.png]]

- Then we'll have a conversation with a marketing analyst, where you'll identify the requirements for doing batch ingestion from REST API, which is a source system we haven't spent much time with us far. 
- After that, we'll have a conversation with a software engineer, to investigate some of the requirements for streaming ingestion that we lost over in the previous course. Things like characteristics of the **data payload**, which just means the characteristics of the individual messages you're ingesting, as well as **event rates** and how you'll need to **configure the stream pipeline** to get the data, where it needs to go. ![[Screenshot 2025-12-05 at 14.24.10.png]]

- Finally, you'll get a chance to build these solutions yourself in the labs, namely batch ingestion from a REST API, and streaming ingestion from a web server log. Join me in the next video, to get started.


## Data Ingestion on a Continuum

At various points throughout these courses. So far, we've been looking at the differences between batch and stream processing as you will encounter them in your work as a data engineer. 

> 	*In some sense, you can think of nearly all data as representing **a continuous stream of events**.* 

An "event" is a specific action or change that occurs, which can be recorded as data. 

These events might be things happening out in the real world, like a stock price changing, or your favorite sports team scoring a goal, or a user clicking a button on a website. And for the purposes of these courses, we care about the **digital data** that is **generated** when code is run and these events are recorded. 

So, when it comes to data ingestion, the way I like to think about it is that whatever system you're working with, you have data being generated somewhere **as a continuous stream of events that is unbounded**. By that, I mean that the stream doesn't have any particular beginning or end. 
![[Screenshot 2025-12-05 at 15.35.40.png]]


If you ingest those events individually, one at a time as they're generated, then that's an example of stream ingestion. 
![[Screenshot 2025-12-05 at 15.35.58.png]]

If instead you impose some boundaries on that stream and ingest all the data within those boundaries as a single unit, then that's batch ingestion. 
![[Screenshot 2025-12-05 at 15.36.20.png]]

**Ways of Batching Data**
You can, of course, think of a number of different ways you could decide to impose boundaries on a stream of data. 
- **Size-Based Batch Ingestion:** 
	- Bounding your data by a certain size threshold, say, into 10GB chunks. ![[Screenshot 2025-12-05 at 15.37.23.png]]
	- Or by the total number of records, like ingesting every 1000 events as they are generated. ![[Screenshot 2025-12-05 at 15.38.59.png]]

- **Time-Based Batch Ingestion:** More commonly, you can bound your data by time.  
	- You may decide to ingest all sales order data from the past week. ![[Screenshot 2025-12-05 at 15.41.11.png]]
	- Or you might choose to ingest data more frequently, like daily, in which case you're just imposing a different set of boundaries on the initial stream of data. ![[Screenshot 2025-12-05 at 15.41.21.png]]
	- And of course, you could continue this exercise and say, well, if you want to retrieve new data from a source system once every hour, or every minute or every second. ![[Screenshot 2025-12-05 at 15.41.32.png]]

What do you think will happen as you continue to increase the frequency of ingestion? At some point **with super high frequency batch ingestion**, you would have essentially made your way all the way back to *streaming*. The point here is that really: 

> 	*Batch and streaming are not too completely separate approaches to data ingestion. Instead, they exist along a continuum.* 
> 	
> 	Where your data pipeline sits along that continuum will depend on what kind of source system you're working with and the end use case you're ultimately aiming to serve.


 Traditionally speaking, batch processing has usually implied moving and processing **large chunks of bounded data** together as a **single unit**. And for a long time, this was really your only option. 
 
 As tools and technologies have become more powerful and flexible, running batch processing more frequently on smaller chunks of data has become possible. And in recent decades, so called **micro-batch processing** tools have emerged that begin to blur the lines between batch and streaming. 
 
> 	 *There's no rule or industry standard for where the official cutoff is between what you call batch or micro batch. Or exactly how close to real time data needs to be processed in order to be considered streaming.* 


**Deciding how you ingest data** 
In practice, your approach to ingesting data in batches or in a stream will depend on the business use case. 
- Identify it from gathering **stakeholder requirements** 
- and the **type of source system** you're interacting with. 
![[Screenshot 2025-12-05 at 15.46.18.png]]
For some databases, you can use connectors like JDBC or ODBC. Or you could set up ingestion to occur at regular intervals or as soon as a certain amount of new data is recorded. Or you could opt for a serverless ingestion tool like AWS glue ETL that can be configured to connect to a source database and ingest data on a regular basis. 

**Ingesting from APIs**
If you're ingesting data from an API, as you'll do in the first lab this week, you'll be required to set up a connection based on that API specific protocols. And you'll be subject to that API's various constraints and limitations when it comes to how much data you can ingest in one go, or how frequently you can call the API. 

At this point in time there is no universal standard for data exchange over APIs. So this can be a somewhat frustrating process that involves 
- reading documentation, 
- communicating with external data owners. 
- And writing and maintaining your own custom API connection code. 

With that being said, we are seeing trends in the industry now toward vendors providing API client libraries that remove much of the complexity of API access. And there are more and more managed data connecting platforms out there that provide simpler connectivity to many data sources. So if you find yourself looking to ingest data from an API, my recommendation would be to use existing solutions whenever possible and reserve your custom connection work for times when there's no other option. 

 **API calls** work similar to **web requests**, where you send a request for data and receive a response, and the number of requests you can make per time is typically limited.

**Ingesting Data from Files**
When it comes to files as a source system, as we looked at last week, you might be working with an **object storage system** as your data source. But you might also encounter scenarios where ingestion of files can't be completely automated, which is to say, you simply need to manually download a file and get someone to send it to you directly. 

File transfer protocols you can use the command line like SFTP or SCP, which stand for "secure file transfer protocol" or "secure copy"respectively,  or other common ways you may ingest files.

**IoT Devices**
If you want to ingest IoT device or sensor data from a streaming system, regardless of whether you ultimately aim to do batch or streaming processing, you may have no other choice but to set up a **message queue** or other streaming system to ingest this type of data. 



## Batch and Streaming Tools


In the labs in course 1, you used *AWS Glue ETL* as a batch ingestion tool to extract data from a relational database. You also learned about *AWS Kinesis Data Streams* and how it could be used as a streaming ingestion tool. Let’s explore other ingestion tools and compare batch and streaming use cases.

### Batch Ingestion Tools

Popular AWS Tools:
- **AWS Glue ETL:** this service enables you to ingest data from various sources (such as Amazon RDS, Amazon S3, Amazon Redshift, Amazon DynamoDB, and [others](https://docs.aws.amazon.com/glue/latest/dg/glue-connections.html)), transforming it and then loading it into a **destination**. It performs an **ETL job** using **Apache Spark** (distributed processing engine) to distribute the transformation workloads across computing nodes. AWS Glue provides a **serverless** environment where you can create **code-based solutions** for both data ingestion and transformation. To learn more about the AWS Glue environment, see [AWS Glue Components](https://docs.aws.amazon.com/glue/latest/dg/components-key-concepts.html) and [AWS Glue ETL guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/serverless-etl-aws-glue/aws-glue-etl.html).

- **Amazon EMR**: Amazon EMR is a **managed cluster platform** that provides a simple way to **run big data frameworks** such as [Apache Hadoop](https://aws.amazon.com/elasticmapreduce/details/hadoop) and [Apache Spark](https://aws.amazon.com/elasticmapreduce/details/spark). These tools are useful for ingesting **vast amounts of data** from a database (petabyte-scale), transforming them at scale, and loading them into AWS data stores and databases. Amazon EMR can run in a **serverless** mode **or** in a **provisioned mode** where you specify the computing resources that are needed for your workload. To learn more about the details of Amazon EMR, you can read the [AWS documentation](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-what-is-emr.html). 
  
  _**What is the difference between AWS Glue ETL and Amazon EMR?**_ 
  Both services can be used to perform big data processing using the Apache Spark engine, but they differ in terms of the amount of management and configuration that you need to perform, as well as cost. 
	- **AWS Glue** requires **less configuration** and is typically **more convenient**.
	- On the other hand, **Amazon EMR** provides **more contro**l over the computing and memory resources but **requires more configuration** **knowledge**. You will learn more about the distributed framework in the upcoming courses of this specialization.

- **AWS DMS:** AWS Glue ETL and Amazon EMR are both tools that enable you to perform transformations at scale while ingesting data. *If you don’t need to perform transformations* while ingesting data, you can consider using **AWS DMS (Data Migration Service)**. This service allows you to **sync data** from an existing database (on-premises or hosted on the AWS cloud) to another data store that exists within your data pipeline (such as Amazon S3 or a data warehouse). You can also use this service to migrate data from one database engine to a different database engine. It is available in **serverless or provisioned modes**. To learn more about this service, check out [the overview page of this service.](https://aws.amazon.com/dms/) 


Other AWS ingesting services: 
- [AWS Snow family](https://aws.amazon.com/snow/): if your company wants to **migrate its legacy on-premise system to the cloud**, you might need to transfer massive amounts of data, sometimes 100 TB or more. It would be very slow and costly to migrate this data over the internet, so you might want to consider a transfer appliance instead. At the time of creation for these courses, AWS offers transfer appliances called Snowball and Snowcone that help you move data in and out of the AWS cloud.

- [AWS Transfer family](https://aws.amazon.com/aws-transfer-family/): This is a service that enables you to **transfer files into and out of Amazon S3** using common file transfer protocols such as **SFTP** and **FTP** protocols.


Other non-AWS ingestion tools:
- There are other **ingestion tools** provided by other vendors or open-source projects that allow you to set a target and source (could be from different cloud providers) and ingest data in various ways. **These tools are known as connectors** because they allow you to connect a particular source to a target system. Examples of such tools include: [Airbyte](https://airbyte.com/), [Matillion](https://www.matillion.com/support) and [Fivetran](https://www.fivetran.com/?r=0).


### Streaming Ingestion Tools

In course 1, you read about two streaming platforms: 

- Amazon Kinesis Data Streams, and 

- Amazon Managed Streaming for Apache Kafka (MSK). 

To quickly refresh your memory about those services, you can check the overview service page of each ([Kinesis](https://aws.amazon.com/kinesis/data-streams/) and [MSK](https://aws.amazon.com/msk/)). Later this week, we’ll get into more detail about these streaming platforms.


### Key Considerations for Batch vs Streaming Ingestion

- **Use cases**: Ask your stakeholders: "if you get data in real time, what actions can you perform that would be an improvement to getting the data periodically in batches?"
    - Machine learning: batch is an excellent approach for many common use cases, such as model training. Consider if stakeholders can benefit from continuous training and online prediction.
    - Dashboards/Reporting: What are the benefits of having a real-time dashboard over one that is updated daily or weekly? Consider how stakeholders will act on real-time data.
        
- **Latency**: Do you need millisecond real-time data ingestion? Or would a micro-batch approach work, accumulating and ingesting data, say, every minute?
    
- **Cost**: A streaming ingestion approach is typically not as straightforward as batch ingestion, and it can carry extra costs and complexities.
    - Will your *streaming-first approach* cost more in terms of time, money, maintenance, downtime, and opportunity cost than simply doing batch?
    - If you're using a streaming platform: does your team have the capability to manage it? Do you have the skills to fix errors propagating in an event system?
        
- **Existing/Available system:**
    - **Destination system:** If you ingest data in real time, can downstream storage systems handle the rate of data flow?
    - **Source system:** Are you getting data from a live production instance? If so, what’s the impact of your ingestion process on this source system? Streaming systems are the best fit for many data source types. For instance, in IoT applications, each sensor writes events or measurements to streaming systems as they happen. While you can connect to the streaming source to directly write data into a database, you might find that it is a better fit to use a streaming ingestion platform such as **Amazon Kinesis** or **Apache Kafka**.
        
- **Reliability/Availability**: Are your streaming pipeline and system reliable and redundant if infrastructure fails? Streaming services require high availability of compute resources. On the other hand, **batch services don't need high availability**. 
    

I suggest you adopt true real-time streaming _only after_ identifying a **business use case** that justifies the trade-offs against using batch. 

**Note**: There are other use cases where you might need to perform both types of ingestions (same computations done on batch and streaming). For that, you can use ingestion frameworks, such as the **lambda architecture** discussed in the previous course, to handle both batch and streaming ingestion patterns.


## ETL vs ELT

These are two very common batch ingestion patterns - ETL and ELT - and while they technically include components of the transformation and storage stages of the data engineering life cycle, in practice, you'll need to be thinking about the trade offs between these patterns and the ingestion stage. 

ETL, or "Extract Transorm Load", is really the original batch ingestion pattern that gained popularity in the 1980s and '90s. ![[Screenshot 2025-12-05 at 17.29.44.png]]
- The process starts with **extracting raw data from a store system**, which could be done by directly querying a database or using an API, for example. 
- Then you **transform** the data in an intermediate staging area. 
- Then you **load** the data into a target storage destination, 
	- like a database 
	- or a data warehouse. 


Back in the '80s and '90s, storage and computing power were extremely limited, and so it was important to have a plan in place for exactly what data you wanted to ingest and how you want to store and access that data, in what format, and so on. Data warehouses were expensive to set up and not well suited for running heavy queries that included complex joins and transformations. In those days, one had no choice but to be **very intentional about how to transform raw data** *during the ingestion process* to ensure it could be stored and made available in an efficient manner. ETL is still very popular today as an ingestion pattern. But now with the relatively low cost of Cloud storage and increased computational power, it's no longer the only option. 

In the early 2010s, Cloud storage systems became highly scalable, and we saw the emergence of **data lakes** built on top of object storage systems like S3 and Cloud data warehouses, like Redshift and Snowflake. This made it possible to store enormous amounts of data relatively cheaply and to perform all of your data transformations directly in your data warehouse. That's when the concept of ELT or extract load transform came about. ![[Screenshot 2025-12-05 at 17.45.42.png]]


In the ELT process, you extract raw data from source systems and load it directly into the target database or data warehouse or even optic storage without performing any transformations. ![[Screenshot 2025-12-05 at 17.46.01.png]]The exciting idea with ELT is that **you don't need to decide upfront** how you want to use your data. This could be attractive because in some sense, you could say that by applying transformations to raw data and only storing the process results like you do with ETL, there's some information that's lost in the process. But with ELT, all of the options remain on the table as you simply capture all the data and save up for later use, and then you can query and transform the raw data however you like, and no information is ever lost. 

**Pros and Cons of ELT**
As attractive as this paradigm might sound, to be honest, when I first heard about the idea of ELT, I thought it was a terrible idea. Why, I thought, would you want to just pile up a bunch of raw data and storage without thinking deeply about how you want to use it. 

> 	*As I've been emphasizing throughout these courses, the **first step** in any data engineering project should be firmly establishing **what your end goals are**, and only then thinking about how to build a system to achieve those goals.* 

Over time, however, I did start to see the potential benefits of ELT. 
- For one thing, ELT is **faster to implement** because it doesn't require detailed planning ahead of time on exactly how you want to transform your data. 
- It's also possible to **make data available more quickly** to end users albeit in raw form, because ELT removes the need for a staging server and in flight data transformations. 
- With the processing power of the modern data warehouse, **transformations can still be done efficiently** after the data is loaded into storage. Beyond that, as I said before, when you want to store all of your raw data, you can set later to adopt different transformations or analyze the data in a different way that might have been possible if you only store transform data in the first place. 

What's the downside of ELT? 
- Well, in short, if you're not careful, your pipeline can simply become an EL pipeline, where you extract and load enormous quantities of raw data into storage without figuring out how to transform it into something useful. 
- When you don't want to spend time upfront, planning how you will use your data, you could end up with what's commonly known as a "**data swamp**", which is a situation where your data has become unorganized, unmanageable, and essentially useless. 

In the early 2010s, data swamps were common as companies found it possible to keep literally every scrap of raw data just in case. Nowadays, much of this has been cleaned up due in part to **regulations** that require companies to store data in such a way that it can be audited or deleted in an orderly fashion, for example, a user requests that their data be removed from company systems. 

Well, that being said, the relatively low cost of storage today combined with the processing power of modern data warehouses and other storage abstractions means that both ETL and ELT can be reasonable approaches to batch processing. But no matter which approach you take, it's important to have a **clear set of end goals** in mind and manage your data accordingly. Let's think back to the conversation with the marketing analyst.


**ETL vs ELT - COMPARISON TABLE**

|                                     | **ETL**                                                                                                                                                                                                                                                                                                                                   | **ELT**                                                                                                                                                                                                                                                                                                              |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **History**                         | - In the 80’s and 90’s, data warehouse cost was very expensive (millions of dollars), so engineers wanted to be *very intentional* about the data they were about to load into the data warehouse<br><br>- Data volume was still manageable.                                                                                              | - *Cloud* data warehouse *reduced* the cost of storing and processing data significantly (from millions of dollars to hundreds/thousands of dollars only) <br><br>- Data volume exploded.                                                                                                                            |
| **Processing (transformation)**     | - Data is transformed into a *predetermined format* **before** it is loaded into a data repository. So, data engineers have to carefully model the data and transform it into this format.<br><br>- Transformations rely on the processing power of the processing tool that is used to ingest data (unrelated to the target destination) | - *Raw data* is loaded into the target destination. Then it is transformed just before analytics (Can be used with not well-defined data requests)<br><br><br>- Transformations rely on the processing power of the data repository, such as the data warehouse.                                                     |
| **Maintenance time**                | - If the transformation is found to be inadequate, *data needs to be re-loaded*.                                                                                                                                                                                                                                                          | - The original data is intact and already loaded and can be used when necessary for additional transformation: *Less time required for data maintenance.*                                                                                                                                                            |
| **Load Time & transformation time** | - Load time: it typically takes longer as it uses a *staging area* and *system*. <br><br><br>- Transformation time: it depends on the data size, the transformation complexity and the tool that is used to perform the transformation.                                                                                                   | - Load time: there is no transformation involved, the data is directly loaded into the destination system<br><br>- Transformation time: it is typically faster because it relies on the processing power and parallelization of modern data warehouse<br><br>(generally considered more efficient)                   |
| **Flexibility (data types)**        | - ETLs are typically designed to handle **structured** data.                                                                                                                                                                                                                                                                              | - ELT can handle all types of data: structured, unstructured, semi-structured. Once the data is loaded into the target system, you can transform it.                                                                                                                                                                 |
| **Cost**                            | - It depends on what ETL/ELT tool is used and to what target system the data is loaded. <br><br>(And of course, it depends on the data volume).                                                                                                                                                                                           | - It depends on what ETL/ELT tool is used and to what target system the data is loaded. <br><br>(And of course, it depends on the data volume).                                                                                                                                                                      |
| **Scalability**                     | - Nowadays, most of the cloud tools are scalable. However, the challenge here is that if you have lots of data sources and lots of targets, you would need to put in lots of effort to manage the code and handle data from multiple sources                                                                                              | - ELT uses the scalable processing power of the data warehouse to enable transformation on a large scale.                                                                                                                                                                                                            |
| **Data quality/ security**          | - It ensures data quality **by cleaning it first**. Transformations can also include **masking personal information.**                                                                                                                                                                                                                    | The data needs to be transferred first to the target system before transformations that enhance data quality or security are applied. <br><br>*There’s a sub-pattern called EtLT where small t does not refer to business modeling but to transformation with limited scope (mask sensitive data, deduplicate rows). |



## REST API

>	*An API is essentially a set of rules and specifications that allows you to programmatically communicate and exchange data with an application.* 

By "*programmatically communicate*", I mean, communicate by running code. 


- Today, APIs are built into the functionality of a wide range of software applications. For instance, social media apps use APIs to *fetch* and *display* data from the web servers to end users. 
- APIs are also used to *facilitate transactions* between e-commerce websites and payment systems. 
- Many companies provide *public-facing APIs* so that you as a developer can access their data and services and integrate them into your own applications. 
> ![[Screenshot 2025-12-05 at 18.07.04.png]]


As a data engineer, you will use APIs to connect with and extract data from various sources, such as: 
- web services, 
- Cloud platforms, or 
- third-party providers 
by **sending requests** and **receiving responses** in a **standardized** format. 

APIs can also provide metadata, documentation, authentication, and error handling features to facilitate data extraction. 

**REST APIs**
The most common type of API is what's known as a **REST API**, where REST stands for "REpresentational State Transfer". 

REST APIs commonly use **Hypertext Transfer Protocol** or what you might know more familiarly as **HTTP** methods as a basis for communication. 

![[Screenshot 2025-12-05 at 18.07.44.png]]


## Streaming Ingestion Details

In week one of this course, we looked at streaming systems, including *message queues* and *event streaming platforms*, from the perspective of these being source systems, where your data pipeline was *on the consumer end* of these data sources. ![[Screenshot 2025-12-06 at 17.33.40.png]]

I also mentioned that **depending on the system you're working with**, the actual source might just be **the event producer** or multiple elements of a streaming system, like **multiple producers**, **brokers**, and **consumers** could be upstream of your ingestion system.
![[Screenshot 2025-12-06 at 17.35.50.png]]

In this sections, I'd like to talk a little more about the details of message streams, and after that you'll be on your way to the lab. 

And so as a quick reminder, you'll have 
- event streaming platforms and 
- message queues 
as the **two main modalities of streaming ingestion**. 


**Message Queue**
A message queue is essentially a **buffer** used to deliver messages **from an event producer to a consumer in an asynchronous manner**. 

Message queues typically operate on a first in, first out or FIFO basis, meaning that the event consumer will always read the oldest message in the queue first, and once a message is consumed, **it is deleted from the queue**. 
![[Screenshot 2025-12-06 at 17.39.14.png]]
![[Screenshot 2025-12-06 at 17.39.35.png]]
**Event Streaming Platform**
Event streaming platforms, on the other hand, function by **storing messages** in a **persistent** way in an **append-only log**. The "**event router**" distributes messages in the log **to the "subscribers"**, and it's possible to *replay* or *reprocess* any messages in the log. 

![[Screenshot 2025-12-06 at 17.40.53.png]]
![[Screenshot 2025-12-06 at 17.40.59.png]]

You'll be using Amazon's **Kinesis** service as your event streaming platform in the lab, but another widely used platform is **Apache Kafka**. 
![[Screenshot 2025-12-06 at 17.42.04.png]]


In this section, I'll use Kafka as the example platform to talk through some of the details and draw parallels with Kinesis where they exist. And then in the next section, Morgan will go through kinesis in more detail. So by the end of this week you can have some context on both of these solutions. 


### **Apache Kafka**
Apache Kafka is an *open-source event streaming platform*, and while streaming platforms come in a number of different flavors and varieties, the principles of how events are routed and stored are similar across platforms. 

At a high level: 
- **Event Producers** *send* or *push* messages over the network to a **Kafka cluster**
- **Kafka Cluster** contains *one* or *more servers*, also called "**brokers**". 
-  **Event Consumers** then *read* or *pull* messages from that Kafka Cluster. 
>    ![[Screenshot 2025-12-06 at 17.55.29.png]]
>    
	**Kafka Cluster**
	Let's zoom in a bit on the Kafka Cluster. *Within* a Kafka cluster, 
	- **"message streams"** are **split up** and **routed** into what are called "**topics**". 
	- "Topics" can be thought of as a *category that holds a collection of related events*, or maybe in another sense, like a *road* to somewhere. And so messages line up in topics similar to how rows of cars line up on different highways based on their destination. 
		- A topic could contain any type of messages, for example, fraud alerts, customer orders, or temperature readings from IoT devices. 
		- It's the job of the **producer** to send a message to its corresponding topic. 
	- Each topic has one or more **"partitions"**, which are just **logs** containing ordered, immutable sequences of messages that you continually add new messages to. Using the road system analogy, partitions are like lanes on the highway. More lanes allow more cars to pass through, and so each partition handles a subset of messages as they are added to the topic. This allows for more efficient traffic or message flow. 
		- And again, it's a **job of the** **producer** to decide on which partition to send each message. The decision could be based on a **round-robin strategy**, for example, or by computing the destination partition based on the **message key**.

![[Screenshot 2025-12-06 at 17.57.28.png]]

With kinesis, all these concepts are essentially the same, but instead of topics, you have streams, and instead of partitions, you have what are called shards. 

![[Screenshot 2025-12-06 at 17.58.07.png]]

- **Event Consumers**
	- Event consumers are **grouped**, and each consumer group can **subscribe** to **one or more topics**. The consumers of a group cooperate together to consume the messages from all partitions of a given topic. 
	  
	- Each partition can only be assigned to **a single consumer** in the group, and each consumer consumes messages from a different subset of partitions. ![[Screenshot 2025-12-06 at 17.58.43.png]]![[Screenshot 2025-12-06 at 17.59.04.png]]When a producer publishes a message to a topic partition, that message is delivered to one consumer within each subscribing consumer group. 
	  
	- Once a message is published to a topic, the Kafka cluster retains that information for a configurable period of time, whether or not the message was consumed. This allows consumers to replay and reprocess messages as needed. 


**How it works**
![[Screenshot 2025-12-06 at 18.00.16.png]]
You could imagine a scenario where you have direct access to monitor the web server log for new messages. Then you could treat the web server log as the event producer. From there, events could be ingested to a Kafka topic or a Kinesis stream as a first step in your ingestion pipeline. 

**CDC**
You can also monitor database activity through a process known as c**ontinuous change data capture, or continuous CDC**. By processing the database log, you can stream data changes into your data pipeline to ensure that the data in your pipeline is synchronized with the data updates in the source database. 


## Kinesis Data Streams Details

Event Producers --> Push data to the stream
Event Consumers --> Pull/read data from the stream

Kinesis Data Stream will be our upstream source system in the upcoming lab.



Just like how in Kafka **producers** send data to **topics**, when working with Kinesis Data Streams, a "**producer**" pushes data to a specific "**stream**".  ![[Screenshot 2025-12-07 at 12.21.57.png]]

**One "stream**" is made up of **many "shards"**, which provide the "**units of capacity**" for the stream. 
![[Screenshot 2025-12-07 at 12.22.55.png]]


As you need to scale your stream up to ingest more data, you need to add more shards to the stream. 
![[Screenshot 2025-12-07 at 12.23.15.png]]


**How to know how many shads I need**
In order to know how many shards you'll need for your use case or when you'll need to increase the number of shards, you'll need to know the "*size*" and "*rate*" of **write and read operations** you're expecting in your pipeline. 

- Write operations are when *an event producer writes data to the stream*, 
- Read operations are when *downstream consumers read data from the stream*. 

![[Screenshot 2025-12-07 at 12.25.22.png]]


When it comes to **capacity**, each of the Shards: 
- can support up to **five READ operations per second** (5 read ops p/s), 
- and those 5 operations can add up to a **maximum total data read rate of two megabytes/second.** In other words, they cannot exceed 2MB/s.
![[Screenshot 2025-12-07 at 12.32.24.png]]


For **writing** data, a producer: 
- can write up to 1,000 records per second to a Shard 
- with a maximum total data write rate at 1 MB/s. 
![[Screenshot 2025-12-07 at 12.33.02.png]]


To determine the number of shards you would need for a specific use case, it would take some analysis and some math, given the size and rate of read and write operations that you expect. 

**On-Demand VS Provisioned Mode**
Sometimes it can be hard to estimate the exact number of read and write operations. For example, in a brand new application. 

Or in other cases, the only thing you might know for sure is that you expect the traffic in your application to vary dramatically over time, like on an e commerce platform or other public facing applications. 

For those situations, you can use Kinesis in "**on-Demand**" mode. 
- On-demand mode will automatically manage the scaling of the shards up or down as needed,
- and you are only charged for what you use. 
- This can be more convenient from an operational perspective when compared to the alternative, which is provisioned mode. 

With **provision** mode: 
- you specified the number of shards necessary for your application based on the expected write and read request rate. 
- And then it's up to you to add more shards or re shard when needed. 
- Provision mode might be a good fit for your work if you have predictable application traffic, or if you want to be able to control your costs more carefully. 


**What kind of data do the producers produce?**
When it comes to the data moving through a stream, **each** data record that is sent to the stream from a producer includes: 
- a partition key, 
- a sequence number, and 
- the data itself in the form of what's called a binary large object, or "BLOB" for short. 


**Partition Keys**
When you are setting up the data producer for your system, you need to choose a **partition key**. The partition key is then used to determine which **shard** the data record is placed into. 
Kinesis itself then assigns a s**equence number** as each record is written to maintain the order of the records within the Shard. 
![[Screenshot 2025-12-07 at 13.10.01.png]]


For example, let's say you wanted to create a stream of transactions from an e commerce platform. Then you might want to use the **customer ID** as the partition key.
![[Screenshot 2025-12-07 at 13.10.57.png]]

 In this case, all transactions for a single customer could be stored in the same shard. 
![[Screenshot 2025-12-07 at 13.11.30.png]]
- This would then make it easier for downstream consumers to pool records related to a single customer for aggregation and analysis. 



**Shared VS Enhanced Fan-Out**
A producer puts data into shards, and a consumer reads data from Shards, and it is common to have multiple consumers reading data from a Shard. 
![[Screenshot 2025-12-07 at 13.13.02.png]]

By default, consumers share a Shards read capacity, which is called "***shared Fan-Out***". 
![[Screenshot 2025-12-07 at 13.13.16.png]]

>	This means that the consumers are contending for read capacity. 
>	This can be an issue for some use cases. 

To avoid running into this capacity issue, you can set things up so that each consumer is able to read at **the full two megabytes/second** read capacity of the Shard, which is called "***Enhanced Fan-Out***".
![[Screenshot 2025-12-07 at 13.14.35.png]]

![[Screenshot 2025-12-07 at 13.18.30.png]]


You can use managed services such as AWS Lambda, Amazon Managed Service for Apache Flink, and ADS Glue to process data stored in Kinesis data streams, 
![[Screenshot 2025-12-07 at 13.19.01.png]]

or you can write your own custom consumers using the **Amazon Kinesis client library (KCL)**. 

You can also set things up so that the output of one stream becomes the input for another, which can allow you to build more complex real time data processing workflows. 
![[Screenshot 2025-12-07 at 13.20.22.png]]

Consumers can also send data to other AWS services, like integrating with A***mazon Data Firehose*** to store data in ***Amazon S3.*** It's important to remember too that Kinesis Data Streams allows for *multiple applications to work with the same stream at the same time*. Each one consuming the data independently and sending that data downstream to different systems. 


## What is Change Data Capture (CDC)?

Suppose you extracted and loaded data from a database into your storage system. After some time, you might need to update the data stored in your storage system to ensure that it is in-sync with the data in the source system. There are two strategies for this:

- **Full snapshots or full load:**  in this approach, every time you want to update the data stored in your system, you ingest the entire data from your source system, replacing the old stored data with the new updated data. If your data is tabular, fully loading the data means that you delete all the old data from the stored table and extract all rows from the source table every time you need to update your stored data. This is a straight-forward approach that ensures the consistency between the data in the source system and the data stored in your data pipeline. However, for high-volume data, it can take a long time to run and it can require lots of processing and memory resources. It is more suitable for cases where there’s no need for frequent data updates.

- **Incremental (differential) load**: in this approach, you only load updates and changes since the last read from the source systems. For example, when loading updates from a source database, you might utilize a _last_updated_at_ column to identify the rows of data that have been updated since you last read from this source database, and then only load the updated data from these identified rows. While this approach is faster than the full load approach, especially for high-volume data, it might require more complex logic to implement. When working with databases, this process is known as **Change Data Capture or (CDC).**  

*According to the book Fundamentals of Data engineering, “Change data capture (CDC) is a method for extracting each change event (insert, update, delete) that occurs in a database” and making it available for downstream systems.*


### Use Cases for CDC

- CDC helps you synchronize data across different databases, supporting continuous database replication. For example, you might have a source PostgreSQL system that supports an application and you want to periodically or continuously ingest table changes into a data warehouse to enable analytics based on the most recent data. Or if you work in a hybrid company, you might need to use CDC to capture changes in on-premises databases and apply those changes to on-cloud databases.
    
- CDC helps you capture every historical change for auditing and other business purposes. For example, certain businesses are required to maintain complete historic information of their customer purchases for regulatory purposes, or to extract insights that allow businesses to improve.
    
- CDC enables microservices to track any change in the source database. For example, consider a microservice that manages purchase orders. When a new order is placed, you can use CDC to relay information to shipment service and customer service.
    

### **Two approaches to CDC**

- **Push:** This approach requires you to implement some sort of logic or process to capture changes in the source database. Then, it relies on the source database to _push_ any data updates to the target system when something changes in the source system. This method allows the target systems to be updated with the latest data in near real-time, but if you don't set this up properly, you risk losing data updates if the target systems are unreachable when the source systems try to push the changes.

- **Pull:** This approach requires the target systems to continuously poll the source database to check for changes and then _pull_ in data updates when they happen. This method typically results in a lag before the target systems pull in any new data updates because the changes are usually batched between pull requests.


### **CDC Implementation Patterns**

There are several methods for how CDC can extract changes from databases.

- **Batch-oriented or query-based CDC (pull-based)**: In this approach, you query the database itself to identify if there has been a change in data. In the case of relational databases, this requires that the database has an additional column labeled as _updated_at_, _last_updated_ or _last_modified_ that helps you find all updated rows past a certain specified time. This process allows you to extract changes and incrementally update a target table. However, t**his approach can add computational overhead** to the source system because target systems have to scan each row in the table to identify the last updated values. 
    
- **Continuous or log-based CDC (pull-based)**:  Instead of running periodic queries to get the table changes as a batch, you can treat each update to the database as an event using **continuous CDC**. This type of CDC relies on **checking the database log**. A database log records every change to the database sequentially  (e.g., every create, update, delete) and is used in case of a failure to restore the database state. You can read the events from this log (by writing your own code or using a CDC tool such as **Debezium**) and send them to a streaming platform, such as Apache Kafka. This way, you can capture data changes in real-time without incurring any computational overhead or requiring the need for an extra column in the source databases.![[Screenshot 2025-12-07 at 13.22.22.png]]

- **Trigger-based CDC (push-based method)**:  A trigger is a stored function that you can configure to run when a specific column changes. The triggers inform the CDC of the changes in the source databases and in this way it relieves the CDC from detecting changes. However, **too many triggers can negatively impact the write performance of the source database**. 

### Tools for CDC

Feel free to read more about some of the common tools used to implement CDC
- [Debezium](https://debezium.io/)
- [AWS DMS](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Task.CDC.html)
- [Kafka connect API](https://limadelrey.medium.com/kafka-connect-how-to-create-a-real-time-data-pipeline-using-change-data-capture-cdc-c60e06e5306a) 
- [Airbyte log-based CDC](https://airbyte.com/solutions/database-replication)


## Summary: General Considerations for Choosing Ingestion Tools

When choosing an ingestion tool for your data systems, you should consider 
- the *characteristics of the data* you're ingesting, 
- as well as the *reliability and durability of the ingestion tool*.


### Characteristics of the data 

_Note: In the book “Fundamentals of data engineering”, Joe and Matt refer to the characteristics of the data as the "**data payload**", which includes data kind (type and format), shape, size, schema and data types, and metadata._

- **Data type & structure**: You learned in course 1 that data in source systems could be structured, unstructured, or semi-structured. When deciding how to ingest data and what tool to choose, you need to understand the data type and structure (e.g. an image in PNG format) so that you can identify the appropriate ingestion tool and transformations you might need to apply later on.
    
- **Data volume**: For data volume, you need to consider two things:
    - **Data size in bytes** of the existing data that you need to ingest: _In case of batch ingestion,_ you need to consider the size of the historical data that you need to ingest. Can you ingest the entire historical data in one big chunk? Depending on the network connection between the source system and the target system, it may be possible to transfer the historical data over the network, but if you have limited bandwidth then you may need to *split  the massive payload into chunks*, which effectively reduces the size of the payload into smaller subsections. _In case of streaming ingestion_, you need to consider the **message size**.  You must ensure that the streaming ingestion tool can handle the maximum expected message size. For example, Amazon Kinesis Data Streams supports a maximum message size of 1 MB, while Kafka defaults to this maximum size but can be configured to support a maximum data size of 20 MB or more.
    - **The size of the future data** that you may ingest with the same pipeline: how are you expecting the data to grow? What is  the daily, monthly, or yearly growth of data?  Considering the actual and future size helps you understand how to configure your tool and what cost to anticipate to ensure that your ingestion system meets the demands.

- **Latency requirements:** When designing your pipeline, one of the stakeholder requirements that you need to consider is latency: how fast do stakeholders want to operate on data? What is the acceptable delay? Do they need to extract insights from the data one day after it is ingested, or do they need near real time insights? In other words, is it a batch scenario: where data needs to be ingested once a day, a week, a month. Or does the data need to be streamed from a streaming source continuously with the lowest delay possible (for instance, in milli-seconds)? To meet the latency requirement, you need to think about how quickly you need to process the ingested data once it reaches your pipeline and also understand how quickly the source data is generated. The velocity of the data will impact the tools (batch or streaming tools) you choose to ingest and process the data.
    
- **Data quality**: Is the source data in good shape for immediate downstream use? What post-processing is required to serve it? Depending on the source systems the data might be incomplete, or contain inconsistent information, duplicates, or errors. If the data is not expected to be in good shape, then you may need to check the quality of the data ingested in order to fix any issues. Some ingestion tools can help you fill in missing values or detect/fix inconsistencies or invalid entries. You’ll learn more about quality checks in the upcoming course. 
    
- **Changes in schema**_: schema changes (e.g. adding a new column, changing a column type, creating a new table, renaming a column) frequently occur in source systems and are generally out of your control. If you’re expecting these changes to happen frequently, then you might need to consider using ingestion tools that automatically detect schema changes. However, communication between you and the upstream stakeholders is as important as the automation that checks for schema changes.
    

### Reliability and Durability

Reliability and durability are two important considerations in the ingestion stage. 

- Reliability means making sure that ingestion systems are performing their intended function properly. 
- Durability means making sure that data isn’t lost or corrupted. 

If you design a reliable ingestion system, you will ensure the durability of the ingested data. For example, streaming systems such as IoT devices do not retain events indefinitely, so if you don’t correctly ingest its data, the data may be lost. Make sure to understand the characteristics of the source systems and the ingestion tools. 

**Advice**: Evaluate the tradeoffs between the cost of losing data vs building an appropriate level of redundancy. For more information and consideration, please check out chapter 7 of _Fundamentals of Data Engineering_.



## Week Summary

This week we've been looking at the ingestion stage of the data engineering lifecycle in the context of batch and streaming, and some of the tools you can use for these two types of ingestion patterns. You implemented a batch ingestion solution, pulling data from a public API, and you set up streaming ingestion for a recommender system. So nice work. One of the main ideas we started with at the top of this week is that while batch and streaming ingestion are often discussed as two distinct ingestion paradigms, they really exist along a continuum where at one end you have ingestion of relatively large batches of data on an infrequent cadence, and on the other end you have real time streaming ingestion of individual messages as they're generated. In between, you have a wide range of batch and micro batch scenarios, as well as different approaches to streaming. 

>	*Whatever approach you take for your own data pipelines should be determined by what stakeholders really need and the requirements of your system.* 


When it comes to batch ingestion specifically, we looked at ETL and ELT and how you might think about the trade offs and advantages of one versus the other depending on the requirements of the system you're building. 

You also saw, however, that doing in flight transformations is something to think about in the context of streaming pipelines as well. In the first lab, you ingested data from a public API and got some practice with API specific concepts like the connection authentication and pagination steps that will apply to many different ingestion scenarios when your source is an API, and then in the second lab, you set up streaming ingestion for a recommender system. In that case, the source system was a Kinesis data stream, and you set up a consumer using Python code, perform some in flight transformations in the data, and then push the messages into two separate streams for downstream consumption by the recommender model. With each of these labs and the concepts we've discussed in between, you've deepened and broadened your knowledge of data ingestion.


## Week 2 Resources

**Optional reading and reference material:**

- Chapter 7 of [Fundamentals of Data Engineering](https://go.redpanda.com/fundamentals-of-data-engineering)

- Amazon Kinesis Data Streams
    - [Kinesis Data Streams: High-level architecture](https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#high-level-architecture)    
    - [Quotas and limits](https://docs.aws.amazon.com/streams/latest/dev/service-sizes-and-limits.html)

- Apache Kafka
    - [Kafka Documentation (Only check the introduction section 1.1)](https://kafka.apache.org/081/documentation.html#introduction)
    - [Gentle introduction into Kafka: Gently down the stream](https://www.gentlydownthe.stream/)