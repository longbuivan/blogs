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

## Conclusion
This step is very important and can be considered as gating for release to Production when migrating data from legacy system.
The notebooks is linked in repo for detailt reference.
