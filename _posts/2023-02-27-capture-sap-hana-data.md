---
layout: post
title: "SAP HANA - Capture Data Change using Lambda"
date: 2023-02-27 14:36:00 +0700
modified: 2023-11-24 13:02:00 +0700
description: Capture only data changes, delta data to improve performance
categories: Project
comments: true
tags:
  - data-engineering
  - data-pipeline
---
{% include _toc.html %}

## Definition

**CDC (Change Data Capture)** refers to capturing all the creates-updates-deletes on a table and making it available for downstream systems to do as required. There are three main types of performing CDC.

### 1. Log

Our CDC system will read directly from a database's transaction log in this method. A transaction log stores every change that happens in the database (every create, update, delete) and is used in case of a crash in the database to restore to a stable state. E.g., Postgres has WAL, MySQL has binlog, etc.

### 2. Incremental

In this method, our CDC system will use a column in the table to pull new rows. For this to work, the column must be ordered, e.g., incrementing key column or a updated timestamp column. It's not possible to detect deleted rows with this method. And depending on how frequently you pull, your pipeline may miss updates between pulls.

### 3. Snapshot

Our CDC system will pull the entire data in this method. Consider this as "select c1, c2, .. from the table" every time.

### 4. Methods

CDC ake Capture Data Change is available for ingesting latest data or last modified data in database, there are various types of data capturing such as using Rest API or SDK supported by Data Streaming Services. Here are some examples:

- Using Application: sends data over REST API using HTTP/HTTPS protocol
- Using SDK: AWS Kinesis Data Stream - KPL aka Kinesis Producer Library to send data to AWS Data Stream.
- Using ConnectionString - JDBC connection string: connect and send data to AWS Data Stream
- Using third-party libraries and services: AWS DMS, **Debezium** open source

In this work, we create In order to capture data change using lambda function.

## Using JDBC Connection

### 1. Execution sql file to capture data change

In order to capture data change in SAP HANA database, we need to execute SQL query to get the latest data, make sure that this query is optimized and return minimum value - only value needs to be captured in platform.

SQL Snippet:

```sql
SELECT *
FROM <TABLE_NAME>
WHERE OBJECTID IN (
    SELECT OBJECTID
    FROM "_SYS_REPO"."ACTIVE_OBJECT"
    WHERE OBJECT_NAME = '<TABLE_NAME>'
)
AND plnbez IN (
    SELECT RECORD_ID
    FROM <TABLE_NAME>_LOG
    WHERE LOG_TIMESTAMP BETWEEN '20230226' AND '20230227'
);

```

### 2. Execution python file to run

Python Snippet:

```python
import os
import boto3
import pyhdb

# Get environment variables
hana_host = os.environ['HANA_HOST']
hana_port = os.environ['HANA_PORT']
hana_user = os.environ['HANA_USER']
hana_password = os.environ['HANA_PASSWORD']
hana_schema = os.environ['HANA_SCHEMA']
s3_bucket = os.environ['S3_BUCKET']

# Connect to SAP HANA
connection = pyhdb.connect(host=hana_host, port=hana_port, user=hana_user, password=hana_password)
cursor = connection.cursor()

# Define SQL statement
sql_statement = f"SELECT * FROM {hana_schema}.MY_TABLE WHERE MODIFIED_TIME >= ADD_SECONDS(CURRENT_UTCTIMESTAMP, -60)"

# Execute SQL statement
cursor.execute(sql_statement)

# Get S3 client
s3 = boto3.client('s3')

# Store results in S3
s3.put_object(Body=cursor.fetchall(), Bucket=s3_bucket, Key='my-table-modifications.csv')

```

### 3. Put data to Kinesis Data Stream - KDS

Send data to Kinesis Data Stream for downstream consumption:

- AWS Lambda Function to get the events from KDS, process and store them into Realtime Analytical Service
- Kinesis Firehose to pushing data into S3 Bucket with pre-defined partitioning or Amazon Redshift

In here we can configure how many Kinesis Shades for scalability and how many days for data loss preventions or how data distributed and replicated to different Availability Zones for preventing data missing, etc.

```python
response = kinesis.put_record(
    StreamName='your_kinesis_stream_name',
    Data=data,
    PartitionKey=partition_key
)
```

## Using Debezium Library

### What is Debezium?

### How is works ?

### Design and implementation

Docker Containers:

```docker-compose
version: '3'
services:
  zookeeper:
    image: debezium/zookeeper:1.4
    ports:
      - "2181:2181"

  kafka:
    image: debezium/kafka:1.4
    ports:
      - "9092:9092"
    environment:
      ZOOKEEPER_CONNECT: zookeeper:2181
    depends_on:
      - zookeeper

  mysql:
    image: debezium/example-mysql:1.4
    container_name: mysql
    hostname: mysql
    environment:
      MYSQL_ROOT_PASSWORD: debezium
      MYSQL_USER: mysqluser
      MYSQL_PASSWORD: mysqlpw
      MYSQL_DATABASE: inventory
    ports:
      - "3306:3306"
    depends_on:
      - kafka

  connect:
    image: debezium/connect:1.4
    ports:
      - "8083:8083"
    environment:
      BOOTSTRAP_SERVERS: kafka:9092
      GROUP_ID: 1
      CONFIG_STORAGE_TOPIC: debezium-configs
      OFFSET_STORAGE_TOPIC: debezium-offsets
      STATUS_STORAGE_TOPIC: debezium-status
      CONNECT_KEY_CONVERTER: org.apache.kafka.connect.storage.StringConverter
      CONNECT_VALUE_CONVERTER: io.confluent.connect.avro.AvroConverter
      CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL: http://schema-registry:8081
    depends_on:
      - kafka
      - mysql
      - schema-registry

  schema-registry:
    image: confluentinc/cp-schema-registry:5.5.1
    ports:
      - "8081:8081"
    environment:
      SCHEMA_REGISTRY_KAFKASTORE_CONNECTION_URL: zookeeper:2181
      SCHEMA_REGISTRY_HOST_NAME: schema-registry
    depends_on:
      - zookeeper

```

mysql-connector config file

```json
{
  "name": "mysql-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "database.hostname": "localhost",
    "database.port": "3306",
    "database.user": "mysqluser",
    "database.password": "mysqlpw",
    "database.server.name": "mysql",
    "database.whitelist": "mysql",
    "topic.prefix": "debezium"
  }
}
```

Makefile:

```makefile
up:
	docker compose up -d --build

down:
	docker compose down -v

source-connect:
    curl -X POST -H "Content-Type: application/json" -d @connector-config.json http://localhost:8083/connectors

source-stop:
    curl -X DELETE http://localhost:8083/connectors/mysql-connector
```

## Conclusion

Using Debezium Connect and API trigger as the best way to capture a change of relational database, otherwise using Cloud Native Solutions like AWS DMS or JDBC SQL query or metadata capture. Here is only the simple example of how to use Debezium to capture a change of PostgreSQL and MySQL databases.
