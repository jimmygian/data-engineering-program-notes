-> C2_W4

## Core Components

We will start with the underlying architecture of Airflow. 

When you write your DAGs in Airflow, there are several components that work together behind the scenes to automatically: 
- **run** your **DAGs**, 
- **check** that the **dependencies** between tasks are **met**, 
- and transfer the status of your DAGs to the **Airflow user interface**. 

This figure shows the main components of Airflow:
![[Screenshot 2025-12-19 at 22.22.41.png]]

The components include: 
- a "**web server**" --> where the Airflow user interface runs, 
- a **"scheduler"**, 
- **workers**, 
- a **metadata database**, 
- and finally a **DAG directory**. 


When you create your Airflow environment, whether by directly installing Airflow or by using an Airflow managed service, all of these components *will be present* in your setup. 

You'll mainly interact with the DAG directory and the user interface, or UI for short. The remaining components will be running behind the scenes. 

- The **DAG directory** is a *folder* in which you store the *Python scripts that define your DAGs*,
- This DAG directory is **connected to the "web server**" on which the **Airflow UI** runs. 
![[Screenshot 2025-12-19 at 22.27.50.png]]


So for any DAG that you create and add to the DAG directory, you will automatically be able to **visualize it in the UI.** you can also use the UI to monitor, manually trigger, and troubleshoot the behavior of your DAGs and the tasks within each DAG. 
![[Screenshot 2025-12-19 at 22.29.51.png]]


But you don't always need to manually trigger your DAGs from the UI. You can also trigger them based on a schedule, or with an event, or rather you can do so with the help of the scheduler component of Airflow. 

The scheduler *constantly* monitors all the **DAGs you defined** in the DAG directory and the corresponding tasks. ![[Screenshot 2025-12-19 at 22.57.26.png]]
- Once per minute, by default, the scheduler checks whether any tasks should be triggered given a particular schedule, or by checking if their dependencies are complete. 
- Once the scheduler identifies a task that is ready to be triggered, it **pushes** the task to a **queue** and uses an **executor** to manage the execution of tasks. 
- The **executor**, which is part of the scheduler, **extracts the tasks from the queue** and sends them to the workers that run the tasks. 
- As the scheduler triggers a given task, you will see the status of the task changes from *scheduled* to *queued*. Then once the workers execute the task, you will see the status change to *running*, and finally *success* or *failed*.
   ![[Screenshot 2025-12-19 at 23.01.02.png]]
- The scheduler and the workers store the status of the tasks as well as the state of the DAGs in the **metadata database**. Then the web server extracts those states from the database and displays them to you in the UI. 

And so that was a quick tour of the core components of Airflow. 

**Managed Services for Airflow**
When you choose a managed service for Airflow, like Amazon Managed Workflows for Apache Airflow, or MWAA for short, all of these components will be automatically created and managed for you. 

![[Screenshot 2025-12-19 at 23.01.50.png]]

You'll notice in the architectural diagram above how Amazon MWAA organizes the core components of Airflow on the cloud. 

For instance, it uses an Amazon S3 bucket as the DAG directory and an Aurora PostgreSQL database as the metadata database. The other components are AWS networking components and additional AWS services that support securing your data as well as logging and monitoring your environment. 

Understanding the Airflow environment and how components interact with each other will help you troubleshoot issues when they occur.


## The Airflow UI


One of the main features you'll interact with when using Airflow is the user interface or UI. This is where you can monitor the status of your dags and their individual tasks, get insights into your historical dag runs and troubleshoot issues with your pipelines. Let's take a look at some of the basic features of the Airflow UI. 

When you open the UI, you will first land on this page, which is called the DAG View. 
![[Screenshot 2025-12-19 at 23.58.46.png]]


Here you can find a list of all the dags that you've created in your Dag directory. 
![[Screenshot 2025-12-19 at 23.59.17.png]]


For each dag, you can see some basic metadata like: 
- the Dag ID, 
- the tag, 
- the owner and 
- the schedule. 
![[Screenshot 2025-12-19 at 23.59.55.png]]

You can also check when the DAG was last run 
- as well as the status of the Dag runs, 
- how many dags are currently queued or currently running, 
- and how many were completed successfully or have failed. 
![[Screenshot 2025-12-20 at 00.01.46.png]]


There's also a **more granular review** for each DAG where you can check the status of all tasks, including 
- how many tasks are queued or running or successfully executed or failed, 
- and also how many were skipped up for retry, up for reschedule, and so on. 

![[Screenshot 2025-12-20 at 00.03.09.png]]



Let's see how you can interact with the dags in this Dag View. 

On the left, you can see the ***toggle*** to pause or unpause a DAG, and on the right, you can manually ***trigger*** a DAG or ***delete*** it from the view. 
![[Screenshot 2025-12-20 at 00.04.55.png]]


Finally, you can ***filter*** the dags you see on this page by the *status* or by *custom tags* if you assign them. ![[Screenshot 2025-12-20 at 00.05.43.png]]


You can click on a **DAG ID** to get more detailed information about that dag. Let's take a closer look at this dag. 

This new view is called the grid view, and it gives you more detailed insights into each dag run and its corresponding task instances. ![[Screenshot 2025-12-20 at 00.06.22.png]]
- On the left side, you see a **bar chart** of all the previous runs of this dag. Here the chart shows two dag runs. In other words, the dag was run twice. 
- You can see the duration of each run, which is represented by the height of the bar. 
- The status of each run is color coded, where red represents the failed status and green represents the success status. 
- For each run, you can also check the outcome of all individual task instances. 


Here, the dag consists of two tasks for the dag run that failed. 
![[Screenshot 2025-12-20 at 00.08.02.png]]
- You can see that the first task had failed and the second one, it's orange coded, which means it did not start because the upstream task had failed. 
- You can always refer to the color legend here at the top of the page to check what each color means. 

On the right side of the grid view, you can find four tabs, details, graph, gant, and code. 
![[Screenshot 2025-12-20 at 00.10.30.png]]

The details tab displays detailed information about the historical dag runs, such as the total number of times the dag has run, the total number of successful runs, the total number of failed runs, and the minimum mean and max duration of the dag runs. 
![[Screenshot 2025-12-20 at 00.11.04.png]]
- You can use all of these metrics as indicators for the health of your pipeline. 


Let's check out the ***graph*** tab. 

Here, you can see a visualization of your dag, which helps you explore your dag structure and ensure that you have correctly configured the dependencies between your tasks. 
![[Screenshot 2025-12-20 at 00.11.59.png]]
- The DAG visualization you're seeing above does not correspond to any specific dag run. If you'd like to visualize the status of a specific dag run, let's say the one that has failed, you can click on it on the left side. ![[Screenshot 2025-12-20 at 00.12.57.png]]
	- Each task is now ***color coded*** based on its status within this run. 


Suppose you want to understand why the first task extract from API has failed. Let's click on this task. A new tab labeled Logs has just appeared on the top, and you can find all the error messages in this tab. 
![[Screenshot 2025-12-20 at 00.13.42.png]]
![[Screenshot 2025-12-20 at 00.14.04.png]]

Based on these messages, you can try to fix the code for this DAG, and when you're done fixing it and ready to retry this task, you can also click on the clear task which will rerun the task. 
![[Screenshot 2025-12-20 at 00.15.20.png]]
![[Screenshot 2025-12-20 at 00.15.53.png]]
- If it runs and it is completed successfully, then all the remaining tasks in the pipeline will run as well. 


Next, the ***Gantt*** chart tab shows you the *queued* duration in gray and the *run* duration in green for each task of a specific DAG run. ![[Screenshot 2025-12-20 at 00.17.05.png]]
- You may find this chart helpful whenever you need to identify any bottlenecks in your pipeline. 


Finally, the ***code*** tab shows a code that corresponds to the given DAG. 

This is not where you will interact or edit your Dag code but you can use a tab to ensure that the code in the UI is in sync with your code in the Dag directory. 
![[Screenshot 2025-12-20 at 00.18.42.png]]



There are many more features in the Airflow UI and more things you can do to interact with your dags. But in this section, we covered the basic UI features that you will use most of the time. 



## Creating a DAG

In this video, we'll go through the **details** of **building a simple DAG** using some core Airflow concepts such as a **DAG** and **operator** classes. 

Let's take the example of an ETL process. 

---

The following diagram shows a DAG representation of such a process, which consists of three **tasks**, that we named *extract*, *transform*, and *load*. 

![[Screenshot 2025-12-20 at 10.07.06.png]]

We don't need to get into the specific details of each task for now, but rather focus **how to set up a DAG that has this structure**. 

In a coding software (e.g. VS Code) we can set up our Airflow:

Let's start with creating an empty Python script in this folder, and let's call it my underscore first underscore DAG dot py. 

```structure
src/
  |
  |-> my_first_dag.py
  
```


In this script, you need to import some packages, starting with the `DAG` class and the `datetime` module. You're going to use both of them to create the **DAG instance**, which is the next thing that we'll do. 

```Python
from airflow import DAG
from datetime import datetime
```


### Creating a Context Manager (DAG Instance)

To create the DAG instance, you will use the `with ..:` statement, known as the context manager, which will help you **group** and **define** all the **tasks** that belong to this DAG here. 

```Python
from airflow import DAG
from datetime import datetime

with DAG():
	...

```

Now you need to specify the **parameters** of the DAG. 
```Python
from airflow import DAG
from datetime import datetime

# context manager
with DAG(
		dag_id = "my_first_dag",
		description = "ETL pipeline",
		tags = ["data_engineering_team"],
		schedule = "@daily",
		start_date = datetime (2024, 12, 1),
		catchup = False
	):
	# define tasks here

```

- The first parameter is the  `dag_id`: This is the **name** that you will use to identify your DAG in the DAG view in the **Airflow UI**. So when you choose to use your `dag_id`, make sure it's unique. 

 - You can also specify a DAG `description` if you wish to provide more details related to the DAG. The DAG description will appear when you hover your mouse over the DAG name in the Airflow UI. 
   
 - You can also set the `tags` parameter to be a **list of tags** that you can use to filter DAGs in the UI. 
   
 - And the next important parameter is `schedule`, which you can use to define when the DAG will run. 
	 - You can assign the schedule parameter a *Cron* expression, like: 
		 - `0 8 * * * ` which would mean that the DAG will run every day at 8 a.m. 
		 - Or you can use Cron presets such as `@daily`, `@monthly`, `@hourly`, `@weekly`, etc. 
		 - Or you can pass a `timedelta` object from the date time package. For example, this timedelta object means that the DAG will run every three days. 
		 
- Besides the schedule, you need to also specify the `start_date`, which is the first date your DAG will be executed on. Here we can use a `datetime` object to specify the year, month, and day of the start date. 
  
- The last parameter that I'm going to specify here is `catchup`. This is a **Boolean** parameter that is set by **default to true**. 
  How is this parameter useful? Let's say that you paused your DAG for a period of time and then unpause a DAG to make it active again. If you set `catchup` to `true`, the scheduler will *kick off a DAG run* for any missed interval when the DAG was paused. Additionally, it will trigger a DAG run upon the first time you run the DAG if the start date of the DAG precedes its creation date. 



### Defining DAG tasks

Let's now move into defining the tasks for the DAG. You need to use **Airflow operators** to define each task. Operators are *Python classes* that are used to encapsulate the logic of the tasks or how data should be processed in your pipeline. 

Operators are provided by Airflow, which you can import to your code and create each task as an instance of an Airflow operator. There are several operators that you can choose from. 
- You can use a `PythonOperator` if you want to execute a Python script that contains the logic of your task. 
- You can choose a `BashOperator` if you want to execute Bash commands. 
- You can use an `EmptyOperator`if you want to organize your DAGs, such as marking the start and end of the pipeline. 
- And finally, you can choose an `EmailOperator` if you want Airflow to send you a notification via email. 
- There's also a special type of operator known as `Sensor`, which you can use to make your DAGs event-driven. 


In the example here, I'm going to use the Python operator to define each of the three tasks. 
![[Screenshot 2025-12-20 at 11.12.52.png]]

First, you need to import the class `PythonOperator`. 

```Python
from airflow import DAG
from datetime import datetime

# IMPORT PythonOperator
from airflow.operators.python import PythonOperator

...


```


Then you'll go inside the context manager to define the first task, which is the extraction step. To do so, you will create an instance of Python operator, which needs two parameters. 

```Python
from airflow import DAG
from datetime import datetime
from airflow.operators.python import PythonOperator

# context manager
with DAG(
	dag_id = "my_first_dag",
	description = "ETL pipeline",
	tags = ["data_engineering_team"],
	schedule = "@daily",
	start_date = datetime (2024, 12, 1),
	catchup = False
):
	# define tasks here
	task_1 = PythonOperator(task_id='extract', python_callable=extract_data)
	task_2 = PythonOperator(task_id='transform', python_callable=transform_data)
	task_3 = PythonOperator(task_id='load', python_callable=load_data)
```

- The first parameter is `task_id`, which you can use to specify the **name** of the task. 
  This name will be used to *reference the task in the Airflow UI*. 
- And the second parameter is `python_callable`, which expects a Python function that contains what needs to be done in the extract step. You can define this function here in the same file, or you could define it in another file and import it into the code here. 
- Then we can repeat the same steps to define the second and third tasks (assuming that the two Python functions `transform_data` and `load_data` already exist). 


Now we need to go back and create these Python functions in this file. 

```Python
from airflow import DAG
from datetime import datetime
from airflow.operators.python import PythonOperator


# Task Functions
def extract_data():
	# code for extracting data
	print("Done with the extraction task")
def transform_data():
	# code for transforming data
	print("Done with the transformation task")
def load_data():
	# code for loading data
	print("Done with the loading task")


# context manager
with DAG(
	dag_id = "my_first_dag",
	description = "ETL pipeline",
	tags = ["data_engineering_team"],
	schedule = "@daily",
	start_date = datetime (2024, 12, 1),
	catchup = False
):
	# define tasks here
	task_1 = PythonOperator(task_id='extract', python_callable=extract_data)
	task_2 = PythonOperator(task_id='transform', python_callable=transform_data)
	task_3 = PythonOperator(task_id='load', python_callable=load_data)
```
- You can define these functions before the DAG definition as shown here, or you could place each function definition next to the corresponding task within your DAG. 


So now you have the DAG and the tasks defined. You just need to specify ***the dependencies between the tasks*** (the order in which the tasks need to be executed). Here's how you can define the dependencies using the bit shift operator. 

```Python
from airflow import DAG
from datetime import datetime
from airflow.operators.python import PythonOperator


# Task Functions
def extract_data():
	# code for extracting data
	print("Done with the extraction task")
def transform_data():
	# code for transforming data
	print("Done with the transformation task")
def load_data():
	# code for loading data
	print("Done with the loading task")


# context manager
with DAG(
	dag_id = "my_first_dag",
	description = "ETL pipeline",
	tags = ["data_engineering_team"],
	schedule = "@daily",
	start_date = datetime (2024, 12, 1),
	catchup = False
):
	# define tasks here
	task_1 = PythonOperator(task_id='extract', python_callable=extract_data)
	task_2 = PythonOperator(task_id='transform', python_callable=transform_data)
	task_3 = PythonOperator(task_id='load', python_callable=load_data)
	
	# Define dependencies using the bit-shift operator
	task_1 >> task_2 >> task_3


```
- This statement means that task one should be executed and completed before tasks two starts, and task two should be completed before task three starts. 



## Scheduling Your DAG & Other DAG Parameters

When instantiating the DAG in the previous section, I specified the following parameters: _dag_id_, _tags_, _description_, _schedule_, _start_date_ and _catchup_. You can also specify other DAG parameters. Check out the [Airflow documentation](https://airflow.apache.org/docs/apache-airflow/2.10.4/_api/airflow/models/dag/index.html) to learn more.

In this reading item, we'll take a closer look at what the _start_date_ parameter does. When you orchestrate your pipeline in Airflow, you may encounter the terms "_data interval_ " and "_logical date"_ in the Airflow UI or in the Airflow documentation. Each DAG run is associated with a **data interval** that *represents the time range it operates in*. Let’s say you instantiated a DAG to run daily using the cron preset `@daily` and the start date is March 1. 

As shown in the following figure, each DAG run operates in a data interval that starts each day at midnight (00:00) and ends at midnight (24:00).

![[Screenshot 2025-12-20 at 14.43.20.png]]

The “logical date” is a term associated *with a specific DAG run*, and it denotes the start of the data interval.

![[Screenshot 2025-12-20 at 14.45.02.png]]

The _start_date_ argument for the DAG marks the "_logical date"_ or the start of the first "_data interval"_.

Given a data interval, the DAG is executed **at the end of the data interval**, not the beginning. This is because Airflow was developed as a solution for ETL needs, where you typically need to aggregate data collected over a time interval. So if you want to analyze the data for March 1, you would need to wait till March 2 midnight after all data for March 1 becomes available. This is why a DAG is always executed at the end of the data interval, and the logical date of a DAG run (start of the data interval) represents the date _**for**_ which the DAG run is executed, not when the DAG is actually executed. So the first DAG run will only be scheduled one interval after start_date.


**Check out these links if you want to learn more about scheduling in Airflow:**

- [Data-interval](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/dag-run.html#data-interval)
- [What does execution date mean?](https://airflow.apache.org/docs/apache-airflow/stable/faq.html#what-does-execution-date-mean) 
- You can customize your DAG scheduling using  [timetables](https://airflow.apache.org/docs/apache-airflow/stable/authoring-and-scheduling/timetable.html). In addition to scheduling DAGs, you can make your DAG data-aware, meaning that it is triggered when a data object is updated in another task. Here's an [example](https://airflow.apache.org/docs/apache-airflow/stable/authoring-and-scheduling/datasets.html) of this.

## Airflow Operators

You learned about some of the Airflow operators such as `EmptyOperator`, `PythonOperator`, `BashOperator`, `EmailOperator`. You can learn more about these operators by checking out the [Airflow documentation](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/operators.html). In addition to those [core operators](https://airflow.apache.org/docs/apache-airflow/stable/operators-and-hooks-ref.html) provided by Airflow, there’s a [list of other operators](https://airflow.apache.org/docs/apache-airflow-providers/operators-and-hooks-ref/index.html) that are released independently of the Airflow core that allows you to connect to **external systems**. For example, [this link](https://airflow.apache.org/docs/apache-airflow-providers/operators-and-hooks-ref/index.html) shows all the possible operators that you can use to interact with each AWS service, and [this link](https://airflow.apache.org/docs/apache-airflow-providers/operators-and-hooks-ref/software.html#transfers) includes the operators you can use to copy data, for example, from a database to S3. It is generally recommended to use the available operators instead of writing your own code from scratch.

In the previous section, the two parameters that were specified in the `PythonOperator` were `task_id` and `python_callable`. You can always review the [Airflow documentation](https://airflow.apache.org/docs/apache-airflow/stable/_api/airflow/operators/python/index.html#airflow.operators.python.PythonOperator) to see what other parameters you can specify for PythonOperator. [Here](https://airflow.apache.org/docs/apache-airflow/1.10.13/_api/airflow/operators/index.html#package-contents) is another set of parameters that you can pass to any operators, it includes the following parameters:

- email (str or list[str]): the ‘to’ email address(es) used in email alerts. 
- email_on_retry (bool): indicates whether email alerts should be sent when a task is retried
- email_on_failure (bool): indicates whether email alerts should be sent when a task failed
- retries (int): the number of retries that should be performed before failing the task

Check out this [link](https://www.astronomer.io/docs/learn/what-is-a-sensor) if you'd like to learn more about Airflow sensors, another special kind of operator.


## Defining Dependencies

You learned that you can use the bit-shift operator (>>) to specify the dependencies between tasks. Here are some examples:
![[def_dep.png]]

## Additional References

- [An introduction to the Airflow UI](https://www.astronomer.io/docs/learn/airflow-ui)
- [Airflow UI - screenshots](https://airflow.apache.org/docs/apache-airflow/stable/ui.html#)



## Xcom and Variables

In this section, you'll learn 
- how to **pass data** from one task to another using airflow XCom, 
- and how to **create global variables** in the airflow UI. 

Let's get started.

![[Screenshot 2025-12-20 at 17.33.37.png]]

Here's a sample DAG. In the task `get_random_book`, you request the data of a randomly chosen book from a *book API* and then store that data in an *S3 bucket* to be used in subsequent tasks. You essentially pass data from one task to another using an intermediate storage, in this case, the S3 bucket. 
![[Screenshot 2025-12-20 at 17.37.36.png]]

This method is appropriate when you want to pass large datasets between tasks. But for small amounts of data, there's another method called `XCom` that you can use. `XCom`, short for *"cross-communication"*, is a key airflow feature for sharing data among tasks. It's designed to pass information like 
- metadata, 
- dates, 
- single value metrics, 
- or simple computations between tasks. 

In a given task, if there's a value that you'd like to use in another task, you can sort in an `XCom` variable by calling the `xcom_push` method. The `XCom` variable is then pushed to a **metadata database**. Each `XCom` contains the following information:

- A **key**, which is the name of the XCom variable, 
- the stored **value**, 
- the **timestamp** at which the variable was created, 
- as well as a **DAG ID** and the **task ID** from which the XCom variable originated. 
![[Screenshot 2025-12-20 at 17.38.45.png]]
To extract the value stored in an XCom variable in any given task, you can call the method `xcom_pull`. 


Let's go over an example. ![[Screenshot 2025-12-20 at 17.40.45.png]]The DAG you see here consists of two tasks. 
- The *extract_metric* task connects to an API, sends a request for a certain data and then computes a metric based on the return data. 
- The second task prints the metric computed by the first task. 

Since you need to *pass* some data between the first and second task, you can use the XCom feature here. 

- The first task uses the function `extract_from_api()`. This is where you need to call `xcom_push`. 
- The second task uses the function `print_data()`. This is where you need to call `xcom_pull`. 

### `xcom_push`
- `extract_from_api()`:
  ![[Screenshot 2025-12-20 at 17.43.37.png]]This is the code for the `extract_from_api` function. 
	- Here I called the REST API of a job site to get the latest 40 remote job postings for data engineering in the US. 
	- Then I computed the ratio of those jobs and asked for a senior data engineer. 
	- Now, you want to pass this value to the second task. 
- You need to first store the obtained value in an XCom variable by calling the method `xcom_push`. This method expects two parameters: the **key** of the variable and the **computed** value. 

**Task Instance**
The above code won't work cause we haven't "imported" the `xcom_push` method anywhere in our code.
`xcom_push` is a method that's *associated with a **task instance***. By task instance, I mean the object that represents the currently running task. 

`**context`
Airflow has a set of **built-in variables** that contain information about the task that is currently running, including the task instance. This information is stored in a dictionary called ***"airflow context"*** that you need to pass to the `extract_from_api` function as an argument, like you see here: 

```Python
def extract_from_api(**context):
	...
```
> *-> More on [context](https://www.astronomer.io/docs/learn/airflow-context)* 

To get the task instance object from a context dictionary, you can use `context` and in brackets, you'll use `ti` (stands for *task instance*). 

Then you can call `xcom_push`:

```Python
def extract_from_api(**context):
	...
	context['ti'].xcom_push(
			key='ratio_senior_jobs', 
			value=ratio_senior_jobs
		)
```

### `xcom_pull`
Then, to access a computed ratio of the XCom variable in the second task, you need to call `xcom_pull`following a similar approach:

```Python
def print_data(**context):
	print(context['ti'].xcom_pull(key='ratio_senior_jobs', task_ids='extract_metric'))
```



### Checking your XCom vars in Airflow UI

When you run your DAG, you can check your **XComs** in the **Airflow UI** by clicking on *Admin* and then navigating to *XComs*. 
![[Screenshot 2025-12-20 at 17.59.17.png]]

Here's the XCom variable that we just created along with this corresponding value.
![[Screenshot 2025-12-20 at 17.59.31.png]]

>	*I want to offer you a word of caution about using XComs. They are not designed to pass large datasets like DataFrames between tasks as they degrade the performance of your DAGs and the metadata database.* 
>	
>	*If you need to share large datasets between tasks, you should follow what you did in the previous lab and use an intermediate storage like **S3**.* 


### User-Created Variable

Now let's discuss another airflow feature using the same DAG as an example. If you examine the API requests of the first task, you see that the value of certain parameters, such as `count` and `geo` are hard-coded, which means they are **directly included in the code**. 

```Python
def extract_from_api(**context):
	import requests
	response = requests.get(
			"https://jobicy.com/api/v2/remote-jobs",
			params={
				"geo": "usa",
				"industry": "engineering",
				"tag": "data engineer"
			}).json()
		)
	
	count = 0
	
	for job in response['jobs']:
		if job['jobLevel'] == 'Senior':
			count += 1
	
	ratio_senior_jobs = count / len(response['jobs'])
	context['ti'].xcom_push(
			key='ratio_senior_jobs', 
			value=ratio_senior_jobs
		)
```


![[Screenshot 2025-12-20 at 18.06.47.png]]

But what if you don't want those values to be fixed because you may need to change them in future DAG runs, or maybe you want to experiment with more than one value? 

You can update the values in the code, but this approach might be error prone and not the most efficient, especially if the values are repeated many times in your code. 

Instead of including hard-coded values within your DAG or task definitions, you can: 
- create **global variables in the Airflow UI** or 
- create **environmental variables** in your development environment and use these variables inside your code. 

Let's create two variables in the Airflow UI, one for the number of posts and another for location. 

If you click on the `Admin` tab  in the Airflow UI  and then select the `Variables` option, you'll see your list of variables. 
![[Screenshot 2025-12-20 at 18.09.07.png]]
![[Screenshot 2025-12-20 at 18.10.00.png]]
Prior to a new variable, you can click on this plus sign and then specify the **key** and **value** of the variable. ![[Screenshot 2025-12-20 at 18.10.40.png]]

To use those variables inside your code, you need to import the `variable` module, and then inside your code, you use the `variable.get `method to retrieve the total number of posts and the list of locations. 

```Python
from airflow.models import Variable

Variable.get(key='number_post')
```

Here you set the parameter `deserialize_json=True` if you want this method to return the **JSON** object as a **dictionary** instead of a string. 

```Python
Variable.get(key='locations', desrialize_json=True)['geo']
```


## Best Practices for Writing Airflow DAGs

When writing DAGs, there are some best practices that help ensure your code is efficient, readable, idempotent and reproducible (like with any code). Let’s go through some of these basic practices.

| **Best practices**                                                                                                                                        | **Explanation/Example of a bad code**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| --------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Keep tasks simple and atomic                                                                                                                              | When you prepare your pipeline for orchestration, you need to identify the tasks or steps of your pipeline. Keep your tasks simple such that **each task represents one operation**. You don’t want to end up with one task that does everything, otherwise you'll lose visibility into your data pipeline and reduce the readability of your code, which does not support idempotency.  <br><br>For example, in an ETL or ELT process, you would need to create at least three tasks: extract, transform, load, instead of creating just one task that handles the entire process.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Avoid top-level code                                                                                                                                      | In the following code,<br><br>`call_some_function()`<br>`perform_computation()`<br><br>`with DAG(dag_id="example_xcom", start_date=datetime(2024, 3, 13), schedule='@daily',catchup=False):`<br>`    task_1 = PythonOperator(task_id='extract',python_callable=extract_api)`<br>`    task_2 = PythonOperator(task_id='load_data',python_callable=load)`<br><br>`    task_1 >> task_2`<br><br>`call_some_function() `and `perform_computation() `are both high-level codes. In general any code that isn’t part of your DAG or operator instantiations is considered to be top-level code. This type of code will be executed at the time when the DAG is parsed by the scheduler. On the other hand, any code that is part of an operator is executed when the task runs, not when the DAG is parsed. Top-level code can cause performance issues because the scheduler checks the DAG directory and parses the DAG files every 30 seconds. **So it may not be efficient to execute the high-level code this frequently especially if the code makes some requests to an API or a database.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Use variables (user-created variables, Airflow [built-in variables and macros](https://airflow.apache.org/docs/apache-airflow/stable/templates-ref.html)) | **User-created variables:** Including hard-coded values directly in your code is generally not a good practice in software development. This is because they make your code less readable and more error-prone -- you may need to use the same value in multiple places and updating the same value in multiple places can be error-prone. The same principle also applies to when you write code to define your pipelines. Instead of including hard-coded values within your DAG or task definitions, you can store these values by creating variables in the Airflow UI or creating environmental variables and use these variables dynamically inside your code.<br><br>[Recommendations from Airflow documentation regarding using Variables](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/variables.html): “Variables are global, and should only be used for overall configuration that covers the entire installation; to pass data from one Task/Operator to another, you should use XComs instead. We also recommend that you try to keep most of your settings and configuration in your DAG files, so it can be versioned using source control; Variables are really only for values that are truly runtime-dependent."<br><br>**Airflow built-in Variables:** You learned that Airflow has a set of built-in variables that contain information about the currently running DAG and its tasks, such as the logical date of the DAG run and task instance (for a list of such variables, check [here](https://airflow.apache.org/docs/apache-airflow/stable/templates-ref.html)). You learned that you can access these variables within a task function by passing the context dictionary as an argument to the function. You can also pass these variables directly to the `PythonOperator` using a syntax known as Jinja templating, which looks like this : “{{ds}}”. You use double curly brackets and inside the brackets you specify the variable you’d like to access. In this example, ds represents the logical date of the DAG run.<br><br>Let’s see an example: Assume that your python_callable is a function that expects the name of a file. For example, this function loads some data to an s3 bucket and requires that you pass the file name.  And let’s say you want to include the logical date in the file_name.<br><br>`def load_to_s3(file_name):`<br>`    # code that loads data`<br>`    print(file_name)`<br><br>So you can specify this information in the PythonOperator as follows:<br><br>`task_load_s3 = PythonOperator(task_id="load_to_d3",`<br>`    python_callable=load_to_s3,`<br>`    op_kwargs={'file_name': "data/created{{ds}}/file.csv"})`<br><br>The parameter op_kwargs allows you to specify the arguments that you need to pass to the function load_to_s3. Note how the logical date was included in the file name using templating (“{{ds}}”). |
| Task groups                                                                                                                                               | In the Airflow UI, you can **group tasks** using **Task Groups** to organize your DAGs and make them more readable. Inside the task group, you can define tasks and their dependencies using the bit-shift operators `<<` and `>>.` You can create a Task Group using the "with" statement, as shown in the following example.<br><br>`from airflow.utils.task_group import TaskGroup`<br><br>`with DAG(...):`<br>`    start = DummyOperator(...)`<br>`    with TaskGroup('task_group')as task_group:`<br>`        task_a = PythonOperator(...)`<br>`        task_b = PythonOperator(...)`<br>`        task_a >> task_b`<br>`    end = DummyOperator(...)`<br>`    start >> task_group >> end`<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Other practices (Airflow is an orchestrator not an executor)                                                                                              | - Heavy processing should be assumed by execution frameworks (e.g. Spark) not Airflow<br>    <br>- For large datasets, don’t use XComs (push dataframes). Use intermediary data storage instead.<br>    <br>- Including code that is not part of your DAG or operator makes your DAG hard to maintain and read: consider keeping any extra code that is needed for your tasks in a separate file.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |

**Additional References** _(if you’d like to learn more)_
- [Airflow best practices](https://airflow.apache.org/docs/apache-airflow/stable/best-practices.html#communication)
- [Other best practices](https://docs.astronomer.io/learn/dag-best-practices)
- [Functional-data-engineering-a-modern-paradigm-for-batch-data-processing](https://maximebeauchemin.medium.com/functional-data-engineering-a-modern-paradigm-for-batch-data-processing-2327ec32c42a) by Maxime Beauchemin

