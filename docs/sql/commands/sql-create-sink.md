---
id: sql-create-sink
title: CREATE SINK
description: Create a sink.
slug: /sql-create-sink

---

Use the `CREATE SINK` command to create a sink. A sink is a target that RisingWave can send data to. 

You can create a sink from a materialized source, a materialized view, a table, or a `SELECT` query.


## Syntax

```sql
CREATE SINK [ IF NOT EXISTS ] sink_name
[FROM sink_from | AS select_query]
WITH (
   connector='kafka',
   kafka.brokers='broker_address',
   kafka.topic='topic_address',
   format='format'
);
```

## Parameters


|Parameter | Description|
|---|---|
|sink_name| Name of the sink to be created. Required.|
|FROM sink_from clause| The direct source from which data will be output. It can be a materialized source, a materialized view, or a table. Either this clause or the AS SELECT statement must be specified.|
|AS select_query| A SELECT query that specifies the data to be output to the sink. Either this query or a FROM clause must be specified.|
|connector| Sink connector type. Currently, only `‘kafka’` is supported.|
|kafka.brokers|Address of the Kafka broker. Format: `‘ip:port’`. If there are multiple brokers, separate them with commas. |
|kafka.topic|Address of the Kafka topic. One sink can only correspond to one topic.|
|format	| Data format. Allowed formats:<ul><li> `append-only`: Output data with insert operations.</li><li> `debezium`: Output change data capture (CDC) log in Debezium format.</li></ul>.|

## Examples

```sql
CREATE SINK sink1 FROM mv1 
WITH (
   connector='kafka',
   kafka.brokers='localhost:9092',
   kafka.topic='test',
   format='append-only'
)
```

```sql
CREATE SINK sink2 AS SELECT 
avg(distance) as avg_distance, avg(duration) as avg_duration 
FROM taxi_trips
WITH (
   connector='kafka',
   kafka.brokers='localhost:9092',
   kafka.topic='test',
   format='append-only'
)

```