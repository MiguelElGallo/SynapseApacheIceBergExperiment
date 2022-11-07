# SynapseApacheIceBergExperiment
A space to collaborate regarding experiences of using Apache Iceberg format with Synapse Spark Pools.

# Steps

## Add the Icebeberg .jar to Synapse
You need to download the Iceberg Jar and add it to Synapse.

Follow instructions from here: https://learn.microsoft.com/en-us/azure/synapse-analytics/spark/apache-spark-manage-pool-packages

I'm using this file: iceberg-spark-runtime-3.2_2.12-0.13.2.jar

## Create a linked service to the storage Account
You can find some guidance here: https://learn.microsoft.com/en-us/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=hadoop#create-and-query-external-tables-from-a-file-in-azure-data-lake

Basically you need to have your Storage Account as 
## Run the following cells
Following the steps from https://iceberg.apache.org/docs/latest/getting-started/ create the following cells in a Synapse Notebook

```json
%%configure -f
{
    "conf":{
        "spark.sql.extensions":"org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions",
        "spark.sql.catalog.spark_catalog":"org.apache.iceberg.spark.SparkSessionCatalog",
        "spark.sql.catalog.spark_catalog.type":"hive",
        "spark.sql.catalog.local2":"org.apache.iceberg.spark.SparkCatalog",
        "spark.sql.catalog.local2.type":"hadoop",
        "spark.sql.catalog.local2.warehouse":"abfss://container@storage.dfs.core.windows.net/db"
    }
}
```
(Remember to replace container@storage)
Then you can create a table

```
%%sql
-- local is the path-based catalog defined above
CREATE TABLE local.db.table (id bigint, data string) USING iceberg
```

Then insert some records:
```
%%sql
INSERT INTO local.db.table VALUES (1, 'a'), (2, 'b'), (3, 'c');
```

And finally check the contents of the table:

```
%%sql
select * from local.db.table
```

This will create the table in the default Storage Account of your Synapse:
(default)/user/trusted-service-user/$PWD/warehouse/db/table

And there you can find two folders data and metadata. You can guess what is inside, right? ;)

My next step is to see how I can specify a different storage account, and that is clearly in this line:
```
"spark.sql.catalog.local.warehouse":"$PWD/warehouse"
```

Feel free to contribute with any ideas!
