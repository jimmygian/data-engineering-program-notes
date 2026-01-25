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
- Breaks down the query into **query tokens**, which are the basic building blocks of the SQL query, including *keywords* like `SELECT` and `FROM`and *attribute names, operators,* and so on. 
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

With this method, the database first **scans all rows of the smaller table**, which is the `customers` table in this case, and **sends each row to a particular bucket**. For example, we could have a hash function that maps Row 1 from the customer's table to the first bucket, Rows 2 and 3 to the second bucket, and Row 4 to the last bucket. 

![[Screenshot 2026-01-23 at 17.55.02.png]]

Then the database **scans through the rows of the larger orders table**, sending each row to a bucket based on the same hash function. 

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

When building a system for analytical workloads, you have to support **aggregating large datasets**. 

>	*Aggregate queries are used to compute a summary value of a column, such as the sum, average, maximum, minimum, and count of the values in that column.* 

Here's a query where you select the min price from the orders table. 

```MySQL
SELECT MIN(price) FROM orders;
```

- In role oriented databases, aggregate queries like this one can be computed by a full table scan, where you scan each row in the table, looking for the row with the minimum value in the price column. 

- Or you can speed up this query by using an index structure if it's available. 

So, for example, if you have a B tree index on the `price` column, the query optimizer can decide to use the index and traverse this tree to reach the leftmost leaf node to return the minimum price. 

![[Screenshot 2026-01-24 at 09.43.39.png]]

###### `GROUP BY`

When working with aggregate queries, you can also use a `GROUP BY `clause to group query results by specific columns and return the summary values within each group. 

So, for example, you can add `GROUP BY country` to your query to get the minimum price for orders placed in each country:

```MySQL
SELECT MIN(price) FROM orders
GROUP BY country;
```

- In this case, the table must be first **partitioned** into groups where each group includes just one country. 
- Then the minimum price is computed for each group.

![[Screenshot 2026-01-24 at 09.46.03.png]]

- The partitioning is usually executed using a **sorting algorithm or a hash function**, 
- Or this could be avoided if an **index** exists on the grouping attribute, which is the `country` column in this case. 

>	*In any case with aggregate queries, you are performing operations on columns rather than rows.* 

###### Row vs Column for analytical queries

![[Pasted image 20260124094839.png]]

Remember that with **row oriented databases**, you store all the values within the same row next to each other on disk. This means that to get the `price` value from each row, you have to transfer all the rows in their entirety from disk to memory, so you end up transferring more data than you need to execute the analytical query. This could work just fine with small data sets, but with large data sets, performing analytical queries and role oriented storage becomes much slower. 

On the other hand, **columnar storage** stores all the values within the same column next to each other on disk. So in this case, if you needed all the values from the price column, you just need to transfer the data from the first price value through the last price value that's stored on disk. So performing analytical queries on columnar storage is much more efficient because you only need to transfer the relevant columns of data for your analytical query from disk to memory. 


## Amazon Redshift Cloud Data Warehouse

You've been learning about queries, indexes, and various strategies and tips for writing efficient SQL queries. 

Now, let's talk about some **architectural factors** that affect query performance for data warehousing workloads running on **Amazon Redshift**. Here, we'll look at how 
- Redshift queries data 
- and some considerations to keep in mind for table design that can optimize query performance. 

![[Screenshot 2026-01-24 at 09.52.32.png]]
![[Screenshot 2026-01-24 at 09.53.41.png]]
Redshift is designed to be a **highly-efficient data warehousing solution**, and it achieves this through a combination of different internal architectural features, including:
- columnar data storage, 
- massively parallel processing, and 
- data compression encoding schemes. 
![[Screenshot 2026-01-24 at 09.54.26.png]]

Redshift is a **columnar database**, which means that it stores data column-by-column together on disc rather than row-by-row. This storage method is particularly efficient for 
- analytical queries and 
- OLAP workloads 
that require aggregating data over many rows, but only need to access a few columns for any particular query. 

By storing data in this columnar way, Redshift can quickly scan and retrieve the necessary data, significantly speeding up query performance. This is part of the reason why Redshift is especially well suited for ***data warehousing*** and ***large-scale data analytics***, where fast query performance over large data sets, and cost efficiency are important. 

###### Data Compression / De-compression on Redshift
Columnar storage also allows for **better data compression**. When you run a query: 
- Redshift reads the compressed data into memory 
- and decompresses it as needed, 
- which means it can use more memory for actually analyzing the data, thus your queries can run faster. 

![[Screenshot 2026-01-24 at 09.57.05.png]]

- This way you can *save on storage space* 
- and *cut down on the amount of data being read* from the disc. 

###### MPP on Redshift
Redshift also uses **"massively parallel processing"** or **MPP**. 

Redshift is made up of a cluster that has multiple compute nodes and a leader node. 
![[Pasted image 20260124100054.png]]


Data associated with a workload is distributed across these compute nodes, and each node is responsible for storing a portion of the data, as well as processing queries on that data. 
![[Screenshot 2026-01-24 at 10.01.39.png]]
![[Screenshot 2026-01-24 at 10.02.01.png]]
![[Screenshot 2026-01-24 at 10.02.22.png]]


Compute nodes are partitioned into what are called ***slices***. 
A slice uses ***a portion of** the compute node's memory and disc space* to process ***a portion of** the data assigned to that node*. 
![[Pasted image 20260124100313.png]]

###### Data Retrieval
With MPP, these compute nodes **work together** to handle query processing with each slice running the query on different portions of the data. 

When you submit a query to Redshift, the leader node: 
- parses the query, 
- develops execution plans, and generates a series of necessary steps. 
- It then compiles the code needed to perform the task and distributes it to the compute nodes for execution. 

Each slice processes its assigned portion of the data ***in parallel***. 

![[Pasted image 20260124100645.png]]

Once a compute node is done with the work, it sends the results back to the leader node, which then aggregates the results into the final result set. 
![[Pasted image 20260124100710.png]]

>	*This parallel approach ensures that queries are executed quickly. However, the performance of your queries may also depend on the **number of nodes** or **node types** you have in the cluster.* 


### Table Design

All that being said, to optimize for efficiency and performance, you can't rely on these built in factors alone. There are multiple key factors to consider related to ***table design.*** When you create your table, you can optionally define a *"sort key"* and *"distribution style"*, which will heavily influence overall query performance. 

##### Distribution Style
Let's dive into the details around distribution style first. I've alluded to this one already when we talked about how MPP works with Redshift. 

The data gets divided amongst the compute nodes, and how that division happens is controlled by defining a distribution style for the table. 

![[Screenshot 2026-01-24 at 10.09.44.png]]

There are **two main goals** when defining an appropriate distribution style. 

1. **Uniform / Even distribution**
The first is to have a **uniform or even distribution** of the data across the nodes. 

Uneven distribution, otherwise known as **"data distribution skew"**, may result in some nodes doing a lot more work than other nodes. Since your query would be waiting on one node to churn through a large amount of data, it can lead to poor performance. ![[Screenshot 2026-01-24 at 10.11.08.png]]

Having an uneven distribution of data like this means that you can't take full advantage of the massively parallel processing capabilities Redshift offers. 


2. **Minimise Data Movement**
The other goal is to minimise data movement across the nodes. If a query running on a node involves joining tables or aggregating data that is distributed across multiple other compute nodes, some of the data may need to be redistributed between nodes over the network. ![[Screenshot 2026-01-24 at 10.13.21.png]]
![[Pasted image 20260124101410.png]]

This *cross-node* data movement can result in increased network traffic, which in turn may slow down query performance and increase your query cost. 

To minimize this, it's important to **carefully select a *distribution style* for your tables**. 

A reasonable data distribution across the nodes ensures that *related data is collocated on the same node:
![[Screenshot 2026-01-24 at 10.15.44.png]]

Thus, reducing the need for this cross node communication. 
![[Pasted image 20260124101629.png]]

This optimization helps balance the workload and improve query efficiency by keeping as much processing as possible localized to each compute node. 

When you create your table, you can choose from the distribution styles: 
- AUTO 
- EVEN 
- KEY 
- ALL 

###### "KEY" distribution style
KEY will let you choose a specific column, and then use that columns values to distribute the rows of data among the nodes. The leader node will distribute rows with the same key value to the same node. 
![[Pasted image 20260124101835.png]]

You may be tempted to define a specific key for every table you create. But this requires some *thorough analysis* of the dataset, and you might not know exactly which column would make the most sense. 

In this case, you may instead want to use AUTO. 

###### "AUTO" distribution style
AUTO which will let Redshift assign an optimal distribution style based on the size of the table data. 

If you can't provide a value for distribution style, it will default to AUTO

Alternatively, you can use the EVEN distribution style. Redshift will have the leader node distribute the rows across the nodes using a **round-robin approach**, regardless of the values in any particular column. 

>	*Even distribution is most appropriate when a table won't really need to have joins run against the dataset.* 

###### "ALL" distribution style
Then there is also the ALL option. With ALL, a ***full*** copy of the entire table is distributed to every node. When using even or key, Redshift places a portion of the table's row on each node. But when you use all, Redshift ensures that every row is collocated for every join that the table participates in. ![[Screenshot 2026-01-24 at 10.29.41.png]]

> *This is nice for cases where you frequently join smaller tables with much larger tables.* 

By having a full copy of the small tables on every node, you eliminate the need for data shuffling across nodes during join operations. 

**GOTCHA**: However, the all distribution *multiplies* the storage required to store the table data by the number of nodes in the cluster. It takes much longer to load, update, or insert data into multiple tables. For this reason, Using the ALL distribution is really only appropriate for relatively ***slow-moving tables***. 

##### Sort Keys

Now let's move on to sort keys and how that impacts Query performance. 

>	*Redshift stores your data on disc **in sorted order according to what you define as the sort key.*** 

When you submit a query to Redshift, the query optimizer uses the **"sort order"** to determine the most optimal query plans. The sort key you choose for a table impacts query performance because it determines how the data is *physically* organized on disc. 

When your queries filter or join data based on sort key, Redshift can more efficiently locate the relevant data, reducing the amount of data that needs to be scanned from disc. 

Choosing an appropriate sort key can: 
- minimize disc read operations and 
- speed up overall query execution. 



For example, if you frequently query a `sales` table by `order date,` defining the order date as the **sort key** allows Redshift to efficiently scan only the necessary portions of the table that match the date range in your query.

![[Screenshot 2026-01-24 at 10.42.39.png]]

Similarly, if you often filter by `customer_id`, setting customer ID as a sort key can optimize those queries. 

>	You can think of this like how OLTP databases use indexes to speed up queries. 
>	In a similar way, OLAP databases like Redshift, use sort keys to speed up queries. 

Those are some considerations to keep in mind for table design. 


## Additional Query Strategies

In addition to knowing how queries are processed behind the scenes, understanding strategies for working with complex queries, such as **query caching** and **common table expressions**, as well as other database maintenance techniques like **vacuuming** can help you improve the performance of your queries. 

#### Leverage Query Caching

Let's assume you're working with the DVD rental database you saw in the previous course, and you want to calculate the total amount spent on three film categories, the family, drama and comedy categories. 

```MySQL
SELECT category.name, SUM(payment.amount) AS amount
FROM payment
JOIN rental ON rental.rental_id = payment.rental_id
JOIN inventory ON inventory.inventory_id = rental.inventory_id
JOIN film ON film.film_id = inventory.film_id
JOIN film_category ON film_category.film_id = film.film_id
JOIN category ON category.category_id = film_category.category_id
WHERE category.name IN ('Family', 'Drama', 'Comedy')
GROUP BY category.name
ORDER BY amount;
```

For this query to be possible we need to join multiple tables.

Running this query frequently on a large database containing this data could be **very costly**. 

>	*To avoid rerunning the same query repeatedly and incurring significant charges, many databases, especially Cloud OLAP databases, allow you to **cache query results** to make them available for instant retrieval later on.* 

By leveraging **"query caching"**, you can reduce a load on your database and enhance the user experience for queries that are executed frequently. 

#### Readable Queries

Another piece of advice for writing complex queries is to prioritize **readability**, just like you would with any code. Readable queries are less likely to contain errors, simpler to debug and easier to collaborate on. You can enhance the readability of your queries by using **Common Table Expressions** or CTEs to create a temporary result set that you can reference elsewhere in your query. This is a concept you practiced in the first lab of this week. 

Let's consider these two identical queries:

```MySQL
SELECT actor.first_name, actor.last_name
FROM actor
WHERE actor.actor_id IN (
	SELECT actor_id
	FROM film_actor
	WHERE film_id = (
		SELECT film_id
		FROM film
		WHERE title = 'Rocky War'
		)
	)
```

```MySQL
WITH selected_film AS (
	SELECT film_id
	FROM film
	WHERE title = 'Rocky War'
),
film_actors_id AS (
	SELECT actor_id
	FROM film_actor
	WHERE film_id IN selected_film
)
SELECT actor.first_name, actor.last_name
FROM actor
WHERE actor_id IN film_actors_id;
```


#### Vacuuming (Database Resources)

Along with optimizing the queries themselves, you also want to optimize the use of DBMS resources to execute the query as efficiently and quickly as possible. 

Certain databases are designed to allow *concurrent access to data* while the data is being updated. When you delete or update a record in these databases, they create new records while physically retaining on disc the outdated data as pointers to the last state of the database. 
![[Screenshot 2026-01-25 at 13.42.35.png]]

This can also help the transaction ***roll back*** to its previous date in case of any failure. 

However, as these outdated records accumulate and are no longer needed to be referenced, they can lead to table bloat. 

![[Pasted image 20260125134628.png]]
- This is where the data space occupied on the physical disc far **exceeds the actual data size**. 
- In addition to the wasted disc space, the database has to **skip over many blocks** to retrieve the required data, slowing down queries. 
- Since a query optimizer relies on the internal statistics of the data on disk to generate query execution plans, **outdated records can also lead to suboptimal or inaccurate plans**. 
- Similarly, **indexes can also become inefficient** as they accumulate entries for outdated data. 


###### Vacuuming

And so to free up space for new records and allow for better query and index performance, you should remove these dead records using a process called **vacuuming**. 

![[Screenshot 2026-01-25 at 13.47.35.png]]
![[Screenshot 2026-01-25 at 13.48.01.png]]
- You can vacuum a single table, multiple tables, or all tables in a database. 
- Vacuuming is more critical for relational databases, such as Postgres and MySQL, because large numbers of transactional operations can cause a rapid accumulation of dead records. 
- When you work in these systems, you need to familiarize yourself with the details and impact of vacuuming. 

With that, you now have more strategies to optimize the performance of equeries. 



## Quering Streaming Data

So far, we've been discussing how to query batch data, but as streaming data becomes more prevalent, you might find yourself needing to aggregate and join together streaming data as well. 

>	*When querying your streaming data, you must adopt query patterns that reflect the real-time nature of this data.* 

Let's say you want to ingest data from this streaming system and process a stream of data as soon as you receive it. You can use **stream processing systems**, such as **"Apache Flink"** and **"Apache Spark Streaming"**, which enable you to apply SQL queries, even complex ones, continuously over your stream of data. Streaming platforms such as **"Kafka"** also support querying data in Kafka streams. 

![[Screenshot 2026-01-25 at 14.26.30.png]]
#### Windowed query

With these systems, you can continuously aggregate the streaming data by applying something called a ***windowed query***, 

>	*Windowed queries allow you to bound your queries using a window and then apply operations (such as aggregation, adding, or removing data) over that window.* 

Let's take a look at three common types of windows: session, fixed time, and sliding windows. 

#### Session Window
A session window is ideal for handling events that arrive at **irregular times**. 

![[Pasted image 20260125142910.png]]
- It groups events that occur at similar times 
- and filters out periods of time when there are no events. 


When using this type of window, you need to specify the **maximum time gap allowed** between events to identify when one session ends and another one begins. 

**Example**
Let's say you're analyzing *website clicks* for each user and decide to set the time gap of inactivity to be 5 minutes or more between your session windows. 

![[Screenshot 2026-01-25 at 14.30.02.png]]

In here, you'll have three session windows because they are each separated by 5 minutes or more of user inactivity. 

![[Pasted image 20260125143216.png]]

Note that session windows are unique to each key, so in this example, each user gets their own set of session windows. Doing analytics on these windows could, for example, allow an analyst to do something like follow-up with an email that has a coupon for a product that was viewed by the user in their last session window. 

#### Session Boundaries
To determine the session boundaries:
- the processing system starts with a new session window **when the first event occurs**. In this case, the first website clicked by a user. ![[Screenshot 2026-01-25 at 14.35.25.png]]
- Then the system continues to accumulate arriving events for that user (as long as no events happen within 5 minutes of the previous one). ![[Screenshot 2026-01-25 at 14.35.47.png]]
- Once there's a 5 minute inactivity period, the system **closes the window**, sends the consumer any specified aggregations like max, min, or average values, and then flushes the data. ![[Screenshot 2026-01-25 at 14.36.18.png]]
- If no events for the user arrive later, the system starts a new session window. ![[Screenshot 2026-01-25 at 14.36.40.png]]

And so with session windows, the windows can extend to be of **any size** as long as events keep on arriving close to each other.
![[Screenshot 2026-01-25 at 14.44.00.png]]

|Event Time|Time Since Previous|Session Status|
|---|---|---|
|14:00|—|Session starts|
|14:03|3 min|Session continues|
|14:07|4 min|Session continues|
|14:13|6 min|Session closes, new session starts|


#### Fixed-Time Window
Alternatively, you could aggregate the data for events over windows of fixed size, known as ***fixed time*** or ***tumbling windows***. 

For example, here you have 3 fixed-time windows, each lasting 20 seconds. The system processes all data arriving within each window, and then sends the aggregations as soon as the window is closed. This can be useful if you like to compute, for example, the total number of clicks that happen every 20 seconds. This is similar to traditional batch ETL processing, where you might run a data update job every day or every hour. However, the streaming processing system allows you to generate windows more frequently and deliver results with lower latency. With session and fixed time windows, the windows are non-overlapping. But with sliding windows, you can group events into windows of fixed time length that can overlap. 

For example, here you have three 60-second overlapping windows generated every 30 seconds. 

![[Screenshot 2026-01-25 at 14.48.56.png]]

>	*The system processes all data arriving within each window, and then sends the aggregations as soon as the window is closed.* 

This can be useful if you like to compute, for example, the total number of clicks that happen every 20 seconds. 

This is similar to traditional **batch ETL processing**, where you might run a data update job every day or every hour. 

However, the streaming processing system allows you to generate windows more frequently and deliver results with lower latency. 


#### Sliding Windows
With session and fixed time windows, the windows are **non-overlapping**. But with sliding windows, you can group events into **windows of fixed time length that can overlap**. 

For example, here you have three 60-second overlapping windows generated every 30 seconds. 
![[Screenshot 2026-01-25 at 14.52.52.png]]

This type of windowing can help you calculate things like a **moving average within a time interval.** 

### Joining Data Streams

In addition to aggregating streams of data, you can also:
- **join multiple streams**, 
- or **combine a stream with batch historical data**. 

##### Conventional Way
The conventional way of joining multiple data streams is to *transform each stream into a table* and then *join the tables in the database*.
![[Screenshot 2026-01-25 at 14.55.15.png]]

##### Stream-to-Stream
But streaming processing systems are increasingly supporting direct **stream-to-stream joining.** So for example, you might want to join a stream of "web browsing" data with streaming data from an "ad platform". Since those streams can be produced **at different event rates** and have **different latencies**, typical streaming join architectures rely on **streaming buffers that can retain those events for a certain period of time.**  Events from the streams get joined in the buffer and are eventually emitted after the buffer's retention period passes. 

![[Screenshot 2026-01-25 at 14.56.41.png]]


##### Enrich with batch data
Aside from joining two streams of data, you might also want to join streaming data with batch historical data that's stored in a database or object storage in order to produce an enriched stream of events. 

For example, you might want to enrich product browsing events from an e-commerce website with product details and user demographic information. 

To do this, you might use a ***serverless function*** or a ***processing system*** to look up the product and user information in an in-memory database or object storage, then add the required information to the event, and finally output the enhanced events to another stream. 

![[Screenshot 2026-01-25 at 15.02.12.png]]


## Deploying an Application with Amazon Managed Service for Apache Flink

When you're looking to run Apache Flink with AWS, you have multiple options to choose from. 

- You can run Apache Flink on **Amazon EMR** as a YARN application. 
- Or you can self host Apache Flink in a **containerized environment** using ***Amazon Elastic Kubernetes service*** or ***Amazon Elastic Container*** service. 

These are what you might call the do-it-yourself options.

![[Screenshot 2026-01-25 at 15.09.13.png]]

but you can also choose to use a managed service like Amazon managed service for Apache Flink. And that's what I'm going to talk about here.
![[Screenshot 2026-01-25 at 15.11.42.png]]

- **Amazon managed service for Apache Flink** runs Apache Flink on AWS. 
- It provides the underlying infrastructure for your Apache Flink applications and creates a hosted, serverless environment for them to run in. 
- It handles a lot of the heavy lifting for you, including provisioning, compute, setting up and managing AZ failover for resilience, automated scaling, and application backups. 

I wanted to pause for a moment to just say a bit more about how Flink is working behind the scenes. The way Flink was able to connect to the Kinesis data stream and write to S3 was by using what are called **connectors**. 

![[Screenshot 2026-01-25 at 15.25.54.png]]

Connectors provide code for interfacing with various systems, which include things like databases, message queues, and cloud storage services. 

For instance, you can connect to Amazon DynamoDB for working with NoSQL data, or use Amazon Kafka for stream processing with Apache Kafka topics. Flink connectors also support relational databases via JDBC, which allows for integration with databases like MySQL or PostgresQL running on Amazon RDS. 

![[Pasted image 20260125152712.png]]

These connectors enable Flink to interact with different data sources and destinations. Helping you with the creation of real-time data processing pipelines across different platforms and services. 

In the Amazon Managed Service for Apache Flink you can use blueprints to quickly create the deployment of your system. In the real world though, you would need to: 
- specify the AWS Glue database that contains your connection information to your data sources and destinations. 
- Then when you want to access your data sources and destinations, you would specify the relevant Glue tables contained in the database. 
- And the tables provide access to the Glue connections that define the locations, schemas, and parameters for your data sources and destinations. 

![[Screenshot 2026-01-25 at 15.34.51.png]]


## Week 3 Questions

1. How is an index implemented in a database?
	It divides data into doubly-linked blocks stored in a balanced B-tree structure.

2. True or False: In general, querying a B-tree index is more efficient when the index is defined on a column with lots of repeated values. --> True

3. Which are the best practices to query?
	Cache results, use CTE, use WHERE, specify only columns you need


## Optional Readings

- Chapter 8 of [Fundamentals of Data Engineering](https://go.redpanda.com/fundamentals-of-data-engineering)
- [Database internals](https://www.amazon.com/Database-Internals-Deep-Distributed-Systems/dp/1492040347)
- [Use the index luke](https://use-the-index-luke.com/sql/table-of-contents)
- Redshift query resources:
    - [https://docs.aws.amazon.com/redshift/latest/dg/t_Sorting_data.html](https://docs.aws.amazon.com/redshift/latest/dg/t_Sorting_data.html)
    - [https://docs.aws.amazon.com/redshift/latest/dg/r_EXPLAIN.html](https://docs.aws.amazon.com/redshift/latest/dg/r_EXPLAIN.html)
    - [https://docs.aws.amazon.com/redshift/latest/dg/c-query-performance.html](https://docs.aws.amazon.com/redshift/latest/dg/c-query-performance.html)
    - [https://docs.aws.amazon.com/redshift/latest/dg/c_designing-tables-best-practices.html](https://docs.aws.amazon.com/redshift/latest/dg/c_designing-tables-best-practices.html)
    - [https://docs.aws.amazon.com/redshift/latest/dg/c_designing-queries-best-practices.html](https://docs.aws.amazon.com/redshift/latest/dg/c_designing-queries-best-practices.html)
    - [https://docs.aws.amazon.com/redshift/latest/dg/c_best-practices-sort-key.html](https://docs.aws.amazon.com/redshift/latest/dg/c_best-practices-sort-key.html)
    - [https://docs.aws.amazon.com/redshift/latest/dg/c_best-practices-best-dist-key.html](https://docs.aws.amazon.com/redshift/latest/dg/c_best-practices-best-dist-key.html)
