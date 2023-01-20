---
id: create-source-s3
title: Ingest data from databases with S3 source.
description: Ingest data from databases with S3 source.
slug: /create-source-s3
---
Use the SQL statement below to connect RisingWave to an Amazon S3 source.

## Syntax

```sql
CREATE [ MATERIALIZED ] SOURCE [ IF NOT EXISTS ] source_name 
[schema_definition]
WITH (
   connector='s3',
   field_name='value', ...
)
ROW FORMAT data_format; 
```

**schema_definition**:
```sql
(
   column_name data_type [ PRIMARY KEY ], ...
   [ PRIMARY KEY ( column_name, ... ) ]
)
```

:::info

For Avro and Protobuf data, do not specify `schema_definition` in the `CREATE SOURCE` or `CREATE MATERIALIZED SOURCE` statement. The schema should be provided in a Web location in the `ROW SCHEMA LOCATION` section.

:::

:::note

RisingWave performs primary key constraint checks on materialized sources but not on non-materialized sources. If you need the checks to be performed, please create a materialized source.

For materialized sources with primary key constraints, if a new data record with an existing key comes in, the new record will overwrite the existing record. 

:::

## Parameters

|Field|Notes|
|---|---|
|`MATERIALIZED`| When you materialize a source, you choose to persist the data from the source in RisingWave.|
|s3.service_name	|Required. The service region.|
|s3.bucket_name	|Required. The name of the bucket the data source is stored in.	|
|match_pattern| Conditional.|
|s3.credentials.access| Conditional. This field indicates the access key ID of AWS. It must appear in pairs with s3.credentials.secret.|
|s3.credentials.secret| Conditional. This field indicates the secret access key of AWS. It must appear in pairs with s3.credentials.access.|


## Example
Here is an example of connecting RisingWave to an S3 source to read data from individual streams.

```sql
CREATE TABLE s(
    id int,
    name varchar,
    age int,
    primary key(id)
) WITH (
    connector = 's3',
    s3.region_name = 'ap-southeast-2',
    s3.bucket_name = 'example-s3-source',
    s3.credentials.access = 'xxxxx',
    s3.credentials.secret = 'xxxxx'
) ROW FORMAT CSV [WITHOUT HEADER] DELIMITED BY ',';
```