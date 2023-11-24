---
layout: post
title: "Data Vault Principles"
date: 2023-03-13 13:52:00 +0700
modified: 2023-11-24 13:02:00 +0700
description: Data Vault Architect Documentation
categories: blog
comments: true
tags: [datadev, course]
---

{% include _toc.html %}

## What is Data Vault ? How it helps in data architect system ?

- Data Vault 2.0 is a modeling methodology used in data warehousing to build flexible and scalable data architectures. It involves modeling the data warehouse as a series of hubs, links, and satellites that can be easily extended and modified as new data sources are added.

- Data Vault is a concept increasingly used for NoSQL databases where semi-structured and unstructured data is processed.

![alt text](/images/post/data-vault/data-vault-basic.png "Data Vault 2.0 by Microsoft")

### Key Concepts of Data Vault

- Data lake for staging purposes, loaded and ingested by Script, data pipeline and which is persist data.

- Due to sources are changing time over time, considered data lake is semi-structure and schema revolution.

- Information Marts matches the definition of data mart, that is defined by end-user and often modeled using dimensional model, or even flat-and-wide entity (fully denormalized entity).

- Data lake is functionally oriented and modeled by source system. on the other hand, information mart is modeled by information requirement ==> both layers are modeled independently from each other. It is fundamental for de-coupling and business changing.

- Because in reality, the L-shape of the Raw Data Vault is due to the fact that some of the enterprise data is good enough for reporting, a Business Vault entity only exists if a business rule needs to be applied.

- The similar L-shape of the data lake has other reasons: first, the data lake should have a dual-use. It’s not only used by the Data Vault team to build the data analytics platform, but also by the data scientists to develop more ad-hoc solutions.

- The message queue on top of the architecture diagram is not only used to capture real-time data but also to deliver real-time information.

## Data Vault Design Pattern with Azure Cloud

Azure provides several services that can be used to implement a Data Vault 2.0 architecture, including:

![alt text](/images/post/data-vault/data-vault.png "Data Vault Design")

1. Azure Data Factory: This service can be used to extract, transform, and load (ETL) data from various sources into a centralized storage location, such as Azure Data Lake Storage or Azure Blob Storage. Data Factory provides support for a wide range of data sources, including structured, semi-structured, and unstructured data.

2. Azure SQL Database: This is a fully managed relational database service that can be used to store the hub and link tables in a Data Vault 2.0 architecture. SQL Database provides built-in support for advanced security features such as row-level security and data masking.

3. Azure Analysis Services: This is a fully managed analytics engine that can be used to build and deploy business intelligence models based on the data stored in the hub and link tables. Analysis Services provides support for a wide range of data visualization tools, including Power BI.

4. Azure DevOps: This is a set of services that can be used to manage the development and deployment of the Data Vault 2.0 architecture. DevOps provides support for continuous integration and continuous deployment (CI/CD) pipelines, automated testing, and deployment monitoring.

## Conclusion

By using these Azure services, organizations can implement a highly scalable and flexible Data Vault 2.0 architecture that can easily accommodate changes in data sources and business requirements. Additionally, Azure provides advanced security features and compliance certifications that can help organizations meet their data security and privacy requirements.

**Important**
Technology stack should only serve as a blueprint for the platform.

## Reference

By reference, they provide us the blueprint for the platform.

1. Data lake zones and containers - Cloud Adoption Framework | Microsoft Learn

2. data-landing-zone/DataManagementAnalytics-Prerequisites.md at main · Azure/data-landing-zone · GitHub

3. data-product-batch/DataManagementAnalytics-Prerequisites.md at main · Azure/data-product-batch · GitHub

4. GitHub - Azure/data-product-streaming: Template to deploy a Data Product for data stream processing into a Data Landing Zone of the Data Management & Analytics Scenario (former Enterprise-Scale Analytics). The Data Product template can be used by cross-functional teams to ingest, provide and create new data assets within the platform.

5. GitHub - Azure/data-product-analytics: Template to deploy a Data Product for analytics and data science use-cases into a Data Landing Zone of the Data Management & Analytics Scenario (former Enterprise-Scale Analytics). The Data Product template can be used by cross-functional teams to create insights and products for external users.

6. GitHub - Azure/data-management-zone: Template to deploy the Data Management Zone of Cloud Scale Analytics (former Enterprise-Scale Analytics). The Data Management Zone provides data governance and management capabilities for the data platform of an organization.

7. Data agnostic ingestion engine - Cloud Adoption Framework | Microsoft Learn
