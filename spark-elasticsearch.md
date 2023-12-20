## Elasticsearch configuration

```bash
from pyspark.sql import SparkSession

# Config the connector jar file
spark = SparkSession.builder.appName("group08").master("spark://34.142.194.212:7077") \
        .config("spark.jars", "/opt/spark/jars/elasticsearch-spark-30_2.12-8.11.3.jar") \
        .config("spark.port.maxRetries", "100") \
        .getOrCreate()

# Test with example Dataframe
colnames = ['col_' + str(i + 1) for i in range(5)]
data = [
    [1, 2, 3, 4, 5],
    [6, 7, 8, 9, 10]
]
df = spark.createDataFrame(data, schema=colnames)

# Connect to Elasticsearch
df.write.format("org.elasticsearch.spark.sql") \
    .option("es.nodes", "34.143.255.36") \
    .option("es.port", "9200") \
    .option("es.resource", "your_index_name") \
    .save()

# Stop the SparkSession
spark.stop()
```
