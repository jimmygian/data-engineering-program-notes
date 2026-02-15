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

So, engineers developed other data processing frameworks that still include some elements of map, shuffle, and reduce but **relax the constraints of Mapreduce** to allow for in-memory caching. 

![[Screenshot 2026-02-06 at 22.13.08.png]]

And since RAM is much faster than SSD and HDD's in transfer speed and seek time, persisting even a tiny amount of chosen data in memory can dramatically speed up specific data processing tasks. 

For example, *Spark*, which we'll discuss in the next section, was designed around in-memory processing. With Spark: 
- you treat data **as a distributed set that resides in memory,** 
- and treat disk as a **second class data storage layer for processing** which you only use if your data overflows from the available memory. 

Nowadays, Hadoop is no longer a hot bleeding edge technology, and MapReduce is not widely used by data engineers. And I think that advancements in leveraging memory for data transformations will continue to yield gains for the foreseeable future. 


## Distributed Processing Framework - Spark

To address the shortcomings of Hadoop MapReduce, researchers at UC Berkeley started the **Spark** project back in 2009. The goal was to come up with a **distributed framework** that borrows ideas from **MapReduce**, but is simpler and faster, supporting in-memory storage for intermediate results and interactive processing of the data. 

**"Spark"** has since evolved to support 
- **streaming processing**  
- as well as **machine learning and graphing libraries**. 

![[Screenshot 2026-02-07 at 18.09.32.png]]

Unlike Hadoop, which integrates *compute and persistent storage* via the Hadoop distributed file system, 

>	Spark is **just a computing engine** designed for processing distributed large datasets. 

With Spark, you can perform **parallel computations** and retain intermediate results in memory, which limits disk IO interactions, enabling significantly faster computation than Hadoop MapReduce. 

You can use Spark: 
- locally 
- in data centers 
- or in the cloud. 

You can *load data from* and *store the final results on* separate persistent storage systems such as relational or key-value databases, object storage, or even Hadoop file system. 

Spark provides a **unified platform** that allows you to run different types of analytical workloads as self-contained Spark applications on one processing engine. 

For example, you can use Spark to perform SQL queries to load data, train and test machine learning algorithms, and apply streaming transformations on your data over the same computation engine. 

You can write your workloads in Python, Java, Scala, and R using Spark core APIs. 

![[Screenshot 2026-02-07 at 18.16.27.png]]

Spark also offers built-in libraries such as
- **Spark SQL** for writing SQL queries, 
- **ML lib** for machine learning applications, 
- **Spark structured streaming** for interacting with real time data, 
- and **graphics** for graph processing. 

![[Screenshot 2026-02-07 at 18.16.39.png]]

Beyond these standard libraries, you can also use external third party libraries published and maintained by the open source communities. These include ***connectors*** that allow you to connect to a variety of external **data sources and storage systems**, monitor performance, and much more. 

### Spark Application
Let's zoom in on the **underlying components** of a Spark Application and see how they work together to process your code. 

A spark application consists of a **cluster of nodes**. It has 
- a **"driver node"**, which is the central controller (brain) of a Spark application. 
- A **"cluster manager node"**, which communicates with the driver to allocate computational and memory resources across a cluster and manage these resources. 
- And a set of **"worker nodes"** where each node contains a ***spark executor*** that executes tasks assigned to them by the driver. 

![[Screenshot 2026-02-07 at 18.18.14.png]]

Spark applies a **partitioning scheme** to *break up the data into partitions* when loading it from disk, and allocates to each spark executor the partitions that are closest to it in the network. So ***each executor's cpu core gets a partition of data to work on***. 
![[Screenshot 2026-02-07 at 18.20.09.png]]

When you write a spark application, you start by **instantiating a spark session object** that represents a single, unified entry point to all Spark's functionality. 

![[Screenshot 2026-02-07 at 18.21.52.png]]

Through this entry, you can: 
- define dataframes, 
- read data from sources, 
- and perform SQL queries. 

The driver node translates the instructions you wrote, which could be in python, scala, or another language, into Spark jobs, which will be executed one by one based on the job's priority. 

![[Screenshot 2026-02-07 at 18.22.44.png]]

To do this, the driver 
- **transforms each job into a sequence of stages** and 
- **represents these stages as a DAG**. 
For example, this job here has three stages and is represented with this DAG. So each DAG is sort of like the **"execution plan"** for the corresponding job. 

![[Screenshot 2026-02-07 at 18.23.23.png]]

Each stage is further broken down into ***tasks*** written in smart code that can run in parallel. Here, stage 1 has four tasks that run in parallel, and stages 2 and 3 each have three tasks that can also run in parallel.
![[Screenshot 2026-02-07 at 18.25.31.png]]

You'll run stages that have shared dependencies serially and run those without dependencies in parallel. 

For example, in this stack, stages 2 and 3 depend on the results of stage 1, so they can't start until stage 1 is done. But stages 2 and 3 don't have any shared dependencies and can be run in parallel. So to execute this job, Spark will start with stage 1 and run all four tasks in parallel. Once these tasks are done, stage two and three will both start, and the three tasks in each stage will run in parallel. 


Returning to our spark application **once the DAG execution plan is developed**, the **driver communicates with the cluster manager** to request computational and memory resources for the executors. 

![[Screenshot 2026-02-07 at 18.28.05.png]]

![[Screenshot 2026-02-07 at 18.28.49.png]]


Each task is assigned to a single core within an executor, and each executor works on a single data partition. ![[Screenshot 2026-02-07 at 18.29.07.png]]


The executor executes the task and communicates the computation results back to the driver node. 
![[Screenshot 2026-02-07 at 18.29.32.png]]


Finally, the driver node aggregates these computations and returns the results back to you. ![[Screenshot 2026-02-07 at 18.30.08.png]]


When interacting with Spark, you don't need to worry about all these underlying details. So whether you write your application using your favorite programming language or using any of the standard libraries, behind the scenes, your code will be decomposed into tasks and assigned across the Spark executors. 

There's a lot we can cover with Spark, but I want to focus on **PySpark**, which is the Python API for Apache Spark. 

Pyspark supports all Spark features, including Spark SQL, Spark dataframes, machine learning, and structured streaming. In the lab, you'll work with structured data using Spark dataframes and Spark SQL. 

So in the next section I'll show you how to create and work with Spark dataframes after that, we'll go over Spark SQL


## Spark DataFrames

Last week, you performed simple transformations on a small tabular customer churn dataset using Pandas DataFrames. With Spark DataFrames, you can work with **much larger tabular datasets** that are distributed across Spark executors behind the scenes. But Spark abstracts these details for you, so you can view and interact with your data **as if it were a single table.** 

Spark DataFrames are actually built on top of a ***low-level data structure*** called **Resilient Distributed Dataset, or RDD,** which represents the actual partition collection of records that can be operated on in parallel. 

To work directly with RDDs, you would need to manually define and optimize all the operations you want to perform on your data. But with Spark DataFrames, you can interact with the data using simpler and more expressive ***high-level operations***, such as filtering, selecting, counting, aggregating, and grouping, and Spark will compile these operations down to the RDD level behind the scenes. 

Spark DataFrames and the underlying RDD are both considered to be **immutable data structures**, which is what makes them resilient, in other words, fault-tolerant. 

You can classify the operations on distributed data into two types, 
- transformations
	- Transformations, such as filtering, selecting, joining, and grouping, create new data frames from existing ones without modifying the original data. This is why data frames and their underlying RDDs are considered immutable. 
- actions. 
	- Actions, such as count, show, and save, trigger the execution of these transformations. 


All Spark transformations are evaluated lazily, meaning that they are not executed immediately. Instead, they're recorded as a lineage and only executed when an action is invoked. This lazy evaluation allows Spark to **optimize the execution plan** by rearranging transformation operations for efficiency. Moreover, the lineage and immutability properties ensure fault tolerance, because these properties allow you to reproduce an original state in the event of failures. In the next section, I'll give you a quick demo of PySpark DataFrames and show you some of the common data frame operations you can perform when working with data in Apache Spark. I'll see you there.

![[Screenshot 2026-02-15 at 17.23.27.png]]


## Working with Spark DataFrames using Python

Let's take a look at some basic operations you can use to 
- create spark dataframes, 
- manipulate existing dataframes, 
- clean data, 
- aggregate data, and 
- define your own functions for working with dataframes. 


For this demo, I'll use this `pip install` command to install `Pyspark` locally. 

```SHELL
!pip install pyspark
```

In the lab you'll be provided with a Spark cluster running inside a docker container. And I've included links in the additional resource section at the end of this week to show you how you can download the full version of Spark. 

Next, let's install another library called `findspark`, which will automatically add `pyspark` to the system's path during runtime so that your system knows where to find Spark. 

```shell
!pip install findspark
```

I'll import both the Pyspark and Findspark packages, and then I'll initialize findspark. 

```python
import pyspark
import findspark
findspark.init()
```

Before you can create a Spark dataframe, you need to create a `SpartSession`, which works as the entry point for working with Spark dataframes. 

```Python
from pyspark.sql import SparkSession
```

Then I'll create a spark session with the name `example` by calling this `getOrCrete()`method. 

```Python
spark = SparkSession.builder.appName('example').getOrCreate()
```
>	This method will get an existing spark session with the name example if it exists, or it will create a new one. 

If we run "spark":
```ipynb
spark
```

We will see the details of the session. 
![[Screenshot 2026-02-15 at 17.30.22.png]]


Let's say you want to manually create a `dataframe` that holds the data from this order details table. I'll create a dataframe called `orders_df` by specifying the spark session object that I created from before and then calling the `CreateDataFrame` method on it. 

This method expects two arguments. 
- The first argument is the `data`, which I'll provide as a list of tuples, each consisting of the data from one row. 
- The second argument is the `schema`, so I'll specify the name and the type of each column. 
- You can use the `show` method to view the rows of this dataframe. 

```Python
data = [
	(100, 1, 1, 50.1, 1, "Thingam", 5, "Joe Reis"),
	...
]
schema = "OrderID long, ItemNumber integer, SKU integer, Price double, Quantity integer"

orders_df = spark.createDataFrame(data=data, schema=schema)
```


Let's say instead you want to create a dataframe by reading data from a CSV file that contains transaction data of an online retail store. 

Let's create another dataframe called `transactions_df` that uses the same spark session object as before. But this time it calls the `read.csv` method, specifying the name of the CSV file and indicating that the data contains a header. 

```Python
transactions_df = spark.read.csv("path/to/file.csv", header=True)
```

You can view the first five rows of this data by calling the show method with n=5. 

```Python
transactions_df.show(n=5)
```


Now let's say you only care about some of the columns in this data frame. You can first use the columns command to view all the column names, and then select only the columns you want by using the select method. 

```Python
transactions_df.columns
```

![[Screenshot 2026-02-15 at 17.37.02.png]]

So here I'll select only the `price`, `quantity` and `country` columns, and then I'll use the `show()` method to view the first five rows of these three columns.

```Python
transactions_df.select('Price', 'Quantity', 'Country').describe().show()
```

You can use the describe method to compute basic summary statistics for each column, including the value, count, mean, standard deviation, min, and max of each column like you see here. ![[Screenshot 2026-02-15 at 17.38.35.png]]


Next, let's take a look at how you can manipulate data frames such as 
- adding, 
- updating, 
- renaming, or 
- removing 
a column from an existing data frame. 

##### Create
Say you want to ***"create"*** a new column that represents the **amount paid for each product:** 
- You can call the `withColumn` method, 
- then specify the *name* of this new column which I'll call `amount`, 
- and the values for this new column which I'll get by multiplying the price and quantity columns from the existing dataframe. 

```Python
transactions_df.withColumn(
	colName='Amount',
	col= transactions_df.Price*transactions_df.Quantity
)
```

Spark checks if the amount column already exists in the dataframe, and if it does, then it will ***replace*** this column with the new values. And if this column does not exist, then it will ***create*** a new column with these values. 


Remember that spark dataframes are *"**immutable**"*, so this method actually returns **a new dataframe**, but I can assign this new dataframe back to the same `transactions_df `variable. 

You can verify that this new column was indeed added by calling the `show()` method. 

##### Rename
Next, let's ***"rename"*** the `invoice` column to `id`. For that we'll call the `withColumnRenamed` method and specify that the name of the existing column is `invoice` and that the new column name should be `id`. 

```Python
# rename a column
transactions_df = transactions_df.withColumnRenamed(existing='Invoice', new = 'ID')
```


##### Remove
And finally, let's ***"remove"*** the description column. I'll call the drop method and specify the name of the column that I want to remove. 

```Python
transactions_df = transactions_df.drop('Description')
```


##### Cleaning
In terms of ***"cleaning"*** your data in a dataframe, you can easily remove the rows that contain null values by calling the `dropna()` method. 

You can also call the `filter` method to remove rows based on Boolean conditions. 

```Python
transactions_df = transactions_df.filter(transactions_df.Quantity>0)
```


### Aggregation
Next, lets take a look at some aggregation operations on dataframes. 


For example, suppose you want to find the total amount spent on each order. First ill use the `groupby` method to group the rows via the order `id`. 

Next, I'll `sum` up the amount column to get the total amount for each order `id`, I'll call the `show()` method to view the results. 

```Python
transactions_df.groupby("ID").sum("Amount").show()
```


As another example, let's count the ***total number of rows for each country*** and order the count in descending order. Again, I'll call the `groupby` method to group the rows by country, and then I'll call the `count` method to sort the results. 

```Python
#total number of rows for each country in desc order
transactions_df.groupby("Country").count().orderBy('count', ascending=False).show()
```

I'll call the order by method to order by the count and specify false for sending so that the results are sorted in descending order. 


### UDFs
While spark provides lots of built in functionalities that you can find in the documentation, it also supports ***user-defined functions***, or UDF. 

For example, let's say you want to convert all the country names to uppercase. You can define a function called `toUpper` that takes in a string and returns a string in uppercase. 

```Python
from pyspark.sql.functions import udf

def toUpper(word: str):
	return word.upper()

udf_to_upper = udf(toUpper, returnType='String')
transactions_df.select('ID', udf_to_upper('Country')).show(n=5)


```

![[Screenshot 2026-02-15 at 17.54.18.png]]

Alternatively , you can use the `@udf` decorator:

```Python
@udf('string')
def toUpper(word: str):
	return word.upper()

transactions_df.select('ID', toUpper('Country')).show(n=5)
```


Again, remember that spark dataframes are immutable. So with the UDF you actually created a new dataframe that has a country column in uppercase. 

Now that you've seen how Python UDFs work, you should be careful when working with these functions. This is because **Spark is native in Scala**, and each executor in the worker node is a Java virtual machine, or JVM process that hosts a partition of data. When you use Python UDFs, **Spark starts a separate python process** inside the worker node and requires data transfer from the JVM. For the data to be processed by Python Spark serializes it to a format that Python can understand. The Python process then executes the function row by row on that data, and finally returns a result of the row operations to the JVM. 

![[Screenshot 2026-02-15 at 17.57.36.png]]

This process is not efficient since the JVM and Python processes will compete for memory resources and is expensive to serialize and deserialize the data. 

For better performance, you should consider rewriting Python UDFs in Scala or Java because these functions will run directly within the JVM. And the good news is that after you register these UDFs in Spark, you can still use them in Python. 

So that was a quick overview of the basic operations you can do with Spark data frames. 

Join me in the next saection to see how you can issue SQL queries with Spark SQL.

## Spark SQL

In the previous section you saw how you can use **spark dataframes** to explore and process your data **using Python code**. In this section, you'll learn how you can also write **SQL code** to interact the same data. 

When working with `PySpark`, you can choose to manipulate your data, using SQL code, Python code or a mix of both. Both types of code will run *on the same computation engine* and will compile to the same low level code. 

Here we have the same Jupyter notebook that you saw in the previous video, and here's the transactions data frame we ended up with. 

#### Temporary view
To issue **SQL queries** to interact with this data, you need to create a **temporary view** from this dataframe. 
- A temporary view is a **virtual table** that doesn't actually hold the data. 
- It persists as long as the spark session is running 
- and it provides an interface for you to work with the table data using SQL code. 

Here, I'll call the `createOrReplaceTempView` method on the transaction data frame to create a temporary view called orders. 

```Python
transactions_df.createOrReplaceTempView('orders')
```

Now you can issue SQL queries on this temporary orders table. 

Using the `SparkSession` object, you can call the `sql` method and specify the SQL query as a ***string*** inside the brackets. 

```python
sql_result_df = spark.sql("........")
```

This method will return a data frame representing the results of the query. 


For example, let's say you want to find the total amount spent on each order.

```Python
total_df = spark.sql("""
SELECT ID, SUM(amount) AS total
FROM orders
GROUP BY ID
ORDER BY total DESC
""")
```



You can also define your own function and use it inside the SQL query. When working with data frames, you saw that you need to either *wrap* your function inside a UDF object or use the UDF decorator. With SQL queries, you need to **register your function**. As an example, let's write a function called `toLower` that transforms a word into lowercase letters. 

```Python
def toLower(word: str):
	return word.lower()
```

```Python
_ = spark.udf.register("udf_to_lower", toLower)
```

```Python
spark.sql("""
SELECT DISTINCT udf_to_lower(country)
FROM orders
""").show()
```

You can also create more than one view, meaning more than one virtual table, and join them in a SQL query. For example, let's first create another data frame called `product_category_df` that contains the code and the category of three products. Then using this data frame, I'll create another temporary view called `items`. 

```Python
product_category_df = spark.createDataFrame(
	data = [(22423, 'category_a'), (21212, 'category_b'),
			(21232, 'category_c'), (84879, 'category_a')],
	schema = 'itemID string, category string'
)
```

```Python
product_category_df.createTempView('items')
```

Then in the SQL query, I'll join the `orders` table from before with this new items table to find the average amount for each category. 

```Python
spark.sql("""
SELECT category, AVG(amount)
FROM items
LEFT JOIN orders
ON items.iemID = orders.stockcode
GROUP BY category
""")
```


## Amazon EMR

Back in Course 1, you were introduced to Amazon EMR as a big data tool that supports a wide range of processing frameworks. In the upcoming lab, you'll use EMR to run Spark jobs in an Amazon EMR Studio notebook. 

In this saection, I want to help you understand a bit more about what's going on behind the scenes when you run Spark jobs on Amazon EMR, as well as some of the other features of EMR. So let's get into it. 

If you remember our earlier lesson about Amazon Redshift, you learned how it uses ***massively parallel processing*** to tackle big data analytics. EMR works in a similar way, in that there is a **cluster** with multiple nodes and each node does a portion of the work. 
![[Screenshot 2026-02-15 at 18.40.44.png]]

When you submit a job using EMR, it gets run across these nodes working in parallel, and each node processes a part of the data. Because of this parallelization, the job is completed much quicker than what could be achieved by a single machine. 

The **size of your cluster** can impact how quickly your jobs run, and with EMR, a cluster is elastic, meaning that it can scale up or down as needed. ![[Screenshot 2026-02-15 at 18.41.24.png]]

Once your job completes, the results are stored in your **desired destination**. This could be in 
- Amazon S3, 
- HDFS, 
- or another data store option. 

You can then *analyze the results* or *feed them into another application or workflow* for further processing. 

![[Screenshot 2026-02-15 at 18.42.42.png]]

EMR also supports numerous popular Big Data frameworks, including Apache Spark, Hadoop, Hive, Presto, Flink, HBase, and many more tools and frameworks that enable data analysis tasks. 

![[Screenshot 2026-02-15 at 18.43.08.png]]

- mEMR provides a **managed environment** that simplifies the setup and scaling of these frameworks and integrates natively with other AWS services. 
- With EMR, you can focus more on your data workflows rather than the underlying infrastructure. 

For example, if you want to analyze data stored in S3 with Hadoop, you can do that with the integration between S3 and the Amazon EMR file system. This allows you to 
- decouple compute and storage 
- and analyze large amounts of data that may not be able to fit on local storage. 
The way this works is that when you launch your cluster, EMR ***streams the data from S3 to each instance*** in your cluster and begins processing it. 


Another advantage of storing your data in S3 is you can use **multiple EMR clusters** to process the same dataset in different ways.![[Screenshot 2026-02-15 at 18.46.50.png]]
![[Screenshot 2026-02-15 at 18.45.27.png]]

EMR can also integrate with other AWS data sources like 
- Amazon DynamoDB, 
- Amazon RDS, and 
- Amazon Redshift as a few examples. 

In the up coming lab, you'll use ***Amazon EMR Studio***, which is a browser-based IDE for Jupyter Notebooks that runs on EMR clusters. 


## AWS Glue Overview

 In each of the labs so far, there have been some aspect of the data infrastructure that you have worked on directly or set up yourself and other aspects that we have provided for you. 
 ![[Screenshot 2026-02-15 at 18.54.11.png]]
 
 When it comes to batch ETL process specifically, in many cases, you have used **AWS Glue**, but for most of the part, the details have been abstracted from you. 

Here, I would like to pull back the curtain, so to speak, and show you the details, what's going ***under the hood*** and how to set up those jobs you have been running to ingest and transform data. 

Behind the scene, AWS Glue leverages **Apache Spark** to process the data. Now you have learned about Apache Spark.

![[Screenshot 2026-02-15 at 18.54.47.png]] 

I would like to show you how you can create `PySpark` script to run your ETL processes using Apache Spark on AWS Glue. But before we get into the details, let's learn a bit more about AWS Glue as a service. 

As you know, when you're setting up the data pipeline for analytics machine learning, or really whatever end use case, you will often be **ingesting data from multiple data sources**: 
- light databases, 
- object stores such as Amazon S3, 
- logs, 
- APIs, 
- data from streaming platform
- or others. 

First and foremost:

***Definition**: AWS Glue is a **data integration service** that helps you bring all this data together from **different** data sources, perform the transformation you need on the data, and then load the data into some downstream place of your system, which could be a database, a data lake, a data warehouse, or other destination.* 

When you create an ETL pipeline to accomplish all these tasks using AWS Glue, we call that **"Glue job"**. 

![[Screenshot 2026-02-15 at 18.58.20.png]]


### AWS Glue Job Options

There are three options when it comes to creating and running Glue job. 

#### 1. AWS Glue Data Brew
The first option is to use ***AWS Glue DataBrew***, which is no-code or low-code informant. With AWS DataBrew, you can visually see how the data looks as you perform all transformation and data manipulation. 

It's kind of like working on an Excel spreadsheet, but with the power of Spark. With **DataBrew**, you don't need to be proficient in Spark or do any coding at all to get the job done. 

![[Screenshot 2026-02-15 at 19.00.27.png]]


#### 2. Glue Studio
The next option is authoring Glue job in a ***Glue Studio***. This is slightly for more advanced ETL user who have a fair understanding of building ETL pipeline and are ready to write some Spark codes. 

![[Screenshot 2026-02-15 at 19.01.20.png]]

In the Glue Studio user interface, you can 
- drag and drop different data sources, 
- transformations, and 
- target destinations. 

#### 3. Jupyter Notebook
Your third option is to create Glue job in a ***Jupyter notebook***, where 
- you will need to write your own Spark code from scratch, and then run it using AWS Glue, 
- or you can use help from ***Amazon Q Developer*** or ***Amazon Q chatbot*** to get started. 

You can then perform ETL operation using ***Glue ETL*** and extract your ETL pipeline using ***Glue triggers, blueprints, and workflow.*** 

![[Screenshot 2026-02-15 at 19.03.13.png]]

### AWS Glue Data Catalog
Another key feature you have seen in a lab in the previous courses is **AWS Glue centralized data catalog and data governance**, which are essential for building data lakes and lake houses. 

In the lab, you have used **AWS Glue Crawler** to *crawl over* the data from different data sources, extracting the metadata, and storing this information in your Glue Data Catalog. 

This allows you to see things like 
- definition, 
- structures, 
- data type, 
- and partition information 
from your data. 

AWS Glue is a ***serverless*** in nature, so you don't have to worry about provisioning and managing any resources. You can easily scale up your ETL jobs. You can start with a few data processing unit or a DPU, which is basic unit that Glue uses to process data and then scale up as needed. 

![[Screenshot 2026-02-15 at 19.05.11.png]]


You have also seen how AWS Glue Data Catalogs support integration with other AWS too. You can integrate AWS Glue Data Catalog with **Amazon Athena** to run SQL query against your data with a **QuickSight** to build BI dashboards and a **SageMaker** to build, train, and deploy your machine learning model, and with many other services. 

![[Screenshot 2026-02-15 at 19.06.48.png]]


In the first lab of this specialization, way back in the first course, you have spun up a data pipeline that looked like this: 
![[Screenshot 2026-02-15 at 19.07.20.png]]
- where you were ingesting normalized data from Amazon RDS database instance, 
- performing some transformation to the model the data into star schema, 
- and then loading the data into Amazon S3 bucket to serve a data analytics and use case. 
- You performed the extract, transform, and load or ETL portion of this pipeline by running *Terraform script* to spin up Glue and run a Glue ETL jobs. 

The Python script called `qlue_jobs.py` was provided to you in the lab. 

In the next section, I walk you through exactly how you can generate the code for that Glue ETL job yourself. I will see you there.


## AWS Glue Visual ETL

>	NOTE: Feel free to skip this section -- see a youtube video if you need to use AWS Glue Visual ETL 

As I said in the previous video, I would like to show you now ***how to use Glue Studio*** to generate the Glue job. 

Here, we will generate the code in that `glue_job.py `script that you have run in the first lab for this program. 

In that lab, your architecture looked like this, and it's this Glue ETL job here that we have been looking at in the details. 

![[Screenshot 2026-02-15 at 19.10.23.png]]

Remember, the purpose of Glue job was 
- to **ingest normalized data from an Amazon RDS instance**, 
- apply **transformation** to model the data into star schema, 
- and then **load** the data into Amazon S3 bucket. 

![[Screenshot 2026-02-15 at 19.11.13.png]]

Let's see how that works. 


First off, recall that schema of the normalized data in the database looked like this, where you have tables from customers, product, productline, orders, orderdetails, payment, employees, and offices:

![[Screenshot 2026-02-15 at 19.11.33.png]]

and you transform the data into ***star schema***, consisting of a central `fact` table containing some measurement related to the orders, and then surrounding dimensions table provided more context on those order, including information about customers, products, and location associated with each order. 

![[Screenshot 2026-02-15 at 19.12.31.png]]

Now let's take a closer look at the `glue_job.py` file that you have used to accomplish this task. 

There are several sections in the script, and I will just quickly walk you through each of these sections, so you are clear on what's going on here. 

To start, of course, this is a PySpark script. We start with some imports. As you can see here, we are importing some packages from awsglue, and then also from pyspark, because, of course, when you run this script, you will be using Spark behind the scene. 

![[Screenshot 2026-02-15 at 19.13.27.png]]

Next, there is the function definitions. This one is called `sparkSqlQuery` to run SQL queries on dataframes. 
![[Screenshot 2026-02-15 at 19.14.36.png]]


After that, you are using `getResolvedOptions` method from the `awsglue` to pass command line argument, such as `["JOB_NAME", "glue_connections", "glue_databases", "target_path"]`. 

![[Screenshot 2026-02-15 at 19.16.01.png]]

Following that, this is the sum context setup, where you've been creating a `SparkContext` and a `glueContext`, as well as initializing a Glue job using the provided `JOB_NAME`. 

![[Screenshot 2026-02-15 at 19.16.30.png]]


Then comes the actual ETL portion. Without getting too much into the details here, with this section, you are setting up the database connection and extracting data from source tables. 
![[Screenshot 2026-02-15 at 19.17.28.png]]

Next, you are applying transformation to the data to build star schema. You can see you are accomplishing that running a series of **SQL statements**. 

![[Screenshot 2026-02-15 at 19.18.23.png]]

Finally, you are loading the transformed data into S3. 
![[Screenshot 2026-02-15 at 19.18.48.png]]

Now, of course, you could sit down and write a script line-by-line, but instead, the way we generated this script for you in the first lab was using ***Glue Studio***.  I'm going to show you how to do that now:

- First, I already have an RDS instance running.
 - I also have an **S3 bucket** here where I want to send the transform data. 
 
 Now I'm going to head over to AWS Gluw, and in the left pane, open up the ***Glue Visual ETL tool*** in a *Glue Studio*, and you can see that I have blank Canvas to start creating my Glue job. 
 ![[Screenshot 2026-02-15 at 19.21.41.png]]

When I click this "+" button on the left, I get a list of choices for the various element I can add to the glue jobs. 

![[Screenshot 2026-02-15 at 19.21.59.png]]

Here you can see there are tabs for sources, transform, and target. You can select from this to add different extract, transform, and load component to your data pipeline, and we're going to do that here. 

Since the first thing we need to do is extract data from RDS, at this source, I'm going to choose MySQL data source node and add it here, and I will name this node `customer_source` to indicate this will represent the ingestion data from customer tables. 

![[Screenshot 2026-02-15 at 19.22.51.png]]

I will set it up as a JDBC connection. 

Here, I will indicate the **table name** that we will be ingesting data from. 
![[Screenshot 2026-02-15 at 19.24.02.png]]

Then I need to indicate an **IAM role** for a Glue job to assume when running this step of the job. You would first need to set up this IAM role, but I have already set up this role called Cloud9-de-c1w2-glue-role, and I will choose that here, and that will give Glue the appropriate permission to read the data from RDS, as well as to eventually write data to S3. ![[Screenshot 2026-02-15 at 19.24.18.png]]

Now I can see a preview of the data that will be extracted here from the customer table. 



The next steps will be to add a transform to create the all star_schema tables. We can do this by selecting the SQL options under "+".


After all tables have been created, we just need to specify the target destination to load the data into. The target destination is going to be **Amazon S3 bucket**, and I already have bucket setup, and I have created this folder just to keep things organized. 

![[Screenshot 2026-02-15 at 19.30.46.png]]
>	I have a processed_data folder, and then inside there, I have one folder for each of the fact and dimension table.

These are all empty right now, as you can see. But when we run the Glue job, we should be able to see this have been populated with the transformed data. 


Now I'm going to come back over here and add a target node, and I will choose Amazon S3, and I'm going to set up a separate target node for each of the fact and dimension tables. 

Then I can click here to browse S3 to identify the dim_customer folder where I want this to go. Then I'm just going to do the same for other's tables. I will add S3 targets and set them to pull from the appropriate parent nodes and deliver to the appropriate folder on S3. Now we have everything for the Glue job defined here in the Visual ETL interface. Then if I click over here on the "Script" tab, you can see that script for this job has now been written. 

To run this Glue job, I can first click over here on the "Job details". This is where you can define number of workers. 

![[Screenshot 2026-02-15 at 19.32.32.png]]

I'm just going to set this to two, and the job timeout, which I will set to three minutes, since this shouldn't take too long. I can verify here that job has her appropriate role assigned. Then I will hit "Save" up here. Next, I can click over the "Run" tab and select Run job. After a couple of minute, I can see that job was completed successfully, and I can head over to S3 and verify that transform table now exist in my S3 bucket, and they are ready to be queried by the analyst. 


### Summary

![[Screenshot 2026-02-15 at 19.34.50.png]]
Just to summarize what we did here. I started with an RDS database instance, an S3 bucket, an IAM role all ready to go. 

I then use Glue Studio Visual ETL tool to define source, transformation, and target for my ETL pipeline. With all these things defined, I could have a look at the script that was generated for the Glue jobs. Then with a few more parameters set, I saved the whole jobs and ran it. Now, as I mentioned, this is just one way to create and run a Glue job. You could have choose the low-code, no-code data proof approach, or you could go more manual Jupyter Notebook approach. That's it. I hope you have enjoyed this little demo and are feeling much more confident now in your ability to set up and run Glue job of your own.


## Technical Considerations

To transform your data using PySpark, you saw that you can issue SQL queries with Spark SQL or operate directly on Spark DataFrames using Python. 

When choosing between using SQL versus Python on Spark DataFrames, you should consider things like 
- the transformation complexity, 
- code reusability and testability, 
- and the skills and technical background of your team. 

When you're working with PySpark, performing simple transformations, such as filtering, grouping, and aggregation, directly on a Spark DataFrame using Python, or coding these transformations as SQL queries, generally results in comparable performance. This is because both Python and SQL approaches are ultimately translated into the same execution plan and executed by the same underlying Spark computing engine. 

However, if the transformation is more *complex*, you might not be able to implement it in SQL or at least not in a straightforward way. 

- For instance, if you want to apply the ***transpose*** operation to swap the rows and columns of a table, you can simply call `df.T` the DataFrame. But transposing is not supported by Spark SQL. 

- Or as another example, you can ***normalize*** columns and clean columns with missing values in SQL, but that might require more code than working with Python on Spark DataFrames. 

- Moreover, working with DataFrames would help you write more testable, maintainable, and modular or reusable code. While reusable libraries are easy to create in Spark and PySpark, the SQL doesn't have a good notion of reusability for more complex query components. 

- You also want to consider the skills and technical background of your team. You might find writing SQL queries to be simpler and easier than working with Python on Spark DataFrames. 

Depending on your transformation use case, you might find one of these approaches more suitable than the other. You can even try to combine both the Spark DataFrames and Spark SQL approaches to realize the best of both worlds. 



Now that we've covered the considerations for using Spark SQL versus using Python on Spark DataFrames, let's take a moment to discuss when you'll want to consider using a distributed framework like Spark to begin with. Like you saw last week, instead of using Spark DataFrames, you could just use Pandas DataFrames to process the data. However, Pandas is not a distributed framework, it'll load the entire data into the memory of the machine on which your Python code is running. If your dataset is not very large, and by that I mean, you can fit your entire data into memory, then you can use Pandas instead of Spark. In fact, using Spark on a small dataset can be overkilled, since you need to manage the cluster of nodes. On the other hand, if your data is so large that it doesn't fit entirely in the memory, or if you want to leverage distributed computations to enhance processing performance, then you should go with Spark and maybe run it on the cluster running in the Cloud. In any case, whether you're working on a single machine or a cluster of nodes, the best practice would be to extract only the data you need from the source. 

The less data you have to process, the less resource heavy and more performant your code will be. You might need to apply transformations, such as joining, grouping, and filtering inside the database before ingesting the data to reduce the size of the data. 

![[Screenshot 2026-02-15 at 20.49.47.png]]

Choosing the right coding approach for batch transformation is about balancing the simplicity of coding with SQL with the flexibility and modularity of coding with non-declarative languages such as Python. 

Choosing the right tool for batch transformation depends on the size of the data you want to transform and the specification of the hardware on which you're running your code. Make sure to understand the trade off between these different approaches. Now that we've talked about batch transformations, join me in the next lesson to learn about streaming transformation and how a streaming processing tool can affect the latency of your system.

