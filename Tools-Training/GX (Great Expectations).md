
## GX (Great Expectation) - Core Components
![[Screenshot 2025-12-17 at 17.06.12.png]]

***Data quality*** is whatever it takes to make it so that that thing works reliably consistently, and you know the results are trustable. Depending on the data system, it could be pretty different.

A big part of the **DevOps** movement is you need to have *reliable tests* for anything that you're building in software. If it's more than a prototype, you can't guarantee that it's going to continue to work unless you have the right infrastructure around to do automated testing. We did the same thing for data. Each expectation makes it so that you can assert something about the data that should be true at a specific moment in a specific place in a pipeline.

Great Expectations is an open-source tool that does exactly that and it's super flexible. You can deploy it in lots of different places.

---

**Great Expectations (GX)** enables you to define expectations for your data and to automatically validate your data against these expectations. It can also notify you of any inconsistencies detected, and you can use it to validate your data at any stage of your data pipeline. You can find GX documentation [here](https://docs.greatexpectations.io/docs/home).

---

### Great Expectations Workflow

The general workflow of Great Expectations is:
- You start your workflow by **specifying the data that you wish to test**. 
- Then, you **define the expectations** or the tests that you want to perform on the data, 
- Finally, you **validate your data against your expectations**. 

To implement such a workflow, you need to interact with the core components of Great Expectations, which consist of the following: 
- Data Context, 
- Data Sources, 
- Expectations, 
- Checkpoints.

![[Screenshot 2025-12-17 at 17.08.27.png]]
You use these components to access, store, and manage the objects and processes that are needed in your workflow. 


>	Here are the steps of a typical Great Expectations workflow:

#### Data Context
To start your workflow, you first **instantiate a "data context" object**. 
![[Screenshot 2025-12-17 at 17.16.28.png]]
- A `data_context` object serves as the **entry point** for the Great Expectations **API**, which consists of classes and methods that allow you to create objects to connect to your data sources, create expectations, and validate your data. 
- Using the `data_context`, you can *configure* and *access* the properties (objects, metadata) of your Great Expectations project. 


#### Data Sources

##### *Data Source Object*
After you instantiate your **data context** object, **you need to declare your *"data source"* object, which tells *great expectations* from where to get the data that you want to validate.**![[Screenshot 2025-12-17 at 17.24.35.png]]
- The source of data could be a SQL database, a local file system, an S3 bucket, or even a pandas data frame. 

##### *Data Asset Object*
After you connect to the desired data source, you need to tell great expectations **which part of that data you need to focus on.** 

You do that by declaring from the data source your _**"data assets"**_.
![[Screenshot 2025-12-17 at 17.25.25.png]] 
- A data asset is a *collection of records within a data source*. 
	- It could be a table in a SQL database or a file in a file system. It could also be a query asset that joins data from more than one table, or it could be a collection of files matching a particular regular expressions pattern. 

##### *Batches*
You can partition the data in your asset to **batches**. 
![[Screenshot 2025-12-17 at 17.27.20.png]]
- For example, if your data asset represents the records that correspond to the sales of a given year in a table, you could partition the records into monthly batches and validate each batch. 
- Or you could partition your data with respect to the store ids.
- You could also work with all the records of your data asset as one batch for you to retrieve the batches of your data asset. 


##### *batch_request Object*
Whether it's one batch or multiple batches, you always need to create a **"batch_request"** object for your data assets. 
- Batch requests are the *primary way to retrieve data from the data asset*, and it's what you need to **provide** for the rest of Great Expectations components. 
![[Screenshot 2025-12-17 at 17.29.02.png]]

### Expectations
After creating your batch_request object off of your data_assets, you need to define your expectations. 

An *"**expectation**"* is a statement that you can use to verify if your data meets a certain condition. 
![[Screenshot 2025-12-17 at 17.29.29.png]]
- For example, you can define an expectation to check if a column does not contain `null` values. 
- You can *define your own expectation* or use one of the available statements from the *expectation gallery*. For example:
	- *expect_column_min_to_be_between*
	- *expect_column_values_to_be_unique*
	- *expect_column_values_to_be_null*
	These are all examples of tests that you can *directly* use.
	 ![[Screenshot 2025-12-17 at 17.30.06.png]]You'll see how you can call them in the workflow example that we'll work on. 

##### Expectation Suite
You can also define **more than one expectation** for your data asset and collect them all in an ***"expectation suite"*** object. 
![[Screenshot 2025-12-17 at 17.32.38.png]]

#### Checkpoints
Now, to validate your data, you need to create a *"**validator**"* object which expects a batch request and its corresponding expectation suite. 
- You can manually validate your data by interacting directly with the validator, 
- or you can *streamline* the validation process by using a ***"checkpoint"*** object. ![[Screenshot 2025-12-17 at 17.33.17.png]]
- A checkpoint takes a *batch_request* and an *expectation_suite* and automatically provides them to a *validator* object which **generates the validation results**. 

#### Expectations Stores
Throughout this process, *metadata* about your project will be generated and *Great Expectations* will save it in some ***"backend stores"***. Great Expectations supports different types of stores. The most common stores are: 
- the "**Expectation Store**", where you can find your expectation suites. 
- the "**Validation Store**", where you can find information about the objects generated when you validate data against the expectation suite. 
- the "**Checkpoint Store**", where you can find your checkpoint configurations. 
- and a "**Data docs Store**", where you can find reports on expectations, checkpoints, and validation results. 

![[Screenshot 2025-12-17 at 17.35.11.png]]
You can access these stores and their settings through the ***"data_context"*** object. 




## Resources

**Coursera**
Core Components Video: https://www.coursera.org/learn/source-systems-data-ingestion-and-pipelines/lecture/UCX9J/great-expectations-core-components

Workflow Concept Video: https://www.coursera.org/learn/source-systems-data-ingestion-and-pipelines/lecture/Uljms/great-expectations-workflow-example

