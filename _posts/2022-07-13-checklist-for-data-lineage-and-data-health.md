---
layout: post
title: "Check List for Data Lineage and Data Health during Data Platform Implementation"
date: 2022-07-13 11:21:00 +0700
categories: Blog
comments: true
tags:
  - data-engineering
  - data-lineage
---
{% include _toc.html %}
# Introduction
When designing and developing data platforms, good data is more important than large data and Data Lineage & Data Health are the techniques that architects & engineers often use to control data quality from pre-processing to post-processing. Applying Data Lineage and Data Quality in an effective way will significantly eliminate the **hard-core** 5Vs character in Big Data.

## Data Lineage

**Providing interactive Data Lineage will help you to:**

- Easily find and **discover** datasets
  - Search to find datasets using connection, table, and column names
  - Click through the data objects to forward-tracing from source to object or backward-rolling from object to source.
- Explore pipelines through a **well-interface**
  - View attributes of a group of tables at once
  - Visualize your pipeline status through coloring (e.g. color out-of-date tables)
  - Drill into details about your data such as its schema, when it was last built, owner, last changed
- **Collaborate** with teammates
  - Create pipeline snapshots to share with other users.

## Data Health

- Is an important service that monitors and alerts on common issues across datasets.
- Data Health comes with pre-built checks for potential issues regarding dataset status, time, size, content, and schema.
- In the event of a failed check, Data Health sends in-platform notifications and emails to notify you about the failure.

#### **Health in Data Platform**

- **Health of a Dataset** <br>
  With capability to view dataset, you can navigate to the Health Check to add new checks, modify existing checks and view historic check results. (Null, Max/Min, Variance)

- **Health across a Pipeline** <br>
  Shows the health checks and their statuses for all datasets in the lineage. Datasets can be colored by their health check status.

- **Health across the Platform** <br>
  To see an overview of health checks for all datasets, all pipeline, we will able to filter/sort dataset by conditions (project/pipeline/owner,...).

#### **Type of Heath Check**

- _Job-Level Checks vs Build-Level Checks_

  **Job**: In common processing engine framework, the engine split the job into multiple stages and tasks that help to monitor entry job or particular stage in job. Define proper LOGGING and EXCEPTION HANDLER will helps to monitoring the lineage.
  <br>
  
  **Build**: a collection of jobs with defined target datasets

  - **Build duration**: it calculates how long the dataset was built (new/refresh).
  - **Build status**: it shows how current status of the dataset for every single build

- _Freshness Checks._

  - _Time since last updated_: this evaluates **freshness of the dataset**. It calculates how much time has elapsed between the current time and the last transaction committed, even data in the dataset is not changed.
  - _Data freshness_: this evaluates **freshness of the data in the dataset**. It calculates how much time has elapsed between the last transaction committed and the maximum value of a timestamp column. This check is only run when a transaction is committed.
  - _Sync freshness_: this evaluates **freshness of the data in the synced dataset**. It calculates how much time has elapsed between the time of the latest sync of a dataset against the maximum value of a datetime column.

Finally, For both data and sync freshness, it is required that the timestamp in the column represents the time when the rows were added in the source system.

#### **Discussion**

Can I abort builds when Health Checks fail?
Most of the standard health checks depend on jobs to finish in order to compute and have a **checkpoint** for saving state of build and being recovered in the next build either automatic way or manual way.
