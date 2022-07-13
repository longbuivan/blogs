---
layout: post
title: "Improving Joins Performance in Spark"
date: 2022-06-17 11:12:00 +0700
categories: blog
---

Nowadays, Spark is lighting-fast computing framework which supports in-memory processing large data on distributed system. In fact, the join operation is the most usage during dealing with several dataset in project or pipeline that needs to enrich from 2 or more dataset together as de-normalizing data processing.

Join is expensive operation in Spark because it requires data movement among nodes in cluster (data shuffling), data being move via network of cluster.

Writing code to join dataset in script is very easy (dataset_a.join(dataset_b, on <key_join>, <type_join>)); but everything possible to be crashed if we don't deeply understand how dataset joins internally in Spark. In order to increase speed and reduce execution time as well as avoid out-of-memory (OOM) problem, improve joining operation able to make a job moves faster significantly.

## Application Components in Spark

First, we need to understand joins in Spark

- Application: contains a main function
- Job: when Action performs on RDD, a "job" is created and submitted into Spark
- Stage: a job be divided into "stage" based on the shuffle boundary
- Task: a stage be further divided into "tasks" based on number of partition in the RDD. So then Tasks are the smallest unit of work for Spark

## Type of Joins
[TBD]