---
id: query-syntax-group-by-clause
slug: /query-syntax-group-by-clause
title: GROUP BY clause
---

The `GROUP BY` clause groups rows in a table with identical data, thus eliminating redundancy in the output and aggregates that apply to these groups.

Additionally, all tuples with matching data in the grouping columns (i.e., all tuples that belong to the same group) will be combined. The values of the grouping columns are unchanged, and any other columns can be combined using an aggregate function (such as `COUNT`, `SUM`, `AVG`, etc.).

The `GROUP BY` clause follows the `WHERE` clause in a `SELECT` statement and can precede the optional `ORDER BY` clause.

Here is the basic syntax of the `GROUP BY` clause:

```sql
SELECT column_list
FROM table_name
WHERE [ conditions ]
GROUP BY column1, column2....columnN
ORDER BY column1, column2....columnN
```

You can use more than one column in the `GROUP BY` clause.


Basic `GROUP BY` example:


Suppose that we have a table,`sales_commissions`, that consists of these columns: `id`, `name`,`age`, and `commission_amount`.

| id | name  | age | commission_amount |
|----|-------|-----|-------------------|
|  1 | Paul  |  32 |  20000            |
|  2 | Allen |  25 |  15000            |
|  3 | Teddy |  23 |  20000            |
|  4 | Mark  |  25 |  65000            |
|  5 | David |  27 |  85000            |
|  6 | Kim   |  22 |  45000            |
|  7 | James |  24 |  10000            |
|  8 | Paul  |  24 |  20000            |
|  9 | James |  44 |   5000            |
| 10 | James |  45 |   5000            |


To obtain the total amount of sales commissions for each person, we could use the following `GROUP BY` query using the optional `ORDER BY` clause:

```sql
SELECT name, SUM(commission_amount)
FROM sales_commissions
GROUP BY name 
ORDER BY name
```

Output:

| name  |  sum  |
|-------|-------|
| Allen | 15000 |
| David | 85000 |
| James | 20000 |
| Kim   | 45000 |
| Mark  | 65000 |
| Paul  | 40000 |
| Teddy | 20000 |