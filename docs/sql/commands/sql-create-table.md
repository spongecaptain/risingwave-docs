---
id: sql-create-table
title: CREATE TABLE
description: Create a table.
slug: /sql-create-table
---

Use the `CREATE TABLE` command to create a new table. Tables consist of fixed columns and insertable rows. Rows can be added using the [`INSERT`](sql-insert.md) command.

:::info
To ingest data streams, you should [create sources](sql-create-source.md) instead.
:::


import Drawer from '@theme/Drawer';
import {Diagram, Stack, Sequence, Optional, Terminal, Comment, NonTerminal, ZeroOrMore, Choice} from '@theme/RailroadDiagram'

export const svg = new Diagram(
    new Stack(
        new Sequence(
            new Terminal('CREATE TABLE'), 
            new Optional('IF NOT EXIST', 'skip'), 
            new Optional(
                new Sequence(
                    new Terminal('schema-name'), 
                    new Terminal('.')
                    ), 
                    'skip'),
            new Terminal('table-name'), 
                    ), 
        new Sequence(
            new Choice(0,
                new Sequence(
                    new Terminal('('),
                    new Terminal ('column_name'),
                    new Terminal ('data_type'),
                    new ZeroOrMore (new Sequence ( new Terminal ('column_name'), new Terminal ('data_type')), ',')
                ),
                new Sequence(
                    new Terminal('AS'), 
                    new Terminal('select-query')))))
                    );

<Drawer SVG={svg} />

```js
export const svg = new Diagram(new Stack(new Terminal('CREATE TABLE'),
  new Optional('IF NOT EXIST', 'skip'),
  new Optional(new Sequence(new Comment('schema-name'), new Comment('.')), 'skip'),
  new Comment('table-name')));
```

## Syntax

```sql
CREATE TABLE [ IF NOT EXISTS ] table_name (
    col_name data_type [ PRIMARY KEY ],
    ...
    [ PRIMARY KEY (col_name, ... ) ]
);
```

:::note
For tables with primary key constraints, if you insert a new data record with an existing key, the new record will overwrite the existing record.
:::

:::note

Names and unquoted identifiers are case-insensitive. Therefore, you must double-quote any of these fields for them to be case-sensitive.

:::

## Parameters

| Parameter| Description|
|-----------|-------------|
|*table_name*    |The name of the table. If a schema name is given (for example, `CREATE TABLE <schema>.<table> ...`), then the table is created in the specified schema. Otherwise it is created in the current schema.|
|*col_name*      |The name of a column.|
|*data_type*|The data type of a column. With the `struct` data type, you can create a nested table. Elements in a nested table need to be enclosed with angle brackets ("<\>"). |

## Examples

The statement below creates a table that has three columns.

```sql
CREATE TABLE taxi_trips(
    id VARCHAR,
    distance DOUBLE PRECISION,
    city VARCHAR
);
```

The statement below creates a table that includes nested tables.

```sql
CREATE TABLE IF NOT EXISTS taxi_trips(
    id VARCHAR,
    distance DOUBLE PRECISION,
    duration DOUBLE PRECISION,
    fare STRUCT<
      initial_charge DOUBLE PRECISION, 
      subsequent_charge DOUBLE PRECISION, 
      surcharge DOUBLE PRECISION, 
      tolls DOUBLE PRECISION>);
```


