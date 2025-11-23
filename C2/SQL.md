
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


## References
<a id="1">[1]</a> SQL Tutorial, W3schools, 2022. [Online]. Available: <https://www.w3schools.com/sql/>. 

