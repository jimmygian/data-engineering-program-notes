# Week 4 Lab 1:<br>Airflow 101 - Building your First Data Pipeline

LINK to lab: https://www.coursera.org/learn/source-systems-data-ingestion-and-pipelines/ungradedLab/HDAf4/practice-lab-1-airflow-101-building-your-first-data-pipeline/lab?path=%2F%3Ffolder%3D%2Fhome%2Fcoder%2Fproject
  

In this lab, you will build your first DAG in Airflow using Python operators,
visualize it in the Airflow UI and troubleshoot errors in the DAG. You will use a
[dockerized](https://docs.docker.com/) version of Airflow running on an AWS EC2 instance.

In this lab, you will implement the following DAG:
![[Screenshot 2025-12-20 at 15.23.07.png]]

To orchestrate this new feature to extract the information about users and sessions each day, you need to create, in a Python script, the Airflow DAG by defining its tasks using Airflow operators and clarifying their dependencies.