# Overview 
## Data Quality 
There are many definitions about data quality in the Internet. An important common thing among these definitions is that many data problems can be detected using the profile of datasets and business rules. For ex. For example, in terms of data consistency, which emphasizes that different datasets should match with each other, it can be tested whether the row count of records that do not match is larger than 0. Another example, in terms of data validity, which emphasized that values should be within their reasonable range, it can be tested with max, min, or distinct values goes beyond the reasonable value. le value.  

## Data Quality Center (DQC) 
DQC is a plugin atop Apache Airflow that provides web GUI that allow data professionals to configure profile items on datasets and the reasonable values to compare with. To make the checks happen, DQC provides a sensor class, which can be embedded into a DAG, to read a specific configuration and then poke a databricks job to generate queries, execute queries to get the profile values and compare the values with the configured reasonable values. If the derived values do not match with the configured reasonable values, the databricks job will fail and the sensor embedded in the DAG will fail. Then the downstream DAG nodes will not be executed in order to avoid bad data enter into systems. 
 
# User Guide 
## Create DQC Job 
1. ID: Globally unique ID of the job. Will be used in future configuration in DQC sensors. DQC sensors will use the configured job ID to find the detailed job configuration, e.g. profiles to check, reasonable values. 
## Create Data Source in DQC Job 
### Create Table Data Source 
Table data sources are the data source that is directly a table or a certain partition of tables. Further configured evaluation rules must be performed on configured data sources. 
<ol>
 <li>Name: Alias of the data source in this hob. Will be referred by the evaluation rules in this job. </li>
 <li>Schema: Data warehouse schema of the target table </li>
 <li>Table: Table name of the target table </li>
 <li>Partition: Optional. Partitions of the data source. Configured as follows: </li>
</ol>

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

### Create Query in DQC Job 
Query data sources are the data source that are complicated and can only be quried through a query, like with joins, unions, multiple selections etc. Further configured evaluation rules must be performed on configured data sources. 
<ol>
<li>Name: Alias of the data source  in this hob. Will be referred by the evaluation rules in this job. </li>
<li>Query: Query to get this data source. String value should to be encapsulated by '' not "".</li>
</ol>

## Create Evaluation Rules in DQC Job
By configuring evaluation rules, certain metrics on a certain column of a previsouly confugired data source will be derived, and then compared with configured values. If the derived metric values do not match the configured values, this evaluation rule will fail. 
1. Data Source: name of a configured data source in this job. 
2. Column: column to be evaluated, must be in the data source.
3. Metric: type of metric to be evaluated. Now we have 8 supported metrics:
3.1. Row Count
3.2. Null Value Count: row count that has a null value on the column
3.3. Null Value Ratio
3.4. Empty Value Count: row count that has a '' value on the column, only for string column
3.5. Empty Value Ratio
3.6. Distinct Value: distinct values of the column, must be evaluated with list values
3.7. Distinct Value Count: number of distinct values of the column
4. Values
