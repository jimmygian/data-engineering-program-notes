
**Normalized Model**

In the lab, you are provided with a PostgreSQL database that contains the `classicmodels` dataset you’ve seen in the previous courses. The dataset has the following normalized schema and it is defined in the database under a schema labeled "_classicmodels_".

![[Screenshot 2026-01-31 at 15.27.03.png]]

**Designing the star schema model**

Suppose you want to serve the modeled data to a data analyst who is interested in 
- **comparing sales across different locations** and 
- **assessing employee sales performance** by analyzing the customer purchases they facilitated.

Recall that you can follow these 4 steps to design the star_schema model:
- Select the Business process.
- Declare the Granularity of your data.
- Identify the Dimensions Tables.
- Identify the Facts.


1. Based on the scenario presented, what would be an **appropriate business process** to model to support the data analyst?
   
Since the data analyst wants to analyze sales across different locations and assess employee sales performance, an appropriate business process to model would be the company's sales transactions.

2. Assuming that you are modeling the sales transaction business process, what would be an appropriate grain (i.e. level of granularity) to model your data?

When designing a star schema, you want to select what is called the **atomic grain**, which refers to the most detailed level at which data is captured by a given business process. So if you're modeling sales transaction data, you want to model **the individual product item** in a sales transactions. A customer can place an order containing one ore more product items. The “orderdetails” table contains information for each product item placed within each order.  You can identify each item using the “orderNumber” and the “productCode”, or you can use the "orderNumber" and the "orderLineNumber". In the lab, you will use the set of “orderNumber” and “orderLineNumber”.

3. Assuming that you are modeling sales transactions, based on the scenario presented, what would be appropriate dimension tables to model?

Given what the data analyst is interested in analyzing, you can design the dimension tables to provide information about the customers, and the employees who served the customer, their branches/offices, and the products sold. You can always add the date dimension. In the lab, you’ll create the following dimension tables:

```MySQL

SELECT 
    {{dbt_utils.generate_surrogate_key(['customerNumber'])}} as customer_key, 
    customerName as customer_name,   
    contactLastName as customer_last_name, 
    contactFirstName as customer_first_name, 
    phone as phone, 
    addressLine1 as address_line_1, 
    addressLine2 as address_line_2, 
    postalCode as postal_code, 
    city as city, 
    state as state, 
    country as country,
    creditLimit as credit_limit
FROM classicmodels.customers

SELECT 
    {{ dbt_utils.generate_surrogate_key(['productCode']) }} as product_key, 
    productName as product_name, 
    products.productLine as product_line, 
    productScale as product_scale, 
    productVendor as product_vendor,
    productDescription as product_description, 
    textDescription as product_line_description
FROM classicmodels.products
JOIN classicmodels.productlines ON products.productLine=productlines.productLine

SELECT
    {{ dbt_utils.generate_surrogate_key(['employeeNumber']) }} as employee_key,
    lastName as employee_last_name, 
    firstName as employee_first_name, 
    jobTitle as job_title, 
    email as email
FROM classicmodels.employees

SELECT 
    {{ dbt_utils.generate_surrogate_key(['officeCode']) }} as office_key, 
    postalCode as postal_code, 
    city as city, 
    state as state, 
    country as country, 
    territory as territory
FROM classicmodels.offices

SELECT 
    {{ dbt_utils.generate_surrogate_key(['orders.orderNumber', 'orderdetails.orderLineNumber']) }} as fact_order_key,
    {{ dbt_utils.generate_surrogate_key(['customers.customerNumber']) }} as customer_key, 
    {{ dbt_utils.generate_surrogate_key(['employees.employeeNumber']) }} as employee_key,
    {{ dbt_utils.generate_surrogate_key(['offices.officeCode']) }} as office_key,
    {{ dbt_utils.generate_surrogate_key(['productCode']) }} as product_key, 
    orders.orderDate as order_date,
    orders.requiredDate as order_required_date, 
    orders.shippedDate as order_shipped_date,
    orderdetails.quantityOrdered as quantity_ordered, 
    orderdetails.priceEach as product_price
FROM classicmodels.orders
JOIN classicmodels.orderdetails ON orders.orderNumber = orderdetails.orderNumber
JOIN classicmodels.customers ON orders.customerNumber = customers.customerNumber
JOIN classicmodels.employees ON customers.salesRepEmployeeNumber = employees.employeeNumber
JOIN classicmodels.offices ON employees.officeCode = offices.officeCode

```

In the lab, you will also learn how to configure global variables in the dbt_project.yml file. You will define the name of the source schema (classicmodels) as a global variable and use it in your SQL queries. So in the lab, you will replace "classicmodels" in line 14 with

` {{var("source_schema")}}`


# Data Modeling with DBT

During this week's assignment, you will learn how to model a dataset based on two multi-dimensional data models such as the Star Schema and One Big Table (OBT).

## 1 - Introduction and Setup

Data modeling is one of the pillars of Data Engineering, it involves organizing bits of data into defined models with their respective data types and relationships between each other. Most of the work in data modeling involves using predefined techniques or patterns on a raw dataset based on the business's requirements. Data models like the **Star Schema** and **One Big Table (OBT)** have become popular for analytical workloads in recent years. In this lab, you will apply these models to the `classicmodels` dataset.

### 1.1 - Initiating **dbt 101** Project

**dbt** is a transformation workflow command line tool based on SQL, it consists of a compiler and a runner. A user writes `dbt` files and then can invoke `dbt` to run these files on the data warehouse of their choice. The compiler converts the `dbt` files into raw SQL scripts and runs them for you.

Let's start a `dbt` project.

Run the following command in the terminal to check that `dbt` Core is installed.

```bash
dbt --version
```

```shell
~/project$ dbt --version
Core:
  - installed: 1.8.1 
  - latest:    1.11.2 - Update available!

  Your version of dbt-core is out of date!
  You can find instructions for upgrading here:
  https://docs.getdbt.com/docs/installation

Plugins:
  - postgres: 1.8.1 - Update available!
  - redshift: 1.8.1 - Update available!

  At least one plugin is out of date or incompatible with dbt-core.
  You can find instructions for upgrading here:
  https://docs.getdbt.com/docs/installation

```

You will get a message that updates are available, do not worry about that.


<span style="font-size:16px"><b>1.1.2.</b></span> Initiate the `classicmodels_modeling` project with the `init` command:

```bash
dbt init classicmodels_modeling
```

Select the `postgres` database by pressing `1` and then `Enter` when prompted to. After that you will be prompted to enter other values, but you should quit that with `Cmd + C` or `Ctrl + C` as you will configure the rest of the connection details later. You should see a new folder, `classicmodels_modeling`.

Copy the `packages.yml` file to the project folder by running the following command in the terminal:

```shell
cp ./scripts/packages.yml ./classicmodels_modeling/
```

```yml
packages:
  - package: dbt-labs/dbt_utils
    version: 1.2.0
  - package: calogica/dbt_date
    version: [">=0.9.0", "<0.10.0"]
```


<span style="font-size:16px"><b>1.1.5.</b></span> Navigate into your project's directory and run the following command from the `classicmodels_modeling` folder to fetch the latest stable versions of tools and libraries specified in the `packages.yml` file.

```shell
dbt deps
```

Open the main configuration file for the project [`./classicmodels_modeling/dbt_project.yml`](classicmodels_modeling/dbt_project.yml). Go through the comments in that file to understand its content.

```yml

# Name your project! Project names should contain only lowercase characters
# and underscores. A good package name should reflect your organization's
# name or the intended use of these models
name: 'classicmodels_modeling'
version: '1.0.0'

# This setting configures which "profile" dbt uses for this project.
profile: 'classicmodels_modeling'

# These configurations specify where dbt should look for different types of files.
# The `model-paths` config, for example, states that models in this project can be
# found in the "models/" directory. You probably won't need to change these!
model-paths: ["models"]
analysis-paths: ["analyses"]
test-paths: ["tests"]
seed-paths: ["seeds"]
macro-paths: ["macros"]
snapshot-paths: ["snapshots"]

clean-targets:         # directories to be removed by `dbt clean`
  - "target"
  - "dbt_packages"


# Configuring models
# Full documentation: https://docs.getdbt.com/docs/configuring-models

# In this example config, we tell dbt to build all models in the example/
# directory as views. These settings can be overridden in the individual model
# files using the `{{ config(...) }}` macro.
models:
  classicmodels_modeling:
    # Config indicated by + and applies to all files under models/example/
    example:
      +materialized: view
```

### 1.2 - Source Configuration

When developing with `dbt Core`, `dbt` connects to your data warehouse using a profile, which is a `YAML` file with all the connection details to your warehouse. You are going to use a Postgres database.

```yml
classicmodels_modeling:
  target: dev  
  outputs:
    dev:
      type: postgres
      threads: 1
      host: <DATABASE_ENDPOINT>
      port: 5432  # optional
      dbname: postgres
      schema: classicmodels
      user: postgresuser
      password: adminpwrd    
```

Copy that file inside `~/.dbt/` and then test connection:

```shell
dbt debug
```

Load the connection configuration into the notebook with the following cell:

```python
import yaml

with open("./scripts/profiles.yml", 'r') as stream:
    data_loaded = yaml.safe_load(stream)
    
DBCONFIG = data_loaded["classicmodels_modeling"]["outputs"]["dev"]
DBHOST = DBCONFIG["host"]
DBPORT = int(DBCONFIG["port"])
DBNAME = DBCONFIG["dbname"]
DBUSER = DBCONFIG["user"]
DBPASSWORD = DBCONFIG["password"]
db_connection_url = f'postgresql+psycopg2://{DBUSER}:{DBPASSWORD}@{DBHOST}:{DBPORT}/{DBNAME}'

%sql {db_connection_url}
```

## 2 - Modeling

Inside the `classicmodels_modeling` project folder, you have the `models` folder, which contains an example of a model definition. Let's explore it.

