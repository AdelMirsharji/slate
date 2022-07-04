---
title: Hamta Sharif ThingsBoard API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - yaml
  - ini

toc_footers:

  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for what is ThingsBoard and how to use it
---

# Introduction

Welcome to the **Hamta Sharif ThingsBoard API**! You can use our API to understannd what is **ThingsBoard** and how to use this open-source IoT platform in your IoT projects.

We have language bindings in Java! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Architecture

ThingsBoard is designed to be:

* **Scalabe**: horizontally scalable platform, build using leading open-source technologies.
* **Fault-tolerant**: no single-point-of-failure, every node in the cluster is identical.
* **Robust and efficient**: single server node can handle tens or even hundreds thousands of devices depending on         use-case. ThingsBoard cluster can handle millions of devices.
* **Durable**: never lose your data. ThingsBoard supports various queue implementations to provide extremely high message durability.
* **Customizable**: adding new functionality is easy with customizable widgets and rule engine nodes.

The diagram below shows key system components and interfaces they provide.

![Diagram-1](/images/diagram-1.png)
![Diagram-1_legends](/images/diagram-1_legends.png)

## ThingsBoard Transports
ThingsBoard provides multiple machine to machine network protocols such as: **MQTT**,**HTTP**,**CoAP** and **LwM2M**. Each of the protocol APIs are provided by a separate server component and is part of ThingsBoard “Transport Layer”. MQTT Transport also provides Gateway APIs to be used by gateways that represent multiple connected devices and/or sensors.

Once the Transport receives the message from device, it is parsed and pushed to durable Message Queue. The message delivery is acknowledged to device only after corresponding message is acknowledged by the message queue.

## ThingsBoard Core
ThingsBoard Core is responsible for handling **REST API** calls and WebSocket **subscriptions**. It is also responsible for storing up to date information about active device sessions and monitoring device connectivity state. ThingsBoard Core uses Actor System under the hood to implement actors for main entities: tenants and devices. Platform nodes can join the cluster, where each node is responsible for certain partitions of the incoming messages.

## ThingsBoard Rule Engine
ThingsBoard Rule Engine is the heart of the system and is responsible for processing incoming **messages**.
ThingsBoard Rule Engine may operate in two modes: **shared** and **isolated**. In shared mode, rule engine process messages that belong to multiple tenants. In isolated mode Rule Engine may be configured to process messages for specific tenant only.

## ThingsBoard Web UI
ThingsBoard provides a lightweight component written using Express.js framework to host static web ui content. Those components are **completely stateless** and no much configuration available. The static web UI contains application bundle. Once it is loaded, the application starts using the REST API and WebSockets API provided by ThingsBoard Core.

# ThingsBoard Deployment
ThingsBoard platform can be delpoyed in multiple ways:

* **On-premise** or **Cloud**
* **Standalone** or **Cluster mode**
* **Monolithic** or **Microservises**

## On-premise VS Cloud deployment
ThingsBoard supports both **on-premise** and **cloud deployments**. ThingsBoard can be runned in production on **AWS**, **Azure**, **GCE** and **private data centers**. It is possible to launch ThingsBoard in the **private network** with no internet access at all.

## Standalone VS Cluster mode
Platform is designed to be horizontally scalable and supports automatic discovery of new ThingsBoard servers (nodes). All ThingsBoard nodes inside cluster are **identical** and are sharing the load. Since all nodes are identical there is no **“master”** or **“coordinator”** processes and there is no single point of failure. The load balancer of your choice may forward request from devices, applications and users to all ThingsBoard nodes.

## Monolithic vs Microservices 
It is possible to run the ThingsBoard platform as a monolithic application or as a set of microservices. Monolithic mode needs less efforts, knowledge and hardware resources to do the setup and low maintenance efforts. However, if you do need high availability or would like to scale to millions of devices, then microservices is a way to go. 

# ThingsBoard Database
ThingsBoard uses database to store **entities** (devices, assets, customers, dashboards, etc) and **telemetry data**(attributes, timeseries sensor readings, statistics, events). Three database options can be used in ThingsBoard platform:

> To configure database for this platform we can do:


```yaml
  ts_max_intervals: "${DATABASE_TS_MAX_INTERVALS:700}" # Max number of DB queries generated by single API call to fetch telemetry records
  
  ts:
    type: "${DATABASE_TS_TYPE:sql}" # cassandra, sql, or timescale (for hybrid mode, DATABASE_TS_TYPE value should be cassandra, or timescale)
  
  ts_latest:
    type: "${DATABASE_TS_LATEST_TYPE:sql}" # cassandra, sql, or timescale (for hybrid mode, DATABASE_TS_TYPE value should be cassandra, or timescale)
```
* **SQL**: Stores all entities and telemetry in SQL database. ThingsBoard authors recommend to use PostgreSQL and this is the main SQL database that ThingsBoard supports. It is possible to use HSQLDB for local development purposes.
* **Hybrid (PostgreSQL + Cassandra)**: Stores all entities in PostgreSQL database and timeseries data in Cassandra database.
* **Hybrid (PostgreSQL + TimescaleDB)**: Stores all entities in PostgreSQL database and timeseries data in Timescale database.

# Programming languages and third-party
ThingsBoard back-end is written in **Java**, but we also have some micro-services based on **Node.js**. ThingsBoard front-end is a **SPA** based on **Angular 9** framework.

# Installing ThingsBoard:

In this API document we will explain step by step how to install ThingsBoard on Ubuntu Server!

<aside class="notice">
To run ThingsBoard and PostgreSQL on a single machine you will need at least 1Gb of RAM. To run ThingsBoard and Cassandra on a single machine you will need at least 8Gb of RAM.
</aside>

### Step.1 Install Java 11 (OpenJDK)

>Run commands below in terminal in order to install Java 11

```shell
sudo apt update
sudo apt install openjdk-11-jdk
``` 
Finally you should see something like this:
![Jdk-1](/images/Jdk-1.png)

After that you should configure operating system to use OpenJDK 11 by default.

>To configure operating system to use OpenJDK 11 by default you can run these commands

```shell
sudo update-alternatives --config java
```

Then you can check installation using the command in right panel.

>To check Java installation use command below:

```shell
java --version
```
<aside class="success">
If you see results like picture below everything is fine and java installed successfully!
</aside>
![Jdk-2](/images/Jdk-2.png)

### Step.2 ThingsBoard service installation

>command to download package

```shell
wget https://github.com/thingsboard/thingsboard/releases/download/v3.3.4.1/thingsboard-3.3.4.1.deb
```

>command to install ThingsBoard as a service

```shell
sudo dpkg -i thingsboard-3.3.4.1.deb
```

first download package whit command in the right pannel.

Then install ThingsBoard as a service

<aside class="success">
Finally you should see something like picture below!
</aside>

![ThignsBoard-1](/images/ThingsBoard-1.png)


### Step.3 Configure ThingsBoard Database
ThingsBoard is able to use SQL or hybrid database approach.
In this API document we use **PostgreSQL**!

**PostgreSQL Installation**
See command in right pannel to install PostgreSQL database.

>How to install PostgreSQL using shell commands

```shell
#install wget if it is not already installed
sudo apt install -y wget
#import repository signing key
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
#add repository contents to your system
RELEASE=$(lsb_release -cs)
echo "deb http://apt.postgresql.org/pub/repos/apt/ ${RELEASE}"-pgdg main | sudo tee  /etc/apt/sources.list.d/pgdg.list
#install and run PostgreSQL service
sudo apt update
sudo apt -y install postgresql-12
sudo service postgresql start
```

if all commands run successfully the result will be like:
![postgresql](/images/postgreSQL-1.png)

Once PostgreSQL is installed you may want to create a new user or set the password for the the main user.

>The instructions below will help to set the password for main postgresql user.

```shell
sudo su - postgres
psql
\password
\q
```

Then, press “Ctrl+D” to return to main user console and connect to the database to create thingsboard DB.

>connecting to database to create thingsboard DB.

```shell
psql -U postgres -d postgres -h 127.0.0.1 -W
CREATE DATABASE thingsboard;
\q
```
<aside class="success">
Result should look like this:
</aside>
![PostgreSQL-2](/images/postgreSQL-2.png)


Now we should edit **ThingsBoard Configuration**, to do so use commands in right pannel.

>ThingsBoard configuration

```shell
sudo nano /etc/thingsboard/conf/thingsboard.conf
```
After openning add lines on the right pannel at the end of file!

>in the nano editor at the end of the file add these lines(go to ini panel):

```ini
# DB Configuration 
export DATABASE_TS_TYPE=sql
export SPRING_JPA_DATABASE_PLATFORM=org.hibernate.dialect.PostgreSQLDialect
export SPRING_DRIVER_CLASS_NAME=org.postgresql.Driver
export SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/thingsboard
export SPRING_DATASOURCE_USERNAME=postgres
export SPRING_DATASOURCE_PASSWORD=PUT_YOUR_POSTGRESQL_PASSWORD_HERE
# Specify partitioning size for timestamp key-value storage. Allowed values: DAYS, MONTHS, YEARS, INDEFINITE.
export SQL_POSTGRES_TS_KV_PARTITIONING=MONTHS
```

<aside class="notice">
Note that you sould write your entered password insted of **PUT_YOUR_POSTGRESQL_PASSWORD_HERE**!
</aside>

your thingsboard.conf in nano editor should look like this:
![nano_editor](/images/nano_editor.png)

use **CTRL+O** to save your changes, and use **CTRL+X** to exit nano editor.

### Step.4 Choose ThingsBoard queue service
ThingsBoard is able to use various messaging systems/brokers for storing the messages and communication between ThingsBoard services.

* **In Memory** queue implementation is built-in and default. It is useful for development(PoC) environments and is not suitable for production deployments or any sort of cluster deployments.

* **Kafka** is recommended for production deployments. This queue is used on the most of ThingsBoard production environments now. It is useful for both on-prem and private cloud deployments. It is also useful if you like to stay independent from your cloud provider. However, some providers also have managed services for Kafka. See AWS MSK for example.

* **RabbitMQ** is recommended if you don’t have much load and you already have experience with this messaging system.

* **AWS SQS** is a fully managed message queuing service from AWS. Useful if you plan to deploy ThingsBoard on AWS.

* **Google Pub/Sub** is a fully managed message queuing service from Google. Useful if you plan to deploy ThingsBoard on Google Cloud.

* **Azure Service Bus** is a fully managed message queuing service from Azure. Useful if you plan to deploy ThingsBoard on Azure.

* **Confluent Cloud** is a fully managed streaming platform based on Kafka. Useful for a cloud agnostic deployments.

In Memory queue is built-in and enabled by default. No additional configuration steps required.

In this API we use Memory queue!

### Step 5. Run installation script
Once ThingsBoard service is installed and DB configuration is updated, you can execute the script on right pannel:

>loadDemo option will load demo data: users, devices, assets, rules, widgets.

```shell
sudo /usr/share/thingsboard/bin/install/install.sh --loadDemo
```

You should see ThingsBoard installation starting as below:
![installation_starting](/images/installation_starting.png)

### Step 6. Start ThingsBoard service
Execute the command to the right to start ThingsBoard.

>ThingsBoard starting command

```shell
sudo service thingsboard start
```
Once started, you will be able to open Web UI using the following link:
**http://localhost:8080/**

Final result will be like:
![thingsboard_service](/images/Thingsboard_service.png)

You can use **email address** and **password** below to login:

Email: sysadmin@thingsboard.org

Password: sysadmin

You should see a page like this:
![thingsboard_service_2](/images/thingsBoard_service_2.png)
