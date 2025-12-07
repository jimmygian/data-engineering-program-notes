
# Streaming Ingestion

In the previous course, you implemented a streaming pipeline that looked like this with a **Kinesis Data Firehose** and **S3 bucket**. 
![[Screenshot 2025-12-07 at 13.55.17.png]]
- You were provided with a **Kinesis Data Stream** that streams online user activities as events or records. 
- You process these records to compute the product recommendations and use the **Data Firehose instance** to deliver the records to the **S3 bucket** in your pipeline. 

In the upcoming lab, you'll learn more about how you can continue streaming these records in your pipeline and further explore Kinesis Data Streams as a source. 

The lab consists of two parts. In the first part, you will work with a **Kinesis Data Stream** acting as a **router** between a simple producer and a simple consumer. 
![[Screenshot 2025-12-07 at 13.58.26.png]]

In the second part, you'll work again with the Course 1 scenario. 
- You'll be provided with a **Kinesis Data Stream** as a source, but this time, you will use **another two Kinesis Data Streams** to continue streaming the records in your pipeline. 
- From each of these two new Data Streams, data will be taken by a **Data Firehose** and delivered to the respective **S3 bucket**.

![[Screenshot 2025-12-07 at 13.59.48.png]]


## 1 - Understanding the Components of an Event-Streaming Platform

An event-driven architecture consists of a producer, a router (buffer/message broker) and a consumer. In this first part of the lab, you will work with these three components:
![[Screenshot 2025-12-07 at 13.58.26.png]]

- **router**: you will create a **Kinesis Data Stream** that will act as a *router* between a producer and a consumer;

- **producer**: in the folder `src/cli`, you can find the Python script: `producer_from_cli.py`. This script contains code that writes one record to a Kinesis Data Stream. You will call this script from a command line interface (CLI) and pass in two arguments: the name of the Kinesis Data Stream and a JSON string representing the message record.

- **consumer**: in the same folder `src/cli`, you can find the Python script: `consumer_from_cli.py` which you will also call from the command line interface (CLI). It takes in one argument which is the name of the Kinesis Data Stream from which the consumer will read the message records.


### 2.1 - Creating the Infrastructure for the Streaming ETL Process


**Introduction**
Now that you have an understanding of the components of an event-streaming platform, let's get back to our original e-commerce scenario. You are provided with a **Kinesis Data Stream** which is now your **source system** (assume it was set up by the software engineer). The producer that feeds into the data stream is also provided to you, and you are on the consumer side. You will need to: 
- **ingest data** from the data stream, 
- **perform** a simple **transformation** on the ingested data 
- and then **send the transformed data to one of two Kinesis Data Streams**. 

For you to explore the results, each of those data streams will then be delivered through a **Kinesis Firehose** to their respective **S3 bucket**.

You will start with creating the necessary resources using `boto3` (Two Kinesis Data Streams, two Kinesis Firehose instances, and two S3 buckets). Let's first understand why you need to create more than one data streams inside your data pipeline.

**Creating the Infrastructure**

Assume you were asked to filter the customers based on their countries. This is because your company noticed that customers showed different purchase behaviors based on their countries. 
- So if they are located in the USA, their online activities need to be processed by a certain recommendation engine. 
- Otherwise, their online activities need to be processed by another recommendation engine.

To do so, you need to send the records of USA customers to a **different data stream** from those of other countries, and then you need to send data from each data stream to a different bucket to be processed differently by downstream users. 

> 	*This means that you have to create a Kinesis Data Stream, a Kinesis Firehose, and an S3 bucket for USA customers. And then the same set of resources for International customers.*

How would you know the location of customers? The data that you will ingest corresponds to the users' browsing history on the e-commerce web page. This is the same data you worked with in the lab of Week 4 of Course 1 (the same structure you produced manually in the first part of this lab) and it contains the city and country of each customer: 

```json
{
    "session_id": "a1",
    "customer_number": 100,
    "city": "Washington",
    "country": "USA",
    "credit_limit": 1000,
    "browse_history": [
        {
            "product_code": "Product1",
            "quantity": 2,
            "in_shopping_cart": true
        },
        {
            "product_code": "Product2",
            "quantity": 1,
            "in_shopping_cart": false
        }
    ]
}
```


Now you want to create the two buckets which follow this naming convention:

- USA: `de-c2w2lab1-{ACCOUNT_ID}-usa`
- International: `de-c2w2lab1-{ACCOUNT_ID}-international`

```Python
import boto3

USA_BUCKET = f'de-c2w2lab1-{ACCOUNT_ID}-usa'
INTERNATIONAL_BUCKET = f'de-c2w2lab1-{ACCOUNT_ID}-int'


def create_s3_bucket(bucket_name: str, region: str) -> None:
   # Call the boto3 client with the `'s3'` resource and region. 
    s3_client = boto3.client('s3', region_name=region)
    
    # Create the S3 bucket
    try:
        s3_client.create_bucket(Bucket=bucket_name)
        print(f"S3 bucket '{bucket_name}' created successfully in region '{region}'.")
    except Exception as e:
        print(f"An error occurred: {e}")

# Create the USA bucket
create_s3_bucket(bucket_name=USA_BUCKET, region=AWS_DEFAULT_REGION)
    
# Create the international bucket
create_s3_bucket(bucket_name=INTERNATIONAL_BUCKET, region=AWS_DEFAULT_REGION)

```

**<span style="font-size:18px">2.1.3.</span>** Remember from Week 1 that you can use the `aws` command to check if the buckets exist.

```Python
!aws s3 ls
```

```Terminal
2025-12-07 14:44:32 de-c2w2lab1-007413979805-int 
2025-12-07 14:44:31 de-c2w2lab1-007413979805-usa
```



Now, let's create the two Kinesis Data Streams. To do so, you will use the function `create_kinesis_data_stream()`.

- USA: `de-c2w2lab1-usa-data-stream`
- International: `de-c2w2lab1-international-data-stream`

```Python
USA_DATA_STREAM = 'de-c2w2lab1-usa-data-stream'
INTERNATIONAL_DATA_STREAM = 'de-c2w2lab1-international-data-stream'

def create_kinesis_data_stream(stream_name: str, shard_count: int = 2) -> None:
    # Call the boto3 client with the `kinesis` resource.  Store the object in `client`.
    client = boto3.client("kinesis")

    # Check if the stream already exists
    if stream_name in client.list_streams()["StreamNames"]:
        print(f"Kinesis data stream {stream_name} already exists")
        return
    
    # Use the `create_stream()` method from the client and pass the data stream name and the shard count.
    response = client.create_stream(StreamName=stream_name, ShardCount=shard_count)
    print("Kinesis data stream created:", response)

# Create the USA data stream
create_kinesis_data_stream(stream_name=USA_DATA_STREAM, shard_count=2)

# Create the International data stream
create_kinesis_data_stream(stream_name=INTERNATIONAL_DATA_STREAM, shard_count=2)
```


Using `boto3`, you can also check the status of the resources. Use the provided function `is_stream_ready()` to see if your data streams are ready to be used.

*Note*: If you get `false` in the output here, wait for a few seconds and try to re-run the cell again.

```Python
def is_stream_ready(stream_name: str) -> None:
    client = boto3.client("kinesis")
    response = client.describe_stream(StreamName=stream_name)
    return response["StreamDescription"]["StreamStatus"] == "ACTIVE"

# Check if the streams are ready
print(is_stream_ready(stream_name=USA_DATA_STREAM))
print(is_stream_ready(stream_name=INTERNATIONAL_DATA_STREAM))
```

```Terminal
True 
True
```


You will finally create the two Kinesis Firehose instances for the two locations, `usa` and `international`. To do so, you will call the following function: `create_kinesis_firehose()` that is provided in the next cell. This function makes use of the `boto3` client method [create_delivery_stream()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/firehose/client/create_delivery_stream.html). 

If you check the arguments passed into this method, you will notice that the source and destination for the Kinesis Firehose are configured (source: **Kinesis Data Stream** and destination: **S3** bucket). Once these resources are configured, Kinesis Firehose will be able to automatically read from the Kinesis Data Stream and automatically write to the S3 bucket. 

The *role name* passed into the configuration of the source and destination, represents the **IAM role** that will be attached to the Kinesis Firehose *to allow it to read from a Kinesis Data Stream and write to an S3 bucket* (note that `arn` means Amazon resource name and it is used to uniquely identify AWS resources, you can learn more about it [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference-arns.html#arn-syntax-kinesis-streams)).
  
In the following cell, you will need to call the function `create_kinesis_firehose()` twice for the `usa` and `international` cases. 