
# Week 3 Lab 1: Comparing the Query Performance Between Row-Oriented and Column-Oriented Databases

In this lab, you will explore the performance differences between row-based and column-based databases by performing analytical queries, as well as update and delete queries, on both types of storage and comparing their execution times. Understanding the differences between these storage options will help you make informed decisions tailored to your project query needs as well as performance, scalability and cost requirements.

### 1.1 - Introduction

In traditional transactional databases, the records are stored in **rows** which makes the databases optimized for **reading and writing rows efficiently**. Recently, there has been a shift towards column-oriented databases that are optimized for **analytical workloads**, as they are more efficient with the aggregating operations.

In this lab, you are provided with: 
- an **Amazon Redshift database** that leverages *columnar storage
- and an **Amazon RDS (Relational Database Service)** PostgreSQL data that leverages *row-oriented* storage. 

To assess the execution time of the analytical queries, you will work with a *benchmarking dataset* and you will run 5 analytical queries to query the data from each store. The provided data and the corresponding SQL queries are referred to as the **TPC-H benchmark.** 

You will also write 50 rows to one table of the provided database and compare the execution time of the write query for both databases. Then you'll delete these rows from both databases and again compare the execution time of the delete query. 

### 1.2 - Cloud Data Warehouse Benchmark

You will be using [The Cloud Data Warehouse benchmark](https://github.com/awslabs/amazon-redshift-utils/tree/master/src/CloudDataWarehouseBenchmark/Cloud-DWB-Derived-from-TPCH), which is derived from the TPC-H Benchmark. TPC-H is a decision-support benchmark established by the Transaction Processing Performance Council (TPC) to simulate a set of basic scenarios to examine a large dataset and execute queries to answer business questions. It is designed to evaluate the performance of various database systems in how they execute complex queries. The Cloud Data Warehouse benchmark is composed of 22 queries and a database at different data scales that help simulate different analytical needs and aggregation tasks across tables. For this lab, you will be using the 10GB version of the data set, which is the smaller data scale of this benchmark. This is the entity relationship diagram of the database:

![[Screenshot 2026-01-25 at 11.03.20.png]]

*Note*: The TPC Benchmark and TPC-H are trademarks of the Transaction Processing Performance Council (http://www.tpc.org). 


## 2 - Column-Based Database: Amazon Redshift

**Amazon Redshift** is a fully managed data warehousing service. It is designed for large-scale data analytics and allows users to analyze their data using **standard SQL queries**. Redshift is based on a columnar storage architecture. This means that values from the same *column* are *stored together on disk*, allowing for efficient data compression and retrieval. It's particularly beneficial for analytics workloads where queries often access only a subset of columns.

Columnar databases excel at **aggregation** operations (e.g., SUM, AVG, COUNT) because they only need to access the columns involved in the aggregation, rather than entire rows. This can significantly improve the performance of analytical queries, such as those commonly used in data warehousing environments.

Amazon Redshift's columnar architecture makes it well-suited for analytical workloads, data warehousing, business intelligence, and ad-hoc querying, offering high performance, scalability, and cost-effectiveness when processing large volumes of data. 

Both storage solutions are already set up, with connection details available in the CloudFormation stack's output. The Amazon Redshift database is pre-filled with the benchmark data. You'll begin the lab by connecting to the Redshift cluster.


Auto-creating insert/delete `.sql` files with `fake` library

```python
# Set a random seed for reproducibility
random.seed(42)

# Generate fake data
fake = Faker()

# Define the range for l_orderkey
leap = 50
start_orderkey = 70000000
end_orderkey = start_orderkey + leap

# Generate SQL queries and write them to .sql files
with open("./sql/individual_row_inserts.sql", "w") as insert_file, open("./sql/individual_row_deletes.sql", "w") as delete_file:
    for orderkey in range(start_orderkey, end_orderkey):
        
        ### START CODE HERE ### (~ 15 lines of code)
        
        # Use the `random_int` method from the `fake` object to generate random integers
        # Set a range between 1 and 1000000
        partkey = fake.random_int(1, 1000000)
        suppkey = fake.random_int(1, 10000) 
        
        
        # Use the `random_int` method from the `fake` object to generate random integers
        # Set a range between 1 and 10
        linenumber = fake.random_int(1, 10)
        
        quantity = round(fake.random_number(2), 2) 
        
        extendedprice = round(fake.random_number(4), 2) 
        
        discount = round(fake.random_number(2), 2) 
        
        tax = round(fake.random_number(2), 2) 
        
        returnflag = fake.random_element(elements=('N', 'R', 'A')) 
        
        linestatus = fake.random_element(elements=('O', 'F')) 
        
        
        # Use the `date_between` method from the `fake` object
        # Use start date as 1 year ago  as '-1y' and `today` as end date
        # Chain with the `strftime` method to return a string with the format '%Y-%m-%d'
        shipdate = fake.date_between(start_date='-1y', end_date='today').strftime('%Y-%m-%d')
        commitdate = (datetime.strptime(shipdate, '%Y-%m-%d') + timedelta(days=fake.random_int(1, 30))).strftime('%Y-%m-%d') 
        
        receiptdate = (datetime.strptime(commitdate, '%Y-%m-%d') + timedelta(days=fake.random_int(1, 30))).strftime('%Y-%m-%d') 
        
        
        # Use the `text` method of the `fake` object to generate a text of 25 characters
        shipinstruct = fake.text(max_nb_chars=25)
        
        # Use the `text` method of the `fake` object to generate a text of 10 characters
        shipmode = fake.text(max_nb_chars=10)
        
        # Use the `text` method of the `fake` object to generate a text of 44 characters
        comment = fake.text(max_nb_chars=44)
        
        ### END CODE HERE ###

        # Generate the SQL insert statement
        insert_statement = f"""
            INSERT INTO public.lineitem (
                l_orderkey, l_partkey, l_suppkey, l_linenumber, l_quantity, l_extendedprice, l_discount, l_tax,
                l_returnflag, l_linestatus, l_shipdate, l_commitdate, l_receiptdate, l_shipinstruct, l_shipmode, l_comment
            ) VALUES (
                {orderkey}, {partkey}, {suppkey}, {linenumber}, {quantity}, {extendedprice}, {discount}, {tax},
                '{returnflag}', '{linestatus}', '{shipdate}', '{commitdate}', '{receiptdate}', '{shipinstruct}', '{shipmode}', '{comment}'
            );
        """
        
        # Write the SQL insert statement to the file
        insert_file.write(insert_statement + "\n")

        # Generate the SQL delete statement
        delete_statement = f"""
            DELETE FROM public.lineitem
            WHERE l_orderkey = {orderkey} AND l_linenumber = {linenumber};
        """
        
        # Write the SQL delete statement to the file
        delete_file.write(delete_statement + "\n")

```