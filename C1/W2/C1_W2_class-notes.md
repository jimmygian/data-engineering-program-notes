
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

I include queries and modeling as separate from transformation here because these are critical components of any data pipeline that really add value when done well and present risks when done poorly. To illustrate this point, let's start with queries.