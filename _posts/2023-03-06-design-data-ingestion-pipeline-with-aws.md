---
layout: post
title: "Design Data Ingestion Pipeline with AWS - Amazon Web Services"
date: 2023-03-06 09:34:00 +0700
modified: 2023-11-24 13:02:00 +0700
description: How to implement Data pipeline, from end to end
categories: blog
comments: true
tags: [datadev, course]
---
{% include _toc.html %}

Traditional Design of Data Ingestion Pipeline

## 1. Requirements Analysis

After the requirements analysis of application that I have made a draft version of how we design and plan for implementation as my analysis. I divide the analysis into two spaces.

### - Biz Space

To ensure down-stream can consume data from down-stream with consistency, completeness and trustable (follow data governance rules).

- Application process data from data sources and stored processed data into database
- Data has to be available every time in database
- Maximize downtime of data

### - Technical Space

In the following list are the technical requirements which are application should have and not only for Data engineering Team but also be involved by Dev/Sec Ops team.

- General: end to end pipeline for ingest – extract – transform – load data
- Reliability: application can recovery from failure by any reason
- Performance: for reading and process large data
- Scalability: scaling in inconsistency traffic of request
- Buffer: processed data should state in buffer loader before dump into DB (by time or by batch size)
- Data partition by datetime
- Prefer Cloud Native solution
- Prefer Serverless solution supports HA, Resilient
- Support Disaster Recovery – DR
- Backout workflow: backfill missing data – deduplicated data (optional)

### - Assumption

- Application pulls data
- Data sources is able DDoS
- Know the data encrypted in data sources
- Data connector and Networking set up is done

## 2. Architecture Design

Assume that we will only focus on implement application to handle ETL part with target to process large and inconsistency data.

### - Application Components & Requirement

The essential module in ETL that we need to be done are: - Ingestion: - Connect data source - Perform 10B req per day - Read data in payload - Backup raw data with partition - Extraction: - Decrypt data - Flatten data - Handle missing key – missing data - Apply schema constraints - Dynamic schema registry (optional) - Transformation: - Read data retrieved from Extraction - Send to processing function - Return transformed data - Loading: - Send data to buffer - Buffer async retrieves data and send to DB

### - Database Design

The data would be followed 2 main streams and we decide into 2 schemas for master control access and manage data in our storage (for now is database but future can be data warehouse or LakeLa-house).
Data should be partition into smaller group for optimizing performance during query data from consumers.

- Schema/Catalog
  - RAW: contents raw data with partition
  - ETL: contents transform data with partition
  - Audit: contents meta data of requests
- Data Format
  - Data will be store under optimal format for storage
  - Data should be compressed during transfer between modules
  - Data should be encrypted if sensitive information (PII, financial)
- Procedure/Job:
  - Data quality control process
  - Audit process
  - Backout process
- Structure:
  - isolated and partitioned by: data source / table / datetime /

## 3. Technology Considerations

### Infrastructure

I recommend we use the infrastructure provided by popular Cloud Providers (PaaS) based in following reasons:

- Less front-up cost, we just need to pay for what we used instead of spending money for hosting, configuring.
- Don’t have much dedicated Dev/Sec Ops or SysAdmin, don’t spend time and money to maintain them (update, monitoring, backup, especially security – Log4g is key lesson), have time for develop features/ optimization.
- As Application and System are bigger and more complicated, we can easily to scale and decoupling the infrastructure.
- Support for integrate with various modern and legacy systems.
- Automation of recovery from failure.
Pros of Cloud and Serverless are mentioned in above. However, we shall need to consider a Cons of those solution rather than bias on it. Cons of those solution are data leakage and limited control, Vender-lock.

## 4.Infrastructure Technology Considerations (assuming that we’re using AWS)

### Technology AWS

Infrastructure technology choice (assuming that we’re using AWS):

|Category|Technology|
|---------|---------|
|Core Application|Python Application running on AWS EC2 deployed by multi-AZs
|Scalable Data Transformation| AWS Lambda|
|Network and Security|AWS Security Group & IAM Role|
|Core Infrastructure|Terraform integrated with CICD pipeline with assume role preferred (Credential)|
|Streaming|AWS Kinesis Data Stream|
|Buffer|Kinesis Firehouse|
|Data Storage|S3 with partitioned structure instead of writing into RDMS with more |benefits: <br>- HA <br>- Scalability <br>- Compress/Encrypted <br>- Integration with multiple Query Engine Services
|Credential Storage|AWS Secret Manager|
|Cataloging|AWS Glue Service|
|Language|Python|

## 5. Design Architecture

With design and documentation from requirements and understanding, the design architecture here is based on the requirements and AWS Service Platform.

![alt text](/images/post/desing-data-pipeline/aws-design.png "AWS Data Pipeline Architecture")

## 6. Conclusion

Of designing and developing data pipeline architecture especially using AWS Data services, this is a careful consideration of business requirements and technical requirements, data sources and transformation processes. Data movement mechanism is based on what type of data source be ingested.

AWS provides variety of powerful tools and services for implementing robust data pipeline such as AWS Kinesis Data Stream, AWS Lambda, S3, CloudWatch, etc. A well-design data pipeline will ensure data quality and performance of data applications that help drive business and decision-making.

This is the typical data pipeline using AWS Data services, and is based on specific requirements but can be extended for any type of data source where we can replace a service, plug and place any of them by relevant one.

Please note that we are designing core components for data pipeline. Let me know your thought behind your designed data pipeline architecture.
