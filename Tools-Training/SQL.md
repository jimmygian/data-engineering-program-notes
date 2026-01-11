
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


##### WHERE
The `WHERE` clause is used to filter data based on a condition. In the end, the query will return the rows which satisfy the condition.

```sql
SELECT *
FROM table_name
WHERE column1 = 'value1';
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

The `GROUP BY` SQL statement groups rows that have the same values in specified columns into summary rows. It is often used with aggregate functions (`COUNT`, `MAX`, `MIN`, `SUM`, `AVG`) to perform calculations on each group of data. So you can use it to help answer questions like "What is the average film length by film release year?".

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


# Advanced SQL Queries 

Let's go through these advanced SQL statements. 
![[Screenshot 2026-01-11 at 10.39.33.png]]


Here's the ERD (Entity Relationship Diagram) of the data we'll be looking at as an exampe:

![[Screenshot 2026-01-11 at 10.41.08.png]]

It contains the same information as the DVD rental database you worked with in Course 2, but here it's organized into what's known as a **star schema**. You'll learn more about this data model in the next course. 

The middle Fact table called `fact_rental` contains information for each rental transaction made by a customer, such as the rental date, return date, amount paid, the ID of the rented firm, it's category ID, the idea of staff that serve the customer, and so on. 

The other surrounding dimension tables contain more detailed information about the customers, films, film categories, actors, as well as the stores and the staff. 

To walk you through these advanced eQL statements, I'll only be focusing on the rental fact table and the dimension tables for the customer, staff, film, and film category. 
![[Screenshot 2026-01-11 at 10.43.04.png]]


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

#### String Manipulation Functions

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

You can also use the `SUBSCTRING()` function to extract a part of a string. For example, to return *the first letter of the last name*, I'll apply the `SUBSTRING()` function to the staff last name. 
```MySQL
SELECT DISTINCT fr.staff_id, fr.customer_id, 
CONCAT(staff.first_name, ' ', SUBSTRING(staff.last_name, 1, 1)) AS staff_name
FROM fact_rental AS fr
JOIN dim_staff AS staff ON fr.staff_id = staff.staff_id;
```
> This function expects two arguments, the start position and the number of characters to extract. 



#### `CASE` Statement
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


## References
<a id="1">[1]</a> SQL Tutorial, W3schools, 2022. [Online]. Available: <https://www.w3schools.com/sql/>. 


