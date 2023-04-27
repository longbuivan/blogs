## Data Testing Strategies

### Data Testing - Function

- Metadata testing: verification of Data warehouse table definitions.
- Data Completeness: Data is not missing
- Data Consistency: Data consistency refers to the uniformity of data as it moves across networks and applications. The same data values stored in difference locations should not conflict with one another.
- Data Uniqueness: No duplicated in dataset
- Data Validity: involves performing number check, date check, precision check, data check, Null check, etc.
- Data Accuracy: Data objects correctly represent the values
- Data Migration Testing:
  - Schema Compare Tests:
    - Check if the table and column name is the same between source and target.
    - Datatype mapping between source and destination should be correct. Example source column with INT datatype should be NUMERIC in the target system.
    - Verify the views, primary keys, and indexes are also matching.
  - Row Count Tests:
    - One-time Row Count checks for the initial loads of all the tables.
    - Row Count checks for delta loads of all or specific tables.
  - Data Comparison Tests:
    - Check the first name column in the source, and the target is the same.
    - Ensure the date value is matching even though the format is different between the source and the target.
  - Data Aggregation Tests:
    - Verify that summation for all the numeric columns in a table matches source and target.

### Data Testing Non Function

- Data Quality Monitoring: Test pipeline failure cases, check logs and notify when there is an error
- Infrastructure: mechanism to copy, backup and restore data
- Data security:
  - User Authentication checks and User Role-Based Authorization
  - Data Encryption and Masking of Personal Information
  - Data performance
  - Data collection phase: speed and capacity to graph data within a given time frame from different sources.
  - Data processing: processing speeds are validated. Queries to be executed are expected to perform with high speeds and with low latencies
  - Data ingestion phase: Application is tested and validated base on its space and capacity to load the collected data from the source to the destination
  - Component Testing:
    - Highly available and connected
    - Component back up should be online when any node phase faces failure
    - Parameters of performance Testing
    - Data storage
    - Commit logs
    - Concurrency
    - Caching
