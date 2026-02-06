So far in this course, you've modeled data for analytical use cases by defining a normalized schema, a star schema, and a one big table schema. 

You've also modeled data for machine learning use cases and done some simple transformations on small datasets using Pandas DataFrames. 

But when you're working with large-scale data, that requires complex transformations, Pandas data frames alone might not offer the scalability and performance you need and so this week: 
- we'll get into ***the details of transformations*** 
- and look at the ***technical considerations*** 
for various data processing frameworks that you can use to transform your data. 


As I already mentioned, *the transformation stage* of the data engineering lifecycle is when you **manipulate and enhance data for downstream stakeholders**, and is where you, the data engineer, can really add value to the organization. 

![[Screenshot 2026-02-04 at 23.14.37.png]]

For example, 
- you can clean and combine data from multiple sources 
- and then store it in a data lake or data warehouse to create a central source of truth for your organization. 
- Inside your data warehouse, you can leverage its massively parallel processing ability to transform the data into a star schema or a data vault so that users can more easily query the data. ![[Screenshot 2026-02-04 at 23.14.59.png]]
- Inside your data lake, you can apply a series of transformations by moving your data from the raw to the cleaned or transformed zone. Finally, to the enriched zone so that it's ready to be consumed by data users. ![[Screenshot 2026-02-04 at 23.15.23.png]]

To apply these transformations, you need to consider things like the size of the data, specification of hardware that's available to you, and the performance requirements. These considerations will help you decide whether you can just process the data on a single machine, or use a distributed processing tool like ***Spark***, and whether you should write your transformation logic in SQL or another language like Python. You can also apply transformations to streaming data, allowing your stakeholders to perform real time analytics. In this case, you'll need to consider the ***latency requirements*** of the system and ensure that your transformations don't incur any delays. 

![[Screenshot 2026-02-04 at 23.16.28.png]]

This week, we'll start by going through some of the ***batch transformation use cases*** you might encounter as a data engineer. Then we'll cover two distributed processing frameworks. 
- **"Hadoop MapReduce"**, which uses disks for storing and processing data, 
- and **"Spark"**, which is an in memory processing framework. 

>	*Many data engineers consider Hadoop to be a legacy technology due to its complexity, the high cost of scaling, and significant maintenance requirements. But I also think that it's important for you to understand the MapReduce paradigm, since it influences many of today's distributed systems.* 

We'll also compare SQL-based transformations with those implemented in other languages such as Python. 

In the lab, you'll get a chance to perform the same transformations you did with DBT previously in this course, but this time, you'll implement these transformations outside the data warehouse. This week, you'll also hear from Navnit Shukla, a senior solutions Architect at AWS, and an expert on the AWS Glue Service, which is a tool built on Spark. He'll show you how you can generate the Glue jobs for processing your data using Glue Studio.

Then in the second lesson, we'll take a look at ***streaming transformations***. You'll implement a change data capture or **CDC pipeline** using **"Kafka"** and **"Flink"** to capture changes in our data source and update the data in your system accordingly. 

![[Screenshot 2026-02-04 at 23.17.40.png]]


## Batch Transformation Patterns and Use Cases

In my discussions with data teams from many big tech companies, I've noticed that a lot of a data engineer's work is **batch processing** to serve data for analytics, and in some cases, for training machine learning models. 

With batch transformations, you manipulate **discrete chunks of data on a fixed schedule** like daily, hourly, every 15 minutes or less to support ongoing reporting, analytics, and machine learning use cases. Let's go through some common batch transformation patterns. 


### Target Model

Suppose you created a model for your data. It could be based on a Kimball Star schema, Data Vault, or some other data modeling approach.

![[Screenshot 2026-02-06 at 17.24.09.png]]

Now, you're ingesting data into your system, and you need to apply transformations to restructure the source data into the expected form. You have a few options here: 

- You could follow a traditional **ETL approach** and rely on an external transformation tool to extract and transform the data based on the data model you created before loading the transformed data into a target system, such as the data warehouse. 
  ![[Screenshot 2026-02-06 at 17.25.13.png]]

- Or you can follow an **ELT approach** where you extract raw data from a source system and import it directly into a data warehouse. Then you clean and transform the data in the warehouse itself, relying on the storage and computing capabilities of the warehouse. 
  ![[Screenshot 2026-02-06 at 17.26.13.png]]

- Or you can take a hybrid approach called **"EtLT"**, where the `t` refers to the simple transformations you apply to clean the data, like duplicating the data before you load the clean data into the data warehouse. Then the `T` refers to the transformations you apply inside the data warehouse to restructure the data based on the model that you defined. 
  ![[Screenshot 2026-02-06 at 17.27.16.png]]


In fact, you already experimented with both the ETL and ELT approaches in previous labs. Back in Course 1, you implemented an ETL pipeline with **AWS Glue ETL** as the external transformation tool to transform the data before loading it into S3. 

![[Screenshot 2026-02-06 at 17.30.21.png]]

>	AWS Glue ETL is based on a distributed processing framework called **"Spark"**. You can use it to perform more complex transformations on larger datasets. 

Then in the first week of this course, you implemented an ELT pipeline using **DBT** to transform the data within a database itself. 

![[Screenshot 2026-02-06 at 17.31.38.png]]

>	Note that DBT is not an execution tool like Spark, meaning it doesn't come with compute resources, but it is instead a **SQL tool** that you can use to facilitate the transformation task within a database or data warehouse by relying on the computing resources of the storage system. 

#### Data Wrangling
In addition to transforming your data into a target schema, you might need to apply transformations to ***clean and normalize your data***. For example, the source data you extracted might have missing values, duplicate entries, outliers, or other inconsistencies. 

This process of taking messy malformed data and turning it into clean data is called **"data wrangling"**. 

![[Screenshot 2026-02-06 at 18.10.33.png]]

You could write your own code to perform data wrangling like you did last week to turn raw data into a useful form for training a machine learning algorithm, but I highly recommend to use a ***data wrangling tool*** to avoid *undifferentiated heavy lifting*. There are many third-party data wrangling tools available to you, and many major cloud providers typically offer their own version of these tools. For example, AWS offers **AWS Glue DataBrew** as a visual data preparation service for cleaning, standardizing, and transforming data. 


### Syncing Source <-> Extracted data
After you store your transformed data in the data pipeline, you might need to *periodically* or *continuously* **update** the data to make sure that it's **in sync with this data in the source system** 

#### Truncate and Reload
You can apply a simple approach known as **"truncate and reload"**, where you *delete all records from your table and then reload the data from the data source*, rewriting any transformations needed to get the data into your target system. This approach works well when you have a small dataset and only need to update the data in your target system once in a while. 

![[Screenshot 2026-02-06 at 18.13.15.png]]
![[Screenshot 2026-02-06 at 18.11.24.png]]
![[Screenshot 2026-02-06 at 18.11.40.png]]



#### CDC 

However, if your dataset is large, this approach can become **very resource-intensive**. In this case, you might want to adopt a **CDC** or **"change data capture"** approach, where: 
- you first ***identify the changes*** made in the source system 
- and then ***update the tables*** in your target system based only on those changes. 
	- For example, you can check the **"Last Updated"** column in a relational source database, 
	- or you can check the databases **"transactional logs"**. 

##### Handling Updated Rows
Each row in the log can be labeled with an `I`, if the row is inserted, a `U`, if the row is updated, or a `D`, if the row is deleted. ![[Screenshot 2026-02-06 at 18.13.51.png]]
- When handling updated rows, you can apply an **insert-only pattern** or **upsert/merge** pattern to update your target system. 
	- With an ***insert-only pattern***, you insert new records *without changing or deleting old records*, and you add additional information to the new record to distinguish it from the old one.
	  ![[Screenshot 2026-02-06 at 18.14.33.png]] 
	- With an ***upsert/merge pattern***, you take a set of source records and look for matches against your target table by using a primary key or another logical condition. 
		- When a match occurs, you update the target record by replacing it with a new record. 
		- When no match exists, you insert the new record. 
	  ![[Screenshot 2026-02-06 at 18.15.35.png]]


##### Handling Deleted Rows
When handling deleted rows, you can adopt a **"hard" or "soft" delete pattern**. 
- With a hard delete, you permanently remove a record from your target system, ![[Screenshot 2026-02-06 at 18.20.04.png]]
- whereas with a soft delete, you mark the record as deleted. ![[Screenshot 2026-02-06 at 18.20.54.png]]
	- You might use hard deletes to remove data for performance reasons, say a table is too big, or if there's a legal or compliance reason to do so. 
	- You can use soft deletes when you don't want to permanently delete a record, but you also want to filter it out-of-query results. 
- You can also delete a record in an insert-only manner when you insert a new record with a ***deleted flag*** without modifying the previous version of the record. ![[Screenshot 2026-02-06 at 18.21.25.png]]

*Single row inserts* are commonly performed on **row-oriented databases**. 

However, a problem I see a lot is that some data engineers try to perform single row inserts into an OLAP column oriented database.
![[Screenshot 2026-02-06 at 18.21.58.png]]

This is an anti-pattern that could put massive load on the OLAP system. It also causes the data to be written in many separate files, which is extremely inefficient for subsequent reads. Instead, I recommend loading data in a **periodic micro-batch** or **batch** fashion. When you insert data in bulk, the data can be organized more efficiently into row groups and better compressed. If the OLAP system is distributed, you can leverage the distributed parallel processing capability instead of loading records one by one. 
![[Screenshot 2026-02-06 at 18.22.30.png]]

Those are some of the common batch transformation patterns. 

Like I mentioned earlier, if you're only working with simple transformations and small datasets, you might be able to get away with performing the transformations on a single machine. 

However, as the transformations you need become more complex, and the datasets become larger, you'll need to consider using **distributed processing frameworks** to meet the scalability and performance requirements. While Cloud data warehouses leverage distributed processing power, you might also need to apply transformations outside the data warehouse or inside a data lake. Join me in the next section to begin our discussion on distributed processing frameworks.


## Distributed Processing Frameworks - Hadoop

Over the years, engineers have developed many ***big data tools*** to handle the growing amounts of data. Let's take a moment to understand the evolution of key tools and frameworks that have led to today's data ecosystem. 


### Apache Hadoop
In this section, we'll be focusing on **"Apache Hadoop"**, one of the earliest frameworks for dealing with large datasets, and it's still surprisingly relevant today. 

![[Screenshot 2026-02-06 at 18.57.12.png]]

- With the **explosion of data** in the early 2000s, traditional **monolithic databases** and data warehouses of the 1990s were not able to handle the massive amounts of data in a cost effective, scalable, available, and reliable way. 

- At the same time, commodity hardware such as servers, ram, disks, and flash drives also became cheap and ubiquitous. 

- During this time several *innovations led to the large scale distributed computing* and storage on massive computing clusters that you see today. 

- In 2003, Google published a paper on the **[Google file system](https://www.youtube.com/watch?v=LXhgFAZroG8
- ), or GFS**, which provided a fault tolerant and distributed file system across many clusters of commodity hardware servers. 

Shortly after that, in 2004, Google published a paper on **"MapReduce"**, a new parallel programming paradigm for large scale processing of data distributed over GFS. Google's publications constituted a big bang for data technologies and the cultural roots of data engineering. 

The Google papers inspired engineers at ***Yahoo*** to develop the open source framework Apache Hadoop in 2006. 

Google's GFS paper provided a blueprint for the Hadoop distributed file system, or HDFS, and MapReduce became part of the framework. 

>	*Although Hadoop is not considered a bleeding edge technology today. I still think it's important for you to understand the concepts behind Hadoop, because MapReduce still influences many distributed systems that data engineers use today and HDFS is still a key ingredient of many current big data engines such as Amazon, EMR, and Spark.* 

The Hadoop distributed file system is similar to object storage, but with a key difference: 
- **Hadoop** combines *compute* and *storage* on the **same nodes**, whereas 
- **Object Storage** typically has *limited compute* *support* for internal processing. 

Hadoop breaks large files into blocks, each block holding a chunk of data less than a few hundred megabytes in size. 

![[Screenshot 2026-02-06 at 21.40.52.png]]

The file system *is managed* by what's called the **"nameNode"**, which maintains directories, file metadata, and a detailed catalog describing the location of the file blocks in the cluster. 

![[Screenshot 2026-02-06 at 21.41.36.png]]

In a typical configuration, you would **replicate** each block of data to **three nodes**, called ***"data nodes"***. This increases both the durability and availability of data. 

![[Screenshot 2026-02-06 at 21.42.08.png]]

If a disk or node fails, and the replication factor for some file blocks fall below three, the name node will instruct other data nodes to replicate these file blocks so that they again reach the correct replication factor. 

![[Screenshot 2026-02-06 at 21.43.07.png]]

By combining compute resources with storage nodes, Hadoop allows **"in-place data processing"**. 

### MapReduce

**In-place data processing** was originally achieved using the [***MapReduce***](https://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf) programming model.

![[Screenshot 2026-02-06 at 21.43.23.png]]

In the MapReduce programming model: 
- you send **computation code** to the **nodes that contain the data** favoring locality, rather than bring your data to your application. 
- The computation code consists of a collection of **map tasks** that read individual data blocks and produce a set of **key value** pairs. 
- These map tasks are **followed by a *shuffle*** that *redistributes results across the cluster* so that **values with the same key are collected together in the same node**. 
- And finally a **reduce** step that **aggregates** data on each node. 

![[Screenshot 2026-02-06 at 21.57.15.png]]


Example:
Suppose that you wanted to run the SQL
```MySQL
SELECT used_id, COUNT(*)
FROM user_events
GROUP BY user_id;
```

So the result will be all the user ids along with the number of records associated with that user_id. 

With **HDFS**, **the data** in the `user_events` table is *broken down into data blocks* and spread across many nodes (by Hadoop's design). 

##### MAP step

Let's zoom in on one data node that contains these three data blocks. 
![[Screenshot 2026-02-06 at 22.00.35.png]]

The MapReduce job generates ***one map task per block***. 
Each map task essentially: 
- runs a query on the respective block 
- and generates key-value pairs, where the key represents a `user_id` that appears in the block and the value is the corresponding COUNT of records associated with that user_id within that block. 

![[Screenshot 2026-02-06 at 22.02.13.png]]

So, for example, in this first block there are **six records** associated with `user2` and ten records associated with `user1`, and so on. 

While the wholte `user_events` table might contain petabytes of data, each block might only contain hundreds of megabytes. So it's much faster to run the query on **a single block** than it is on the full table. 

##### SHUFFLE step
In the "SHUFFLE" step, you redistribute the results **by key**, which is the `user_id` in this example, so that each key ends up on one and only one node. So here all the `user1` key-value pairs end up in one data node and the `user2` key-value pairs end up in another data node. This is the shuffle step, which is often executed using a hashing algorithm on keys. 
![[Screenshot 2026-02-06 at 22.05.38.png]]

##### REDUCE step
Once the map results have been shuffled, you sum the results for each key. The key, along with its computed total count, can be written to the local disk on the node where they're computed. Then finally you collect the results stored across nodes to view the full query results. 

![[Screenshot 2026-02-06 at 22.08.25.png]]

#### Hadoop shortcomings
This model is extremely powerful, but it has some shortcomings. It utilizes numerous short-lived MapReduce tasks that read data from disk and write intermediate computations to disk. 

![[Screenshot 2026-02-06 at 22.11.29.png]]

In particular, no intermediate state is preserved in memory, and all data is transferred between tasks by storing it on disk or pushing it over the network. This simplifies state and workflow management and minimizes memory consumption, but it can also drive **high disk bandwidth utilization and increase processing time.** 

![[Screenshot 2026-02-06 at 22.12.19.png]]

So, engineers developed other data processing frameworks that still include some elements of map, shuffle and reduce but **relax the constraints of Mapreduce** to allow for in-memory caching. 

![[Screenshot 2026-02-06 at 22.13.08.png]]

And since RAM is much faster than SSD and HDD's in transfer speed and seek time, persisting even a tiny amount of chosen data in memory can dramatically speed up specific data processing tasks. 

For example, *Spark*, which we'll discuss in the next section, was designed around in-memory processing. With Spark: 
- you treat data **as a distributed set that resides in memory,** 
- and treat disk as a **second class data storage layer for processing** which you only use if your data overflows from the available memory. 

Nowadays, Hadoop is no longer a hot bleeding edge technology, and MapReduce is not widely used by data engineers. And I think that advancements in leveraging memory for data transformations will continue to yield gains for the foreseeable future. 


## Distributed Processing Framework - Spark

[TBC]

