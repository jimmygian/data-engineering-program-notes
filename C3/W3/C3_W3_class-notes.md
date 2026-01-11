In previous weeks, you learned how data is stored in storage systems such as databases and optic storage, and how storage abstractions add additional management layers on top of storage systems. 

This week, we're going to look at how the way data is stored and managed has a direct impact on the speed of data retrieval, meaning how ***fast*** you can query the data, as well as how queries can impact the *performance* of the storage systems themselves. 

In your work as a data engineer, you'll be writing queries to extract data from internal and external systems, as well as setting up data storage solutions that your stakeholders can query directly. You need to understand how your data storage and management choices can impact query speed and system performance. To do that, you need to understand **how queries actually work in detail.** 

>	*A query is a statement that you write in a specific query language to retrieve data or act on data.* 

For instance, in the previous course, you wrote SQL statements to interact with a relational database management system or RDBMS. But queries are not only limited to tabular data. In another lab in that course, you use SQL like statements to retrieve data from Amazon object storage. In the first week of this course, you use Cypher to query relationships and node properties from a Neo4j graph database. 

Query languages are declarative languages, meaning that when you write your queries, you only have to describe to the DBMS what data you want to retrieve or what you want to do with the data without worrying about the exact steps that are needed to execute your query. These details are abstracted from you and handled by the DBMS. 

So with the DBMS handling the details, it might be tempting to think that you don't have to understand exactly how queries are processed behind the scenes. But if you don't really understand how queries work, one day, you might end up running a query that brings down a critical database for a few days or more. Believe me, that's no good. Even if you're already good at writing efficient queries yourself, understanding how queries are processed can help you model the data so that it's easier and faster to retrieve by your stakeholders. 

This week: 
- We'll explore **the journey** a query takes to be executed from the moment you write a query to when it's parsed through the creation and implementation of an execution plan to finally when the results are returned or the desired action is executed. ![[Screenshot 2026-01-11 at 09.39.30.png]]

- Then, we'll cover techniques you can use to **improve SQL query performance**, like creating a **database index** that can help optimize a search for specific records in a database. We'll be focusing on SQL this week because it's an extremely popular and well established language. Many of the techniques that we'll cover are applicable to other query languages, as well. 

- We'll also look at **aggregations**, revisit our discussion for row versus column storage, 
- and discuss **queries on streaming data**. 

In the labs this week, you'll get hands-on experience with advanced SQL statements, compare the execution time of an analytical query performed on a row storage versus columnar storage, and finally, use Amazon Managed Service for Apache Flink to perform time-based windowed queries on streaming data. Join me in the next video to examine the life of a query.

## The Life of a Query

While running a query might seem like a simple task where you write code, run it, and get the resultsm a lot's going on behind the scenes. Multiple components inside your database management system work together to translate your query into a set of detailed action steps. Even the simplest select query statement goes through these stages in order to be executed. Let's go through the life of a query so you can see how it's executed in a database. 

Here's a typical architecture for a database management system. 
![[Screenshot 2026-01-11 at 09.42.43.png]]
>	*In Week 1 of this course, we discussed the details of the storage engine. Here, I'll be focusing on the other components.* 

### Query Processor
When you send your query to the database, your request arrives through the **"transport system"**, which hands a query over to the **"Query Processor."** 
The processor has two main components, 
- the ***Query Parser***, 
- and the ***Query Optimizer***. 

##### Query Parser

The Query Parser:
- Breaks down the query into **query tokens**, which are the basic building blocks of the SQL query, including *keywords* like `SELECT` and from, `FROM`and *attribute names, operators,* and so on. 
- Checks for **proper syntax** 
- **Validates the query** by ensuring that all table and attribute names referenced in the query *actually* exist in the database.
- It also performs **Control Checks** to ensure that you or whoever ran the query have the appropriate access to these attributes. ![[Screenshot 2026-01-11 at 09.49.13.png]]
- Lastly, the Query Parser **converts** the SQL code into ***byte code***, which expresses the steps needed to execute the query in an efficient machine readable format. ![[Screenshot 2026-01-11 at 09.49.47.png]]
- The byte code is then passed to the **Query Optimizer.**

#### Query Optimizer
The **Query Opitmizer** *analyzes* the query and *devises* an **execution plan** to retrieve the results from the storage layer. 

Since a query can be executed in multiple ways, the query optimizer attempts to find a suitable strategy that uses available resources **as efficiently as possible**. To do this, the Query Optimizer: 
- **Generates various execution plans** based on factors like 
	- the types of operations required, 
	- the presence of indexes, 
	- and the data scan size. 
- Calculates a **cost value** for each plan, which can include several components such as 
	- the IO costs for transferring data from disk to memory, 
	- as well as a computation and memory usage cost. 
- In the end, it picks **the least expensive plan**.

Query optimizers are fundamental to your query performing as well as it can. 

#### Execution Engine

Once the execution plan is created, the execution engine carries out the sequence of operations outlined in the plan and produces a query results. 
![[Screenshot 2026-01-11 at 10.01.17.png]]

While all the details of executing a query are generally abstracted from you, you can **access** the **execution plan** of any query statement to understand its performance before it's executed or troubleshoot the cause of a slow query after it's executed. 

For instance, with relational databases, you can add the `EXPLAIN` command before your SQL statement to display the sequence of steps the database will take to execute the query. 

This will also show you the various **resource consumption** and **performance statistics** in each query stage. 

Let's take a look at an example: 
![[Screenshot 2026-01-11 at 10.03.42.png]]
>Here's the customer table of the DVD rental database that you saw in a lab in the previous course. 

Let's say that I sort this data in a *PostgreSQL* database, and I want to select all records from the customer table. 

```PostgreSQL
SELECT * FROM customer;
```

I can add the `EXPLAIN` command in front of this simple select statement to **fetch the execution plan** created by the postgress query optimizer. 

```PostgreSQL
EXPLAIN SELECT * FROM customer;
```

```shell
QUERY PLAN
_____________________________
Seq Scan on costumer (cost=0.00..14.99 rows=599 width=70)
```

The **return plan** specifies  the following: 
- It's going to perform a **Seq Scan**, or sequential scan, meaning a full table scan. 
- It also shows **two cost values** (0.00 and 14.99). 
	- The first is the *start up cost* for any processing needed **before** outputting the results 
	- and the second is a **total cost** for the execution to retrieve the query result. 
	- Additionally, the plan also outputs the **estimated number of rows** that will be returned and the **expected row width** and **bytes**. 

In this example, the full table scan will have a start up cost of zero *cost units*, but a total cost of 14.99 cost units to return 599 rows. 

Let's say you want to run this select query again, but this time, you decide to use this were clause to filter the records in the queries so that only records with `customer_id=3 `will be returned. 

```PostgreSQL
EXPLAIN SELECT * FROM customer WHERE customer_id = 3;
```

```shell
QUERY PLAN
_____________________________
Index Scan using customer_pkey on costumer
(cost=0.28..8.29 rows=1 width=70)
```

The return plan now specifies that it's going to use the **Index** on the customer ID column to look for the specified row that matches the `WHERE` condition instead of scanning the entire table. 

Recall from Week 1 that: 

>	An index is a *special data structure* that you create as a way to keep some metadata on the side to help locate the data you want more efficiently. 

You'll notice that with this index based strategy, the total estimated cost is less than the cost of the full table scan. 

We'll get into the details of the database index in another video. 

You can use this `EXPLAIN` feature whenever you want to understand your query's performance. This feature is not exclusive to relational databases, you can also use it with NoSQL databases as well as data warehouses or Data lake Houses. 

>	*Check the [[SQL]] training document for other Advanced SQL queries that will be used in the Lab this week.*



