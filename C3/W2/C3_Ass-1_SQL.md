Lab Link: 
https://www.coursera.org/learn/data-storage-and-queries/programming/TwRws/assignment-3-advanced-sql-queries

# Advanced SQL queries

As a Data Engineer, you will have to manipulate data to process it and help the organization find valuable insights. To do so, you should learn the basics of SQL to work with data in different ways.

This time, you will be working with a DVD rental sample dataset where you will find information about stores, customers, and rented films. Below you will find the diagram with the relationships between the tables and a brief description of each one.

The purpose of this assignment is to answer business questions using SQL language to query the database. The idea is that you get the same answer that is shown in each exercise.

**After this assignment you'll be able to:**

- Build SQL queries using several techniques to manipulate data.
- Distinguish between Common Table Expressions and Subqueries.
- Apply the `DATE_PART` function to manipulate date values.
- Make use of the `LAG` and `LEAD` functions to access values from other rows when making a calculation.
- Utilize the `CASE` statement to return different values based on conditions.
- Use window functions on SQL to aggregate data within a specific window.
- Understand pivot tables and identify how they are built.


<a id='1'></a>
## 1 - Database

You will be working with a modified version of the [Sakila Sample Database](https://dev.mysql.com/doc/sakila/en/), which is licensed under the [New BSD license](https://dev.mysql.com/doc/sakila/en/sakila-license.html).

For learning purposes, let's assume that the data belongs to _Rentio_, a fictitious company dedicated to renting movies to clients from all around the world. The company has a Dimensional Model created to perform analytical queries based on the data of the staff and the stores, including inventory, films, and paying customers. The management team would like to make informed decisions based on the available data.

Your task will be to **design and build analytical queries** based on Rentio's Dimensional Model stored in their Data Warehouse.

### 1.1 - Tables

Rentio's Dimensional Model includes the following tables.

- `fact_rental`: Contains the amount of the payment for a particular rental registry as the metric. This rental registry associates a film with its corresponding category, a customer, a rental date and a payment date. It is also related to the store where the film was rented and the staff member who rented it.
- `dim_film`: Contains information about films including the title, language, rating, and release year.
- `dim_customer`: Contains customer data including name, address, and email.
- `dim_store`: Contains store data such as the manager and store address.
- `dim_staff`: Contains staff data such as first name, last name, stores where they work, and staff status (active or not).
- `dim_category`: Stores the relationship between the film and the category it belongs to. Contains the `category_id` and `name` for each category: Action, Comedy, Documentary, Sci-Fi, among others.
- `dim_actor`: Contains the actor's data such as first and last name.
- `bridge_actor`: A bridge table relating each `actor_id` with each `rental_id`.

Here you can find the entity-relationship model (ERM) of the star schema showing all the tables and how they are related:

![[Screenshot 2026-01-14 at 15.23.26.png]]

Exercise 1:
```MySQL
%%sql
SELECT
    fact_rental.store_id,
    dim_category.name AS category_name,
    SUM(fact_rental.amount) AS total_amount
FROM 
    dim_category
    INNER JOIN fact_rental ON fact_rental.category_id = dim_category.category_id
WHERE 
    fact_rental.rental_date BETWEEN "2005-06-01" AND "2005-08-01"
    AND dim_category.name IN ("Travel", "Family", "Children")
GROUP BY 
    fact_rental.store_id, 
    dim_category.name
ORDER BY 
    fact_rental.store_id, 
    dim_category.name 
```


Exercise 2:

Write an SQL query using a CTE to get the **average number of films per category**. 
Then, **calculate the average rounded down and rounded up**.

```MySQL
WITH film_category AS (
    SELECT DISTINCT 
        fr.category_id, 
        fr.film_id
    FROM
        fact_rental AS fr
),
film_category_count AS (
    SELECT
        fc.category_id,
        COUNT(fc.film_id) AS films
    FROM
        film_category AS fc
    GROUP BY
        fc.category_id
    ORDER BY
        fc.category_id
),
films_average_by_category AS (
    SELECT
        AVG(film_category_count.films) AS average_by_category
    FROM
        film_category_count
)
SELECT
    films_average_by_category.average_by_category AS average,
    FLOOR(films_average_by_category.average_by_category) AS average_down,
    CEIL(films_average_by_category.average_by_category) AS average_up
FROM
    films_average_by_category;
```


Exercise 3:

Write an SQL query using a subquery to **get the film categories** that have the number of films **above the average rounded up calculated in the previous exercise**.

```MySQL
WITH film_category AS (
    SELECT DISTINCT 
        dim_category.category_id,
        dim_category.name AS category,
        fact_rental.film_id
    FROM
        dim_category
        INNER JOIN fact_rental ON fact_rental.category_id = dim_category.category_id
),
film_category_count AS (
    SELECT
        film_category.category_id,
        film_category.category,
        COUNT(film_category.film_id) AS films
    FROM
        film_category
    GROUP BY
        film_category.category_id
)
SELECT
    category,
    films
FROM
    film_category_count
WHERE
    films > (
        SELECT
            CEIL(AVG(film_category_count.films)) AS average_by_category
        FROM
            film_category_count
    )
ORDER BY
    film_category_count.category;
```



Exercise 4:

Write an SQL query to 
- get the **maximum purchase amount by customers** 
	- on `2007-04-30` 
	- between `15:00` and `16:00`. 
- Obtain 
	- the customer's **full name** in **capital letters**, 
	- the **maximum purchase amount**, 
	- and the **payment date**. 
- Then, create a column called `value_rate`, and 
	- assign the `low` label if the amount is between 0 and 3, 
	- the `mid` label if it is between 3 and 6, and 
	- the `high` label if it is above 6. 
- Sort by the maximum purchase amount in descending order 
- and full name in ascending order.

```MySQL
WITH max_amount_customer AS (
    SELECT
        fr.customer_id,
        MAX(fr.amount) AS max_amount,
        DATE(fr.payment_date) AS payment_date
    FROM
        fact_rental AS fr
    WHERE
        fr.payment_date BETWEEN "2007-04-30 15:00:00" 
        AND "2007-04-30 16:00:00"
    GROUP BY
        fr.customer_id,
        DATE(payment_date)
)
SELECT
    CONCAT(
        UPPER(dim_customer.first_name),
        ' ',
        UPPER(dim_customer.last_name)
    ) AS full_name,
    max_ac.max_amount as max_amount,
    max_ac.payment_date,
    CASE
        WHEN max_amount >= 0
        AND max_amount < 3 THEN 'low'
        WHEN max_amount >= 3
        AND max_amount < 6 THEN 'mid'
        WHEN max_amount >= 6 THEN 'high'
    END AS value_rate
FROM
    dim_customer
    INNER JOIN max_amount_customer AS max_ac ON dim_customer.customer_id = max_ac.customer_id
ORDER BY
    max_amount DESC,
    full_name ASC;
```


Exercise 5: Pivot Table

```MySQL
SELECT
    CONCAT(
        dim_customer.first_name,
        ' ',
        dim_customer.last_name
    ) AS full_name,
    dim_category.name AS category,
    SUM(fact_rental.amount) AS amount
FROM
    fact_rental
    INNER JOIN dim_customer ON dim_customer.customer_id = fact_rental.customer_id
    INNER JOIN dim_category ON dim_category.category_id = fact_rental.category_id
GROUP BY
    full_name,
    category
ORDER BY
    full_name,
    category,
    amount
LIMIT 30;
```

##### __Expected Output__

*Note*: Not all of the records are shown here.

| **full_name** | **category** | **amount** |
| ------------- | ------------ | ---------- |
| Aaron Selby   | Action       | 4.99       |
| Aaron Selby   | Classics     | 7.98       |
| Aaron Selby   | Comedy       | 7.98       |
| Aaron Selby   | Drama        | 5.99       |
| Aaron Selby   | Family       | 8.98       |
| Aaron Selby   | Foreign      | 8.97       |
| Aaron Selby   | Games        | 6.99       |
| Aaron Selby   | Horror       | 10.98      |
| Aaron Selby   | Music        | None       |
| Aaron Selby   | Travel       | 25.94      |
| Adam Gooch    | Action       | 2.99       |
| Adam Gooch    | Animation    | 10.99      |
| Adam Gooch    | Children     | 6.97       |
| Adam Gooch    | Classics     | 3.99       |
| Adam Gooch    | Comedy       | 8.99       |
| Adam Gooch    | Family       | 1.98       |
| Adam Gooch    | Foreign      | 13.98      |
| Adam Gooch    | Games        | 10.98      |
| Adam Gooch    | Horror       | 12.97      |
| Adam Gooch    | Music        | 6.99       |
| ...           | ...          | ...        |

Now I need to create a Pivot Table based on the above query output:

Using previous results, write an SQL query to create a pivot table that shows the total amount spent for each customer in each category. Also, fill the null values with a 0.

```MySQL
WITH customer_category_sum AS (
	SELECT
	    CONCAT(
	        dim_customer.first_name,
	        ' ',
	        dim_customer.last_name
	    ) AS full_name,
	    dim_category.name AS category,
	    SUM(fact_rental.amount) AS amount
	FROM
	    fact_rental
	    INNER JOIN dim_customer ON dim_customer.customer_id = fact_rental.customer_id
	    INNER JOIN dim_category ON dim_category.category_id = fact_rental.category_id
	GROUP BY
	    full_name,
	    category
	ORDER BY
	    full_name,
	    category,
	    amount
)
SELECT
    customer_category_sum.full_name,
    MAX(
        CASE
            WHEN customer_category_sum.category = 'Family' THEN amount
            ELSE 0
        END
    ) AS "Family",
    MAX(
        CASE
            WHEN customer_category_sum.category = 'Games' THEN amount
            ELSE 0
        END
    ) AS "Games",
    MAX(
        CASE
            WHEN customer_category_sum.category = 'Animation' THEN amount
            ELSE 0
        END
    ) AS "Animation",
    MAX(
        CASE
            WHEN customer_category_sum.category = 'Classics' THEN amount
            ELSE 0
        END
    ) AS "Classics",
    MAX(
        CASE
            WHEN customer_category_sum.category = 'Documentary' THEN amount
            ELSE 0
        END
    ) AS "Documentary",
    MAX(
        CASE
            WHEN customer_category_sum.category = 'Sports' THEN amount
            ELSE 0
        END
    ) AS "Sports",
    MAX(
        CASE
            WHEN customer_category_sum.category = 'New' THEN amount
            ELSE 0
        END
    ) AS "New",
    MAX(
        CASE
            WHEN customer_category_sum.category = 'Children' THEN amount
            ELSE 0
        END
    ) AS "Children",
    MAX(
        CASE
            WHEN customer_category_sum.category = 'Music' THEN amount
            ELSE 0
        END
    ) AS "Music",
    MAX(
        CASE
            WHEN customer_category_sum.category = 'Travel' THEN amount
            ELSE 0
        END
    ) AS "Travel"
FROM
    customer_category_sum
GROUP BY
    full_name
ORDER BY
    full_name
LIMIT 10;
```


Exercise 7:


```MySQL
SELECT
    fact_rental.customer_id,
    CASE
        WHEN (EXTRACT(HOUR FROM TIMEDIFF(fact_rental.return_date, fact_rental.rental_date))) > dim_film.rental_duration * 24 THEN 'Late'
        ELSE 'On time'
    END AS delivery    
FROM
    fact_rental
    INNER JOIN dim_customer ON dim_customer.customer_id = fact_rental.customer_id
    INNER JOIN dim_film ON dim_film.film_id = fact_rental.film_id
WHERE
    fact_rental.payment_date BETWEEN '2007-04-30 15:00:00'
    AND '2007-04-30 16:00:00'
ORDER BY
    dim_customer.customer_id
;
```


Exercise 8:

