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





## Index Deep Dive

Back in week one, you saw that with relational databases, you can **create an index** to help certain **queries run faster**. In fact, when the **query optimizer** analyzes a query, it considers 
- if an index is present 
- and whether using an index based plan can reduce the query cost. 

So in this section, we'll dive a bit deeper into the details of the index structure. As a data engineer, understanding how indexes are implemented can help you **design better index structures** and **optimize your queries** to improve SQL performance. 

>	*An index is a **separate data structure** that has **its own disk space** and contains information that refers back to the actual table.* 

It stores the data from one or more columns **in a well defined order**. So, just as you would use an alphabetical index at the end of a book to quickly find pages related to specific topics, instead of searching through the entire book, a database management system **can query data by searching an ordered index rather than scanning the whole table.** And in this way, the results are returned to you much faster. 

![[Pasted image 20260123143543.png]]


Revisiting the example from week one, you can see here that the entries from the country column are stored alphabetically in an index structure. When you want to query the orders that took place in a certain country, say you want to select all records from the order table where the country is USA, the database can perform a binary search over the index to locate the rows in the actual order table that have "USA" in the country column. 

![[Screenshot 2026-01-23 at 14.37.11.png]]

#### **But how is the index actually implemented?** 

In the example above, I showed the indexed entries in a table as a way to explain the concept of an index. However, t**he data in an index is not actually stored sequentially as a table.** It's instead **divided into "blocks**" that are **doubly linked together** to enable forward and backward reading from any block. 

![[Pasted image 20260123155538.png]]

- The data stored within each block is **sorted**, 
- then the index blocks are linked together in a way that maintains the logical order of the entire index. 
- Moreover, the location of the blocks doesn't matter because they are properly linked together. 

This structure facilitates the update of the index when new data is inserted or old data is deleted. 


**Example**

For example, let's say: an order from Thailand was added to the above table. Since Thailand is alphabetically between Mexico and USA, you would add it *in the third index block* you see here and shift the rest of the data accordingly. 

![[Screenshot 2026-01-23 at 16.01.28.png]]
![[Pasted image 20260123160158.png]]

And if that record were to be deleted, then you would remove the corresponding country from its index block. This whole time, the index blocks remain doubly linked. 


#### Balanced Search Tree (B Tree)

Now, in order to efficiently locate these index blocks, another structure known as the ***"balanced search tree"***, or **"B tree"**, is built on top of the index blocks. 

The index blocks represent what is known as the leaf nodes for the tree. 
![[Screenshot 2026-01-23 at 16.04.03.png]]

Then *building up* from these leaf nodes, you have **"internal nodes"**, also known as **"branch nodes"**, that act as *parent nodes* for groups of leaf nodes. 
![[Screenshot 2026-01-23 at 16.04.48.png]]


So for example, the \[Canada, Mexico, USA\] internal node is the parent node of these three leaf nodes we were just discussing. The **internal node** stores three entries, Canada, Mexico and USA, which represent **the last country from each leaf node sorted in alphabetical order**. 

![[Pasted image 20260123160604.png]]


- This way, if the database is looking for a country whose name is between Canada and Mexico, it will traverse down to the second leaf node. ![[Screenshot 2026-01-23 at 16.08.37.png]]

- If the name is between Mexico and USA, it will traverse down to the third leaf node. ![[Screenshot 2026-01-23 at 16.09.05.png]]

This pattern of how nodes are grouped and linked **is repeated up the tree** until it reaches the first node or root node of the tree. 

Full example:

![[Screenshot 2026-01-23 at 16.11.12.png]]

Let's say you want to query orders that were placed in Canada. 

```MySQL
SELECT * FROM orders WHERE country = 'Canada'
```

- To locate these rows, the database uses the Index created for the "country" column.
- It starts at the root node of the index, then looks for the country `'Canada'`. 
- `'Canada'` is not in the root node, but since it's alphabetically between Brazil and USA, the database **chooses the second internal node**, which you know will contain countries that are alphabetically between Brazil and USA. 
- The database searches this internal node for `'Canada'` and finds that it is the first entry. So the database chooses the first path and **reaches a leaf node** where it finds **all the records within the leaf node that represent orders placed in Canada.** 
- Since the entries are not unique within the country column, the database also needs to **horizontally traverse across the leaf nodes to find all entries that correspond to Canada**.

So in summary, to retrieve data that has an index structure, the database would need to traverse the B tree first, since the tree is maintained to be balanced, meaning that the number of children nodes are evenly distributed between the parent nodes all the way up to the root node of the tree. 

Traversing the tree is always an efficient operation that takes $O(log\space n)$ time, but if the index does not contain unique elements, once the database has located the appropriate leaf node, it needs to then traverse horizontally across a chain of leaf nodes to retrieve all rows with the desired index value. 

> 	*If there are lots of repeated values, traversing a chain of nodes could end up being almost like scanning the entire table, in which case a query optimizer will choose to scan the full table instead of using the index.* 
> 	![[Screenshot 2026-01-23 at 16.20.31.png]]

So when you create an index, you need to **carefully choose a suitable column or columns to build your index on.** The general strategy is to create index structures that will improve the performance of your most *performance-sensitive* queries. 

>	 *You also don't want to overload the tables with too many indexes because maintaining balance in many tree structures whenever the data gets updated may actually **degrade the performance of your database.*** 



#### Index Creation

To better understand how indexes can impact the cost of a query, let's take as an example the payment table of the DVD rental database you saw in the first lab of this week. 

![[Screenshot 2026-01-23 at 16.29.20.png]]

First, let's check the execution plan of an SQL query that doesn't involve indexes. 
![[Screenshot 2026-01-23 at 16.28.31.png]]
- From the return plan, you can see that the query optimizer chose to do a ***sequential scan***, meaning a *full table scan*. 


Now let's create an index for the rental_id column. I'll start with the keywords `CREATE INDEX` and give this index the name `rental_idx`. I want to create this index structure on the `rental_id` column of the payment table.

```MySQL
CREATE INDEX rental_idx ON payment (rental_id);
```

![[Screenshot 2026-01-23 at 16.31.45.png]]

This time, the query optimizer recognized the presence of the index and chose the index based strategy because it has a much lower cost. So here, by adding an index, I was able to reduce the query time by more than a factor of 30.



#### Indexes in Columnar Storage

So far, we've been discussing the concept of indexes for **traditional relational databases**, but the same concept also exists for **columnar storage**. 

For instance, when you create a table in **Amazon redshift**, you can declare one or more of its columns as **"sort keys"**. Then, without creating a separate data structure, redshift directly sorts the rows of the data according to the sort key and then stores the data on disk. 

![[Screenshot 2026-01-23 at 16.33.23.png]]


For example, when you declare the `country` column as the sort key for this table, 
![[Screenshot 2026-01-23 at 16.34.11.png]]

all the rows of the table will be reorganized based on the `country` column, like this. 
![[Screenshot 2026-01-23 at 16.34.37.png]]
- This is what you see when sorting by a column or group of columns in a spreadsheet. 

The reorganized version of each column is then sorted on disk. 

>	By the way, other cloud data warehouses, such as **BigQuery**, refer to the sort key as the **cluster key**, but it's the exact same concept. 

By properly creating an index in row oriented databases or specifying a sort or a cluster key for columnar storage, you can enhance the performance of certain queries by reducing the amount of rows that need to be scanned. 


#### Additional Index Examples

##### Example 1

```MySQL
SELECT first_name, last_name
FROM employees
WHERE employee_id = 123
```

If `employee_id` is the primary key , then an index is automatically created on the primary key. So if you check the query  plan of such statement, you will see that the DBMS will use the index to scan the table and get the results.
![[Screenshot 2026-01-23 at 17.04.24.png]]


##### Example 2

```MySQL
SELECT productcode, priceeach
FROM orderdetails
WHERE ordernumber = 10101
```

The `orderdetails` table has a **composite primary key**, which consists of `(productcode, ordernumber)`. The index created is on the `productcode` column. So if you check the query plan of such statement, you will see the DBMS will perform a full table scan.![[Screenshot 2026-01-23 at 17.16.01.png]]

Now, if you instead choose the composite primary key to consist of `(ordernumber, productcode)`, then the table will create an index on the `ordernumber` column. The DBMS will use the index to get the results for this query.
![[Screenshot 2026-01-23 at 17.17.23.png]]


## Retrieving Only the Data You Need

In the previous section, you saw that by creating a proper index for a table, you can avoid doing a full table scan, which can be very costly. 

It turns out, however, that you can actually do *even worse* than simply doing a full table scan. That's when you write a query that not only scans the entire table, **but also returns everything.** 

When you run `SELECT * `with no predicates, meaning no `WHERE` clause to filter the results, the DBMS need to scan the entire table and retrieve every row and column. This could be costly for the source database because large amounts of data needs to be transferred from disk, and the data might need to be further processed. 


Running` SELECT * `on your Cloud pay-as-you-go database or data warehouse can also be very expensive. You'll be charged for reading all the bytes from the entire table and for utilizing any compute resources while the query is running. 

#### Pruning

This is why, in general, you should avoid running `SELECT *` with no where clause to filter the results. As a rule of thumb, you should **query only the data you need**. If you'd like to explore the data, consider using a technique called ***"pruning"*** to exclude irrelevant data from being scanned in your query. 

##### Row-based pruning
One of the most common pruning techniques is ***"row-based pruning"***, where you filter out rows that don't meet your wear condition. For example, like you saw earlier, you can select all the records from the payments table where the rental ID column is one. 

```MySQL
SELECT * FROM payment 
WHERE rental_id = 1;
```

When filtering your results, you can further improve your query performance by using indexes in traditional row-oriented databases, or by using sort or cluster keys in columnar storage, such as BigQuery or Amazon Redshift. 

As you saw in the previous video, you can create an index called rental_idx on the rental ID column and the payment table to speed up this query. 

##### Column-based pruning
Pruning techniques also include ***"column-based pruning"*** where you specify in the query statement, *only the columns you need*. For example, instead of selecting all records, you can select only the customer ID and rental ID columns from the payment table. 

```MySQL
SELECT customer_id, rental_id
FROM payment;
```

This way, the database won't have to scan all the other irrelevant columns. 


##### Partition pruning
There's also ***"partition pruning"***, where you only scan specific *partitions* that contain relevant data instead of scanning the entire table. This type of pruning is possible when you work with a data store that allows you to partition your data based on a *partition key*, such as date or location. 

For instance, with big query, you can take this table here that's not partitioned and partition the records based on the order dates. You can then further order each partition by the country .

![[Screenshot 2026-01-23 at 17.27.18.png]]



Let's say user queries this data and filters by a specific order date of April 1st, and country value of USA. The database only has to scan the records in the April 1st partition, then look for records with USA as a country.![[Screenshot 2026-01-23 at 17.28.34.png]]

To avoid incurring any surprising expenses or degrading the performance of the source data storage, you should always make sure to only read the data that you need. 

Another factor that has a huge impact on query performance is the way you `JOIN` data from different tables. We'll discuss the challenges that come with table joins in the next section.


## The `JOIN` Statement

Using ***joins*** is one of the most common ways to combine datasets, allowing you to transform data and create new datasets within your data pipeline. Additionally, your end users might also use joins to combine the data you serve to them. However, joins are one of the **most time-consuming query operations**, so it's critical that you understand how your end users will need to combine data with joins when you're modeling the data. To quickly recap how joins work, let's consider these two tables. 
![[Screenshot 2026-01-23 at 17.30.48.png]]

- The `orders` table contains information about each order placed in an e-commerce company 
- The `customers` table contains information about each customer 


These two tables **are related to each other** through the `customer_id` and are part of a normalized model that has data stored in separate tables to reduce redundancy. 

To simplify the process of finding information about an order and the customer who placed it, you can combine the data from the orders and customers table using an SQL `JOIN` statement. 

![[Screenshot 2026-01-23 at 17.32.58.png]]

```MySQL
SELECT * FROM orders
JOIN customers ON customers.id = orders.customer_id
```

This way, the corresponding customer information from the customers table is combined with the orders information based on the customer's ID. 

#### `INNER JOIN`

For example, for the three first orders of the `orders` table all have a `customer_id = 1` . So, you'll append the customer information for the customer with `id = 1` from the `customers` table. Then for this next order that has a `customer_id = 2`, you'll append the customer information for the customer with ID of two and so on. 

![[Screenshot 2026-01-23 at 17.37.53.png]]

This type of join is known as the `INNER JOIN`.

>	*An `INNER JOIN` combines data from **only** the rows that share a matching customer ID in both tables*. 



### 3 Common Methods of Implementing `JOIN`s

To help you understand why the `JOIN` operation is one of the most time-consuming query operations, let's go through *three common methods* for implementing joins. Most database query optimizers will use one of these methods when devising an execution plan for join statement. 

###### Nested Loop (default method)
The default method is the **nested loop join**, which works like a "nested for loop". 

- Starting with the first row in the orders table, the database takes note of the customer ID, 
- then it scans every row in the customers table and only retrieves the rows with this matching customer ID. 
- It combines the information from this row of the orders table with a retrieved rows of customer information. 
- This is repeated for every row in the orders table and the combined results are returned at the end. 

```python

for customer_id in orders:
	for id in customers:
		if customer_id == id:
			<join data>
```

![[Screenshot 2026-01-23 at 17.43.13.png]]

###### Index-based nested loop
The second method is the **index-based nested loop**, which is a variation of the first method. 

This method can be used when an index exists for at least one of the join attribute, which in this case, could be the `id` column in the `customers` table or the `customer_id` column in the `orders` table. 

![[Screenshot 2026-01-23 at 17.50.23.png]]

Let's assume an index exists for the `id` column in the customers table. 

You might have a **B-tree structure with a root node**, internal nodes, and leaf nodes that looks something like this. 

![[Screenshot 2026-01-23 at 17.51.23.png]]

To execute this join, the database retrieves a first row from the orders table, and then uses the ID index to locate all rows in the customers table that have a matching ID of one. 

From the root node, since one is less than 10, it'll go down to the first internal node. Then since one is less than three, it'll go down to the first leaf node. Finally, it finds unique ID of one and its corresponding row addressed from the customers table. The database will then retrieve the data from this row in the customers table and join it with the first row from the orders table. It will repeat this for every row in the orders table and then return the combined results. 
![[Screenshot 2026-01-23 at 17.52.15.png]]


###### Hash-Join Method
The last method is the ***hash-join method***. This method uses a **hash function** to *map the rows from each table to **buckets*** *based on the value of the join attribute*, which is a *customer's ID* in this example. 

![[Screenshot 2026-01-23 at 17.54.24.png]]

With this method, the database first scans all rows of the smaller table, which is the `customers` table in this case, and sends each row to a particular bucket. For example, we could have a hash function that maps Row 1 from the customer's table to the first bucket, Rows 2 and 3 to the second bucket, and Row 4 to the last bucket. 

![[Screenshot 2026-01-23 at 17.55.02.png]]

Then the database scans through the rows of the larger orders table, sending each row to a bucket based on the same hash function. 

![[Screenshot 2026-01-23 at 17.56.58.png]]


In the first bucket here, since the row from the `customers` table and the rows from the `orders` table, all have a customer ID of one, the database would combine all these into three rows. 

This method can be much faster because scanning these smaller buckets in parallel can be much faster than scanning the entire table. 

In all of these methods, the join operation requires a database to scan a considerable number of rows from each table, and that can occur multiple times. That's what makes a join operation one of the most time consuming query operations. 

>	*Aside from being able to write efficient join queries yourself as a data engineer, **you should model and serve data to your end users in a way that enables them to easily join the data when needed.*** 
>	
>	The ***data model*** and ***schema*** you choose affects the number of joins or end users need to perform to get the data that they want. 


>	*In general, normalized schemas result in less redundancy in your data, but require more complex join statements to combine the data.* 


Here are two schemas you've seen in the first lab of the specialization. The first is a normalized schema of the input data that you ingested in your pipeline. You then ran a glue op to transform the data into the star schema before serving it to the data analyst. 

![[Screenshot 2026-01-23 at 18.13.22.png]]

Let's say the data analyst is interested in computing the **total number of products sold for each country**. 

- If you kept the data in its original normalized schema, then the data analyst would need to join the `customers` table with the `orders` table and then join the orders table with the orders details table. 
- On the other hand, in the star schema, the data analyst would only need to perform one JOIN by combining the fact_orders table with the dim_locations table. 

##### OBT (One Big Table)
Another alternative is to combine the relevant attributes into one big table. 
![[Screenshot 2026-01-23 at 18.15.35.png]]

Then the data analyst won't need to perform any joins at all. 

We'll get more into the details of data modeling and the pros and cons of each approach in the next course. 

##### Many-to-Many relationship (Row Explosion)
Aside from modeling your data, another challenge you'll likely encounter when working with joins is **when two tables have a many-to-many relationship.** 

For example, the payments and orders table shown here have a many to many relationship.

![[Screenshot 2026-01-23 at 18.17.49.png]]

A payment can be associated with many orders and an order can be paid over several payments.
![[Screenshot 2026-01-23 at 18.18.39.png]]

- Each payment is associated with a **customer**, and let's say that customer number 1 made five different payments. 

- Each order is also associated with a **customer**, and let's suppose that customer number 1 made five different orders. 

You can join the `payments` table and the `orders` table based on the `customerNumber` to get the new table. That sounds simple, right? 

![[Screenshot 2026-01-23 at 18.19.53.png]]

The problem is that this table might not represent what you think it should. 

At first glance, you might think that this table shows the payment information and their associated orders, but there's actually an ***error in the join logic*** here. 

>	*ERROR: By joining the table on the `customerNumber`, you are blindly joining the payments table with the orders table without considering if a given payment is correctly mapped to its corresponding order.* 

Because of this error, every row in the payments table that's associated with Customer 1 is mapped to every row in the orders table that's also associated with Customer 1. This creates five times five or 25 rows in the output. 

Now, suppose that there are many other repeats in the customer number column. This leads to a scenario known as row explosion, where the query returns more rows than expected. 

**Row explosion** can generate enough output rows to consume a massive amount of database resources and can actually cause queries to fail occasionally. Avoid this problem, make sure that you check your **query plan** to see if it correctly describes what you intend the join to do. 

If this query will be run frequently, then consider adding a table to your model that correctly maps a payment to its corresponding order number. 

Understanding how joins or process can help you design more efficient join queries as well as properly model the data for your end users.


## Aggregate Queries

[TBC]
