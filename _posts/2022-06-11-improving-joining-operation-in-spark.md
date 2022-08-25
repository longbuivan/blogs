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

1. Broadcast Join
   This type of join strategy is suitable when one side of the datasets in the join is fairly small. (The threshold can be configured using “spark. sql. autoBroadcastJoinThreshold” which is by default 10MB).

Now since Table B is present on all the nodes where we have data for table A, no more data shuffling is required and each partition of table A can join with the required entries of table B.

This is the fastest type of join( as the bigger table requires no data shuffling) but has the limitation that one table in the join has to be small.

2. Sort Merge Join
   This is the standard join type, suitable when datasets on both sides of the join are medium/large.

- **Shuffle partitions**: The default value of the number of partitions as an output of this stage is 200 (can be changed using spark.sql.shuffle.partitions). The goal of this step is to reshuffle the data of table A and table B in such a way that rows that should be joined go to the same partition identifier ( Data rows to be joined becomes co-located physically/ on the same node). Partition identifier for a row is determined as Hash(join key)% 200 ( value of spark.sql.shuffle.partitions) . This is done for both tables A and B using the same hash function. This results in all the rows( in both table A and table B) with equal value in the join column being reshuffled to the same node post reshuffling ( since their hash value would be the same). This type of repartitioning is called HashRepartitioning. Hash is computed by default using the .hashcode() method in java.
- **Sorting within each partition**: This sorting is also done based on the join key.
- **Join the sorted partitions**: Depending on the join type(INNER, LEFT, etc), we produce the final output. This approach is similar to the standard solution for “merging two sorted arrays” using two pointers.

## Data skewness

Even when input data to the join step is uniformly partitioned, it may so happen that the intermediate data produced post shuffling in the sortmerge join doesn’t have uniform data size in new partitions created.

If we recollect the above discussion, in 1st stage of sortmerge join, data is hashed based on the join key and a new partition for that is decided. If we have a non-uniform distribution of values in the join column, then we would have a skewed partition produced after data shuffling.

In general, non-uniform distribution in the join column can lead to this skewness. (Eg value x is very frequent in the 1st table below and all those rows go to a single partition post shuffling). **Data skewness is the most popular reason for join failures/slowness.**

### Handling data skewness

- Method 1: Replace join of A and B with : ((A where join column is non-null) joined with (B ) ) UNION with (A where join column is null). This is for cases where apart from null values, other values in the join column are uniformly distributed. A more generic approach is discussed in b).

- Method 2: Add random numbers to the join column: Here we add some random value to the join key to make it distributed.

### Keep input data lean

If the join is becoming too slow, remove columns from the data which are not required post joining. Write this intermediate data somewhere and read this as input in the join step. This will reduce the size of the data that moves across the network during data shuffling.

Also, filter out any rows which might not be required post joining.

### Split big join into multiple smaller joins

If there are ways to split your input data into smaller batches without affecting the functionality then try doing joins in small batches. In our case, we were joining two datasets on a UUID field. Both the datasets also had a timestamp column. So, instead of joining the whole day data, we divided a day into multiple slots and joined only the corresponding slots of each data. This idea came to us as when we were joining full-day data it was taking 2–3 days to complete but sampling it to 25% data, made the join possible in 1–2 hours indicating smaller joins are faster.

### Job parameters tuning

Following are the important parameters that we can increase. ( This might not help much if you have any of the fundamental issues described above).

    executor-memory,
    spark.executor.memoryOverhead,
    spark.sql.shuffle.partitions,
    executor-cores,
    num-executors

## Conclusion

With the above optimizations, we were able to improve our job performance by greater than 10X.

Summary:

1. How join internally happens. Broadcast join vs SortMerge join.
2. Why does Data skewness happen(In Input data VS no skewness in input data partition but induced due to Hashpartitioning before join)
3. Handling data skewness
4. Keep the input data to join as lean as possible
5. Split big join into multiple smaller join
6. Tuning the spark job parameters for join

**! Important:** The data skewness is the predominant reason for join failures/slowness. For any other wide transformations too ( like distinct(), reduceBykey(), etc), similar data reshuffling happens at the start to bring keys with the same hash value to the same partitions.
