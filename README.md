# SynapseApacheIceBergExperiment
Experimenting with Apache Iceberg using Synapse Spark Pool

# Steps
Following the steps from https://iceberg.apache.org/docs/latest/getting-started/ create the following cells in a Synapse Notebook

```json
%%configure -f
{
    "conf":{
        "spark.sql.extensions":"org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions",
        "spark.sql.catalog.spark_catalog":"org.apache.iceberg.spark.SparkSessionCatalog",
        "spark.sql.catalog.spark_catalog.type":"hive",
        "spark.sql.catalog.local":"org.apache.iceberg.spark.SparkCatalog",
        "spark.sql.catalog.local.type":"hadoop",
        "spark.sql.catalog.local.warehouse":"$PWD/warehouse"
    }
}
```

Then you can create a table

```json
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
