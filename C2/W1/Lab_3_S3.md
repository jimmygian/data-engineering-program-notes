Check [Object Storage](../Object-Storage)'s S3 Document.
Lab link: https://www.coursera.org/learn/source-systems-data-ingestion-and-pipelines/ungradedLab/6tu85/practice-lab-3-interacting-with-amazon-s3-object-storage

- you'll work with AWS Object Storage and Amazon S3. 
- You will create an S3 bucket, 
- query data from the bucket, and 
- work with object versioning in S3. 

In terms of CRUD operations & data management:
- You will load into the S3 bucket 
	- structured data from a CSV file, 
	- semi-structured data from a JSON file, and 
	- unstructured data from an image file.
	
- You will also interact with the S3 bucket through the AWS management console and programmatically using `boto3` (the AWS Software Development Kit (SDK) for Python).
```python
  import boto3
  ```


To upload your data to Amazon S3, you need first to create an **S3 bucket** in one of the **AWS Regions**. 

## Creating an S3 Bucket

To create an S3 bucket, you need to specify
- the bucket name and 
- the region for your bucket. 

In this lab, the region is set to `us-east-1`. Regarding the name of the bucket, Bucket names should be **globally unique** to avoid collisions with buckets that other learners could be creating or working with at the same time. To ensure the uniqueness of the bucket name, you will use your Vocareum's AWS Account ID to include it in the bucket name.

```python
AWS_ACCOUNT_ID = subprocess.run(
	['aws', 
	 'sts', 
	 'get-caller-identity', 
	 '--query', 'Account', 
	 '--output', 'text'
	], 
	capture_output=True, 
	text=True
).stdout
 .strip()

BUCKET_NAME = f'de-c2w1lab3-{AWS_ACCOUNT_ID}'

AWS_DEFAULT_REGION = 'us-east-1'
```

This code snippet is a Python command that uses the `subprocess` module to execute an **AWS CLI** command. Here's a breakdown of what each part does:

1. **`subprocess.run([...])`**: This function is used to run a command in the *shell*. The command is specified as a **list of strings**, where each string is a part of the command.

2. **Command List**:
   - `'aws'`: This is the AWS Command Line Interface (CLI) command.
   - `'sts'`: This specifies the AWS Security Token Service (STS), which is used to obtain temporary security credentials.
   - `'get-caller-identity'`: This command returns details about the IAM identity whose credentials are used to call the command.
   - `'--query', 'Account'`: This option filters the output to only return the `Account` field, which contains the **AWS account ID**.
   - `'--output', 'text'`: This option specifies that the output should be in plain text format.

3. **`capture_output=True`**: This argument captures the standard output and standard error of the command, allowing you to access it programmatically.

4. **`text=True`**: This argument indicates that the output should be treated as a string (text) rather than bytes.

5. **`.stdout.strip()`**: After the command is executed, `.stdout` accesses the standard output of the command. `.strip()` is then used to remove any leading or trailing whitespace from the output.

The result of this entire operation is that **`AWS_ACCOUNT_ID`** will contain the **AWS account ID** of the caller, as a string, with any extra whitespace removed. This is useful for scripts that need to programmatically determine the AWS account they are operating in.

To programmatically create the bucket in Python using boto3, you can use the `S3` method [`create_bucket()`](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/create_bucket.html) which requires a Client object (as explained in the DynamoDB lab).

The following function `create_s3_bucket()` consists of the steps needed to create the S3 bucket (instantiating a Client object and then calling the method `create_bucket()`). The function takes in as input the bucket name and the region.

```python
def create_s3_bucket(bucket_name:str , region: str):
    
    # Create an S3 client
    s3_client = boto3.client('s3', region_name=region)

    # Create the S3 bucket
    try:
        s3_client.create_bucket(Bucket=bucket_name)
        print(f"S3 bucket '{bucket_name}' created successfully in region '{region}'.")
    except Exception as e:
        print(f"An error occurred: {e}")
```

```python
create_s3_bucket(bucket_name=BUCKET_NAME, region=AWS_DEFAULT_REGION)
```

You can check that the bucket has been created using the `aws cli` tool. To list the buckets created in your account, you can use the following command:
```shell
aws s3 ls
```

You can run the command in the terminal or you can run it in the jupyter notebook but you need to add an exclamation mark `!` at the beginning of the command. This allows you to run shell commands in a code cell.

To list the objects stored inside a bucket, you can use the command `aws s3 ls <your-bucket-name>`. If you now run this command, no result will be shown since the bucket is still empty.

```python
!aws s3 ls $BUCKET_NAME
```

You can also inspect the S3 bucket in the AWS Console. 
- Search for **S3**. 
- You will see the bucket with the name you provided. 
- You can check that the bucket is empty by simply clicking on it.


## Uploading Files

To programmatically upload this CSV file to the bucket, you can use the S3 method [upload_file()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/upload_file.html). This method expects three arguments: 
- the path of the source file that you want to upload (Filename), 
- the name of the bucket to upload to (Bucket) 
- and the object key or name (Key). 
  The last argument specifies how you want to label the uploaded object or file within the bucket, this name should uniquely identify the uploaded object.

The following function `upload_file_to_s3()` consists of the steps needed to upload the file to the S3 bucket (instantiating a Client object and then calling the method `upload_file()`). The function takes in as input the path to the local file to upload, the bucket name, and the object key.

```python
def upload_file_to_s3(local_file_path: str, bucket_name: str, object_key: str) -> None:
    """Uploads a local file to S3 using boto3

    Args:
        local_file_path (str): Local file path
        BUCKET_NAME (str): Bucket name
        object_key (str): the key name, which should uniquely identifies the uploaded object in the bucket
    """
    # Create an S3 client
    s3_client = boto3.client('s3')

    # Upload the file to S3
    try:
        s3_client.upload_file(local_file_path, bucket_name, object_key)
        print(f"File {local_file_path} uploaded to s3://{bucket_name}/{object_key} successfully.")
    except Exception as e:
        print(f"Error uploading file to S3: {e}")
```

> 	*Note*: remember that object storage has a **flat** structure. When you use the delimiter `/` in the object name or key, like in this example: `object_key = 'csv/ratings_ml_training_dataset.csv'`, you're including a **key name prefix** that is used by S3 to **group objects** inside the bucket. The console uses the terminology `folder` because this grouping of objects can be analogous to a folder in a regular file system. You can learn more about object keys [here](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-keys.html).


## Quering the Data

You can query the data from your csv files by using **AWS Athena**. This is something you have done in previous labs like in C1W2 Assignment and you will continuing doing it in future labs. 

In general, **AWS Athena** allows to **query data in different formats**, like parquet, tsv, csv, etc. As you will continue querying data with Athena in future labs, for this particular lab you will only be pointed to some documentation that you can read to have some insights about it:

* [Querying data from the AWS console](https://builtin.com/articles/aws-architecture-athena-query-csv-table-stored-s3)
* [Querying data from multiple sources at AWS forum](https://repost.aws/questions/QUeZq3d77YQ8-9EPtDDBe6RQ/query-data-from-multiple-sources-in-s3-on-athena)
* [Usage of delimiter at AWS documentation](https://docs.aws.amazon.com/athena/latest/ug/lazy-simple-serde.html)


## Downloading from S3

Now, instead of querying the JSON file, you will download it using the `S3` [download_file()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/download_file.html) method, which is called in the following provided function.

```python
def download_object_from_s3(bucket_name: str, object_key: str, local_file_path: str) -> None:
    """Downloads object from S3 using boto3

    Args:
        bucket_name (str): Bucket name
        object_key (str): Object key in S3.
        local_file_path (str): Path in the local file system to put the downloaded object.
    """
    # Create an S3 client
    s3_client = boto3.client('s3')
    
    try:
        # Download the file to a local directory
        s3_client.download_file(bucket_name, object_key, local_file_path)
    except Exception as e:
        print(f"Error downloading or printing JSON file: {e}")

```


## Uploading unstructured data to AWS

Finally, you will work with unstructured data. 
- You will upload a `.jpg`  image to the bucket and, this time, 
- download it from a browser (to show you the various ways you can download objects from an S3 bucket). 

By default, an S3 bucket and its objects are **private**. To be able to download S3 objects from a browser, you will have to make some modifications to the bucket to make some of its objects available for public reading. 
- First, you need to configure the bucket to accept **public policies** and **public Access Control Lists (ACLs)**. To do so, you will use the method `S3 put_public_access_block`. To understand what this method expects as arguments, check the following [documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/put_public_access_block.html).

```python

def s3_public_access_setup(bucket_name: str, public_access_block_configuration: Dict[str, Any]) -> None:
    """Sets public access configuration for S3 bucket

    Args:
        bucket_name (str): Bucket name
        public_access_block_configuration (Dict[str, Any]): Configuration for public access
    """
    
    s3_client = boto3.client('s3')
    
    # Update the bucket's public access settings
    s3_client.put_public_access_block(
        Bucket=bucket_name,
        PublicAccessBlockConfiguration=public_access_block_configuration
    )
```

```python
# Define the public access settings  
public_access_configuration = {
    'BlockPublicAcls': False,
    'IgnorePublicAcls': False,
    'BlockPublicPolicy': False,
    'RestrictPublicBuckets': False
}

s3_public_access_setup(bucket_name=BUCKET_NAME, 
                       public_access_block_configuration=public_access_configuration)
```

You just modified the bucket so that it now accepts public access rules to its objects. You'll now attach a policy to the bucket to allow **anyone on the internet to have reading access** to the objects whose key starts with `images/`. 
> 	*("A policy is an **object** in AWS that, when associated with an identity or resource, defines their permissions", [reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html). You will learn more about policies in the next lesson or you can check the documentation [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)).*

To attach the mentioned policy to the S3 bucket, you'll use the `S3` [put_bucket_policy()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/put_bucket_policy.html) method, define the details of the policy and pass in the policy to `S3 put_bucket_policy()`. Run the following three cells to attach the appropriate policy to the S3 bucket.

```python
def s3_put_bucket_policy(bucket_name: str, policy: Dict[str, Any]) -> None:
    """Allows to put bucket policies

    Args:
        bucket_name (str): Bucket name
        policy (Dict[str, Any]): Bucket policy
    """
    
    s3_client = boto3.client('s3')
    response = s3_client.put_bucket_policy(Bucket=bucket_name, Policy=json.dumps(policy))
    return response
```

```python
policy = { 
    "Version": "2012-10-17", 
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": f"arn:aws:s3:::{BUCKET_NAME}/images/*"
        }
    ]
}
```

This policy allows anyone (`"Principal": "*"`) to use the method `S3 GetObject` on `{BUCKET_NAME}/images/`, i.e., to retrieve objects stored in your s3 bucket and whose key/name starts with `images/`. You can learn more about such policy [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#access_policies-json).


### Versioning

Let's see how the Bucket Versioning works. You need first to enable this feature in your bucket by calling the method `S3` [put_bucket_versioning()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/put_bucket_versioning.html) and switching on versioning.

```python
def configure_bucket_versioning(bucket_name: str, versioning_config: Dict[str, str]) -> Dict[Any, Any]:
    
    s3_client = boto3.client('s3')

    # Enable bucket versioning
    response = s3_client.put_bucket_versioning(
        Bucket=bucket_name,
        VersioningConfiguration=versioning_config
    )

    return response
```

```python
versioning_config = {'Status': 'Enabled'}

response = configure_bucket_versioning(bucket_name=BUCKET_NAME, 
              versioning_config=versioning_config)

print(response)
```

### Listing S3 files using boto3
```python
def list_objects_in_folder(bucket_name: str, prefix_key: str):
    # Create an S3 client
    s3_client = boto3.client('s3')

    # Use list_objects_v2 to list objects in the specified folder
    response = s3_client.list_objects_v2(
        Bucket=bucket_name,
        Prefix=prefix_key
    )

    # Check if objects were found
    if 'Contents' in response:
        # Print each object's key
        print("Objects with a key that starts with '{}':".format(prefix_key))
        for obj in response['Contents']:
            print(obj['Key'])
    else:
        print("No objects found in folder '{}'.".format(prefix_key))
```

```python
list_objects_in_folder(bucket_name=BUCKET_NAME, prefix_key='images')
```

To see all versions of a file:
```python
def list_object_versions(bucket_name: str, prefix_key: str) -> None:
    # Create an S3 client
    s3_client = boto3.client('s3')

    # List object versions
    response = s3_client.list_object_versions(Bucket=bucket_name, Prefix=prefix_key)

    # Process the response to get object versions
    for version in response.get('Versions', []):
        print("Object Key:", version['Key'])
        print("Object Version Id:", version['VersionId'])
        print("Is Latest:", version['IsLatest'])
        print("Last Modified:", version['LastModified'])
        print()

list_object_versions(bucket_name=BUCKET_NAME, prefix_key='images/')
```


## Deleting a bucket

To delete the bucket, you need to make sure i**t is empty** before the deletion process. And for that, there are two methods that you can use: `S3` [delete_object()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/delete_object.html) and `S3` [delete_bucket()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/delete_bucket.html).

In the next cell, you are provided with a function that makes use of `S3 delete_object()` and `S3 delete_bucket()`. This function takes as an input the boolean parameter`delete_objects`; this boolean parameter is used to indicate if the bucket is empty or not. If the bucket contains objects, then the function first deletes the objects and then the bucket. Otherwise, the function directly deletes the bucket. Note that you need to delete all object versions. The deletion of versions is necessary only if you have enabled Bucket Versioning. Note that the function also removes delete markers. These are placeholders that are created after you delete objects in a versioning-enabled bucket. You can learn more about them [here](https://www.learnaws.org/2022/10/04/aws-s3-delete-marker/#what-is-an-aws-s3-delete-marker).

***Note:** It is important to take into account that when you are working with S3 buckets in real life and production environments, you SHOULD NOT delete them or delete the objects within them unless you are completely sure about what you are doing. Make sure that the bucket/objects are not used anymore by any upstream or downstream process. This is something you should do with caution and after talking with bucket/object owners, stakeholders, and other process owners who may depend on the information hosted in that bucket.*

```python
def s3_delete_bucket(bucket_name: str, delete_objects: bool) -> Dict[Any, Any]:
    s3_client = boto3.client('s3')
    
    if delete_objects:
        # List all versions of all objects in the bucket
        response = s3_client.list_object_versions(Bucket=bucket_name)
        
        # Delete all object versions
        for version in response.get('Versions', []):
            key = version['Key']
            version_id = version['VersionId']
            s3_client.delete_object(Bucket=bucket_name, Key=key, VersionId=version_id)
        
        # Delete all delete markers
        for delete_marker in response.get('DeleteMarkers', []):
            key = delete_marker['Key']
            version_id = delete_marker['VersionId']
            s3_client.delete_object(Bucket=bucket_name, Key=key, VersionId=version_id)        
    
    # Delete the bucket
    response = s3_client.delete_bucket(
        Bucket=bucket_name
    )

    return response

response = s3_delete_bucket(bucket_name=BUCKET_NAME, delete_objects=True)
print(response)
```

Finally, check that the bucket no longer exists.

```python
!aws s3 ls
```

or 

```Shell
aws s3 ls
```
