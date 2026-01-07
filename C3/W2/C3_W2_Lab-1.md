
# Simple Data Lake with AWS Glue

In the lab, you'll work with a **simple data lake** that uses Amazon S3 as its primary storage. 
- You'll be given an **S3 bucket** containing raw JSON files that represent reviews and metadata for some Amazon products. 
- You'll process and **transform** these files into **parquet** files and then store them in the same bucket. 
- To **process** the data, you'll define **Glue ETL jobs** using Terraform 
- Then you'll use a **Glue crawler** to populate the **data catalog** with your processed data. This enables you and your stakeholders to query the processed data in the S3 bucket using Amazon Athena with SQL queries. 
- There will be an optional part at the end of the lab where you'll explore the effects of **compression and partitioning** when storing your data in S3 and how these techniques impact storage capacity and the data retrieval performance. 

![[Screenshot 2026-01-07 at 15.37.10.png]]


___

You have explored and performed some transformations on the reviews and metadata sample datasets, and you are now ready to perform those transformations over the complete dataset. This process will be done with AWS Glue.

## 4 - Transformation with AWS Glue ETL

**AWS Glue ETL** (Extract, Transform, Load) is a ***serverless service*** that simplifies data integration and processing. You briefly used Glue in the labs of course 1. In this lab, you will interact more closely with this service, and in course 4, you will learn more details about its underlying distributed framework (Apache Spark). 

AWS Glue **requires a Spark script** to perform a **job** - this script can be coded in Python or Scala. Here you are provided with Python scripts. In these scripts, you will extract the raw data from the provided bucket, transform the data and then save it in the **parquet** format. Parquet is a columnar storage file format commonly used in *big data processing frameworks* like Apache Hadoop and Apache Spark. Parquet format has several features such as the support of compression algorithms and support of schema evolution. If you want to know more about this format, you can read [this article](https://airbyte.com/data-engineering-resources/parquet-data-format).

