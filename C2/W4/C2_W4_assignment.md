This time you will support the Machine Learning team in building a Machine Learning pipeline for three fictitious Mobility-As-A-Service vendors (Alitran, Easy Destiny, and ToMyPlaceAI). 

This pipeline consists of **preprocessing** and **validating** **data** to *train a model* to estimate the ride duration and decide if the model is suitable for deployment based on training metrics. The continuous training and evaluation of the model will allow each vendor to improve the ride duration estimation service.

**After this assignment you'll be able to:**

- Implement a DAG using Taskflow API.
- Use Great Expectations to perform quality checks in your DAG.
- Employ the `BranchPythonOperator` within a DAG to determine the path of the workflow based on a condition.
- Construct multiple dynamic DAGs based on configuration files.


## 1 - The DAG Details

In this assignment, 
- you will implement a DAG to validate data, train and evaluate a model, and decide to deploy it or notify low performance. 
- Then you'll generalize this DAG as a **"template"** to create Dynamic DAGs for different vendors.

This is the DAG diagram which you will be using:
![[Screenshot 2025-12-21 at 14.08.41.png]]

The following are the descriptions of the tasks that constitute the DAG:

- `start`: an empty task marking the start of the DAG. It doesn't include any behavior.
- `data_quality`: triggers a data quality check on the data using the `@task.virtualenv` decorator from the TaskFlow API.
- `train_and_evaluate`: trains and evaluates a model to estimate ride duration
	- *returns* the **performance** metric.
- `is_deployable`: decides whether to deploy a model based on the model performance.
- `deploy`: prints a deployment message to simulate the deployment process of the model.
- `notify`: prints a notification message to simulate the notification for a low-performance model.
- `end`: an empty task marking the end of the DAG. It doesn't include any behavior and will be executed with the `DummyOperator`. However, here the default value `all_success` of the parameter `trigger_rule` will be changed to `none_failed_or_skipped`. The rule specifies that the end task should be triggered if **at least one upstream task has succeeded**. But it's okay if some upstream tasks fail, as long as not all of them do.

>	***Note:** For educational purposes, the proposed DAG uses pandas, SciPy, Great Expectations, and NumPy to handle the data within the Airflow instance. This is not desirable in real-life Airflow pipelines for the following two reasons:*
>		*1. Airflow should interact with storage and processing solutions using operators specifically designed for them*
>		*2. Airflow should only be an orchestrator, and it should delegate the actual processing workload of the pipeline to the appropriate tools such as databases or Spark clusters.*


## 4 - Creating the DAG and its Tasks

### 4.1 - Checking Data Quality

Data quality is the measure of the suitability of data to be used for its intended purpose. It is usually thought of as comprising several [dimensions](https://www.collibra.com/us/en/blog/the-6-dimensions-of-data-quality "https://www.collibra.com/us/en/blog/the-6-dimensions-of-data-quality"), with the most common being:

- **Accuracy:** The level to which data reflects the real-world scenarios and events and can be confirmed through verifiable sources.
- **Completeness:** The degree to which the data carry all of the desired information.
- **Consistency:** The absence of conflicts between versions of the same data stored in different locations.
- **Validity**: Adherence of the data to an expected set of rules, which may come from physical, mathematical, statistical, or business-related restrictions.
- **Uniqueness:** The absence of duplicate or overlapping values within a single copy of the data.
- **Timeliness:** The ability to have the data available and sufficiently up-to-date when it is to be used.

A tool that allows you to solve these problems is **Great Expectations** (GX), which was covered in Week 3 of this Course.

![[DataQualityDimensions.pdf]]