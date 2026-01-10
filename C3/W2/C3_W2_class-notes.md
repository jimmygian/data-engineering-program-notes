
## Overview

Last week we looked at the details of the **raw ingredients of storage**. 

- You saw how, as a data engineer, understanding the characteristics, performance, and cost of the different storage mediums and processes can help you better evaluate tradeoffs in your storage solution decisions. 
- Then we explored **different storage systems**, including object storage and common types of databases. 

This week, we'll move up in the storage hierarchy and explore different storage abstractions. 
![[Screenshot 2026-01-02 at 18.15.09.png]]

We'll start by looking at the **data warehouse architecture** and the transformative shift towards modern **cloud data warehouses**. 
![[Screenshot 2026-01-02 at 18.16.03.png]]

We'll then dive into the **rise of data lakes** as a *response to the growing storage needs* in terms of data volume, data types, and use cases. ![[Screenshot 2026-01-02 at 18.16.19.png]]
- We'll also examine the challenges associated with this architecture. 

And finally, we'll look at the data Lakehouse architecture, which aims to combine the advantages of both data warehouses and data lakes. 
![[Screenshot 2026-01-02 at 18.17.09.png]]

I've set up this week as a sort of journey through the history of data storage abstractions. As we move through each paradigm, you will consider the pros, cons, and possible use cases for each one. 

The goal here is to give you enough of an understanding behind each architecture so you can choose the most appropriate storage solution based on your use case. 


## Key Architectural Ideas

---

In your work as a data engineer, you'll find yourself ingesting data **from many different source systems**. 

One very common source you'll ingest from will be a database that's part of an **OLTP system**, where the data is structured in a way that's optimized for transactional workloads. 

>	OLTP systems have been around for decades. In the early days of data analytics, it wasn't uncommon for teams to be running analytical queries directly on a production OLTP database. 

As I've already mentioned a couple of times, running analytical queries on your production databases can be a **recipe for disaster**. And beyond that, attempting to do analytics directly on row based transactional data can be very inefficient and costly. 

Back in the late 1980s, Bill Inmon came up with the notion of a **"data warehouse"** to solve this problem. 

He described a data warehouse as *"a subject-oriented, integrated, non volatile, and time-variant **collection of data** in support of management's decisions."* 

This foundational definition underscores the role of the data warehouse as a central data repository designed to facilitate *reporting and analysis*. 


Let's break down this definition. 
- **"Subject-oriented"** means that a data warehouse organizes and stores data around *key subjects* or *domains* of the business, such as customers, products, sales, or finance. With a data warehouse, your focus is on modeling the data to support **decision making** rather than on transaction processing and recording. 
- **"Integrated"** means that a data warehouse brings together data *from different sources* and ensures that it's stored in a consistent way with a predefined **schema**. 
- **"Nonvolatile"** means that the data in a data warehouse is *read only* and cannot be deleted or updated in the general sense. 
	- This is useful for historical data analysis, where the principle of non volatility requires a data warehouse to **preserve historical records.** 
	- When initially loading data from source systems into a data warehouse, the data is captured and loaded as a **snapshot**. ![[Screenshot 2026-01-02 at 18.40.51.png]]When subsequent changes and the source systems occur, either a new snapshot of the data is loaded into the data warehouse or just the changes are loaded. ![[Screenshot 2026-01-02 at 18.41.16.png]]![[Screenshot 2026-01-02 at 18.41.32.png]]Either way, the existing snapshot of data is not deleted or changed, it's kept in the data warehouse. 
- This leads to the **"time variant"** characteristic, meaning that a data warehouse stores *current and historical data*. 
	- Data users can look at this historical data to ***observe trends*** across multiple subjects to support their business decisions. 
	- This is something you can't do with the OLTP source systems, which typically don't support preserving historical data or historical data analysis. 

Although technical aspects of the data warehouse have evolved significantly. This original definition still holds true today. 
![[Screenshot 2026-01-02 at 18.42.39.png]]

#### Data Warehouse-Centric Architecture
Traditionally, you'll load data into a data warehouse from *various sources* by using **an ETL pipeline**. 

The typical process here, would be that: 
- you first move the extracted data into a **staging area** outside the data warehouse, which could be an S3 object storage, for example. ![[Screenshot 2026-01-02 at 18.45.10.png]]
- There you would apply transformations to the data to clean it and standardize it. This is also where you structure the data according to a particular model to make the data useful for downstream users. We'll get into data modeling in detail in the next course. ![[Screenshot 2026-01-02 at 18.45.51.png]]
- Next, you load the transform data into the data warehouse. ![[Screenshot 2026-01-02 at 18.47.57.png]]
	- **Data warehouses are designed to serve the *broader* organization**. 
	- **But you can also serve a narrow set of users by loading the data into what are called *data marts*.** You can think of each data mart as a *more refined subset* of the data warehouse that's designed to serve the specific needs of a *single department or business function* like sales, marketing, or finance. 

**Data Marts**
Unlike the comprehensive schema of a data warehouse, the data in a data mart often follows a simpler or de-normalized schema that provides a more focused view of only the subset of data that's specific to a department. 

With data marts, you can also perform an additional stage of transformation beyond that provided by the initial ETL pipeline which can improve performance for analytical queries that require complex joins and aggregations. 

This ETL process is commonly used to keep data in your data warehouse in sync with your production databases as the source databases are continuously updated. 

![[Screenshot 2026-01-02 at 18.49.00.png]]

#### CDC
When you extract data from production databases, **instead of extracting all the data**, you can use a change data capture or **"CDC"** process to identify and capture only change events like insertions, updates or deletions, and deliver those changes to your data warehouse. 
![[Screenshot 2026-01-02 at 18.50.10.png]]
![[Screenshot 2026-01-02 at 18.50.56.png]]

By only extracting incremental changes, you can minimize your impact on the performance of the source systems. 


So data warehouses were a departure from traditional OLTP systems. By extracting data from production databases, modeling to support analytical workloads, and then loading it into a separate data warehouse, you can direct the load away from the production systems and provide a better end user experience with improved analytical query performance. 

This made data warehouses a **standard for online analytical processing or OLAP data architecture**. 


#### Monolithic, MPP, and Cloud Data Warehouses systems

The earliest implementations of data warehouses were based on a single monolithic server that limited their performance. 
![[Screenshot 2026-01-02 at 18.51.40.png]]

As the amount of data grew steadily throughout the 1990s, traditional data warehouses couldn't keep up. 


Then, the emergence of **massively parallel processing or MPP systems** enable data warehouses to scale. Data warehouses that implemented MPP were able to scan large amounts of data *"in parallel"*, achieving high performance analytical queries. But the systems were complex to configure and required effort and time to maintain. ![[Screenshot 2026-01-02 at 18.53.38.png]]

In the early 2010s, modern Cloud data warehouses, like Amazon Redshift, Google Big Query, and Snowflake, emerged and represented a significant evolution from the on premises data warehouse architecture of the past. 

The Modern Cloud Data Warehouse architecture separates compute from storage and expands the capability of MPP systems. This allows for scalable and efficient processing of large datasets and makes data analytics more accessible and cost effective to smaller organizations. ![[Screenshot 2026-01-02 at 18.54.16.png]]

In the next section, we'll take a closer look at the characteristics of Modern Cloud data warehouses.


## Modern Cloud Data Warehouses

As a data engineer, you'll likely work with **cloud data warehouses**. In this section, we'll take a closer look at the factors that give cloud data warehouses greater processing power than traditional *on-premises data warehouses*. Understanding these factors will help you design more efficient data warehouses and better manage scaling, performance ,and costs. 

As mentioned in the previous section, data warehouses typically implement **Massively Parallel Processing**, or **MPP**, which uses **multiple processors** to crunch large amounts of data.![[Screenshot 2026-01-07 at 14.53.35.png]]

With cloud data warehouses, instead of needing to appropriately size an MPP system and spend millions of dollars upfront to set up the system, you can just spin up compute clusters **on-demand**, scaling it up over time as data and analytics demand grows, or delete the clusters when they're no longer needed. ![[Screenshot 2026-01-07 at 14.54.16.png]]


## MPP in Amazon Redshift
Let's take a moment to look at the specific MPP architecture for Amazon Redshift. Other cloud data warehouses, like Google, Big Query and Snowflake use a similar structure but different implementation details. 

In *Redshift*, a collection of computing resources is called a **"cluster"**. ![[Screenshot 2026-01-07 at 14.55.23.png]]

Each cluster is composed of *one or more **"compute nodes"*** that are managed by a **"leader node"**. ![[Screenshot 2026-01-07 at 14.56.04.png]]


These nodes have their own CPU, memory, and disk space. ![[Screenshot 2026-01-07 at 14.56.19.png]]

A compute node is further partitioned into node slices, where each slice contains a share of the node CPU, memory, and disk space. ![[Screenshot 2026-01-07 at 14.57.10.png]]

### Loading Data into Redshift
When you load data into redshift, the **leader node** manages how the data is distributed across the **node slices**. ![[Screenshot 2026-01-07 at 14.58.26.png]]

Then, when a client application sends a query request to the data warehouse, the leader node:
1. **"parses"** a request into a series of steps 
2. and forms an **"execution plan"**. 
3. Then, based on the plan, the leader node **"compiles"** the code and **"distributes"** it to the appropriate compute node slices that contain data that's relevant to the query. 
![[Screenshot 2026-01-07 at 15.00.37.png]]

- The slices work in parallel to complete their workload, ![[Screenshot 2026-01-07 at 15.01.35.png]]
  
- Then, the **"compute node"** sends the intermediate results back to the **leader node**. 
- Finally, the leader node **aggregates the results** and sends a final result back to the client application. ![[Screenshot 2026-01-07 at 15.02.12.png]]


As your workload increases, you can 
- **spin up more compute nodes** or 
- **upgrade the node type to one with higher computing capacity**. 
![[Screenshot 2026-01-07 at 15.03.30.png]]

So in this sense, cloud data warehouses expand the capabilities of MPP systems and allow you to easily scale your data infrastructure to handle even petabytes of data in a single query. 


With the increase in processing power that comes with MPP, cloud data warehouses can also support the **ELT or Extract, Load, and transform ingestion pattern**. After you extract data from source systems, rather than transforming and modeling the data outside the data warehouse, you can ***load the raw, unprocessed data directly into a staging area within the data warehouse***. Then you can leverage the massive computational power of the cloud data warehouse to transform the data. 
![[Screenshot 2026-01-07 at 15.04.48.png]]

This results in faster ingestion so you can quickly provide your downstream stakeholders with data. 


- Another change we've seen with cloud data warehouses is a **shift from row based to columnar architecture**. As discussed last week, column storage, along with data compression, facilitates higher performance of large scale analytical queries. 
- Finally, in many cloud data warehouses, data is stored in object storage, allowing virtually limitless storage. This means that you can separate compute and storage, allowing you to manage and scale these resources independently to optimize for both cost and performance.![[Screenshot 2026-01-07 at 15.06.40.png]]


Now, cloud data warehouses still have all the attributes of traditional data warehouses, namely that the data you store can be highly structured and modeled to enable analytical queries. Combining these attributes with the high processing power that comes with MPP, column storage, and the separation of compute and storage is what makes cloud data warehouses highly efficient at storing and processing data for high volume analytical workloads. 

![[Screenshot 2026-01-07 at 15.07.35.png]]


Now, what if your company wants to store in query *unstructured* data like text, images, audio files, or videos? Modern data applications are no longer limited to analytics and reporting, they will often involve machine learning or exploratory analysis use cases that require direct access to a variety of unstructured data, not just data that's accessed by running SQL queries. That's where the **data lake storage architecture** comes into play. So join me in the next lesson to dive into the data lake paradigm.


## Data Lakes

### Key Architectural Ideas

Imagine you're a data engineer at an e commerce company where you're tasked with bringing together: 
- structured data from a sales order database, 
- semi-structured customer records from a CRM, 
- and customer reviews, sort of text, video, and audio files (unstructured data)

You start with a *data warehouse*, but then you quickly realize that the semi-structured data and unstructured data don't fit into a fixed schema, and the data is coming at you at very large volumes. So instead of imposing tight structural limitations on your data, why not simply route all of your data, structured and unstructured, into a **central repository**? This is exactly the concept of a data lake that emerged in the 2000s up to the early 2010s. 

>	The name data lake itself came a little later, but the idea of a central repository for sorting large volumes of structured and unstructured data was a motivation for this new paradigm. 

Unlike data warehouses, data lakes don't require you to decide on a fixed schema or predefined set of transformations ahead of time. Instead, data lakes follow a ***"schema-on-read" pattern*** where the reader determines the schema when reading the data. 

The first generation of data lakes, which I like to call **"Data Lake 1.0"**, made solid contributions towards this promise by combining *different storage and processing technologies*. For storage, Data Lake 1.0 started with **HDFS** or **Hadoop Distributed File System**, but as the cloud grew in popularity, it became more common to see data lakes built on top of **cloud based object storage**, like **Amazon S3**. This extremely cheap and virtually limitless storage capacity allows you to store massive amounts of data of any size and any type, creating a central source of truth for all the data in an organization. 

In terms of processing, when you need to transform or query the data, you could pick from many different technologies, including **MapReduce, Apache Pig, Spark, Presto, and Hive,** among others. 
![[Screenshot 2026-01-07 at 15.16.45.png]]

### Data Lake Shortcomings
Despite the promise and hype, Data Lake 1.0 had many serious shortcomings. The biggest downside was that data lakes in the 1.0 era commonly became **"data swamps"**, a place for organizations to *dump* their data with no proper data management. Without things like data cataloging and data discovery tools, users would struggle to find the data they needed and had trouble understanding how one piece of data related to another. Even if you could locate the data you needed, there was no guarantee on the data integrity or data quality. That is, you couldn't tell if the data was up to date or accurate. 

On top of that, the original data lake concept was essentially **"write only."** Simple data manipulation language or DML operations you commonly use in SQL, like deleting or updating rows, were painful to implement and generally required users to create an entirely new table. This made it very *difficult for organizations to comply with data regulations* such as **GDPR**, which required them to be able to delete user records when requested to do so. Without schema management and careful data modeling it was also very challenging to process the data that was stored in data lakes. 

The data was not optimized for querying in the same way that structured data in a data warehouse was. For example, common data operations like joins were a huge headache to code as Mapreduce jobs.
![[Screenshot 2026-01-07 at 15.18.02.png]]


But even with all these shortcomings of Data Lake 1.0, many organizations, especially tech companies like Netflix and Facebook, which is now known as Meta, that are heavily data focused, they found significant value in data lakes. These companies had the resources to build successful data practices and create their own custom tools for processing the data. But for many organizations, Data Lake 1.0 was an expensive disappointment. The good news is that in recent years, many tools and practices have emerged to help businesses better organize and query the data stored in a data lake. So join me in the next section to explore the characteristics of next generation data lakes.

### Next Generation Data Lakes

In response to the shortcomings of the original **data lake 1.0** that I described in the previous section, engineers developed approaches to more efficiently manage and find data stored in their data lakes. 

In this section, we'll take a look at some of these approaches, specifically:
- data zones, 
- partitioning, and 
- data catalogs. 


#### Data Zones
To better manage data stored in a data lake, you can **organize it into different zones**, where each zone houses data that has been processed to varying degrees. 

> Data Zones are used to organize data in a data lake, where each zone houses data that has been processed to varying degrees.

Although there are no set rules for the number of zones or the naming of these zones, a common design pattern is to have three zones. 
- **Landing/Raw Zone**: The first zone is usually known as a landing or raw zone. When you load raw data into a data lake, it lands in the zone so that you can have a permanent record of the raw data ingested from source systems. 
- **Cleaned/Transformed Zone**: Then, after you apply transformations, to clean, validate, and standardize the data, as well as remove or mask any PII information, a *copy* of this transform data will be written to a second zone, commonly known as the cleaned or transformed zone. 
- **Curated/Enriched Zone**: Next, you model the data by imposing *business logic* and applying further transformations to it. Then you write the transformed data to a third zone, known as a **curated or enriched zone.** Data in this zone should abide by the organization's standards and is ready for consumption. 

>	You typically store data in the cleaned and curated zones using **open file formats** like **Parquet**, **Avro**, or **ORC** to make storage on disk more efficient. Since these formats are open source, they also allow a wide range of analytics engines and machine learning systems to directly access the data. 

>	*The number and naming of these zones can vary. You might design a data lake with only the raw or curated zones, or maybe you need to perform more complex transformations to the data to comply with strict regulations, in which case you might have four, five, or more zones to handle intermediate storage stages.* 

![[Screenshot 2026-01-07 at 15.27.41.png]]

Organizing data into different zones allows you to apply appropriate **data governance policies** *on each zone* and ensures that data users are consuming data at the appropriate level of quality and readiness for their specific needs. 


#### Data Partitioning

![[Screenshot 2026-01-07 at 15.28.25.png]]

To improve **query performance** with data lakes, you generally want to take the data from cleaned or curated zones and **sort it as partitions**. 

> Data partitioning is a technique where you divide a dataset into smaller, more manageable parts b*ased on some sort of criteria*, like time, date, or location recorded in the data. 
> 
> That way, when you query the data, the query engine only needs to scan the partitions that contain the data relevant to the query, resulting in faster query performance. 

#### Data Catalog

Finally, to address a challenge of data discoverability with data lakes, you can create a data catalog, which is a collection of metadata about the datasets. 

> Data Catalog is a collection of metadata about the dataset (owner, source, partitions, etc.)

This centralized metadata allows data users to search for database on things like the data owner, data source, partitioning information, business definitions for the columns, and much more. The catalog also records and maintains a schema of the datasets, including changes over time. The data catalog is a critical feature that provides everyone in the organization a common understanding of the data structure and meaning. So, with these enhanced data management features and search capabilities, you can use a data lake to store, manage, and process large amounts of data of many different types. 


### Problems of Data Lakes

Despite these efforts to make data lakes more organized and searchable, historically, organizations still needed multiple storage systems to meet their business needs. They wanted to leverage the low cost storage of data lakes to store large amounts of data for machine learning applications and they also wanted to leverage the superior query performance of data warehouses for analytical use cases. 

![[Screenshot 2026-01-07 at 15.33.06.png]]

So they would first ingest and process data in a data lake so that there's a single source of truth for all the data. Then they would take a subset of the data that needs to be queried frequently and loaded into a data warehouse to support low latency query performance. But this solution is expensive because you have to continuously move data with an ETL pipeline from your data lake into your data warehouse, which have higher storage costs. And each step of the ETL process can introduce bugs or failures, causing issues with **data quality,** **duplication**, and **consistency**. 

![[Screenshot 2026-01-07 at 15.33.26.png]]

To solve these challenges, a **new storage architecture** known as the **"Data LakeHouse"** was created. This new architecture aims to combine the benefits of a data warehouse and a data lake into *one unified architecture*. 


## The Data Lakehouse Architecture

You can think of a data lakehouse architecture as a **data lake with additional features built in** to create an experience that is similar to a data warehouse. It aims to combine the flexible and low cost storage benefits of a data lake with the superior query performance and robust data management of a data warehouse. This enables analytics and reporting applications, as well as machine learning and big data processing use cases. 

![[Screenshot 2026-01-07 at 17.58.47.png]]


### Architectural Components
Let's take a closer look at the key **architectural components** and characteristics of a data lakehouse. 

At its core, a data lakehouse is very similar to a data lake. It uses a **single storage layer** built on top of *object storage* to store large amounts of data of any type. You can organize a storage layer into different zones, like you saw in the previous video, to facilitate data governance and ensure better data quality. 
![[Screenshot 2026-01-07 at 18.00.05.png]]


**"Databricks"**, which is the company that first introduced the notion of a data lakehouse, refers to a storage layer organized by data zones as **"the medallion architecture"**, labeling: 
- the raw data zone as bronze, 
- the clean data zone as silver, 
- and the curated data zone containing modeled and enriched data as gold. 
![[Screenshot 2026-01-07 at 18.00.53.png]]
>	Transformed data is stored in the ***silver*** and ***gold*** zones, and written in an open file format, typically *parquet* for more efficient storage and allow analytics and query engines of all kinds to access the data directly. 

In addition to the data lake characteristics, lakehouses include the **data management features** found in data warehouses. They **enforce schemas at the storage level** to ensure that the data you load **adheres to specified formats and quality standards**, and they also **support schema evolution**. ![[Screenshot 2026-01-07 at 18.06.27.png]]

>	Data lakehouses typically adhere to **ACID principles**, meaning that transactions are atomic, consistent, isolated, and durable. This enables your data users to concurrently read, insert, update, and delete data, while ensuring it's reliable for analytical processes. 

Lakehouses also have **built-in data governance and security features**, such as: 
- robust access controls, 
- data auditing capabilities, and 
- data lineage tracking. 

You can also connect to a data lakehouse using **connector APIs**, then use **SQL** to perform incremental updates and deletions to your datasets. 

These are *critical features* that enable compliance with data regulatory and privacy rules. Since lakehouses retain old versions of files and metadata, you can also roll back to or access any version of your historical data as needed. 

![[Screenshot 2026-01-07 at 18.08.34.png]]


By integrating the best capabilities of data warehouses and data lakes, data lakehouses provide a **unified architecture that supports everything** from SQL applications to business reporting to machine learning. 

Since the inception of the data lakehouse, various cloud and software providers, along with open source organizations, have been creating new products to help organizations move toward a data lakehouse architecture.


## Data Lakehouse Implementation

Ever since the inception of the data lakehouse, we've seen an interesting fusion happening with Cloud data warehouses and data lakes: Cloud data warehouse providers have started integrating features typically associated with data lakes and meanwhile, data lake technologies have started embracing characteristics typical of data warehouses, like enforcing and managing schemas, as well as SQL functionality. 

In this section, we'll look at Data Lakehouse Implementation using **open table formats**. In the next section, will walk you through how you can implement a lakehouse on AWS. 


When it comes to lakehouse implementation, a number of open table formats have been developed to support the idea of a more ***transactional*** data lake. These open table formats include: 
- Databricks Delta Lake, 
- Apache Iceberg, and 
- Apache Hudi (which stands for Hadoop Update, Delete, Incremental). 

![[Screenshot 2026-01-07 at 18.13.02.png]]

>	*Open table formats like these are specialized storage formats that add transactional features to your data lake house.* 

This allows you to easily update and delete individual records in the storage layer that's built on top of an optic storage data lake, while supporting the ACID principles traditionally found in a data warehouse. 


**How do open table formats work exactly? 

Well, in short, they provide a **logical abstraction that says on top of your store data.** ![[Screenshot 2026-01-07 at 18.13.54.png]]


- When you **perform an operation** on your data tables, *such as inserting, updating, or deleting a record,* the open table formats track those changes and store them as a series of **"snapshots"** that reflect the ***state of the data at a given time***. 
- These snapshots enable a feature known as **"time travel"**, where you can see any previous version of a table by specifying a timestamp, as well as roll back the table to a previous version to recover from any incorrect changes made to a table. 
- This also supports **schema and partition evolution**, meaning that you'll still be able to query the data, even if you make schema changes, like adding or deleting a column, or changing how you partition the datasets. 
- Since open table formats are open source, they also enable different query engines to access the data stored in the data lake house. You can use whatever processing tools you like that are suitable for your use case without having to duplicate the data and restructure it into another format. ![[Screenshot 2026-01-07 at 18.14.55.png]]


In your work as a data engineer, you might encounter 
- Databricks Delta Lake, 
- Apache Iceberg, and 
- Apache Hudi. 

These technologies all offer the same features of schema evolution and time travel, but they usually differ in terms of the **underlying implementation details.** 

For example, here's how **"Iceberg"** works. 

![[Screenshot 2026-01-07 at 18.19.38.png]]

- Similar to a data lake, there's a **data catalog** and a **data storage** layer, which contains files written in *Parquet* for efficient storage 
- but then there's a **metadata layer** that says between the catalog and the storage layer. 

- Whenever you update or create a data file, Iceberg creates a new **"manifest file"** to keep track of these data files and additional details about the metadata of each file. 
- Then a new **"manifest list"** is created to keep track of the information about the location of the manifest files, which snapshot each manifest file is a part of and partitioning information. 
- Finally, a new **"metadata file"** written in JSON format is created that contains a new snapshot that points to the new manifest list. 
	- These files include information like the current table schema, partitioning information, snapshots, and which snapshot is the current one.

- Within the Iceberg catalog, there's a **pointer** for each table stored in the lake house that references the table's **most current metadata file**. This pointer gets updated whenever a new metadata file is created. 


When you run a query, the pointer in the catalog tells a query engine which metadata file is the current one based on the table that is being queried. The query engine then retrieves a manifest list for the current snapshot in the metadata file, then the relevant manifest files, and finally, the relevant data files. 

![[Screenshot 2026-01-07 at 18.24.25.png]]

This metadata layer helps Iceberg determine which data files need to be read and it ignores files that are not relevant to the query. This significantly speeds up query performance. 



When it comes to choosing between a data warehouse, a data lake, or a data lakehouse, it's really about choosing the *right storage abstraction to support your organization's needs*. 

- If you're at an early stage company that only needs to process small amounts of structured data for analytics and reporting, you might be able to get away with connecting your BI tools directly to a read replica of your production database. 
- However, as the volume and sources of data grow, you'll want to use a Cloud data warehouse to bring together structured and semi structured data from multiple sources and allow your data users to query current and historical data for analytics and reporting without adding too much extra load on your production databases. 
- If your organization requires *massive* amounts of data, especially if it's unstructured, say for machine learning applications, then you might want to consider implementing a data lake architecture to save on storage costs. 
- In this case, you can choose to evolve your storage architecture into a data lake house by adding on data management and discoverability features to enable both machine learning applications and low latency analytical queries. 

![[Screenshot 2026-01-07 at 18.25.53.png]]


As you've seen, the technical architectures of Cloud data warehouses and data lakes have started to converge. I think this trend of convergence will only continue. The data lake and the data warehouse will still exist as different architectures, but in practice, their capabilities will blend together. So in the future, instead of choosing between a warehouse or a lake, you will have the option to actually choose a **converged** data platform based on your specific circumstances and data use cases. 


## Lakehouse Architecture on AWS

It's common for organizations to start with a simple data lake and then evolve over time to use a more mature solution like a **Data Lakehouse**. 

Now it's time to discuss how you can architect a data lake house using AWS services, including "AWS lake formation", and "Amazon Redshift Spectrum". 

### AWS Lake Formation
At its core, AWS lake formation is designed to simplify the process of building and managing data lakes. 

Traditionally, setting up a data lake or lake house involves a lot of manual steps, 
- defining storage,
- setting up access controls, 
- cataloging data, 
- and managing permissions to data assets. 

This can be complex and time consuming, and lake formation automates some of these tasks, making it simpler for you to get started. 
![[Screenshot 2026-01-09 at 18.07.54.png]]

Here's how it works: 

- You start by *identifying your **existing data sources*** (like Amazon S3 or relational and NoSQL databases). ![[Screenshot 2026-01-09 at 18.10.14.png]]

- Then, you can use **"*Lake Formation*"** to move that data into your data lake. ![[Screenshot 2026-01-09 at 18.11.31.png]]

- After that, you again use lake formation to 
	- ***crawl* through the data** (with `Crawler`)
		- catalog it, and (with `AWS Glue Data Catalog`)
		- get it ready for analytics (`Metadata`)
	![[Screenshot 2026-01-09 at 18.12.54.png]]


- Finally, you can give your users secure self service access to this data with their preferred analytics tools. ![[Screenshot 2026-01-09 at 18.14.56.png]]It's a streamlined way to make sure everyone in your organization can easily find and use the data they need. 


You may recognize some of these tasks as things you've done before with AWS Glue, and that is because lake formation is *actually* built **on top of AWS Glue and IAM**, so, it's using features of Glue you are already familiar with like: 
- glue jobs,
- workflows, and 
- crawlers 
to perform these tasks. 
![[Screenshot 2026-01-09 at 18.16.05.png]]


When you're using lake formation, you can create things like **workflows**, but also manage these features directly in the Glue console. ![[Screenshot 2026-01-09 at 18.17.07.png]]

### Managing Permissions
With a typical data lake or lake house, there are ***many AWS services interacting with each other*** and with *end users* accessing different datasets. Along with that, comes a fair amount of overhead for managing permissions, and so, a main feature of AWS LakeFormation helps automate the creation of data lake by managing complex fine grained permissions.![[Screenshot 2026-01-09 at 18.17.57.png]]

Lake formation also provides fine grained access control on the data stored in S3, and the metadata in the data catalog. You can **centrally** manage permissions and IAM policies to streamline the process of governing and sharing your data internally and externally for both analytics and machine learning applications. 
![[Screenshot 2026-01-09 at 18.20.17.png]]


### Example Diagram of a data LakeHouse Architecture on AWS

Now that you've got a high level understanding of lake formation, let's review a diagram of an example of data lake house architected with AWS services. We'll dive deeper into various aspects of this architecture as we go along. 

![[Screenshot 2026-01-09 at 18.28.14.png]]


In the upcoming lab, you'll get hands on with some of these services.

More on AWS Lakehouse architecture in the [[AWS LakeFormation]] document.



