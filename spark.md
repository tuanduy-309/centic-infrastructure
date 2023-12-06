# Apache Spark 3 Cluster Setup Guide (Ubuntu)

This guide provides step-by-step instructions on how to set up an Apache Spark cluster with one master and two slave nodes.

## Prerequisites

Before starting, ensure you have the following:
- Make sure that master node has access to two worker nodes using SSH Key
- Three machines (virtual or physical) with network connectivity.
- Java installed on all machines.
- Apache Spark downloaded on all machines.

## Step 1: Configure SSH (only master)
**Add entries in hosts file (master and workers)**

Edit hosts file.
```plaintext
sudo nano /etc/hosts
```
Now add entries of master and slaves in hosts file.
```
<MASTER-IP>  master
<SLAVE01-IP> worker01
<SLAVE02-IP> worker02
```

Save changes and exit from the text editor.

>[!NOTE]
>Use ```Ctrl + X``` to save change when using ```nano```
>
**Install Open SSH Server-Client**
```
sudo apt-get install openssh-server openssh-client
```
**Generate key pairs**
```
ssh-keygen -t rsa -P ""
```
**Configure passwordless SSH**

Copy the content of .ssh/id_rsa.pub (of master) to .ssh/authorized_keys (of all the workers as well as master node).

**Check by SSH to all the workers**
```
ssh worker01
```
```
ssh worker02
```


## Step 2: Installing Java

Ensure Java is installed on **all nodes**. You can check this by running:

```plaintext
java -version
```
If java is not installed, run the following command :
```plaintext
sudo apt install default-jdk scala git -y
```
And check the java version again to make sure java is installed successfully
## Step 3: Downloading and Installing Apache Spark

Now, you need to download Spark to **all nodes** from Spark's website. We will go for Spark 3.5.0 with Hadoop 3 as it is the latest version.
**Download latest version of Spark**

Use the `_``wget```_ command and the direct link to download the Spark archive:
```plaintext
wget https://downloads.apache.org/spark/spark-3.5.0/spark-3.5.0-bin-hadoop3.tgz
```
**Extract Spark tar**

Now, extract the saved archive using **tar**:
```plaintext
tar xvf spark-*
```
Let the process complete. The output shows the files that are being unpacked from the archive.

**Move Spark software files**

Move the unpacked directory spark-3.5.0-bin-hadoop3 to the _**opt/spark**_ directory.

Use the ```mv``` command to do so:
```plaintext
sudo mv spark-3.5.0-bin-hadoop3 /opt/spark
```
The terminal returns no response if it successfully moves the directory.

**Set up the environment for Spark**
Edit bashrc file.
```plaintext
sudo nano ~/.bashrc
```
Add the following lines to the end of the file (Using ```Alt+/``` to move to the end of the file)
```plaintext
export SPARK_HOME=/opt/spark

export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin

export PYSPARK_PYTHON=/usr/bin/python3
```
Use the following command for sourcing the ~/.bashrc file.

```
source ~/.bashrc
```
>[!NOTE]
>_The whole spark installation procedure must be done in master as well as in all worker nodes._

## Step 4: Configuring the Spark Master Node

Do the following procedures only in master.
**Edit spark-env.sh**
Move to spark conf folder and create a copy of template of _```spark-env.sh```_ and rename it.
```
cd /usr/local/spark/conf
cp spark-env.sh.template spark-env.sh
```
Now edit the configuration file _```spark-env.sh```_.
```
sudo nano spark-env.sh
```
And add the following parameters at the end of the files.
```
export SPARK_MASTER_HOST='<MASTER-IP>'
export JAVA_HOME=<path_of_java_installation>
```
>[!NOTE]
>_You must replace with your specific ```<MASTER-IP>``` and ```<path_of_java_installation>```._
>
>_The java path is usually ```/usr/lib/jvm/java-11-openjdk-amd64``` by default_

**Add Workers**

Edit the configuration file _```workers```_ in (/usr/local/spark/conf).
```
cp workers.template workers
sudo nano workers
```
And add the following entries.
```
worker01
worker02
```

## Step 5: Start Spark Cluster

Move to the spark sbin folder
```
cd /opt/spark/sbin
```
To start the spark cluster, run the following command on master.
```
bash start-all.sh
```
Check if it works perfectly by using jps
```
jps
```
>[!NOTE]
>In master node, when running _```jps```_ command, it will show something like
>```
>408236 Jps
>1548809 Master
>```
>In worker node, when running _```jps```_ command, it will show something like
>```
>1221944 Worker
>1804116 Jps
>```

To stop the spark cluster, run the following command on master.
```
cd /opt/spark/sbin
bash stop-all.sh
```
