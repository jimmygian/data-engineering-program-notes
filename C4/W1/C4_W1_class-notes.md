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

[TBC]