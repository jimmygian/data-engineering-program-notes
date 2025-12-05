
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

_**How to connect to an RDS instance?**_

After you create your own public instance of an Amazon RDS database, you need to establish a connection to the database server to query it. In this example, the Amazon RDS database we are connecting to is MySQL, and the following information is needed:

1. Database hostname/endpoint (address or location of the database server)
2. Database port (the network port the MySQL server is running on)
3. Database username & password (credentials to log in to the database)

You can get the hostname/endpoint and port from the AWS management console or programmatically from the command line interface (CLI) after you create the table. You would have created the database username & password when setting up the database instance. Otherwise, ask the database administrator for this information.

_**Connecting to the database through AWS CloudShell**_

AWS CloudShell is a browser-based shell providing command-line access to your AWS resources in the selected region. You can access CloudShell from the console as follows.

![[Pasted image 20251130102928.png]]
![[Pasted image 20251130102941.png]]

To connect to MySQL database through the console, you can use the following command ([link to documentation](https://dev.mysql.com/doc/refman/8.0/en/connecting.html))

```shell
mysql --host=[hostname] \
 --port=[port number] \
 --user=[database user name] \
 --password=[database user password]
```

This command is specific to MySQL. Each DBMS specifies how to connect to its database through the command line. For example, here's [the documentation](https://www.postgresql.org/docs/9.1/app-psql.html) for connecting to a PostgreSQL database.

The endpoint and port number were manually entered, but you could get them programmatically using this [_describe-db-instances_](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command:

```shell
aws rds describe-db-instances --filters "Name=engine,Values=mysql" --query "*[].[DBInstanceIdentifier,Endpoint.Address,Endpoint.Port,MasterUsername]"
```

After you establish the connection, you can send your queries to the database using SQL.

_**Connecting to the database through Python**_

To connect to MySQL database using Python, first install [pymsql](https://pymssql.readthedocs.io/en/stable/). pymsql allows you to establish a connection to a MySQL database in your Python code using the .connect() method.

You need to specify the following pieces of information:

- Database hostname/endpoint (address/location of the database server)
- Database port 
- Database username & password (credentials to connect to the database)
- Database name

You can get the port and endpoint programmatically in Python using the AWS software development kit (SDK) [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html). It allows you to connect to your provisioned resources and extract the necessary parameters.
![[Pasted image 20251130103509.png]]
The second cell in the image above shows how to set the access and secret access keys. These keys are generated for any AWS IAM user as long-term credentials to programmatically connect to AWS resources. However, it is not best practice to include these credentials in your code. For best practice, you should save these keys in a config file. Check [here](https://wellarchitectedlabs.com/common/documentation/aws_credentials/) for other options.

To get the parameters in 'dbInstance', you can run 'dbInstance'. The output is shown below.
![[Pasted image 20251130103550.png]]

Once you have the necessary parameters, you can establish a connection to the database using pymysql.connect(), and then you can perform your SQL request.
![[Pasted image 20251130103612.png]]

**Final Remark**

If you created an RDS database instance for practice, please remove it when you are done.

From the RDS service page, click on "Databases" in the left-hand menu, select the database you just created, and delete it (you can find the delete option in the "Action" drop-down menu or by right-clicking on the database).

