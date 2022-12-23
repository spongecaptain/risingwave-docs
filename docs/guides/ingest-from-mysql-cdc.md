---
id: ingest-from-mysql-cdc
title: Ingest data from MySQL CDC
description: Ingest data from MySQL CDC.
slug: /ingest-from-mysql-cdc
---

Change Data Capture (CDC) refers to the process of identifying and capturing data changes in a database, then delivering the changes to a downstream service in real time.

MySQL has a binary log (binlog) that records all operations in the order in which they are committed to the database. This includes changes to table schemas as well as changes to the data in tables. MySQL uses the binlog for replication and recovery.

RisingWave supports ingesting row-level data (`INSERT`, `UPDATE`, and `DELETE` operations) from the binlog of a MySQL database.

You can ingest CDC data from MySQL in two ways:

- Using the direct MySQL CDC connector
  This connector is included in RisingWave. With this connector, RisingWave can connect to MySQL directly to obtain data from the binlog without starting additional services. Use this approach if Kafka is not part of your technical stack.

- Using the [Debezium connector for MySQL](https://debezium.io/documentation/reference/stable/connectors/mysql.html)
  You can use this connector to propogate data change events to Kafka topics, and then create a source in RisingWave to consume data from the Kafka topics. Us this approach is Kafka is part of your technical stack.


## Using the direct MySQL CDC connector

### Set up MySQL

Before using the MySQL CDC connector, you need to complete several configurations on MySQL. For details, see [Setting up MySQL](https://debezium.io/documentation/reference/stable/connectors/mysql.html#setting-up-mysql).

### Create a materialized source using the CDC connector

To ensure all data changes are captured, you need to create a materialized source in RisingWave using the direct CDC connector. Use the `CREATE MATERIALIZED SOURCE` command to create a materialized source. 


#### Syntax

```sql
CREATE MATERIALIZED SOURCE [ IF NOT EXISTS ] source_name (
   column_name data_type [ PRIMARY KEY ], ...
   PRIMARY KEY ( column_name, ... )
) 
WITH (
   connector='cdc',
   database.hostname = '',
   database.port = '',
   database.user = '',
   database.password = '',
   database.name = '',
   table.name = ''
) 
ROW FORMAT { DEBEZIUM_JSON | MAXWELL };
```

#### Example

```sql
CREATE MATERIALIZED SOURCE orders (
   order_id INT,
   order_date BIGINT,
   customer_name STRING,
   price DECIMAL,
   product_id INT,
   order_status SMALLINT,
   PRIMARY KEY (order_id)
) WITH (
 connector = 'cdc',
 database.hostname = '127.0.0.1',
 database.port = '3306',
 database.user = 'root',
 database.password = '123456',
 database.name = 'mydb',
 table.name = 'orders'
) ROW FORMAT DEBEZIUM_JSON;
```

## Debezium connector for MySQL

### Set up MySQL

Before using the Debezium connector for MySQL, you need to complete several configurations on MySQL. For details, see [Setting up MySQL](https://debezium.io/documentation/reference/stable/connectors/mysql.html#setting-up-mysql).

### Deploy the MySQL connector

You need to download and configure the connector, and then add the configuration to your Kafka Connect cluster. For details, see [Deploying the MySQL connector](https://debezium.io/documentation/reference/stable/connectors/mysql.html#mysql-deploying-a-connector).


### Create a materialized Kafka source

To ensure all data changes are captured, you need to create a materialized source in RisingWave. Use the `CREATE MATERIALIZED SOURCE` command to create a materialized source for Kafka. 


:::note

Currently, RisingWave only supports materialized CDC sources with primary keys, and the data format must be Debezium JSON or Maxwell JSON.

:::

#### Syntax

```sql
CREATE MATERIALIZED SOURCE [ IF NOT EXISTS ] source_name (
   column_name data_type [ PRIMARY KEY ], ...
   PRIMARY KEY ( column_name, ... )
) 
WITH (
   connector='kafka',
   field_name='value', ...
) 
ROW FORMAT { DEBEZIUM_JSON | MAXWELL };
```


#### Parameters

|Field|	Required?| 	Notes|
|---|---|---|
|topic|Yes|Address of the Kafka topic. One source can only correspond to one topic.|
|properties.bootstrap.server	|Yes|Address of the Kafka broker. Format: `'ip:port,ip:port'`.	|
|properties.group.id	|Yes|Name of the Kafka consumer group	|
|scan.startup.mode|No|The Kafka consumer starts consuming data from the commit offset. This includes two values: `'earliest'` and `'latest'`. If not specified, the default value `earliest` will be used.|
|scan.startup.timestamp_millis|No|Specify the offset in milliseconds from a certain point of time.	|

#### `ROW FORMAT` parameters

- `DEBEZIUM_JSON` — [Debezium](https://debezium.io) is a log-based CDC tool that can capture row changes from various database management systems such as PostgreSQL, MySQL, and SQL Server and generate events with consistent structures. Supported serialization format: JSON.
- `MAXWELL` — [Maxwell](https://maxwells-daemon.io) is a log-based CDC tool that can capture row changes from MySQL and write them as JSON to Kafka.


#### Example

```sql
CREATE MATERIALIZED SOURCE source_name (
   column1 varchar,
   column2 integer,
   PRIMARY KEY (column1)
) 
WITH (
   connector='kafka',
   topic='user_test_topic',
   properties.bootstrap.server='172.10.1.1:9090,172.10.1.2:9090',
   scan.startup.mode='earliest',
   properties.group.id='demo_consumer_name'
) 
ROW FORMAT DEBEZIUM_JSON;
```
