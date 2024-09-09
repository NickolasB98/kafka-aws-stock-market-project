# Stock Market Data Engineering Project with Apache Kafka


## Overview

This project demonstrates a real-time data pipeline for processing stock market data using Apache Kafka. The pipeline ingests simulated stock market data, processes it in real time, and stores it in AWS for further analysis using services such as AWS Glue and Amazon Athena.

## Architecture

<img width="835" alt="image" src="https://github.com/user-attachments/assets/125021b9-9595-4141-ae34-4642fd9a6a74">

The architecture consists of the following components:

**Stock Market App Simulation:** Simulates real-time stock market data using Python.
**SDK Boto3:** Used for interacting with AWS services.
**Dataset:** A CSV file serving as the data source for stock price simulations.
**Kafka Producer:**
Sends the simulated stock market data to Kafka topics.
Hosted on an Amazon EC2 instance configured to manage Kafka brokers.
**Kafka Broker:**
Acts as a message broker that facilitates the transmission of data between producers and consumers.
Ensures data is replicated and available for real-time consumption.
**Kafka Consumer:**
Consumes data from Kafka topics and processes it in real time.
Stores processed data in Amazon S3 for long-term storage and analysis.
**Amazon S3:**
Stores the data consumed and processed by Kafka.
Acts as a data lake for raw and processed data, enabling easy integration with other AWS services.
**AWS Glue Crawler and Data Catalog:**
Automatically discovers data in S3 and creates metadata tables in the AWS Glue Data Catalog.
Makes the data queryable using Amazon Athena.
**Amazon Athena:**
Provides SQL-based querying capabilities on the data stored in S3.
Used for running analytics and generating insights from the stock market data.

## Getting Started

**Prerequisites**

1. Python 3.x
2. Apache Kafka
3. AWS Account
4. AWS CLI configured with appropriate permissions
5. Kafka Python Libraries (kafka-python, boto3, pandas)


## Project Setup Instructions for Apache Kafka on AWS EC2

This guide provides step-by-step instructions for setting up Apache Kafka on an AWS EC2 instance. Kafka is a distributed event streaming platform used for building real-time data pipelines and streaming applications. This setup involves downloading Kafka, configuring Zookeeper, starting Kafka servers, and creating producer and consumer services.

### Prerequisites
1. An AWS EC2 instance running Amazon Linux 2.
2. SSH access to your EC2 instance.
3. Basic knowledge of terminal commands.

   
### Setup Instructions

1. Download and Extract Kafka
   
Download Kafka version 3.8.0 and extract it.
```
# Download Kafka
wget https://downloads.apache.org/kafka/3.8.0/kafka_2.12-3.8.0.tgz

# Extract the downloaded Kafka archive
tar -xvf kafka_2.12-3.8.0.tgz
```

2. Install Java
   
Ensure that Java is installed on your EC2 instance as Kafka requires Java to run.

```
# Check the installed Java version
java -version

# If Java is not installed, install Java 1.8.0
sudo yum install java-1.8.0-openjdk

# Verify the Java installation
java -version
```

3. Start Zookeeper
   
Kafka requires Zookeeper to manage its clusters. Start Zookeeper by running the following command in the Kafka directory.

```
# Navigate to the Kafka directory
cd kafka_2.12-3.8.0

# Start Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties
```

4. Start Kafka Server
   
Start the Kafka server in a new terminal session.

Open a new terminal session and SSH into your EC2 instance.
Set Kafka Heap Options to optimize memory usage (optional).
```
# Set Kafka Heap Options (optional)
export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"

# Navigate to the Kafka directory
cd kafka_2.12-3.8.0

# Start the Kafka server
bin/kafka-server-start.sh config/server.properties
```
Important: Configure Kafka to Use Public IP
Modify the server.properties file to allow connections from external IPs by changing the ADVERTISED_LISTENERS property.

```
# Edit the server.properties file
sudo nano config/server.properties

# Change ADVERTISED_LISTENERS to the public IP of your EC2 instance
advertised.listeners=PLAINTEXT://<your-public-ip>:9092
```
Replace <your-public-ip> with your EC2 instance's actual public IP address.

5. Create a Kafka Topic

Create a topic to allow data to be sent and consumed by Kafka producers and consumers.

```
# Open a new terminal session and SSH into your EC2 instance again

# Navigate to the Kafka directory
cd kafka_2.12-3.8.0

# Create a Kafka topic named 'demo_testing'
bin/kafka-topics.sh --create --topic demo_testing --bootstrap-server <your-public-ip>:9092 --replication-factor 1 --partitions 1
```

6. Start the Kafka Producer
   
The producer will send messages to the Kafka topic.

```
# Start the Kafka producer
bin/kafka-console-producer.sh --topic demo_testing --bootstrap-server <your-public-ip>:9092
```

7. Start the Kafka Consumer
   
The consumer will read messages from the Kafka topic.

Open a new terminal session and SSH into your EC2 instance again


```
# Navigate to the Kafka directory
cd kafka_2.12-3.8.0

# Start the Kafka consumer
bin/kafka-console-consumer.sh --topic demo_testing --bootstrap-server <your-public-ip>:9092
```

### Conclusion

This guide helps you set up Apache Kafka on an EC2 instance, configure necessary components, and demonstrates how to create a producer and consumer to stream data. Ensure that you replace <your-public-ip> with the actual public IP of your EC2 instance. This setup can be used as a base for further Kafka exploration and real-time data processing projects.

