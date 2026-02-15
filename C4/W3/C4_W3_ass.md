# Programming Assignment: Assignment 3: Data Transformations with Apache Spark

Link: https://www.coursera.org/learn/data-modeling-transformation-serving/programming/SaiEu/assignment-3-data-transformations-with-apache-spark

In this assignment, you are provided with a Spark cluster deployed using Amazon EMR. This service comes with a Studio and Workspace functions allowing you to run Spark jobs from this notebook directly.

You will recreate the Star Schema data model from the Week 1 assignment using PySpark, the Python API for Spark.

## 2 - Environment Setup

The `classicmodels` database is stored in an RDS instance running a Postgres engine, you will need to configure the connection to read the source data and then store the generated data models. Thankfully, the Studio functionality of Amazon EMR provides you with the necessary classes ready to use, but you will need to add a configuration to allow the environment to connect to a Postgres database.