Also Check this week's slides: [C2_W1_slides](./C2_W1_slides.pdf)

This course includes a focus **on the first two stages of the data engineering life cycle**, which are: 
- Data Generation and Source Systems.
- Data Ingestion from those source systems.

In this part of the course, we'll definitely be talking about, understanding the source systems from which you're going to get your data, different ways of ingesting it. As well as different ways of orchestrating these data pipeline workflows and monitoring them to make sure that you're preserving data quality, as well as ingestion, performance, and other characteristics, etc., very important to your work as a data engineer.

[**Course 2 - Source Systems, Data Ingestion, and Pipelines**](https://www.coursera.org/learn/source-systems-data-ingestion-and-pipelines/home/welcome)

This course consists of 4 weeks of content and covers these main learning objectives:

- Identify different data formats and determine appropriate source systems for generating each type of data
- Explain at a high level how data is generated, stored, and retrieved in various source systems, including relational databases, NoSQL databases, object storage, and streaming systems
- Explain the basics of cloud networking
- Troubleshoot database connection errors
- Explain the difference between batch and streaming ingestions and identify uses cases for each pattern
- Differentiate between the two batch ingestion patterns: Extract-Transform-Load (ETL) and Extract-Load-Transform (ELT)
- Create a script to ingest data from a REST API
- Describe the basic components of an event-streaming platform
- Interact with an event streaming platform as a source system and as an ingestion tool
- Use Terraform to provision AWS resources for your data pipeline
- Identify tools for monitoring your data systems and data quality
- Identify and monitor relevant data quality metrics
- Explain how orchestration can be applied to a data pipeline, and list its benefits
- Build data pipelines with DAGs in Airflow using features such as Taskflow API, operators, XCom variables, etc.


## Working with Source Systems

 This week, we're going to get started by looking at different types of source systems and how you can interact with these systems. 
 
As you saw in the first course of the specialization, data generation in source systems is the first stage of the data engineering lifecycle. 

![[Screenshot 2025-11-07 at 19.26.11.png]]

As a data engineer, you are typically **not responsible** for generating this data yourself or maintaining these source systems. But ingestion from source systems is where all your data pipelines will begin. So, it's important that you understand: 
- **how this data is generated**, **
- where** and **how** it's stored, and 
- some of its **characteristics** 
so that you can build robust data pipelines with these upstream systems as your data source.


## Different Types of Source Systems

The specific source systems you'll work with as a data engineer will often vary depending on what kind of data you're ingesting from those systems. 

**Structured Data**
The most common type of data you'll work with is structured data

![[Screenshot 2025-11-07 at 19.32.47.png]]
which is to Structured data are data organized as tables of rows and columns. Chances are you've worked with structured data in the past, whether that was in a **spreadsheet** or a **relational database**, or maybe even using Python to read a **CSV** file. 


**Semi-Structured Data**
Semi-structured data is data that is **not in tabular form**, so it's not made up of rows and columns, but it still has some structure. A common semi-structured data format you'll run into is what's known as **JavaScript object notation, or JSON**. A JSON file contains a series of **key-value pairs**.

**Unstructured Data**
Unstructured data, on the other hand, has no predefined structure. For example, text, video, audio, and images are all examples of unstructured data. 

But you'll note that things like video, audio, and images do have an inherent structure behind the scenes, in the sense that there are dimensions of pixels as well as colors such as red, blue, and green. We'll dive more into unstructured data throughout this course. 


### Source System Types
When it comes to ingesting these different types of data, I'd like to classify the relevant **source systems** you could encounter into three general **types**: 
1. **Databases**
2. **Files**
3. **Streaming systems**




While these three types of source systems don't necessarily correspond one-to-one with the three types of data, you could say that:
- From databases, you'll most often be ingesting **structured** and **semi-structured** data. 
- From streaming systems, you will often be ingesting **semi-structured messages** as a data format. 
- And files, well, files could be anything from text to image, audio, video, or even regular old rows and columns of tabular data. 


### Source System type: Databases
Databases **store information in an organized way** that allows you to find, retrieve, update, and delete data. The way this works is through a transactional pattern known as **CRUD**, which stands for **create, read, update, and delete**. The C comes first because, of course, data has to be created before it can be read, updated, or deleted. 

**DBMS**
There's typically a **software interface** called the **database management system**, or **DBMS**, that sits between the physical database storage and the person or application interacting with the database. The DBMS is what allows you to access and manipulate the data stored in the database. 

There are two types of databases that we'll look at this week. 
- Relational databases, that store information in **tables** with rows and columns, and 
- Non-Relational, also known as **NoSQL** or not only SQL, which are databases that store **non-tabular** data. 


### Source System type: Files
You no doubt already have lots of experience working with files of various types. These might be **documents** you store on your computer, or **images**, or **videos** you record with your phone, or maybe even a **CSV file** you receive in an email from a coworker. It might seem strange to think of regular old files as a source system for data engineering, but at its core, **a file is just a sequence of bytes that represent information**. 

Applications of all types write data to files, and so files are a universal medium of data exchange. And believe it or not, they're one of the most common source systems you'll work with as a data engineer. 

Files, just like data, can be structured like a spreadsheet, semi-structured like a JSON or XML file, or unstructured like a text, image, video, or audio file. You might be receiving or accessing these files from a file system like Google Drive or an object storage system like Amazon S3, or simply as an attachment to an email. 


### Source System type: Streaming Systems
The third type of source system you're likely to be ingesting data from is **streaming systems**. And you can think of streaming systems as providing a **continuous flow of data**, recorded as messages that contain information about events. And those events include something that happened in the world or a change to the state of a system. In practice, you might be interacting with a stream of events via **message queues** or other streaming platforms. 

For example, an IoT device like a smart thermostat might record an event that contains the latest temperature reading and publish that event as a message to a streaming platform like **Kinesis** or **Kafka**. Then, as a data engineer, you could set up another service to ingest this message and send an update to an embedded analytics dashboard. In this case, you can think of the streaming platform as a source system from which you are extracting raw data. 

In later weeks of this course, you will see how these streaming systems can also cut across the data engineering lifecycle and be used in the ingestion and transformation stages to process data for various downstream use cases. In fact, you can see the same of all types of source systems, whether you're talking about databases, files or streaming systems. These could be systems from which you're ingesting raw data, or they could be systems you build into your data pipelines at another stage of the lifecycle. 


![[Screenshot 2025-11-07 at 19.59.29.png]]
So, to recap, as a data engineer, you will extract raw data from different source systems. This raw data may be structured, semi-structured or unstructured, and the source systems may be databases, files or streaming systems. 


## Relational Databases

As a data engineer, the most common type of source system you'll interact with is a **relational database**, and this is because relational databases are everywhere. 

Many web and mobile applications use relational databases on the backend, and you will find them as well in many corporate systems like customer relationship management, human resource, and enterprise resource planning systems. 

They are also commonly used for what are called **online transaction processing**  (OLTP) systems where you need to execute a high volume of transactions concurrently, like for banking or online bookings. 

The name "*relational*" database comes from the fact that this type of database is most often used to store data across different tables that are related to one another through a set of keys or common attributes. These tables are typically organized based on how information is structured in the business. 

So, as a data engineer working at an e-commerce company, for example, you might be working with a relational database where one table captures customer information, another table captures product information, and a third table captures order information. ![[Screenshot 2025-11-10 at 22.31.14.png]]

Structuring a database in this way **reduces redundancy** and makes the data easier to manage by not having the same piece of information duplicated across multiple rows or tables in the database. 

**The way** in which a database is organized into related table is called the **database schema**. Relational databases represent these relationships across tables through the **use of keys**. 

- A **primary key** is a special column or a collection of columns that **uniquely** identify each row in a table. 

![[Screenshot 2025-11-08 at 00.22.01.png]]

- A **foreign key** references the **primary key** or the **id** column of another table. 

Beyond the row structure in a relational database, each **column** has a 
- unique name, and a
- specified data type. 

One big table for everything will almost certainly lead to many duplications, and inconsistency in case some details need updating.
![[Screenshot 2025-11-10 at 22.32.57.png]]
![[Screenshot 2025-11-10 at 22.38.38.png]]

Relational data can help with better data structure and no duplication of the same data in multiple tables through the use of keys.

 The way in which a database is **organized** into related tables like this is called **the "database schema"**. Relational databases represent these relationships across tables through the use of keys. A primary key is a special column or a collection of columns that uniquely identify each row in a table.


**Data Normalization**
As you can imagine, there are many different ways you could potentially establish relationships between tables, and this is where the concept of **data normalization** comes into play.  Data normalization is an approach that was developed in the 1970s to minimize redundancy and ensure data integrity by storing data across tables in a logical way. 

**Normalized Relational Data vs OBT approach**
But is there any downside to structuring the data in a relational normalized way? Well, it turns out while a normalized relational database structure provides a high degree of integrity and minimizes redundancy, it can actually be slow when it comes to **querying** the data. Nowadays, storage is relatively cheap and speed is often of the essence.  Data integrity is so critical, of course, but the answer to exactly how you just store your tabular data could depend on **what you're trying to optimize for.** 

Today, there are even some use cases where data engineers are electing to take a so called one big table or **OBT approach**, where all the data is recorded in a single table for faster processing than will be possible if joining multiple tables in a traditional relational database. 

**RDBMS**
When it comes to interacting with the database, you'll use a relational database management system, or RDBMS. That's a software layer that sits on top of a relational database. There are many popular RDBMs out there, including **MySQL, PostgreSQL, Oracle, and SQL Server**. 

Most RDBMS support the structured query language, also known as sequel or SQL for short. 
SQL provides a set of commands for performing various operations on the relational databases. 

And as a data engineer, SQL will be part of your everyday work. 

![[Screenshot 2025-11-10 at 22.45.13.png]]


## SQL Queries

Working with an SQL database requires understanding of many aspects of the database.

Let's take an example:  Database for a fictitious DVD rental company called **Rentio**.

Rentio's database includes tables that contain information about stores, staff, DVD inventory, customers, etc.

To query the data, we need to:
- Know how to (practical SQL querying skills)
- Understand the Database Schema (names of tables, columns, relationships through keys), This database is normalized, meaning that data is stored in separate tables to reduce redundancy and make it easier to update the data when it changes. You can refer to this as the "**entity relationship model**" that shows the relationships and attributes of the tables in Rentio's database.

Basic SQL Statements:

```sql
SELECT [column] FROM [table] LIMIT [no_of_rows]
SELECT * FROM ..
```

> 	*You'll learn how queries are executed behind the scenes in the next course. But for now, just know that it can take a lot of processing resources to retrieve all data from all the columns, especially if your dataset is very large.* 


I recommend **only** using `SELECT *` to retrieve all the data in a table, where you can filter the results with some Boolean condition. For example, let's say you're only interested in exploring films that are less than 60 minutes long. You can add a `WHERE` clause after the `FROM` clause to filter the results based on the length column. 

```sql
SELECT * FROM film
WHERE length<60
ORDER BY length DESC
LIMIT 10
```


What if we want to explore data from more than one table?
We can use the `JOIN` clause to combine two or more tables.

For example, let's say I want to get 
- a list of film titles 
- and their corresponding film_category 
- for all films 
- that are under 60 minutes long.

![[Screenshot 2025-11-10 at 23.00.32.png]]


```sql
SELECT film.title, category.name 
FROM film
JOIN film_category
ON film.film_id = film_category.film_id
JOIN category
ON category.category_id = film_category.category_id
WHERE length<60

```

**INNER JOIN**

Note that by default, the `JOIN` clause combines **only** the records from both tables that have a matching column value specified in the ON statement. It will not include any records from either table that don't have matching values. **For example, if the film table has a row with film_id that doesn't appear in the film_category table, then that row will not be included in the results.** This type of join is also known as the INNER JOIN, and you can think of the join results as the **middle overlapping part** of a Venn diagram.
![[Screenshot 2025-11-10 at 23.09.40.png]]


The other types of joins include: 
- the LEFT JOIN, which returns all records from the first table, along with any matching records from the second table, 
- the RIGHT JOIN, which returns all the records from the second table, along with any matching records from the first table, and 
- the FULL JOIN, which returns all the records from both tables **and combines the ones with matching values.**
![[Screenshot 2025-11-10 at 23.11.14.png]]

**GROUP BY**
Going back to the results from the last query, I can see that quite a few of the shorter films belong to the children or documentary category. Let's say I want to know for sure which is the most popular category for short films. I can use the **GROUP BY** command to group the rows based on the film_category. Then use the **COUNT** command to count the number of records for each of the film categories. The GROUP BY command is written after the WHERE clause. Here I'll add GROUP BY category.name. Then I'll modify the SELECT statement to select category.name and **COUNT(*)**, which counts all the rows for each category. 

I will also use the **AS** command to give the output of this count the film_count **alias name**. Finally, I'll order the results by the film count in descending order. 

```sql
SELECT category.name, COUNT(*) AS film_count
FROM film
JOIN film_category
ON film.film_id = film_category.film_id
JOIN category
ON category.category_id = film_category.category_id
WHERE length<60
GROUP BY category.name
ORDER BY film_count DESC
```


The lab also covers some **data manipulation operations**, including **CREATE**, **INSERT INTO**, **UPDATE**, and **DELETE**. Make sure you read the instructions carefully when trying each of the exercises. 
![[Screenshot 2025-11-10 at 23.16.28.png]]


Check  [SQL](../SQL)'s document for more.


## NoSQL Databases

In the early 2000s, tech giants like Google and Amazon began outgrowing their relational databases. They needed to process large volumes of data from disparate sources that didn't fit neatly into the relational database model. Enforcing tabular structures would lead to **data redundancy and performance issues at scale**, and so these companies led the way in developing new distributed non-relational databases to scale their web platforms. 

In this way, **NoSQL databases** were developed to overcome the limitations of relational databases, trading certain RDBMS characteristics like strong consistency, joins, and a fixed schema for more schema flexibility, scalability and improved performance. 

Before we go any further, let's get one thing straight, NoSQL doesn't stand for No SQL, it means **not only SQL**. It's a category of databases that break away from the relational framework we saw in the previous video. But **some non-relational databases still support SQL or SQL like query languages.** 


Let's go over the basics of NoSQL databases. 

NoSQL databases have **non-tabular structures**. They can support various data formats including: 
-  key-value, 
- document wide-column, 
- graph, 
- and others.

![[Pasted image 20251123160754.png]]

- Unlike relational databases, NoSQL databases **don't require predefined schemas**, so this means you have more flexibility when deciding how you want to store your data. 
- NoSQL databases excel in **horizontal scaling**, which means automatically distributing data and workloads across multiple servers to meet increased traffic demands. 
  When a user writes data to a NoSQL database that is distributed across multiple servers - or nodes - that write operation is first performed on a single node in this distributed system, which is a location where one version of the database is running.  ![[Pasted image 20251123161139.png]]
  
  Then, there might be a slight delay before those changes are propagated to all other nodes in the system. ![[Pasted image 20251123161249.png]]
  
  Unlike relational databases, NoSQL databases operate under the **principle of "eventual consistency"** rather than strong consistency, meaning that the database will allow you to read from a node that has not received the latest write update and you may not get the most up to date data.  ![[Pasted image 20251123161406.png]]
  
  But given enough time, the database will be consistent and reading data from any node will give you the same data. 

With a relational database that provides strong consistency, you would *not* be able to read data until all the nodes in the system have been updated. In this way, eventual consistency allows no single databases to **prioritize speed** which is perfect for applications where system availability and scalability is more important than real time consistency, such as social media platforms or content distribution networks. 

#### Data Integrity
In terms of **data integrity**, **not all** NoSQL databases **guarantee** the principles of atomicity, consistency, isolation and durability, also known as **ACID compliance**, but some do, for example, MongoDB. This means that if you're sourcing data from a NoSQL database, then you may need to take extra steps to ensure data integrity. 

#### Querying NoSQL databases
Finally, NoSQL databases use **specialized query languages** tailored to their **data model**, which are often, but not always, different from SQL. Let's take a closer look at two common types of NoSQL databases, **key-value databases** and **document databases**.

**Key-Value database**
A **key-value** database stores data as a collection of key-value pairs, similar to what you might find in a **JSON file** or a **Python dictionary** structure. The key serves as **unique identifier** to retrieve the corresponding value. **Both** the keys and the values can be anything from **simple to complex objects**. This type of NoSQL database is perfect for scenarios where fast data lookup is needed, such as caching user session data in a web or mobile application. 

For example, when a user logs into an ecommerce application, actions like viewing different products, adding items to the shopping cart, and checking out can all be stored in a key-value database with the user session id as a unique identifier. 

![[Pasted image 20251123162044.png]]

**Document Stores**
Document stores are a special type of key-value database that store data in JSON like documents. Each document has a **unique key** that identifies a document and allows you to retrieve that document's data. Documents are **organized into collections**, so you can think of a collection sort of like a table in a relational database and a document like a row. 

In this example, data is stored in a collection called users. Each document represents a single user and the id is the key that uniquely identifies each user. This locality makes it easier to retrieve all the information about a particular user compared to a relational database, where the user information may be spread across multiple tables. 

![[Pasted image 20251123162255.png]]

However, document stores **don't support joins**, so it's harder and less efficient to combine information from multiple documents as compared to combining information across multiple tables in a relational database. The advantage, however, is this notion of a **flexible schema**. As you saw with relational databases, all records need to conform to a fixed schema, but with key-value databases and document stores, there's no fixed or predefined structure to data records. 

Document stores are commonly used for applications involving content management catalogs and sensor readings. Each interaction, product or sensor reading from an IoT device, for example, can be stored as a single document with a flexible schema. But be careful, this flexibility can have a downside. I've seen document databases become absolute nightmares to manage inquiry. 

And if you're ingesting data from a NoSQL document store as your source system, the flexibility of the schema makes it even easier for source system owners to change something that'll break your data pipelines. Both relational databases and NoSQL databases can be used as sort of a wide range of applications. When it comes to applications processing online transactions in areas like banking, finance and e commerce, among others, things are happening fast, money is changing hands, and products are on the move. And these types of online transaction processing or OLTP applications, any errors or inconsistencies in the data can cause major problems. 


## Database ACID Compliance

Both relational and non-relational databases can support very high transaction rates. They are commonly used in **online transaction processing or OLTP systems**. These systems typically need to store rapidly changing application states, such as the details of bank account balances or online orders. 

Most relational database systems are what's known as **acid compliant**, which means they support the principles of 
- atomicity, 
- consistency, 
- isolation, and 
- durability 

This helps ensure transactions are processed reliably and accurately in an OLTP system. 

By contrast. Many NoSQL databases are **not asset compliant** by default, but many offer you the ability to **configure** them to be asset compliant. In this video, I'm going to talk about what each asset principle is so you can get a better sense of when these principles will apply to the work you do as a data engineer. 

#### Atomicity
The first asset principle is **atomicity**, which ensures that transactions are atomic, or in other words, treated as a **single indivisible unit**. 

A transaction might consist of multiple operations. 
![[Pasted image 20251123163941.png]]

But the atomicity principle ensures that either all of the operations within a transaction are executed successfully, or none of them are. 
![[Pasted image 20251123164109.png]]


#### Consistency
The second principle is **consistency**, which means that **any changes** to the data made within a transaction must follow the set of rules or constraints defined by the database schema. 

This ensures that the database will transition from **one valid state to another**. 
![[Pasted image 20251123164356.png]]

For example, suppose that the inventory database schema prevents any stock level from going below zero. Let's say that the stock level for a particular item is currently one. If a customer tries to place in order for two of those items, then the operation will fail, and the entire transaction will be ruled back to ensure that the database remains consistent with the predefined schema. 

>	*As I already mentioned, this is the default in relational databases, but would need to be configured in most NoSQL systems.* 


>	Quick Note:
>	The word *consistency* ends up getting a little overloaded here. Previously, we described the *"strong consistency"* property of relational databases, which refers to the idea that all nodes in a distributed system will provide the same up to date data. 

>	It turns out that strong consistency of a database system is a result of compliance with the ACID principles, but a slightly different *concept* than the consistency represented by the C in ACID. ![[Pasted image 20251123164808.png]]

#### Isolation
The next principle is **isolation**, which ensures that when several clients try to execute transactions concurrently, each transaction is executed **independently** in **sequential order**. 

For example, let's say the inventory shows that there are 10 of one item remaining. Suppose that two customers place an order for five of these items at the exact same time each. The isolation principle guarantees that even though the time stamp on these two transactions may be the same, both transactions will happen independently in sequence, so that when the two transactions are completed, inventory level for that item will be zero and not five. 
Similarly, if one customer orders five and another orders 10 of the item at the same time, whichever order gets processed first, we'll go through, and the second one will fail, resulting in a sock level of either five or zero. 


#### Durability
The final principle is **durability**, which guarantees that once a transaction is completed, its effects are *permanent* and will survive any subsequent system failures, such as a power loss. 

This is essential for maintaining the reliability of the database, even when faced with an unexpected event like a natural disaster. 


### Summary
In summary, the ACID principles guarantee that a database will maintain a consistent picture of the world. 

That might sound logical and relatively even straightforward. But in the real world, a database might be **partitioned across multiple servers** because of its size or replicated across multiple data centers for **redundancy and speed**. In these cases, it's especially critical to know that the data you're reading and writing **remains consistent** across the entire network of servers. This is the principle of what's called **strong consistency**, which is a key feature of ACID compliance that holds even for a distributed database system. 

Now, it's important to note that while relational databases are typically ACID compliant, not all databases are required to abide by all of the ACID principles in order to support application back ends. 

Some NoSQL databases only possess some degree of ACID compliance. But relaxing one or more of these constraints, you can improve certain aspects of the databases performance and make it more scalable. As a data engineer, understanding **when** your database needs to be ACID compliant can help you prevent disasters. 

Check [Lab_2_NoSQL](Lab_2_NoSQL.md) and [NoSQL](../NoSQL)'s DynamoDB documentation for more.


## Object Storage

Files are one of the **most common source systems** that you will deal with every day as a data engineer. And you might be receiving or accessing these files from a file system like **GoogleDrive** or an object storage system like **AWS S3**, or simply as an attachment to an **email**. 

While files may come at you from many different places, object storage is arguably the most important mechanism for file storage and retrieval in your work as a data engineer. 

Object Storage treats data, files in this case, as **individual objects** and stores them in a **flat structure** that doesn't adhere to a traditional file system hierarchy. This means that while you might be accustomed to storing files in a hierarchy of folders and subfolders on your local computer, Object Storage has **no hierarchy**.
![[Screenshot 2025-11-29 at 12.58.54.png]]


>	*Now, just as a side note, this flat structure approach can be confusing. If you go into Amazon S3 for example, there is a **Create Folder** button, and you can go ahead and create folders and subfolders to your heart's content and happily store your files in what looks very much like a hierarchical file system. 
>	
>	However, it turns out this is just a feature of the user interface to keep things looking organized in a way that looks familiar. The actual storage mechanism is **flat**, meaning that even though it might look like you have folders and subfolders in the UI, all files are actually stored right at the top level. And this is by design because it allows **quick and straightforward access** to all objects without worrying about the overhead of a folder structure.

So objects can be anything from CSV, JSON, text, video, image, or audio files, to machine-readable binary data. This versatility makes Object Storage the **perfect repository for semi-structured and unstructured data**, which can be useful when supporting applications like serving data for training machine learning models. Object storage plays a crucial role as a **data source**.


### Key Components of Object Storage
In later weeks and courses, you'll see how object storage is also integrated throughout the entire data engineering lifecycle. But for now, let's take a look at some of the **key components of object storage**. 

##### UUIDs
In Object Storage, each object is assigned a **Universal Unique Identifier**, or **UUID**, which is a sort of a **key**. This key is required for accessing and managing the corresponding object. 

Each object also has associated metadata, which is additional information about the object, like the creation date, file type, or owner. It's worth noting that after the initial write, objects technically become **immutable**, and they don't support random write or append operations. In this sense, a file in Object Storage is not like a table in a relational database or a document in a non-relational database that you can update or append to. 

To change the data stored in an object, you must **rewrite the full object** and **have the uuid point to this new object**. 

##### Object Versioning
With object storage, you can enable **object versioning**, which allows you to add metadata to an object to specify its version. So when you update an object, instead of overwriting the old object under the same uuid, you can **keep multiple versions of that object**. 

### So why use object storage? 
- Object Storage allows you to store files of various data formats without a specific file system structure. This removes the complexity associated with hierarchical folder systems and databases. 
- In a cloud environment, object storage can easily scale out to provide virtually **limitless storage** space for massive amounts of data. 
- In terms of availability, the data in **cloud** object storage is typically replicated across **several availability zones**, meaning that data is replicated across multiple physical data centers that are isolated from each other. This makes the data highly durable and available even in the case of natural disasters. For example, Amazon S3 offers 99.9999999% of data durability, which means Object Storage on S3 can withstand concurrent device or data center failures. 
- Also, Object Storage is often **cheaper** than other storage options, especially if you're storing data that you don't need access to on a regular basis. 

Cloud Object Storage is used in many applications and is the underlying storage for newer architecture designs such as data lakes and data lake houses because of its flexibility, high scalability, cost-effectiveness, and durability. 

## Logs

The simplest type of streaming system I can think of is a **log**. In fact, the log isn't even a system at all. It's just a **record of information about events** that can serve to track the activity of a system or an application. 

In the previous course, I mentioned that it used to be common for developers to regard the data coming from software applications **as an exhaust or a byproduct**, not necessarily having any intrinsic value on its own, but useful for monitoring or debugging a system. 

The specific data that is most commonly regarded as exhaust is the data contained in **logs** produced by software applications. When a developer deploys a product or a platform, like a website or mobile app, they'll set it up such that all activity that occurs within the application **is recorded in a log**. The log might include 
- a user activity, like 
	- a user logging in, or 
	- navigating to a particular page. 
- It might also include **a record of events** on the back end, like 
	- an update to a database, or 
	- an error that was generated when trying to run a particular procedure. 

Logs are most commonly used in practice as a means of monitoring the health of systems. Engineers will use logs to trigger alerts or to debug, what went wrong when an error occurs. In this sense, logs can seem boring and the characterization of logs as application exhaust might seem appropriate. 

However, logs are a **rich source of data** that can be useful for much more than just monitoring the health of an application. As such, they can be an important source system. You'll ingest data from in your work as a data engineer. 

***At its core, a log is an append-only sequence of records, ordered by time, capturing information about events that occur in systems.*** 

For example, if you're the data engineer for an e-commerce company, your web server logs can capture detailed user activity data that could be used to support downstream analysis of user behavior patterns. 

Many database systems will have logs that you could use to 
- **track changes** in the database process known as **change data capture** or **CDC**, for short. 
- You could use those changes to **trigger your ingestion processes** so that they run based on the arrival of new data in the database. 
- Or you might ingest log data for use in certain **machine learning applications**, like anomaly detection, if, for example, you're ingesting log data from security systems. 

Logs play a crucial role in tracking what happened in many of the upstream software systems you'll work with. This makes them a rich data source that can support downstream use cases like data analysis, troubleshooting issues, monitoring performance, machine learning applications, and automation.

![[Screenshot 2025-11-29 at 16.53.49.png]]

Log data may be recorded as simple unstructured text or in JSON or CSV format, or even as binary encoded data. In addition to the data describing the time and substance of an event, logs will also often include a tag to categorize the event by assigning what's known as a log level to each record. 

**Log Tags**
Log levels might include **tags** like debug, info, warn, error, or fatal that let you know what kind of information a particular record contains. For example, a record containing basic activity information would be assigned the info log level. Well, a record containing an error message might be assigned the error log level, or if something more serious happens, like major systems have failed and need urgent attention. This might carry the fatal log level as a tag. We'll talk more about log levels later on when you start building logs into your own data pipeline applications, instead of monitoring for your own systems. As a data engineer, it's important that you understand how to work with logs, their types, formats, and applications. Logs will be an important source of data for the work that you do and can help you troubleshoot issues, monitor performance, and serve lots of downstream use cases. 


## Streaming Systems

 In this section, we'll take a closer look at the details of **event-driven architectures** and how **message queues** and **streaming platforms** work as *source systems* for your data pipelines. 
 
 First, let's define some terminology. 
 
 Throughout these courses, so far, we've been talking about streaming data in terms of 
 - events, 
 - messages, and 
 - streams. 

**Event**
*Broadly speaking, an event is just something that happened in the world or a change to the state of the system.* 

For example, a user clicking on a link or a sensor measuring a change in temperature are both examples of events. 

As mentioned before, in some sense, you can think of all data as being streaming data at its source. This is because essentially all data consists of a record of events that happened out in the world or within some system. 

**Message**
A message is a record of information about an event. 

A message might include details about the event, like which button a user clicked or what temperature the sensor recorded, as well as some metadata around the event and a timestamp of when the event happened. 

**Stream**
Messages can be generated continuously to form a stream. 

A stream is a sequence of messages that might be a series of sensor readings or website clicks over a period of time. 

**Streaming Data**
Messages and streams collectively make up streaming data. 

>	If you want to handle chunks of this data all at once, like over a specific time interval, then that would be **batch processing** **applied to a stream of messages**. 

> 	If you want to process each message as it's received, then you need a system that's set up to take **action based on incoming messages**. 
> 	Then what you have is a system where messages record information about events and action is taken as messages are received, or in other words, a streaming system. 
> 	![[Screenshot 2025-11-29 at 17.02.49.png]]	
Now, out in the real world, you'll often hear the words **event** and **message** used almost interchangeably when it comes to describing the various components of an event-driven architecture. 
>
But don't let that worry you. While it's technically accurate to say that events are the things that happen and messages or the information or data recorded about those events, for all intents and purposes in data engineering, it's not important to distinguish between the two. 

When we're talking about events or messages being produced, or consumed, or stored in a queue, it's all the same thing. 

### Components of a Streaming System
There are three components of a streaming system: 
- the event producer, 
- the event consumer, and 
- the event router, also known as the streaming broker that sits between the producer and the consumer. 
> ![[Screenshot 2025-11-29 at 17.04.40.png]]

> 	*Just note that here, I could say message producer, message consumer, and message router, and it would mean the same thing. It's just that you'll often see these things described in terms of events, and so we'll go with that terminology here.* 

**Event Producer**
The event producer is what generates the messages in a stream. The producer could be an IoT device, a mobile app, an API, or a website, to name a few examples. 

**Event Consumer**
The event consumer, sometimes known as the **subscriber**, is what processes each individual message, and **there can be more than one consumer** in any given streaming system. 

**Event Router**
The way that events **find their way** to the correct destination is through the **event router** or **streaming broker**, such as **Apache Kafka**, which acts as a buffer to filter and distribute the events from the producer to the consumer. 

It's this router that helps decouple the producer from the consumer, which enables asynchronous communication between them, so the producer doesn't have to wait for the event to be delivered to the consumer before it can send another one. This also prevents events from being lost even if the consumer is not immediately available. 

##### Event Systems as a source system
When you work with **event systems** as a **source system**, it could be that your upstream source is a simple event producer, like an IoT device, and your system comprises both the event router and consumer. 

![[Screenshot 2025-11-29 at 17.08.44.png]]

Or it could be that your upstream source system is made up of multiple producers, routers, and consumers, and the systems you build are effectively just another downstream consumer of events. 

![[Screenshot 2025-11-29 at 17.09.00.png]]

### Types of Streaming Systems
In your work building data pipelines to process stream data, you'll encounter **two** main types of streaming systems : 
- message queues and 
- streaming platforms. 

I often see these two types of systems confused with one another. While there are many similarities and potential overlaps and how they can work, there's one main difference between them, and that's in **how the event router works**. So I'd like to spend a minute talking about that now. 

**Message Queue**
In a **message queue**, the event router **acts as a queue** that accumulates the messages sent by the producer. The event consumer then reads the messages from the queue in a first-in-first-out order (FIFO). Once the consumer reads the message from the queue and acknowledges this, the message is deleted from the queue. 
![[Screenshot 2025-11-29 at 17.15.13.png]]
![[Screenshot 2025-11-29 at 17.15.51.png]]
![[Screenshot 2025-11-29 at 17.16.28.png]]

With a message queue, the event producer can push new messages to the queue at any time, and the event consumer can read them at any time. 

You can think of the queue itself as a sort of **temporary storage solution** that allows event producers to be decoupled from event consumers. 

An example of a message queue that you might encounter as a data engineer is **Amazon Simple Queue Service** (**SQS**). It's a fully managed message queue that's commonly used for microservices, distributed systems, and serverless applications. 


**Streaming Platforms**
With a streaming platform like **Apache Kafka** or **Amazon Kinesis Data Streams**, the event producer streams events to a log, which, as we looked at in the previous video, is an **append-only record of events**. The event router then distributes messages in the log to appropriate event consumers. The consumer processes messages in the log sequentially as a read-only operation. 

![[Screenshot 2025-11-29 at 17.17.30.png]]
This means that unlike a message queue, the **messages do not get deleted from the log**, and the data is **persistent**. Since the data is retained in this way in a streaming platform, it's possible to replay batch or reprocess events in the log from a past point in time. 

Streaming systems will be among the source systems you'll ingest data from as a data engineer. As you saw in the previous course and as you'll continue to see throughout these courses, you can also build streaming systems into your own data pipelines as part of the ingestion, transformation, and serving stages of the life cycle.


# Interaction with Source Systems

When it comes to *actually* connecting to source systems and your work as a data engineer, it's relatively common to run into unforeseen issues that block you from accessing the data you're interested in. 

These issues can be due to things like 
- having improper identity and access management or IAM definitions, 
- broken networking configurations, or even 
- just having the wrong set of access credentials. 

These might at first sound like relatively trivial issues, but in my own experience, running into these problems happens all the time in data engineering, and they can be **major blockers** if you don't know how to debug and solve them properly. 

- In this section, I'll start by going over some of the details of **how you connect to different source systems.** I'll demonstrate this in AWS. But the principles we'll be looking at also apply to other Cloud platforms. 
- In the context of IAM roles and permissions, we'll look at the importance of **security in the Cloud**, where IAM is the key to controlling and managing access to Cloud based data sources and other components within your data pipelines. 
- Finally, we'll get into **networking**. I'll start you off with a high level overview, and then Morgan will walk you through the details of networking on AWS, including VPCs and subnets, gateways, routing, security groups, and more. 
	![[Screenshot 2025-11-29 at 17.32.21.png]]

## Connecting to Source Systems

Before you can ingest data, you need to first **establish a connection** to your **data source** and **verify that you're authorized to read data from it**. 

You've already had some experience doing this in previous labs. 

For instance, in the **DynamoDB** lab, you used **Boto3**, which is the AWS software development kit or SDK for Python to **create a client connection** to a table within DynamoDB. 

```python
def create_client():
    try:
	    client = boto3.client("dynamodb")
	    return client   
    except ClientError as e:
        error = e.response.get("Error", {})
        logging.error(
            f"Failed to load DynamoDB. Error: {error.get('Message')}"
        )
        response = {}
    return response
```

And you also connected to an **Amazon RDS MySQL instance** by running this command with proper parameter values. 

```Shell
mysql --host=<MySQLEndpoint> --user=<DatabaseUserName> --password=<Password> --port=3306
```

- The *endpoint* and *port* information you see here are what you use to locate the correct database instance. 
- And the *username* and *password* credentials were used to authenticate you as someone with the proper permissions to access the database. 

And so, as you can see, there's more than one way to connect to a database. Or any resource for that matter. So let's take a closer look at this. 

**Using the AWS Management Console**
If a source system is housed in a resource within your organization's AWS account, you can get the connection information from the management console. 

For example, if I'm trying to connect to an **RDS database instance**, I can navigate to the RDS console, locate the database I want to connect to, and find the connection information, including the endpoint and the port number. 

>	*And just as a side note, AWS is always rearranging exactly how things appear in the console. So what I'm showing here might look a little different than the console when you're looking at it.* But this basic set of steps will still be the same. 

And so the console can be pretty convenient for finding information like this or spinning up resources and connections. But keep in mind that doing your work from the console involves you navigating through and clicking on widgets and buttons to get things done. 

If you had to repeat this process in the future, it could be hard to remember exactly what steps you took. And like I said, by the time you want to do this again, AWS may have changed how things are arranged in the console, which could make things even harder. So in general, operating from the console is great to get something done quickly, maybe when you're prototyping something in your system. **But the process is not very repeatable nor traceable.** 


**Using CLI tools**
As a somewhat more programmatic way of finding the information you need and connecting to source systems, you can run code at the command line interface or CLI. 

In this way, 
- you can get the database endpoint, 
- then you can connect to the database using the command syntax specific to the DBMS you're using. 

And so issuing commands directly in the CLI like this is a common practice among data engineers in the connection and ingestion process, but it's still relatively *manual*. So it's typically better for simple workloads rather than complicated ones. 


**SDKs**
To take another step towards **repeatability and automation**, you can connect to a source system using an **SDK** like **boto3**, running code in an IDE or, for example, from a **Jupyter** notebook. 


**API Connectors**
For certain source systems, you can also connect to them through an API connector. For example, you might use 
- a Java Database Connectivity, or JDBC for short, or 
- Open Database Connectivity, or ODBC for short.

Check [connecting-to-source-systems](../../Training/connecting-to-source-systems)