# Jupyterhub
## Introduction
In order to take use of spark cluster, you need to leverage the Centic server resources by joining to the Jupyterhub. This repository show you how to connect to the jupyterhub and config the pyspark connection
## Table of contents
- [Prerequisite](#Prerequisite): Things you need to complete beforehand
- [Setting](#Setting): Spark connection configuration
- [Google cloud configuration](#Google): Spark configuration for google bucket's data connection
## Prerequisite
Log in to jupyterhub account, each group have their own account
```bash
+ Access server: YOUR_JUPYTER_LAB_CONNECTION
+ Confirm your information:
++ for the IT4043E class:
    + account: name of group
    (e.g: group01)
    + password: e10bigdata

++ for the IT5384 class:
    + account: name of group with class
    (e.g: group02_it5384)
    + password: bigdata5384
```
## Setting
**Note:**  You have to create and use conda environment with you group name (e.g: group01). We'll track you based on this environment

### Step 1: Enter  terminal in jupyterhub and activate conda environment
```bash
$ conda init
$ source bash
$ conda create -n 'name' python==3.9
$ conda activate 'name'
$ pip install pyspark
```
### Step2: Connect to Spark (optional)
```python
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, IntegerType
spark = SparkSession.builder.appName("SimpleSparkJob").master("YOUR_SPARK_CONNECTION").getOrCreate()
```

### Step 3: Test (optional)
```python
df = spark.range(0, 10, 1).toDF("id")
df_transformed = df.withColumn("square", df["id"] * df["id"])
df_transformed.show()

spark.stop() # Ending spark job
```



## Google cloud configuration
**Note:**  You don't need to upload the credential file (lucky-wall-393304-2a6a3df38253.json) as we've already uploaded it to the cluster

```python
##Configuration

#config the connector jar file
spark = SparkSession.builder.appName("SimpleSparkJob").master("YOUR_SPARK_CONNECTION").config("spark.jars", "/opt/spark/jars/gcs-connector-latest-hadoop2.jar").getOrCreate()

#config the credential to identify the google cloud hadoop file 
spark.conf.set("google.cloud.auth.service.account.json.keyfile","/opt/spark/lucky-wall-393304-2a6a3df38253.json")
spark._jsc.hadoopConfiguration().set('fs.gs.impl', 'com.google.cloud.hadoop.fs.gcs.GoogleHadoopFileSystem')
spark._jsc.hadoopConfiguration().set('fs.gs.auth.service.account.enable', 'true')

## Connect to the file in Google Bucket with Spark

path=f"gs://it4043e-it5384/addresses.csv"
spark.read.csv(path)
spark.show()

spark.stop # Ending spark job

```




