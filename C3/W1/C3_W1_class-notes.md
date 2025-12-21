
## Course Intro & Overview 

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

In your work as a data engineer, having deep expertise in storage solutions and queries will help you be more successful at every stage of the lifecycle. >> Great, let's get started.

