# Install ```elasticsearch-spark-30_2.12```

Currently, the ES-hadoop package from https://www.elastic.co/downloads/hadoop only support Spark 2.x and Scala 2.11

Therefore, to download the ES-hadoop package for Spark 3.x and Scala 2.12, we need to use ```maven```

Here are the detail steps to download the ```elasticsearch-spark-30_2.12```:
## Step 1: Ensure Maven is Installed:

- First, make sure Maven is installed on your Ubuntu system. You can check if Maven is installed by running mvn -v in your terminal. If it's not installed, you can install it using:
  ```bash
  sudo apt update
  sudo apt install maven
  ```
## Step 2: Create a Maven Project:
- In your terminal, navigate to the directory where you want to create your project and run:
  ```bash
  mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-    archetype-quickstart -DinteractiveMode=false
  ```
- Replace ```com.mycompany.app``` with your group ID and ```my-app``` with your artifact ID.

## Step 3: Edit the pom.xml File:

- In your Maven project directory, locate the pom.xml file. Open it in a text editor and add the following dependency:
  ```bash
  <dependency>
  <groupId>org.elasticsearch</groupId>
  <artifactId>elasticsearch-spark-30_2.12</artifactId>
  <version>8.11.3</version>  # Replace with the version you need
  </dependency>
  ```
- Replace **8.11.3** with the version of Elasticsearch-Hadoop compatible with your Elasticsearch setup.

>[!NOTE]
>To check the compatibility of the ES-hadoop, you can visit:
>
>https://mvnrepository.com/artifact/org.elasticsearch/elasticsearch-spark-20_2.12/8.11.3
>
>https://www.elastic.co/guide/en/elasticsearch/hadoop/current/install.html

## Step 4: Download the Jar:

- In the terminal, navigate to your Maven project directory and run the following command to download the jar and its dependencies:
  ```bash
  mvn dependency:copy-dependencies
  ```
- This command downloads the specified dependencies and places them in the target/dependency directory within your project folder.

## Step 5: Access the Jar:
- After the process completes, you will find ```elasticsearch-spark-30_2.12-<version>.jar``` in the ```target/dependency``` directory. You can use this jar in your Spark applications.

