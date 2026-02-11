Training: https://www.youtube.com/watch?v=FNJze2Ea780

# Basics


**What is Apache Spark?**
Earlier we were relying only in one machine, but with big data this is not possible. We now need a "distributed" approach to processing data. That's instead of just using one single machine / processing engine to process our data, we use a group of machines. That group of machines is called a **"cluster"**.

![[Screenshot 2026-02-07 at 19.13.19.png]]

In "spark" language, we call a machine a "node" of that cluster. So, node is just a machine.


**Why do we need Apache Spark?**

There's two approaches to processing data:
- Monolithic Approach: Scaling Vertically (Up) (e.g. upgrading CPU/laptop/RAM/cores)
	- Drawbacks: 
		- There's a limit to that
		- Low availability -- what if the PC fails?
- Distributed Approach: Scaling Horizontally (e.g. adding more machines to the system to process your data)


Definition: **Apache Spark** is a unified computing engine and a set of libraries for parallel data processing on computer clusters.

>	 It's not meant to be a "storage solution". It's great for getting things from one place, transforming them, and sending/saving them to another place.


Whenever you have enough data that you want multiple machines to analyse it, Spark is the way to go. For small datasets, other libraries like pandas can work well.

**How does Apache Spark do things?**

![[Screenshot 2026-02-07 at 19.29.13.png]]

These are some of the main features. Apache performs:
- In-Memory Computation
- Lazy Evaluation --> Making code optimized when running queries
- Fault tolerance
- Partitioning
- Batch & Streaming Processing --> using the same(!) API.


The Spark ecosystem is really rich:
 - Spark SQL
 - Streaming Processing
 - MLlib library for machine learning tasks
 - GraphX
 - SparkR (if you like R)

You can find Apache spark in many places:
- Databricks
- Google Cloud Dataproc
- IBM Watson Studio
- Amazon EMR
- Microsoft Azure

## Apache Spark VS Hadoop MapReduce

MapReduce was doing the same thing as Apache, distributing data in different machines and processing data. 

The difference between them is that MapReduce is writing all intermediate transformations to disk, while Apache writes all these transformations in memory. This made Apache Spark as much as 100x faster than MapReduce (in some cases).



# Apache Spark Architecture

Apache Spark architecture is built on top of a concept called "Leaders - Followers" (Master-Slave architecture). In this architecture the will be one Leader and multiple Followers.

To go deeper, we need to understand some technical parts of the Apache architecture: 
- Resource Manager
- Driver
- Workers

![[Screenshot 2026-02-07 at 19.36.48.png]]

#### Resource Manager

***"Resource Manager"*** is our "Leader" (Master). It's called a Resource Manager because it manages (allocates) our resources.

We have resources in the form of:
- ***"Driver"***
- ***"Workers"***

Our ***"cluster"*** is made of one ***Driver*** and multiple ***Workers***

![[Screenshot 2026-02-07 at 19.39.33.png]]


#### Driver

Our Driver is the "team/tech lead" that **"orchestrates"** the work of the cluster.

So, manager gets the work from the client --> Manager allocate works to team leads --> team leads allocate the work to their workers.

**Why don't we get directions directly from the Resource Manager?**
Simply because the Resource manager does not understand the "technical terms" of the work to be done. This is the job of the Driver, our team lead.


## Architecture flow (high-level concept)

Let's say we have a developer who's writing a **Spark Code** for transforming some data. This code is submitted to the Resource Manager.

![[Screenshot 2026-02-07 at 19.47.32.png]]

In this case, that developer is the "client" of the resource manager.

As in any client <-> business relationship, the client needs to provide some additional "task" information based on their budget. That is:
- How many workers are they requesting?
- Should these workers be senior (get the job done fast) or can they also be mid-level/junior? (node processing power)
- etc..
- Should we put more than one team to work on this task? (how many clusters)

This client command that the client is initiating and explains the task along with the resources needed is called ***"spark submit"***.

![[Screenshot 2026-02-07 at 19.53.11.png]]


The resource manager gets the spark submit command and the first thing it does is it creates the **"driver node"**.

That driver node will connect to the resource manager.
![[Screenshot 2026-02-08 at 10.19.46.png]]

It will then take the manager's task and read it (e.g. you need 3 executors, etc..). Once it reads the manager's sheet, it sends a response back to the resource manager requesting the resources (e.g. I need you to assign me three executors to do the job).

The resource manager then creates the three executors.

![[Screenshot 2026-02-08 at 10.21.41.png]]

Once this is done, the resource manager is free, leaving the job to the driver node and its workers.
![[Screenshot 2026-02-08 at 10.22.57.png]]

As mentioned, the Driver Node already has the sheet (code, directions) from the Resource Manager. It will now distribute that workload (e.g. JOINs, GROUP BYs, etc) to the worker nodes to actually do the job.
![[Screenshot 2026-02-08 at 10.24.18.png]]

The team lead (driver node) is basically there just for distributing the workload to the workers, and then monitoring how the workers are doing.

#### Leader-Follower changes
We mentioned earlier that Spark follows a (Master-Slave) Leader-Follower architecture. Who's the leader here? A controversial thing with Spark is that the Leader keeps changing.

When Resource Manager was in the picture, that one was giving the commands (Leader) so all the other nodes (driver, workers) were the followers.

Now that Resource Manager is not in the picture, Driver is Leading and the workers are following.

The architecture in the official Apache Spark site is this:

![[Screenshot 2026-02-08 at 10.31.58.png]]

Let's reiterate now so that we can understand the above architecture.

The client (a developer) submits the code into the Cluster Manager.
![[Screenshot 2026-02-08 at 10.34.09.png]]

The resource manager creates the Driver Program
![[Screenshot 2026-02-08 at 10.35.02.png]]

The Driver Program gets the "sheet" from the Cluster Manager and then creates the worker nodes, as per client's instructions.
![[Screenshot 2026-02-08 at 10.36.19.png]]

#### Worker / Executor distinction
Now it's a good time to explain the Worker/Executor distinction.

- A **Worker Node** is just a machine. 
- On that node we create the **executors** which execute the work on that worker machine.

We can host multiple executors on one machine, but nowadays, in all modern applications, each worker only hosts one executor.

Now, these worker nodes have established a 2-way communication with the Driver program. So, whatever task the driver program assignes to its workers, they'll do it and then report back to the Driver node.

![[Screenshot 2026-02-08 at 10.58.12.png]]

Official Apache documentation for Apache Architecture:
https://spark.apache.org/docs/latest/cluster-overview.html


## Cluster Managers

There's not only one cluster manager for Apache. There are several types of cluster managers, native & 3rd-party, that you can use in a Spark Application. Some of them are:
- Spark's own **standalone** cluster manager
- **Apache Mesos** (Deprecated)
- **Hadoop YARN** (the most popular cluster manager - the resource manager in Hadoop 3)
- **Kubernetes** (an open-source system for automating deployment, scaling, and management of containerized applications)

### Spark Session (previously SparkContext)

SparkSession has an embedded SparkContext:
![[Screenshot 2026-02-08 at 11.09.11.png]]

Spark offers three types of Contexts as of today:
- SparkContext
- HiveContext 
- one more..

Now they have condensed all these above contexts into one ContextSession.

>	SparkContext (or SparkSession) is the starting point of Spark.

The Driver Program with the Cluster Manager are basically connected (and communicate) via this SparkContext.

![[Screenshot 2026-02-08 at 11.13.27.png]]

>	Basically, the client creates this sparkContext (entry point) in their code, and when that code is submitted to the Cluster Manager, the Cluster manager creates the Driver Program and shares client code with the Driver Program, so now the Driver Program runs this Spark Context as created by the client.

As per the official documentation:

Spark applications run as independent sets of processes on a cluster, coordinated by the `SparkContext` object in your main program (called the ***Driver Program***).


## Driver Node

Let's talk about the Driver Node in detail.

What does actually happen when the resource manager selects one machine to be treated as a "Driver Node"?

Our resource manager (cluster manager) installs a container in the Node  that was chosen to be the Driver Node, called an ***"Application Master Container"***.

![[Screenshot 2026-02-08 at 11.24.33.png]]

Application Master Container can be abbreviated as "AMC".
![[Screenshot 2026-02-08 at 11.25.41.png]]


This is a container that's responsible for all the Driver-related activities.

So what do we have in this Application Master Container?

1. PYSPARK MAIN
2. JVM MAIN
3. Py4J Process

Let's understand what these are.

Although we'll be writing most of our code in Python using the Python API of Spark called PySpark, Spark was actually developed in a language similar to Java, called "Scala".

PYSPARK MAIN is added within the AMC and acts as a Wrapper that can translate Scala into Python. Obviously, PYSPARK MAIN is only required if you want to write code in Python. There are also other wrappers for other languages.

>	The actual flow is:
>	Spark (written in Scala) has a wrapper for Java, and then Java has a wrapper for Python (or any other language we choose to write to).

So, whatever code we write in Python, will be converted to JVM main (Java Virtual Machine) by a process called ***Py4J Process***

The PYSPARK MAIN process is called a ***"PySpark Driver"*** and the JVM MAIN process is called ***"Application Driver"***.

![[Screenshot 2026-02-08 at 11.41.06.png]]

These are technical terms.


## Worker Node

Now, let's uderstand the Worker Node. Worker node is very simple. A worker node only has a JVM machine. 

![[Screenshot 2026-02-08 at 11.42.12.png]]

That's because these are the executors who execute the work. All that's needed to be done is for the worker node to have a JVM installed. All the work can be translated into JVM by the Driver node and sent to the worker nodes.

But we actually need python in the worker nodes too.

![[Screenshot 2026-02-08 at 11.43.33.png]]

That's there for a special case. Clients (users) can create UDF's, or, User Defined Functions, which are functions that are not Spark Functions.

![[Screenshot 2026-02-08 at 11.46.00.png]]

Why would we need to create UDFs? Although Spark has a multitude of functions for different cases, this doesn't mean that it covers everything. Sometimes, for some edge cases or specific needs, a user needs to create their own functions that workers need to understand and execute.

Only in these cases will the Worker node also install the Python service, so that it can interpret the function we have manually created.

We obviously should avoid creating UDFs when possible, because this adds additional load into the worker nodes cause they need to install Python as well.

>	 NOTE: The JVM's are slowly being replaced by C++ executors because C++ can execute code natively rather than on a Virtual Machine, which will make Spark workers even faster.



# Code Implementations on DataBricks

## Free DataBricks Account

We need a Free - community account. 

Databricks helps you to work with Apache Spark. It takes care of a lot of overhead. 

With databricks you don't have to create your own cluster locally. Databricks sets up the Spark cluster, driver, worker nodes etc. So that's why we are using Databricks to run our spark code.


![[Screenshot 2026-02-11 at 17.13.00.png]]

On the left pane you can see multiple categories. We won't deal with all of them - we just need to use databricks for running our Spark Application.

### Catalogs

Catalogs is where we manage all of our schemas/tables/etc..

If we click on "catalogs" we see "workspace" which is our one and only catalog (that's what we get in the free edition).

![[Screenshot 2026-02-11 at 19.30.10.png]]

There's already two schemas in our workspace catalog, but we can create our own schema (top right button).

Within the new schema, you can create tables, views, but you can also create "volumes". What are volumes? 

Databricks allows you to work with files directly and you can govern those files using URLs and all through your catalog. 

Let's say we create a volume called "streaming":

![[Screenshot 2026-02-11 at 19.36.26.png]]

In this case, the volume name is the parent name (`/Volumes/workspace/stream/streaming`).
Within this one we can create subfolders.

![[Screenshot 2026-02-11 at 19.37.57.png]]


### Workspace

In the "Workspace" menu, it's where we go to create our workspace.
Within the "workspaces" folder, create a folder called `ApacheSparkGuide` and within it create a notebook. Let's name that notebook `SparkSession` .

#### Connecting notebook to a cluster
When you create a notebook, you need to attach it to a cluster.

To understand this further, let's take an example. When writing a code to this notebook, where will this go?

It will go to the Resource Manager, and from there the Resource Manager will create the Driver Node which will be connected to the Resource Manager, etc.. The best thing with databricks is we don't need to say "Hey, create one driver, two executors, etc..". We can simply create a "***cluster***" and then reuse it.

This can be done in the "Compute" pane.

#### Compute

The compute pane is where we can create our clusters (in the Free Edition you can't really do that, we'll be using serverless).

But let's say we could do it. You would go to Compute pane, and from there:
![[Screenshot 2026-02-11 at 18.26.54.png]]
![[Screenshot 2026-02-11 at 18.27.30.png]]
	These are the cluster versions

![[Screenshot 2026-02-11 at 18.28.20.png]]


When you click on `create compute`, databricks will handle the creation of the cluster for us. We'll also be able to re use it as it's saved.

![[Screenshot 2026-02-11 at 18.26.05.png]]


To use this cluster, go to the newly-created notebook. From there, choose the cluster you created on the top dropdown (in the Free tier only a "serverless" option is available)

![[Screenshot 2026-02-11 at 18.30.30.png]]


So what will that cluster hold? In real life (when paying for databricks) you can define driver size, number of workers, and worker size. You are saving this cluster information and then you can reuse.

In the free account you can't create 100 machines and 100GB ram and so on. It only uses 1 simple serverless option.

If we had created a cluster, we could select it here:

![[Screenshot 2026-02-11 at 18.33.22.png]]


## Coding on DataBricks

## SparkSession
We previously talked about how we need to create a sparkSession to initiate our Spark App. In DataBricks you don't need to create a sparkSession. Databricks does this for us.

If we were to create one ourselves (we will see that later in practice) we would need to do something like this:

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("tutorial")
```

In the data engineering industry, the variable `spark` is a standard for creating your SparkSession. Databricks found this as an opportunity to create this for us. So, we can actually use `spark` without initiating it.

e.g.
```Python
print(spark)
```

Output:
```shell
<pyspark.sql.connect.session.SparkSession object at 0xffefcb915d60>
```


Let's see what kind of information we have inside this `spark` object.


## Lazy Evaluation & Action
![[Screenshot 2026-02-11 at 18.53.58.png]]

Spark is "lazily evaluated".

Let's say we do 3 transformations (filter, adding, aggregating). And we then execute the cells one by one. Nothing will happen. Why?

Because f the concept of "Lazy Evaluation".

- When running transformations, Spark will save all of these transformations in a "Plan". 
- It will only execute this plan when we click on the "action" switch.

Moreover, we don't want our code to run straight away. We first need to evaluate our code in terms of how optimized it is.

### plan

When we hit "action", Spark will re-arrange our transformations in a better, more optimized way.

![[Screenshot 2026-02-11 at 18.57.58.png]]

### actions

So where are these actions? Some actions are:

```Python

.show()
.count()
.display()
.collect()
```

When we perform these actions to a spark dataframe, it will simply execute Spark's revised plan, by creating a so called "JOB".

### spark job

The spark job created by the "action", holds all of our transformations.


## Code Example

```Python
data = [
    ("Alice", 25, "New York"),
    ("Bob", 30, "Los Angeles"),
    ("Charlie", 35, "Chicago"),
    ("David", 40, "Houston"),
    ("Eve", 45, "Phoenix")
]

schema = StructType([
    StructField("name", StringType(), True),
    StructField("age", IntegerType(), True),
    StructField("city", StringType(), True)
])

df = spark.createDataFrame(data, schema=schema)
```

