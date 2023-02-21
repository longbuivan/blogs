---
layout: post
title: "GDPR AND CCPA COMPLIANCE IN DATA PIPELINE"
date: 2023-02-21 15:13:00 +0700
categories: blog
---

As technology advances and companies continue to collect and process large amounts of personal data and most data project requires data governance with data security, data privacy within the platform. From a data engineering perspective, how do we comply with those requirements?
Summary: in the article, Author pointed out details of scenarios and use cases with sample implementation on Databricks and follow Lakehouse architecture.
![alt text](/images/post/gdpr-ccpa.png "Data Privacy")

### Scenario 1 - Anonymization of PII Data using Data Masking

using a hashing function for masking data

### Scenario 2 - Pseudonymization of PII Data using Data Encryption

using Key Management Service (KMS) to encrypt data and descrypt when consuming

### Scenario 3 - Pseudonymization of PII Data by Splitting Tables

splitting table contains PII into PII and Non-PII tables which are linked via KEY, restricted access for PII table

### Scenario 4 - Pseudonymization of PII Data using Tokenization

using token for obscuring PII data with token value, lookup the real value by token
