# Word Count Using Hadoop MapReduce

## Project Summary

This project implements a **Word Count** application using **Hadoop MapReduce** in **Java**. The program processes a text dataset, counts the occurrences of each word, and generates the output. Built with **Maven**, it is designed to run on a Hadoop cluster.

## Implementation Details

### Mapper (WordMapper.java):
- Reads lines from the input dataset.
- Splits each line into individual words.
- Emits `(word, 1)` key-value pairs for each word.

### Reducer (WordReducer.java):
- Collects words grouped by key.
- Computes the total occurrences by summing values.
- Outputs the `(word, count)` pairs.

## Setup and Execution

### 1. Start the Hadoop Cluster
Launch the Hadoop cluster using Docker:
```bash
docker compose up -d
```

### 2. Build the Project
Compile and package the code with Maven:
```bash
mvn install
```

### 3. Transfer JAR File to Shared Directory
Move the generated JAR file to the shared folder:
```bash
mv target/*.jar shared-folder/input/code/
```

### 4. Copy JAR File to Hadoop Container
Transfer the JAR file to the ResourceManager container:
```bash
docker cp shared-folder/input/data/WordCountUsingHadoop-0.0.1-SNAPSHOT.jar resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### 5. Upload Dataset to Hadoop
Move the input dataset into the container:
```bash
docker cp shared-folder/input/data/input.txt resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### 6. Access the Hadoop Container
Enter the Hadoop ResourceManager container:
```bash
docker exec -it resourcemanager /bin/bash
```
Navigate to the required directory:
```bash
cd /opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### 7. Prepare HDFS for Processing
Create a directory in HDFS to store the input dataset:
```bash
hadoop fs -mkdir -p /input/dataset
```
Upload the dataset to HDFS:
```bash
hadoop fs -put ./input.txt /input/dataset
```

### 8. Run the MapReduce Job
Execute the Word Count program using the command:
```bash
hadoop jar WordCountUsingHadoop-0.0.1-SNAPSHOT.jar com.example.controller.Controller /input/dataset/input.txt /output
```

### 9. View the Results
To check the output of the job:
```bash
hadoop fs -cat /output/*
```

### 10. Retrieve Output from HDFS
Copy the output from HDFS to local storage:
```bash
hdfs dfs -get /output /opt/hadoop-3.2.1/share/hadoop/mapreduce/
```
Then, transfer it from the container to the local machine:
```bash
exit 
```
```bash
docker cp resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/output/ shared-folder/output/
```

## Challenges Encountered
Despite correct implementation, the job fails to complete execution and stalls at **map 17%, reduce 0%** due to excessive resource consumption, triggering a system warning.

## Sample Input
```text
Hello world  
Hello Hadoop  
Hadoop is powerful  
Hadoop is used for big data  
```

## Sample Output
```text
Hadoop 3  
Hello 2  
is 2  
used 1  
for 1  
big 1  
data 1  
powerful 1  
world 1  
