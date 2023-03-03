---
layout: post
title: "SAP HANA - Capture Data Change using Lambda"
date: 2023-02-27 14:36:00 +0700
categories: work
---

In order to capture data change using lambda function.

## Project Structure

### 1. Execution sql file to capture data change

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

### 3. Put data to kinesis

```python
response = kinesis.put_record(
    StreamName='your_kinesis_stream_name',
    Data=data,
    PartitionKey=partition_key
)
```

## Conclusion
