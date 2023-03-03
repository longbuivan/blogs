---
layout: post
title: "Data Migration Validation"
date: 2022-01-25 11:05:00 +0700
categories: work
---
# Data Migration Validation

In nowadays, a lot of companies and cooperation are preparing and migrating On-premises data on Legacy system to new system with high scalability, reliability and resiliency as core tenets of Data Platform.
There are some high-lighted items to be addressed for validating migrated data.

## Problem

1. Data need to be rekey for new logic.
2. Capture full data with new schema mapping.
3. Define Slowly Changing if the Legacy system still inputs new data to migrated table.

## Test Cases

1. Row count validation to capture if there is full data migrated.
2. Duplicated records count to verify if the same record is captured.
3. Check full records changing during migration if any data changed.

## Environment set-up

- Spark Nodes: AWS EMR
- Workspace: Jupiter Notebook
- PySpark Kernel Setup with

  > `sc` to check the current status

  > `%%info` to verify the current configuration

## Implementation

### 1. Import and Config Logging

```python
# Lib and Var
import json
import logging

from pyspark.sql.functions import countDistinct, concat_ws, sha2

DESTINATION_LOCATION = 's3://stage_location/stream_output/'
TARGET_LOCATION = 's3://public_location/data/parquet/'
```

### 2. Set global variable

```python
S3_LOCATION = {
    'event-d': {
        'stage_s3': STAGE_S3_LOCATION + 'event_d/',
        'public_s3': PUB_S3_LOCATION + 'event_d/',
    },
    'eventtype-d': {
        'stage_s3': STAGE_S3_LOCATION + 'eventtype_d/',
        'public_s3': PUB_S3_LOCATION + 'eventtype_d/',
    },
    'page-d': {
        'stage_s3': STAGE_S3_LOCATION + 'page_d/',
        'public_s3': PUB_S3_LOCATION + 'page_d/',
    },
    'site-d': {
        'stage_s3': STAGE_S3_LOCATION + 'site_d/',
        'public_s3': PUB_S3_LOCATION + 'site_d/',
    },
    'componentapplication-d': {
        'stage_s3': STAGE_S3_LOCATION + 'componentapplication_d/',
        'public_s3': PUB_S3_LOCATION + 'componentapplication_d/',
    },
    'confirmationtype-d': {
        'stage_s3': STAGE_S3_LOCATION + 'confirmationtype_d/',
        'public_s3': PUB_S3_LOCATION + 'confirmationtype_d/',
    },
    'geocode-d': {
        'stage_s3': STAGE_S3_LOCATION + 'geocode_d/',
        'public_s3': PUB_S3_LOCATION + 'geocode_d/',
    },
    'optimizely-d': {
        'stage_s3': STAGE_S3_LOCATION + 'optimizely_d/',
        'public_s3': PUB_S3_LOCATION + 'optimizely_d/',
    }
}


TABLE_PK = {
    'event-d': 'eventkey',
    'eventtype-d': 'eventtypekey',
    'page-d': 'pagekey',
    'site-d': 'sitekey',
    'componentapplication-d': 'componentapplicationkey',
    'confirmationtype-d': 'confirmationtypekey',
    'geocode-d': 'geocodekey',
    'optimizely-d': 'optimizelyidkey'
}
```

- Set the current data location
- Composite key to identify unique record for particular table

### 3. Code Implementation

- Row count checking

```python
def _comparing_row_count_dim_data(S3_LOCATION):
    table_conf = S3_LOCATION
    try:
        for table in table_conf:

            stage_loc = table_conf[table]['stage_s3']
            public_loc = table_conf[table]['public_s3']

            stage_df_count = spark.read.parquet(stage_loc).count()
            public_df_count = spark.read.parquet(public_loc).count()
            if stage_df_count == public_df_count:
                print(f'Table {table} matching between public and stage: {stage_df_count} = {public_df_count}')
            else:
                print(f'Table {table} doesn\'t matching between public and stage: {stage_df_count} != {public_df_count}')
    except Exception as e:

        print(f'Error with {table} with {e}')
```

- Duplicated record checking

```python
def _comparing_dup_dim_data(S3_LOCATION, TABLE_PK):
    table_conf = S3_LOCATION
    table_pk = TABLE_PK
    try:
        for table in table_conf:

            stage_loc = table_conf[table]['stage_s3']
            public_loc = table_conf[table]['public_s3']

            table_key = table_pk[table] #create table PK

            stage_df_count = spark.read.parquet(stage_loc).groupBy(table_key).count().count()
            public_df_count = spark.read.parquet(public_loc).groupBy(table_key).count().count()
            if stage_df_count == public_df_count:
                print(f'Table {table} matching between public and stage with distinct PK: {stage_df_count} = {public_df_count}')
            else:
                print(f'Table {table} doesn\'t matching between public and stage with distinct PK: {stage_df_count} != {public_df_count}')
            table_key = ''
    except Exception as e:

        print(f'Error with {table} with {e}')
```

- Field Comparison for every-single values by every-single records

```python
# Field Comparison
def _read_dim_data(table):
    table_name = table
    table_conf = S3_LOCATION
    try:
        stage_loc = table_conf[table_name]['stage_s3']
        public_loc = table_conf[table_name]['public_s3']
        stage_df = spark.read.parquet(stage_loc)
        public_df = spark.read.parquet(public_loc)
        LOGGER.debug(f'Done: Read {table_name} table in stage and public')
        return stage_df, public_df
    except Exception as e:
        print(f'Error with {table} with {e}')



def _field_compare(S3_LOCATION):
    table_conf = S3_LOCATION
    for table in table_conf:
        stage_df , public_df = _read_dim_data(table)
        # process dataframe
        public_df_hash = public_df.withColumn('cols_hash', sha2(concat_ws('_', *stage_df.columns), 256))
        stage_df_hash = stage_df.withColumn('cols_hash', sha2(concat_ws('_', *stage_df.columns), 256))
        LOGGER.debug(f'Done: Add hashing column for {table} table in stage and public')

        # inner join
        matching_count = stage_df_hash.join(public_df_hash, stage_df_hash.cols_hash == public_df_hash.cols_hash, 'inner').count()
        LOGGER.debug(f'Done: Counting inner join between stage and public')
        print(f'Total count of different record: {matching_count} in table {table}')
```

## Conclusion

This step is very important and can be considered as gating for release to Production when migrating data from legacy system.
The notebooks is linked in repo for detail reference.
