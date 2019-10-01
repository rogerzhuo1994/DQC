# Overview 
## Data Quality 
There are many definitions about data quality in the Internet. An important common thing among these definitions is that many data problems can be detected using the profile of datasets and business rules. For ex. For example, in terms of data consistency, which emphasizes that different datasets should match with each other, it can be tested whether the row count of records that do not match is larger than 0. Another example, in terms of data validity, which emphasized that values should be within their reasonable range, it can be tested with max, min, or distinct values goes beyond the reasonable value. le value.  

## Data Quality Center (DQC) 
DQC is a plugin atop Apache Airflow that provides web GUI that allow data professionals to configure profile items on datasets and the reasonable values to compare with. To make the checks happen, DQC provides a sensor class, which can be embedded into a DAG, to read a specific configuration and then poke a databricks job to generate queries, execute queries to get the profile values and compare the values with the configured reasonable values. If the derived values do not match with the configured reasonable values, the databricks job will fail and the sensor embedded in the DAG will fail. Then the downstream DAG nodes will not be executed in order to avoid bad data enter into systems. 
 
# User Guide 
## Create DQC Job 
1. ID: Globally unique ID of the job. Will be used in future configuration in DQC sensors. DQC sensors will use the configured job ID to find the detailed job configuration, e.g. profiles to check, reasonable values. 
## Create Dataset in DQC Job 
### Create Table Data Source 
Table data sources are the data source that is directly a table or a certain partition of tables. Further configured evaluation rules must be performed on configured data sources. 
1. Name: Alias of the dataset in this hob. Will be referred by the evaluation rules in this job. 
2. Schema: Data warehouse schema of the target table 
3. Table: Table name of the target table 
4. Partition: Optional. Partitions of the data source. Configured as follows:
```
# One partition column per line:

# For dynamic values, partition_column:reserved_keywords_or_variable_names_in_dag_file
# Reserved keywords include dt and env
env:env
dt:dt

# For static values, partition_column:value
# String value should to be encapsulated by '', numeric values should not.
env:'LA1'
dt:'2019-08-20'
```
