
# RAW SQL Execution

There are various ways to execute SQL commands. We will begin by exploring the "raw" execution of SQL commands. While Python libraries and frameworks, such as SQLAlchemy and Django ORM have been developed to provide abstraction and simplify interactions with databases, it is important to first understand the fundamental "raw" method of executing SQL commands. This foundational knowledge will help you appreciate the abstractions and make informed decisions when using higher-level tools.


## CRUD Operations

CRUD stands for **Create, Read, Update, and Delete**, which are basic operations for manipulating data. When we talk about databases, we use `INSERT INTO`, `SELECT`, `UPDATE`, and `DELETE` statements respectively to refer to CRUD operations.

###### 1. CREATE TABLE
**Before** using the statements for CRUD operations, you will see the `CREATE TABLE` statement which is used to create a new table in a database. You must specify the name of the columns, and the data type for each column. You can check the full list of data types [here](https://dev.mysql.com/doc/refman/8.0/en/data-types.html).

```sql
CREATE TABLE table_name (
	column1 datatype,
	column2 datatype,
	column3 datatype,
	...
);```


##### 2. INSERT INTO
The `INSERT INTO` statement is used to insert **new rows** in a table.

You could insert new rows *without* specifying some columns, but you will have to write the column names and values that you want to insert. That's useful when some columns are filled automatically by the default value of the column or when the column is of `SERIAL` data type.

```sql
INSERT INTO table_name (
	column1,
	column2,
	column3,
	...
)

VALUES (
	'value1',
	'value2',
	'value3',
	...
);
```


If you are adding a row to a table and will be specifying all the column values, then you don't need to specify the column names and can just specify the values to be inserted.

```sql

INSERT INTO table_name
VALUES (
	'value1',
	'value2',
	'value3',
	...
);
```


##### 3. UPDATE

The `UPDATE` statement is used to change the values of some columns on existing rows in a table. You could use the `WHERE` clause to filter the rows you want to change.

```sql
UPDATE table_name
SET
	column2 = 'value2',
	column3 = 'value3',
	...
WHERE column1 = 'value1';
```


##### 4. DELETE

The `DELETE` statement is used to delete existing rows in a table. It also goes along with the `FROM` clause to indicate the table where you want to delete the rows. You could use the `WHERE` clause to filter the rows you want to delete.

**You have to be careful because you will delete all rows of a table if you don't specify a condition:**

```sql
DELETE FROM table_name;
```

That's why you should add a condition unless you want to delete all rows:

```sql
DELETE FROM table_name
WHERE column1 = 'value1';
```



## BASIC QUERY Operations


##### SHOW, DESCRIBE
The `SHOW tables;`  command shows you the tables of the active database.

```sql
SHOW tables;
```

Once you have the list of tables, you can describe a specific table to see its schema:

```sql
DESCRIBE <TABLE_NAME>;
```


##### SELECT .. FROM, LIMIT

The `SELECT` statement is used to get data from a database. It also goes along with the `FROM` clause to indicate the table you want to query.

You could specify the columns of the table you want to retrieve from the query by listing each one as follows:

```sql
SELECT
	column1,
	column2,
	column3,
	...
FROM table_name;
```

  
What's more, you could use `*` to get all the columns from the table:

```sql
SELECT *
FROM table_name;
```


The `LIMIT` clause is used to limit the number of rows the query is returning.

```sql
SELECT *
FROM table1
LIMIT 1;
```

###  Logical Operators
##### WHERE
The `WHERE` clause is used to filter data based on a condition. In the end, the query will return the rows which satisfy the condition.

```sql
SELECT *
FROM table_name
WHERE column1 = 'value1';
```



`WHERE` clause is used to filter data based on a condition. If you want to use multiple conditions you will need to use logical operators.

The `AND` operator returns the records for those that meet all the conditions applied in the operator.

```sql
SELECT
    *
FROM table_name
WHERE
    column1 = 'value1'
    AND column2 < 'value2'
    AND column3 > 'value3';
```

The `OR` operator returns the records for those that meet **at least one of** the conditions applied in the operator.

```sql
SELECT
    *
FROM table_name
WHERE
    column1 = 'value1'
    OR column2 < 'value2'
    OR column3 > 'value3';
```

The `NOT` operator is used in a condition to reverse its result.

```sql
SELECT
    *
FROM table_name
WHERE NOT column1 = 'value1';
```



## SQL Clauses

##### ALIASES

Aliases temporarily change the name of a column. They allow you to use and display a more readable name for the columns. To create an alias you could use the keywords `AS` next to a column to change its name.

It is optional and mainly serves to improve clarity.

```sql
SELECT
    column1 AS Alias_Column_1,
    column2 AS Alias_Column_2,
    column3 AS Alias_Column_3,
    ...
FROM table_name;
```


##### JOIN

The `JOIN` clause is used to combine the data from multiple tables based on a shared column between the tables.

```sql
SELECT
    *
FROM table1
JOIN table2 ON table1.column1 = table2.column2;
```

By default, the JOIN clause is equivalent to `INNER JOIN`, which combines rows from two tables based on a related column between them, returning only the rows with matching values in the specified column in both tables. There are also other types of joins:

- `LEFT JOIN`: Returns all the rows from the left table, and the matching rows from the right table. Rows in the left table that don't have matches in the right table will include nulls for the right table columns.

- `RIGHT JOIN`: Returns all the rows from the right table, and the matching rows from the left table. Rows in the right table that don't have matches in the left table will include nulls for the left table columns.

- `FULL JOIN`: Combines the results of both `LEFT JOIN` and `RIGHT JOIN`, returning all rows from both tables with matches where available. If there's no match, the missing side will contain nulls in the columns.

Example:
```sql 
SELECT
    staff.first_name AS name,
    staff.last_name AS surname,
    country.country,
    city.city,
    address.address
FROM staff
JOIN address ON staff.address_id = address.address_id
JOIN city ON city.city_id = address.city_id
JOIN country ON city.country_id = country.country_id
WHERE staff.active = 1;
```


##### GROUP BY

The `GROUP BY` SQL statement groups rows that have **the same values in specified columns** into summary rows. It is often used with aggregate functions (`COUNT`, `MAX`, `MIN`, `SUM`, `AVG`) to perform calculations on each group of data. So you can use it to help answer questions like "What is the average film length by film release year?".

```sql
SELECT
    column1,
    COUNT(column2),
    SUM(column3)
FROM table1
GROUP BY column1;
```

Example:
```sql
SELECT rating, COUNT(title) AS film_count
FROM film
GROUP BY rating;
```


##### ORDER BY

The `ORDER BY` clause is used to sort the rows in ascending or descending order based on one or more columns. By default, this clause will sort the rows in ascending order, but you could use the `DESC` keyword to order the rows in descending order.

```sql
SELECT
    *
FROM table1
ORDER BY column1 DESC;
```


Example:
```sql
SELECT category.name AS category, COUNT(film.title) AS film_counts
FROM film
JOIN film_category ON film.film_id = film_category.film_id
JOIN category ON film_category.category_id = category.category_id
GROUP BY category
ORDER BY film_counts;
```


## Advanced SQL Queries 

Let's go through these advanced SQL statements. 
![[Screenshot 2026-01-11 at 10.39.33.png]]


Here's the ERD (Entity Relationship Diagram) of the data we'll be looking at as an exampe:

![[Screenshot 2026-01-11 at 10.41.08.png]]

It contains the same information as the DVD rental database you worked with in Course 2, but here it's organized into what's known as a **star schema**. You'll learn more about this data model in the next course. 

The middle Fact table called `fact_rental` contains information for each rental transaction made by a customer, such as the rental date, return date, amount paid, the ID of the rented firm, it's category ID, the idea of staff that serve the customer, and so on. 

The other surrounding dimension tables contain more detailed information about the customers, films, film categories, actors, as well as the stores and the staff. 

To walk you through these advanced eQL statements, I'll only be focusing on the rental fact table and the dimension tables for the customer, staff, film, and film category. 
![[Screenshot 2026-01-11 at 10.43.04.png]]

#### `EXPLAIN` a query
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


#### `DISTINCT`
Let's say you're interested in knowing which staff member served which customer.

```SQL
SELECT fact_rental.staff_id, fact_rental.customer_id
FROM fact_rental
```

From the fact rental table, I'll select the staff ID and the customer ID. This query returns all pairs of staff and customer IDs. 

But since the customer could be served by the same staff member multiple times, the result will most likely contain repeated pairs of staff and customer IDs. 

You can add the SQL keyword `DISTINCT` to the select statement here to ensure that the result contains only unique pairs of staff and customer IDs. 

```SQL
SELECT DISTINCT fact_rental.staff_id, fact_rental.customer_id
FROM fact_rental
```


#### `CASE` Statement

The CASE statement works like an `if` statement in programming. It will evaluate some conditions and return a value when the first condition is met. If no conditions are met it will return the value used in the `ELSE` clause.

```sql
SELECT
    CASE
        WHEN column1 < 0 THEN 'value1'
        WHEN column1 > 0 THEN 'value2'
        ...
        ELSE 'value3'
    END
    column2
FROM table_name;
```

Now, let's say you want to check whether a customer made an on-time payment, meaning that they paid for the DVD rental **before** they actually returned the DVD. 

In the `fact_rental` table, you could **compare** the `payment_date` and `return_date` columns for individual records. But to make this easier, let's use the SQL `CASE` statement to create a column that contains a `1` if the payment date is before the return date and a `0` otherwise. 

The CASE statement starts with a `CASE` keyword and finishes with the `END` keyword. 

```MySQL
CASE
...
END
```

In-between these two keywords, you can use the 
- `WHEN` keyword to specify the condition 
- and the `THEN` keyword to specify the result you want to associate with that condition. 
- After you list all the desired conditions, you can use the `ELSE` keyword to specify the result to return if none of the listed conditions are met. 

```MySQL
CASE
WHEN cond1 THEN result1
WHEN cond2 THEN result2
...
ELSE result3
END
```


Let's use the statement to create the indicator column. 

```MySQL
SELECT
fr.customer_id,
fr.rental_id,
(CASE
WHEN fr.payment_date < fr.return_date THEN 1
ELSE 0
END) AS on_time_payment
FROM fact_rental AS fr
LIMIT 5
```


Now, let's ***filter*** these results so that you can only see the results 
- for customers who are located in the **United States** and **Canada**, 
- and for any rentals that occurred between May 24th, 2005, and July 26th, 2005. 

```MySQL
SELECT
fr.customer_id,
fr.rental_id,
(CASE
WHEN fr.payment_date < fr.return_date THEN 1
ELSE 0
END) AS on_time_payment
FROM fact_rental AS fr
JOIN dim_customer AS c ON c.customer_id = fr.customer_id
WHERE c.country = "United States" OR c.country = "Canada"
LIMIT 5
```

Here, we are joining the `dim_customer` table that contains the information we need, and we then filter the countries that we are interested in. Instead of using `OR` and passing the results we want one by one, we can use `IN` statement and pass on a list of countries (similar to Python's expression).

```MySQL
SELECT
fr.customer_id,
fr.rental_id,
(CASE
WHEN fr.payment_date < fr.return_date THEN 1
ELSE 0
END) AS on_time_payment
FROM fact_rental AS fr
JOIN dim_customer AS c ON c.customer_id = fr.customer_id
WHERE c.country IN ("United States", "Canada")
LIMIT 5
```


Next, to check the date, you can use the `BETWEEN` operator to check if the rental date is between 2005-05-24, which is May 24th, and 2005-07-26, which is July 26th. 

```MySQL
SELECT
fr.customer_id,
fr.rental_id,
(CASE
WHEN fr.payment_date < fr.return_date THEN 1
ELSE 0
END) AS on_time_payment
FROM fact_rental AS fr
JOIN dim_customer AS c ON c.customer_id = fr.customer_id
WHERE c.country IN ("United States", "Canada")
AND
(fr.rental_date BETWEEN "2005-05-24" and "2005-07-26")
LIMIT 5
```

So far, we have discovered 
- the `SELECT DISTINCT`statement, 
- some **SQL string functions**, 
- Boolean expressions, 
- and the case statement.


### Common Table Expressions (CTEs)

A **Common Table Expression (CTE)** is used to create a temporary result set that can be referenced in another query. It only exists during the execution of a query.

```sql
WITH temp_results AS (
    SELECT
        column1,
        column2
    FROM table1
    WHERE column2 = 'value1'
)
SELECT
    *
FROM table2
INNER JOIN temp_results
ON temp_results.column1 = table2.column1;
```

Deeper dive:

We have these two queries from previously:

```MySQL
SELECT DISTINCT
fr.staff_id,
CONCAT (staff.first_name,' '. staff.last_name) AS staff_name,
fr.customer_id
FROM fact_rental as fr
JOIN dim_staff as staff
ON fr.staff_id = staff.staff_id
```

```MySQL
SELECT fr.customer_id, fr.rental_id,
(CASE 
	WHEN payment_date < return_date THEN 1
	ELSE 0
END) AS on_time_payment
FROM fact_rental as fr
JOIN dim_customer as c
ON c.customer_id = fr.customer_id
WHERE c.country IN ("United States", "Canada")
AND (fr.rental_date BETWEEN "2005-05-24" AND "2005-07-26")
```

Let's say you want to perform some additional computations on top of each of the above two queries. 

- In the first example, you might need to know the total number of customers that were served by each staff member, 
- In the second example, you might want to compute for each customer the average of the on-time payment column to get the percentage of on-time payments for each customer. 

In both examples, I don't want to store these results in a separate table. I just need to be able to reference these ***temporary results*** to perform my computations. To do that, you can use **common table expressions or "CTE"s** to define these **temporary result sets** that can be referenced elsewhere in the query. 

Let's take a look at the first example and compute the total number of customers that were served by each staff member. 

```MySQL
WITH <result_table> AS (
	<EXPRESSION_TO_GET_RESULT_TABLE>
) 
SELECT <whatever>
FROM <result_table>
GROUP BY ...
LIMIT ...
```


```MySQL
WITH staff_customer_pairs AS (
	SELECT DISTINCT
	fr.staff_id,
	CONCAT (staff.first_name,' '. staff.last_name) AS staff_name,
	fr.customer_id
	FROM fact_rental as fr
	JOIN dim_staff as staff
	ON fr.staff_id = staff.staff_id
)
SELECT staff_name, COUNT(customer_id)
FROM staff_customer_pairs
GROUP BY staff_name
```

- To define the CTE, you start `WITH` the with keyword. 
- Then you specify a variable `<name>` for the CTE, which we'll call `staff_customer_pairs`, followed by `AS` keyword and the **query that represents the temporary results** *enclosed* between two parentheses. 
- Within these parentheses, I'll write the query you saw above that gets us the unique staff and customer pairs. 
- Once you define your CTE, you can query it in the same way you query any table. 
	- I'll write a regular query to select the staff name and count the customer IDs from the staff customer pairs CTE and then `GROUP BY` the staff name. 
![[Screenshot 2026-01-14 at 14.19.03.png]]

You can see that there are only two staff members, and they both served the same number of customers. 


Now, let's move on to the second example to compute the percentage of on-time payments for each customer. 

```MySQL
WITH customer_payment_info AS (
	SELECT fr.customer_id, fr.rental_id,
	(CASE 
		WHEN payment_date < return_date THEN 1
		ELSE 0
	END) AS on_time_payment
	FROM fact_rental as fr
	JOIN dim_customer as c
	ON c.customer_id = fr.customer_id
	WHERE c.country IN ("United States", "Canada")
	AND (fr.rental_date BETWEEN "2005-05-24" AND "2005-07-26")
), customer_percent_on_time_payment AS (
	SELECT customer_id, AVG(on_time_payment) AS percent_on_time_payment
	FROM customer_payment_info
	GROUP BY customer_id
)
SELECT MAX(percent_on_time_payment)
FROM customer_percent_on_time_payment

```
- Here, I am creating another CTE out of the first one.

```plaintext
SELECT <something> FROM <resulting table from query2 that came from computing on top of resulting table of query1> FROM <resulting table from query2>
```


With CTEs, you saw that you can define your temporary results using the with keyword and you can query from the CTE similarly to how you would from any table. 


### Subqueries

You can also incorporate some temporary results within your main query using **subqueries**. 

A **subquery** is a query nested inside another query. The inner queries will be executed first and their results will be passed into the outer queries in order to be executed.

```sql
SELECT
    *
FROM table1
WHERE column1 > (
    SELECT AVG(column1)
    FROM table1
);
```

Let's focus on the film dimension table. 
![[Screenshot 2026-01-14 at 14.56.51.png]]

You can see that each film has a certain `length`, and let's say you're interested in getting the IDs of the films that have length greater than the average length. 

You can start by getting the average length of the film by selecting average length from dim film. 

```MySQL
SELECT AVG(length) FROM dim_film
```

This query returns a single number, which represents the average of the film length. 

You can then incorporate this query as a **subquery** within the main query to return the IDs of the film with length greater than this average. 

```MySQL
SELECT film_id, length 
FROM dim_film 
WHERE length > (
	SELECT AVG(length) FROM dim_film
)
```
- I can get this average by writing the subquery that returns the average length of the films and enclose it within parentheses. 


## SQL Window Functions

The last type of query I'd like to go over are **SQL window functions.** 

>	*A "SQL Window Function" is type of query that allows you to apply an aggregate or ranking function over a particular window or set of rows.* 

Window functions **aggregate the query results** *without* reducing the number of rows returned by the query.

Considering data from the sales department, let's say you want to get the maximum amount of units sold for every month without grouping the rows. For example, you have the following data.

| **id** | **month** | **units** |
| ------ | --------- | --------- |
| 1      | 1         | 1         |
| 2      | 1         | 2         |
| 3      | 1         | 3         |
| 4      | 2         | 2         |
| 5      | 2         | 4         |
| 6      | 2         | 6         |
| 7      | 3         | 4         |
| 8      | 3         | 8         |
| 9      | 3         | 12        |
| 10     | 4         | 8         |
| 11     | 4         | 16        |
| 12     | 4         | 24        |
| ...    | ...       | ...       |

If you apply this query to get the desired result:

```sql
SELECT
    id,
    month,
    units,
    MAX(units) OVER (
        PARTITION BY month
    ) AS units_maximum
FROM sales;
```

Then you should get the following output.

| **id** | **month** | **units** | **units_maximum** |
| ------ | --------- | --------- | ----------------- |
| 1      | 1         | 1         | 3                 |
| 2      | 1         | 2         | 3                 |
| 3      | 1         | 3         | 3                 |
| 4      | 2         | 2         | 6                 |
| 5      | 2         | 4         | 6                 |
| 6      | 2         | 6         | 6                 |
| 7      | 3         | 4         | 12                |
| 8      | 3         | 8         | 12                |
| 9      | 3         | 12        | 12                |
| 10     | 4         | 8         | 24                |
| 11     | 4         | 16        | 24                |
| 12     | 4         | 24        | 24                |
| ...    | ...       | ...       | ...               |

It's similar to performing **aggregation** using `GROUP BY` but instead of considering all rows at once, it applies the aggregation **to a subset of rows**, and it also doesn't group rows into a single output row. Each row remains separate. 

#### RANKING_FUNCTIONS
Let's start with a ranking function. 

##### `RANK()` and `ROW_NUMBER()`

```MySQL
SELECT column_name1, ranking_function() OVER (
	PARTITION BY column_name1
	ORDER BY column_name3
) AS new_column
FROM table_name;
```

To define the window, you use the `OVER` clause, which expects two pieces of information: 
1. The column you want to partition the rows by, 
2. and the column you want to rank the rows by. 

To rank each row in each window, you can choose from **several ranking functions** such as: 
- `rank()` 
- `row_number()` 

Let's go over an example. 
![[Screenshot 2026-01-14 at 15.08.35.png]]
First, I need a query that I can use as a CTE to apply the window function. Let's write a query that computes the average duration and days that a customer spent on a film category. I'll focus on the rental fact table and the category dimension tables. 

```MySQL
WITH customer_info AS (
	SELECT
	fr.cutomer_id,
	c.name,
	AVG(datediff(return_date, rental_date)) AS average_rental_days
	FROM fact_rental AS fr
	JOIN dim_category AS c
	ON fr.category_id = c.category_id
	GROUP BY fr.customer_id, c.name
	ORDER BY fr.customer_id, average_rental_days DESC
)
SELECT customer_id, name, average_rental_days,
rank() OVER (PARTITION BY customer_id ORDER BY average_rental_days DESC)
AS rank_category
FROM customer_info
ORDER BY customer_id, rank_category
```


- The `rank()` function assigns the same rank to the rows where there's a tie. 
- The `row_number()` function, on the other hand, assigns different ranks when there's a tie. 

For the same window function, if instead of the rank function you use any aggregate function such as the sum of the average rental days, it will return the running sum over each window. In the results here, you can see that you have the customer IDs and the category names like before, but now the running sum column shows the total average rental days for this film category along with all the film categories before it. There are other window functions such as lead and lag that you can check out in the optional part of this lab. Now it's your turn to try out the lab. After that, join me in the next series of videos to see how some of these SQL statements are processed behind the scenes and explore strategies you can use to improve their performance.

#### `LAG()`

The `LAG` function is a window function used to get the value of a row that comes before the current row at a given offset. Have a look at the example:


```sql
SELECT
    id,
    column1
    LAG(column1) OVER (ORDER BY id) AS prev_column1
FROM table_name;
```

Query result:

| **id** | **column1** | **prev_column1** |
| ------ | ----------- | ---------------- |
| 1      | a           |                  |
| 2      | b           | a                |
| 3      | c           | b                |
| ...    | ...         | ...              |
The `LAG` function can also take an optional second parameter which specifies the offset from the current row. By default, this offset is 1, but you can set it to any integer value. For example, `LAG(column1, 2) OVER (ORDER BY id)` would return the value from two rows before the current row.



## Pivot Tables

A **pivot table** is a term used to describe a rotated table with one or more categories as columns used to aggregate the items by a specific value.

For example, let's say you want to pivot the following table by `category` while adding the `value`.

| **id** | **category** | **value** |
| ------ | ------------ | --------- |
| 1      | category1    | 1.0       |
| 1      | category1    | 1.5       |
| 1      | category2    | 1.0       |
| 1      | category2    | 1.5       |
| 2      | category1    | 2.0       |
| 2      | category1    | 2.5       |
| 2      | category2    | 2.0       |
| 2      | category2    | 2.5       |
| 3      | category1    | 3.0       |
| 3      | category1    | 3.5       |
| 3      | category2    | 3.0       |
| 3      | category2    | 3.5       |
| ...    | ...          | ...       |

Then you should get the following result.

| **id** | **category1** | **category2** |
| ------ | ------------- | ------------- |
| 1      | 2.5           | 2.5           |
| 2      | 4.5           | 4.5           |
| 3      | 6.5           | 6.5           |
| ...    | ...           | ...           |


## SQL Functions

Depending on the [Database Management System (DBMS)](https://www.ibm.com/docs/en/zos-basic-skills?topic=zos-what-is-database-management-system) that you use, there can be some differences with the function names to handle things.

For this section we are going to explore MySQL's functions -- other databases should have similar functions to handle these things.

### Date Manipulation Functions

#### `EXTRACT()`

The `EXTRACT` function is used to get the year, month, week, and other date information from a date or time value. Other DBMS such as PostgreSQL have the `DATE_PART` function for the same purpose.

Example:

```MySQL
SELECT
    EXTRACT(SECOND FROM TIMESTAMP '2022-03-04 01:02:03') AS second_value,
    EXTRACT(MINUTE FROM TIMESTAMP '2022-03-04 01:02:03') AS minute_value,
    EXTRACT(DAY FROM TIMESTAMP '2022-03-04 01:02:03') AS day_value,
    EXTRACT(YEAR FROM TIMESTAMP '2022-03-04 01:02:03') AS year_value;
```

#### `TIMEDIFF()`


## String Manipulation Functions

Now, let's include the first and last name of the staff member, which you can find in the staff dimension table. I'll `JOIN` the `fact_rental` table with the `dim_staff` table based on the `staff_id` column. I'll add the staff first name and staff last name to the select statement here. 

```MySQL
SELECT DISTINCT fr.staff_id, fr.customer_id, 
CONCAT(staff.first_name, ' ', staff.last_name) AS staff_name
FROM fact_rental AS fr
JOIN dim_staff AS staff ON fr.staff_id = staff.staff_id;
```

You can concatenate the first and last names into one string. 

>	Depending on the database management server, the syntax for string concatenation might look different. Here I'm working with a MS SQL database where I can use the `CONCAT()` function to combine the first and last names. 

Other than concatenating two strings, you could also apply other string manipulation functions such as `LOWER()` to convert the string to lower case or `UPPER()` to convert it to uppercase. 

You can also use the `SUBSCTRING()` function to extract a part of a string. The `SUBSTRING` function is used to get part of a string value by defining a start position and a length. 

For example, to return *the first letter of the last name*, I'll apply the `SUBSTRING()` function to the staff last name. 
```MySQL
SELECT DISTINCT fr.staff_id, fr.customer_id, 
CONCAT(staff.first_name, ' ', SUBSTRING(staff.last_name, 1, 1)) AS staff_name
FROM fact_rental AS fr
JOIN dim_staff AS staff ON fr.staff_id = staff.staff_id;
```
> This function expects two arguments, the start position and the number of characters to extract. 




## References
<a id="1">[1]</a> SQL Tutorial, W3schools, 2022. [Online]. Available: <https://www.w3schools.com/sql/>. 


