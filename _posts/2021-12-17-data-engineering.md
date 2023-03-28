---
layout: post
title: "Data Engineering"
date: 2021-5-5 01:09:00 +0700
categories: blog
---

This topic will discuss about who is data engineer in data platform and what they do for bring the data available for users. How to be come data engineer with needed skills.

## Data Engineer Scope

- Design and building system for collecting, storing and analyzing data at scale.
- The data engineering organization have the ability to collect massive amounts of data, ensure the data is highly reachable by data scientists and analyst.
- Build, test and maintain data pipeline architectures.
- Collaborate with management to understand objectives of data, align collected data with business needs.
- Create new data validation methods and data analysis tools.
- Ensure compliance with data governance and security policies.

## Data Engineering Skills

- Most of the task of data engineer is focus on development, many data engineers start of as software engineers and preferred to have the software development skills because eventually data pipeline for data tools are just a software.
- In advance, the data engineer can move into managerial roles or become a data architect, solution architect or **machine learning engineer** who bring data data and model to production.
- To develop your skill as data engineer, preferred skill are:
  - Coding: Very common languages are SQL, Python, I prefer you can learn Scala.
  - Relational and non-relational databases: mandatory skill for data engineering, and most of solution are working with both of them in a data pipeline.
  - ETL or ELT system: understand the process and technique of moving data from sources into a single repository, like a warehouse.
  - Automation and scripting: a necessary part of working with data lineage that's making data pipeline running well automatically and healthy, scripting to automate repetitive tasks
  - Machine learning: understand the needs of data scientists, making the gap and quality of data better
  - Cloud computing: for better scalability, high availability, new world comes to Cloud, it MUST have for data engineer to move data to Cloud from On-Premise systems even it is on another Cloud.
  - Data security: while most of the company have dedicated data security team, many data engineer are still tasked with securely managing and storing data to protect it from loss or theft.
  - A lot of skill are mentioning in _exposing-data-engineer_ post, including: DevOps, SecOps, CloudOps, DataOps.

### Learning Path and Development Road-map
<!-- insert image -->
![alt text](/images/post/de-roadmap.png "Data Engineering Roadmap")

## Data Engineer in Data platform

- Data Modeling: Learning to create relational and NoSQL data model to fit the diverse needs of data consumers.
  - Data modeling with [Postgres](https://www.postgresql.org) for relational database
  - Data modeling with [Apache Cassandra](https://cassandra.apache.org/_/index.html) for NoSQL database
- Cloud Data Warehouse: create data warehouse with cloud solution with understanding of data infrastructure with Amazon Web Services ([AWS](https://aws.amazon.com/console/))
  - Infrastructure as Code ([IaC](https://en.wikipedia.org/wiki/Infrastructure_as_code))
  - Database infrastructure
  - Populate data into data warehouse
- Big data and Data Lakes: understand the big data ecosystem and how [Spark](https://spark.apache.org) helps in the working with massive datasets.
  - Processing big data with big data framework
  - And storing big data in a data lake with queryable with Spark
- Data pipeline with Automatic tasks:
  - schedule, automate and monitor data pipelines with DAG frameworks such as [Airflow](https://airflow.apache.org).
  - Run data quality checks, track data lineage, release data pipeline to production.

## How do we start with Data Engineer Project from Scratch ???

- The most question from beginner was being How can we start a Data Engineering Project and Where we can be ? Understand my question from many years ago. I do have a summary and list out features where we can start, consolidating a Road-map shall help people like me can catch up Data Engineer with very fundamental skills to advanced skills.
- Here are my Documentation of Feature Engineering
