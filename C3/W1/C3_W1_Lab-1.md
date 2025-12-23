
In the first lab of this week, you'll explore some features of 
- Cloud object storage, 
- file storage, and 
- block storage 
through a set of exercises. 
- You will also interact with the **RAM of the server** that's hosting the lab to understand how in-memory storage features like *caching* can significantly enhance data retrieval performance over disk-based storage systems.

**Object Storage**

**Object storage systems** are a type of data storage architecture that stores data as objects rather than in a traditional file hierarchy.

In object storage systems, data is stored as **discrete units** called **"objects"**. Each object consists of the actual data (*payload* or *content*) along with *metadata* attributes that describe the object. Metadata typically includes information such as object name, unique identifier (key), size, content type, creation/modification timestamps, and custom user-defined metadata.

Unlike file storage systems, object storage systems do not use a hierarchical directory structure. Instead, objects are stored in a flat namespace, **identified by unique keys or identifiers**. This flat architecture allows for easier scalability and distribution of data across multiple storage nodes. Object storage contains **immutable objects of various sizes and types.** Unlike files on a local disk, **objects cannot be modified in place.**

Between other features of object storage systems you can find:

- **Durability and Redundancy:** Object storage systems typically employ data redundancy and replication techniques to ensure data durability and availability. Objects are often replicated across multiple storage nodes or data centers to protect against hardware failures, data corruption, and other types of data loss.

- **Access Methods:** Objects are accessed using standardized application programming interfaces (APIs) such as **Amazon S3 API**. These APIs provide a set of operations for storing, retrieving, updating, and deleting objects, as well as managing object metadata and access control.

- **Data Access Patterns:** Object storage systems are well-suited for storing unstructured data and large volumes of data, such as multimedia files, backups, archives, log files, and data lakes. They are commonly used in cloud storage services, content delivery networks (CDNs), and big data analytics platforms.


With object storage, you will explore the **flat structure** of the storage option and the **immutability** of its objects. 
- You'll use **boto3** to upload a CSV file containing some employee information to an S3 bucket. 
- When you upload the file to the bucket, you'll choose the key `data/csv/employee.csv`, as the **identifier** for the created object. 
- The prefix `data/csv` is just **part of the object key** and does not represent an actual directory.
	- the prefix `data/csv` does not represent an actual object and the S3 bucket, while `data/csv/employee.csv` does represent an object. 
- You can create different objects that share the same prefix. 
	- For instance, if you want to organize your sales data by year, you can specify the year in the object key and use it as a prefix to represent all the data objects that correspond to the sales of that particular year. This helps you organize your data and more quickly retrieve the objects from the right bucket. Now, since objects are immutable, you can't modify them in place. 
- If you upload an object using the same key of an object that already exists in your bucket, then the old object **will be replaced with the new object**. 
- Or as you've seen in the previous course, you can enable **object versioning** so that when you upload an object using the existing key, the old version is kept and a new version of the object is created. 


![[Screenshot 2025-12-23 at 13.42.20.png]]


- In this lab, you will again experiment with ***versioning*** in the provided bucket. 
- You'll modify the employees data and then upload it again using the same key. 
- You'll then use the `boto3 list_object_version` method to verify that a new version of the object is created. 
	- This method returns the **metadata** of the old and new versions of the object


**File Storage**
![[Screenshot 2025-12-23 at 15.23.07.png]]

A **File Storage System**, also known as a file system, is a method used by operating systems to manage and store data on storage devices such as hard disk drives (HDDs), solid-state drives (SSDs), and network-attached storage (NAS) devices. It provides a hierarchical structure for organizing files and directories, and it includes mechanisms for accessing, reading, writing, and modifying files.

Here are the key components of File Storage Systems

- File: A named collection of data stored on a storage device. Files can contain various types of data, such as text, documents, images, videos, and programs.

- Directory (or Folder): A container used to organize files into a hierarchical structure. Directories can contain files and other directories, allowing for the creation of a tree-like directory structure.

- File System Metadata: Information about files and directories stored by the file system, including attributes such as file name, size, type, permissions, creation/modification timestamps, and file location.

- File System Operations: Actions performed on files and directories, such as creating, reading, writing, deleting, renaming, moving, copying, and accessing files. These operations are typically performed using file system APIs or command-line utilities.

- File System Drivers: Software components responsible for interacting with storage devices and translating file system operations into low-level disk operations. These drivers enable the operating system to access and manipulate files stored on different types of storage media

You could be more accustomed to using File Storage Systems as it is the default storage system in an Operating System.


After you complete the exercises with object storage, you'll explore the hierarchical structure of **file storage systems**. In a file system, you might see a path like data/employees.csv. Here, data refers to a directory, which you can think of as a special file that contains information that allows you to access other files. 

![[Screenshot 2025-12-23 at 13.46.13.png]]


- In this lab, you'll use the **command line** to navigate the data directory, then explore its contents and metadata. 
- You will also explore **how the data is modified in place** in this type of storage. 

![[Screenshot 2025-12-23 at 14.03.36.png]]


**Block Storage**

**Block storage** is a type of data storage method commonly used in computing systems, particularly in enterprise environments and cloud computing infrastructures. In block storage, data is stored in **fixed-sized units called blocks**, each with a unique identifier, which are typically managed by a storage area network (SAN) or server-based operating systems. Additionally, Hard Disk Drives (HDD) and Solid State Drives (SSD) that are attached to a computer physically or via a network are examples of block storage devices.

One of the key features of block storage is **direct access to individual blocks of data**. This means that applications can read from or write to specific blocks without having to access the entire file or dataset. This direct access enables faster data retrieval and manipulation, making block storage particularly suitable for applications that require high-performance storage and computing. Examples include databases, virtual machine storage, and enterprise storage systems.

File systems are generally built **on top of block storage**, which is in general abstracted from you. To explore some of the block storage features in the lab, you'll be provided with a server that **emulates the behavior of block storage**. 

While block storage offers high performance through direct data access, it introduces **complexity** in terms of deployment, management, and maintenance, often requiring specialized expertise and inciting higher expenses. Additionally, block storage lacks inherent awareness of file structures, necessitating additional overhead for data organization within applications or file systems.



To simulate a block storage system, you have a simplified block storage server in an EC2 instance. Using a Python class called `BlockStorageClient` you will be able to connect to the server and send and receive files. When you send a file, the file will be divided into similar size blocks and each one will be sent to the server. For this exercise, you will upload the files located in the `data` folder to the server and then check that they were uploaded.

- You'll connect that server and send a file to it. 
- The server will then mimic how block storage stores data by decomposing the file into blocks. 
![[Screenshot 2025-12-23 at 14.04.31.png]]


**Memory**
Finally, you'll interact with the **RAM of the server** that's running the lab environment. 

**Memory-based Storage Systems**, also known as in-memory databases or caching systems, store data primarily in RAM (Random Access Memory) instead of on disk. These systems are designed to provide fast and efficient access to data by keeping it in memory, which allows for much quicker read and write operations compared to traditional disk-based storage systems.

- **Speed:** Since data is stored in memory, which has much faster access times compared to disk storage, operations such as reads, writes, and queries can be performed very quickly. This makes memory-based systems ideal for applications that require low-latency access to data, such as real-time analytics, caching, and high-performance transaction processing.

- **Caching:** One common use case for memory-based storage systems is caching frequently accessed data to improve the performance of applications. By storing frequently accessed data in memory, applications can **avoid repeatedly fetching data** from slower disk-based storage systems, resulting in faster response times and reduced load on backend databases.

- **Data Structures:** Memory-based storage systems often use specialized data structures optimized for fast access and manipulation of data in memory. Examples include hash tables, trees, and linked lists. These data structures are designed to minimize the overhead of memory management and provide efficient access to stored data.

- **Volatility:** Unlike disk-based storage, which retains data even when the power is turned off, memory-based storage systems are volatile, meaning that data is lost when the system is powered down or restarted. To mitigate this, some memory-based systems offer mechanisms for persistence, such as periodic snapshots or replication to disk-based storage for durability.

- **Scalability:** Memory-based storage systems can often scale horizontally by adding more nodes to distribute the data across multiple servers. This allows them to handle large volumes of data and high request rates, making them suitable for use in distributed environments and cloud computing platforms.

- **Use Cases:** Memory-based storage systems are commonly used in various applications, including **web servers**, content delivery networks (**CDNs**), **session** stores, **real-time analytics** platforms, and high-frequency **trading** systems. Any application that requires fast access to data or benefits from caching frequently accessed data can benefit from using memory-based storage.

Overall, memory-based storage systems offer significant performance advantages over disk-based storage systems, making them well-suited for applications that demand low-latency access to data and high throughput. However, they may also have limitations in terms of data durability and storage capacity compared to disk-based alternatives, so their use should be carefully considered based on the specific requirements of the application.

Although in this lab you are not going to work directly with an in-memory database, the idea is to give you some insight into certain features of those systems.

Earlier this week, you learned that **transferring data from memory is faster than transferring data from disk** and that **certain databases allow you to cache the query results in memory** so you can quickly access them. 

- You'll test this feature using the **cache Pandas package**. This package provides the `timed_LRU_cache` **decorator** that you can use to easily cache in memory. 
- Pandas data frames that are generated by functions so that the next time you run the scripts, it'll *return the cached data frame rather than running the function again*. 
- You'll use the decorator with a function that *reads a CSV file into a Pandas data frame*. 
- You will compare the *time it takes to read the file for the first time with the time it takes to read the same data stored in memory.* 

![[Screenshot 2025-12-23 at 14.05.33.png]]


But since memory has limited storage capacity, whenever you load data into memory, you need to make sure that **you have enough space**. To monitor your memory storage capacity, you'll use the command `htop`, which provides you with a **real time overview of your system's CPU usage, memory, and running processes as shown here.** 
![[Screenshot 2025-12-23 at 14.10.24.png]]
- At the top, the bars represent the usage of the system resources, including CPUs and memory. 
- Each line below corresponds to a process and contains its CPU and memory usage. 



In conclusion, the choice between object, file, memory, and block storage hinges on weighing their respective advantages and disadvantages to best suit the needs of Data Engineering projects:

- Object storage shines with its scalability, cost-effectiveness, and simplicity, making it ideal for scenarios requiring vast amounts of unstructured data such as backups, archives, and content delivery networks.
- File storage offers ease of access and organization, making it suitable for shared file systems and applications reliant on hierarchical data structures like file servers.
- Memory storage, with its lightning-fast access speeds, is indispensable for real-time data processing and caching tasks, particularly in-memory databases and high-performance computing environments.
- Block storage, providing direct access to data blocks, excels in performance-critical applications such as databases, virtualization platforms, and enterprise storage systems.

Data Engineers must carefully assess the specific requirements of their projects to determine the most suitable storage solution, leveraging the unique strengths of each type to optimize performance, scalability, and cost efficiency.


--- 

### Lab notes

**Object Storage**

**Upload file to S3 function**
```Python
def upload_file_to_s3(local_file_path: str, bucket_name: str, s3_key: str) -> None:
    """Uploads a local file to S3 using boto3

    Args:
        local_file_path (str): Local file path
        bucket_name (str): Bucket name
        s3_key (str): Key (Path) in S3 to save the file
    """
    # Create an S3 client
    s3_client = boto3.client('s3')
    
    try:     
        # Upload the file to S3
        s3_client.upload_file(
	        Filename=local_file_path, 
	        Bucket=bucket_name, 
	        Key=s3_key)
           
        print(f"File at {local_file_path} has been uploaded to s3://{bucket_name}/{s3_key} successfully.")
    except Exception as err:
        print(f"Error uploading file to S3: {err}")
```

To check if key exists or not:

```Python
def key_exists_in_s3(bucket_name: str, s3_key: str) -> Dict[Any, Any]:
    """Checks if an specific key exists in an S3 bucket

    Args:        
        bucket_name (str): Bucket name
        s3_key (str): Key (Path) in S3 to save the file
    """
    
    ### START CODE HERE ### (~ 5 lines of code)
    
    # Create an S3 client
    s3_client = boto3.client('s3')
     
    try: 
        
        # Use `head_object()` method passing Bucket name and Key parameters.
        response = s3_client.head_object(Bucket=bucket_name, Key=s3_key)
        print(f"File at s3://{bucket_name}/{s3_key} exists!") 
        
        return response
        
        ### END CODE HERE ###
        
    except botocore.exceptions.ClientError as err:
        print(f"Error message: {err}")
        
        if err.response['Error']['Message'] == 'Not Found':
            print(f"The key {s3_key} does not exist.")                        
            return err.response

s3_key_to_check = 'data/csv/employees.csv'

response = key_exists_in_s3(bucket_name=BUCKET_NAME, s3_key=s3_key_to_check)
```

Check the response and you will see a key named `'VersionId'` with an identifier of the current version of the Object you just queried, alongside other information about the size of the object and the date of modification.

Output of `print(response)`:

```JSON
{'ResponseMetadata': {'RequestId': 'V7FTAJK1XXWHG4DV',
  'HostId': 'qwc0cIFdtYc2K8mpgn6HsvWBY2Dsr2GPb+sNq0dpAONRstELUeeZeCDpgfVJpV7Z6lU4rD4cntA=',
  'HTTPStatusCode': 200,
  'HTTPHeaders': {'x-amz-id-2': 'qwc0cIFdtYc2K8mpgn6HsvWBY2Dsr2GPb+sNq0dpAONRstELUeeZeCDpgfVJpV7Z6lU4rD4cntA=',
   'x-amz-request-id': 'V7FTAJK1XXWHG4DV',
   'date': 'Tue, 23 Dec 2025 15:01:57 GMT',
   'last-modified': 'Tue, 23 Dec 2025 14:58:40 GMT',
   'etag': '"88589ef0daa61336a36d0dadfda5ba18-8"',
   'x-amz-server-side-encryption': 'AES256',
   'x-amz-version-id': 'ZtmZwii5xmJg8W6tigKIlbMmFO1CnHyS',
   'accept-ranges': 'bytes',
   'content-type': 'binary/octet-stream',
   'content-length': '64740125',
   'server': 'AmazonS3'},
  'RetryAttempts': 0},
 'AcceptRanges': 'bytes',
 'LastModified': datetime.datetime(2025, 12, 23, 14, 58, 40, tzinfo=tzutc()),
 'ContentLength': 64740125,
 'ETag': '"88589ef0daa61336a36d0dadfda5ba18-8"',
 'VersionId': 'ZtmZwii5xmJg8W6tigKIlbMmFO1CnHyS',
 'ContentType': 'binary/octet-stream',
 'ServerSideEncryption': 'AES256',
 'Metadata': {}}
```


Listing object versions:

```Python

def list_object_versions(bucket_name: str, s3_key: str) -> None:
    """ List versions of S3 objects that start with a particular key

    Args:                
        bucket_name (str): Bucket name
        s3_key (str): Prefix of the Key (Path) in S3 of the object
    
    """
    
    # Create an S3 client
    s3_client = boto3.client('s3')

    # List object versions
    response = s3_client.list_object_versions(Bucket=bucket_name, Prefix=s3_key)

    # Process the response to get object versions
    for version in response.get('Versions', []):
        print("Object Key:", version['Key'])
        print("Object Version Id:", version['VersionId'])
        print("Is Latest:", version['IsLatest'])
        print("Last Modified:", version['LastModified'])
        print()

list_object_versions(bucket_name=BUCKET_NAME, s3_key=s3_key)
```

Output:
```bash
Object Key: data/csv/employees.csv
Object Version Id: P8YKWp3.POKrUanQOqbkJI.vcebcWeac
Is Latest: True
Last Modified: 2025-12-23 15:17:19+00:00

Object Key: data/csv/employees.csv
Object Version Id: A1HULL2MJNN_r6ltn2lJ_yn4ta3qRM4b
Is Latest: False
Last Modified: 2025-12-23 15:15:28+00:00

Object Key: data/csv/employees.csv
Object Version Id: ZtmZwii5xmJg8W6tigKIlbMmFO1CnHyS
Is Latest: False
Last Modified: 2025-12-23 14:58:40+00:00
```


To read from a specific version:
```Python
def read_csv_version_from_s3(
		bucket_name: str, 
		s3_key: str, 
		version_id: str
	) -> pd.DataFrame:
    """Reads a particular version of a csv file stored in an S3 bucket

    Args:        
        bucket_name (str): Bucket name
        s3_key (str): Key (Path) in S3 of the file
        version_id (str): Object's Version ID
    """
    # Create an S3 client    
    s3_client = boto3.client('s3')

    try:
        

        response = s3_client.get_object(
	        Bucket=bucket_name, 
	        Key=s3_key, 
	        VersionId=version_id)

        
        csv_content = response['Body'].read().decode('utf-8')
        df = pd.read_csv(StringIO(csv_content))
                
        return df
        
    except Exception as err:
        print(f"Error message: {err}") 
```


---

**Memory Storage**

```Python
def get_memory_usage():
    """
    Get total memory and memory usage
    """
    with open('/proc/meminfo', 'r') as mem:
        ret = {}
        tmp = 0
        for i in mem:
            sline = i.split()
            if str(sline[0]) == 'MemTotal:':
                ret['total'] = int(sline[1])
            elif str(sline[0]) in ('MemFree:', 'Buffers:', 'Cached:'):
                tmp += int(sline[1])
        ret['free'] = tmp
        ret['used'] = int(ret['total']) - int(ret['free'])
        ret['percentage'] = int(ret['used'])/int(ret['total'])
    return ret

print(get_memory_usage())
```


