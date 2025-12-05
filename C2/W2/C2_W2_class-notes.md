
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