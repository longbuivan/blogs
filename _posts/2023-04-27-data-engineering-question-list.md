---
layout: post
title: "Data Engineering Question List"
date: 2023-04-27 09:34:00 +0700
modified: 2023-11-24 13:02:00 +0700
description: Question used for interview
categories: blog
comments: true
tags: [datadev, course]
---

{% include _toc.html %}

In order to unblock and get impressed Interviewer as well as revise data engineering knowledge, this list or question is good source to walk through and practice.

## Questionnaire

### 1. What is your main role ? such as design data pipeline ?

A Data Engineer role in project as providing high quality technical solution for data pipeline design and featuring in detail for development, integration, maintenance, operation.

#### 1.1. How do you deploy your data pipeline using CICD? Do you experience with any DevOps Stack likes Jenkin or Codepipeline?

Data engineer or Data developer are working as software developer with Agile culture, applying Scrum activities in daily basis, we understand that the data pipeline is developed and deployed using CICD. Code and changelog are pushed to Code Repository with accordingly functionality of codebase. The code changes would require to be tested and verified before merge into production.

For more details, the data pipeline is structured as follows structure:

* `src`: contains the source code of the data pipeline
* `resources`: contains the resources used by the data pipeline
* `infra`: contains the IaC to provision the data pipeline
* `test`: includes the test functions
* `docs`: places the documentation
* `poetry`: places the environment, development dependencies \* `.workflow`: where defined CICD workflow is used

#### 1.2. Which Cloud Services used to work? Do you found any alternative solution or service and improve your tech debt ? Technical issue?

In order to use Cloud Services for data project, consider providers of cloud services is most important and associated with to enterprise point of view. With Data Landscape, the data pipeline must be aligned with what existing platforms have been developed.

For only data pipelines point of view, consider the service which are suitable for data application with criterial likes:

* Scalability
* Resilient
* Efficient

There are several different services available, but microservices is a pattern that should be considered when designing and implementing the application for ore easier to de-coupling and debugging as well as providing the ability to maintain easily.

### What kind of modeling technique you follow when design DWH ? => Star schema design for shaping dimensional model, follow new technique as Lake-house design

Designing Data Model is the most important thing and requires a lot of technique, including business logic. Specifically for creating data warehouse with more than 10 years of **Kimball** and **Immon** DWH toolkit. The technique of designing data warehouse are:

#### Approaching to data modeling

* Top-Down: where you have business, object defined that data warehouse is built for splitting to data-marts
* Bottom-Up: where you are not sure with your data and use cases of where data can help to answer the question by building data warehouse from data-marts

### What # of fact and dimension tables ? Why do we need 2 kind of tables?

Deeply understand dimensional data model, facts tables and relevant dimension tables are built, because multiple metrics and dimension need to monitor and figure out data insight, fact table is understood as the metric divided by various segmentation.

Dimensional data model is fundamental concept to step into multi-dimensional data models - aka (CUBE). Supporting to analysis of data models with different angles of magnitude.

There are 5 types of data models operation: roll-up, drill-down, rotate, slice, dice.

### Getting data from different sources, loading data to data lakes, how do you connect from cloud system to intranet ?

Setup DX or VPC connectivity, setup gateway, set up proxy and security, setup networking and routing in VPC, hosting resources on VPC, provision resource in VPC with Connector to load data in intranet.

### Have you heard about data gateway ?

Where data is loading with data quality gate is front of destination to control the output/input of data.

Data Quality Dimensions are understood and had been implemented to check data quality: Timeliness, Uniqueness, Validity, Consistency, Accuracy, Completeness.

### You connect to data source, how to you extract data from source ?

depend on source system: API, DB, Shared file. ingesting new data with CDC.

### How do you build data pipeline to identify the data change ?

Using checkpoint method to capture where the data has been copy, the checkpoint should: 1.last_updated_datetime to capture is there any changes on current data; 2.row_count( with nolock system) to capture any new data insert.

### Which transformation when you do data pipeline ?

#### Streaming processing

Ingesting data via API gateway, put data into streaming data buffer with relevant partition key for balancing loader
Extract data as flatten format and send to buffer to delivery data into DL, create catalog and schema for ingestion and analysis and reference
Modeling data and creating SCD pipeline.
Create dimensional model on Virtual DWH and data marts.
Implement failing data handler and refill data handler.
Adding metric to monitor data quality and data lineage.
Create data security to encrypt data on DL.
Exposing data to BI tools or API gateway.
Manage resources of data pipeline with IaC, integrate data pipeline with DevOps tools.
Automating data pipeline with CICD.

#### Batching and micro-batch processing

Data reconciliation is done when data had migrated from on-prem to cloud storage
Handling data was changing during the migration
Process Dimension data loading process


### Give high level design data pipeline ?

There are 5-tiers as good as data pipeline should follows is:

* Data Sources
* Data Backend tier
* Data Warehouse tier
* Data OLAP/CUBE tier
* Data Frontend tier

### Data Loading - Data Capture

Depending on the data source and data availability requirements, the data will be loaded and refreshed as needed.

Type of data loads is determined by:

* Full Load: loading small data (ex: dimensions, master data, etc)
* Delta Load: transactional data

Engineering Technique to load data:

* Incremental: split data into smaller chunks
* Snapshot: snapshot data and merge into destination

### Ensure Data Quality by using set of quality dimensions

Data Quality is key factor used to evaluate the quality of data
Data Quality Dimensions are understood and had been implemented to check data quality: Timeliness, Uniqueness, Validity, Consistency, Accuracy, Completeness.

### What and Why have caching and buffering enabled during data loading and data reporting process.

### Implementing Change Data Capture

### Spark optimization - Data processing optimization

### DAG Design in data pipeline: Task, Stage, Job difference

### What is distributed System ? Disk ? Compute ?

### Data Tiering ? Data Lifecycle ?

### Some way to ingest data

### Azure Services ? Specifying Which resource to use for data integration, storage and modeling?

### AWS Services ? Specifying which service to use Data Platform

### DevOps Experience ? DataOps Experience ?

### What is Data governance ? Data Quality and Validation ? Data Reconciliation ?

### Basic Architecture Graphics for Data Platform or Data Applications ?

## Coding Example

### Give me a query for a scenario: Table: Customer: Customer ID, Name

   Talbe 2: Order: Order ID, Customer ID, Order Date, Order Amount
   List of customer who has never place the order =>
   "select cust.customer_name
   from Customer cust
   left join Order ord on cust.customer_id = ord.customer_id
   where order_amount is null or order_date is null"

### Given order_tab (ord_id, name, time, qty, price), customer_tab (cust_id, name, ord_id)

    - total number of order in 2020, 2019
    - total customer has order in morning
    - top 3 customers paid in 2020