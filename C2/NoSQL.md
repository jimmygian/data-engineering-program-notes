# DynamoDB

DynamoDB is a **key-value database** that stores **a set of key-value items in a table**. 
![[Pasted image 20251123165848.png]]
Each **row** contains the **attributes** of one item and is uniquely identified by the item's key. 

Example:
![[Pasted image 20251123165754.png]]

#### Primary (Partition) Key
In this example, each row contains the **personID** and the corresponding **attributes**. Since a personID column uniquely identifies each row, it represents the **primary key** for this table. 
![[Pasted image 20251123165954.png]]

When you work with DynamoDB, you can also refer to the primary key as the **partition key**. This is because DynamoDB uses the primary key to determine the partition, or in other words the **physical storage** in which the item will be stored. 

**Composite Key**
You can also define a **composite primary key** for a DynamoDB table. For example, here's a table where each row represents an order's item, and is uniquely identified by a composite primary key. 

This composite key consists of two keys: Partition key | Sort key
![[Pasted image 20251123170317.png]]

With this composite key, you can have two items with the same partition key, but they should have different sort keys, so that you can still uniquely identify each item.

DynamoDB uses the partition key to determine in which partition the item will be stored, and the sort key to sort the items within the same partition.

In these two tables, you can see that each item can have **its own distinct attributes**. This is because DynamoDB tables are **schema-less**, which means you don't need to define the attributes beforehand.

#### Boto3
 In this lab, you will create and interact with four DynamoDB tables using Python code in a Jupyter notebook. 

To do so, you will use **Boto3**, which is the **AWS Software Development Kit** that allows you to create and configure AWS services using Python. 

Here's a documentation of Boto3, where you can find a list of methods that you can use to interact with various AWS resources:
https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb.html

In the lab, you'll focus on the methods that you can use to apply the CRUD operations, such as 
- **creat_table** to create the tables, 
- **put_item**, **write_batch_items**, **update_item** to add or update items in the created tables, **
- scan**, **get_item**, query to read items from the tables, and
- **delete_item** to delete items from the tables. 

![[Pasted image 20251123171158.png]]
To call these methods, you will first create a client object, 

```python
import boto3

client = boto3.client('dynamodb')
```

This provides you with an interface representing an Amazon DynamoDB table. Using this client object, you will call any of these DynamoDB methods.


