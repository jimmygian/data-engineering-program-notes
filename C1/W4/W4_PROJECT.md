## Requirements Gathering Exercise

**Functional Requirements**

When thinking about the **functional** requirements of the data pipeline, consider the tasks the pipeline needs to perform (i.e. what data it needs to ingest or combine, how it should process it, and what data it needs to serve). 

Other common considerations around functional requirements that are usually discussed for machine learning models include:

- The duration the data pipeline should retain or store the training data.

- How the data pipeline should combine the old with the new dataset. Should the old dataset be discarded? If the same customer updated their rating for a given product, should you keep the last rating or compute the average?

- The file format the training data should be served in. (e.g. CSV, Parquet, etc.)

**Nonfunctional Requirements**

**Nonfunctional** requirements are **attributes** or **characteristics** of the data pipeline that allow it to successfully meet stakeholder needs. The data scientist mentioned that they’d like to retrain the model as soon as they notice any drift in performance or change in the input data, meaning that the data pipeline needs to perform a simple transformation task only when it is needed. So, it must **not require too much operational overhead** to deliver the new training set.

Another common consideration around nonfunctional requirements is the **cost** of implementing the batch pipeline. You generally want to build a cost effective pipeline.

| **Functional Requirements**                                                                                                                                                                                                                                                                                                                                                               | **Nonfunctional Requirements**                                                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Provide the training data for the content-based recommender model in the following format:<br><br>- Tabular data <br>    <br>- Each row in the table contains the following user and item features: “customer number”, “credit limit", "city", “postal code”, and “country”, “product code”, "quantity in stock", "buy price”, "msrp", "product line", "product scale", “product rating”. | - The data system must be easy to maintain and update, and requires less operational overhead (Irregular / on demand run schedule)<br>    <br>- The data system must be cost effective. |


## Translating Requirements to Architecture (W4 PROJECT)

You've talked with stakeholders and gathered your system requirements. Now it's time to translate those requirements into tool and technology choices for your system. 

In the process of converting system requirements to actual data systems, you need to be familiar with what different services it can be used for. 

### AWS Services for BATCH pipelines

![[Screenshot 2025-11-07 at 12.26.57.png]]

#### What's the Data Source?
We need to know what the Data Sources are to be able to archtect our technologies. In our case, we will be processing Tabular Data through Amazon's RDS.

#### How are we planning to extract the data?
1. **EC2:** One way is using EC2, to spin up an EC2 instance and write a bunch of scripts to connect to the database > Ingest and transform the data > then send it onto storage somewhere. This could certainly work, but keep in mind that you need to avoid **undifferentiated heavy lifting**. 

Serverless Tools.: The best approach is to look for serverless options first that are the most easy to set up and maintain. 

2. **AWS Lambda:** AWS Lambda was one of the first and remains one of the most popular serverless tools on AWS. With Lambda functions, you can have code **run in response to a trigger or event** and for your ETL pipeline this week
   
   However, Lambda functions come with **limitations**. Things like a 1**5 minute timeout** for each function call and limitations around **memory and CPU allocation** for each function among others, which might mean you need to break up your task and do smaller chunks to stay within these limitations. Beyond that, writing Lambda functions requires you to write **custom code** for your use case, which in this case, again, might not be the best use of your time. 

In terms of serverless tools that are specifically for **batch processing** of data, there are two services I'd like to talk about. These are **Amazon Glue ETL** and **Amazon EMR serverless**. In some sense, you could say that there is a fair amount of overlap between the things these two services can be used for. And really, it may come down to the nuances of your specific project that determine which tool can best serve your needs. 

3.  **Amazon Glue ETL**: Glue ETL provides a more convenient experience.
	- Glue ETL can also handle big data workloads, but the real advantages are the additional features you have access to. For example, when you connect to a source system, Glue uses something called **AWS Glue Crawlers**, which automatically discover and classify data creating metadata in the process, including things like table definitions and schemas. 
	- This metadata is then used to populate the "**Glue data catalog**", which is a central repository containing information about all of your data assets. 
	- With this information about your data now in your data catalog, you can use the "**Glue Visual ETL tool**" to design your pipeline using a graphical interface in the AWS management console that will **automatically generate the Spark code** you need to run in your pipeline. 
	- When you run your pipeline, the servers will maintain the Glue data catalog to track the transformations you apply, and that catalog can be used downstream to more easily integrate with other AWS services.
	
4.  **Amazon EMR serverless**: EMR serverless gives you more control over what you can do.
	- Designed as a big data tool, supporting a wide range of frameworks like Apache Spark and Apache Hive. If your team is handling petabyte scale analytics, maybe using Hadoop or requiring the flexibility to incorporate custom components, then EMR or EMR serverless might be the right choice.

![[Screenshot 2025-11-07 at 12.49.18.png]]

There are other options you could consider for building the ingestion and transformation portion of your ETL pipeline on AWS, but those are the main options I wanted to share with you.


#### How do we Load / Serve the data?
When it comes to the storage and serving aspects of the batch pipeline you're working with this week, your choice would depend mainly on **what downstream use case you're serving**. 

- For example, if you're ingesting **normalized tabular data**, then applying transformations to model it in a star schema for analytics, then one option you could choose would be to store and serve it in **another RDS** **instance**. 
- Or if you wanted to run **complex analytical queries** on massive datasets and take advantage of other features that data warehouses offer, you could choose to store and serve the data in **Amazon Redshift**. Which is a powerful data warehouse solution, albeit at a significantly higher cost than RDS. 

![[Screenshot 2025-11-07 at 12.52.41.png]]

**Serving The Data**

This week, you'll be serving a machine learning use case, where the data will be used for **training a recommender model**. 

When your downstream data consumer is another technical data professional who's planning to manipulate the data and incorporate it into their own systems, oftentimes, **the best and cheapest storage and serving option is object storage on Amazon S3**. Out in the real world for data systems built on AWS, it's relatively common for S3 to serve as a sort of **staging area** like this because S3 is flexible, scalable, and relatively cost effective. It allows you to store virtually any kind of data that easily integrates with other AWS services. **In S3, you store data in buckets, which are containers for objects.** 

When building data pipelines, you may have multiple S3 buckets that are used throughout different stages of your pipeline depending on the task at hand.

![[Screenshot 2025-11-07 at 12.56.36.png]]



## AWS Services for STREAMING pipelines

In the lab this week, in addition to a batch component, your system will also have a streaming component.

Streaming data could be coming to you from a number of different sources. These could be IoT devices or click-stream data from a website or mobile app. Your streaming source could even be a database in the sense that you could be continuously streaming any changes or updates to the data in the database through a process called **change data capture or CDC**. 


**EC2**
Just like for batch data processing one way you could potentially ingest streaming data would be to spin up an **EC2 instance** and write some custom scripts to perform CDC or connect to whatever other streaming source, then transform the data and set it downstream to wherever it needs to go. 
![[Screenshot 2025-11-07 at 13.02.11.png]]
Just like with batch processing, this EC2-based approach means you'd be responsible for installing software, managing security, and all the complexity that comes with deploying a server on the Cloud. 


**AWS Lambda**
Following the same ideas we discussed with batch processing, you could also consider **Lambda functions** as a serverless option to create your own streaming system. But, again, this comes with writing custom code for whatever you need to do and the potential limitations of Lambda for your particular use case. On top of that, interacting with streaming sources can be more complex than running batch workloads. 

I can say that you probably shouldn't build your own custom streaming solutions with EC2 or Lambda. Unless you are 100% certain that your use case cannot be solved with existing open source or managed tools. 

### **Serverless Options**
Let's take a look at some of the AWS services that can support streaming workloads. First up is Amazon Kinesis Data Streams.

**Amazon Kinesis Data Streams**
![[Screenshot 2025-11-07 at 13.20.55.png]]
This is a popular AWS service that enables real-time data ingestion. The way it works is that you have **data producers** that are sending data to Kinesis **Data Streams**. This could
- log data from web servers, 
- data from IoT devices, or 
- click stream data as a few examples. 

Kinesis itself is not picky about the type of data that you send. It's **data agnostic**. So you can send JSON, XML, structured or unstructured data to a data stream. 
![[Screenshot 2025-11-07 at 13.24.59.png]]
1. The data gets posted to the stream by the **producers**, 
2. Then, the data is **stored** in Kinesis for a **configurable amount of time**. 
   The default and minimum retention time is 24 hours, but it can also be extended. 
3. The stored data can then be **pulled** by consumers of the data stream. 
   Multiple consumers can pull the same data and process it in different ways. 
   It's common for Kinesis consumers to **either** 
	- take the data and place it somewhere else, such as a **storage service** or **data warehouse**, 
	- or ,consumers might be performing some **real-time analysis** of the data that is passing through the stream. These consumers could be software applications that are pulling data from the stream and processing it, and that application could be running on compute services like EC2 or Lambda. 


**Amazon MSK (Amazon Managed Streaming for Apache Kafka)**
![[Screenshot 2025-11-07 at 13.20.13.png]]
Apart from Kinesis, another option you have is Amazon Managed Streaming for Apache Kafka, otherwise known as MSK. MSK is a service that provides much of the same functionality as Kinesis Data Streams. 

![[Screenshot 2025-11-07 at 13.21.26.png]] Apache Kafka itself is an *open source* **streaming platform**, that is a popular choice for many different streaming use cases. 

MSK is a fully managed service that makes it easier to build and run applications that use Apache Kafka to process streaming data. MSK runs open-source versions of Kafka, which is useful because any existing applications, tooling, or plugins from the Apache Kafka community are supported. 

The way MSK works is that: ![[Screenshot 2025-11-07 at 13.26.33.png]]
- Tou first create an **Apache Kafka cluster** (middle panel), 
- The MSK service manages the heavy lifting of **provisioning and operating** the **nodes** that run Kafka for you. This allows you to avoid that undifferentiated heavy lifting and focus more time on your customized application logic. 
- Then you, as the user, interact with what's called the **Kafka data plane** that MSK manages for you to create topics, produce, and consume data. 
- **Data producers** and **data consumers** then connect to the cluster to send and receive messages. 

Both Kinesis Data Streams and MSK can scale up to handle **petabyte-level data volumes** coming from multiple data sources with **millisecond latency**, which enables real-time processing and analytics. 

When it comes to choosing one versus the other, just like when it comes to choosing between similar tools for other aspects of your data systems, it will depend on your use case. But at a high level, you could think of this again as a trade-off between control and convenience. 
- **Amazon MSK**: More Control
- **Amazon Kinesis Data Streams**: More Convenienve
If you're brand new to data streaming architectures, Kinesis is often recommended for its relative user-friendliness and reduced operational overhead. On the other hand, if you're already running a Kafka cluster or maybe you have existing Kafka technical experience in-house, or if you're looking for a higher degree of flexibility and control, then MSK might be the better choice. 
![[Screenshot 2025-11-07 at 13.31.00.png]]


**Amazon Data Firehose**

With regard to the use case of **reading the data from a stream and storing it somewhere else**, the next service I want to tell you about is Amazon Data Firehose. 

For a little context on why the Data Firehose service exists in the first place, it turns out that Kinesis came first as a service for **streaming data systems**. AWS realized that a lot of users of Kinesis Data Streams, are simply taking the stream data and storing it in S3 or somewhere else. But to work with Kinesis in this way, **you need to write custom code** that creates the connection with the data stream, reads the stream, chunks the data, and then stores it. To make that whole process easier, AWS created the **Amazon Data Firehose** service, which can integrate with Kinesis Data Streams and is designed to **allow you to get data from a stream and store** it in a destination like **S3** or **Redshift**, or send it to **HTTP endpoints** or third party service providers such as **Data Dog** or **Splunk**. 

The main takeaway with Data Firehose is that it helps you more easily read data from a stream and move it to storage **without needing to write custom code** or create any difficult integrations yourself. Beyond Kinesis Data Streams, Data Firehose also integrates with more than 20 other ADS sources to ingest streaming data, including MSK and others. As with all the other ADS Cloud topics we've discussed in this course so far, there is a lot more to know when it comes to streaming resources and services. 


#### What we'll be using in this lab assignment

In the lab this week, you will only focus on implementing the part of the streaming pipeline that stores the recommended products.

![[Pasted image 20251107134621.png]]


**✅ Streaming System:** Amazon Kinesis Data Streams is a highly scalable streaming solution that provides low latency access to data. It offers an on-demand serverless deployment that makes it easy to set up and manage your data pipeline. It is a simpler solution than Amazon MSK, and can help you get started quickly without requiring special expertise. Kinesis Data Streams integrates well with other AWS services, and scales with increased volume in data (you can check out the pricing for this [here](https://aws.amazon.com/kinesis/data-streams/pricing/)). Amazon Data Firehose is also used here because it helps you deliver streaming data from Kinesis data streams into data stores such as S3. Moreover, in the lab, you will use Kinesis data firehose to invoke a lambda function that allows you to run the computations of the deployed model to find the products to recommend.

**✅ Storage:** Amazon S3 is a durable, performant, and low-cost storage solution that you can use to store the product recommendations for later analysis. Since the downstream data scientist is another technical data professional who’s planning to analyze the data and use it to retrain the model when needed, S3 is a great storage option because it is flexible, scalable, and relatively cost-effective.

## 1. Batch Pipeline

![[Screenshot 2025-11-07 at 16.40.02.png]]




## 2. Streaming 
![[Screenshot 2025-11-07 at 17.49.04.png]]