Side-Course for Training: https://www.youtube.com/watch?v=FNJze2Ea780

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
from pyspark.sql import SparkSession
from pyspark.sql.functions import *
from pyspark.sql.types import *
```

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

Now, let's say I want to filter this data

```Python
## Applying a filter

df = df.filter(
    col('city') == 'New York'
)
```

Notice that when you run the cell, nothing really happens (as we said above - due to the Lazy Evaluation. It's simply stored in the "plan"). To run the above filter, do the action "display":

```Python
# Action
display(df)
```

Output:
![[Screenshot 2026-02-13 at 17.18.20.png]]

This created a 3 spark jobs and then run them in spark's preferred order. We'll dive deeper into jobs later in the course.


## Query plans and DAGs

Let's say we now run two transformations:

```Python
df_new = df_new.filter(
    col('city') == 'New York'
)
```

```Python
df_new = df_new.select('city')
```

Although we are running two separate transformations, spark cleverly joins these two. To know what Spark is doing, we can run:

```Python
df_new.explain()
```

Output:
```
== Physical Plan ==
*(1) Project [city#23]
+- *(1) Filter (isnotnull(city#23) AND (city#23 = New York))
	+- *(1) Scan ExistingRDD[name#21, age#22, city#23]
```

We should read this output "Bottom to Top".

- It added "isnotnull" on its own for better optimization. 
- It also performed AND operator to connect our two transformations into one.

Whenever we run our transformations, Spark creates a DAG for us (Directed Acyclic Graph). A DAG is basically the flow of the job.


# Partitions

What are "partitions" in spark?


Let's say we have this data (dataframe):
![[Screenshot 2026-02-13 at 17.40.58.png]]

Let's say this dataframe contains 1 million of this kinds of records.

A partition means that our data is being distributed. How is our data partitioned though?
Spark is partitioning our data in order to be distributed amond the executors.

![[Screenshot 2026-02-13 at 17.43.03.png]]

But how can we partition our data? We can't partition our data, we can only create "logical partitions".

## RDD
In order to understand Logical Partitions we need to understand another topic: RDD.

RDD is the backbone of Apache Spark. It stands for "Resillient Distributed Dataset".
RDD is one of the datatypes we have in Apache Spark. It's a kind of list - a list of logical partitions of our data. The specialty of this list is that it can be distributed to our executors ( worker machines).

![[Screenshot 2026-02-13 at 17.47.23.png]]

So, basically, RDD is the collection of logical partitions of our dataframe.

Why is it  called "resilliant"? Let's take the previous example, in which we did two transformations to a single dataframe. 


```Python
df_new = df_new.filter(
    col('city') == 'New York'
)
```

```Python
df_new = df_new.select('city')
```

Although we are changing the same variable, behind the scenes, this creates two separate RDDs.
- RDD1
- RDD2

In case RDD1 fails, it will read the DAG it created for RDD1 and then try and rerun RDD2.
That's what's makes it resillient.


# Transformations

We have different types of transformations in Spark. These are:
- Narrow transformations
- Wide transformations

![[Screenshot 2026-02-13 at 17.56.38.png]]

## Narrow Transformations

- filter
- select

Let's say, this is your dataframe's partitions:

![[Screenshot 2026-02-13 at 17.58.08.png]]

If we want to apply a `filter` transformation on just the first one, and then just on the second one, we don't need any "reference" information from one or the other.

To understand this better. Let's imagine our data is:

"name", "age", "occupation", "city" of people.

If partition 1 holds half the the data (let's say nemes starting from A to P) and partition 2 holds the other half, and the "filter" we apply is "find all people who's age is 20", partition 1 doesn't need to know what partition 2 holds and vice versa. Each partition will output their own result.

That's a "narrow" transformation.

![[Screenshot 2026-02-13 at 18.34.25.png]]




## Wide Transformations

Examples is "GroupBy"

Let's take the previous example. Now, we want to aggregate data based on the "city". For example, count number of cities where bla bla..

Now, we can't apply this transformation on each partition seperately, because we need the "city"
from all partitions (in our case both partitions 1 and 2).

Spark will understand the need to "shuffle" the data from the partitions in a way that makes sense for this query - in this case, it will shuffle it based on the city, so, each partition will have the data that corresponds to a specific city.

![[Screenshot 2026-02-13 at 18.42.51.png]]

By default, Spark creates 200 partitions in cases of WIDE transformations (we'll explore that later on).

As we said, it applied hash aggregation to our data.
![[Screenshot 2026-02-13 at 19.08.54.png]]

When we see "exchange" means the data has been shuffled.
![[Screenshot 2026-02-13 at 19.09.24.png]]


# Repartition VS Coalesce

This is another concept we need to understand, in order to deeply understand how jobs are run in Spark.

## Repartition

Let's say we have a table that initially only has two partitions


![[Screenshot 2026-02-13 at 19.15.23.png]]

These partitions were created automatically and by default they are 128Mb each.

>	NOTE: 128Mb is the default partition and block size.

If our total data is 200Mb, we are going to end up with 2 partitions
- 1st partition: 128Mb
- 2nd partition: 72Mb

For some reason we want to increase the number of partitions and create 10 partitions. To do so, we can create something called "repartition". 

When we perform repartition, data obviously needs re-shuffling.

![[Screenshot 2026-02-13 at 19.20.11.png]]


We can also use `repartition` for DEcreasing the number of partitions, but that's not recommended.


## Coalesce

If we want to decrease the number of partitions, we use something called `coalesce`

In coalesce, it usually does not perform shuffling. Sometime's it does though.



![[Screenshot 2026-02-13 at 19.22.39.png]]

If we have 1 executor and two partitions, when performin `coalesce` , it will simply pull the two partitions and perform this process.

But if we have 2 executors, each partition will handle one partiiton.
![[Screenshot 2026-02-13 at 19.24.13.png]]

In this scenario, it needs to shuffle the data so that coalesce succeeds
![[Screenshot 2026-02-13 at 19.24.52.png]]


```Python
df_2.rdd.getNumPartitions()

df = df.repartition(3)
df = df.coalesce(1)
```


# Reading  data

To read Spark data, we need to use the `spark.read` api.

```Python 

df = spark.read.format('csv')\
			.option("header", True)\
			.option("inferSchema", True)\
			.load("path/to/file.csv")
```

- `option`: With options, we can specify the configurations that we want.
	- E.g. we know that our CSV has a header.
	- We also need to define our schema, but in many cases we can infer it using the option `inferSchema`. This will predict the best schema for our dataframe.
- `load`: This loads the file.

When you run this cell, jobs are created. Why? 
- When we create a df without a file it is not an action
- When we read from file, using `.read()` method, it is.
![[Screenshot 2026-02-13 at 20.08.06.png]]

Why are we seeing two spark jobs though?

```Python
df = spark.read.format('csv')\            # Action-1
			.option("header", True)\
			.option("inferSchema", True)\ # Action-2
			.load("path/to/file.csv")
```

InferSchema checks the record files and then predicts the schema. Because it actually looks at your data, it's an extra action.



# Jobs, Stages, and Tasks

When we run a Spark action it creates multiple Jobs, and each of these jobs contains Stages and each stage contains tasks. Let's dive into this and understand what's happening.

![[Screenshot 2026-02-13 at 19.41.33.png]]

- Whenever we hit "action", a job is created for us.
- This job contains multiple stages.
- Each stage contains multiple tasks.

### Stages

Who is deciding these stages?
Stages are the individual transformations of your job.

Let's say, we apply `filter`, `select`,  and `groupBy` to a spark DataFrame.

The job will determine which transformations are similar to each other and bucket those together. In our example, filter and select will be STAGE 1, and groupBy will be STAGE 2.


### Tasks

Within each stage we can have multiple tasks. 

> Tasks are associated with the number of partitions.

So, for example, `groupBy` creates 200 partitions. This means we'll have 200 tasks.


## Example

When you run this cell, jobs are created. Why? 


```Python
df = spark.read.format('csv')\            # Action-1
			.option("header", True)\
			.option("inferSchema", True)\ # Action-2
			.load("path/to/file.csv")
```


- When we create a df without a file it is not an action
- When we read from file, using `.read()` method, it is.

![[Screenshot 2026-02-13 at 20.08.06.png]]

Why are we seeing two spark jobs though?

```Python
df = spark.read.format('csv')\            # Action-1
			.option("header", True)\
			.option("inferSchema", True)\ # Action-2
			.load("path/to/file.csv")
```

InferSchema checks the record files and then predicts the schema. Because it actually looks at your data, it's an extra action.

Now, why are we seeing stages? Stages should be for transformations but we didn't apply any transformation when reading the data.

![[Screenshot 2026-02-13 at 20.11.14.png]]

This is because:

>	1 Job = (AT LEAST) 1 Stage + 1 Task 


Now, let's perform some transformations..

![[Screenshot 2026-02-13 at 20.36.48.png]]

Stage20 is our two narrow transformations, then Stage 21 is our  Wide transformation.

The exchange means that partitions are being shuffled. 
- Because we change from narrow to wide transformation, stage 1 needs to write this data to the next stage. This type of exchange is called "Write Exchange"
- Stage21 is reading this exchange (that's why it's on the top). This type of exchange is called "Read Exchange".

![[Screenshot 2026-02-13 at 20.31.21.png]]


Because by default, groupBy creates 200 partitions (and we have disabled AQE), we have 200 jobs. But if you count the skipped ones, only 1 is being run, with the other 199 skipped.
![[Screenshot 2026-02-13 at 20.37.56.png]]


# JOINS in Spark

Joins are a fundamental operation in Spark, enabling you to combine data from multiple DataFrames based on a common key. Understanding how Spark handles joins, especially at scale, is crucial for writing efficient and correct data processing pipelines.

---
## Partitioning and Executors

Suppose you have a dataset of **450MB**. In many Spark SQL/file-read scenarios, Spark will create partitions around **128MB** each by default (this is configurable, and the actual number depends on file layout and settings such as `spark.sql.files.maxPartitionBytes`). So **450MB might become ~4 partitions** based on the default configuration. For our example, we'll suppose that's the case.


![[Screenshot 2026-02-14 at 12.44.35.png]]

Let's say we have 2 executors.  And let's suppose that:
- The first one will take partitions 1 and 2
- The second one will take partitions 3 and 4
(that's oversimplified for teaching purposes)

Let's also suppose that each executor has 4 cores. Cores are responsible for paraller processing. Each core can perform a task, so 4 cores can perform 4 parallel tasks.

---
### Joining Two DataFrames

Now, imagine you want to join your original DataFrame (`df_1`) with a new DataFrame (`df_2`) of 460MB, which is also split into 4 partitions.

![[Screenshot 2026-02-14 at 14.57.35.png]]

When you perform a `JOIN` operation, Spark treats this as a **wide transformation** if it requires data movement (i.e., a shuffle). In Spark SQL/DataFrames, shuffle-based joins typically use a configured number of shuffle partitions: 

- `spark.sql.shuffle.partitions` (default is often `200`, but configurable). 

In our case we'll suppose that the shuffling process will create 200 partitions. So, for a shuffle-based join, Spark will repartition/shuffle data into ~200 shuffle partitions (by default), even if you started with only 4 + 4 input partitions.

![[Screenshot 2026-02-14 at 14.59.59.png]]

> 	NOTE: As we'll see later on, Note: Spark may also avoid shuffling entirely for some joins (e.g., broadcast joins), depending on sizes and settings.

---
### How Spark Handles Joins

Joins in Spark are performed on a common key (e.g., `item_id`). However, the data for a given key may be spread across different executors and partitions. For example:

- `df_1`'s data with `item_id = 5` might be in executor 1.
- `df_2`'s data with `item_id = 5` might be in executor 2.

![[Screenshot 2026-02-14 at 15.03.23.png]]

To correctly join these rows, Spark must **repartition** the data so that all rows with the same key end up in the same partition. This process is called **shuffling**.

---
### The Shuffling Process

Spark determines the new partition for each row using a **hash-based partitioning** approach (conceptually):

```
partition_number = positiveHash(join_key) % num_shuffle_partitions
```
- For string keys (and generally all key types), Spark hashes the key to produce an integer-like value, then applies modulo.
- For multi-column join keys, Spark hashes the **combination** of those columns.


After shuffling, all rows with the same join key are grouped together in the same partition. This reorganized data is referred to as the **Shuffled State**.

![[Screenshot 2026-02-14 at 15.23.11.png]]
![[Screenshot 2026-02-14 at 15.24.50.png]]

So, long story short: 

**"SAME JOINING KEY ON THE SAME PARTITION"** (for shuffle-based joins)

The post-shuffle layout is commonly referred to as "**shuffle output" / "post-shuffle partitions**" (but we can also call it “Shuffled State” in your notes).

![[Screenshot 2026-02-14 at 15.46.59.png]]

---
### The Shuffled State  (post-shuffle partitions)

The **Shuffled State** is the result of Spark's shuffling process:

- Data is repartitioned based on the join key.
- All rows with the same key are now together, ready for the join operation.
- This state is crucial for ensuring correctness and efficiency in joins.

---
### Sorting Before the Join

After shuffling, the data is ***not necessarily*** sorted.
- Spark will sort within each partition **only for join strategies that require sorting**, most notably **Sort-Merge Join**.

Let's focus on Partition-1 as an example:

![[Screenshot 2026-02-14 at 15.53.28.png]]

Before the actual join, Spark sorts the data to optimize the join process. There are two main join strategies:

#### 1. Shuffle Sort Merge Join

- Spark shuffles both DataFrames (if needed) so matching keys land in the same partition count.
- Spark sorts each DataFrame within each partition by the join key(s).
- Sorting enables efficient merging of rows with the same key.
![[Screenshot 2026-02-14 at 15.55.00.png]]
#### 2. Shuffle Hash Join

- Spark shuffles both sides by the join key (so matching keys are in the same partition).
- Spark builds a hash table for the **smaller side per partition**, then probes it with the larger side.
- This avoids sorting but requires the ***per-partition hash*** table to fit in memory.

![[Screenshot 2026-02-14 at 16.15.03.png]]

> NOTE: When creating hash tables, we should make sure that the table is small enough to fit into memory.
> 
> NOTE 2: The hash table will be created for EACH partition.


## Broadcast JOINs

df_pink = 540MB

![[Screenshot 2026-02-14 at 17.18.31.png]]

df_green = 5 MB
![[Screenshot 2026-02-14 at 17.18.51.png]]


The whole df_green table fits in one executor / partition.

In this case, shuffling (which is an expensive transformation) doesn't need to happen. What's going to happen is that the whole "yellow" table is going to be broardcasted into all other partitions instead (which is way less computationally expensive).

![[Screenshot 2026-02-14 at 17.21.23.png]]


But how does this broadcasting happens?

The "Driver" node will take the broadcasted table temporariliy, then broadcast it to all the executors so that they can easily apply a JOIN. That's why the table should be small enough so that this can happen.

![[Screenshot 2026-02-14 at 17.22.01.png]]

## Code Implementation

#### Suffle Sort Merge Join (the default)
```Python
df_join = df1.join(
  df2, 
  df1['id']==df2['id'],
  'left'
  )
```

In most cases, that's the best option that spark also chooses by default.

#### Broadcast Join
```Python
df_join = df1.join(
  broadcast(df2), 
  df1['id']==df2['id'],
  'left'
  )
```


# Spark SQL Engine

![[Screenshot 2026-02-14 at 17.52.11.png]]

We need to understand the entire flow, and how Spark optimizes the plan and sends that to the executors.

Let's say we are writing our code as a Data engineer. The totality of our code transformations is called the "Unresolved Logical Plan".

In Spark we have something called "Catalog". Catalog contains all the objects registered:
- Catalog names
- Database names
- Table names
- Column names

Whenever we are reading data, it will create an **"Unresolved Logical Plan",** and it will make sure that whatever metadata we are using will be verified. It basically performs a small analysis on top of our unresolved logical plan. 

If in our code we have an error, the analysis will catch it and throw us an **"analysis exception"**.

Once all columns are verified/approved, it will then proceed in created in the Resolved Logical Plan. This is basically the plan that's verified. This plan could be run but it's not yet optimized.

Last step is to create the **"Optimized Logical Plan"**. This is the plan that will be used (and it's the one we see when we `explain()` an action.)


So what happens when the Optimized Logical Plan is created?

![[Screenshot 2026-02-14 at 18.02.24.png]]

The Optimized Logical Plan will be converted to Physical Plans. To understand the difference, we can think of:
- Optimized Logical Plan says "Perform a JOIN"
- Physical Plan translates that to "Perform a specific type of JOIN (e.g. broadcast)"


### Cost Model

There are many physical plans created out of the Optimized Logical Plan. The cost model will calculate the cost for each one, and pick the least expensive one.

This model outputs the "best physical plan". That will be the plan that will be sent to the executors.


# Driver Memory Management

- The Driver in Spark is the master process that coordinates all tasks
- It holds metadata, task scheduling infol DAGs, and more.
- Driver Memory => Memory allocated to the Driver process when the spark job runs
- If Driver runs out of Memory ==> job fils with `OutOfMemorryError`.

Driver is the "heart" of the whole spark framework. 

Driver memory has two main components:
- JVM HEAP MEMORY
- OVERHEAD MEMORY

![[Screenshot 2026-02-14 at 18.11.01.png]]

### JVM HEAP Memory

That's for JVM tasks, and stores things like: DAG, Metadata, Broadcast Variables, Task scheduling info.


### Overhead Memory

It's responsible for all non-JVM tasks.


Whenever we want to request driver's memory, we are saying that we need `spark.driver.memory`. We will also get 10% extra of Overhead memory.

![[Screenshot 2026-02-14 at 18.14.06.png]]

So, in total will be getting 10% more than what we are requesting OR 384MB (whatever is higher).

![[Screenshot 2026-02-14 at 18.15.53.png]]


## Driver Out-Of-Memory (OOM) Error

Driver's heap memory will broadcast the broadcast variables/data.

If we're broadcasting data that's more than the heap memory, this will create a DriverOutOfMemory (OOM) error.

The other time this can happen is when we run command such as `df.collect()`

When we do `df.show()` or `display(df)`, the data is sent to the driver. The way is done, is one partition is sent back to the driver to give the command.

![[Screenshot 2026-02-15 at 09.45.07.png]]

But with `df.collect()` you're requesting all data collected to a list. In this scenario it will collect all the partitions to the driver.

![[Screenshot 2026-02-15 at 09.46.38.png]]

If these partitions are small enough that's fine. But if that data is more than the Driver's JVM memory, then we'll get the error.

`df.collect` should be used with caution.

# Executor Memory Management

This is really important to understand. (Especially for interviews)

![[Screenshot 2026-02-15 at 09.49.55.png]]

Executor memory is divided into 4 major parts.

- **JVM Heap Memory**: Similar to Driver's heap memory, Executor's HEAP memory is for JVM tasks, and stores things like: DAG, Metadata, Broadcast Variables, Task scheduling info.
- **Off-Heap Memory:** Off-Heap memory in executor by default is `0`. We rarely use it, but sometimes it comes in handy.
- This is not managed by the JVM, we need to manage it.
- **Overhead Memory**: Similar to the Driver's overhead memory, we get 10% of this from what we requested from the JVM Heap Memory.
- **PySpark Memory**: This is again 0. We rarely use it


So, these are the most important parts of the memory that we always use with the executors:

![[Screenshot 2026-02-15 at 09.58.20.png]]


### Executor's HEAP memory

The OG though is the executor's JVM Heap Memory. It has its own hierarchy.

![[Screenshot 2026-02-15 at 09.58.36.png]]

Let's say, we requested some heap memory (~10Gb) from the resource manager (for our executor).

![[Screenshot 2026-02-15 at 10.01.49.png]]

As we said, we are also getting 10% of the Overhead Memory:

![[Screenshot 2026-02-15 at 10.02.51.png]]

In the executor, we need to understand what's happening here. So let's expand the heap memory of our executor:

![[Pasted image 20260215101204.png]]

Let's unpack the above image.≈
- GREY area: Our total 10Gb of the memory requested.
- Reserved Memory: Always a Fixed amount of 300MB for our spark engine.
- Spark Memory Pool: By default, that's 60% of our Total memory. Our total memory is `10Gb - Reserved Memory (300Mb)`. 
	- You can finetune this using `spark.memory.fraction`
- User Memory: This is the remaining memory (so, the rest 40% in case we reserved 60% for Spark Memory Pool).
	- This is allocated for UDFs (User-Defined Functions). So, for whatever functions we customly create.

Let's calculate the exact memory allocation based on the above (for 10Gb of requested memory with the default allocation percentages):

- Reserved Memory = 300MB
- Spark Memory Pool (60%) = 0.60 \* (9.7) = 5.82GB or 5820MB
- User Memory (40%) = 0.40 \* (9.7) = 3.88GB or 3880MB

### Expanding Spark Memory Pool

We have already explained what Reserved and User memories are for. Now, let's understand the Spark Memory Pool.

This is where the following is happening:
- Spark Functions (Transformations)
- Cached data
- more

This is the Spark Memory Pool, expanded:

![[Screenshot 2026-02-15 at 10.20.36.png]]

Let's take another example now and say that 5GB was allocated for the Spark Memory Pool (just to make maths simpler).

- **Storage Memory** is used for Caching (Long Term Memory). We store the data that we need to refer to later in the code. They are not elimitated.
- **Executor Memory** is used for the transformation processes (JOINs, Aggregations, etc..)

![[Screenshot 2026-02-15 at 10.46.42.png]]

This 50% allocation between the two types is being defined automatically, but we can change that by using `spark.memory.storageFraction`

Now, we need to understand the boundary behaviour:

![[Screenshot 2026-02-15 at 10.48.20.png]]


This boundary that separates storage from executor memory is not a "hard" boundary. It's flexible.

Even if we define that we want 50%, still this boundary can move up and down. That's where the concepts of "allocation" and "borrowing" come into play.

If Spark runs out of the memory we defined, it will automatically scale one or the other to fit what's needed.


### Unified Memory Management

![[Screenshot 2026-02-15 at 10.51.19.png]]

Unified Memory is another name for "Spark Pool Memory", in which we have these two types of memories:
- Executor/execution Memory (for performing Transformations)
- Storage Memory (for caching data)
- Seperator

Let's understand how the boundary (in green) is changing based on needs.

**Scenario 1** 
Executor Memory ran out but there's available Storage Memory left.

![[Screenshot 2026-02-15 at 10.58.50.png]]

Solution: Executor Memory will occupy the storage memory without any problems.


**Scenario 2** 
Executor Memory needs even more space

The executor memory will check which are the "Least Used Cached Data", and it will occupy those using the LRU method.

![[Screenshot 2026-02-15 at 11.02.11.png]]

Executor Memory has this authority -- it's prioritised because it needs to actually process the data by performing the transformations.


**Scenario 3** 
Storage Memory ran out but there's available Executor Memory left.

![[Screenshot 2026-02-15 at 11.03.57.png]]

In this case, Storage Memory can occupy Executor Memory

![[Screenshot 2026-02-15 at 11.04.25.png]]

**Scenario 4**
Executor Memory needs even more space

![[Screenshot 2026-02-15 at 11.04.55.png]]

In the scenario that We run out of Storage Memory but Executor doesn't have free space, there's no way Storage can occupy Executor Memory. Executor Memory is always prioritised.

Instead, Storage Memory will occupy more of its OWN memory, using the LRU approach - meaning, it will check which are the "Least Used Cached Data", and it will occupy that space using the LRU method.

![[Screenshot 2026-02-15 at 11.08.10.png]]


## Executor Out-Of-Memory (OOM) Error

![[Screenshot 2026-02-15 at 11.30.17.png]]

Let's say our dataframe has ID and Product Category. Let's say this data has millions of rows.

Our executor is processing the data (for simplicity let's suppose we only have 1 executor).

We also have a disk that's associated with our executor.

![[Screenshot 2026-02-15 at 11.31.57.png]]


---
***Side-knowledge: Scattered VS Skewed Data***
- **Scattered data:** Evenly spread, symmetrical, mean ≈ median.
	- **Definition:** Scattered data refers to values that are spread out across the range of possible values, often without any clear pattern or concentration. In a dataframe, this means the data points are distributed fairly evenly, and there is no strong clustering or *bias* toward any particular value.
	- **Scenario:** Suppose you have a dataframe of **random test scores** evenly distributed between 0 and 100.
- **Skewed data:** Concentrated at one end, asymmetrical, mean ≠ median.
	- **Definition:** Skewed data refers to a distribution where values are concentrated toward one end of the range, creating an asymmetry. In a dataframe, this means most data points are clustered near either the lower or upper end, with fewer values at the opposite end.
	- **Scenario:** Suppose you have a dataframe of customer wait times, where most people wait a short time, but a few wait much longer (right-skewed).

- **Scattered data** is often easier to analyze and model, as many statistical methods assume normality.
- **Skewed data** can affect statistical tests, regression, and machine learning models. You may need to transform (e.g., log, Box-Cox) or handle outliers.
---


Let's say we are doing a `GroubBy` operation in the product category column. As we've seen already, this will create partitions for each product category e.g. one for `food`, another for `shoes`, `dairy`, and so on.

So, if we have normally-scattered data, usually we don't have problems. E.g. We see 3 product categories in this picture, and three partitions in the executor. Tha's fine. Each partition (300MB each) will be allocated to a partition within the executor:

![[Screenshot 2026-02-15 at 11.49.30.png]]

Now, what will happen if we have more data? e.g. Imagine have a new category called "plants".

#### Data Spill
Now there's no available space in that executor ( that executor ran out of space). That's when "Data Spill" happens. 

![[Screenshot 2026-02-15 at 11.54.47.png]]

The data that's being transferred to the disk is data that's already computed. Whenever it needs this data, it will go grab it from the disk.

> 	NOTE: You can only shift the whole partition (from memory to disk) or no partition at all. You can't partition the partition basically.

This is the concept of spillin, and it's allowed -- no errors! So, why do we sometimes get the "OOM" error (out of memory) if we have so much available disk space?

If our data is "skewed" (not evenly distributed) we may have problems. Let's say: 

The product "FOOD" has data that are a total of 800MB 

![[Screenshot 2026-02-15 at 11.59.42.png]]

This creates a partition that's 800MB for the food category. Which means, the other two categories won't fit, and so they will be written on disk:

![[Screenshot 2026-02-15 at 12.00.22.png]]

Let's say now, our store only sells food products for some reason (the other products are low qualitiy and customers don't buy them). This will make the data even more skewed, with a lod of rows having "food" as a product category. 

![[Screenshot 2026-02-15 at 12.04.19.png]]

When applying GroupBy, we know that the "food" data will need to be under the same partition but now that's not possible because executor size is 1GB. This data will be spilled in the disk though.

![[Screenshot 2026-02-15 at 12.05.45.png]]

This data though is not processed. In order for this data to be processed, it needs to go to memory. So basically this wiill give us the error OOM.

Here we have two options:
1. Expand the available memory of our executor. This is a foolish approach though cause data will keep growing so we will always need to increase the memory overtime (expensive)
2. "Salt" the skewed data. Salting is an approach of eliminating Skeweness.

## Salting in PySpark

The way to fix the skeweness of data is by "salting" that data.

![[Screenshot 2026-02-15 at 12.09.47.png]]

We can add a new column called "salt".

![[Screenshot 2026-02-15 at 12.10.52.png]]

We know that our skewed data (food column) is let's say 1.5GB as per previous example.

Let's say we decide to split this data into 4 which would give us ~375MB per partition.

we can create an array of 4 numbers `[1, 2, 3, 4]` and assign these randomly into all food product categories. 

We now make 4 partitions for the food column.

![[Screenshot 2026-02-15 at 12.13.45.png]]

So now it can fit into the executor memory for transformations.

# Caching

It's one of the most used methods.

Why do we need caching? 

Let's say we do the following:

df1 = read()
df2 = df1.filter()
df3 = df2.groupBy()

This will create 3 DAGs, one by one.

`df1` will be stored in the Executor Memory.
![[Screenshot 2026-02-15 at 12.29.22.png]]

The moment we start creating `df2`, the previous one will be removed from the Executor Memory because , as we said, it's a short-term memory.

But, wait a second! `df2` needs `df1` to be computed as we see (`df2 = df1.filter())`. But we just said that `df1` will be removed.. So, df2 would need to recompute df1 and then also filter it. The same would happen with df3, which would need to compute df1 and df2 and then group df2 by.

This is a waste of compute.

But, what IF we could store df1 in long-term memory and we don't need to recompute it? That would save a lot of compute. This is what's called "caching".

### Coding Example

```Python
from pyspark.sql import SparkSession
from pyspark.sql.functions import *
from pyspark.sql.types import *


# Create first DataFrame
data1 = [
    (1, "Alice"),
    (2, "Bob"),
    (3, "Charlie"),
    (4, "David"),
    (5, "Eva")
]
df1 = spark.createDataFrame(data1, ["id", "name"])

# We add a new column 'flag = "Yes"'
df1 = df1.withColumn('flag', lit("Yes"))

# we are CACHING data from df1
df1.cache()

# This will used the cached data from df1
# So, it won't re-create the "YES" flag
df2 = df1.filter(col('id')==1)

```

>	NOTE: We should only cache small data otherwise OOM errors will be coming our way.
>	
>	Do that if your dataframes are small enough, and also if you are reusing them multiple times in your code.


## Persist()

Caching is actually a special case of "persist()". 

We can prioritize where we want to store our data.

Caching is not an independent thing. IT's kind of special case of "persist.". Previously, everything was done via `persist()` and we had a lot of options to choose from. One of these options is the following:


![[Screenshot 2026-02-15 at 12.58.44.png]]

`df.persist(StorageLevel.MEMORY_AND_DISK`) is the same as `df.cache()`.

This flavor of persisting data prioritizes memory, and when there's no memory left, it spills the rest of the data to disk. 

But there are more ways to create persisting data. Let's see:

1. MEMORY_ONLY: Doesn't spill the data to disk -- the rest of the data will be recomputed.

![[Screenshot 2026-02-15 at 13.01.55.png]]

2. DISK_ONLY

This is nice because we don't need to worry about memory usage. But it's also the slowest way of caching data.

![[Screenshot 2026-02-15 at 13.03.44.png]]

3. MEMORY_ONLY_2

This is the same as MEMORY_ONLY, but the data is replicated 2 times. That's for fault tolerance.

4. OFF_HEAP (Experimental)

![[Screenshot 2026-02-15 at 13.16.32.png]]
Sometimes you can use off-heap memory (outside JVM heap) to store your data.

This is enabled by `spark.memory.offHeap.enabled=True`

This is maintained by us (not by Spark).

#### Code Example

```Python
from pyspark.storageLevel import StorageLevel

df1.persist(StorageLevel.MEMORY_ONLY)
```


Unpersisting data:

```Python
df1.unpersist()
```



# EDGE Node

![[Screenshot 2026-02-15 at 13.21.04.png]]

So far, we were imagining that we, as developers, are directly communicating to the cluster / resource manager.

But if we are a Senior Engineer, would we want to allow a Junior Engineer to talk directly to the resource manager?

That's why we have EDGE nodes.

We have two "teams": CLIENT and CLUSTER.

CLIENT can be any machine (physical or virtual) that acts as our Edge Node. This machine has access to the CLUSTER (RESOURCE MANAGER).

We can log in to that Edge node and talk to the resource manager.

This concept gives birth to the concept:

### Cluster mode VS Client Mode

These are two different types of "deployment nodes".

![[Screenshot 2026-02-15 at 13.26.26.png]]

### Client Mode
If we deploy our application in "client mode", let's see what happens

In this case, Cluster (Resource) Manager will create the Driver Node on the CLIENT machine.

### Cluster Mode
That's what used in most cases. 

In this mode, Driver will be created on one of the machines, and worker nodes in the others.

![[Screenshot 2026-02-15 at 13.28.43.png]]

### When are we using one vs the other?

If we turn off the "client" machine in a "client mode" app, the whole app will break.

Good things of "client mode":
- We write code and see output on our own machine.
- When working in "dev", it's really handy.

Bad things:
- Network latency will be high.
- Not good for production

Cluster is best for "production".


# Partition Pruning

Our executor will be writing the data in a destination (e.g. datalake). Instead of writing everything in one folder, we will create partitions in that storage space.

E.g.
 - IT data
 - Finance data
 - HR data

![[Screenshot 2026-02-15 at 13.38.36.png]]

This type of partition is not to be confused with memory partitions (that we talked about previously). This is when writing data to a destination.

So why do we not just save everything under one folder? This is because of optimization.

Let's take the example that we are fetching data from "HR" only. If we don't have these partitions, Spark would need to read ALL data. After reading all that data, it will then need to apply transformations to that data.

But with partitions, Spark will go to the relevant folder directly. so we are saving a lot of processing.

That is making the Spark Jobs really optimized. This is called "partition pruning". Because we are not fetching all data, we are pruning the data.


### Writing data using partitions

```Python

from pyspark.sql import SparkSession
from pyspark.sql.functions import *
from pyspark.sql.types import *


# Create first DataFrame
data1 = [
    (1, "HR", "Alice"),
    (2, "IT", "Bob"),
    (3, "FIN", "Charlie"),
    (4, "HR", "David"),
    (5, "IT", "Eva")
]
cols = ["id", "department", "name"]
df1 = spark.createDataFrame(data1, cols)





```


```Python
# STEP: Write DataFrame using partitioning
output_path = "/Volumes/workspace/stream/partitions/OutputData"

df1.write \
    .mode("overwrite") \
    .partitionBy("department") \
    .parquet(output_path)
```

Paritions in "catalog":
![[Screenshot 2026-02-15 at 16.23.11.png]]

```Python

# STEP: Write DataFrame without partitioning
output_path_2 = "/Volumes/workspace/stream/partitions/OutputDataNoPartitions"

df1.write \
    .mode("overwrite") \
    .parquet(output_path_2)
```

![[Screenshot 2026-02-15 at 16.24.01.png]]

> 	NOTE: The number of partitions depends on the number of cores of the executor

![[Screenshot 2026-02-15 at 16.25.21.png]]


### Reading data using Pruning

Now that we have written this data, we can read them directly from the storage:

```Python
# Data that was partitioned
df_withpart = spark.read.format("parquet") \
                .load("/Volumes/workspace/stream/partitions/OutputData")

display(df_withpart)
```
>	This code will still read ALL data, because we are NOT using pruning.

This can be verified if you had databricks full account and could go to the following:
Job > SQL / DataFrame > Scan Partitions

![[Screenshot 2026-02-15 at 16.32.22.png]]

```PYTHON
# Data that was partitioned
df_withpart = spark.read.format("parquet") \
                .load("/Volumes/workspace/stream/partitions/OutputData") \
                .filter(col('department') == 'HR')
```

![[Screenshot 2026-02-15 at 16.34.47.png]]

Because we filtered in the same way the files were partitioned, we are "pruning" the result.


# Dynamic Partition Pruning

That's one step ahead of partition pruning.



We previously saw that we needed to apply `filter` to our partitioned data to use pruning. What if there was a way to apply pruning to non-filtered data? That's what Dunamic Partition Pruning is about.

Scenario:

```Python
df_1 = [
    (1, "HR", .., "Alice"),     # 1GB of Data
    (2, "IT", .., "Bob"),       # 1GB of Data
    (3, "FIN", .., "Charlie"),  # 1GB of Data
    (4, "HR", .., "David"),     # 1GB of Data
    (5, "IT", .., "Eva")        # 1GB of Data
]
```

We have two DFs --> `df1` and `df2`
- df1 = Big Table (fact)
	- Let's say we have partitioned our data based on the "department" column for this table.
	- Let's say that each department contain ~1GB of data.
- df2 = Smaller table (dim)
	- Non-partitioned table. Cause it doesn't make sense to create partitions for this one.

![[Screenshot 2026-02-15 at 16.36.37.png]]

Now, let's say we apply a JOIN:

`df1.join(df2)` <-- This gets data only for HR

Now, according to what we said before, `df1` should still read ALL partitions because we are not directly applying filter to `df1`. But what if I said, Spart will still only read the data for `HR`.

- Spark, before applying filescan, it will do a "Broadcast Exchange". 
- It will forward the query of `df2` whose filter is on "HR", and this will act as a dynamic filter, referenced from another query.
- The JOIN key used should be the column that the partition was applied to.

The only thing to note is:

***When we want pruning (even dynamic pruning) to be applied, we need to filter according to the way we have partitioned the data on disk.***


# Adaptive Query Execution (AQE)

That's a game-changer in Apache Spark. It's an automatic optimization technique. Before that, Data Engineers would have to do all optimizations manually.

So, Adaptive Query Execution (AQE) does the following:
- Dynamically Coalesce the partitions every time it finds the opportunity to do so
	- Whenever we apply Wide Transformations, it says "every time you're creating 200 partitions, why?". It will then coalesce these partitions in a way that makes sense.![[Screenshot 2026-02-15 at 16.55.25.png]]
- It also distributes data evenly
	- Instead of creating 200 partitions with 195 empty ones, it will not create these 195, and it will also coalesce the four small partitions into one bigger one ![[Screenshot 2026-02-15 at 16.57.52.png]]![[Screenshot 2026-02-15 at 16.59.05.png]]
- It Optimizes Join Strategy during RunTime
	- IT does that by calculating the "Query Statistics"
- It dynamically optimizes the skeweness of the data (it's an alternative to Salting, although we should still  use Salting because we have way more flexibility)


PySpark Course (practical): https://www.youtube.com/watch?v=94w6hPk7nkM





