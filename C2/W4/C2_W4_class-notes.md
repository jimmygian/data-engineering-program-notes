Welcome to the fourth and final week of this course. This week is all about **orchestration**. But really, orchestration is closely intertwined with many of the concepts you've already been exploring. 

In the previous weeks materials, we focused on the **DataOps pillars** of *automation*, *observability* and *monitoring*. You got some practice using the infrastructures code framework, **"Terraform"**, and you set up **data quality checks** and monitoring of your data pipelines using **"great expectations"** and **"CloudWatch"**. 

Orchestration, you'll be doing more work within each of these areas of DataOps. You'll automate individual tasks in your data pipeline and *move from the concept of **infrastructure as code** to the concept of **"pipelines as code"** using the **"Airflow"** orchestration platform.* With airflow, you'll build in data quality checks and additional monitoring as well. 

Orchestration is closely related to other undercurrents of the data engineering life cycle like data management and software engineering. However, it's important enough to be called out as a separate undercurrent of the Data Engineering Lifecycle. It is given how central it is to your work as a data engineer. 

This week, we're going to take what you learned about DataOps in the previous week and build on it. 
- We'll start by taking a look at how pipelines were set up **before** orchestration tools existed. 
- Then, we'll cover the **evolution of orchestration tools** that led us to where we are today and where that evolution might lead to in the future. 
- After that, we'll dig into the **details of orchestration** and discuss at a basic level how to **implement** orchestration in your data pipelines. 

There are a number of different orchestration tools on the market today, and many of the concepts we'll be covering this week are *tool agnostic*. However, the Number 1 tool being used in industry today is **"Airflow"**. So after we get through the basics, we're going to focus on the details of orchestration using Airflow since that's what you're most likely going to be using on the job today as a data engineer.


## Before Orchestration

### Pure Scheduling Approach
Before orchestration, the simplest way to automate a data pipeline, or any set of software tasks for that matter, was to set up a series of **"Cron jobs"**. 

Cron is a command line utility that was first introduced in the 1970s to execute a particular command at a specified date and time. ![[Screenshot 2025-12-19 at 19.13.12.png]]

To schedule a job with *cron*, you simply write a command or entry known as a Cron Job, where you specify **five numbers separated by spaces and followed by the command you want to schedule.** 
![[Screenshot 2025-12-19 at 19.13.50.png]]

These five numbers - from right to left - indicate: 
- the day of the week (0: Sunday to 6: Saturday) 
- the month of the year (from 1 to 12)
- The day of the month (from 1 to 31), 
- the hour of the day (from 0 to 23), 
- and the minute of the hour (from 0 to 59). ![[Screenshot 2025-12-19 at 19.14.52.png]]

You can also include an *asterisk* in place of any of these five numbers to indicate **no restriction** on that value. 

![[Screenshot 2025-12-19 at 19.16.47.png]]


So, for example, this cron job 
![[Screenshot 2025-12-19 at 19.18.28.png]]
will print "Happy New Year" to the terminal of the computer that it's running on every year at midnight on January 1. 



So how would you have scheduled the tasks in your data pipeline? 

- Suppose you had a data pipeline where you were **ingesting data from a REST API**. 
- And suppose you wanted to ingest data from the API **every night at midnight**. 
- And you had a python script called` ingest_from_rest_api.py` to do the ingestion step. 

In that case, you could have written a cron job like this:
```cronjob
0 0 * * * python ~/ingest_from_api.py
```


Then maybe you wanted to do some in flight cleaning or processing to the API data. And let's suppose you had another Python script written to perform that step. And maybe you knew that it always took less than an hour to ingest all the new data from the API. Then you could have set up the next step, the in flight transformations to take place at 1AM every morning. 

That would look like this as a cron job:
```cronjob
0 1 * * * python ~/transform_api_data.py
```


And then maybe you were also ingesting data from a database every night at midnight. So you would have written another cron job for that:

```cronjob
0 0 * * * python ~/ingest_from_database.py
```


And maybe you wanted to combine the transform API data with the data from the database. And so you would have written yet another cron job, maybe one that kicks off at 2 AM every morning so that it happens after the other jobs are complete. 

So that would look like:

```cronjob
0 2 * * * python ~/combine_api_and_database.py
```


And you could have written all the Cron jobs you needed to get the pipeline implemented, carefully timed to execute in a sequential manner. 


This is what's known as a **pure scheduling approach**, and this is how many data pipelines were automated before orchestration tools were available. 


In fact, this is still how many simple data pipelines are automated today. 
![[Screenshot 2025-12-19 at 19.26.38.png]]


The problem with a setup like this is that there are many ways in which you can fail. For example, if one task fails to run, or takes longer than expected, or produces some unexpected result, your entire pipeline can fail. 

And you would have essentially no way of knowing exactly how or why it failed without implementing tests and debugging to determine what went wrong. 
![[Screenshot 2025-12-19 at 19.25.13.png]]

Or even worse, since you don't have any built-in monitoring or alerts to tell you how things are going. You might not learn about the failure until your downstream stakeholders come to you asking why the data looks funny. 

And so why am I talking about scheduling with Cron if I'm not recommending it? Well, first off, it can be a nice, intuitive way of wrapping your head around what it means to automate your data pipelines. And a Cron job can work great for some simple tasks, like a data download that needs to happen on a regular basis and doesn't have any downstream dependencies. Or if you're in the prototyping phase and testing various aspects of your data pipelines, using Cron can be a quick and easy way to get started. 



## Evolution of Orchestration Tools

Orchestration has long been a key capability for data processing. But until the past decade or so, orchestration was really only accessible to the largest companies. And this is because open source or managed orchestration tools did not exist yet, and it was complicated and expensive to build your own in house solutions. In the late 2000s, things began to change. 
- Facebook developed a tool called **Data Swarm** for internal use, which they still use today. 
- Another tool called **Apache Oozie** became extremely popular in the 2010s. But it's designed to work within a **Hadoop cluster** and was more difficult to use in a more heterogeneous environment.
- Inspired by these earlier tools, particularly Data Swarm, Airbnb introduced **Airflow** in 2014, which has become the **industry standard orchestration tool.** 
- Today, there are many other orchestration tools under development and the landscape of orchestration will no doubt continue to evolve in the future. 

![[Screenshot 2025-12-19 at 19.41.40.png]]

 >	*Throughout these courses we have for the most part, actively avoided discussing any particular tool or technology too extensively. Instead, I have been aiming to focus on the skills and the knowledge that will be broadly applicable no matter where you work as a data engineer. In certain cases, however, I'm going to make an exception for a tool or technology that you will very likely use in your job as a data engineer. And one such tool is **"Airflow"**. Right now when it comes to orchestration, more teams are using Airflow than any other tool, and so it's a skill set that recruiters are looking for.* 

With that being said, Airflow is not without its shortcomings, and I'm excited about some of the other newer open source tools that are emerging in the orchestration space. And so I'll mention a few of those too. 

Airflow was developed by Maxime Beauchemin and other collaborators at Airbnb. They were primarily interested in serving their own internal data orchestration needs. However, from the very beginning, they built Airflow as a **noncommercial open source project** with the vision that the tools they were developing to serve Airbnb's internal needs would also be useful to other teams solving similar challenges. 

**The framework quickly grew significant mind share outside of Airbnb becoming an Apache incubator project in 2016 and a full Apache sponsored project in 2019.** 
![[Screenshot 2025-12-19 at 19.52.27.png]]
Today, Airflow offers many advantages as an orchestration platform, largely because of its dominant position in the open source marketplace. 

- Airflow is written in *Python*, making it accessible to almost any use case imaginable. 
- Beyond that, the Airflow open source project is very active with a high rate of commits and a quick response time for bugs and security issues. 
- Airflow is also available as a managed service through a number of vendors, including AWS, GCP, and astronomer.io for anyone who is looking for more comprehensive support. 

With all that being said, Airflow is certainly not the only orchestration tool out there. In terms of things like scalability, ensuring data integrity, and streaming pipelines, Airflow either does not solve for these issues or there are significant room for improvement. 
![[Screenshot 2025-12-19 at 19.53.33.png]]


Many other interesting open source orchestration projects exist, such as **Luigi** and **Conductor**, and newer tools like **Prefect**, **Dagster**, and **Mage** have been getting traction as they aim to mimic the best elements of Airflows core design while improving on it in key areas. ![[Screenshot 2025-12-19 at 19.53.48.png]]

Still other tools are focused on providing better orchestration support for streaming pipelines. I think it's entirely possible that one or more of these newer orchestration tools will become widely used in the coming years as alternatives to Airflow. It's also possible that one of these alternative tools could serve your needs better depending on what pipelines you're setting up. With that being said, my recommendation is to learn Airflow for now because Airflow is what many people are using today, but also keep learning about other tools and keep up on new developments in the field of orchestration so you can stay current as things continue to evolve. 


## Orchestration Basics

While setting up proper orchestration of your data pipelines does come with more operational overhead than civil Cron scheduling, it also comes with the option to set up dependencies, monitor tasks, get alerts, and create fallback plans if something doesn't go as expected. 
![[Screenshot 2025-12-19 at 20.26.23.png]]


When we looked at Cron scheduling in a previous video, we had a data pipeline that looked like this with data flowing in from two sources, going through some transformations, and eventually making its way to a data warehouse. 
![[Screenshot 2025-12-19 at 20.26.41.png]]

### DAG
As we've mentioned a couple of times already in these courses, the sort of visual representation of a data pipeline is referred to as a **"Directed Acyclic Graph"** or **"DAG"** for short. 
- Think of each task as a ***"node"*** in the graph. 
- Then, in graph terminology, you can call these arrows ***"edges"*** between the nodes. 
![[Screenshot 2025-12-19 at 20.30.05.png]]

You can see that data flows only in one direction between the nodes or tasks in the graph. So, there's an overall *sense of direction* to your data pipeline. There are no circles, and so this is what it means for the graph to be **directed** and **acyclic**. 


>	 *Remember, with Cron scheduling, we could set up a pipeline like this, but if one task took longer to run than expected and the next task kicked off before the previous one finished, it could break everything downstream.* ![[Screenshot 2025-12-19 at 20.30.32.png]]


With orchestration, this is where the idea of *"dependencies"* comes in. You could, for example, build in *dependencies between tasks* in this pipeline that **require** the previous task to finish before the next task starts. ![[Screenshot 2025-12-19 at 20.31.46.png]]


Most orchestration frameworks allow you to, and in fact, require you to define your data pipelines as **DAGs**. In many cases, they include a **user interface** where you can visualize your DAG, as well as debug, troubleshoot, and monitor your data pipelines. 

In Airflow, you will define your DAGs programmatically by writing python code that looks like the code below, to define all the tasks and dependencies in your data pipeline.
```Python
with DAG(
	dag_id="dag_etl_example", 
	start_date=datetime(year: 2024, month: 3, day: 23), 
	schedule='@weekly'
):
	task_ingest_api = PythonOperator(
						task_id='ingest_from_API', 
						python_callable=ingest_from_rest_api
					)
	task_ingest_db = PythonOperator(
						task_id='ingest_from_database', 
						python_callable=ingest_from_database
					)
	...
	
```

![[Screenshot 2025-12-19 at 20.37.50.png]]

You can visualize a pipeline you've defined using the Airflow UI, which looks like this. 
![[Screenshot 2025-12-19 at 20.38.07.png]]

- Here you can trigger the DAG to run. 
- Monitor the progress of tasks, 
- visualize the DAG you defined previously in code 
- and troubleshoot any issues. 
![[Screenshot 2025-12-19 at 20.38.51.png]]
![[Screenshot 2025-12-19 at 20.39.07.png]]

When you have defined your pipeline as a **DAG**, you can set up the dependencies or conditions on which the DAG should run. These conditions could be time based if you want to run the DAG on a particular schedule or event-based if you want to trigger the DAG based on an event. ![[Screenshot 2025-12-19 at 20.39.42.png]]


For example, here's how you could define a DAG in airflow that should run every day at midnight. *By setting the parameter scheduled to daily.* 
![[Screenshot 2025-12-19 at 20.40.01.png]]


To make your DAG event-based, you can use the same parameter schedule, but define it differently. More specifically, by defining the name of a dataset, you can schedule your DAG to run when the dataset is updated. 
![[Screenshot 2025-12-19 at 20.40.54.png]]


You could also make a portion of your DAG, wait for some **external process to complete**. For example, some external process is going to upload a CSV file to an S3 bucket, then you can set your DAG to **wait for the presence of that file in the S3 bucket**. 


Here's how you could define a **"task"** and airflow - called a "***sensor***" - that listens for a file upload event. 

![[Screenshot 2025-12-19 at 20.41.51.png]]

The portion of the DAG that starts with this task won't run until the `my_file.csv` is available in the S3 bucket. 


You can also set up **monitoring** and **logging** as well as **alerts** for the tasks in your DAG. 

For example, you might want to get an alert if a particular task fails or monitor how long a task takes to run. 
![[Screenshot 2025-12-19 at 20.42.33.png]]

You can also set up **data quality checks** along the way to ensure that the data flowing through your data pipeline meets your expectations. 

That might include checking for things like the null values or the range of some set of values or just verifying that the schema of the data you're ingesting is what you expect. 


