# Stock Market Data Engineering Project with Apache Kafka


## Overview

This project demonstrates a real-time data pipeline for processing stock market data using Apache Kafka. The pipeline ingests simulated stock market data, processes it in real time, and stores it in AWS for further analysis using services such as AWS Glue and Amazon Athena.

## Architecture

<img width="835" alt="image" src="https://github.com/user-attachments/assets/125021b9-9595-4141-ae34-4642fd9a6a74">

The architecture consists of the following components:

**Stock Market App Simulation:** Simulates real-time stock market data using Python.

**SDK Boto3:** Used for interacting with AWS services.

**EC2 Instance:** Hosts the Kafka broker, ensuring reliable and scalable message delivery between the stock market data producer and consumer.

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

### Create the EC2 Instance in AWS

**Type:** Choose an instance type that meets the performance and scalability requirements of the project. Personally, my project ran smoothly with t2.micro (also free tier eligible)

**Operating System:** Recommended: Amazon Linux 2 AMI (HVM), SSD Volume Type (64-bit x86) 

**Security Group**: Configure a security group to allow inbound traffic on the Kafka port (typically 9092) from your producer and consumer applications. It is recommended that other inbound traffic be restricted to minimize security risks, especially in a production environment.

### Access the EC2 through the CLI:

Create and download as csv one Key Pair during EC2 creation.

<img width="377" alt="image" src="https://github.com/user-attachments/assets/aedcb372-820f-4179-abb3-688640f3f415">

Import the csv into the project's folder.

Make the connection with the following code:

```
ssh -i /path/to/your-key.pem ec2-user@your-instance-public-dns

```


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


Ensure that you replace <your-public-ip> with the actual public IP of your EC2 instance. This setup can be used as a base for further Kafka exploration and real-time data processing projects.


## Configure Kafka in Python Notebooks and Connect with AWS S3

Now that we have manually configured our Kafka components, lets move on to creating this connection programmatically in Python notebooks, and forming the connection with AWS S3 using a secret Key ID and Value.

**The notebook KafkaProducer.ipynb**

<img width="840" alt="image" src="https://github.com/user-attachments/assets/4d53ede4-f3a5-46fc-96ee-c205acdabacb">

<img width="839" alt="image" src="https://github.com/user-attachments/assets/39696a0a-494b-4ced-a773-ac9c5a5cb820">

##The notebook KafkaConsumer.ipynb**

<img width="783" alt="image" src="https://github.com/user-attachments/assets/985078a4-1f4d-4b35-81d6-111fb2200211">

<img width="797" alt="image" src="https://github.com/user-attachments/assets/d69e491e-d700-4708-9cbb-2674dfcb4fb6">

## Connection to AWS

After running both notebooks for a while, we observe new data in our S3 Bucket, getting imported every second (or every n seconds depending on the sleep(n) function in **KafkaProducer.ipynb**

**The S3 Bucket**

<img width="1439" alt="s3bucket" src="https://github.com/user-attachments/assets/bede940a-9524-4e70-8c66-f51586144f9a">

We create a Glue Crawler, which automatically crawls the schema of the newly created table based on the imported data.
As a source for the Crawler, we use our S3 Bucket with a '/' in the end, to choose all the inserted object files inside the folder.

<img width="1099" alt="crawler" src="https://github.com/user-attachments/assets/46f7fa7e-dc35-447b-a452-37fb499e6bfc">

The table is now created with the correct schema formed by the crawler, and ready to be queried and analysed in AWS Serverless SQL Engine: Athena

**AWS Athena**

<img width="1266" alt="athena_query" src="https://github.com/user-attachments/assets/c033a1f1-11b6-430e-bdf6-61a10637c6e5">


## Conclusion

The Stock Market Data Engineering Project showcases the integration of Apache Kafka and AWS services to create a robust, real-time data pipeline. This project demonstrates how Kafka, combined with AWS Glue, Amazon S3, and Athena, can handle large-scale, simulated stock market data with high efficiency.

By utilizing Kafka producers and consumers, data is ingested in real-time and stored securely in AWS, enabling scalable and low-latency data processing. The automated data discovery and cataloging through AWS Glue, followed by SQL-based analytics in Athena, allow users to gain valuable insights quickly.

This architecture not only highlights the power of Kafka for real-time data streaming but also leverages the AWS ecosystem for comprehensive data management, making it ideal for real-world applications in finance, trading, and beyond.
