## Importent spark configuration settings

- spark.sql.files.maxPartitionBytes - this parameter controls the maximum size of a partition when data is read into Spark. This ensures that each partition fits within memory and provides efficient parallelism during data processing.

- spark.executor.memory - sets the amount of memory to allocate per Spark executor, example:  spark.conf.set("spark.executor.memory", "4g") 

- spark.driver.memory -  allocates memory for the Spark driver process, example: spark.conf.set("spark.driver.memory", "4g") 
