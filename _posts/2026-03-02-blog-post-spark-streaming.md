# Streaming data frame with watermark


Streaming data pipeline with a grouped aggregation using DataFrame df. The pipeline needs to calculate the average humidity and average temperature for each non-overlapping five-minute interval. 
Incremental state information should be maintained for 10 minutes for late-arriving data. Streaming DataFrame df has the following schema: “device_id INT, event_time TIMESTAMP, temp FLOAT, humidity FLOAT“

Setting withWatermark(“event_time“, “10 minutes“) ensures the pipeline retains state for 10 minutes to handle late-arriving data, meeting the pipeline‘s requirements for incremental processing. Other options are either invalid or inappropriate for the task.


```python
df
.withWatermark(“event_time“, “10 minutes“)
.groupBy(
window(“event_time“, “5 minutes“).alias(“time“),
“device_id“
)
.agg(
avg(“temp“).alias(“avg_temp“),
avg(“humidity“).alias(“avg_humidity“)
)
.writeStream
.format(“delta“)
.outputMode(“append“)
.saveAsTable(“sensor_avg“)
```