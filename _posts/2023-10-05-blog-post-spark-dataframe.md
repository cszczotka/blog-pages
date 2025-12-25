## Spark dataframe

### How to get information about partitions count and size

```python
from pyspark import RDD
from pyspark.sql import DataFrame



def print_partition_info(df: DataFrame):

    import statistics

    def get_partition_len(iterator):
        yield sum(1 for _ in iterator)

    rdd: RDD = df.rdd

    count = rdd.getNumPartitions()
    # lengths = rdd.glom().map(len).collect() # much more memory hungry than next line
    lengths = rdd.mapPartitions(get_partition_len, True).collect()

    print("")
    print(f"{count} partition(s) total.")

    print(f"size stats")
    print(f"     min: {min(lengths)}")
    print(f"     max: {max(lengths)}")
    print(f"     avg: {sum(lengths)/len(lengths)}")
    print(f"  stddev: {statistics.stdev(lengths)}")

    print("")
    print("detailed info")
    for i, pl in enumerate(lengths):
        print(f"  {i}. {pl}")

```