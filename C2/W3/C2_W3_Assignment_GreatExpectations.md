
# Testing Data Quality with "Great Expectations"

Lab Link: https://www.coursera.org/learn/source-systems-data-ingestion-and-pipelines/programming/w1wyQ/assignment-3-testing-data-quality-with-great-expectations/lab?path=%2F%3Ffolder%3D%2Fhome%2Fcoder%2Fproject


In this lab, you will work on generating some expectations and validations over a dataset in a PostgreSQL Database. For that, you will also interact with the different components of GX: Data Context, Data Sources, Expectations and suites, and Checkpoints.

```
MYSQL_CONNECTION_STRING: mysql+pymysql://admin:adminpwrd@de-c2w3a1-rds.ctw0yyaoarfd.us-east-1.rds.amazonaws.com:3306/taxi_trips
```


To initiate GX:
```bash
great_expectations init
```


This command initialized the **"data context"** object and created your *backend* stores, such as the **checkpoints**, **expectations**, **data_docs** and **validations** stores, *as local directories.*
```bash
GX
|-- great_expectations.yml
    |-- expectations
    |-- checkpoints
    |-- plugins
    |-- .gitignore
    |-- uncommitted
        |-- config_variables.yml
        |-- data_docs
        |-- validations
```


Let's configure these stores as `S3` buckets. For that, you are already provided with two `S3` buckets:

- `GXArtifactsS3Bucket`: you will use this bucket to store information about your expectations, validations and checkpoints;
- `GXDocsS3Bucket`: you will use this bucket to store your DataDocs, which are human readable documentations that contain reports on Expectations, Checkpoints and Validation results.


There are several advantages for storing your project metadata in an `S3` bucket. These advantages include:

- Accessibility: S3 buckets are highly accessible from various environments and by other team members or stakeholders.
- Scalability: You can continue storing your project metadata seamlessly as your metadata grows over time.
- Durability: Your metadata are reliably stored and protected.

### 1 - `great_expectations.yaml`

This YAML file represents the central configuration file used by GX, and contains various settings that control the behavior of your GX project. 

```yml

# Welcome to Great Expectations! Always know what to expect from your data.
#
# Here you can define datasources, batch kwargs generators, integrations and
# more. This file is intended to be committed to your repo. For help with
# configuration please:
#   - Read our docs: https://docs.greatexpectations.io/docs/guides/connecting_to_your_data/connect_to_data_overview/#2-configure-your-datasource
#   - Join our slack channel: http://greatexpectations.io/slack

# config_version refers to the syntactic version of this config file, and is used in maintaining backwards compatibility
# It is auto-generated and usually does not need to be changed.
config_version: 3

# Datasources tell Great Expectations where your data lives and how to get it.
# Read more at https://docs.greatexpectations.io/docs/guides/connecting_to_your_data/connect_to_data_overview
datasources: {}

# This config file supports variable substitution which enables: 1) keeping
# secrets out of source control & 2) environment-based configuration changes
# such as staging vs prod.
#
# When GX encounters substitution syntax (like `my_key: ${my_value}` or
# `my_key: $my_value`) in the great_expectations.yml file, it will attempt
# to replace the value of `my_key` with the value from an environment
# variable `my_value` or a corresponding key read from this config file,
# which is defined through the `config_variables_file_path`.
# Environment variables take precedence over variables defined here.
#
# Substitution values defined here can be a simple (non-nested) value,
# nested value such as a dictionary, or an environment variable (i.e. ${ENV_VAR})
#
#
# https://docs.greatexpectations.io/docs/guides/setup/configuring_data_contexts/how_to_configure_credentials


config_variables_file_path: uncommitted/config_variables.yml

# The plugins_directory will be added to your python path for custom modules
# used to override and extend Great Expectations.
plugins_directory: plugins/

stores:
# Stores are configurable places to store things like Expectations, Validations
# Data Docs, and more. These are for advanced users only - most users can simply
# leave this section alone.
#
# Three stores are required: expectations, validations, and
# evaluation_parameters, and must exist with a valid store entry. Additional
# stores can be configured for uses such as data_docs, etc.
  expectations_store:
    class_name: ExpectationsStore
    store_backend:
      class_name: TupleFilesystemStoreBackend
      base_directory: expectations/

  validations_store:
    class_name: ValidationsStore
    store_backend:
      class_name: TupleFilesystemStoreBackend
      base_directory: uncommitted/validations/

  evaluation_parameter_store:
    # Evaluation Parameters enable dynamic expectations. Read more here:
    # https://docs.greatexpectations.io/docs/reference/evaluation_parameters/
    class_name: EvaluationParameterStore

  checkpoint_store:
    class_name: CheckpointStore
    store_backend:
      class_name: TupleFilesystemStoreBackend
      suppress_store_backend_id: true
      base_directory: checkpoints/

  profiler_store:
    class_name: ProfilerStore
    store_backend:
      class_name: TupleFilesystemStoreBackend
      suppress_store_backend_id: true
      base_directory: profilers/

expectations_store_name: expectations_store
validations_store_name: validations_store
evaluation_parameter_store_name: evaluation_parameter_store
checkpoint_store_name: checkpoint_store

data_docs_sites:
  # Data Docs make it simple to visualize data quality in your project. These
  # include Expectations, Validations & Profiles. The are built for all
  # Datasources from JSON artifacts in the local repo including validations &
  # profiles from the uncommitted directory. Read more at https://docs.greatexpectations.io/docs/terms/data_docs
  local_site:
    class_name: SiteBuilder
    # set to false to hide how-to buttons in Data Docs
    show_how_to_buttons: true
    store_backend:
        class_name: TupleFilesystemStoreBackend
        base_directory: uncommitted/data_docs/local_site/
    site_index_builder:
        class_name: DefaultSiteIndexBuilder

anonymous_usage_statistics:
  enabled: True

```

Search for the `stores` key; you will find several subkeys associated with each type of store. Replace the sections of the YAML file that correspond to the expectation, validations and checkpoint stores with the following configurations. Make sure to replace the placeholder `<GX-ARTIFACTS-BUCKET>` with the corresponding bucket name in those sections. 

> 	*Note: the YAML file is indentation-sensitive, so make sure to keep the same indentation level when you replace the configuration information (otherwise you will get some errors when you run the commands).*


Search for the `stores` key; you will find several subkeys associated with each type of store. Replace the sections of the YAML file that correspond to the expectation, validations and checkpoint stores with the following configurations.


- Expectations store:
```yaml
  expectations_store:
    class_name: ExpectationsStore
    store_backend:
      class_name: TupleS3StoreBackend
      bucket: <GX-ARTIFACTS-BUCKET>
      prefix: expectations/
```

- Validations store:
```yaml
  validations_store:
    class_name: ValidationsStore
    store_backend:
      class_name: TupleS3StoreBackend
      bucket: <GX-ARTIFACTS-BUCKET>
      prefix: validations/
```


- Checkpoint store:
```yaml
  checkpoint_store:
    class_name: CheckpointStore
    store_backend:
      class_name: TupleS3StoreBackend
      suppress_store_backend_id: false
      bucket: <GX-ARTIFACTS-BUCKET>
      prefix: checkpoints/
```


Now, you will configure the storage for the documentation files Data Docs. In the same `YAML` file, search for the key `data_docs_sites` and replace the `local_site` subkey with the following configuration. Make sure to replace the placeholder `<GX-DOCS-BUCKET>` with the corresponding bucket name, using the name that is for the docs bucket not the artifacts bucket.

```yaml
  S3_site:
    class_name: SiteBuilder
    store_backend:
      class_name: TupleS3StoreBackend
      bucket: <GX-DOCS-BUCKET>
    site_index_builder:
      class_name: DefaultSiteIndexBuilder
```


To check that your stores have been properly configured, execute the following command in the terminal:
```bash
great_expectations store list
```

Output:
```terminal
coder@e5c42a05caf8:~/project$ great_expectations store list
5 active Stores found:

 - name: expectations_store
   class_name: ExpectationsStore
   store_backend:
     class_name: TupleS3StoreBackend
     bucket: de-c2w3a1-293974146222-us-east-1-gx-artifacts
     prefix: expectations/

 - name: validations_store
   class_name: ValidationsStore
   store_backend:
     class_name: TupleS3StoreBackend
     bucket: de-c2w3a1-293974146222-us-east-1-gx-artifacts
     prefix: validations/

 - name: evaluation_parameter_store
   class_name: EvaluationParameterStore

 - name: checkpoint_store
   class_name: CheckpointStore
   store_backend:
     class_name: TupleS3StoreBackend
     bucket: de-c2w3a1-293974146222-us-east-1-gx-artifacts
     prefix: checkpoints/
     suppress_store_backend_id: False

 - name: profiler_store
   class_name: ProfilerStore
   store_backend:
     class_name: TupleFilesystemStoreBackend
     base_directory: profilers/
     suppress_store_backend_id: True

```

This command lists the available Stores and shows information such as name, type, and location. Note that the Data Docs Sites is not listed by this command.

Now you will you use the GX components to set up the validation workflow, starting with the Data Context.


## 2 - Data Context

The **data context** serves as the entry point for the Great Expectations API, which consists of **classes** and **methods** that allow you to create objects to 
- connect to your data sources, 
- create expectations and 
- validate your data. 

So, using the data context, you can connect to the Data source, define your expectations, create a validator, run your checkpoints, and access the metadata of your Great Expectations project.

>	*The Data Context can be either: 
>	-> **ephemeral** - existing only in memory and not persisting beyond the current Python session
>	-> or **backed by the configuration files** - so that it can persist between Python sessions and can be saved for later usage (File Data Context). 

In this lab, GX was set up so that your data context is a File Data Context backed by an AWS S3 Bucket.*

Use the `get_context()` method of great expectations to load your Data Context.

```Python
context = gx.get_context()
```

Let's inspect the `context` object. 

```Python
context
```

You will see information about the stores you just configured:

```Output
{
  "anonymous_usage_statistics": {
    "usage_statistics_url": "https://stats.greatexpectations.io/great_expectations/v1/usage_statistics",
    "explicit_id": true,
    "data_context_id": "ae9bfc80-d19d-4396-88ba-c9e293679e78",
    "enabled": true,
    "explicit_url": false
  },
  "checkpoint_store_name": "checkpoint_store",
  "config_variables_file_path": "uncommitted/config_variables.yml",
  "config_version": 3.0,
  "data_docs_sites": {
    "S3_site": {
      "class_name": "SiteBuilder",
      "store_backend": {
        "class_name": "TupleS3StoreBackend",
        "bucket": "de-c2w3a1-293974146222-us-east-1-gx-docs"
      },
      "site_index_builder": {
        "class_name": "DefaultSiteIndexBuilder"
      }
    }
  },
  "datasources": {},
  "evaluation_parameter_store_name": "evaluation_parameter_store",
  "expectations_store_name": "expectations_store",
  "fluent_datasources": {},
  "include_rendered_content": {
    "globally": false,
    "expectation_validation_result": false,
    "expectation_suite": false
  },
  "plugins_directory": "plugins/",
  "stores": {
    "expectations_store": {
      "class_name": "ExpectationsStore",
      "store_backend": {
        "class_name": "TupleS3StoreBackend",
        "bucket": "de-c2w3a1-293974146222-us-east-1-gx-artifacts",
        "prefix": "expectations/"
      }
    },
    "validations_store": {
      "class_name": "ValidationsStore",
      "store_backend": {
        "class_name": "TupleS3StoreBackend",
        "bucket": "de-c2w3a1-293974146222-us-east-1-gx-artifacts",
        "prefix": "validations/"
      }
    },
    "evaluation_parameter_store": {
      "class_name": "EvaluationParameterStore"
    },
    "checkpoint_store": {
      "class_name": "CheckpointStore",
      "store_backend": {
        "class_name": "TupleS3StoreBackend",
        "suppress_store_backend_id": false,
        "bucket": "de-c2w3a1-293974146222-us-east-1-gx-artifacts",
        "prefix": "checkpoints/"
      }
    },
    "profiler_store": {
      "class_name": "ProfilerStore",
      "store_backend": {
        "class_name": "TupleFilesystemStoreBackend",
        "suppress_store_backend_id": true,
        "base_directory": "profilers/"
      }
    }
  },
  "validations_store_name": "validations_store"
}
```


## 3 - Data Source

The next step is to configure your Data Source. In  this lab, you are provided with a MySQL database labeled as `taxi_trips` which contains a sample of the [TLC trip record data set](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page). The database contains one table named `trips`, which has the following schema:

![[Screenshot 2025-12-19 at 12.12.43.png]]

**<span style="font-size:18px">3.1.</span>** Now that you have a general understanding of the data schema you will use, let's connect to the database. 

Using the `context` object, you can connect to a SQL database using the method: 

```Python
context.sources.add_sql()
``` 

This method expects:
- a **name** for your data source (which can be of your choice) and 
- a ***connection** string* that consists of the database credentials that are needed to establish a connection to the database. 
	- GX supports passing Data Source [connection credentials](https://docs.greatexpectations.io/docs/0.18/oss/guides/setup/configuring_data_contexts/how_to_configure_credentials/) through environment variables or setting them through the GX configuration files. 
	  
	  In this lab, you will set the `config_variables.yml` configuration file. 


Open the `gx/uncommitted/config_variables.yml` file and at the end of the file, append the output that you got from the execution of the script located at `scripts/setup.sh`. 

Append it in the following way:

```yaml
MYSQL_CONNECTION_STRING: mysql+pymysql://<DBUSER>:<DBPASSWORD>@<DBHOST>:<DBPORT>/<DBNAME>
```

Save changes. Then, run the following cell to create the data source object.

```Python
# Create the data source to represent the data available in the MySQL DB
mysql_datasource = context.sources.add_sql(
    name=f"{LAB_PREFIX}-db-datasource", connection_string="${MYSQL_CONNECTION_STRING}"
)
```


3.2 -- Let's now define a Data Asset from your data source. Remember that a Data Asset represents *collections of records stored within a Data Source*. It could be a table in a SQL database or a file in a file system. It could be also a query asset that joins data from more than one table or it could be a collection of files matching a particular regular expressions pattern. 

In other words, by defining your data asset, you tell GX on **which part of your data you want to focus on.**

Since the given database consists of only the `trips` table, you will create a `Table Data Asset` from your data source using the method `add_table_asset`. This method expects a name for the data asset (in this case, we used the lab prefix followed by the table name) and the actual name of the table in the source database.

Run the following cell to create your data asset.
```Python
# Add a Data Asset to represent a discrete set of data
trips = mysql_datasource.add_table_asset(
    name=f"{LAB_PREFIX}-trips", table_name="trips"
)
```


## 4 - Batch Request

The next thing you need to create is the Batch Request, which represents the primary way to retrieve data from your data asset. It can retrieve your data asset: 
- as a single batch 
- or as multiple batches. 
In this lab, you will define your data asset as batches based on the `vendor_id` column.


**<span style="font-size:18px">1.</span>** To create batches on your Table Data Asset `trips`, call the method `add_splitter_column_value()` on your data asset, and pass the `"vendor_id"` column as the splitter column. 

   *Note*: GX offers several ways to split your data, according to different conditions.

**<span style="font-size:18px">2.</span>** Create the batch request, using the `build_batch_request()` method of your Table Data Asset.

**<span style="font-size:18px">3.</span>** To inspect the batches, get the batches using the `get_batch_list_from_batch_request()` method of your Table Data Asset. This method expects as input the batch request. You can inspect the information about each batch, such as the Table Data Asset name, the type of splitter used, and the batch identifier according to the column selected as the splitter.

##### __Expected Output__ 

```json
{'type': 'table', 'data_asset_name': 'de-c2w3a1-trips', 'table_name': 'trips', 'schema_name': None, 'batch_identifiers': {'vendor_id': 1}, 'splitter_method': 'split_on_column_value', 'splitter_kwargs': {'column_name': 'vendor_id'}}
{'type': 'table', 'data_asset_name': 'de-c2w3a1-trips', 'table_name': 'trips', 'schema_name': None, 'batch_identifiers': {'vendor_id': 2}, 'splitter_method': 'split_on_column_value', 'splitter_kwargs': {'column_name': 'vendor_id'}}
{'type': 'table', 'data_asset_name': 'de-c2w3a1-trips', 'table_name': 'trips', 'schema_name': None, 'batch_identifiers': {'vendor_id': 4}, 'splitter_method': 'split_on_column_value', 'splitter_kwargs': {'column_name': 'vendor_id'}}
```


Now, let's create a batch request list for each of the batches generated before.

```Python
batch_request_list = [batch.batch_request for batch in batches]
```



## 5 - Expectation Suite and Validator
### Expectation Suite

In order to define expectations for your data, you need to create an Expectation Suite which is a collection of expectations or assertions about your data. 

1. Use the `add_or_update_expectation_suite()` method of the `context` object to create a new `Expectation Suite`. Pass the name stored in the variable `expectation_suite_name` to the parameter `expectation_suite_name` in the same method.


```Python
# Add an expectation suite name to the context
expectation_suite_name = f"{LAB_PREFIX}-expectation-suite-trips-taxi-db"

context.add_or_update_expectation_suite(expectation_suite_name=expectation_suite_name)

```
##### __Expected Output__ 

```json
{
  "expectation_suite_name": "de-c2w3a1-expectation-suite-trips-taxi-db",
  "ge_cloud_id": null,
  "expectations": [],
  "data_asset_type": null,
  "meta": {
    "great_expectations_version": "0.18.22"
  }
}
```


### Validator

In GX, a Validator is the component responsible for validating your data against your expectations. 
- You can directly interact with the validator to *manually* validate your data. 
- OR you can *streamline the validation process* using checkpoints. 

For now, let's directly interact with the validator to explore the manual process of validating your data.

1. Instantiate the validator by calling the `get_validator()` method of the `context` object; store it in the `validator` variable. Make sure to pass the following parameters:

* `batch_request_list` as the list with batch requests you generated previously, which is stored in `batch_request_list`.
* `expectation_suite_name` as the Expectation Suite name you used in the previous cell.

```Python
validator = context.get_validator(
    batch_request_list=batch_request_list,
    expectation_suite_name=expectation_suite_name,
) 
```

### Setting the Expectations

Now you have the expectation suite and the validator objects both ready. **But you still did not define any expectations for your data**. 

> 	*An Expectation is a **statement** about your data that can be validated, serving to improve data quality and facilitating clearer communication about data features. Similar to assertions in Python unit tests, Expectations offer a descriptive language for specifying the conditions the data should meet. However, unlike conventional unit tests, GX applies these Expectations directly to your data rather than to code.* 

There are several types of expectations, such as

* `expect_column_values_to_not_be_null`
* `expect_column_values_to_be_unique`
* `expect_table_row_count_to_be_between`
* `expect_column_values_to_be_between`

And so on. You will use only a pair of those expectations for this lab. You can also check the [Expectations Gallery](https://greatexpectations.io/expectations/) to see the available expectations depending on the type of Data Source you are using.

```Python
validator.expect_column_values_to_not_be_null(column="pickup_datetime")
validator.expect_column_values_to_not_be_null(column="passenger_count")
validator.expect_column_values_to_be_between(column="congestion_surcharge", min_value=0, max_value=1000)
```

##### __Expected Output__ 

*Note*: The actual values in the output may change.

```json
{
  "success": true,
  "result": {
    "element_count": 96,
    "unexpected_count": 0.0,
    "unexpected_percent": 0.0,
    "partial_unexpected_list": [],
    "missing_count": 0.0,
    "missing_percent": 0.0,
    "unexpected_percent_total": 0.0,
    "unexpected_percent_nonmissing": 0.0
  },
  "meta": {},
  "exception_info": {
    "raised_exception": false,
    "exception_traceback": null,
    "exception_message": null
  }
}
```

This corresponds to the output of the *last batch* that the validator has taken. Don't worry, GX actually has validated the other batches but only shows the output of the last one.

Run the following cell to save your Expectation Suite to the S3 bucket (expectation store), so you can use the expectations you just defined in another session.

```Python
validator.save_expectation_suite(discard_failed_expectations=False)
```

You can inspect your artifacts bucket. Inside the `expectations/` folder you will find a `json` file named as your Expectation Suite (`de-c2w3a1-expectation-suite-trips-taxi-db`). If you download it, you will see the expectations you have added, which will look as follows:

```json
{
  "data_asset_type": null,
  "expectation_suite_name": "de-c2w3a1-expectation-suite-trips-taxi-db",
  "expectations": [
    {
      "expectation_type": "expect_column_values_to_not_be_null",
      "kwargs": {
        "column": "pickup_datetime"
      },
      "meta": {}
    },
    {
      "expectation_type": "expect_column_values_to_not_be_null",
      "kwargs": {
        "column": "passenger_count"
      },
      "meta": {}
    },
    {
      "expectation_type": "expect_column_values_to_be_between",
      "kwargs": {
        "column": "congestion_surcharge",
        "max_value": 1000,
        "min_value": 0
      },
      "meta": {}
    }
  ],
  "ge_cloud_id": null,
  "meta": {
    "great_expectations_version": "0.18.9"
  }
}
```


## 6 - Creating the Batch Requests and the Validations List

Now in a production environment, you can't manually validate your data the way you did in the previous exercise. 

What you can instead do, is to: 
- load into your new environment the expectation suite that you stored, 
- then pass it with your batch requests 
- to a Checkpoint object. 
  The checkpoint will automatically create a validator to validate your data against your expectations. 

A `checkpoint` object expects a *collection of data batches* and their corresponding expectation suite. We will call this collection a **validations** list. So before you create the checkpoint object, let's first create the validations list.

First, create a `batch_request` from your Data Asset as you did previously. Call the `build_batch_request()` in your Data Asset and store it in the variable `batch_request`. Then, create the `batches` from your Data Asset by using the `get_batch_list_from_batch_request()` method and passing the `batch_request` as a parameter.

```Python
# Build the batch request
batch_request = trips.build_batch_request() 

# Create your batches using the batch_request from the previous cell
batches = trips.get_batch_list_from_batch_request(batch_request)
```

For the expectation suite, run the following cell to retrieve the Expectation Suite's name from the context object. In this case you only have one expectation suite that you can access from the list of Expectations Suite returned by the `list_expectation_suite_names()` method.

```Python
expectation_suite_name = context.list_expectation_suite_names()[0]
```

