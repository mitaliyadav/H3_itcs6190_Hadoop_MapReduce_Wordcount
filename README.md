
# WordCount-Using-MapReduce-Hadoop

## Project Overview

This project demonstrates a basic MapReduce job using Apache Hadoop to count the occurrences of each word in a given text file. The repository contains the source code for the Mapper and Reducer classes, along with a sample input file and its corresponding output. The entire process is containerized using Docker to ensure a reproducible environment.

## Approach and Implementation

### Mapper Logic

The Mapper class processes the input file line by line. It uses a StringTokenizer to break each line into individual words. A loop iterates through these words, and each word is emitted as a key-value pair, where the key is the word itself and the value is 1. This approach counts every word. However, as a specific project constraint, only words with a length of three or more characters are processed and emitted.

### Reducer Logic

The Reducer receives the key-value pairs from the Mapper, where all identical keys (words) are grouped together. It then iterates through this group and sums the values (which are all 1s). The final output is a new key-value pair for each unique word, where the key is the word and the value is its total count. This result gives the final word count for the entire input file.

## Execution Steps

The following steps outline how to build and run the MapReduce job from a clean environment.

Start the Hadoop Cluster: Launch the Docker containers for the Hadoop cluster.

```bash
docker compose up -d
```

### Build the Project: Compile the Java code and package it into a JAR file using Maven.

```bash 
mvn clean package
```
### Prepare the Environment: Copy the generated JAR file and the input dataset into the running Hadoop container.

```bash

docker cp target/WordCountUsingHadoop-0.0.1-SNAPSHOT.jar resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/
docker cp shared-folder/input/data/input.txt resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### Access the Container: Connect to the resourcemanager container's bash shell.

```bash

docker exec -it resourcemanager /bin/bash
```
### Set Up HDFS: Create a directory in the Hadoop Distributed File System (HDFS) and copy the input file into it.

```bash

hadoop fs -mkdir -p /input/data
hadoop fs -put ./input.txt /input/data
```
### Execute the MapReduce Job: Run the main application using the hadoop jar command. This will process the input file and store the output in HDFS.

```bash
hadoop jar /opt/hadoop-3.2.1/share/hadoop/mapreduce/WordCountUsingHadoop-0.0.1-SNAPSHOT.jar com.example.controller.Controller /input/data/input.txt /output1
```
### View Output: Display the contents of the output file stored in HDFS.

```bash
hadoop fs -cat /output1/*
```

### Retrieve Output (Optional): Copy the output file from HDFS to the local machine for inspection.

```bash
exit
docker cp resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/output1/ shared-folder/output/
```
## Challenges Faced & Solutions

### Challenge: Output directory already exists error.

Description: When rerunning the MapReduce job, a common error message indicates that the specified output directory already exists. Hadoop's design prevents it from overwriting existing directories to avoid accidental data loss.

Solution: Before each execution, the hadoop fs -rm -r /output1 command was used to delete the old output directory. For a one-time test, the output directory name was simply changed from /output to /output1.

## Sample Input: 
 ```bash
Rain is a vital form of precipitation consisting of liquid water droplets that fall from the sky, sustaining life by watering crops, filling rivers and lakes, and recharging groundwater. The unique, fresh scent of rain, known as petrichor, can be smelled after a dry spell, and while heavy rains can cause floods, a gentle rain makes the world look fresh and green. Raindrops themselves are not teardrop-shaped but are instead the result of condensation from water vapor in the atmosphere.  
   ```

## Expected output: 
 ```bash
and     4
the     4
from    2
can     2
are     2
fresh   2
water   2
Rain    1
sustaining      1
makes   1
vapor   1
dry     1
scent   1
watering        1
lakes,  1
unique, 1
consisting      1
rain,   1
while   1
teardrop-shaped 1
Raindrops       1
instead 1
known   1
droplets        1
filling 1
not     1
The     1
themselves      1
gentle  1
spell,  1
crops,  1
life    1
after   1
fall    1
world   1
rivers  1
floods, 1
vital   1
precipitation   1
green.  1
liquid  1
condensation    1
rain    1
rains   1
recharging      1
smelled 1
cause   1
look    1
but     1
petrichor,      1
result  1
that    1
sky,    1
atmosphere.     1
form    1
heavy   1
groundwater.    1
   ```
