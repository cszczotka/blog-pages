---
layout: post
title:  "Change Data Feed"
date:   2025-01-12 12:00:00 +0100
categories: Databricks
---

## Change Data Feed in Delta tables

To enable change data feed ( CDF ) on delta table set property delta.enableChangeDataFeed = true.

Example how to read change data feed for updated and inserted records.

```python
from pyspark.sql.functions import col

(
    spark.read.format("delta")
        .option("readChangeFeed", "true")
        .option("startingVersion", 0)
        .table("bronze")
        .filter(col("_change_type").isin(["update_postimage", "insert"]))
        .write
        .mode("append")
        .table("bronze_history_type1")
)

```

### Schema 

|Column name|Type|Values|
|--|--|--|
|_change_type|String|insert, update_preimage , update_postimage, delete|
|_commit_version|Long|The Delta log or table version containing the change.|
|_commit_timestamp|Timestamp|The timestamp associated when the commit was created.|

The following are the values inserted in the _change_type column according to the operation performed on the table:

|Operation|Value in _change_type column|
|--|--|
|Inserting a new record |insert|
|Updating an existing record | update_preimage ( value before the update), <br> update_postimage ( value after the update )|
|Deleting an existing record |delete|
