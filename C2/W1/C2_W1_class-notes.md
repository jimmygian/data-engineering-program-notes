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

> 	*You'll learn how quarries are executed behind the scenes in the next course. But for now, just know that it can take a lot of processing resources to retrieve all data from all the columns, especially if your dataset is very large.* 


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