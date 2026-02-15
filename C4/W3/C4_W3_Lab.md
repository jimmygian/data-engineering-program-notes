
# Week 3 Lab:  Capture Data Change with Flink and Debezium

In this lab, you will implement a streaming pipeline with the intention of capturing data changes from a source system and delivering those changes in real time to a target system.

## 1 - Introduction

***Change data capture (CDC)*** refers to a *software process or design pattern* that can detect **changes** to a source system (such as a database or data warehouse), as you can keep track of changes in near real-time, you can implement processes that achieve data consistency in data sources down the line of the source.

A requirement has come through for your team to implement a CDC system from a source MySQL database to a target RDS database running Postgres. The rest of the team has set up a prototype of the architecture in an EC2 instance, the missing configuration and Flink pipeline have been left for you to develop and finally test the architecture.


## 2 - CDC Infrastructure

![[Screenshot 2026-02-15 at 21.13.29.png]]

The prototype has mostly been set up using a multi-container approach inside an EC2 instance, you can connect with the EC2 instance using an *SSH tunnel*. Here is a brief description of the components:

- **Source Database:** You already interacted with the source database in previous labs. For the prototype, the team set up a MySQL database inside a Docker container with the `classicmodels` database.
    
- **Debezium:** Distributed platform for `CDC`, it connects to the source system and captures the changes based on the configuration one setup. The UI has been provisioned with the `8080` port of the EC2 instance.
    
- **Apache Kafka:** The **event streaming platform**, has already been configured to connect with Debezium, based on your configuration in Debezium topics are created for each table that contains messages with each change.
    
- **Apache Zookeeper:** Service that stores configuration information and allows the coordination of cloud applications.
    
- **Apache Zeppelin:** Web-based notebook environment, it allows us to run `Flink` in a notebook alongside other possible interpreters. You can access the UI by connecting to the `8085` port of the EC2 instance, you can also see the Flink UI with the `8086` port.
    
- **Apache Flink:** Distributed processing engine for data streams, you will use it to connect to the Kafka topics and update the target database accordingly.
    
- **Target Database:** For the prototype, the team set up a Postgres database in an RDS instance with the target schema `classicmodels_star_schema`.


