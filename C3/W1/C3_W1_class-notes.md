
## Course Intro 

[**Course 3 - Data Storage and Queries**](https://www.coursera.org/learn/data-storage-and-queries/home/welcome)

This course consists of 3 weeks of content and covers these main learning objectives:
- Explain how data is **physically** stored on disk and in memory
- Compare how data is stored and queried in **object, block, and file storage systems**
- Explain how data is stored in r**ow-oriented vs column-oriented databases**
- Explain how **graph and vector databases** store and retrieve data
- Explain the key **architectural features** of **data warehouses, data lakes, and data lakehouses**
- Implement a data lake using **AWS Glue**
- Implement a data lakehouse with a **medallion-like architecture** using **Lake Formation and Iceberg**
- Explain the **stages of the life of a query**
- Implement **advanced SQL queries**
- Explain the **role of of an index** and its impact on **query performance**
- **Summarize approaches** for processing aggregate and join queries
- Compare the **execution times** of **aggregate** queries between row and columnar storage
- List some strategies for **enhancing query performance**
- Aggregate and join streaming data


While storage inquiries might seem straightforward, at first glance, they are anything but straightforward. 

There are many decisions in how to store your data that will affect how you can use the data efficiently afterward. 

And then also **the design of the database schema**, and maybe even **deciding which data centers** that is, in what parts of the world **you decide to store your data**. All this can drive both performance as well as impact how you have to affect with registry concerns such as data sovereignty. 

As you've seen in previous courses, you can think of data storage as a sort of hierarchy, where you have: 

1. the **raw ingredients of storage at the bottom**, including: 
	- the physical elements of storage like magnetic disks, solid state drives, and RAM, 
	- as well as the less tangible elements of storage things like CPU compression, networking, and serialization. 

2. On top of that, you have **storage systems built from these raw ingredients**, things like 
	- databases  
	- object storage
	- etc.

3. And on top of that you have **storage abstractions** built from these storage systems. So things like: 
	- data warehouses, 
	- data lakes, and 
	- data lake houses. 

4. Then, when it comes to querying the data you have in storage, there are a **wide range of tools and techniques** you need to be aware of as a data engineer to ensure your systems are working efficiently.


## Overview 

In your work as a data engineer, having deep expertise in storage solutions and queries will help you be more successful at every stage of the lifecycle.

Storage is arguably the most complicated component of the data engineering life cycle. That's because you'll store data many times as it moves through the life cycle, and the storage solutions you choose will impact everything from: 
- the **cost**, 
- to the **performance**,
- to the **end user experience** of your data systems. 


Data storage really **spans all the stages of the lifecycle**
- source systems that are out of your domain of control as a data engineer:
  ingestion --> transformation --> serving data to your end users. 

![[Screenshot 2025-12-22 at 12.58.30.png]]

In this course, we'll focus on the storage you'll directly handle as a data engineer, from ingesting data on through to serving. 

**Data Characteristics**
To **choose the proper storage solutions** for your data architecture, you need to know the *"characteristics"* of your data, including things like: 
- type, 
- format, 
- size, 
- and how it will be accessed and updated by various stakeholders at different points in time. 

Recall that you can think of storage as a sort of hierarchy. 
![[Screenshot 2025-12-22 at 13.20.13.png]]



#### Raw Ingredients
At the bottom layer, you have the **raw ingredients** that comprise any storage system. 
![[Screenshot 2025-12-22 at 13.11.52.png]]
- These are storage components like: 
	- *the magnetic disks,* 
	- *solid sate drives,* 
	- *and memory* 
	that physically store data, 
	along with the **processes** needed to store and transmit data, such as:
	- *networking,* 
	- *serialization,* 
	- *and compression.*


#### Storage Systems
You typically don't interact directly with these physical storage components or processes. Instead, you interact with the **storage systems** that are built from these raw ingredients. 
![[Screenshot 2025-12-22 at 13.12.06.png]]

A storage system consists of an "**internal management system**" that organizes your data and allows you to interact with the stored data. You already learned about some of these storage systems, such as "*databases*" and "*object storage*" in the context of store systems in Course 2. 

**OPTP vs OLAP**
Back then, we mainly discuss "***OLTP***" systems for processing transactions, which focus on performing read and write queries with low latency. However, storage systems that support transactional processing are different from those needed to support online analytical processing, or **"*OLAP*"** systems. These are systems that focus on applying analytical activities on data like *aggregation*, and *summarization* to make business decisions. 

>	**OLTP Systems:** Online Transactional Processing Systems
>	*Focus: Performing read and write queries with low latency*

>	**OLAP Systems:** Online Analytical Processing Systems
>	*Focus: Applying analytical activities on data (e.g. Aggregation, summarization)*


**Graph, Vector DBs**
Nowadays, we also have storage systems like *graph* and *vector* databases that are more specialized and can support **machine learning analytical use cases**. 
![[Screenshot 2025-12-22 at 13.18.22.png]]


### Storage Abstractions
At the top of the storage hierarchy, storage systems are assembled into ***"storage abstractions"***, including Cloud 
- data warehouses, 
- data lakes, 
- and data lakehouses.

![[Screenshot 2025-12-22 at 13.18.41.png]]




In **Week 1** of this course, we'll focus on these first two layers, 
- the raw ingredients, 
- and storage systems. 

![[Screenshot 2025-12-22 at 13.26.05.png]]

- You'll dive deeper into the **characteristics** of physical storage technologies and look at the technical details of *serialization* and *compression* algorithms. 

- Then we'll explore the **cloud storage paradigms** of block, object, and file storage. 
  We'll also cover distributed storage systems. 

- Finally, we'll discuss the **details of data storage** in various types of databases. 
	- You'll compare the *performance* between row and column-oriented databases to understand their use cases in OLTP and OLAP systems. 
	- Then you'll explore **how data is stored** in NoSQL graph and vector databases. 
	- You'll gain hands-on practice using the ***Cipher*** language to query a Neo4j database, which is a graph database with vector search capabilities. 

The theme of this first week is how to:

> 	*"assess the trade offs between storage costs and performance at the raw ingredient and storage system levels"* 

so that you can start to make informed storage decisions when designing your data architectures.


## Storage Raw Ingredients 


### Physical Components of Data Storage

The raw ingredients used to physically store data are at the heart of all data storage systems. As a data engineer, you need to be aware of 
- the characteristics, 
- performance, 
- data durability, 
- and cost 
of these raw ingredients in order to select the storage system that is appropriate for your end use case. 

As data works its way through the data pipeline, it frequently passes through **persistent storage mediums or disk** like magnetic disk drives or solid-state drives. Also passes through volatile memory, like RAM and CPU cache. 
![[Screenshot 2025-12-22 at 14.32.18.png]]


In this section, I'd like to compare these different raw ingredients to help you understand the differences between them. 

#### Magnetic Disks
Magnetic disks often referred to as hard disk drives or **HDDs**, use *rotating platters coded in magnetic film* to store data. Magnetic disks work like **old record players**, where you need to move the stylus or needle back and forth to locate the right track on the record. 

![[Screenshot 2025-12-22 at 14.33.33.png]]
- As the record spins, the stylus reads the vibrations from the grooves to generate music. 

With magnetic disks, each platter contains **circular tracks** that are broken up into **storage units** called "***sectors***". 
![[Screenshot 2025-12-22 at 14.34.29.png]]

Together, the *track* and *sector* ***number*** create a **unique address** to organize and locate data. 
![[Screenshot 2025-12-22 at 14.35.33.png]]


When you perform a *write* operation, a "**read/write head**" magnetizes the film to physically encode binary data at a particular address by changing the magnetic field to point in one direction to store a bit representing a one, and in the opposite direction to store a bit representing a zero. 
![[Screenshot 2025-12-22 at 14.37.12.png]]

When you *read* the data, that same read/write head detects the magnetic field at the specified address and **outputs a "*bit stream***". 


#### Solid-State Drives
Solid-state drives, or **SSDs**, on the other hand, store data as **electrical charges in flash memory cells**. A charged cell represents a one bit and an uncharged cell represents a zero bit. 
![[Screenshot 2025-12-22 at 14.38.09.png]]

Since they eliminate the mechanical parts of magnetic disks, SSDs can **read and write data much faster** through purely electronic means. 

#### Performance Comparison

*How do magnetic disks compare to SSDs in terms of performance?* 

The latency or total time it takes to fetch data on a **magnetic disk** depends on:
- the ***seek time***, which is the time it takes for the read/write head to physically locate the appropriate track 
- and the ***rotational latency,*** the time it takes for the write sector to rotate under the read/write head. 
Both of these mechanical operations have physical limitations. At the time of this recording, a typical commercial magnetic disk drive rotates at around 7,200 revolutions per minute (7,200revs/min), which implies an average of **4 milliseconds of latency when fetching data**. A magnetic disk drive can only perform a maximum of several hundred IOPS or input/output operations per second. 

It's much faster to read data through electrical charges in an SSD. Newer SSDs can typically perform up to **tens of thousands of IOPS** with a data fetch latency of about **0.1 milliseconds**. This makes SSDs better for random access, meaning they can read or update data from any location very quickly. 

In terms of the actual data transfer speed, magnetic disks can read and write up to 300 megabytes of data from disk to memory or RAM in a second, while SSDs can be more than 10 times faster than that. 

![[Screenshot 2025-12-22 at 14.48.10.png]]


**Distributed storage and Parallel processing**
*You can achieve even better read and write performance through distributed storage systems and parallel processing.* 

For example, you can distribute data **across many magnetic disks and clusters and read from these clusters simultaneously**. 
![[Screenshot 2025-12-22 at 14.48.41.png]]
- In this case, your transfer speed will primarily be *limited by the network performance* and less so by the physical limitations of the disk itself

Or, you can scale SSDs by **slicing storage into partitions** with numerous **storage controllers running in parallel** to handle more data transactions simultaneously. 

![[Screenshot 2025-12-22 at 14.49.36.png]]
- With a parallel processing approach, SSDs can reach a transfer speed of **many gigabytes per second.** 

We'll dive into distributed storage and parallel processing later this week. 


**Cost Comparison**
Now that we've compared magnetic disks and SSDs in terms of their performance, **let's consider their costs.** 

- Commercial magnetic disks are typically 2-3 times cheaper than SSDs for storing the **same amount of data**. That's why even with slower data transfer speeds and higher latency, magnetic disks still form the backbone of the bulk of data storage systems. 

![[Screenshot 2025-12-22 at 14.51.12.png]]



When considering the choice between these two storage mediums, my advice is to choose: 
- **magnetic disks** as a more *cost efficient option* if you require 
	- infrequent data access in blocks 
	- of one megabyte or more at a time, 
	- and where your applications don't need super fast read and write speeds. 

- **SSDs** are commonly used in commercial deployments of **OLTP systems** because they allow *relational databases to handle thousands of transactions per second*. However, SSDs are not always the best option for analytical storage because of their higher costs. 


### Volatile Memory Ingredients
Let's switch gears to take a look at the volatile memory ingredients, namely random access memory or **RAM** and **CPU** cache. 

![[Screenshot 2025-12-22 at 15.01.32.png]]

In order for the CPU to process data, you need to transfer the data from persistent disk storage, such as SSDs and magnetic disks to RAM. 

	Physical Storage ---> RAM ---> CPU

**RAM is typically attached to a CPU**, so it's very fast and can better match the processing speed of CPU than that of disk storage. 

Now, the exact performance metrics for RAM move fast, no pun intended, and they can change very quickly. But at the time of this writing, RAM offers a data transfer speed of about **100 gigabytes per second** and a very low data fetch latency of about **0.1 microseconds**, which allows it to perform **millions of IOPS**. Now, these metrics can vary significantly based on hardware and configuration specifications, but RAM is not all powerful. 

Because it's attached to a **CPU**, it's more expensive. 

- As of now, RAM is usually ***30-50 times more expensive** than SSD per unit of stored data*. 
- It's also **volatile**, meaning that if you were to lose power, the data stored in RAM could be lost in less than a second. 

You usually only use RAM to store the code that the CPUs execute and the data that's code directly processes, and nothing that needs to persist over time. This makes RAM good for 
- *caching*, 
- *data processing*, 
- or *indexing*, 
which we'll discuss later in this course. 

>	*NOTE! Several databases **treat RAM as a primary storage layer** because it allows very fast read and write performance. In these applications, you should always keep in mind **the volatility of RAM**. Even if data memory is replicated across a cluster, a power outage that brings down several nodes could cause data loss.* 


Another type of memory that's even faster than RAM is **CPU cache**, which is located directly on the CPU processing chip. 

You want to use **CPU cache** to store frequently accessed data for ultrafast retrieval during processing. Because of its location, it has a data fetch latency of about **one nanosecond**, and a **super high data transfer speed of about one terabyte per second.** Caches are not only used for CPU cache, but you can also use them in multiple applications to store frequently and recently accessed data in a fast access layer. 

For example, you can use a browser cache to store downloaded web resources so that you can load a web page faster. You can also use a database cache to store the results of frequently used queries.
![[Screenshot 2025-12-22 at 15.02.03.png]]


With that, we cover the performance and cost trade offs between the different ingredients that physically store data. 

As a data engineer, having a solid understanding of these trade offs can help you evaluate different storage technologies to ensure that they meet the performance requirements of your data processing workloads. 



### Processes Required for Data Storage

So far, when talking about the storage hierarchy, we've been focusing on the bottom layer, the raw storage ingredients that are used to physically store data. 

![[Screenshot 2025-12-22 at 13.20.13.png]]

But the bottom layer also consists of other components and processes that are required for storing and transmitting data in modern data systems. And so in this section, I'd like to take a moment to go over how **networking**, **CPU** ,**serialization**, and **compression** play a part in your storage systems. 


In the cloud era, storage systems **are increasingly distributed**. This means that your data can be *split up, replicated, and spread out across many connected servers* to enhance 
- read / write performance, 
- data durability, 
- and availability. 

So, you can consider **networking** and the **CPU** required to handle the details of servicing read and write requests (like aggregating read results and distributing writes across many servers) **as part of the raw ingredients of storage solutions.** 

>	*You'll learn more about distributed storage systems later on.* 

Regardless of whether you're storing data on a single server or across a distributed storage system, when you store data in a file or database or send it over to a network, you need to transform it into a different format. That's because **data stored in memory has different representation than the data that's stored in disk.** 
![[Screenshot 2025-12-22 at 15.08.18.png]]

- In system memory, you will store data in data structures that are optimized to be efficiently accessed and manipulated by the CPU. 
- But that format is **not suitable for persistent storage in disk** or **transmission over a network**. 

#### Serialization / De-serialization

To make data suitable for t storage in disk and transmission over a network, you need to use a process known as *"**serialization**"* to translate the data into a **standard format**, usually a sequence of bytes that can be efficiently stored or shared over a network.

And when you want to read the data, you'll use a process known as ***"de-serialization"*** to reconstruct the original data structures from the serialized format.

![[Screenshot 2025-12-22 at 15.12.58.png]]


**Row-Based vs Column-Based Serialization**

You can serialize data using a row or a column-based approach. 


**ROW-BASED SERIALIZATION**
In **row-based** serialization, you encode and sort tabular data ***record-by-record*** so that a consecutive sequence of bytes represents one row of data. 

![[Screenshot 2025-12-22 at 15.26.16.png]]
![[Screenshot 2025-12-22 at 15.26.33.png]]
![[Screenshot 2025-12-22 at 15.26.59.png]]


If you're encoding **semi-structured data**, you encode the data *object-by-object* or *document-by-document*, so that data representing a single object or document is represented as a **consecutive sequence of bytes** on disk. 

![[Screenshot 2025-12-22 at 15.28.41.png]]

Row-based approach is ideal for **transactional operations** where you need to access data from an entire row. 
![[Screenshot 2025-12-22 at 15.29.52.png]]



**COLUMN-BASED SERIALIZATION**
In **column-based** serialization, you encode and store data column by column so that a consecutive sequence of bytes in a serialized format represents a column. ![[Screenshot 2025-12-22 at 15.30.28.png]]
![[Screenshot 2025-12-22 at 15.30.53.png]]


And if you're encoding column oriented **semi-structured data**, the values for a single ***key*** across all the objects is stored as a **consecutive sequence of bytes**. 
![[Screenshot 2025-12-22 at 15.32.10.png]]

This is perfect for analytical queries where you need to perform operations on specific columns. ![[Screenshot 2025-12-22 at 15.32.30.png]]



As a data engineer, you'll likely encounter a wide range of data serialization formats, from human readable text formats like 
- CSV, 
- XML, and 
- JSON 
that are widely used to exchange data between systems and applications, to binary formats like: 
- avro, and 
- parquet 
that are even more efficient for storing and querying data. 


![[Screenshot 2025-12-22 at 15.38.40.png]]

**CSV**
- CSV is a popular **row-based format**
- It's quite prone to error because it doesn't support a defined schema. 
- So it's up to the application to define the meaning of each row and column. 
- If an application adds a new row or column to its data, you have to handle that change manually. 

So if possible, you should avoid using this format in your data pipelines. 

**XML**
XML, short for "extensible markup language" was popular when HTML and the Internet were new, but it's now viewed as a **legacy format** because it's generally *slow to serialize and deserialize for data engineering applications.* 
XML has largely been replaced by the **JSON format** for plain text object serialization. 

**JSON**
Nowadays, JSON is viewed as a new standard for data exchange over APIs, and it's a very popular format for data storage. 

**PARQUET**
In terms of binary formats, parquet is a **column-based format** that's **designed for efficient storage and big data processing**. 

**AVRO**
Avro is a **row-based format** that uses a schema to define its data structure, and it supports schema evolution. 

> 	*You'll learn more about row versus column-based storage in databases and about the popular parquet format later this week.* 


#### Query Performance
The decisions you make as a data engineer around *serialization* and *how you store data* in files and databases can impact overall **query performance.** 

By simply switching the serialization format from CSV to parquet, you could potentially improve job performance by a factor of 100. 


#### Data Compression
Now, let's say that you've serialized your data so that it can be stored on disk or transmitted over a network. 

As your data volume grows, you might want to: 
- enhance **storage efficiency** and 
- **speed up the transmission** of your data. 

Data compression is a way to *reduce the number of bits needed to represent the data*, and it's a critical component for modern data applications that require increasingly large datasets. 
![[Screenshot 2025-12-22 at 15.42.17.png]]



With compression algorithms, instead of directly encoding the data into a sequence of bits, 
![[Screenshot 2025-12-22 at 15.43.12.png]]


you use *sophisticated mathematical techniques* to **identify redundancy** and **repetition** in your data, then re-encode the data in a more efficient way. 
![[Screenshot 2025-12-22 at 15.43.45.png]]



For example, traditional compression algorithms that you can apply to text-based data formats such as CSV, JSON, and XML, identify the characters that occur most frequently and encode them differently than the characters that occur less frequently. 

Instead of mapping each character to a sequence of bits of fixed length, these algorithms match common characters to shorter bit sequences and less common characters, so your compressed data file **takes up fewer bits in total to store on disk.** 

![[Screenshot 2025-12-22 at 15.44.45.png]]



The **ratio** of the compressed file size relative to the original uncompressed file size is called the **"compression ratio"**. 
![[Screenshot 2025-12-22 at 15.45.24.png]]


In addition to reducing disk space, compression also *improves query performance* because it reduces the input and output, or I/O time needed to load the necessary data from disk to memory when processing a query. 

In recent years, engineers have created a new generation of compression algorithms that prioritize speed and CPU efficiency over compression ratio. 

These algorithms are frequently used to compress data in data lakes or columnar databases to optimize for fast query performance. 


## Compression Algorithms

#### Compression Overview
To encode data into a *sequence of bits*, you can use raw encoding which relies on the data type (boolean, integer, double, character) to map the data item into a sequence of bits of fixed size. This is the **raw uncompressed method of encoding**. 

Compression algorithms look for *redundancy* and *repetition* in the data values, then re-encode data to reduce the overall number of bits that represent data in storage systems. For example, one way to compress textual data is to map the most frequent characters to codes that use less number of bits than the codes mapped to the less frequent characters. In this way, the total number of bits representing a text could be less than the total number of bits used in raw encoding. 

Compression algorithms utilize more sophisticated mathematical techniques to identify and remove redundancy; they can often realize compression ratios of **10:1 on text data**. Note that we’re talking about **lossless compression algorithms**. *Decompressing data encoded with a lossless algorithm recovers a bit-for-bit exact copy of the original data*. 

Lossy compression algorithms for audio, images, and video aim for sensory fidelity; decompression recovers something that sounds like or looks like the original but is not an exact copy. Data engineers might deal with lossy compression algorithms in **media processing pipelines** but not in serialization for analytics, where exact data fidelity is required.

Traditional compression engines such as `gzip` and `bzip2` compress text data extremely well; they are frequently applied to JSON, XML, CSV, and other text-based data formats. 

In recent years, engineers have created a **new generation of compression algorithms** that **prioritize speed and CPU efficiency over compression ratio**. Major examples are: 
- Snappy, 
- Zstandard, 
- LZFSE, and 
- LZ4.

#### Compression in Column-Based Formats
Some algorithms are **generic** and can be used in both **row-stores** and **column-stores** to compress data using a general-purpose algorithm: LZO (1996), LZ4 (2011), Snappy (2011), Brotli (2013), Oracle OZIP (2014), and Zstd (2015). [Source](05-compression.pdf)

However, some algorithms are specific to **column-stores** since they use the fact that consecutive values from the same column are stored together on disk. Compression algorithms benefit from repetition and redundancy in data, and values from the same column can have this characteristic.

Consider the following table:
![[Screenshot 2025-12-22 at 15.55.06.png]]

- If data is stored in **rows**, it means that you need to store the values of product sku, quantity, price, customer id, store ID, and state **all together.** 
	- Since each value represents a different feature, the algorithm might not encounter a lot of repetitions. 
- On the other hand, if data is stored in **columns**, then the product SKUs are all stored together, same thing for store ID and state. 
	- Each column can have lots of repeated values.
	- This column characteristic allows the compression algorithm to detect the common patterns in data easier, as well as represent the data more efficiently. 

In addition to reducing disk space, compression also improves database performance, meaning it helps the database process queries faster since less data is read from disk into memory, and from memory to CPU.


#### **Examples of Compression Algorithms Used on Column-Based Formats**

**Run-length Encoding**
*Run-length encoding (RLE)* compresses a run of the same values in a column to a more compact representation. Each run is replaced with a tuple that has 3 elements -- (value, start position, runLength), where each element is represented with a fixed number of bits.

For example, here's the product sku column data from the table mentioned earlier:

>	_34 34 34 63 32 32 32 67 67 67_

With RLE, you'll get this result:

>	_(34, 1, 3), (63, 4, 1,), (32, 5, 3), (67, 8, 3)_

RLE can be used in column-oriented systems where the columns have few distinct values, meaning that you'll likely have runs of the same value stored together.


**Bit-Vector Encoding (or bitmap encoding)**
With this algorithm, **each distinct value is associated with a sequence of bits** where the length of the sequence is the same as the number of records/rows in the column: a ‘1’ in the _i-_th position means that the distinct value appears in the _i-_th row of the column, and ‘0’ otherwise.

For example, here's the product sku column data again:

>	_34 34 34 63 32 32 32 67 67 67_

With this algorithm, this data would be represented by four sequences of ten bits (i.e. the number of rows):

- bit-string for value 34: _1110000000_
- bit-string for value 63: _0001000000_
- bit-string for value 32: _0000111000_
- bit-string for value 67: _0000000111_
    

Bit-vector encoding is most useful when columns have a **limited number of unique values** (such as states in the US, store ID, product ID). However, it can be used even for columns with a large number of distinct values, especially if it is combined with another compression such as RLE (to further compress it).


## **Optional Resources**

- Other compression algorithms: [The Design and Implementation of Modern Column-Oriented Database Systems](columnstoresfntdbs.pdf)
- [Compression encodings supported in AWS](https://docs.aws.amazon.com/redshift/latest/dg/c_Compression_encodings.html)
- There is an additional file format (Avro) that you may encounter as a data engineer, especially when working with *streaming systems such as **Kafka***. Avro is a row-oriented binary file format that encodes semi-structured data in a way that is more efficient than Binary Json. To learn more about Avro, check the following two resources:
    - [Schema evolution](https://martin.kleppmann.com/2012/12/05/schema-evolution-in-avro-protocol-buffers-thrift.html)
    - [Parquet vs Avro](https://airbyte.com/data-engineering-resources/parquet-vs-avro)



## Cloud Storage Options: Block, Object and File storage

As a data engineer, you're faced with a large number of cloud storage options. These storage systems exist at a level of abstraction **above the raw ingredients** that you saw previously. 

In this section, we'll look at the three common types of cloud storage systems. These are: 
- block storage, 
- file storage, and 
- object storage. 

You'll need to consider the **performance and scalability trade-offs** between these options when choosing the best system for your use case. 


### File Storage Systems
Let's start with the file storage systems, which organize files into directory trees, similar to how the folders might be organized on your laptop. 

![[Screenshot 2025-12-22 at 16.18.30.png]]

- Each folder contains **metadata** for its files and subfolders, detailing the names, owners, last modification dates, access permissions, and location pointers to the actual files and subfolders themselves. 

- To locate a file on disk, you give the operating system a **path** to follow, like `/Users/matthewhousley/output.txt`. 
	- To locate the hierarchical structure from left to right, your operating system starts at the **root directory**, indicated by the forward slash "`/`", then it finds the User directory, then the Matthew Housley subdirectory, and finally it locates the file named output.txt. 


>	 *In your work as a data engineer, you use file storage when you need to provide **centralized access to files** that need to be **easily shared and managed by multiple users or host machines**.* ![[Screenshot 2025-12-22 at 16.23.36.png]]

You can use a managed cloud file storage service, like **Amazon Elastic File System, or EFS**. This service provides you, your applications, and your stakeholders access to shared files over a network without the hassle of managing networking, scaling disk clusters, or configuration. 

>	*File storage is often **built on top of block storage**, where the complexity of the underlying storage mechanisms is abstracted from you.* 

Despite being a more accessible and understandable storage format, file storage systems **don't have the highest read and write performance** because they need to keep track of the file hierarchy. 


### Block Storage
Block storage, on the other hand, provides the **performance and flexibility needed for high-speed transactional data access**. 
![[Screenshot 2025-12-22 at 16.33.46.png]]
- Block storage divides files into **small, fixed-size blocks of data** that you can store on magnetic disks or SSDs. This allows you to **precisely allocate storage space for any given piece of data**. 
- Each block has a **unique identifier**, like the *address* for that block, which helps you efficiently retrieve and modify data in individual blocks, providing higher performance and lower latency than file storage. 
- You often design block storage systems based on a **distributed architecture**, **spreading blocks of data across multiple storage disks**, which leads to *higher scalability* and stronger *data durability*. This makes block storage the backbone of most modern storage solutions, including: 
	- your local file systems, 
	- transactional databases, and 
	- virtual machine storage. 



When you store a file in block storage, the storage application writes the data into *multiple blocks* and records the block's identifier into a **data lookup table**. 

When you request a specific file, the application retrieves the data from the relevant blocks and merges them into the original sequence for you to read. This is all abstracted from you.
![[Screenshot 2025-12-22 at 16.34.49.png]]

You can locate any block by its unique identifier and update the block without having to replace the entire file. 


- This makes block storage ideal for use cases where your data is **accessed and modified** **often**. 
	- Transactional database systems generally access disks at a block level for high random access performance. 
	- This enables OLTP systems to perform small and frequent read and write operations with **low latency.** 
	- Block storage is also used to provide **persistent storage for virtual machines**, like EC2 instances. 
		- When you create EC2 instances, you automatically attach a *root storage device* that's backed by a block storage volume to each of your instances. You can install the operating system, file system, and other computing resources in the block storage volume. ![[Screenshot 2025-12-22 at 17.49.30.png]]
	
	With EC2, the default storage is **Amazon Elastic Block Store, or EBS,** and you can choose from various EBS volume types depending on your use case. For example, some volumes are built on **high-performance SSDs** and are great for *latency-sensitive workloads*, while others use **cost-efficient magnetic disks** to store *data that's infrequently accessed*. 

Since block storage volumes are typically **attached to compute instances**, **scalability is limited** by how much you can scale your compute resources. And so block storage typically caps out at a few terabytes. Object storage, on the other hand, decouples the data storage layer from the compute layer, so it can scale to *petabytes* of storage or more. 

> 	*In a cloud environment, where your storage capacity is limited only by budget, with object storage, you'll likely run out of money before you run out of object storage space.* 

#### Object Storage
Cloud object storage allows you to process data with **ephemeral clusters** and scale these clusters up and down *on demand*. These ephemeral clusters exist behind the scenes, and you don't need to worry about them. This is a big factor in making big data available to smaller organizations that can't afford to own hardware for data jobs that they'll only occasionally run. 

- Unlike a traditional hierarchical file storage system, you store files as *immutable data objects in a flat structure* in an object storage system. 

- You organize objects into **top-level *logical* containers**, like an S3 **bucket**, and each object is assigned a **unique identifier, or key**, that you can use to find the object within its container. ![[Screenshot 2025-12-22 at 17.53.05.png]]
  
  So an object identifier in S3 might look something like this. 
  `s3://o'reilly-data-engineering-book/data-example.json|
	- The first part refers to the bucket name, which **must be unique across all of AWS**
	- and the path after that bucket is the ***key*** pointing to the particular object. 
  ![[Screenshot 2025-12-22 at 17.53.52.png]]`

- Once you write the data initially, the object becomes **immutable**. Even if you want to change one single character of a 1GB file, you'll have to rewrite the entire object instead of just making a small change like you would in block storage. 
	- This might seem like a constraint, but it actually *removes* the overhead of supporting chain synchronization. So you can distribute the objects across many storage nodes that each contain their own disks, eliminating the need to propagate data changes across all these nodes. ![[Screenshot 2025-12-22 at 17.57.25.png]]
	- This allows object stores to scale horizontally and support extremely performant parallel reads and writes across many disks. Each node holds *shards* of objects, which are replicated acros  s multiple nodes for durability. 


- This high scalability and durability makes object storage **ideal for the storage layer of cloud data warehouses and data lakes**. It allows these storage abstractions to accommodate massive volumes of data in a cost-efficient way. But since objects are immutable, object storage **is not good at supporting transactional workloads** where many small update operations need to happen with low latency. 

-  Object stores are also great for storing data needed in **massive OLAP systems** that focus on read-heavy analytical workloads rather than write-heavy transactional operations. 
  
- In modern data engineering applications, object storage also plays a crucial role in **machine learning pipelines** that require large amounts of unstructured training data such as raw text, images, videos, and audio. 

| Ideal for...                                                                 | Not ideal for...                   |
| ---------------------------------------------------------------------------- | ---------------------------------- |
| Storage layer of cloud data warehouses or data lakes                         | Supporting transactional workloads |
| Storing data needed in OLAP systems                                          |                                    |
| Machine learning pipelines:<br>- Raw text<br>- Images<br>- Videos<br>- Audio |                                    |


And so file, block, and object storage all have wide use cases:

| File Storage                                                    | Block Storage                                             | Object Storage                                       |
| --------------------------------------------------------------- | --------------------------------------------------------- | ---------------------------------------------------- |
| Supports data sharing                                           | Supports transactional workloads                          | Supports analytical queries on massive datasets      |
| Easy to manage with low performance and scalability equirements | Allows frquent read and write operations with low latency | Offers high scalability and parallel data processing |


## Storage Tiers - Hot, Warm, Cold Data

Most Cloud data storage services offer different storage tiers you can choose from depending on your costs, access speed, access frequency, and compliance requirements. 

When choosing a storage tier, I recommend you consider the **hot, warm and cold data classification method** that's based on how frequently data is accessed and used. 
![[Screenshot 2025-12-22 at 18.08.00.png]]

**Hot Storage
On one end of the spectrum, you have **hot data**, which is data that's *accessed frequently* and requires *fast retrieval times*. 

For example, in a product recommendation application, you need to frequently access the product catalog and the purchase history of your users. You might also want to store the results of frequently run queries in a *cache* so that you can quickly serve customers with product recommendations. 

> 	To serve fast read access, you typically want to store hot data in systems that utilize *high performance storage mediums* like **SSD** and **memory**. 

The **storage cost** for hot data is typically **more expensive**, but the retrieval time and compute resources required to access the data is relatively low because you store data in a way that optimizes for fast access. 

**Warm Storage**
Warm data is accessed **less frequently than hot data, but still needs to be readily available.** For example, this could be data that's used for *regular reports and analysis* that *don't need to be updated in real time*. 

You typically want to store warm data in l**ower cost storage systems that utilize slower magnetic disks or hybrid storage systems.** Compared to hot data, the storage costs for warm data is lower, but it typicall**y takes more time and compute resources to retrieve the data.** 

**Cold Storage**
Then on the other end of the spectrum, you have cold data. This is data that's rarely accessed and is often ***archived***. 

For example, you might decide to archive project documentation or keep old emails for compliance purposes. You want to store cold data in the **most cost effective storage tier that's built on low cost magnetic disks.** So the storage cost for cold data is the most inexpensive, but compared to warm data, *it'll take you longer and require more compute resources to retrieve this data.* 

- In general, the storage price goes down as you move from high performing storage with fast access, to lower performing storage with slower access. 
- If you store all of your data in hot storage, you'll be able to access your data very quickly, but at a tremendous storage price. 
- If you store all your data in cold storage, then you'll save on storage costs, but it'll be at the expense of long retrieval times and high compute required for data access. 
- You'll typically want to choose a combination of storage tiers for your various storage needs. 

|                         | Hot Storage                        | Warm Storage                             | Cold Storage                  |
| ----------------------- | ---------------------------------- | ---------------------------------------- | ----------------------------- |
| **Access<br>Frequency** | Very frequent<br>(e.g. real-time)  | Less frequent<br>(e.g. weekly batch)     | Infrequent<br>(e.g. Archived) |
| **Example**             | Product recommendation application | Regular reports and analyses             | Archive                       |
| **Storage <br>Medium**  | SSD & Memory                       | Magnetic disks or hybrid storage systems | Low-cost magnetic disks       |
| **Storage Cost**        | HIGH                               | Med                                      | low                           |
| **Retrieval Cost**      | low                                | Med                                      | HIGH                          |


As an example ![[Screenshot 2025-12-22 at 18.19.54.png]]
- When storing data in Amazon S3, you might store your frequently accessed hot data for real time transactions in what's called the *"S3 express one zone"* or the "*S3 standard tiers*". 
- You might store warm data that needs to be accessed say weekly or monthly for finetuning a product recommendation system in the *"S3 Standard infrequent access"* or "*S3 one zone infrequent access*" tiers. 
- Finally, you want to archive historical cold data in *one of the "S3 glacier" tiers*. 



## Distributed Storage Systems

When designing your storage solutions, aside from access frequency, you also want to consider things like the **scalability** and **durability** of your storage solutions. 

As your data **storage needs** increase and the data access patterns become more complex, you'll inevitably outgrow storage on a single machine and move to distributing data to more than one server. 

In fact, **distributed storage is the default way to store data in the Cloud**, whether you using block, file, or object storage. In this section, I'll build on what you've previously learned and dive deeper into the details of how a distributed storage architecture works. 


In a distributed storage system, you *distribute* and *replicate* data **across multiple servers** known as "**nodes**" that are connected by a **network**. 

![[Screenshot 2025-12-23 at 10.54.10.png]]

Groups of nodes make up what's called a cluster, and these clusters collectively make up the distributed storage system. 

![[Screenshot 2025-12-23 at 10.58.22.png]]

Each node contains **storage mediums**, such as magnetic disks or SSDs ,to physically store the data. The storage capacity of your distributed system is the total capacity of all the individual nodes, and **each node typically has processing capabilities to handle data management, replication, and access control.** 
![[Screenshot 2025-12-23 at 10.59.34.png]]

Storing data this way allows you to **easily scale your storage systems horizontally**, meaning, you can **add more nodes to clusters** to accommodate growing data volumes and tackle increased workloads. 
![[Screenshot 2025-12-23 at 11.00.59.png]]
![[Screenshot 2025-12-23 at 11.01.23.png]]

In the single machine storage architecture, you can *only* achieve **vertical scaling**, which means you can only upgrade the storage capacity of a single server. 
![[Screenshot 2025-12-23 at 11.02.08.png]]

- By spreading data across multiple nodes and replicating it across clusters, you can also ensure a **higher level of "*fault tolerance*" and "*data durability*"**. Which is to say , your data will persist over time even in the event of failure to one or more components of the system. 

- This goes hand in hand with **"*high availability*"**. If a node becomes unavailable due to hardware or software failure, network outages or other disruptions, you can still access the data from another node that's not impacted, maybe because it's in a different geographical location. ![[Screenshot 2025-12-23 at 11.04.42.png]]



In terms of performance, distributed storage systems **divide large processing tasks into smaller sub tasks that are handled by individual nodes**. 
![[Screenshot 2025-12-23 at 11.05.24.png]]
![[Screenshot 2025-12-23 at 11.05.47.png]]
![[Screenshot 2025-12-23 at 11.06.11.png]]

 - This helps the system process many read and write operations ***"in parallel"***. 


Since data is replicated across multiple nodes, the system can also **serve read requests from the nearest or least congested replica node** so you can access the data faster. 

Because of these advantages, many storage solutions, including *object storage, Cloud data warehouses, Hadoop Distributed File System or HDFS, Apache Spark*, and **many more** rely on distributed storage architecture. 
![[Screenshot 2025-12-23 at 11.08.22.png]]


### Methods of Distributing Data
As a data engineer, you'll find there are **two common ways** to distribute data across multiple nodes: 
- Through **replication** 
- Through **partitioning** (or "sharding") 

With "***replication***", you ***keep a copy of the same data*** on **several different nodes,** potentially in different geographical locations. This redundancy results in **higher availability** and helps improve performance. ![[Screenshot 2025-12-23 at 11.12.15.png]]
![[Screenshot 2025-12-23 at 11.12.27.png]]
**"*Partitioning*"** on the other hand, also known as ***"sharding"***, splits a big dataset into smaller subsets called partitions or shards, and then different partitions can be assigned to different nodes. 
![[Screenshot 2025-12-23 at 11.12.51.png]]
![[Screenshot 2025-12-23 at 11.13.24.png]]


In practice you'll likely use a ***combination of these methods*** to distribute data. 
- You partition a large dataset and distribute the shards to different nodes. 
- Then you'll replicate those nodes to create a **good level of redundancy**. 
>	![[Screenshot 2025-12-23 at 11.14.43.png]]



Most databases can automatically partition and replicate your data in a way that's abstracted from you, or you can specify replication and partition parameters for more control over your distributed storage system. 


### The CAP Theorem

One challenge with distributed storage is that *it takes time to replicate changes across nodes.* 
When you're trying to access data from a node **that's currently being updated**, you can either: 
- wait for the update to complete before accessing the data, 
- or you can access the most recent data that's *currently* available in that node. 

![[Screenshot 2025-12-23 at 11.15.52.png]]

This trade off is summarized by something called the CAP theorem, which states that 

>	*"Any distributed system can only guarantee two out of three properties: consistency, availability, and partition-tolerance."* ![[Screenshot 2025-12-23 at 11.18.39.png]]

**Strong "consistency"** means that *every read reflects the latest write operation*. 

>	*Note that this is different from the consistency component in the "ACID principle" you saw in Course 2, which says that any changes to the data made within a transaction must follow the set of rules or constraints defined by the database schema.* 
>	
>	The ACID consistency principle ensures that the database will transition from one valid state to another, which is a condition that is facilitated by the strong consistency property. 


"**Availability**" means that *every request will receive a response*, even if it's **not necessarily the most recent data**. 

"**Partition-tolerance**" means that the system *continues to function even when the network experiences disruptions or failures* that isolate some nodes from others. Since some distributed system is safe from network failures or unforeseen disruptions, network partitioning usually has to be tolerated, which is to say, building systems that guarantee partition-tolerance is usually a given. 

You usually have to choose **between consistency or availability** because ,remember, the CAP theorem states that any distributed system can only guarantee *two out of the three properties*. ![[Screenshot 2025-12-23 at 11.22.03.png]]

This means that in the scenario where you're trying to access a node that's still being updated, you can either: 
- design your system to *cancel the request*, which decreases availability, but ensures consistency, 
- or you can configure the system to *proceed with a read operation*, which provides high availability, but risks inconsistency. 


Database systems such as an RDBMS that are designed to be **"ACID compliant"** will choose consistency over availability. 
Whereas NoSQL database systems that are not ACID-compliant, we'll typically choose availability over consistency.

| ![[Screenshot 2025-12-23 at 11.24.23.png]] | ![[Screenshot 2025-12-23 at 11.25.14.png]] |
| ------------------------------------------ | ------------------------------------------ |
|                                            |                                            |


### ACID vs BASE principles
In contrast to the ACID principles, there's actually a set of principles called ***BASE*** that you can use to design and evaluate your distributed data systems. 

BASE stands for: 
- **B**asically **A**vailable *(meaning that consistent data is available most of the time)* 
- **S**oft-state *(meaning it's uncertain whether the transaction is committed or uncommitted)*
- **E**ventual consistency *(meaning at some point, reading data will return consistent values)* 

As a data engineer, you need to understand how your database handles consistency, which can be determined by: 
- vthe database technology itself, 
- the database configuration parameters, 
- or the consistency configuration at the individual query level. 

Once you understand the technical limitations and business use cases for your data, you might need to **negotiate consistency requirements with other stakeholders**. 

**Example:**
Let's revisit the scenario from Course 1, where you needed to *serve sales data to the data scientists so they can update an analytics dashboard* for the marketing team. 

- The software engineers have set up a **read replica** of the production database, so you can ingest, transform, store, and serve the required sales data to the data scientists. 
- Suppose that the database is implemented using **Amazon RDS Aurora**, which is a distributed relational database service. 
	- With this database, there's a "**main database instance**" that supports **strict read after write consistency**, meaning strong consistency. 
	- But if the data scientist values immediate access to sales data, even if it's not the most up to date data, then you can set up "**read replicas**" and RDS that follow the BASE principle. These read replicas will track all changes made to the main database instance and update their own copies of the data. 

Then on a query-by-query basis, you and the team can decide to read from either: 
- the main database instance that supports strong consistency, 
- or you can read from one of the read replicas that supports the eventual consistency but high availability. 

![[Screenshot 2025-12-23 at 11.34.59.png]]


## Database Partitioning/Sharding Methods

Let’s take a closer look at one approach for implementing distributed storage, specifically for databases, known as **"database sharding"**.

Say you want to distribute the following dataset across multiple nodes.

| ---- Customer ID ---- | ------- Name ------- | ------- Country ------- |
| --------------------- | -------------------- | ----------------------- |
| 10023                 | Sanjay               | IND                     |
| 27181                 | Jane                 | USA                     |
| 98221                 | Mo                   | IND                     |
| 10134                 | Abdul                | CAN                     |
| 33410                 | Mina                 | USA                     |
| 30191                 | Sam                  | USA                     |
| …                     | ...                  | ...                     |
You need to **split** the dataset into partitions or _shards_, where each shard contains **unique** *rows* of data and the shards will collectively make up the whole dataset. Then you can distribute these shards across the nodes in your system. You can use a **database sharding method or rule** to construct a _shard key_ that indicates how the data will be partitioned.

### Common Sharding Methods

#### Range-based sharding
This method splits the rows based on a range of values. 

For example, let’s say you want to group the rows based on the first letter of the customer's name. One shard might hold customers whose names start with A through J, another shard might hold names starting with K through R, then a third shard could hold names starting with S through Z. Then the shard key you see in the following tables tells the database which node to distribute each row of data to. This is a straight-forward method **but can result in unbalanced shards, meaning unbalanced nodes.**

| Name               | Shard Key |
| ------------------ | --------- |
| Starts with A to I | A         |
| Starts with J to S | B         |
| Starts with T to Z | C         |


#### Hashed sharding
This method uses a **mathematical formula** called a **"hash function"** to determine how to partition your data. For example, you can simply assign alternating hash values of 1 or 2 to each row to separate the rows into two shards. Then this shard key tells the database where to distribute the data for each row. This method can result in a **more even distribution of data across nodes.**

#### Geo sharding
This method partitions data based on **geographical location**. Then you can store the customer's information in nodes that are physically located in that location. By *reducing the physical distance between the shard and the customer,* you retrieve data faster.

#### Other methods
There are also other methods that split the data based on meaningful attributes, for instance, the *customer’s occupation or favorite color.*

### **Resources** (optional further readings)
- [What is database sharding?](https://aws.amazon.com/what-is/database-sharding/)
- [Designing data intensive applications](https://www.amazon.com/Designing-Data-Intensive-Applications-Reliable-Maintainable/dp/1449373321/ref=sr_1_1?adgrpid=1344703291324157&dib=eyJ2IjoiMSJ9.i1bUGZK7N-KyWM2sQR7-B8KYS_yn_vgEDIPgCZKZEqrD3_kYv1WLMRNg2a_cyMTZkenScKZLD1xQT6PoxGtZjpfYLwagMBcOcvqwyg12Ux6vvPPHgXX1vMZoOg1vTM_pc7M5GoJOYAWtL-UQU8rix049vlX-qOUnpYLTJ2MrssfiHjzXSj62mtpldPZ9F8sSVwb2QyjkabDuQFUBKt8wljiPffjJIMY5B8rR7JfDvO8.HDmJ7Lu7-7fnydSo4DSG8hxechXwbUNz0baNI01HWH0&dib_tag=se&hvadid=84044027549737&hvbmt=be&hvdev=c&hvlocphy=44152&hvnetw=o&hvqmt=e&hvtargid=kwd-84044312865379%3Aloc-190&hydadcr=16438_10463512&keywords=designing+data+intensive+applications&qid=1713574421&sr=8-1) - Chapters 5 and 6 (Replication and partitioning)


