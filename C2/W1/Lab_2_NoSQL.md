Check [NoSQL](NoSQL.md)'s DynamoDB Document.

In the next lab, you'll work with **Amazon DynamoDB** as a key value database and apply some create, read, update, and delete, or **CRUD** operations on the data in this NoSQL database.

You will create and interact with four DynamoDB tables using Python code in a Jupyter notebook. To do so, you will use **Boto3**, which is the A**WS Software Development Kit** that allows you to create and configure AWS services using Python.

Here's a documentation of Boto3, where you can find a list of methods that you can use to interact with various AWS resources:
https://botoÍÍ3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb.html


The basic CRUD methods that you can use are:
- **CREATE:**
	- *create_table*
- **READ:**
	- *scan*
	- *get_item*
	- *query*
- **UPDATE:**
	- *put_item*
	- *write_batch_items*
	- *update_item*
- **DELETE:**
	- *delete_item*


To call these methods, you will first create a client object, 

```python
import boto3

client = boto3.client('dynamodb')
```

This provides you with an interface representing an Amazon DynamoDB table. Using this **client object**, you will call any of these DynamoDB methods.


**Creating the tables**
To create the tables, you are provided with these four JSON files that are downloaded from the Amazon DynamoDB Developer Guide. You will read and load each of the contents into a DynamoDB table. 
![[Screenshot 2025-11-26 at 15.14.34.png]]

**Product Catalog file**
- Information about some products that are sold on Amazon. 
- Each product is defined by its ID, which you will use as a simple **primary key** for the corresponding table. 
	- ***ID**: simple primary key*

**Forum file**
- The forum file contains information about some AWS forums where users post questions or start threads about AWS services. 
- For **each forum**, you can find the total number of threads, messages, and views. 
- Each forum is defined by its **name**, which you will use as a simple **primary key** for the corresponding table. 
	- ***Name**: simple primary key*

**Thread file**
- Information about **each forum thread**, such as 
	- the thread subject, 
	- the thread message, 
	- the total number of views and replies to the given thread. 
- Each thread is defined by the ***ForumName*** and the thread ***Subject***, which you will use as a **composite primary key** for the corresponding table. 
	- ***ForumName & Subject**: composite primary key*

**Reply file**
- Information about **the replies on each thread**, such as 
	- the time of the reply, 
	- the reply message, 
	- the user who posted the reply, and
	- the ID of the reply, which is a concatenation of the forum and the thread subject. 
- Each reply is defined by the **ID and time of the reply**, which you will use as a **composite primary key** for the corresponding table. 
	- ***ID & ReplyDateTime**: composite primary key*


![[Screenshot 2025-11-26 at 15.20.02.png]]


Let's take a look at the forum file. It contains a list of ***PutRequest*** elements, each of which contains a single ***Item***. You will be extracting the attributes of each item and then loading them into the DynamoDB table. 
![[Screenshot 2025-11-26 at 15.26.42.png]]



*Note*: If you check the content of each JSON file, you will notice the use of letters such as N, S, B. These are known as [*Data type descriptors*](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypeDescriptors) that tell DynamoDB how to interpret the type of each field. We will talk more about it later in this lab.

![[Screenshot 2025-11-26 at 15.27.18.png]]

So the letter *N* here, means number, and *S*, means string. 

These are examples of DynamoDB data type descriptors that tell DynamoDB how to interpret the type of each attribute.

All the remaining JSON files follow the same pattern, so I encourage you to quickly skim through the other JSON files to gain a better understanding of each one.

The dataset that you will use in this lab is the sample data from the [Amazon DynamoDB Developer Guide](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/AppendixSampleTables.html#AppendixSampleData) ([dataset zip file](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/samples/sampledata.zip)).

In this lab, you will create 4 DynamoDB tables (`de-c2w1-dynamodb-ProductCatalog`, `de-c2w1-dynamodb-Forum`, `de-c2w1-dynamodb-Thread`, `de-c2w1-dynamodb-Reply`) and load in each the data from the corresponding JSON file.