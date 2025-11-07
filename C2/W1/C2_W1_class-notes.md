This course includes a focus **on the first two stages of the data engineering life cycle**, which are: 
- Data Generation and Source Systems.
- Data Ingestion from those source systems.

In this part of the course, we'll definitely be talking about, understanding the source systems from which you're going to get your data, different ways of ingesting it. As well as different ways of orchestrating these data pipeline workflows and monitoring them to make sure that you're preserving data quality, as well as ingestion, performance, and other characteristics, etc., very important to your work as a data engineer.

[**Course 2 - Source Systems, Data Ingestion, and Pipelines**](https://www.coursera.org/learn/source-systems-data-ingestion-and-pipelines/home/welcome)

This course consists of 4 weeks of content and covers these main learning objectives:

- Identify different data formats and determine appropriate source systems for generating each type of data
- Explain at a high level how data is generated, stored, and retrieved in various source systems, including relational databases, NoSQL databases, object storage, and streaming systems
- Explain the basics of cloud networking
- Troubleshoot database connection errors
- Explain the difference between batch and streaming ingestions and identify uses cases for each pattern
- Differentiate between the two batch ingestion patterns: Extract-Transform-Load (ETL) and Extract-Load-Transform (ELT)
- Create a script to ingest data from a REST API
- Describe the basic components of an event-streaming platform
- Interact with an event streaming platform as a source system and as an ingestion tool
- Use Terraform to provision AWS resources for your data pipeline
- Identify tools for monitoring your data systems and data quality
- Identify and monitor relevant data quality metrics
- Explain how orchestration can be applied to a data pipeline, and list its benefits
- Build data pipelines with DAGs in Airflow using features such as Taskflow API, operators, XCom variables, etc.


## Working with Source Systems

 This week, we're going to get started by looking at different types of source systems and how you can interact with these systems. 
 
As you saw in the first course of the specialization, data generation in source systems is the first stage of the data engineering lifecycle. 

![[Screenshot 2025-11-07 at 19.26.11.png]]

As a data engineer, you are typically **not responsible** for generating this data yourself or maintaining these source systems. But ingestion from source systems is where all your data pipelines will begin. So, it's important that you understand: 
- **how this data is generated**, **
- where** and **how** it's stored, and 
- some of its **characteristics** 
so that you can build robust data pipelines with these upstream systems as your data source.


## Different Types of Source Systems

The specific source systems you'll work with as a data engineer will often vary depending on what kind of data you're ingesting from those systems. The most common type of data you'll work with is structured data, which is to say, data organized as tables of rows and columns. Chances are you've worked with structured data in the past, whether that was in a spreadsheet or a relational database, or maybe even using Python to read a CSV file. The other types of data you'll encounter as a data engineer are semi-structured and unstructured data. Semi-structured data is data that is not in tabular form, so it's not made up of rows and columns, but it still has some structure. A common semi-structured data format you'll run into is what's known as JavaScript object notation, or JSON. A JSON file contains a series of key-value pairs.

