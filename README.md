# Jupyterhub
## Introduction
In order to take use of spark cluster, you need to leverage the Centic server resources by joining to the Jupyterhub. 

## Prerequisite
Log in to jupyterhub account, each group have their own account
```bash
+ Access server: http://34.142.194.212:8000
+ Confirm your information:
    + account: name of group
    (e.g: group01)
    + password: e10bigdata
```
## Setting
**Note:**  You have to create and use conda environment with you group name (e.g: group01). We'll track you based on this environment

### Step 1: Enter  terminal in jupyterhub and activate conda environment
```bash
$ conda init
$ source bash
$ conda create -n 'name'
$ conda activate 'name'
```
### Step2: Connect to Spark (with pyspark)
```bash
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, IntegerType

spark = SparkSession.builder.appName("SimpleSparkJob").master("spark://34.142.194.212:7077").getOrCreate()
```

### Step 3: Test
```
df = spark.range(0, 10, 1).toDF("id")
df_transformed = df.withColumn("square", df["id"] * df["id"])
df_transformed.show()
```
