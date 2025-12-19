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

Check [connecting-to-source-systems](connecting-to-source-systems.md) for more detailed instructions on creating and connecting to databases.


## Basics of IAM and Permissions

When you're building data pipelines in a Cloud-based architecture, identity and access management, also known as **IAM**, is central to your role as a data engineer. 

As a data engineer, you're entrusted with sensitive data. Whether that's the personal and private information of your clients or proprietary business information. The owners of that data are trusting that their information is safe in your hands. 

Now, security on the cloud is a vast and complicated subject, but for the purposes of these courses, we're going to focus on the **basics**. And that's because by simply adhering to a basic set of best practices, you'll be able to successfully avoid the vast majority of data disasters. In fact, a study in 2023 found that more than half of all cloud data breaches were caused by simple human error, things like **insecure storage of passwords** or other credentials, or **IAM** **misconfigurations**. 

**What is IAM?**
*IAM is a **framework for managing permissions**. Permissions define **which actions** an identity, like a person or an application, can perform on a specific set of resources, like a database or an ETL tool.*  IAM ensures that **the appropriate set of identities have access to the right resources at the right time**. 

Remember the principle of least privilege we talked about in the previous course? IAM is how you exercise this principle in practice, as it allows you to grant people and applications access to only the essential resources they need to do their jobs, and only for the duration that it's required. 

### AWS IAM
Many cloud providers have built in IAM services that help users manage access and permissions to their cloud resources, for instance, **AWS IAM** is a web service that helps you manage and securely control access to AWS resources in your account. 

As a brand new data engineer, chances are that you won't be the person setting up IAM configurations at a high level across your company's cloud service account. But you will be interacting with various cloud resources, and you may be responsible for configuring IAM for the resources you deploy as part of your data pipelines. And so you need at least a basic understanding of the different IAM components so that you can operate securely and troubleshoot problems as they come up. And so we'll look at these iam components in the context of AWS Iam. But just know that these same types of components are common across other cloud providers like GCP or Azure, they just might go by different names in some cases. 

**AWS IAM Policies**
In AWS IAM, you'll use **policies** that **grant identities permissions for actions** on AWS resources. 

>	**AWS Identities**
>	There are different types of AWS identities. 
>		- There's the **Root User** who created the AWS cloud account and has unrestricted access to all resources in that account. 
>		 - Then, there are **IAM Users** who are given specific permissions to certain resources. As a junior data engineer, it's most likely that you'll be assigned an IAM User account within your company's AWS account. You'll be given a set of **long term credentials**, like a username and password, **or an access key** that you can use to programmatically access AWS resources using code. 
>		- Then there are **IAM groups**, which are **collections of users** that you can attach a policy to. 

A policy is just a **JSON document** that includes the details of what resources and permissions that policy allows. This streamlines the process of provisioning resources. For example, your company might have an **IAM group for data engineers**, where every user in that group has access to the resources needed to build and maintain data pipelines. 

**IAM Roles**
And finally, there are IAM roles which are **not associated with a specific person or application long term**, but are **briefly assumed by a user, application, or service** to grant them **temporary permissions** to perform specified actions on your AWS resources for a limited time. 

For example, by default, an EC two instance does not have permission to read or write to s three storage, but you can create a rule that has read and write permissions to specific s three buckets and let the EC two instance assume this role when needed. This is a much more secure way to give the EC two instance permission to work with s three than, for example, storing your long term user credentials within the EC two configurations. If you ever get an access denied error message when making a request with temporary role credentials, it's good to check whether those credentials have expired. 

IAM Policy Examples: https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html

In the previous labs, we were given specific policies that allowed us to access specific resources while prohibiting us from accessing others:

![[Screenshot 2025-11-30 at 11.16.49.png]]

Check [AWS IAM](AWS%20IAM.md) doc for more info.

## Basics of Networking in the Cloud

When you're building a data pipeline in a cloud based architecture, what you're really building is **a network of connected resources.** And so the way you configure that network plays a key role in ensuring that the data flows properly throughout your data pipeline. 

Networking is really jus**t a collection of connected devices** that can share data and communicate among themselves and over the internet. 

When it comes to networking in the cloud, the basic principles are roughly the same across all major cloud providers. Here I'll go over the principles in the context of AWS, since that's what you're working with in these courses.

The term cloud computing can sound a little abstract, like computation is somehow happening out in the ether. But make no mistake, the cloud and cloud computing is made up of very real **physical data centers** that are spread out around the world. 
- The **AWS cloud** is a global network that is spread across different geographical areas known as **regions**. 
- Each region contains **clusters of availability zones**
- Each availability zone consists of one or more data centers with redundant power, networking, and connectivity. 

> ![[Screenshot 2025-11-30 at 18.00.04.png]]

In many cloud computing applications, data and resources are **replicated across regions** and availability zones to ensure that systems keep working even if one or more data centers were to go down. As a data engineer spinning up new resources on the cloud, you'll need to decide which region to host your resources in. 

**Region considerations:**
When choosing a region for your data to be stored, there are many things to take into account:
- You might need to consider things like **legal compliance**. For example, does storing your data in a specific region mean it needs to adhere to unique data privacy or regulatory requirements? 
- You might also need to consider other factors like **latency**. The closer your end users are to the region where your resources are hosted, the lower the latency.
- and **availability**. The more availability zones your resources are replicated across, the better you will be able to withstand or recover from a disaster. 
- **Cost**. In addition to these considerations, **the cost of resources can vary from one region to the next,** which may be a factor in your decision. 

And so, when you're actually working with resources on the cloud, it can be easy to lose sight of the fact that what you're really doing is interacting with a network of physical devices that are spread out around the globe. But as a data engineer, it's important to understand how this global infrastructure is set up and how it impacts things like latency, cost, reliability, and availability of the systems that you build. 

**Virtual Private Cloud (VPC)**
So moving back to important things you need to know about networking in the cloud. **Within** any given **region**, you can create **custom virtual private clouds, or VPCs,** which are smaller networks that span multiple availability zones within a region. 
![[Screenshot 2025-11-30 at 18.07.08.png]]
- Creating VPCs allows you to have more fine-grained control over who can access what resources. 

You can then divide the space in your VPC into **subnetworks**, or **subnets** for short, that house your actual data pipeline resources. 
![[Screenshot 2025-11-30 at 18.09.01.png]]

Each subnet can then have **its own security rules**, known as a **network access control list**, or **network ACL** for short, as well as **routing configurations** through an internet gateway. 

![[Screenshot 2025-11-30 at 18.09.51.png]]
- This lets you create public subnets for internet-facing resources, like web servers, and private subnets for internal resources, like databases. 

In the real world, things can get complicated very quickly, especially when you start setting up multiple VPCs, subnets, gateways, and routing configurations between resources. And it's in this context that the simple act of connecting to a database depends on multiple layers of networking configurations, not to mention IAM permissions. And so, understanding the details of network configuration is critical when it comes to connecting to your source systems. It's also required for successful orchestration and automation of your data pipelines, which we'll get into in the last week of this course.


## AWS Networking Overview - VPCs & Subnets

**Networking** will be a significant part of your job as a data engineer, and networking can feel overwhelming at first with all there is to know about setting up connections between resources, permissions, security and all the rest. But don't worry, networking in the cloud is something you can become proficient in. It just takes time and understanding a set of core concepts and how you can apply those concepts in building your data systems. 

Through this and the next several sections, I'll go over the details of these important core concepts including 
- **Amazon Virtual Private Clouds**, or **VPCs**, 
- subnets, 
- gateways, 
- route tables, 
- network access control lists (**ACLs**), and
- security groups. 

**Example Scenario**
Let's see how to build out the **VPC** and **networking components** for a scenario where you need to deploy: 
- an **Amazon RDS** database and 
- an **Amazon EC2** instance. 
Let's imagine there is
- a web application running on the EC2 instance 
- that allows you to query the database running on RDS. 

This is a simple scenario for any kind of web application with a relational database on the backend. So the end state will look like this. 

- One **VPC**, with:
	- 2 **public** and 
	- 2 **private** subnets
- **EC2** and **RDS** database instances deployed into the *private* subnets, 
- **NAT gateways** in the *public* subnets, 
- as well as an application **load balancer** to front the web application fronting on the EC2 instances. 
![[Screenshot 2025-11-30 at 18.18.31.png]]

I'll walk through each setup step for this scenario that is related to networking and explain all of these components as we go along. We won't actually deploy the EC2 or RDS instances or the application load balancer, but we will build out all of the networking components needed for it. 
Having this scenario in mind will hopefully make the networking concepts easier to grasp.

### 1. Creating VPC and Subnets
 So to get started, we will need a **VPC** and **subnets** to place the EC2 and RDS instances in. 
![[Screenshot 2025-12-03 at 13.08.35.png]]

Let's go ahead and complete that task:

- From the AWS console homepage, I will go to the search bar and type VPC. ![[Screenshot 2025-12-03 at 13.40.37.png]]
  
- Then I'm going to select VPC. This brings us to the **VPC dashboard** where I can then select the **Create VPC** button. ![[Screenshot 2025-12-03 at 13.41.28.png]]
  
- Now on the create VPC page, I am *only* going to create one VPC. Then we will manually create these subnets next. ![[Screenshot 2025-12-03 at 13.42.20.png]]


	**Default VPC** **- Explained:**
	Before moving ahead though, I want to point out that there is what's called a **"default VPC"** in *each* region. By default, in an AWS account, the default VPC includes:
	-  a **public** subnet in each availability zone in that region, 
	- and an **Internet gateway**
	Using the default VPC can allow you to quickly launch *public Internet facing EC*, two instances without additional setup. This can be convenient for experimenting with launching public resources like a simple website, but **for most real world use cases**, you likely don't actually want directly public facing resources, and instead you want **network protected resources**. 
	
	So with that in mind, your default should not be to use the default VPC for any real work. Instead, you should create **custom vpcs** for your specific use cases, and that's what I'm going to show you how to do here. 


As we've seen before, AWS operates in *regions* around the world, and these regions are made up of *multiple availability zones,* or **AZs** for short. 

**A VPC has the ability to span all of the AZs within the region in which the VPC was created.**
![[Screenshot 2025-12-03 at 13.16.18.png]]

Here were going to create **one VPC**, but a region can have **more than one** VPC in it. For example, you might create different VPCs for different projects, environments, or other organizational or technical considerations. 
![[Screenshot 2025-12-03 at 13.17.06.png]]
- Each VPC is isolated from other VPCs by default. 
- Resources within the same VPC can communicate with each other, but communication between resources from two different vpcs is something you would need to design and configure. 
	![[Screenshot 2025-12-03 at 13.18.16.png]]


When you create a VPC, you need to give it: 
- a name, 
- a private IP address range, 
- and choose the region you want it to be placed into. 
![[Screenshot 2025-12-03 at 13.47.13.png]]

It's useful to give VPCs a descriptive name so you can more easily identify which VPC is which. 

- I will go ahead and name this VPC *project-1*. And you can see here the region im creating this VPC in is **US-East-1**, but I could choose another region from the dropdown if I needed to. ![[Screenshot 2025-12-03 at 13.48.07.png]]

- Next, you need to define the IPv4 **CIDR block**. CIDR stands for "*Classless Inter-Domain Routing*" and it defines the range of private IP addresses that can be used within the VPC or how many private IP addresses are available in the VPC. 
  
  Any resource that gets deployed into this VPC will be assigned a **private** IP address from this range. That being said, if you want to create a resource that is **available via the Internet**, you would also need it to have a **public** IP address. Any public IP address assigned to it would come from a pool of AWS managed public IPs. 
  
  So this CIDR range here is ONLY for private IP addresses. I'm going to type **10.0.0.0/16** for the CIDR. 
  ![[Screenshot 2025-12-03 at 13.50.35.png]]

And I'd like to pause now and go over this CIDR notation because it can seem complicated if you're not used to seeing it. I'm going to try to simplify it to make it easier to grasp if this is your first time getting into the details of networking. 

![[Screenshot 2025-12-03 at 13.28.13.png]]

- IP addresses have **four numbers separated by dots**. 
- Each number can range from 0 to 255. In other words, each number in the address is an **eight bit integer** value. 
  In the case of this example, 10.0.0.0/16, the 10.0.0.0 is the starting address of our network. The 16 part is a **prefix length** which tells you how many bits are used for the **network part** of the address. So in this case, the 16 tells you that **16 bits**, or in other words, the first two of these eight bit integers will be the network prefix. 
  ![[Screenshot 2025-12-03 at 13.25.38.png]]

- In binary form an IPV4 address consists of 32 bits, which again, just means that each number between the dots in the address represents 8 bits, with 4 numbers of 8 bits each. So as I said, the 16 means that the first 16 bits or the first two numbers are **fixed** and define the network, while the remaining 16 bits or the other two numbers can vary and are used for host addresses within that network. 
- That means that any resource deployed into this network would have a private IP address that starts with **10.0**. Then the other two numbers could be anything between 0 and 255. ![[Screenshot 2025-12-03 at 13.28.56.png]]
- If I wrote /24 instead of /16, that would mean that the first *three* numbers would be fixed and only the last number would be able to be used to assign host addresses. 
- You'll see why you need to know this in the next step when we create subnets.

So now back in the AWS console I have defined the different pieces I need to create a VPC and I can select **create VPC** . ![[Screenshot 2025-12-03 at 13.53.44.png]]From here I need to create the subnets. 

**Subnets**
Subnets **are sub networks within the VPC**, meaning, **smaller divisions** of the private IP space for the VPC that you can use to **group resources** based on their network access and security requirements. 
> 	*Later, we'll use network access control lists (ACLs) and security groups to control what types of network traffic can come in and out of each subnet.* 

- Each subnet is associated with a **specific AZ**, meaning that when you create a subnet, you MUST specify which AZ it resides in. 
- By strategically placing your resources in *different subnets across multiple AZs*, you can enhance the redundancy and availability of your applications. 
- It's common to create at least: 
	- one private, and 
	- one public subnet 
	*per* AZ that you intend to use.
	
Here. I would like to deploy the EC2 instances and RDS databases into *private* subnets so that they aren't exposed to the Internet. And for **redundancy** I plan to create two public subnets and two private subnets across two azs in this VPC, which is a common pattern to follow. That way, for example, if the primary database instance experiences degradation, or if the AZ itself has temporary availability issues, you have all of your data and an instance running in another AZ that can absorb the traffic after failover occurs. 
![[Screenshot 2025-12-03 at 13.40.19.png]]

So, to create these subnets I will select Subnets in the navigation and then select Create subnet. 
![[Screenshot 2025-12-03 at 13.57.05.png]]
![[Screenshot 2025-12-03 at 13.56.39.png]]

Now I need to select which VPC I want to create the subnets in. I will select the dropdown and choose the project-1 VPC. 
![[Screenshot 2025-12-03 at 13.57.28.png]]

From this page I can create all four subnets. 
- For the first subnet I will name it public subnet one and then select a specific AZ. So later I can ensure I am deploying additional subnets to different AZs. 

I will select us east one a for this one. ![[Screenshot 2025-12-03 at 13.57.54.png]]

Then I need to give it a CIDR range and subnets need to have an IP range that is a subset of the IP range of the VPC. The VPC is defined to use 10.0.0.0/16 so I will make the range for this first subnet 10.0.1.0/24. 
![[Screenshot 2025-12-03 at 13.58.28.png]]

This means that any resource deployed into the subnet will be assigned an IP address that starts with 10.0.1. 

Then the last number will be assigned to identify the specific host. Now to create my first private subnet I will select add new subnet and then repeat the same steps. But this time, I will name the subnet private-subnet-1 and give it a CIDR of 10.0.2.0/24. 

Now let's do it two more times for the other public subnet and private subnet for the other AZ which I will use us-east-1b for. 

So the next thing to do is to create the public-subnet-2 which has a CIDR of 10.0.3.0/24 and then private-subnet-2 which has a CIDR of 10.0.4.0/24. >> Then I can finally select Create which will create all four subnets. 

All right, you now have a VPC with two public and two private subnets ready to use and it looks like this. 
![[Screenshot 2025-12-03 at 13.34.41.png]]

The way this sits right now, anything deployed into this VPC would not have access to the Internet. 
![[Screenshot 2025-12-03 at 13.34.56.png]]

You can add the EC2 and RDS instances to this diagram to show them sitting in the private subnet. Neither of these resources would be accessible from the Internet and they wouldn't be able to initiate connections with any resource on the Internet either. 
![[Screenshot 2025-12-03 at 13.35.08.png]]

So at this point, it's a closed network and you will need to deploy and configure some more resources to make Internet connectivity possible for anything in this VPC. ![[Screenshot 2025-12-03 at 13.35.35.png]]

Up next, we will look at Internet connectivity and how Internet and NAT gateways work.



### AWS Networking - Internet Gateway & NAT Gateway

At the end of the last video, we had created a VPC with two public subnets and two private subnets. 

In our current configuration state, if you deployed any resource like an EC2 Instance into one of the public subnets,
- it would NOT be accessible via the Internet, 
- and it would NOT be able to establish connections to other resources on the Internet either. 
![[Screenshot 2025-12-03 at 13.35.08.png]]

That is because VPCs and subnets alone create an isolated network. No traffic in or out. In this section, we will discuss how to make Internet connectivity possible using 
- **Internet gateways** and 
- Network Address Translation, or **NAT gateways**. 


Now, if you recall the scenario we are following, you would have an EC2 Instance and an RDS database in your VPC. As a best practice, both the application running on the EC2 Instance and the RDS database should be in **private subnets** and wouldn't need direct connectivity with the Internet. 

However, I want to call out *two considerations* we didn't discuss before. 
1. The applications running on the EC2, DO need to occasionally **download updates from resources on the Internet** for things like application upgrades and patching. 
2. You would still need a way to **submit requests to the application** running on the EC2 Instance through the load balancer so that you can query your data on RDS. 

![[Screenshot 2025-12-03 at 15.13.41.png]]

Both of these considerations mean that your VPC does, in fact, need Internet connectivity. 


To better understand what an Internet gateway is and what it does, I want you to think of your VPC in its current state like a **house without a door.** If you build a house around you without a door, you wouldn't be able to leave the house, and no one can enter the house from the outside. 

If you were in the house, you could move freely from room to room. But in order to get out, you would need to **install a door to the outside world**. So that's what we have so far here, a house without a door. And so what we will be doing next with our VPC is to install a door to the Internet. Or in other words, we will attach an **Internet gateway** to it. ![[Screenshot 2025-12-03 at 15.17.28.png]]
- Internet gateways allow resources in your *public* subnets to connect with the Internet. 
- They support both *inbound* and *outbound* traffic. 

> 	*Creating an Internet gateway and attaching it to the VPC is j**ust one step** in allowing Internet traffic to flow to and from the public subnets. You will also need to configure **routes** in the **route tables** and configure **network security rules**, and we will do those in the next couple videos.* 


Now, I mentioned earlier that our EC2 Instance will be in *private* subnets. And here I am saying that we will be attaching an Internet gateway to allow traffic to flow to and from the *public* subnets. How will this help if our resources are in the private subnets? Well, let's recall our two considerations from earlier. 

- First, the EC2 Instance needs to be able to download updates from resources on the Internet. This means that our EC2 Instance in the private subnet needs to be able to make outgoing connections from the VPC. 
- And the second, you need to be able to **submit requests** to the application *from* the Internet. 

Let's talk about how **NAT gateways** and an **application load balancer** can help us meet these requirements. 


**NAT Gateway**
The NAT and NAT gateway stands for Network Address Translation, and this is a service that: 
-  **allows resources in a *private* subnet to connect to the Internet or other AWS services** but 
- **prevents the Internet from initiating connections with those resources**. 

Think of it as a **controlled doorway** that only allows *outgoing* traffic and protects the resources inside that are initiating that traffic. With a NAT gateway in place, your EC2 Instances in the private subnet can **download updates and patches** from the Internet without exposing them directly to the public Internet. 
![[Screenshot 2025-12-03 at 15.21.36.png]]


**Application Load Balancer (ALB)**
Next, we need to address the second consideration, **allowing external users to submit requests** to our application. This is where the Application Load Balancer, or ALB comes in. The ALB distributes incoming application traffic across multiple back end targets, like our EC2 Instances, which are hosted across two availability zones. 

The ALB serves as the **entry point for external users**. Handling the load and ensuring the application remains responsive and available while also allowing us to keep those EC2 Instances private. 

![[Screenshot 2025-12-03 at 15.22.23.png]]
>	*We won't actually be building this part since I am focusing on the networking aspect of this architecture for these lessons. However, it's good to know when to use an ALB to allow for application connectivity without directly exposing your back end EC2 Instances. 



So now let's create and attach an Internet gateway and deploy two NAT gateways to the VPC we built in the previous section. Here I am in the AWS Management Console on the Home page. And I will type in VPC into the Search bar. Then from the VPC dashboard, I will select Internet gateways from the Navigation panel. ![[Screenshot 2025-12-03 at 15.24.16.png]]
From the Internet gateways page, I will select the Create Internet gateway button. ![[Screenshot 2025-12-03 at 15.25.03.png]]On the next page, I can give the Internet gateway a name. I'll name it Project 1 Gateway, and then select Create Internet Gateway. 
![[Screenshot 2025-12-03 at 15.25.31.png]]
So now we have an Internet gateway, but we need to attach it to the VPC. 

> 	*Just as a note, **one VPC can have one Internet gateway, and an Internet gateway can only be attached to one VPC at a time.*** This is a one-to-one relationship. 

From here, I'm going to select actions, then attach to VPC. On the next screen from the list of VPCs, I can select the Project 1 VPC, and then select attach Internet gateway. Now, the state of this gateway is attached. So we have installed the front door to this VPC. 

![[Screenshot 2025-12-03 at 15.26.05.png]]
![[Screenshot 2025-12-03 at 15.26.35.png]]
![[Screenshot 2025-12-03 at 15.26.55.png]]

The next step is to create the **NAT gateway**. It's a best practice to create **one NAT gateway in every AZ that you are operating in.** So I will actually create two NAT gateways and place them in each public subnet. To do this from the navigation panel, I will select NAT gateways then create NAT gateway. From this screen, you'll configure the NAT gateway. So first, I'll give it a name. I'll call this one NAT Gateway 1, and then select Public subnet 1 from the dropdown. 
![[Screenshot 2025-12-03 at 15.27.12.png]]
Then this needs to have an **elastic IP address** configured, which will provide a static IP address, and I will select Allocate Elastic IP to do this. ![[Screenshot 2025-12-03 at 15.29.50.png]]

Then I will select Create NAT Gateway. Then I will repeat the steps, but this time, place the NAT gateway into the other public subnet. I'll name this one NAT Gateway 2, and then select Public subnet 2 from the drop down. Create the Elastic IP address, and then finally, create the gateway. 

At this point, this is what our network looks like with the EC2 Instances, RDS database, and ALB included. ![[Screenshot 2025-12-03 at 15.30.27.png]]
There are a few more steps that we need to walk through before inbound and outbound Internet connectivity is working. 

In the next steps, we will walk through the process of setting up the necessary elements, which will include configuring **route tables** and defining **security rules** to secure our VPC. By the end of the series of videos, we'll have a VPC with both secure Internet connectivity and robust access control.

### AWS Networking - Route Tables

The next basic component of networking on AWS you need to learn about is **route tables**. In the last video, we ended with an architecture diagram like this. 
![[Screenshot 2025-12-03 at 15.41.20.png]] 
- There is a VPC 
	- with two public and 
	- two private subnets across two AZs 
- and an Internet gateway attached to the VPC, 
- with EC2 instances and RDS database instances in the private subnets, 
- NAT gateways deployed in the public subnets in each AZ, 
- and a public application load balancer, which *fronts* the private EC2 instances. 


The next step to enable Internet connectivity is to **configure the route tables** for the **subnets**. 
- Route tables are essential for **directing network traffic within your VPC**. 
- Each subnet can be associated with a route table, which contains a **set of rules or routes** that determine where network traffic is directed. 

**Default Route Table**
When you create a VPC, AWS automatically creates a *default* route table. 
This default route table allows internal communication within the VPC, meaning resources in different subnets can communicate with each other. However, it does not include routes for Internet connectivity. 

This is where you need to customize your route tables to meet your specific requirements. 

Without these routes, your subnets won't know how to direct traffic either to the Internet or within the VPC itself. ![[Screenshot 2025-12-03 at 15.49.06.png]]

- For *public* subnets, you'll configure the route table to direct ALL Internet-bound traffic to the **Internet gateway**. This allows resources in the public subnet to access the Internet. ![[Screenshot 2025-12-03 at 15.49.40.png]]

- For *private* subnets, you'll direct Internet-bound traffic to the **NAT gateway** in the public subnet. This allows resources in the private subnet to make outbound connections to the Internet while preventing inbound connections from the Internet. ![[Screenshot 2025-12-03 at 15.50.31.png]]

Let's hop into the AWS Management Console and build out these route tables and routes. First, I will create and associate the route tables. Then we will actually go back in and create the actual routes. 

Starting from the VPC dashboard, I will select route tables from the Navigation panel and then select Create route table.
![[Screenshot 2025-12-03 at 15.51.50.png]]
![[Screenshot 2025-12-03 at 15.52.31.png]]
![[Screenshot 2025-12-03 at 15.52.50.png]]

I'll create **one route table per subnet** in our VPC. 
- The first one I will name public-route-table-1. 
- Then select Project-1 VPC, and then select Create route table. 
![[Screenshot 2025-12-03 at 15.53.43.png]]

Then I can associate this one with the first public subnet by selecting: 

actions > Edit subnet associations
![[Screenshot 2025-12-03 at 15.54.09.png]]

selecting public-subnet-1 > and then save Associations. 
![[Screenshot 2025-12-03 at 15.54.55.png]]


Next, we will do the same for the other three subnets. Select Route tables, Create route table, name the second one public-route-table-2. Select the VPC, and create. Then associate this route table with public-subnet-2. 
![[Screenshot 2025-12-03 at 15.56.44.png]]


***Creating private routes through AWS Console***
Now it's time to create the private route tables. I can name this one private-route-table1. Select the VPC and create. Then associate this route table with private-subnet-1. Finally, to create the last one, I'll name it private-route-table-2, select the VPC and create. Then associate this route table with private-subnet-2. Now that we have the route tables, it's time to create the routes. 

For this step, we will want to create routes for the traffic that will flow between the public subnets and the Internet gateway and routes for the private subnets that will flow to the NAT gateway. 

From the route tables dashboard, I will select public-route-table-1, > Edit routes. ![[Screenshot 2025-12-03 at 15.58.14.png]]
- There is already one route in the table, and this route is a default route added to every route table that you create for communication within the VPC. 

To add the route to allow Internet traffic to be directed to the Internet gateway, I will select Add route. Then for the destination, this will be 0.0.0.0/0. If you remember the lesson about CIDR notation, this has a slash zero prefix, which means it can match any IP address because zero bits are fixed. This represents any IP address or in other words, the entire Internet. 

The destination is 0.0.0.0/0 or any IP address. ![[Screenshot 2025-12-03 at 15.58.53.png]]The route we will want to direct traffic to is the Internet gateway. 

Under target, I will select the dropdown and then select the Internet gateway attached to this VPC we created in the last video.![[Screenshot 2025-12-03 at 15.59.39.png]]
- I'll set up this route along with other network and configurations in the next video to allow instances in our public subnet to send and receive traffic from the Internet via the Internet gateway. 
For now, I will select Save changes and then navigate back to the route tables page. 


Let's move on to configuring the private subnets route table. I'll select a route table associated with one of our private subnets and add a new route. 

Again, the destination will be 0.0.0.0/0, but this time, the target will be the NAT gateway that we deployed to the public subnet. This ensures that any Internet-bound traffic from our private subnet instances is routed through the NAT gateway, allowing these instances to access the Internet for updates and patches while remaining isolated from direct inbound Internet traffic. ![[Screenshot 2025-12-03 at 16.00.41.png]]

To complete this, you would do the same steps for the other public and private subnets. 


Now, our route tables are configured to handle both internal and external traffic as required. The public subnets have routes directing Internet-bound traffic to the Internet gateway, enabling instances within these subnets to communicate with the Internet. The private subnets have routes directing Internet-bound traffic to the NAT gateway, allowing instances within these subnets to make outbound connections to the Internet securely. 

In the next section, we'll cover additional networking configurations, such as security groups and network ACLs. These configurations will further secure our VPC by defining the inbound and outbound traffic rules for instances and subnets.



### AWS Networking - Network ACLs & Security Groups

As a reminder, in the previous section we left off having configured the route tables for these subnets which will direct traffic for the VPC here, well look at a few other networking configurations you're going to need to know about. In our scenario, the application load balancer will send Internet traffic to the EC two instances which would then create a connection with the RDS database instances for queries. ![[Screenshot 2025-12-03 at 16.03.47.png]]

You can configure **networking rules** to allow only the network traffic you want to reach these instances. By default, *no traffic* is allowed to reach these instances even with the route tables in place. (Principle of least privilege)
- Routes are building the actual "roads" that lead to the resources, but these roads are guarded.
- Rules are the "passes" we give to resources to be allowed to pass through that road that leads to these resources.

To change that, you first need to understand 
- security groups![[Screenshot 2025-12-03 at 16.16.58.png]]

- and network access control lists, aka network **ACLs**. ![[Screenshot 2025-12-03 at 16.17.32.png]]


**Security Groups**
Let's start with security groups. Think of security groups as **instance level virtual firewalls** controlling both *inbound* and *outbound* traffic. 

By default, security groups **deny** all inbound traffic and **allow** all outbound traffic. 
![[Screenshot 2025-12-03 at 16.19.25.png]]

So, you need to define inbound rules which determine 
- ***what* types of traffic you want to allow** and 
- **where** you want to allow that traffic to come from. 

Security groups are also **stateful**, which means that *if* you allow inbound traffic to an instance, the return traffic is automatically allowed even if there are no outbound rules explicitly permitting it. For instance, if you permit an inbound HTTP traffic on Port 80, the responses to those HTTP requests are allowed to flow out without needing an explicit outbound rule. ![[Screenshot 2025-12-03 at 16.21.39.png]]
- This stateful nature simplifies security group management because you don't have to create matching outbound rules for every inbound rule. 

Resources that are placed in VPCs use security groups, like EC2 instances, load balancers, and RDS database instances. All of them can use security groups with different rules, and the rules for security groups **can reference other security groups**. 


**Security Group Chaining**

In our example, we have: 
- an application load balancer (ALB) that would need to accept traffic for HTTP on port 80 and HTTPs on port 443 from the Internet. 

- Then we have an EC2 instance hosting our web server. So you'll need to allow HTTP and HTTPS traffic coming *from* the application load balancer. To do that, you could **reference** the security group used by the load balancer. 

- We also have **RDS database instances** which would need to allow TCP on Port 3306, which is commonly used by databases like MySQL coming from the security group used by the EC2 instances. 

This is called security **group chaining**. 
![[Screenshot 2025-12-03 at 16.25.30.png]]

---


Now let's walk through an example of creating a security group that could be used by the application load balancer. 

To set up a security group, navigate to the VPC dashboard, and then select security groups. Then select create security group. 
![[Screenshot 2025-12-03 at 17.21.42.png]]

- First you give it a name like `alb-sg` 
- Then we want to select *which* VPC this security group belongs to. Because security groups are associated with one VPC, we will select the `project-1` VPC. ![[Screenshot 2025-12-03 at 17.23.56.png]]
- Next, we need to define the *inbound* rules. 
	- I'll select Add rule. 
	- Then for the type of traffic, I will select HTTP, which will auto populate the port range to be 80. 
	- Then I will select 0.0.0.0/0 for the source which will allow traffic from the Internet on Port 80. ![[Screenshot 2025-12-03 at 17.24.22.png]]
	- Then I will add another rule for HTTPs which uses port 443 and will allow traffic from the Internet. 

- Then I will select create security group. 

This security group can then be associated with the load balancer. 

**Network ACLs**
Now let's move on to network ACLs which provide an additional layer of security ***at the subnet level***. 

- Unlike security groups which are stateful, network ACLs are stateless. 
	- This means you need to define *both* inbound and outbound rules explicitly. 
- They offer more *granular* control over traffic and can be particularly useful for implementing security policies at the **subnet level**. 
- By default, network ACLs allow all inbound and outbound traffic, but you can modify these rules to meet your specific security requirements. 

We don't need to change this behavior for our simple use case, but it's still important to know as **it's one of the main ways to control network traffic on AWS** and you may need to tweak these rules if you are troubleshooting a network issue. 

So this is what our diagram looks like: ![[Screenshot 2025-12-03 at 17.27.59.png]]

Now, accounting for the places where we could apply security groups and network ACLs. 

In the upcoming lab, you will need to troubleshoot connectivity issues with a database, which is a very common scenario you will run into as a data engineer. 


##### **AWS Networking - Summary**
Resource: 
- First, we covered the basics of **VPCs** and **subnets**, which give you a way to define **a private isolated network on AWS**. 

- We discussed the importance of correctly **configuring route tables** to direct traffic within the VPC and to the Internet. 

- You learned how to set up **public subnets** with routes pointing to the Internet gateway, allowing resources within these subnets to access the Internet. 

- Similarly, we covered configuring **private subnets** with **routes** pointing to the **NAT Gateway**, enabling instances to initiate outbound connections securely. 
	- Notice how we call the subnets public subnets or private subnets, *but it's actually the route table that determines that access*. 
	- There is nothing magic about creating a public or private subnet. It's all about correctly configuring and managing the route tables. 

- Next, we explored security groups, which act as virtual firewalls. At the instance level. They control both inbound and outbound traffic, and their stateful nature means that if you allow inbound traffic, the return traffic is automatically permitted. It's most common to configure inbound rules **only on security groups**, as they by default, allow all outbound traffic. 

- We then moved on to network ACLs, which provide an additional layer of security at the subnet level.
	- Unlike security groups, network ACLs are *stateless*, requiring explicit rules for both inbound and outbound traffic. 
	- This allows for more granular control and is useful for implementing specific security requirements. 

So if you encounter connectivity issues, 
1. Start by verifying your VPC has an **Internet gateway properly attached**, 
2. that the **route tables have appropriate rules** to direct traffic correctly, and 
3. that the **route table associations with the subnets are configured correctly**. 
4. Next, **check security groups** to make sure they have the needed rules in place, and 
5. **review network ACLs** to confirm they allow the necessary traffic. Also, 
6. double check **instance configurations** to ensure they are **associated with the correct security groups and subnets**. 

# Summary

Key learnings (concepts) of this week, in no particular order:

- Understanding how source systems work.![[Screenshot 2025-12-05 at 14.13.18.png]]

- How to connect to these data sources
- Troubleshooting AWS
- CRUD operations
- ACID compliance
- DBMS / RDBMS
- Data Normalization
- NoSQL / SQL basics
- Data integrity / data redundancy
- AWS Networking Basics (VPS, AZ, Subnets)
- IAM concepts (policies, users, roles, groups)
- structured / semi / un structured data
- Connecting to a database
- Streaming VS Batch processing

# Resources

- Chapter 5 and Appendix B of [Fundamentals of Data Engineering](https://go.redpanda.com/fundamentals-of-data-engineering)