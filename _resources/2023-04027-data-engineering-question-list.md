## Data Engineering Questionnaire

1. What project are you working on ? Introduce your self
2. What is your main role ? such as design data pipeline ? => I am playing a Data Engineer role in project as providing high quality technical solution for data pipeline design and featuring in detail for development, integration, maintenance, operation.
   2.1. How do you deploy your data pipeline using CICD? Do you experience with any DevOps Stack likes Jenkin or Codecommit..
   2.2. Which Cloud Services used to work? Do you found any alternative solution or service and improve your tech debt ? Technical issue?
3. What kind of modeling technique you follow when design DWH ? => Star schema design for shaping dimensional model, follow new technique as Lake-house design.
4. What # of fact and dimension tables ? Why do we need 2 kind of tables => 5 fact tables and 7 dimension tables, because multi metric and dimension need to monitor, the metric divide by various segmentation.
5. What data in fact table ? Can you give me example of fact table ? => user click event table is a sample of fact.
6. Getting data from different sources, loading data to data lakes, how do you connect from cloud system to intranet ? => setup DX or VPC connectivity, setup gateway, set up proxy and security, setup networking and routing in VPC, hosting resources on VPC, provision resource in VPC with Connector to load data in intranet.
7. Have you heard about data gateway ? => where data is loading with encryption and decryption
8. You connect to data source, how to you extract data from source ? => depend on source system: API, DB, Shared file. ingesting new data with CDC.
9. How do you build data pipeline to identify the data change ? => 1. using checkpoint method to capture where the data has been copy, the checkpoint should: 1.last_updated_datetime to capture is there any changes on current data; 2.row_count( with nolock system) to capture any new data insert.
10. Do you use Data brick ? Yes, using to stream data from Kafka and Eventhub to Delta lake and Blob storage, include Databrick into Azure ADF for processing data, and create task job as scheduled.
11. Give me a query for a scenario: Table: Customer: Customer ID, Name
    Talbe 2: Order: Order ID, Customer ID, Order Date, Order Amount
    List of customer who has never place the order =>
    "select cust.customer_name
    from Customer cust
    left join Order ord on cust.customer_id = ord.customer_id
    where order_amount is null or order_date is null"
12. Company has many offices in different countries
    each office has employees
    data is fetched in different locations
    each office will provide file every month
    ==>how do you design ETL from different files ? File format will be same, data will be aggregated into single table, employee left organization will be marked => need to set up connection by the File shared location (propose to use Box because version control), adding plugin to integrate Box with data pipeline, create master_data for manage where the file is coming from and add this field to ingestion pipeline, but need to consider this solution because that will increase storage. Define physical and logical schema for destination dataset. Create a DAG which is includes all step to ingest file into DL, then extract it and tagging the source systems, loading data into RDBMS.
13. Which transformation when you do data pipeline ? => Streaming processing. ingesting data via API gateway, put data into streaming data buffer with relevant partition key for balancing loader and ingester. extract data as flatten format and send to buffer to delivery data into DL, create catalog and schema for ingestion and analysis and reference. Modeling data and creating SCD pipeline. Create dimensional model on Virtual DWH and data marts. Implement failing data handler and refill data handler. Adding metric to monitor data quality and data lineage. Create data security to encrypt data on DL. exposing data to BI tools or API gateway. Manage resources of data pipeline with IaC, integrate data pipeline with DevOps tools. automating data pipeline with DataOps process,
    ==> Give high level design data pipeline ?

14. Wide and narrow transformation spark
15. Spark optimization (https://medium.com/analytics-vidhya/4-performance-improving-techniques-to-make-spark-joins-10x-faster-2ec8859138b4)
16. Task, Stage, Job difference
17. CS fundamentals
18. GC in Python

19. Given order_tab (ord_id, name, time, qty, price), customer_tab (cust_id, name, ord_id)

    - total number of order in 2020, 2019
    - total customer has order in morning
    - top 3 customers paid in 2020

20. Move zero characters in array and create TC for it, run it on Python
21. What is data skewness distribution, how to process that data with big data
22. How can submit a spark job
23. How Spark perform an job in distributed nodes
24. What is distributed System ? Disk ? Compute ?
25. Approaching to data modeling
26. Data Tiering ? Data Lifecycle ?
27. Some way to ingest data
28. Azure Sercices ? Specifying Which resource to use for data integration, storage and modeling?
29. AWS Services ? Specifying which service to use Data Platform
30. DevOps Experience ? DataOps Experience ?
31. What is Data governance ? Data Quality and Validation ? Data Reconciliation ?
32. Basic Architecture Graphics for Data Platform or Data Applications ?
33. What and Why have caching and buffering enabled during data loading and data reporting process.
34.
