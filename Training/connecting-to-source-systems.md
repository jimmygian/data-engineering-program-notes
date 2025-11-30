
Before you can ingest data, you need to first **establish a connection** to your **data source** and **verify that you're authorized to read data from it**. 

You've already had some experience doing this in previous labs. 

For instance, in the **DynamoDB** lab, you used **Boto3**, which is the AWS software development kit or SDK for Python to **create a client connection** to a table within DynamoDB. 

```python
def create_client():
    try:
	    client = boto3.client("dynamodb")
	    return client   
    except ClientError as e:
        error = e.response.get("Error", {})
        logging.error(
            f"Failed to load DynamoDB. Error: {error.get('Message')}"
        )
        response = {}
    return response
```

And you also connected to an **Amazon RDS MySQL instance** by running this command with proper parameter values. 

```Shell
mysql --host=<MySQLEndpoint> --user=<DatabaseUserName> --password=<Password> --port=3306
```

- The *endpoint* and *port* information you see here are what you use to locate the correct database instance. 
- And the *username* and *password* credentials were used to authenticate you as someone with the proper permissions to access the database. 

And so, as you can see, there's more than one way to connect to a database. Or any resource for that matter. So let's take a closer look at this. 


**Using the AWS Management Console**
If a source system is housed in a resource within your organization's AWS account, you can get the connection information from the management console. 

For example, if I'm trying to connect to an **RDS database instance**, I can navigate to the RDS console, locate the database I want to connect to, and find the connection information, including the endpoint and the port number. 

>	*And just as a side note, AWS is always rearranging exactly how things appear in the console. So what I'm showing here might look a little different than the console when you're looking at it.* But this basic set of steps will still be the same. 

And so the console can be pretty convenient for finding information like this or spinning up resources and connections. But keep in mind that doing your work from the console involves you navigating through and clicking on widgets and buttons to get things done. 

If you had to repeat this process in the future, it could be hard to remember exactly what steps you took. And like I said, by the time you want to do this again, AWS may have changed how things are arranged in the console, which could make things even harder. So in general, operating from the console is great to get something done quickly, maybe when you're prototyping something in your system. **But the process is not very repeatable nor traceable.** 


**Using CLI tools**
As a somewhat more programmatic way of finding the information you need and connecting to source systems, you can run code at the command line interface or CLI. 

In this way, 
- you can get the database endpoint, 
- then you can connect to the database using the command syntax specific to the DBMS you're using. 

And so issuing commands directly in the CLI like this is a common practice among data engineers in the connection and ingestion process, but it's still relatively *manual*. So it's typically better for simple workloads rather than complicated ones. 


**SDKs**
To take another step towards **repeatability and automation**, you can connect to a source system using an **SDK** like **boto3**, running code in an IDE or, for example, from a **Jupyter** notebook. 


**API Connectors**
For certain source systems, you can also connect to them through an API connector. For example, you might use 
- a Java Database Connectivity, or JDBC for short, or 
- Open Database Connectivity, or ODBC for short.


# AWS

## Connecting to an Amazon RDS MySQL Database

You can create your own **Amazon RDS database** instance in your personal account through the AWS management console as explained [here](https://aws.amazon.com/getting-started/hands-on/create-mysql-db/). In the upcoming weeks, you will learn how to create an RDS database programmatically using Terraform.