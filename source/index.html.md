---
title: Hamta Sharif ThingsBoard API

language_tabs: # must be one of https://git.io/vQNgJ
  - YAML
  - Java

toc_footers:

  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

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

* **SQL**: Stores all entities and telemetry in SQL database. ThingsBoard authors recommend to use PostgreSQL and this is the main SQL database that ThingsBoard supports. It is possible to use HSQLDB for local development purposes.
* **Hybrid (PostgreSQL + Cassandra)**: Stores all entities in PostgreSQL database and timeseries data in Cassandra database.
* **Hybrid (PostgreSQL + TimescaleDB)**: Stores all entities in PostgreSQL database and timeseries data in Timescale database.


> To configure database for this platform we can do:

```YAML
  ts_max_intervals: "${DATABASE_TS_MAX_INTERVALS:700}" # Max number of DB queries generated by single API call to fetch telemetry records
  
  ts:
    type: "${DATABASE_TS_TYPE:sql}" # cassandra, sql, or timescale (for hybrid mode, DATABASE_TS_TYPE value should be cassandra, or timescale)
  
  ts_latest:
    type: "${DATABASE_TS_LATEST_TYPE:sql}" # cassandra, sql, or timescale (for hybrid mode, DATABASE_TS_TYPE value should be cassandra, or timescale)
```

# Programming languages and third-party
ThingsBoard back-end is written in **Java**, but we also have some micro-services based on **Node.js**. ThingsBoard front-end is a **SPA** based on **Angular 9** framework.

# Getting started with ThingsBoard:


> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -X DELETE \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

