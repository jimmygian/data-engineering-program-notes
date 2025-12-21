# DynamoDB

DynamoDB is a **key-value database** that stores **a set of key-value items in a table**. 
![[Pasted image 20251123165848.png]]
Each **row** contains the **attributes** of one item and is uniquely identified by the item's key. 

#### What is a DynamoDB table?
DynamoDB database is a **key-value store** that stores a set of key-value pairs. Let's say you have a set of key-value items where each item represents a product. **Each item is characterized by a unique key** (product ID) and has a **set of corresponding attributes** (the **value** of the key). DynamoDB stores this key-value data in a **table** where each row contains the attributes of one product and it uses the key to uniquely identify each row. This table is different from relational tables because it's **schemaless**, which means that neither the attributes nor their data types need to be defined beforehand. **Each item can have its own distinct attributes.** 

For example, you can have one item that represents a book (Title, Authors, ISBN, Price) and another item that represents a bicycle (BicycleType, Brand, Price, Color) **both stored in the same DynamoDB table**.

Example:
![[Pasted image 20251123165754.png]]

#### Table's Keys
When you create a dynamoDB table, you need to **specify the primary key** which is the key that **uniquely identifies each item**. The primary key could be:
- a simple primary key *(partition key)*
- a composite primary key *(partition key + sort key)*


**Simple Primary Key**
For dynamoDB, this simple primary key is called a partition key because dynamoDB uses it as an input to a hash function. The output of the hash function determines the partition (internal physical storage) in which the item will be stored.

In this example, each row contains the **personID** and the corresponding **attributes**. Since a personID column uniquely identifies each row, it represents the **primary key** for this table. 
![[Pasted image 20251123165954.png]]

When you work with DynamoDB, you can also refer to the primary key as the **partition key**. This is because DynamoDB uses the primary key to determine the partition, or in other words the **physical storage** in which the item will be stored. 

#### Composite Key
In a composite key, two items *can have* the **same partition key** but they should have **different sort keys** so that the composite key can still **uniquely** identify each item. DynamoDB will use the partition key to determine in which partition the item will be stored. All items with the same partition key value are stored together, in **sorted order by sort key value**.

You can also define a **composite primary key** for a DynamoDB table. For example, here's a table where each row represents an order's item, and is uniquely identified by a composite primary key. 

This composite key consists of two keys: Partition key | Sort key
![[Pasted image 20251123170317.png]]

In these two tables, you can see that each item can have **its own distinct attributes**. This is because DynamoDB tables are **schema-less**, which means you don't need to define the attributes beforehand.

You can learn more about the core components of DynamoDB [here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html).


#### Boto3
To interact with DynamoDB in Python, you will use **Boto3**, which is the **AWS Software Development Kit** that allows you to create and configure AWS services using Python. 

Here's a documentation of Boto3, where you can find a list of methods that you can use to interact with various AWS resources:
https://botoÍÍ3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb.html

The basic CRUD methods that you can use are:
![[Screenshot 2025-11-26 at 15.05.44.png]]

To call these methods, you will first create a client object, 

```python
import boto3

client = boto3.client('dynamodb')
```

This provides you with an interface representing an Amazon DynamoDB table. Using this client object, you will call any of these DynamoDB methods.

*Note:* To interact with AmazonDynamoDB in Python, you are going to create a `boto3` client object. This object allows you to make API requests directly to AWS services to create, delete, or modify resources. When you create a `boto3` client object, you will need to **specify the AWS services** you want to interact with, and then, with the created client object, you can call methods to perform various operations on that resource.


## CREATE

**How will you create the tables?**

You will use the [DynamoDB create_table()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb/client/create_table.html) method. This method expects 3 required parameters:

* `TableName`: the **name** of the table.
* `KeySchema`: an **array of the attributes** that make up the **primary key** for a table. 
  For each element in this array, you need to specify: 
	* `AttributeName`: the name of the attribute, and 
	* `KeyType`: the role that the key attribute will assume (`HASH` if it is a partition key and `RANGE` if it is a sort key). 

	For example,

		'KeySchema'= [
			{'AttributeName': 'ForumName', 'KeyType': 'HASH'},
			{'AttributeName': 'Subject', 'KeyType': 'RANGE'}
		]

* `AttributeDefinitions`: an array that describes the attributes that make up the primary key. For each element in this array, you need to specify `AttributeName` and `AttributeType`: the data type for the attribute (S: String, N: Number, B: Binary,...). 
  
  For example,

		'AttributeDefinitions': [
			{'AttributeName': 'ForumName', 'AttributeType': 'S'},
			{'AttributeName': 'Subject', 'AttributeType': 'S'}
		]


There is an additional parameter that you can specify if you don't wish to pay for DynamoDB based on demand and you want to choose the provisioned mode:
* `ProvisionedThroughput`: a dictionary that specifies the read/write capacity (or throughput) for a specified table. It consists of two items:
- `ReadCapacityUnits`: the maximum number of strongly consistent reads consumed per second;
- `WriteCapacityUnits`: the maximum number of writes consumed per second.

Full example

```python
response = client.create_table(
	# REQUIRED
    TableName='string',
    KeySchema=[
        {
            'AttributeName': 'string',
            'KeyType': 'HASH'|'RANGE'
        },
    ],
    AttributeDefinitions=[
        {
            'AttributeName': 'string',
            'AttributeType': 'S'|'N'|'B'
        },
    ],
    
    # OPTIONAL
    LocalSecondaryIndexes=[
        {
            'IndexName': 'string',
            'KeySchema': [
                {
                    'AttributeName': 'string',
                    'KeyType': 'HASH'|'RANGE'
                },
            ],
            'Projection': {
                'ProjectionType': 'ALL'|'KEYS_ONLY'|'INCLUDE',
                'NonKeyAttributes': [
                    'string',
                ]
            }
        },
    ],
    GlobalSecondaryIndexes=[
        {
            'IndexName': 'string',
            'KeySchema': [
                {
                    'AttributeName': 'string',
                    'KeyType': 'HASH'|'RANGE'
                },
            ],
            'Projection': {
                'ProjectionType': 'ALL'|'KEYS_ONLY'|'INCLUDE',
                'NonKeyAttributes': [
                    'string',
                ]
            },
            'ProvisionedThroughput': {
                'ReadCapacityUnits': 123,
                'WriteCapacityUnits': 123
            },
            'OnDemandThroughput': {
                'MaxReadRequestUnits': 123,
                'MaxWriteRequestUnits': 123
            },
            'WarmThroughput': {
                'ReadUnitsPerSecond': 123,
                'WriteUnitsPerSecond': 123
            }
        },
    ],
    BillingMode='PROVISIONED'|'PAY_PER_REQUEST',
    ProvisionedThroughput={
        'ReadCapacityUnits': 123,
        'WriteCapacityUnits': 123
    },
    StreamSpecification={
        'StreamEnabled': True|False,
        'StreamViewType': 'NEW_IMAGE'|'OLD_IMAGE'|'NEW_AND_OLD_IMAGES'|'KEYS_ONLY'
    },
    SSESpecification={
        'Enabled': True|False,
        'SSEType': 'AES256'|'KMS',
        'KMSMasterKeyId': 'string'
    },
    Tags=[
        {
            'Key': 'string',
            'Value': 'string'
        },
    ],
    TableClass='STANDARD'|'STANDARD_INFREQUENT_ACCESS',
    DeletionProtectionEnabled=True|False,
    WarmThroughput={
        'ReadUnitsPerSecond': 123,
        'WriteUnitsPerSecond': 123
    },
    ResourcePolicy='string',
    OnDemandThroughput={
        'MaxReadRequestUnits': 123,
        'MaxWriteRequestUnits': 123
    }
)
```


## PUT

You can load data item by item or as a batch of items. Let's explore each option.

To load data item by item, you will use the method: [DynamoDB put_item()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb/client/put_item.html). This method expects two required arguments 
1. (1) the table name and 
2. (2) the item you need to add. 

The item should be a **dictionary** that contains the **attributes of the item** (and most importantly the value of its **primary key**), for example, here's the format of what the item should look like (an item in the reply table):

```python

item = {
	"Id": {
		"S": "Amazon DynamoDB#DynamoDB Thread 1"
	},
	"ReplyDateTime": {
	"S": "2015-09-15T19:58:22.947Z"
	},
	"Message": {
		"S": "DynamoDB Thread 1 Reply 1 text"
	},
	"PostedBy": {
		"S": "User A"
	}
}

```



This JSON structure that looks as follows..

  ```JSON
{
    "<AttributeName>": {
        "<DataType>": "<Value>"
    },
    "<ListAttribute>": {
        "<DataType>": [
            {
                "<DataType>": "<Value1>"
            },
            {
                "<DataType>": "<Value2>"
            }]
    }    
}
```

...is called **Marshal JSON**. This is similar to a regular JSON file but it also includes the types of each value. The `<DataType>` placeholders specify the data type of the corresponding value; you can learn more about the Data type conventions for DynamoDB in the [documentation](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypeDescriptors). 

## Desirializing Data (Optional)

The returned data from DynamoDB has the same input structure that the method `DynamoDB put_item()` expects, which is the ***Marshal JSON.*** Marshal JSON is different from the usual JSON format (that looks like the following):

  ```JSON
{
    "AttributeName": "Value",
    "ListAttribute": [
        "Value1",
        "Value2"
    ]
}
```

The usual JSON format is the typical format you will find in real life, as it can be easily parsed into Python Dictionaries. So you may need to convert the output returned by the `DynamoDB scan()` method into the usual JSON format, or you may need to convert data that is in the usual JSON format into Marshal JSON before inserting it into a DynamoDB table. 

The next optional part shows you how you can convert data in Marshal JSON into the usual JSON format.

Now, if you want to process data returned from DynamoDB operations with Python, you have to convert the data format to the usual JSON. `boto3` provides some utilities to help you with this process.

For that, you can use the `data_deserializer()` function provided below that takes in as input the data in Marshal JSON. This function consists of the following:

1. A `boto3` resource instantiation: [Resources](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/resources.html) is a higher-level abstraction class built on top of Client that is used to represent AWS resources as Python objects, providing in this way a Pythonic and Object Oriented interface. With that resource, you can create a *deserializer* object by calling the method `TypeDeserializer()`.

2. You can then use the `deserializer` object to call the `deserializer.deserialize()` method and apply it to each value to convert it into its deserialized version. 
	   (*Note*: if the returned value from `deserializer.deserialize(v)` is an instance of `decimal.Decimal`, you should convert it to float. This process of checking if the returned value is an instance of `decimal.Decimal` should be done because, by default, numerical values in DynamoDB are deserialized as decimals, which need to be handled properly if you want to work with the result; the easiest way is to convert them directly to float data type).

```python
def data_deserializer(data: Dict[str, Any]):
    boto3.resource("dynamodb")

    deserializer = boto3.dynamodb.types.TypeDeserializer()

    deserialized_data = {
        k: (
            float(deserializer.deserialize(v))
            if isinstance(deserializer.deserialize(v), decimal.Decimal)
            else deserializer.deserialize(v)
        )
        for k, v in data.items()
    }

    return deserialized_data
```


## READ

#### All items in a table
The `DynamoDB scan()` method returns all items in a table. You can perform a `DynamoDB scan()` operation on a DynamoDB table that fully scans the table and returns the items in 1MB chunks. Scanning is the slowest and **most expensive** way to get data out of DynamoDB.

#### One item from a table
If you want to read a single item, you could use the `DynamoDB get_item()` method. This method expects the **name of the table** and the **primary key** of the requested item. It is the **cheapest** and **fastest way** to get data from DynamoDB.

call the `client.get_item()` method of the `client` object. It should receive the table name, key and keyword arguments. For more information about this method, you can search for the `get_item` in the [documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb.html).

#### More options for read methods (optional)

By default, a read from DynamoDB will use ***eventual consistency***. An eventual consistent read in DynamoDB is cheaper than a ***strongly consistent read***. Several options can be added to the read methods, some of the regularly used ones are:
- `ConsistentRead`: specifies that a strongly consistent read of the table is required;
- `ProjectionExpression`: specifies what attributes should be returned;
- `ReturnConsumedCapacity`: determines what level of detail about the consumed capacity the response should return.

Example:
  
Cell 1: Function creation
```python
def get_item_db(table_name, key: Dict[str, Any], **kwargs):
    client = boto3.client("dynamodb")

    try:
        response = client.get_item(TableName=table_name, Key=key, **kwargs)
        
    except ClientError as e:
        error = e.response.get("Error", {})
        logging.error(
            f"Failed to query DynamoDB. Error: {error.get('Message')}"
        )
        response = {}
    
    return response
```

Cell 2:
```python
kwargs = {'ConsistentRead': True,
          'ProjectionExpression': 'ProductCategory, Price, Title',
          'ReturnConsumedCapacity': 'TOTAL'}

response = get_item_db(table_name=product_catalog_table['table_name'], key={'Id': {'N': '101'}}, **kwargs)
print(response)
```

The previous request consumed 1.0 RCU because this item is less than 4KB. 

> 	*RCU stands for Read Capacity Unit: "One read capacity unit represents one strongly consistent read per second, or two eventually consistent reads per second, for an item up to 4 KB in size", [reference](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/provisioned-capacity-mode.html)).*

If you run again the command but remove the ConsistentRead option, you can see that eventually consistent reads consume half as much capacity:

```python
kwargs = {'ReturnConsumedCapacity': 'TOTAL', 
          'ProjectionExpression': 'ProductCategory, Price, Title'
         }

response = get_item_db(table_name=product_catalog_table['table_name'], 
                    key={'Id': {'N': '101'}}, **kwargs
                    )
print(response)
```

You can find more information about the parameters that the `DynamoDB.Client.get_item()` accepts by reading the [documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb/client/get_item.html).

## Filtering READS
#### Query Items that Share the Same Partition Key

In DynamoDB, an **"item collection"** is a group of items that share the **same partition key** value, which means that items are related. You can query the items that belong to an item collection (i.e., that have the same partition key) using [DynamoDB query()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb/client/query.html) method. In this method, you need to specify the **particular value** of the partition key of the items in interest.

Item Collections only exist in tables that have **both a Partition Key and a Sort Key**. Optionally, you can provide the query method a sort key attribute and use a comparison operator to refine the search results.

The following cell shows an example of what `kwargs` should contain, as expected by the `DynamoDB query()` method:

`KeyConditionExpression`: is the condition that specifies the partition key value of the items that need to be retrieved; you can see in this syntax the name of the partition key which is `Id` and its particular value is denoted with another parameter `:Id` which is defined in the next argument `ExpressionAttributeValues`. To understand more about this syntax, you can always check the [documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb/client/query.html). The parameter: `ReturnedConsumedCapacity` determines what level of detail about the consumed capacity the response should return.

  ```python
  kwargs = {'ReturnConsumedCapacity': 'TOTAL', 
          'KeyConditionExpression': 'Id = :Id',
          'ExpressionAttributeValues': {':Id': 
	          {'S': 'Amazon DynamoDB#DynamoDB Thread 1'}}
          } 

# returns the items that has ID = 'Amazon DynamoDB#DynamoDB Thread 1'
response = query_db(table_name=reply_table['table_name'], **kwargs) 
               
print(response)
  ```

You can also query the items that share the **same partition key** and also **satisfy a certain condition on the sort key**. Since the sort key of the Reply table is a timestamp, you can add a condition to `KeyConditionExpression` to get the replies of a particular thread that were posted after a certain time. Take a closer look at how the sort key is compared with the `:ts` parameter and how this parameter is defined in the `ExpressionAttributeValues`.

```python
kwargs = {'ReturnConsumedCapacity': 'TOTAL', 
          'KeyConditionExpression': 'Id = :Id and ReplyDateTime > :ts',
          'ExpressionAttributeValues': 
	          {':Id': 
		          {'S': 'Amazon DynamoDB#DynamoDB Thread 1'}, 
	            ':ts' : 
	              {'S':"2015-09-21"}
              }
          }

response = query_db(table_name=reply_table['table_name'], **kwargs)

print(response)
```

In addition to `keyConditionExpression`, you can also use `FilterExpression` to filter the results based on **non-key attributes**. For example, to find all the replies to Thread 1 that were posted by User B, you can do:
```python
kwargs = {'ReturnConsumedCapacity': 'TOTAL', 
          'KeyConditionExpression': 'Id = :Id ',
          'FilterExpression': 'PostedBy = :user',
          'ExpressionAttributeValues': 
	          {':Id': 
		            {'S': 'Amazon DynamoDB#DynamoDB Thread 1'}, 
                ':user' : 
	                {'S':'User B'}
                }          
          }

response = query_db(table_name=reply_table['table_name'], **kwargs)

print(response)
```

Note that in the response you will see these lines:

```
"Count": 1,
"ScannedCount": 2,
```

This tells you that the Key Condition Expression matched 2 items (ScannedCount based on the value of the partition key) and that's what you were charged to read, but the Filter Expression reduced the result set size down to 1 item (Count).

#### Filtering the Table Scans

The `DynamoDB scan()` method is similar to the `DynamoDB query()` method except that you are scanning the whole table, not just a single Item Collection, so there is no Key Condition Expression that you need to specify for `DynamoDB scan()`. However, you can specify a `FilterExpression` which will reduce the size of the result set (even though it will not reduce the amount of capacity consumed).

```python
def scan_db(table_name: str, **kwargs):
    client = boto3.client("dynamodb")
    response = client.scan(TableName=table_name, **kwargs)
    
    return response
```

```python
kwargs = {'ReturnConsumedCapacity': 'TOTAL', 
          'FilterExpression': 'PostedBy = :user', 
          'ExpressionAttributeValues': {':user' : {'S':'User A'}}
        }

response = scan_db(reply_table['table_name'], **kwargs)
print(response)
```

Note that in the response you will see these lines:

```
"Count": 1,
"ScannedCount": 2,
```

This tells you that the Key Condition Expression matched 2 items (ScannedCount based on the value of the partition key) and that's what you were **charged** to read, but the Filter Expression reduced the result set size down to 1 item (Count).

Let's take the `LastEvaluatedKey` field and use it for the next table scan:

```Python
last_evaluated_key = response.get("LastEvaluatedKey")
print(last_evaluated_key)
```

So you can invoke the scan request again, this time passing that `LastEvaluatedKey` value to the `ExclusiveStartKey` parameter:

```python
kwargs = {'ReturnConsumedCapacity': 'TOTAL', 
          'FilterExpression': 'PostedBy = :user', 
          'ExpressionAttributeValues': {':user' : {'S':'User A'}},
          'Limit': 2,
          'ExclusiveStartKey': last_evaluated_key
        }

response = scan_db(reply_table['table_name'], **kwargs)
print(response)
```


## INSERT and UPDATE date

#### Insert
The `DynamoDB put_item()` method is used to create a new item or to replace existing items with a new item. You have already created the `put_item_db()` function to load data item-by-item to some tables. Now, let's say we wanted to insert a new item into the Reply table. 

```python
new_item = {
        "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
        "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"},
        "Message" : {"S": "DynamoDB Thread 2 Reply 3 text"},
        "PostedBy" : {"S": "User C"}
    }

kwargs = {'ReturnConsumedCapacity': 'TOTAL'}
    

response = put_item_db(table_name=reply_table["table_name"], item=new_item, **kwargs)
print(response)
```

Output:
```terminal
{'ConsumedCapacity': {'TableName': 'de-c2w1-dynamodb-Reply', 'CapacityUnits': 1.0}, 'ResponseMetadata': {'RequestId': 'M13IA6ISRS13GLUSGJF4R1Q5KFVV4KQNSO5AEMVJF66Q9ASUAAJG', 'HTTPStatusCode': 200, 'HTTPHeaders': {'server': 'Server', 'date': 'Wed, 26 Nov 2025 20:02:54 GMT', 'content-type': 'application/x-amz-json-1.0', 'content-length': '79', 'connection': 'keep-alive', 'x-amzn-requestid': 'M13IA6ISRS13GLUSGJF4R1Q5KFVV4KQNSO5AEMVJF66Q9ASUAAJG', 'x-amz-crc32': '4237884917'}, 'RetryAttempts': 0}}
```

You will see in the response that this request consumed 1 Write Capacity Unit (WCU) (One write capacity unit represents one write per second for an item up to 1 KB in size. [reference](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html)).

#### Update Data
The `DynamoDB update_item()` method can be used to edit an existing item's attributes or add a new item to the table if it does not already exist. 

"This method requires that you provide the **primary key** of the item that you want to update. You must also provide an **update expression** (`UpdateExpression`), indicating the attributes that you want to modify and the values that you want to assign to them" ([developer's guide](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithItems.html)). For more information about the format of the update expression, check [here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.UpdateExpressions.html). You can also specify a condition expression to determine which items should be modified.

```python
def update_item_db(table_name: str, key: Dict[str, Any], **kwargs):
    client = boto3.client("dynamodb")

    response = client.update_item(
        TableName=table_name, Key=key, ReturnValues="UPDATED_NEW", **kwargs
    )

    return response
```

```python
kwargs= {    
    'UpdateExpression': 'SET Messages = :newMessages',
    'ConditionExpression': 'Messages = :oldMessages',
    'ExpressionAttributeValues': {
        ":oldMessages" : {"N": "4"},
        ":newMessages" : {"N": "5"}
    }
}
response = update_item_db(table_name=forum_table['table_name'], key={'Name' : {'S': 'Amazon DynamoDB'}}, **kwargs)
print(response)
```

You can also use the UpdateExpression to APPEND, DELETE attributes and more.

e.g.
```python
kwargs = {
    'UpdateExpression': 'SET #Color = list_append(#Color, :values)',
    'ExpressionAttributeNames': {'#Color': 'Color'},
    'ExpressionAttributeValues': 
	    {':values': {'L': [{'S': 'Blue'}, {'S': 'Yellow'}]}},
    'ReturnConsumedCapacity': 'TOTAL'
}

kwargs = {
    'UpdateExpression': 'REMOVE #Color[2], #Color[3]',
    'ExpressionAttributeNames': {'#Color': 'Color'},
    'ReturnConsumedCapacity': 'TOTAL'
}
```


## DELETE

The `DynamoDB DeleteItem()` method is used to **delete** an item. Deletes in DynamoDB are **singleton** operations. There is no single command you can run that would delete all the rows in the table. Let's delete one of the items we previously added to the Reply table; for that, you need to reference the **full Primary Key**. Remember that the Reply table has `Id` as the partition key and `ReplyDateTime` as the sort key, so the complete Primary Key is composed of those two keys. Follow the instructions to create the `delete_item_db()` function.

```python
def delete_item_db(table_name: str, key: dict[str, Any], **kwargs):

    client = boto3.client("dynamodb")
    response = client.delete_item(TableName=table_name, Key=key, **kwargs)
    
    logging.info(f"response {response}")
```

```python
key = {"Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
       "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"}
       }

delete_item_db(table_name=reply_table['table_name'], key=key)

```


## Transactions (Optional section)

The `DynamoDB transact_write_items` is a **synchronous** write operation that **groups up to 100 action requests**, with a collective size limit of 4MB for the entire transaction. 

These actions can operate on items in various tables, though not across distinct AWS accounts or Regions. Additionally, no two actions can target the same item. 

The execution of actions is **atomic**, ensuring that *either all of them succeed or all of them fail*.

**Why is transactions important?**
You have seen that the sample data includes interconnected tables: `Forum`, `Thread`, and `Reply`. When adding a new `Reply` item, there's a need to increment the `Messages` count in the associated `Forum` item. **This operation should occur within a transaction** to guarantee that both changes either succeed or fail simultaneously. Any observer reading this data should witness both changes or none at the same time.

DynamoDB transactions adhere to the concept of **idempotency**, allowing the submission of the same transaction multiple times. However, DynamoDB will execute it only once. This feature is particularly valuable when working with APIs that lack inherent idempotency, such as using `update_item` to modify a numeric field. During transaction execution, you specify a string as the `ClientRequestToken` (also known as Idempotency Token). 

```python
def transact_write_items_db(transaction_items: List[Dict[str, Any]], **kwargs):

    client = boto3.client("dynamodb")
    response = client.transact_write_items(TransactItems=transaction_items, **kwargs)


    return response
```

```python
transaction_items=[
    {
        "Put": {
            "TableName" : reply_table['table_name'],
            "Item" : {
                "Id" : {"S": "Amazon DynamoDB#DynamoDB Thread 2"},
                "ReplyDateTime" : {"S": "2021-04-27T17:47:30Z"},
                "Message" : {"S": "DynamoDB Thread 2 Reply 3 text"},
                "PostedBy" : {"S": "User C"}
            }
        }
    },
    {
        "Update": {
            "TableName" : forum_table['table_name'],
            "Key" : {"Name" : {"S": "Amazon DynamoDB"}},
            "UpdateExpression": "ADD Messages :inc",
            "ExpressionAttributeValues" : { ":inc": {"N" : "1"} }
        }
    }
]

kwargs = {'ClientRequestToken': 'TRANSACTION1'}

response = transact_write_items_db(transaction_items=transaction_items, **kwargs)
print(response)
```

If the transaction is executed again with the same value of the `'ClientRequestToken'` as `'TRANSACTION1'` you can see that other invocations of the transaction are ignored and the `Messages` attribute remains the value at 5. 

## CLEANUP

Delete the created tables from DynamoDB. Check the provided function in the next cell `delete_table_db()` and execute the next cells to delete the tables.

```python
def delete_table_db(table_name: str):
        client = boto3.client("dynamodb")
        response = client.delete_table(TableName=table_name)
        return response
```

```python
for dynamodb_tab in [product_catalog_table, forum_table, reply_table, thread_table]:
    response = delete_table_db(table_name=dynamodb_tab['table_name'])
    print(response)
```

