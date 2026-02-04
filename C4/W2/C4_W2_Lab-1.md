# Week 2 Lab: Feature Engineering for ML

In this lab, you will apply ***feature engineering*** to transform a dataset and make it ready for training a Machine Learning (ML) model. You will perform basic transformations for the numerical and categorical variables or features, preparing the data to train a recommender system. 

### 1.1 - Introduction to Feature Engineering

A **feature** is a numerical representation of a phenomenon. Choosing or creating the most appropriate features is a process called **Feature Engineering**. Feature Engineering is a crucial step in the data preparation process for machine learning and data analysis. It involves the creation, transformation, and selection of features (variables, attributes, or columns) from raw data to improve the performance of machine learning models. Effective feature engineering can significantly enhance the predictive power of the model.

In this lab, you will implement feature engineering in three steps: 

- **Data extraction**: Collecting data and then extracting the required dataset to train the ML model. This involves connecting to different data sources and handling large data volumes to extract the initial dataset.
- **Feature creation**: This involves the creation of numerical features from the initial dataset. Some examples are:
    - *Data labelling*: Designating labels to each record to later train a model to predict the label on new data.
    - *Text embeddings*: Creating vector embeddings to represent text data as vectors from which an ML model can learn. 
    - *Categorical data encoding*: Encoding categorical data to a numerical representation.
- **Feature storage**: After the data is cleaned and transformed, the final dataset should be stored in a system that the ML team has access to, where they can explore the dataset and run the training for the ML model. Here you will split the final dataset into a training and testing dataset and store each in an S3 bucket.

You will reexamine the assignment for Course 1 Week 4, in which you built a comprehensive pipeline to feed a Machine Learning model, specifically a content-based filtering recommender system based on the `classicmodels` database. Here you will be provided with the model and the scripts required to train it, but you will be modelling and implementing the required transformations to deliver the datasets for training and testing the ML model. In this lab, you will replicate steps of the pipeline using just this notebook, mainly for testing the logic of a machine learning pipeline in just one place.

## 2 - Data Extraction

As a first step in the process of feature engineering, you will create a new base dataset for the ML model. You will extract the required data from different tables and, based on that initial dataset, add more features or improve the ones that were selected. You will use the schema from the transformed table to create a new table. An AWS Glue job was used for that in Course 1 Week 4 Assignment, but for the prototype, you will do the required transformations in this notebook.