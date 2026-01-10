
# Building a Data Lakehouse with AWS Lake Formation and Apache Iceberg

In the lab, you'll implement a data lakehouse with a medallion-like architecture using Amazon S3, Apache Iceberg, and AWS Lake Formation.
![[Screenshot 2026-01-10 at 12.07.15.png]]
![[Screenshot 2026-01-10 at 12.08.17.png]]


You're provided with an **S3 bucket**, labeled as the *"data_lake_bucket"*, that will act as the underlying storage for your data lakehouse implementation. 

The S3 bucket is registered with Lake Formation, so you can explore how you can establish governance and fine-grain permissions for the data stored in the data lake using Lake Formation. You're also provided with two source systems, an RDS MySQL database that contains the classic models data that you've worked with in Course 1, and an S3 bucket that contains a JSON file with the customer ratings of the products found in the classic models data. ![[Screenshot 2026-01-10 at 12.09.39.png]]

- You'll first bring the raw data from both sources to the landing zone of the data lake bucket. 
- Then you'll process each dataset, apply schema enforcement, and then store the processed data in the curated zone of the data lake bucket. 
- Finally, you'll use the processed data to create additional tables that include the information needed for your analytics and machine learning end-users, and store these tables in the presentation zone of the data lake bucket. 
- You're also provided with two databases in the Glue Data Catalog, curated zone and presentation zone databases, each containing some empty tables. 
	- For each object you create in the curated and presentation zones of the data lake bucket, you'll associate it with a table in the corresponding catalog database so that the data can be queried using Amazon Athena. 
	- You'll also choose the iceberg format for some of the data stored in the curated zone and all the data stored in the presentation zone, and then explore the schema evolution and time travel features of this open table format. 

![[Screenshot 2026-01-10 at 12.11.17.png]]



### Lab Explanation

In this section, I'll give you an overview of the **data format** in each zone of the data lake house. Then, in the next one, I'll go through the iceberg format and the governance feature of the lake formation service that you'll apply in the lab. 

### Raw Data Ingestion
Let's start with the **raw** data that you'll ingest into the landing zone. 

From the MySQL source database, you'll extract each table and save each as a **CSV file** in the landing zone.

This means that you will create eight CSV files, where each file corresponds to one of these tables and has the S3 key that starts with the landing zone, then RDS, and finally the table name. ![[Screenshot 2026-01-10 at 12.16.11.png]]


From the **source_bucket**, you'll extract the JSON file ratings, which consists of a list of JSON objects. Each object contains the customer number, the product code, and the product rating assigned by this customer to the given product. 

You'll ingest the JSON file as a **data frame**, add the field ingest  `_ts`, that represents a timestamp when the file was ingested into the data lake bucket, and then finally store the data frame as a singular JSON file in the landing zone using an S3 key that starts with 'landing_zone', then 'json', and finally 'ratings'. 
![[Screenshot 2026-01-10 at 12.17.28.png]]


### Curated Zone
After you transfer the raw data to the landing zone, you'll 
- extract the data, 
- perform three transformations to it, 
- and then store the processed data in the curated zone. 

#### Transformation 1
In the first transformation, you'll focus on **processing the CSV files.** 

- You'll extract all eight tables from the landing zone and convert each into a data frame. 
- Then for each table, you'll add two metadata columns, 
	- `ingest_ts`, which represents the timestamp when the data was ingested in the curated zone, 
	- and `source`, which represents the name of the source database. 

 - You're also provided with a *schema* that these process tables have to follow in the curated zone, describing the name and the type of each column in each table. So you'll enforce the schema by casting the numerical columns to the expected type.

- And finally, you'll **store** each table as a **parquet** file using **Snappy** as the compression algorithm and specify the S3 key that starts with `curated_zone`, then the `<table>` name. 

- For each table you create in the curated zone, you'll write its metadata to the corresponding catalog table in the Glue Catalog database that's labeled as curated_zone. 
![[Screenshot 2026-01-10 at 12.26.40.png]]

### Transformation 2
In the second transformation, you'll focus on **preparing** the data that is needed for the machine learning team by **combining** the latest ratings data with the customer and product info from the CSV tables. ![[Screenshot 2026-01-10 at 12.30.49.png]]

- From the landing zone, you'll *extract* 
	- the CSV tables 
	- and the latest ratings from the JSON file 
	- and convert each of them into a **data frame**. 

- You'll then create a new data frame that contains some customer info from the customer table, some product information from the products table, and the ratings from the ratings data. 
- You'll add an additional column to this data frame that contains the timestamp when the data was processed, and finally store the data frame using the Iceberg format in the curated zone of the Data Lake bucket by specifying the S3 key that starts with curated zone, then ratings for ML, then Iceberg. 
>	In the next section, I'll go over the Iceberg format and what you should expect under this path. 
- You'll also associate the processed data to its corresponding catalog table in the Glue Data Catalog curated zone database. 

![[Screenshot 2026-01-10 at 12.29.36.png]]


### Transformation 3
In the third transformation, you'll focus on just *extracting* the latest ratings and storing them in Iceberg format in the curated zone of the Data Lake bucket using the S3 key that starts with curated zone, then ratings, and finally Iceberg. 

If the ratings table in the curated zone already contains the ratings assigned by a customer to a product, then with the new data you ingest, you'll extract the new pairs of customers and products and update the ratings if the pair already exists. You'll also associate the ratings data to its catalog table in the Glue Data Catalog curated zone database. ![[Screenshot 2026-01-10 at 12.34.08.png]]


You'll carry out these transformations using **Glue ETL** by defining the ETL jobs for the landing and curated zones using Terraform. 

>	I won't go through all the details of the provided Terraform files, but I'll highlight a few components. For a more detailed explanation of the Terraform files, you can revisit the lab walkthrough videos for the simple Data Lake lab of Lesson 1. 


### Terraform walkthrough

Under the Terraform files, you'll find three modules. 
- The Landing ETL and Transform ETL modules contain the configuration files for the corresponding ETL jobs for the landing and curated zones. 
- You'll use the alter table in the optional part of the lab to explore the schema evolution of the Iceberg format. 

#### `landing_etl/` module
Under the `landing_etl/` module, if you check the `glue.tf` configuration file, you'll find the `"aws_glue_job"` jobs (resources) that ingest the raw data into the landing zone of the Data Lake bucket. 

You'll also find a block that defines the information needed to connect to the RDS database (`aws_glue_connection`).  

In the `iam_roles.tf` configuration file, there is a `"data"` block for the role that will be assumed by all Glue jobs. Note that this is a data block, and this is because the Glue role is already created and provided to you. 

#### `transform_etl/` module
Under the `transform_etl/` module, you'll find the configuration file for the three Glue jobs that you'll use to process the raw data from the landing zone and store it in the curated zone. 

#### `assets/` folder
Under the Assets folder, you can find the folder containing the Python scripts for each of these transformations. Each script contains three parts, extracting the data from the source, processing the data, and then storing the data into the target destination. 

Feel free to skim through these files to get a quick overview of how each transformation is coded. And in the lab, you'll be asked to complete a small part of the code in these Python scripts. 

The first file, `..batch_transform.py`, corresponds to the job of processing the CSV files from the landing zone, enforcing schema to each table, and then storing the processed data as parquet files in the curated zone. So in this file, you can find the schema definition of each table, defined as a struct type, each containing a list of struct fields corresponding to the name of the column, the type of the column, and whether the column can contain null values or not. These struct type and struct field objects are provided by Spark, which you'll use to enforce the schema on the processed data. ![[Screenshot 2026-01-10 at 14.24.46.png]]You'll learn more about Spark in the next course. 


In the other two scripts, you'll extract the raw data from the JSON files and transform it to create the Ratings4ML and RatingsData stored in Iceberg format. 

You'll find the set of configuration settings, which are used to enable Iceberg for AWS Glue. 
![[Screenshot 2026-01-10 at 14.25.15.png]]

So by setting these configurations, you can use AWS Glue to perform read and write operations on Iceberg tables in Amazon S3, or work with Iceberg tables using the AWS Glue data catalog. I've included a link in the resource section that includes more information about these configurations. 

### Presentation Zone
After you have all your processed data ready in the curated zone, you'll create additional tables in the presentation zone that will be shared with your end-users. 

- For your analytics end-users, you'll create two tables, one that contains the average sales grouped by year and month, and another that contains the average ratings per product. 
- You will also create an additional table that represents the ratings table you created in the curated zone. 
- And for your machine learning end-users, you'll create one table that represents the Ratings4ML table you created in the curated zone. 

While you could use **Glue ETL** to create these tables in the presentation zone, you'll instead use **Athena** to create these tables. Since you've associated catalog tables to the data in the curated zone, you can use Athena as a processing tool as well. 

Here's an example of the SQL query you can use with Athena to create the ratings table in the presentation zone. 
![[Screenshot 2026-01-10 at 14.27.35.png]]


When you work with Athena, you use the name of the catalog database and the catalog table you created in the Glue data catalog, which points to the actual data in the S3 bucket. 

After you write this query, you'll pass it to the `start_query_execution()` method from the AWS Wrangler package, where you also specify the name of the catalog database you want to associate with the created table, which is presentation zone in this case. So after you execute this statement, the actual data in the S3 bucket and its catalog table in the Glue data catalog will be created. You'll repeat the same process for the other tables and store all of them in Iceberg format in the presentation zone. 

![[Screenshot 2026-01-10 at 14.28.48.png]]


So that was an overview of the format of the data in the landing, curated, and presentation zones of your data lake house. Join me in the next video for an overview of the Iceberg format and other lake house governance features.

## Apache Iceberg format

In the previous section, you saw how you'll store your process data in iceberg format in the lab. Now let's go through the iceberg format and see how these files are organized in the S3 bucket. 

I'll take as an example the ratings data created in the curated zone. 

When you bring the ratings data to the curated zone, you'll specify this path in the S3 bucket. 

```aws
s3://{data_lake_bucket}/curated_zone/ratings/iceberg
```

If you further check the file that has the `iceberg` prefix, you will see two additional prefixes, metadata and data. 

```aws
s3://{data_lake_bucket}/curated_zone/ratings/iceberg/metadata
s3://{data_lake_bucket}/curated_zone/ratings/iceberg/data
```

- The `metadata/` prefix represents the **metadata layer**, which contains the metadata, manifest list and manifest files that we've discussed in a previous video. 

- On the other hand, the `data/` prefix represents the **storage layer**, which contains the actual data stored in *parquet*. 
![[Screenshot 2026-01-10 at 14.32.38.png]]

Here's the content of the metadata layer just after you create the data. 
![[Screenshot 2026-01-10 at 14.33.31.png]]

The JSON file represents the metadata file, which contains information about: 
- the table schema, 
- the location of the table as stored in S3, 
- the date and time of when the table was last updated, 
- and the UUID of the current snapshot, which is created every time the content of the table is updated. 

Every time you make changes to the metadata of the table, *another* metadata file will be created. 

The `.avro` file that starts with `snap-..` is another metadata file that represents the **manifest list** file corresponding to one snapshot. It points to the list of the manifest files that contains detailed metadata about the snapshot. 

This manifest list file points to this manifest file, which is another Avro file.
![[Screenshot 2026-01-10 at 14.35.56.png]]


And here's the content of the storage or data layer, which contains the parquet data files. 
![[Screenshot 2026-01-10 at 14.36.37.png]]


Recall that **at the top of the metadata and storage layers**, you have the **"catalog layer"**, which points to the current metadata and helps identify where to read or write data for a given table. 
![[Screenshot 2026-01-10 at 14.37.13.png]]

In the lab, this catalog layer is implemented using `AWS Glue Data Catalog`, which contains a catalog table for *each* **iceberg** file you create in the curated and presentation zone. 

The catalog tables are organized into curated zone and presentation zone databases, like I mentioned earlier. In the optional section of the lab, you'll explore the schema evolution feature of Iceberg. 
- You'll be asked to add a new column to the ratings table to accommodate for the change in schema of the input data from the source bucket. 
- You'll apply the transformation and terraform using the third module, `alter_table/`. When you add the new column to the ratings tables, only the metadata file will be changed and you won't have to rewrite or update any of the data files. 
- You'll also explore the time travel feature of Iceberg and see how you can query from both the new and old versions of the rating table. 
- Finally, you'll explore how you can apply fine-grained permissions to your data lake using Lake Formation. As I already mentioned, the data lake provided to you is registered with Lake Formation and you'll act as the data lake **administrator**. 
  
  I've included in the resource sections links to AWS documentation that show you how to set up Lake Formation and associate it with your data lake. 



In the lab, you're provided with the roles assumed by the lab environment and Glue resources, and a user that represents a machine learning team member. The broad permissions for these IAM identities have already been defined for you. In the lab, you'll use Lake Formation to grant them fine-grained permissions to the data lake tables. 

In particular, you'll grant the role assumed by the lab environment permissions to access all catalog tables and the underlying stored data. And you'll grant the machine learning user permissions to access only the Ratings for ML table from the presentation zone. And then you'll verify that they can't access other tables. With that, I think you're ready to try out the lab. This lab can be a bit long, so feel free to skip or just skim the optional section if you're short on time. After you finish the lab, I'll see you back here for a quick summary of this week.