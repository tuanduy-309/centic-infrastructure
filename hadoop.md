# Hadoop 3 cluster set up guide (Ubuntu)
This README provides step-by-step instructions for setting up a Hadoop 3-node cluster on Ubuntu Linux. A Hadoop cluster typically consists of a master node (NameNode and ResourceManager) and two data nodes (DataNode and NodeManager).
## Prerequisite
Before you begin, ensure you have the following prerequisites:

Three Ubuntu machines (virtual or physical)
SSH access to all machines
Java installed on all machines
Hadoop downloaded on each node 
## Step 1: Create user for Hadoop and configure SSH
**First, create a new user named hadoop:**
```bash
sudo adduser hadoop
```
To enable superuser privileges to the new user, add it to the sudo group:

```bash
sudo usermod -aG sudo hadoop
```
Once done, switch to the user hadoop:

```bash
sudo su - hadoop
```

**Add entries in hosts file (master and workers)**
Edit hosts file.
```bash
sudo vim /etc/hosts
```
Now add entries of master and slaves in hosts file.
```
<MASTER-IP>  master
<SLAVE01-IP> datanode01
<SLAVE02-IP> datanode02
```

**Install Open SSH Server-Client**
```bash
sudo apt-get install openssh-server openssh-client
```
**Generate key pairs**
```bash
ssh-keygen -t rsa -P ""
```
**Configure passwordless SSH**

Copy the content of .ssh/id_rsa.pub (of master) to .ssh/authorized_keys (of all the workers as well as master node).

**Check by SSH to all the workers**
```bash
ssh datanode01
ssh datanode02
```


## Step 2: Installing Java

Ensure Java is installed on **all nodes**. You can check this by running:

```bash
java -version
```
If java is not installed, run the following command :
```bash
sudo apt install default-jdk scala git -y
```
And check the java version again to make sure java is installed successfully

## Step 3: Install Hadoop (all nodes)
If you have created a user for Hadoop, first, log in as the hadoop user:
```bash
sudo su - hadoop
```
Use the _```wget```_ command and the direct link to download the Spark archive:
```bash
wget https://downloads.apache.org/hadoop/common/stable/hadoop-3.3.6.tar.gz
```
Once you are done with the download, extract the file using the following command:

```bash
tar -xvzf hadoop-3.3.6.tar.gz
```
Next, move the extracted file to the ```/usr/local/hadoop``` using the following command:
```bash
sudo mv hadoop-3.3.6 /usr/local/hadoop
```
Now, create a directory using mkdir command to store logs:

```bash
sudo mkdir /usr/local/hadoop/logs
```
Finally, change the ownership of the ```/usr/local/hadoop``` to the user ```hadoop```:
```bash
sudo chown -R hadoop:hadoop /usr/local/hadoop
```
## Step 3: Configure Hadoop on Ubuntu
First, open the .bashrc file using the following command:
```bash
sudo nano ~/.bashrc
```
Jump to the end of the line in the nano text editor by pressing ```Alt + /``` and paste the following lines:

```
export HADOOP_HOME=/usr/local/hadoop

export HADOOP_INSTALL=$HADOOP_HOME

export HADOOP_MAPRED_HOME=$HADOOP_HOME

export HADOOP_COMMON_HOME=$HADOOP_HOME

export HADOOP_HDFS_HOME=$HADOOP_HOME

export YARN_HOME=$HADOOP_HOME

export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native

export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin

export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```
To enable the changes, source the ```.bashrc``` file:
```bash
source ~/.bashrc
```

## Step 4: Configure java environment variables (Do this on master node)

### Edit the ```hadoop-env.sh``` file
First, open the ```hadoop-env.sh``` file:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
Press ```Alt + /``` to jump to the end of the file and paste the following lines in the file to add the path of the Java:
```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64

export HADOOP_CLASSPATH+=" $HADOOP_HOME/lib/*.jar"
```
Save changes and exit from the text editor.
>[!NOTE]
>Use ```Ctrl + X``` to save change when using ```nano```

Next, change your current working directory to ```/usr/local/hadoop/lib```:

```bash
cd /usr/local/hadoop/lib
```

Here, download the javax activation file:

```bash
sudo wget https://jcenter.bintray.com/javax/activation/javax.activation-api/1.2.0/javax.activation-api-1.2.0.jar
```
Once done, check the Hadoop version in Ubuntu:

```bash
hadoop version
```
Next, you will have to edit the ```core-site.xml``` file to specify the URL for the namenode.

### Edit the ```core-site.xml``` file
First, open the ```core-site.xml``` file using the following command:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
And add the following lines in between ```<configuration> ... </configuration>```:
```bash
<property>
  <name>fs.defaultFS</name>
  <value>hdfs://<MASTER_NODE>:9000</value> #replace with your master node ip
</property>
```
Save the changes and exit from the text editor.

Next, create a directory to store node metadata using the following command:

```bash
sudo mkdir -p /home/hadoop/hdfs/{namenode,datanode}
```
And change the ownership of the created directory to the ```hadoop``` user:

```bash
sudo chown -R hadoop:hadoop /home/hadoop/hdfs
```
### Edit the hdfs-site.xml configuration file

By configuring the ```hdfs-site.xml``` file, you will define the location for storing node metadata, fs-image file.

So first open the configuration file:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
And paste the following line in between ```<configuration> ... </configuration>```:

```bash
<property>

      <name>dfs.replication</name>

      <value>2</value> # 2 is the number of datanode

   </property>


   <property>

      <name>dfs.name.dir</name>

      <value>file:///home/hadoop/hdfs/namenode</value> # The directory of the namenode folder

   </property>


   <property>

      <name>dfs.data.dir</name>

      <value>file:///home/hadoop/hdfs/datanode</value> # The directory of the datanode folder

   </property>
```
Save changes and exit from the ```hdfs-site.xml``` file.

### Edit the ```mapred-site.xml``` file

By editing the ```mapred-site.xml``` file, you can define the MapReduce values.

To do that, first, open the configuration file using the following command:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
And paste the following line in between ```<configuration> ... </configuration>```:
```bash
<property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
</property>
```
Save and exit from the nano text editor.

### Edit the yarn-site.xml file
The purpose of editing this file is to define the YARN settings.

First, open the configuration file:


```bash
sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
And paste the following line in between ```<configuration> ... </configuration>```:

```bash
<property>
  <name>yarn.nodemanager.aux-services</name>
  <value>mapreduce_shuffle</value>
</property>
<property>
  <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
  <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
  <name>yarn.resourcemanager.hostname</name>
  <value> <MASTERNODE_IP> </value>  # replace <MASTERNODE_IP> with your masternode ip
</property>
```
Save changes and exit from the config file.

## Step 5: Set up Hadoop 3-cluster (Do this on masternode)
We’re still on master node, let’s open the ```workers``` file:

```bash
sudo nano /usr/local/hadoop/etc/hadoop/workers
```
Add these two lines: 
```bash
datanode01
datanode02
```
We need to copy the Hadoop Master configurations to the datanode, to do that we use these commands:
```bash
scp /usr/local/hadoop/etc/hadoop/* datanode01:/usr/local/hadoop/etc/hadoop/
scp /usr/local/hadoop/etc/hadoop/* datanode02:/usr/local/hadoop/etc/hadoop/
```
Now we need to format the HDFS file system. Run these commands:

```bash
source /etc/environment
hdfs namenode -format
```

## Step 6: Start the Hadoop cluster
On master node, start HDFS with this command:
```bash
start-dfs.sh
```
Still on master node, start YARN with this command:
```bash
start-yarn.sh
```
To stop the Hadoop cluster, run:
```bash
stop-all.sh
```

>[!NOTE]
>To check if the cluster is set up successfully, run ```jps``` on each node and you will see something like this:
>
>On masternode:
>```bash
>448358 NameNode
>448608 SecondaryNameNode
>453423 Jps
>448826 ResourceManager
>```
>On datanode:
>```bash
>1828253 NodeManager
>1829805 Jps
>1828083 DataNode
>```





