
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

TBC