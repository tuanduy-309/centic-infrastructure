# Jupyterhub
## I.Introduction
In order to take use of spark cluster, you need to leverage the Centic server resources by joining to the Jupyterhub. 

## II.Prerequisite
Log in to jupyterhub account, each group have their own account
```bash
+ Access server: http://34.142.194.212:8000
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
## III.Setting
**Note:**  You have to create and use conda environment with you group name (e.g: group01). We'll track you based on this environment

### Step 1: Enter  terminal in jupyterhub and activate conda environment
```bash
$ conda init
$ source bash
$ conda create -n 'name'
$ conda activate 'name'
```
### Step2: Connect to Spark (optional)
```python
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, IntegerType
spark = SparkSession.builder.appName("SimpleSparkJob").master("spark://34.142.194.212:7077").getOrCreate()
```

### Step 3: Test (optional)
```python
df = spark.range(0, 10, 1).toDF("id")
df_transformed = df.withColumn("square", df["id"] * df["id"])
df_transformed.show()

spark.stop()
```



## IV.Google cloud Spark cluster  configuration
**Note:**  You don't need to upload the credential file (lucky-wall-393304-2a6a3df38253.json) as we've already uploaded it to the cluster

```python

#config the connector jar file
spark = SparkSession.builder.appName("SimpleSparkJob").master("spark://34.142.194.212:7077").config("spark.jars", "/opt/spark/jars/gcs-connector-latest-hadoop2.jar").getOrCreate()

#config the credential to identify the google cloud hadoop file 
spark.conf.set("google.cloud.auth.service.account.json.keyfile","/opt/spark/lucky-wall-393304-2a6a3df38253.json")
spark._jsc.hadoopConfiguration().set('fs.gs.impl', 'com.google.cloud.hadoop.fs.gcs.GoogleHadoopFileSystem')
spark._jsc.hadoopConfiguration().set('fs.gs.auth.service.account.enable', 'true')
```




