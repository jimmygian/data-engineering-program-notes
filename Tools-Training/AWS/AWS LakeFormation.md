It's common for organizations to start with a simple data lake and then evolve over time to use a more mature solution like a **Data Lakehouse**. 

## AWS LakeFormation Overview
At its core, AWS lake formation is designed to simplify the process of **building** and **managing** data lakes. 

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
#### Data Sources
First, you have the data sources, which from a data engineer's perspective, are often *out of your control*. These are things like databases, file shares, SAS applications, and more. 

#### Ingestion Mechanisms / Ingestion Layer
Then you have the **ingestion mechanisms**, which would ingest data into the data lake house. 

As you have learned in previous courses, there are various services you can use for data ingestion, including: 
- Amazon Kinesis Data Streams, 
- Amazon Data Firehose, 
- AWS Data Sync, 
- AWS database migration service, and 
- Amazon AppFlow. 
- Also, AWS lake formation can manage some data ingestion tasks through AWS Glue. 

#### Storage Layer
All that ingested data needs to be stored somewhere, so next to the ingestion layer, there is the storage layer that uses **Amazon Redshift** and **Amazon S3**. 

#### Processing Layer
Above that, sits the **processing layer,** where you would read data from the Lake house storage layer and transform it for downstream consumers. You would use services like 
- Amazon EMR or 
- AWS Glue, 
- Amazon manage service for Apache Flink, or 
- SQL data processing on Amazon Redshift. 

#### Catalog Layer
Then there's the **catalog layer** that uses lake formation to provide a central catalog to store and manage metadata for all datasets hosted in the storage layer. 

In this layer, you can also use lake formation to manage permissions and provide fine grained access control. 

#### Consumption Layer
Then finally, the rightmost layer here for the lake house architecture is the c**onsumption layer**, which provides AWS services you may use to **consume** the data, including but not limited to 
- Amazon Sage maker for machine learning use cases, 
- Amazon QuickSite for business intelligence and data visualizations, and 
- Amazon Athena and 
- Amazon Redshift Spectrum for querying data in the lake house. 

We will spend more time exploring A**mazon Redshift Spectrum** in the next section. Since you have already learned a lot about data sources and ingestion use cases, I won't focus on those more right now. But I would like for you to join me in the next section for a closer look at the storage, processing, catalog, and consumption layers of these data lake house architecture. I'll see you there.


## Implementing a Lakehouse on AWS

In the previous section, I gave you a high-level overview of how various AWS services can be **combined** into a *series of layers* in a data lakehouse architecture. 

As a reminder, here's how that architecture looks. 

You have the source and ingestion layers on the left over here, 
![[Screenshot 2026-01-09 at 18.31.37.png]]

then the storage, processing and catalog layers in the middle, 
![[Screenshot 2026-01-09 at 18.32.04.png]]

and finally, the consumptions layer over here on the right. 
![[Screenshot 2026-01-09 at 18.32.27.png]]

---

I'd like to start off with a focus on the storage and catalog layers. 
![[Screenshot 2026-01-09 at 18.33.19.png]]


> 	For data lakes, it's common to use S3
> 	For data lakehouses, it's common to use both S3 and Redshift as the storage layer. 


Typically, 
- S3 provides storage for **structured, semi-structured, and unstructured data**, 
- whereas Amazon Redshift stores **highly curated, structured or semi-structured trusted data** that fits into **predefined schemas**. 

This dual storage approach leverages: 
- the **cost efficiency and scalability of "S3"** for large, structured, and unstructured datasets, 
- while utilizing **"Redshift"** for high performance analytics on more structured datasets. 

### Amazon Redshift Spectrum
Part of the reason for creating a data Lakehouse is that you want to be able to **analyze** the data that is stored across S3 and Redshift ***at the same time***. 

You could, of course, write *ETL jobs* that move data from S3 to Redshift on a recurring basis, but creating a data pipeline for this effort might be 
- costly over time 
- and can lead to data redundancy. 
- It also creates an opportunity to introduce bugs or issues into the data. ![[Screenshot 2026-01-10 at 11.22.41.png]]

Anytime you are moving and transforming data, you are opening the door for mistakes to be made, which can impact data quality and availability. With that being said, it would be nice if you could somehow integrate your data lake with your data warehouse ***natively***. This is where **"Amazon Redshift Spectrum"** comes in, which works as an *integration between S3 and Redshift for querying data.* 
![[Screenshot 2026-01-10 at 11.23.23.png]]

This is represented here in the *storage layer*, but it is a key part of the consumption layer for a data lakehouse on AWS. 
![[Screenshot 2026-01-10 at 11.24.00.png]]


>	In a nutshell, **"Redshift Spectrum"** allows you to run queries on data stored in S3 without having to load it into Redshift first. 

This is great because it *eliminates* the need for complex ETL pipelines to move data between your data lake and your data warehouse. This helps you make a data lakehouse possible by integrating these two data storage systems. Using something like Redshift Spectrum to be able to query data in both the data lake storage and the data warehouse storage is definitely a preferred method over moving data out of S3 using ETL processes into Redshift for querying. More on this when we get to the consumption layer. 

### Cataloging with Lake Formation
Next up, we'll move on to the layer above the storage layer, which is the catalog layer. 

>	A central data catalog is used to provide **metadata** for all datasets in your lakehouse in a single place and make it easily searchable. ![[Screenshot 2026-01-10 at 11.29.03.png]]

This is extremely important for self-service discovery of data in your lakehouse. You learned earlier about a data swamp. You don't want your data users to be wading through murky waters. Instead, you want to be able to provide clarity about the data in the lakehouse. In this case, we're using A**WS Lake Formation**, which uses Glue behind the scenes to create the data catalog to store metadata for all datasets hosted in the lakehouse. 

Lake Formation **coordinates Glue crawlers** to identify datasets, and then it persistently stores metadata, including 
- schema information, 
- partition information, and 
- data location in the Glue data catalog. 

Now, it's important to remember that it's common for datasets in the storage layer to have
- **evolving schemas** and 
- **increasing data partitions** over time, 
so populating the metadata catalog isn't a *one-and-done* job. It's something that has to be maintained and kept up to date. To automatically keep the catalog up to date, you can configure AWS Glue to **periodically crawl through the lakehouse storage layer** to discover new or updated datasets and *extract their metadata*, which is then stored in a table in the catalog. 

#### Apache Iceberg
While **Lake Formation** and **Glue** are great for *managing and cataloging your data*, handling, evolving schemas and large datasets efficiently is where **"Apache Iceberg tables"** may come into play. ![[Screenshot 2026-01-10 at 11.35.40.png]]

You learned in an earlier section about iceberg tables and how they make it easier to make changes to your data schema without disrupting existing processes or underlying data. This is made possible in part through ***schema and data versioning***, which allows users to track changes to data over time. With versioning, you can use the ***time travel*** feature to access and query historical versions of data and analyze changes to data between updates and deletes. 

Lake Formation also supports Iceberg tables, and you can create Iceberg tables that use Parquet format in the AWS Glue data catalog. 
![[Screenshot 2026-01-10 at 11.36.31.png]]

### Consumption Layer
Next up, we'll move on to the consumption layer. I want to spend some more time covering **Redshift Spectrum** and **Amazon Athena**. You'll have an opportunity to use Athena in the upcoming lab. Though you won't be using Spectrum, it is commonly used in data lakehouses on AWS, so you should understand the basics. 

#### Amazon Redshift Spectrum
Redshift Spectrum enables Redshift to present a **unified SQL interface** for data consumers that can *accept and process SQL statements* where the same query can reference and combine datasets hosted in the data lake or S3, as well as the data warehouse storage or Redshift. This means that by using Redshift Spectrum, you can reduce **data latency.** 

In other words, by querying data *in place*, you can get insights faster without waiting for data to be moved or transformed. Redshift Spectrum queries use *massive parallelism* to run queries against large datasets, and a lot of the processing occurs in the Redshift Spectrum layer.![[Screenshot 2026-01-10 at 11.46.07.png]] That means most of the data remains in Amazon S3. After your Redshift Spectrum tables have been defined, you can query and join the tables just as you would with any other Amazon Redshift table. 

Multiple Redshift clusters can also query the same dataset at the same time in Amazon S3 without the need to make copies of the data for each cluster. Using Redshift Spectrum, you can do things like keep large volumes of historical data in the data lake and ingest a few months of hot data into the data warehouse using Redshift Spectrum, or you can create enriched datasets by processing both hot data from Redshift and historical data from S3 without needing to move data in either direction. You can also more easily offload volumes of large historical data from the data warehouse into S3, which provides more cost effective data lake storage while still being able to easily query the data as a part of Amazon Redshift queries. 

### Amazon Athena
Then there is Amazon Athena. Athena makes it possible to query data in S3 directly using standard SQL. There is no need to load the data into another system to query it using SQL. 

Instead, you can **create tables using Athena and query it directly**. Athena is ***serverless***, so there is no infrastructure to set up or manage, and you pay only for the amount of data *scanned* by the queries that you run. 

#### Federated Queries
Athena also supports something called **federated queries**, which allow you to query data that's outside of S3. 

It supports a wide range of data sources for federated queries, including Redshift. The Amazon Athena Redshift connector enables Athena to access your Amazon Redshift tables. You can write queries that pull in data from your data warehouse. 
![[Screenshot 2026-01-10 at 11.49.36.png]]

For consumption using SQL, you can query your datasets in S3 and Redshift using Athena and/or Redshift Spectrum, which both can use the schema stored in the Lake Formation catalog and apply it by following the schema on read approach you learned about earlier in the course from Joe. ![[Screenshot 2026-01-10 at 11.50.14.png]]

That's an example of a data lakehouse architecture on AWS using AWS Lake Formation and other services, including Amazon Athena and Amazon Redshift Spectrum. 

In the upcoming lab, you'll get hands on with some of these services.



### AWS Lake Formation Permissions
Lake Formation allows you to enforce permissions at two levels, 
- metadata level permissions on the data catalog resources, such as databases and tables, 
- and storage access permissions on the underlying data stored. 

>	Lake Formation enables you, the data lake administrator, to grant IAM users or roles fine-grained permissions on data lake databases, tables, columns, rows, and cells. 

When you use AWS Lake Formation to manage the access to your underlying data, it provides temporary access to an integrated analytical engine, such as **Amazon Athena** or **AWS Glue**, to access the S3 data. In this way, you don't need to write a detailed IAM policy to grant data lake users direct permissions to interact with the underlying S3 objects. However, the permissions you grant to your data lake users using Lake Formation ***are meant to augment the regular IAM permissions, not replace them***. 

The data lake user still needs to be attached to an IAM policy that grants them access to: 
- the AWS Glue service, 
- the Lake Formation service, 
- and Amazon Athena. 

The way it works is that with an IAM policy, you apply *broad* permissions to a user or role, but then with Lake Formation, you apply *fine-grained* permissions to grant them access to *specific* S3 objects. 


## Week Summary

![[Screenshot 2026-01-10 at 19.09.14.png]]

This week, we walk through the evolution of storage abstractions from traditional data warehouses to modern Cloud data warehouses, and then to data lakes and finally, data lakehouses. You saw how the data lakehouse architecture aims to combine the advantages of both the data warehouse and the data lake to support the growing data needs of many organizations. Understanding the key concepts of each architecture enables you to choose the most appropriate storage solution based on your organization's needs. Remember that modern Cloud data warehouses can be used to store data for analytical workloads and reporting use cases. They enable low latency query performance by leveraging the massively parallel processing power of Cloud computing. But data warehouses typically come with higher storage costs. On the other hand, data lakes are built on low cost object storage to store large amounts of structured and unstructured data, supporting applications that require massive amounts of data, such as machine learning and big data processing. 

But without the proper data management features or data discovery tools, your data lakes can easily become unusable data swamps. In the first lab of this week, you saw how you can mitigate this challenge by creating a data catalog for the data sets stored in your data lake, and you partition the data to improve data retrieval. Finally, data lakehouses combine the scalable, low cost, and flexible storage capabilities of a data lake with the structured querying and data management features of a data warehouse to provide a unified platform that supports both low latency analytical workloads and machine learning. In the lab assignment, you created a data lakehouse using LakeFormation and Iceberg tables. As I said earlier, existing data warehouse technologies are increasingly incorporating features that allow them to also function like a data lake, and data lake technologies are incorporating features that allow them to also function like a data warehouse. In your work as a data engineer going forward, I think it's likely that you'll see the distinction between data lakes and warehouses and lakehouses start to fade away in favor of a set of tools that allows you the flexibility to optimize your storage solutions to the needs of your organization. Then next week, we'll dive into queries. 

You'll see how queries work under the hood and we'll explore strategies for improving query performance.


## Optional Readings

- Chapter 6 of [Fundamentals of Data Engineering](https://go.redpanda.com/fundamentals-of-data-engineering)
- [Bill Inmon - Data Warehousing in 2022 (Monday morning chat)](https://www.youtube.com/watch?v=-ckvw6I9KKw)
- [Building the data lakehouse, by Bill Inmon](https://www.databricks.com/resources/ebook/building-the-data-lakehouse)
- [Building the data warehouse, by Bill Inmon](https://www.amazon.com/Building-Data-Warehouse-W-Inmon/dp/0764599445)
- [What is a medalion architecture?](https://www.databricks.com/glossary/medallion-architecture)
- [Exploring the Architecture of Apache Iceberg, Delta Lake, and Apache Hudi](https://www.dremio.com/blog/exploring-the-architecture-of-apache-iceberg-delta-lake-and-apache-hudi/)
- [What is an Open Table Format? & Why to use one?](https://www.startdataengineering.com/post/what_why_table_format/)
- [Apache Iceberg](https://iceberg.apache.org/)
- [Delta Lake](https://delta.io/)
- [Apache Hudi](https://hudi.apache.org/)
- [Modern data lake centric analytics platform (AWS)](https://docs.aws.amazon.com/whitepapers/latest/aws-serverless-data-analytics-pipeline/logical-architecture-of-modern-data-lake-centric-analytics-platforms.html)

Links about Lakehouse configurations
- [AWS Lake Formation](https://aws.amazon.com/lake-formation/features/)
- [Lake Formation set up](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html)
- [AWS post: Use AWS Glue ETL to perform merge, partition evolution, and schema evolution on Apache Iceberg](https://aws.amazon.com/blogs/big-data/use-aws-glue-etl-to-perform-merge-partition-evolution-and-schema-evolution-on-apache-iceberg/)
- [Using the Iceberg framework in AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/aws-glue-programming-etl-format-iceberg.html)