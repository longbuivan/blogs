---
layout: post
title: "1 HOUR for setting data pipeline"
date: 2023-02-23 13:07:00 +0700
categories: blog
---

## Know about the data pipeline

Nowadays, we have modern data stack and buzzwords comes with a lot of challenges and open source projects that help to resolve the problems of big data also data platforms. In order to resolve the problems what we need to do is to understand the current data pipeline concepts and identify the area where we are working on the possible improvement and adopt the changes.

Below is a diagram of the data pipeline concepts that we are working now:
![alt text](/images/post/set-up-pipeline/pipeline-concept.png "Data Pipeline Concept")

Like a title of this article, we will walk through how to set up the data pipeline from end to end with core components in the pipeline. We will also understand and know how and know why we need to do this.

The data pipeline is based on the following core components and following components around the core components with more flexibility and ease of replacement. Remember that "Technology is always changed but the core concepts are always there".

Using Cloud Services, it would be possible to configure and set up the data pipeline within an hour. However this post is assumed that you have fundamental knowledge about Cloud Services such as Virtual Machine, Network, IAM role and permissions, and specific Data processing services which are depended on by your application and Cloud platform.

**Microsoft Azure** provides all of these components and makes it easy to configure and possibly. Here are steps by steps for setting up the data pipeline on Azure Cloud.

## Follow Steps - Keep that in mind

**1. Set up Budget**
Monitor your spending and receive notifications when your spending exceeds the budget limit. You can also make adjustments to your budget at any time if needed.
On Azure Portal, you can set up the budget alerts on Cost management and notification will be sent to your email id if the cost of your spend is over the maximum allowed.
**2. Data Connector**
In order to connect your data sources, you need to create a connection between your data sources and your data pipeline. We need to provide the connector the host information and data sources information while connecting through data pipeline. The credentials of data sources are required to store on secret management - Key Vault.
Here we are using Azure Integration Runtime for connecting various data sources with built-in scaling and and security features.
**3. Data Pipeline**
Setting up a data pipeline can be a complex process, but it is an essential part of any data-driven organization. With the right tools and strategies, you can create a robust, reliable, and scalable pipeline that meets your specific data integration and processing needs.
Once you have the connector, on Azure Data Factory, we should create a data pipeline with the right configuration and custom by use cases.
**4. Data Processing**
Once the data is ingested to Data lake, of Databricks functionality and Spark built-in data processing engines, data will be processed as a framework. This process will be executed in parallel and advanced tranformations will help data be cleaned and ready for downstream processing.
**5. Data Modeling**
Using Synapse data models with Spark pool for adhoc analysis and modeling that help to governing data as model and create semantic models.
**6. Data Visualizing**
80 % of problems will be resolved by data visualization. wiht Power BI service and desktop, data will be populated into charts and business driven
**7. Alerting**
There are a lot of alerting functions available in pipeline, system, account,... In this article we are mentioned about Cost and Data Pipeline issues. But in real world, there are many different ways to alert an issues happened in whole system. We can discuss more details in very soon topic separately.
**8. Deploy**
With CICD process, it helps the deployment more easy and reliable with the combination of Azure DevOps and Terraform/Azure Resource Management Template. We can deploy whole system within few minutes to an hour and resources will be managed as a code.
 ## Work on Data Pipeline Design with Azure Cloud

In here, we replace the component in data pipeline with Azure Cloud Services.
![alt text](/images/post/set-up-pipeline/pipeline-design.png "Data Pipeline Concept")

Using Azure Portal, it is possible to configure and set up the data pipeline but as long as you have familiar with DevOps stacks lake Azure DevOps, GitHub Actions, Azure Resource Manager Templates or especially Terraform. We can create a CICD pipeline for testing and deploying the data pipeline within more efficient and effective manner.

After setting up the data pipeline, you can check on resource groups in the subscription and get the list of resources for example:

![alt text](/images/post/set-up-pipeline/sample-resource-group.png "Data Pipeline Resource Groups")

## Trigger and Test Data Pipeline

In the example above, we set up a MySQL database and Connect to Azure Cloud via Azure Data Factory. The data flow is:

1. Azure Data Factory connects and ingest data from MySQL which is hosted in VM using Azure IR connector.
2. Ingested data will be stored in Azure Blob Storage Container for downstream processing.
3. Data in Data Lake will be processed in Azure Databricks with Native Spark support for cleansing/transforming/enreaching.
4. Processed data will be stored and consumed in Microsoft Power BI

## Conclusion

Data is visualized in Power BI

![alt text](/images/post/set-up-pipeline/sample-reports.png "Report in Power BI")

Data Pipeline in Azure Data Factory

![alt text](/images/post/set-up-pipeline/sample-pipeline.png "Data Pipeline in ADF")

Small steps, big thought. There are several ways to create Data Pipeline but keep in mind that MUST keep the core components in between of design and implementation as 80% of the problem will be fixed, 20% of the enhancement is done through you have 80%.

**Notes:** I must missing some points as I don't know what I don't know. Please let me know what your thought and feedback me. Thanks!
