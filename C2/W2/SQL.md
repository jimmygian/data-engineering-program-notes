
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

