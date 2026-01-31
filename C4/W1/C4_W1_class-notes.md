Your job as a data engineer is to get raw data from somewhere, turn it into something useful, and then make it available to end users. In some sense, you have experience with all those things already, but the focus of this course is really on turning data into something useful and serving it in a way that creates business value. 


## Course overview

In Courses 2 and 3, you learned how to **ingest data into your data pipeline** and **explore various storage solutions** for hosting your data. Now, before you serve the data to your end users, you need to **transform and model it in a form that supports its intended use cases**. 

In this course, we'll start with data modeling and then look more closely at transformation in the second half of this course. 

>	Modeling your data involves deliberately **choosing a coherent data structure** that *aligns with the business goals and logic*. 


>	**What is a data model?** 
>	A data model organizes and standardizes data into a precise structured representation to enable and guide human and machine behavior, inform decision making and facilitate actions. 

The first part of the definition implies that when you model your data, you define the structure, relationships, and meaning of the data. 

For example, when you model tabular data, you need to think about: 
- **the tables that make up the model**, 
- how to **label** the tables, 
- how the tables **relate** to one another, 
- and **what columns to choose** for each table. 

![[Screenshot 2026-01-28 at 14.09.22.png]]

You should structure the data in a way that connects back to the organization and this is what the second part of the definition implies. 

- For the data to serve its purpose, you'll need to **make the data understandable and valuable to humans**, if you're modeling the data for analytical use cases such as creating reports or dashboards. 
- If the data is going to be used for machine learning use cases, you need to model the data into a form **that is meaningful to a computer**. 


A well constructed data model should reflect the business goals and logic while incorporating **business rules** like requiring a valid payment method before processing in order to ensure compliance with operational standards and legal requirements. 

A good data model should also outline the relationships between business processes. For instance, linking sales data with product inventory data to ensure that the sales process is directly informed by current inventory levels, preventing overselling. 

Beyond that, a robust data model serves as a powerful communication tool, creating a shared language among stakeholders like engineers, analysts, and executives, by standardizing business vocabulary such as clearly defining what constitutes an active user, is that someone who has logged into their account in the last 30 days, someone who has made a purchase in the previous six months, or something else entirely. 

>	 Carefully defining business terms can have a massive impact on downstream reports that describe customer behavior and things like projecting customer churn. 

To ensure successful data modeling, recall the framework for thinking like a data engineer from Course 1 and **always start with talking to your stakeholders**. Understanding the business definitions, rules, and goals is the first step to modeling the data and providing the business with quality data for actionable insights and intelligent automation. 

On the other hand, poor data models that are created haphazardly and don't reflect how the business operates can create more problems than they solve. Instead of promoting communication and shared understanding, poor data models might provide stakeholders with inaccurate information and create confusion. 

Another professional oversight that I often see is when data teams ignore data modeling entirely because they see it as a slow, tedious, and irrelevant process that's only reserved for big companies. They jump directly into building data systems without a plan for how they will organize their data to make it useful for the business. Well, that is a huge mistake. Data modeling has been a practice for decades and was traditionally used to structure data stored in data warehouses and relational databases. With the rise of Data Lake 1.0, NoSQL and big data systems, engineers started ignoring traditional data modeling, sometimes for legitimate performance gains. However, the lack of rigorous data modeling created data swamps, along with lots of redundant, mismatched, or simply inaccurate data. Nowadays, the growing popularity of data management, in particular, data governance and data quality is pushing the need for coherent business logic. 

I see data modeling as a critical practice that enhances your understanding of the data throughout its life cycle. As a data engineer, data modeling helps you improve data quality and integration and encourages the adoption of data throughout the organization. No matter the size of the business you're a part of, you should **take a targeted approach to data modeling** by focusing on specific **business domains.** For instance, 
- you could create a data model to help the marketing team better understand customer behavior and campaign effectiveness. 
- Or you can model the company's financial transactions so that the finance team can analyze spending patterns and identify cost saving opportunities. 
- Targeted data modeling efforts can provide valuable insights to drive better decision making and impactful AI models, even within highly complex businesses.


# Week 1: Data Modeling & Transformations for Analytics


## Conceptual, Logical and Physical Data Modeling

As a data engineer, you can add value by **building and maintaining a data model that promotes communication and shared understanding** across your organization. 

But where should you start?  Well, my suggestion is:
- Start with a **high level conceptual data model** that **describes the business entities**. 
- Then you can fill out more of the details to **create a logical data model**. 
- Finally, you'll create what's known as a **physical data model**, where you'll decide on the database or other storage systems that you'll use to store and serve the data, as well as outline the implementation details, meaning the actual tools you'll use to implement the storage systems in your data pipeline. 
![[Screenshot 2026-01-28 at 14.35.26.png]]
#### High-Level conceptual data model
- Zooming in on the conceptual model, it should focus on the high level business entities, the relationships between them and the attributes of each entity. 
- It should also reflect the business logic and rules. 

For example, for tabular data, this description could include the tables, relationships between the tables, and column names. 

##### ER Diagram
When you create a conceptual model, you can visualize it with an **Entity Relationship** or **ER** diagram, 

>	*An **Entity Relationship (ER) Diagram** is a standard tool for visualizing the relationships among different aspects of your data, like customers, products or events.* 

Here's part of the ER diagram of the classic models data set you worked within Course 1. 
![[Screenshot 2026-01-28 at 14.41.11.png]]

As you can see, it contains data about `products` and `orderdetails` for each order. It encodes a connection between product data and order details using these symbols:


This symbol ( `||` ) stands for one and only one:
![[Pasted image 20260128145844.png]]
	meaning that each `orderdetails` record can be associated with one and only one `product`. 
	It's common to call this relationship from `orderdetails` to `products` a **one-to-one** relationship. 

This symbol over here stands for **zero-or-many**:![[Screenshot 2026-01-28 at 14.59.10.png]]![[Screenshot 2026-01-28 at 14.57.44.png]]
	meaning that a product can be associated with zero order details if no one purchased that product, or it can be associated with many order details if it was purchased many times. 
	This relationship from products to order details is a *zero-or-one-to-many* relationship, or more commonly known as simply a **one-to-many relationship**, meaning that one product can be associated with many order details. 

Notice that the relationship changes **depending on the direction you take** when viewing the relationship. ![[Screenshot 2026-01-28 at 15.00.16.png]]

This ER diagram also shows that: ![[Screenshot 2026-01-28 at 15.00.44.png]]
- the relationship from `orderdetails` to `orders` is one-to-one, meaning that one order details can only be associated with one order. 
- the relationship from `orders` to `orderdetails` is one-to-many, meaning that one order can be associated with many orderdetails. 

For example, if a customer brought a bunch of products within the same order, then there would be many order details, one for each product purchased. Each of these order details would be associated with only one order, but the order would be associated with many order details. ![[Screenshot 2026-01-28 at 15.01.28.png]]

#### Logical Model
The next level after the conceptual model is the **logical model**, where you add more details about how you'll implement the conceptual model. 
For example, you would add information on the types of columns for each table and map out the primary and foreign keys. 

![[Screenshot 2026-01-28 at 15.02.10.png]]

#### Physical Model
Then finally, you create the **physical model** where you choose the specific **DBMS** and define ***how*** you'll implement the logical model in that system. 

This physical model should define: 
- the configuration details describing **how data is stored**, such as on disc or RAM or using a hybrid approach, 
- and **how processes like partitioning and replication are implemented**.

When you model your data, you'll move along this continuum from abstract modeling concepts to concrete implementation. 


## Normalization

Back in course 2 when we discussed relational databases as source systems, you learned how normalization can reduce the duplication of data and improve data integrity. 

In this section, we'll revisit this topic and discuss the various forms of normalization. 

>	*Normalization is a data modeling practice that's typically applied to relational databases to remove the redundancy of data within the database and ensure referential integrity between data tables.* 

It was first introduced by relational database pioneer Edgar Codd in 1970, and here are some of the **normalization objectives** that Codd outlined at that time. 
- To free the collection of relations from undesirable insertion, update, and deletion dependencies. 
- To reduce the need for restructuring the collection of relations as new types of data are introduced. And thus increase the lifespan of application programs. 


To better understand Codd's normalization objectives, let's take a look at this example where our sales order data is represented in two different ways. 

![[Screenshot 2026-01-28 at 15.26.51.png]]
- The first model represents the data in one giant sales order table, 
- and the second model represents the same data but spreads it across multiple tables. 

The first model is less normalized than the second one, meaning that the large sales order table contains more redundant data than the smaller tables in the second model. 
![[Screenshot 2026-01-28 at 15.27.22.png]]

So, for example, if you want to update the *address* of a customer, let's say for **Joe Reis** here, you would need to update *every single row that corresponds to Joe Reis in this first table*. 

In the second model, on the other hand, the customer data is stored in a different table. So whenever you want to update the address of a customer, **you only need to change a single row in the customers table.** 
![[Screenshot 2026-01-28 at 15.39.55.png]]

Now, let's say you want to add the orders shipment information. 

In the less normalized model, you would need to change the structure of the table by adding new columns for the shipment information.![[Screenshot 2026-01-28 at 15.41.02.png]]

But in the more normalized model, you can simply create a new table for the shipment data, then use the order id as the foreign key to link this new table through the existing orders table.![[Screenshot 2026-01-28 at 15.41.22.png]]
In this way you won't have to make changes to any other tables. 


The first model that is less normalized is called the **first normal form**, and the more normalized model here is called the **third normal form**. 

![[Screenshot 2026-01-28 at 15.42.14.png]]


### Transforming Data

On the spectrum of normalization, there's also something called the **"denormalized form"** and **"second normal form"** as well. 

>	Each form contains a different level of *redundancy* and incorporates the conditions of prior forms. 

Using the same sales order example, let's start with a denormalized form and transform it step by step to turn it into third normal form. 

##### Denormalized Form

![[Screenshot 2026-01-28 at 15.47.11.png]]

Here's a denormalized table that contains the details of each order placed by a customer. It contains six columns with the order Id as the primary key. 
- A denormalized form is the most "messy" one.
- A *denormalized* form can contain not only redundant data but also nested data. 
- In this example, the order items column contains nested objects where each object contains information such as the items sku number, price, quantity, and name. 

##### DeNormalized --> 1NF
To convert this denormalized table to **first normal form** (1NF), you need to make sure that 
- **each column is unique **
- each column has a single value**, meaning, **no nested data**. 
- And the table must have **a unique primary key.** 

So let's unnest the order items column and replace it with four columns. 
![[Screenshot 2026-01-28 at 15.54.45.png]]

To create a unique primary key, let's enumerate the items in each order by adding a column named `ItemNumber`. ![[Screenshot 2026-01-28 at 15.55.14.png]]

So now the composite key consisting of `orderid` and `ItemNumber` together represents a unique primary key for this table. 

##### 1NF --> 2NF
But this form still contains redundant data and can be further normalized by converting it into the second normal form, which I'll denote here as **2NF**. 

For the second normal form: 
- the requirements of first normal form must be met 
- and any ***"partial dependencies"*** should be removed. 

  >	*A partial dependency occurs when there is a **subset of non-key columns** that depend on some columns **in the composite key**.* 

![[Screenshot 2026-01-28 at 15.57.00.png]]

So, for example, the `customerID`, `CustomerName`, customer `address`, and `OrderDate` are non-key columns that all depend on the `orderID`. Meaning that if you know the order id, you can uniquely identify the information in these last four columns. 

And so you can split the sales order table into two tables, an `orderitems` table and an `orders` table. The composite key consisting of `orderID` and the `ItemNumber` is now a unique primary key for the `OrderItems` table, while `orderID` is a primary key for the `orders` table. 

![[Screenshot 2026-01-28 at 16.08.30.png]]


##### 2NF --> 3NF
So now there are no more *"partial dependencies"* in these tables, but they have another form of dependency called a ***"transitive dependency"***. A transitive dependency occurs when a non-key column depends on another non-key column. So, for example, 
- in the `OrderItems` table, the `price` and the `name` of an item depends on its `sku`, 
- and in the `orders` table, the `CustomerName` and the `address` depend on the customer id. 


![[Screenshot 2026-01-28 at 16.10.06.png]]

While this type of dependency can exist in a table thats in second normal form, a table in third normal (3NF) form: 
- needs to meet all the requirements of second normal form 
- and have no transitive dependencies. 

So to convert these tables from second normal form to third normal form, you can remove the transitive dependencies from the order items table by creating another table called `Items` that contains a name, price, and SKU for each item. 

The SKU is now a unique primary key for the items table. 
![[Screenshot 2026-01-28 at 16.26.40.png]]


>	A database is usually considered normalized **if it's in third normal form**, and that's what we'll be using as a convention in this course. 


As a data engineer, **you might ingest data from source databases that are normalized**, especially if they represent a **transactional system**. Or you might work with a data warehouse that contains normalized data. 

When you model your data, the degree of normalization that you should apply to your data depends on your use case. 

There's no one size fits all solution: 
- ***you might encounter cases where denormalization actually has performance advantages***, because it doesn't require you to perform any `JOIN` operations between tables. \
- In other cases, you might prefer the **normalized** form to ensure efficient read and write operations and better data integrity. 


## Dimensional Modeling - Star Schema

While normalized models focus on connecting data entities and modeling the relationships to reduce data redundancy, the **star schema**, also known as a dimensional data model, focuses on structuring the data in a way that facilitates 
- **faster analytical queries** 
- and delivers data that is **more understandable to business users**. 

A star schema collects business measures in a table called the "**fact table**" and surrounds this table with the necessary contextual information stored in **"dimension tables"** in a way that resembles a star-like structure, hence the name star schema. 

![[Screenshot 2026-01-30 at 13.51.48.png]]

Let's zoom in on the fact and dimension tables to see how they can better support analytical queries. 

### The "fact table"

>	*The fact table contains **quantitative business measurements** that result from a **business event or process**.* 

For example, when you **order a UBER ride**, this event generates measures such as: 
- the trip duration, 
- trip price, 
- tip paid, 
- trip delays, 
- and so on. 

![[Screenshot 2026-01-30 at 13.55.08.png]]
	These business measures are what we call ***facts***, so each row in the fact table corresponds to **the facts of a particular business event.** 

#### Fact table's "Grain"
When designing a star schema model, you also need to decide on what's called **"the grain"**, meaning, the ***level of detail*** you want to show in each row of the fact table. 

In the `rideshare` example, ***each row*** in the fact table could represent 
- "all the rides completed by all customers within a single day", 
- "all the rides of a single customer in a day" 
- or "a single ride completed by a single customer", 
- and so on. 

![[Screenshot 2026-01-30 at 14.15.32.png]]
##### The atomic grain
While various grain levels are possible, I suggest you go with what's called **"the atomic grain"**, which refers to **the most detailed level at which data is captured by a given business process** and so for the ride share example, ideally, each row of the fact table would correspond to *"one completed ride by a single customer"*. 

Since ***facts relate to events*** and you can't change an event after it's already happened, the data in the fact table is **immutable**. In other words, fact tables don't change and are **append-only** and so you'll find that most fact tables are typically **narrow and long**, meaning that they won't have a lot of columns, but can have *a lot of rows that represent events*. 


### Dimension Tables
A fact table is always accompanied by dimension tables.

>	Dimension tables provide the reference data, attributes, and relational contexts for the events stored in the fact table. 

They basically describe the what, who, where, and when of each event in the fact table, and they often have many columns. In contrast to the narrow and long fact tables, dimension tables are typically **wide and short**, meaning that they will have *lots of descriptive columns, but fewer rows.* 

In the `rideshare` example, you can have dimension tables that contain information about the customers, drivers, and trip locations. 

In a star schema, you have the
- **fact table in the center** that contains the facts about the business event, 
- surrounded by **dimension tables** that provide additional context![[Screenshot 2026-01-30 at 14.21.22.png]]

In some cases, you may even **connect a dimension table to more than one fact table from various star schemas.** 

>	A dimension that is reused across multiple-star schemas is called a conformed dimension. 
>	![[Screenshot 2026-01-30 at 14.20.59.png]]



- The fact tables are connected to the dimension tables through foreign keys. 
- Each dimension is defined by a primary key 
- and the fact table has its own primary key.
![[Screenshot 2026-01-30 at 14.23.28.png]]


**Surrogate Key**
The fact table's primary key  could be the natural primary key from the production tables, but **the best practice is to create a *substitute for the natural key*, also known as the "surrogate key".** This way: 
- you can combine data from different source systems that have natural primary keys written in different formats, 
- and you can decouple the primary keys of your star schema from the primary keys of the source database, which can be subject to change. 


Here's an example of a star scheme you worked with in the first lab of course 1. 
![[Screenshot 2026-01-30 at 14.33.28.png]]

- Each row in the fact table corresponds to "a product placed within an order" and contains business measures, such as the quantity ordered, price each, by price, and so on. 
- This fact table is connected to three dimension tables that provide further features related to customers, products, and the locations from which an order was placed. 

You can see that the fact table has a primary composite key consisting of the order number and the order line number, and the table contains three foreign keys to connect each of the three dimension tables. 

#### Start Schema for Analytical Queries
How exactly does the star schema help with analytical queries? 

- Well, you can start with a fact table by applying **aggregate queries** to find, for example, the sum, average, or maximum value of a particular fact measure in the fact table. 
- You can then use the dimension tables to **filter or group the facts**. 
![[Screenshot 2026-01-30 at 14.46.53.png]]For example, let's say you're interested in finding the total sales amount for each product line within the USA, so you need to sum up the order amount column in the fact table. 


To do this using SQL:
```MySQL
SELECT
	dim_products.productLine,
	SUM(fact_orders.orderAmount) AS total_sales
FROM fact_orders
JOIN dim_products ON
	fact_orders.productCode = dim_products.productCode
JOIN dim_locations ON fact_orders.postalCode = dim_locations.postal_code
WHERE dim_locations.country = 'USA'
GROUP BY dim_products.productLine;
```


#### Star Schema VS 3NF
Say you wanted to find the same information, the total sales amount for each product line within the USA, but using **the normalized version** of the data set, how would that query look? 

![[Screenshot 2026-01-30 at 14.53.07.png]]


Well, you would first need to **locate the business measures you're looking for**, meaning the total sales amount. You can get this by multiplying the price each column with the quantity ordered column and the order details table. Then you'll have to join the order details table with the products table, then the products table to the product lines table to group by the product line. Finally, you'll have to join the customers orders and orders details table to filter the results by country. While the two models contain the same information, star schema organizes the data in a way that's easier for business users to understand and navigate. It also results in simpler queries with fewer joints which speeds up the query performance. Normalized forms and star schemas both have their own use cases. 

While normalized forms ensure data integrity and avoid data redundancy, star schemas facilitate analytical workloads. 


## Inmon VS Kimball Data Modeling Approaches for Data Warehouses

Back in course three, we discussed ***data warehouses as a storage system*** that you can use to separate transactional systems from analytical systems, but we didn't discuss how the data is actually modeled within the data warehouse to support this. 

There are many approaches to modeling data for data warehouses. And the big ones you will likely encounter in your work as a data engineer are the: 
- **Kimball**, 
- **Inmon**, and
- **data vault** 
modeling approaches. 

In this section, we'll go through the Inmon and Kimball approaches, and then later this week we'll look at data vault and other data modeling techniques. 

### The "Inmon" modeling approach
Bill Inmon, who is widely known as the **father of the data warehouse**, created his approach to data modeling for data warehouses in 1989 with the goal of **separating the source system from the analytical system**. 

>	 As you've seen in course three, Inman defines a data warehouse as *"a subject-oriented, integrated, non-volatile, and time-variant collection of data in support of management's decisions."*

Here's a continuation of the definition: 

>	The data warehouse contains **granular corporate data**. Data in the data warehouse is able to be used for **many different purposes**, including sitting and waiting for future requirements, which are unknown today. 

The subject oriented and granularity aspects of this definition mean that an Inmin model organizes data into the **major subject areas of the business** and includes all the details related to those subjects. 

For instance, in an e-commerce company, you can have subjects such as products, orders, customers, shipments, and so on. And for each subject, the data model must contain all the details, such as business keys, relationships, and attributes. So with the Inman data modeling approach, you need to consolidate data from various data sources and model the data into a highly normalized form that is then stored in a data warehouse. You can then serve the data for downstream reports and analysis via **department-specific data marts**. 

![[Screenshot 2026-01-30 at 15.42.29.png]]

- With this approach, the data warehouse represents ***a single source of truth*** that supports many data use cases, even if the current analytical requirements are not yet defined. 

- This strict normalization requirement in the data warehouse reduces data duplication, leading to fewer downstream analytical errors, better data integrity, and consistency. 

Here's an example of how you can apply the Inmon modeling approach to e-commerce data.

Let's say that the business stores information about orders, inventory, and marketing in separate source systems.![[Screenshot 2026-01-30 at 15.46.08.png]]

You can ingest the data from these sources and store it in the data warehouse in a highly normalized third normal form (3NF). 
![[Screenshot 2026-01-30 at 15.46.53.png]]

To meet ***department-specific data needs***, you can then take this data from the data warehouse, model it into **various star schemas** or other appropriate models, and place them in downstream **data marts** for sales, marketing, and purchasing, with each department having its own data structure that's unique and optimized to its specific needs. 

![[Screenshot 2026-01-30 at 15.47.41.png]]

This way, data users from each department can easily query the data for their use cases. 

### The "Kimball" modeling approach
Unlike the Inmon modeling approach that uses a data warehouse to store data across a business that's been modeled into a normalized form and then serves department specific analytics through data marts, The Kimball modeling approach focuses more on modeling and serving department-specific analytics ***directly*** in the data warehouse without first normalizing the data. 

>	Created by Ralph Kimball in the early 1990s, this approach effectively allows you to serve data that's structured as star schemas or similar variants directly from the data warehouse itself, incorporating data marts into the overall warehouse architecture. 


Here's an example of how a Kimbell data warehouse can be used for e-commerce. After you ingest the data from the orders, inventory, and marketing source systems, you model the data into ***multiple star schemas*** to address the different facts of the business and then store them directly in the data warehouse. 

![[Screenshot 2026-01-30 at 15.53.28.png]]

- Kimball's approach enables **faster modeling**, hence quicker iterations, than Inmon's approach. 
- But with a trade off of potential data integrity issues because you're storing star schemas that have more data redundancy and duplications directly in the data warehouse. 

So if your organization prioritizes quick, practical insights into specific business processes and is looking for rapid implementation and iteration of their data warehouse, then I suggest you adopt Kimball's approach to data modeling. On the other hand, if **data quality** is your highest priority, or if the analysis requirements are not yet defined, then I suggest you choose Inman's data modeling approach that treats a data warehouse as a **single source of truth**. 
All data marts are then built on top of the highly normalized data warehouse to ensure data consistency and integrity. Depending on the organization, you might need to apply both the Inmon and Kimball modeling approaches when modeling data for different data warehouses. So it's important that you understand how to handle data that it's highly normalized and data that's in a star schema. 


### From Normalization to Star Schema (Example)

As a data engineer, there is a good chance you'll need to transform data that's stored in a normalized schema into a star schema. For example, you might need to extract normalized data that's stored in a relational database and model the data into a star schema so it's easier to query ***before*** loading it into department-specific data marts. 

So let's walk through an example where we convert normalized data that's in third normal form (3NF)into a star schema. 

Here's the Diagram of the normalized data we obtained in an earlier section by applying the normalization stages. 


It consists of four tables. 
- Customers, 
- orders placed by the customers, 
- the items that make up each order, 
- and the characteristics of each item. 
- I'll add another table here to represent the stores where each order was placed. 
>	*In this diagram, PK means primary key and FK means foreign key.*
![[Screenshot 2026-01-30 at 16.16.54.png]]

 Let's say you're tasked with modeling this data into a star schema to serve to the data analyst in your company. We'll follow the **four key steps** Kimball proposed for designing a star schema. 

#### The four Key Steps for creating a star schema

##### 1. Understanding Business Needs
 
 First, you need to understand the needs of the business. 
 - This helps you identify which **business events** or processes you want to model in the **fact tables**, 
 - and it'll help you declare the ***grain***, meaning the level of detail you want each row in the fact table to represent. 
 - Then, you can identify the **business measurements** or **facts** that are generated by the business processes and are associated with the grain, 
 - as well as **choose the dimensions** to provide the needed context for the facts. 
 ![[Screenshot 2026-01-30 at 16.21.43.png]]
 
 Let's say you did some requirements gathering by talking to a data analyst, and learned that they are interested in *analyzing the sales data to understand which products are selling in which stores on a given day, if there are any differences in sales between stores, and which product brands are most popular.* 

From this, you've determined that the business process that needs to be modeled is the **"company's sales transactions"**. 

So you could decide to represent the total sales transactions 
- on a particular day in each row of the fact table. 
- Or you could represent a single sales transaction, 
- or even an individual product item in a sales transaction in each row. 

When deciding on the grain, like I mentioned earlier, I encourage you to choose **the atomic grain** to capture the *lowest level of detail* of the sales transactions. This way, you'll be able to keep your system flexible enough to handle unpredicted user questions in the future. So let's declare the grain to be an individual product item in a sales transaction. 

![[Screenshot 2026-01-30 at 16.23.09.png]]

Next, you want to select the dimensions. 

Since the data analyst is interested in analyzing the sales with respect to the **stores, dates, and brands,** you can have one dimension table that represents the stores, another dimension for the item's features, and another for the date dimension. 

![[Screenshot 2026-01-30 at 16.26.15.png]]

So let's write a SQL statement to create the `dim_stores` dimension table. 


You want to select the `store_id`, `store_name`, `store_city`, and `store_zipcode` from the stores table, and you also need a `primary key` for this table. 

![[Screenshot 2026-01-30 at 16.27.25.png]]

> 	Like I mentioned in an earlier video, you typically want to generate surrogate keys and use them as the primary keys for your dimension and fact tables. This guarantee is that each row in your store schema can be uniquely identified by the primary key of the fact and dimension tables, regardless of changes that might happen to your source systems. 
> 	
> 	To generate the surrogate key for the stores dimension table, you can create a sequence of integers starting from 1, and assign one integer to each store. Or you can use a hash function that takes in the natural primary key and generates a unique surrogate key for each store. ![[Screenshot 2026-01-30 at 16.28.50.png]]



Popular database management systems such as PostgreSQL and MySQL support several hash functions. For example, MD5 is a hash function that encodes a string into a hash output. So assuming that the `store_id` from the production database is a string, I can apply the MD5 hash function to the `store_id` to generate a surrogate key for each row, and I'll label the surrogate key as `store_key`. 
![[Screenshot 2026-01-30 at 16.29.35.png]]
- If the `store_id` is actually an integer value, then you'll need to first convert it to a string. So with this SQL statement you'll end up with this store's dimension table.  
- Notice that it contains both the surrogate store key and the natural `store_id` for ease of reference and interpretability purposes. 

Next, let's create the items dimension table by selecting the SKU, name, and brand from the items table. 

Then for the primary key, you can apply the MD5 hash function to SKU, assuming that SKU is a string, and then label the surrogate key as item_key. You'll end up with this items dimension table. 
![[Screenshot 2026-01-30 at 16.31.05.png]]

Finally, let's create the `dim_date` dimension table. The idea behind the date dimension is that for each date you can create columns that specify the corresponding day of the week, month, quarter, and year like you see here. This will help the data analyst answer questions like what are the total sales in the first quarter of 2022? What products are most popular on the weekends? 

To create the `dim_date` dimension table, you can generate a series of sequential dates covering a desired period of time. 

Here's one way of generating a series of daily dates in PostgreSQL. First, you'll generate a series of dates from January 1, 2020 all the way to January 1, 2025. Then you'll extract the day of the week, month, quarter, and year from each date. So that takes care of the three dimension tables. 

![[Screenshot 2026-01-30 at 16.32.03.png]]

Finally, let's create the fact tables. 

Each row in the fact table must represent ***a product within a sales transaction.*** The facts associated with each product are the quantity sold, which you can get from the order items table, and the price of each item, which you can get from the items table. Additionally, the fact tables must contain the foreign keys that connect it to the dimension tables. In this case, those are the store_key, item_key, and date_ key, which are the surrogate keys created for each dimension table. And of course, the fact table must contain **a primary ke**y to uniquely identify each row. You could create a composite key consisting of order_id and item_line_number, but the better practice is to generate a ***surrogate key*** from the combination of these two natural keys. So the fact_order_items table should look something like this. 

![[Screenshot 2026-01-30 at 16.34.11.png]]


Now let's write the SQL query to create this fact table. For the primary key, you'll select the `order_id` and `item_line_number` from the order items table, and then concatenate them so you can apply the MD5 hash function to the concatenated string to generate the surrogate key. 

![[Screenshot 2026-01-30 at 16.35.33.png]]

You'll label this as `fact_order_key`. You'll also include the `order_id` and `item_line_number` natural keys in the fact table for reference. Then you'll join the orders table and then the items table so you can create the rest of the attributes. For the foreign keys, let's apply the hash function to the `store_id` from the orders table to create the `store_key` that links to the store dimension table. Then let's apply the hash function to the item SKU from the `order_items` table to create the item key that links to the item dimensions table. And finally, let's select the `order_date` from the orders table to create the date key that links to the date dimension table. And don't forget, we need to add the two facts. So let's select the item quantity from the order items table and the price from the items table. 


Here's the Diagram of the star schema we just created. ![[Screenshot 2026-01-30 at 16.39.05.png]]


### DB Transformation Tools
While you can write your own code to transform the data like you've done in the previous labs with AWS Glue, in this lab you'll use a popular data transformation tool called DBT, which helps you model your data by abstracting away a lot of the heavy lifting with writing pure SQL code. 

- DBT allows you to connect to your data warehouse and then transform and validate your data within the data warehouse itself. 
- It treats a modeling process as a transformation task and generates a SQL code behind the scenes to transform your data. 
- Although DBT simplifies the transformation step for you, it can only connect to **a single target,** meaning you can't use DBT to join together data from different sources, and you can't move the data to another target system after it's been transformed. 
- To join the data from multiple sources, you need to first bring the data inside the same target system. 

AWS Glue, on the other hand, allows you to **connect two different sources**, apply transformations, and store the process data somewhere else. So if you need to perform transformations on data that will be moved around, you should choose AWS glue or a similar ingestion tool over DBT. 

![[Screenshot 2026-01-30 at 16.39.49.png]]![[Screenshot 2026-01-30 at 16.40.44.png]]


## "Data Vault" modeling approach

- The Inmon and Kimball modeling approaches focus on the *structure of **business logic** in the data warehouse* 
  
- The ***"Data Vault"*** modeling approach on the other hand focuses on *separating the **structural aspects of data***, meaning:
	- the *business entities* and *how they're related* 
	- from the descriptive attributes of the data itself. 

It uses separate tables to represent core business concepts, the relationships between those concepts, and the descriptive attributes about those business concepts. 

> You can follow a Data Vault model to allow for a more *flexible, agile, and scalable data warehouse structure* by keeping the data as closely aligned to the business as possible, **even while the business and its data are changing.** 


Dan Linstedt introduced Data Vault in the 1990s as a different approach to modeling data in the data warehouse. This approach has evolved over time. Nowadays, the Data Vault architecture consists of three layers: 
- the **staging** layer, 
- the **enterprise data warehouse** layer, 
- and **the information delivery** layer. 

![[Screenshot 2026-01-30 at 17.47.06.png]]


**High-Level Workflow**
![[Screenshot 2026-01-30 at 17.01.47.png]]
- You can **load** raw data from source systems into the staging layer, *in an insert-only manner*, meaning, you don't alter the data or enforce business logic, except to ensure that the expected data type is ingested. 

- Next, you model the data in the enterprise data warehouse layer, using ***hubs, links, and satellites*** to 
	- *separate business objects and their relationships* 
	- from their descriptive attributes.
  
- Then finally, in the **information delivery layer**, you ***load*** the data into downstream **data marts** that can be modeled as star schemas or other structures to support various business areas, so, operations like aggregation and grouping, which modify the meaning of the data to meet user needs are applied in the information delivery layer. 

Unlike other data modeling approaches you've learned about so far, there's no notion of good, bad, or conformed data in a Data Vault. You only change a structure in which the data is stored. 
- This way, you can easily trace the data in the data warehouse back to its source, 
- and you can avoid having to restructure the data in the warehouse in case the business requirements change. 


### Data Warehouse Layer
Let's take a closer look at the model in the enterprise ***"data warehouse layer"***. The Data Vault model consists of **three main types of tables**. These are: 
- hubs
- links 
- satellites
#### hub
*"A hub stores a unique list of **business keys** 	to represent **a core business concept.**"*
> *This can be keys such as customers, products, employees, vendors, and others.* 

#### link
*A link **connects** two or more **hubs** to represent the relationship, transaction or event between **two or more business concepts**.* 

>	NOTE: *The links and hubs **don't contain descriptive data**, that's actually stored in the satellite tables.* 

#### satellite
*A satellite contains **the attributes that provide the descriptive context** for the hubs or other links.* 

![[Screenshot 2026-01-30 at 18.02.04.png]]

User will query a hub, which will link to a satellite table containing the query's relevant attributes. 

### 3NF to "Data Vault"
Let's go back to our e-commerce example and model the data as a *"Data Vault model"* by following three key steps.

1. **Modelling the "hubs"**
First, you need to model the hubs, which contain business keys. To identify the business keys, you can ask yourself: 

>	 *What is the identifiable business element?* 
>	 *How do users commonly look for data?* 

A business key can be a column or a set of columns that the business uses to identify and locate the data, and ***it must not be a key generated in or tied to a particular source system***. This way, you can easily integrate these business elements from different source systems. 
![[Screenshot 2026-01-30 at 18.07.09.png]]

In a e-commerce example, the business concepts you can model as **hubs** are a `customer`, `order`, `store`, and `item`. 

![[Screenshot 2026-01-30 at 18.08.00.png]]

The business keys are 
- `customer_id` for the `customer` hub, 
- `order_id` for the `order` hub,
- `store_id` for the `store` hub, and
- `sku` for the `item` hub. 
![[Screenshot 2026-01-30 at 18.09.21.png]]
- These keys uniquely identify each business element, 
- and the `store_id`, `customer_id`, and `order_id` are ***keys generated by the business*** rather than being tied to the source system. 

In addition to the business keys, a hub must also contain t**hree additional standard fields**: 
- The "***\*_hash _key***", which is a calculated column consisting of a hash of the business key column and used as a hubs primary key. 
- The ***`load_date`***, which represents the date on which the business key in a given ***row*** was first loaded into the hub, 
- and the ***record source***, which represents a source from which the unique row is obtained. 
![[Screenshot 2026-01-30 at 18.12.02.png]]

2. **Modelling the "links"**
The next thing you need to model are the links. You use a link table to ***connect two or more hubs*** to *capture the relationships between the **business keys** of the hubs.* 

Let's say you want to model the event of ***"a customer placing an order"***. You can add a link table to connect the order hub to the customer hub to show which customer placed the order.![[Screenshot 2026-01-30 at 18.15.39.png]] 

Then you can add a link table to connect the item hub to the order hub to show which items were ordered.
![[Screenshot 2026-01-30 at 18.17.17.png]]

Finally, you can add a link table to connect the order hub to the store hub to show which store the order was placed in. 
![[Screenshot 2026-01-30 at 18.17.33.png]]


> 	Each table must contain the **primary** and **business** keys from its parent hubs, the `load_date` of a row, and the **source** for the record. 
> 	
> 	For each table, the primary key consists of a **hash calculated based on the business keys of the parent hubs
> ![[Screenshot 2026-01-30 at 18.18.45.png]]


With link tables, you can
- ***easily add new relationships*** 
- or ***update a current relationship*** 
without having to re engineer the Data Vault. 


For example, let's say the company decided to completely change a sliding of business and instead of selling *items* at the sites to sell ***services***. In this case, you can create a new service hub table and a new link that connects this service hub to the order hub, and then stop using the old item hub and item order link. 

![[Screenshot 2026-01-30 at 18.20.05.png]]


Moreover, the link tables can model a many-to-many relationship. 

Let's say the organization changes the rule and now *allows more than one customer to contribute to the same order.* You can **use a link table** to model this new many-to-many relationship between the customer hub and the order hub. You don't need to change anything in the design. 
![[Screenshot 2026-01-30 at 18.22.04.png]]


3. **Modelling the "satellites"**

Now, to give meaning and descriptive context to the hubs and the links, and the third step, you can create **satellite** tables. 

- In this example, I created a satellite table for the customer hub and added some customer information, such as customer name and customer zip code. 
- You can add satellites for the store, order and item hubs as well. 
- I also created a satellite table for the item order link to add context to this relationship, namely the quantity of that item placed in the order. 

Each satellite table **must also contain** 
- the record source, 
- and for the primary key, it should consist of a hash key of the parent hub and the load date. 

![[Screenshot 2026-01-30 at 18.24.02.png]]
![[Screenshot 2026-01-30 at 18.24.22.png]]

There you have an overview of the Data Vault model. Data Vault provides a flexible design that decouples the structure of your data from the source systems, enabling you to easily adapt as a business evolves. 

With that, we covered the basics of a three most popular data modeling approaches for data warehouses Inmon, Kimball, and Data Vault. 

However, the coverage hardly does justice to their respective complexity and nuance. In the resource section, I have listed some books from the creators of each modeling approach that I highly recommend you read to further understand how and why data modeling is central to batch analytical data. 

In recent years, an approach known as **"one big table"** has emerged to model data for analytical use cases. Join me in the next section to explore one big table as the final data modeling approach will cover this week.


## One Big Table

The modeling approaches we've looked at so far this week, in particular, Kimball and Inmon were developed when data warehouses were expensive, on premises, and heavily resource constrained with tightly coupled compute and storage. 

While batch data modeling has traditionally been associated with one of these strict approaches, more relaxed approaches such as what's known as **one big table**, or **obt** for short, are becoming more common. 

With one big table, you throw all your data into a single wide table, which is exactly what it sounds like, a very wide collection of many columns typically created in a columnar database. 

![[Screenshot 2026-01-31 at 11.25.40.png]]
- A wide table can potentially have **thousands of columns** 
- and a column may be a single value or contain nested data. 
So, wide tables with one big table are highly denormalized and flexible. 

Here's an example of a wide table, which is the denormalized table you saw in an earlier video. This is just a small example, and the wide tables you'll encounter with one big table can have way more columns, and as you can see, this table combines various data types and each row represents a customer order. 
![[Screenshot 2026-01-31 at 11.27.53.png]]


You can think of one big table as the denormalize extension to Kimball's approach, where you have facts and dimensions represented in the same table. 

![[Screenshot 2026-01-31 at 11.28.35.png]]
- By doing so, you can free the data analyst from performing any complex joins, or any joins for that matter. 
- Moreover, you can run the same analytical queries **faster** on wide tables than on highly normalized data, or even on data modeled as a star schema, where you might still need to join dimension tables with the fact table. 
- The wide table simply contains all of the data you would otherwise need to join together in a more rigorous modeling approach, which can have a huge impact on scan performance. 

This one big table approach is becoming more common because of the low cost of cloud storage. Also, many organizations are choosing to design flexible schemas in their source and analytical systems by using nested data. And with one big table, you can store this nested data all together in one table without having to worry about the optimum weight represented in storage. 

![[Screenshot 2026-01-31 at 11.32.06.png]]

 I suggest using a wide table when you have a lot of data that needs more flexibility than a traditional data modeling approach might provide. When it comes to modeling your data, there's no one size fits all solution, so make sure you understand the trade offs between the possible approaches when it comes to flexibility, data integrity, and ease of use by downstream stakeholders to choose the best approach for your use case. 

## Quiz 

![[Screenshot 2026-01-31 at 18.14.24.png]]


**Optional reading and reference material:**
- Chapter 8 of [Fundamentals of Data Engineering](https://go.redpanda.com/fundamentals-of-data-engineering)
- [Building the data warehouse](https://www.amazon.com/Building-Data-Warehouse-W-Inmon/dp/0764599445), by Bill Inmom.
- [The data warehouse toolkit](https://www.amazon.com/Data-Warehouse-Toolkit-Definitive-Dimensional/dp/1118530802), by Ralph Kimball, Margy Ross
- [Building a Scalable Data Warehouse with Data Vault 2.0](https://www.oreilly.com/library/view/building-a-scalable/9780128026489/), by Daniel Linstedt, Michael Olschimke
- [Data Vault 2.0 Modeling Basics](https://vertabelo.com/blog/data-vault-series-data-vault-2-0-modeling-basics/)