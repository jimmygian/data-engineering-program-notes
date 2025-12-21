
# Week 4 Lab 2: Airflow 101 - Best Practices

In this lab, you will apply some of the best practices in orchestrating data pipelines in Airflow. You will build two DAGs that extract data from an RDS database, transform it and load it into a storage service. After this lab, you'll be able to:

- Implement **variables** and **templating** in an Airflow DAG following best practices
- Integrate **cross-communication** between Airflow Tasks using **XCOMs**
- **Organize Airflow Tasks** into **groups** to improve DAG readability and monitoring

For this lab, you are provided with a MySQL database that represents the source system you will interact with. The database `classicmodels` ([MySQL Sample Database](https://www.mysqltutorial.org/mysql-sample-database.aspx "https://www.mysqltutorial.org/mysql-sample-database.aspx")) is already instantiated for you in Amazon RDS. You have used this database in the previous labs. In this lab, you will interact with  4 of its tables: `orders`, `customers` , `payments` and `products` to create two DAGs.  If you need to review how to access the database, you can read the instructions in the last (optional) section of this lab.

You are provided with a dockerized version of Airflow that is running on an EC2 instance. You will only need to interact with the Airflow UI and the S3 bucket that represents the DAG directory, not with the EC2 instance directly.

You will use the following DAG which comprises five tasks to process the `orders` table from `classicmodels`.
![[Screenshot 2025-12-20 at 21.32.59.png]]

Here are the descriptions of the DAG tasks:

- `start`: is an empty task marking the start of the DAG. Here you will use `DummyOperator`. Similarly to `EmptyOperator` it doesn't include any behavior, but it creates a task instance in the Airflow metadata database.
- `extract_load_orders`: extracts data from the table `orders` and loads it into a zone in the S3 bucket (bronze zone), using the following destination path:
	- `s3://<BUCKET_NAME>/bronze/orders/YYYY/MM/DD/`. 
	- You will implement this task using the [SqlToS3Operator](https://airflow.apache.org/docs/apache-airflow-providers-amazon/stable/transfer/sql_to_s3.html "https://airflow.apache.org/docs/apache-airflow-providers-amazon/stable/transfer/sql_to_s3.html"). It is an Amazon transfer operator, which you can use to copy data from a SQL server to an S3 file.
- `transform_orders`: transforms the data extracted from the table `orders` and loads it into another zone of the S3 bucket (silver zone), using the following destination path:
	- `s3://<BUCKET_NAME>/silver/orders/YYYY/MM/DD/`
	- The transformation consists of dropping the null records and duplicate rows from the table `orders`. You will execute this task using the `PythonOperator` that calls the `drop_nas_and_duplicates` function.
- `notification`: emulates a notification task that sends an email with the number of the resultant records in the transformed table. In this lab, this task will just print the number of rows of the transformed data. You will execute this task using the `PythonOperator` that calls the `notify_valid_records` function.
- `end`: is an empty task marking the end of the DAG. It doesn't include any behavior and will be executed also with the `DummyOperator`.