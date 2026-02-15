
# Serving Data for Analytics and Machine Learning


Congratulations on reaching the last week of this program. Throughout this course, you've learned how to model your data for analytics and machine learning use cases, and what tools and technical considerations you should take into account when transforming your data. 

In this final week, we will take a look at serving data for various analytics and machine learning use cases. 

For example, with a business analytics use case, you might be serving data from a data warehouse or a data lake. 
![[Screenshot 2026-02-15 at 21.46.32.png]]

Then your end users can use that data to create dashboards or reports, or perform ad hoc analysis. 
![[Screenshot 2026-02-15 at 21.46.53.png]]

You could also serve data for operational analytics, where your end users monitor immediate trends in the data to inform immediate action. With operational analytics, you need to make sure that you serve data within the required latency. 

![[Screenshot 2026-02-15 at 21.47.05.png]]


Finally, you could serve data for embedded analytics, where your end users feed the data to a client facing, data product, or dashboard. 
![[Screenshot 2026-02-15 at 21.47.22.png]]


When it comes to serving data for machine learning applications which are becoming widely adopted in all types of companies, you will likely work with a data scientist or machine learning engineer to acquire, transform, and deliver the data necessary for model training in a format suitable for the target application. 

Earlier in the course, we looked at some examples of serving data to build a customer churn model, as well as a recommendation system. 

![[Screenshot 2026-02-15 at 21.47.56.png]]

Additionally, you could incorporate business definitions and data logic through a semantic layer that you build after you model your data. In this semantic layer, you can document definitions, such as the meaning of an active user, and derive business metrics, such as revenue, to create a common language for the data you serve. 

![[Screenshot 2026-02-15 at 21.48.15.png]]

You can also serve your data as a table, a view, or a materialized view. 

![[Screenshot 2026-02-15 at 21.48.37.png]]

We'll go through all these concepts later in the week, and you'll get a lot of practice creating views using dbt in the first lab. 

After we go through the details of serving data, I'll give you a ***summary*** of the data engineering concepts you learned in this program, revisiting the framework of how to think like a data engineer, as well as all the stages and undercurrents of the data engineering life cycle. Finally, you'll see how all these concepts come together in the capstone lab. 

![[Screenshot 2026-02-15 at 21.49.05.png]]


## Serving Data for Analytics and Machine Learning

There's more than one way to serve data to your end users. 


- Sharing data as files is one common and straightforward way to serve data. A data scientist might need a text file of customer reviews to perform sentiment analysis. 
- An analyst might need numerical data in a CSV file of invoices to perform some statistical analysis. 
- Or a machine learning engineer might use images of products to develop a product classification system. 

- Well, of course, you could serve text or numerical or image data using a database or object storage. 
  ![[Screenshot 2026-02-15 at 21.51.10.png]]

### Sharing Data As Files

In some cases, you might just share a file directly, say through an email. ![[Screenshot 2026-02-15 at 21.51.42.png]]
	- It's difficult to manage the versioning of files this way. 

- Using a **data sharing platform** helps you ensure a coherent and consistent version of the files you share with your end users. ![[Screenshot 2026-02-15 at 21.52.17.png]]

Serving single files one at a time might be sufficient for certain ad hoc requests, but this practice is very hard to scale. If you want to share large files of semi structured or unstructured data, then you would need to share your data through **object storage or a data lake**. 
![[Screenshot 2026-02-15 at 21.52.57.png]]


### Serving More than Single Data
To scale beyond sharing single files, you might choose to serve data directly from your OLAP database or data warehouse. 

In this case, an analyst or data scientists can query the storage system using SQL or another query language, and then export those results to a downstream application or analyze the results in a notebook. 

Serving data from a database has its benefits. 

![[Screenshot 2026-02-15 at 21.56.03.png]]

- A database imposes order and structure on the data by enforcing a schema. 
- Databases give you fine grain permission controls at the table, column and row level, allowing you to craft complex access policies for various roles. 
- Modern OLAP databases and query engines can offer high performance for complex, computationally intensive queries. 

### Serving data from Streaming Systems

If you're working with ***streaming data***, serving data and files may be impractical or impossible, and databases on their own might not have the functionality you need. 

In that case, you'll need to work with *streaming systems* that serve data in real time. For example, operational analytics databases are becoming increasingly popular because they allow your end users to perform analytical quarries with low latency across a large range of historical data and up-to-the-second current data. When you serve data from these databases, you are effectively combining the features of an OLAP database with a stream processing system. 

![[Screenshot 2026-02-15 at 21.57.34.png]]

### Data Management

When it comes to data management, you need to ensure that stakeholders trust the data you serve them and that they can interpret it correctly and use it in a consistent manner. So you want to ensure that the data encompasses proper data definitions and logic. 
- Data definition refers to the meaning of data as it's understood throughout the organization. 
	- For example, the definition of a term like a customer should be documented and made available to everyone who uses the data 
- Data logic consists of formulas for driving metrics from data, say gross sales or customer lifetime value. 

Proper data logic depends on proper data definitions and contains details of statistical calculations. So, for example, to compute customer churn metrics, you would need to understand what the word customer means to the end user. Then you can write a SQL query to define this metric once, and it can be reused across the organization. 

![[Screenshot 2026-02-15 at 21.58.29.png]]

This helps avoid a messy and unmaintainable sprawl of SQL code. 

So formally, declaring data definitions within your organization goes a long way to ensuring data correctness, consistency, and trustworthiness. 


## Semantic Layer

While you can model the data to help capture data definitions and logic, you can also build a ***semantic layer on top of your data model*** to translate the underlying data elements and structures into business terms that are more intuitive and useful for your end users. The semantic layer ensures a single consistent definition for each business term and helps your end users more easily navigate the data to find what they need. 

The semantic layer can live in a BI tool, or you can create this layer using something like DBT. With DBT, you can define your standard business metrics using YAML file and SQL queries. 

![[Screenshot 2026-02-15 at 21.59.33.png]]

Once your end users receive the data, they might use a visualization tool or business intelligence platform, such as Amazon QuickSite, Apache Superset, or Looker to create an analytics dashboard, or a data scientist might use notebooks to explore the data, engineer features, or train a model. 

You might also be responsible for helping set up and manage the Cloud platforms that are designed to handle Cloud data science workloads, such as Amazon Sagemaker, Google Cloud Vertex AI, and Microsoft Azure Machine Learning. 

![[Screenshot 2026-02-15 at 22.01.10.png]]

Those are several ways you can serve data for analytics and machine learning. In the next section, we'll dive into the approach for serving data from a database, focusing specifically on using views and materialized views.

## Views and Materialized Views

When you give downstream stakeholders direct access to a database, in addition to serving the data as tables, you can also serve data using table-like objects such as views and materialized views. 

You can create these objects during the **transformation stage** in your data pipeline or before you serve the data to your end users in the **data consumption layer.** 

>	*A view is just a query that you can store in your database to give you and your stakeholders easier access to common queries, and it can also help simplify the process of writing complex queries.* 

Here's an example of how you can create a view. You first type the keywords `CREATE VIEW` followed by the name of the view along with the `AS` keyword. 

```MySQL
CREATE VIEW customer_info AS
SELECT
	first_name,
	last_name,
	email,
	phone,
	city,
	postal_code,
	country
FROM
	customer
	JOIN address ON ..
```


The view represents a ***virtual table***, not a physical table that you or the end users can select from, just like any other table. 

When you select from a view
- the database creates a new query that combines the view with the query that referenced it, 
- and then the query optimizer optimizes and runs the full query. 

Suppose that a marketing analyst needs to ***frequently run a query*** on the results from joining the customer address, city and country tables. 

![[Screenshot 2026-02-15 at 22.09.30.png]]

When you create this customer info view, you are joining together the four tables into a wide table. 

![[Screenshot 2026-02-15 at 22.09.46.png]]

And so the marketing analyst can simply write queries that filter and perform aggregations on top of this view, rather than having to write a query to join these tables together themselves every time. 

### Views for Security Principles

You can also use *views* to apply security principles when serving your data.

For example, you can create a view that selects **only specific columns and rows.** Then, when you serve your downstream stakeholders with this view, you are effectively *restricting* their data access to only the data that they need. 

![[Screenshot 2026-02-15 at 22.11.31.png]]

### CTE VS Views

CTE or common table expressions that you learned back in Course 3 is a SQL concept that's similar to views. 

Remember that you can create a CTE using the with clause followed by the name of the CTE and then a query enclosed in parentheses. But that query represents some temporary results that you want to reference in a subsequent SQL query, so both CTEs and views help organize the code by making it cleaner and easier to follow. 

- However, CTEs only exist *within the scope of the main query* where they're referenced, so once the main query is executed, the CTE is discarded and cannot be referenced in other queries. 
- On the other hand, a view is an actual database object that can be accessed by external database users. 

So the body of the query that represents the view ***is actually stored in the database disk***, and it can persist in the database until you explicitly drop it. So views can be referenced and used by your end users across different sessions and queries. 


### Materialized Views

Now, with views, **you can't perform and store any pre-computation**, meaning that the query represented by the view **needs to be executed every time the view is referenced**. So using a view to store a complex query that your end users need to run frequently can be ***extremely expensive***. 

A **"*materialized view*"**, on the other hand, does some or all the view computations in advance. It then caches the query results and allows you to refresh the data periodically. 

Here's an example of how you can create a materialized view. 

![[Screenshot 2026-02-15 at 22.16.54.png]]

- You start with the `CREATE MATERIALIZED VIEW` keywords. 
- Then you give the materialized view a name followed by the `AS` keyword. I'll call this materialized view rental_by_category. 
- Then you specify the query you want to represent with this materialized view. 
- For this example, I want to `JOIN` together the payment, rental, inventory, film, film category, and category tables. 
- Then I'm going to `SELECT` the `category.name` and the `sum` of the payments. 
- Then `GROUP BY` the categories, the query is executed, **and the results of the six table joins are saved and cached.** 

Then, when a user references this rental by category materialize view, they're querying from the ***prejoined data***. 

A materialize view is useful when you can tolerate **some amount of latency** between the refreshes. 

