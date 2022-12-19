---
id: query-syntax-limit-clause
slug: /query-syntax-limit-clause
title: LIMIT clause
---

`LIMIT` is an output modifier. Logically it is applied at the end of the query, and the `LIMIT` clause restricts the number of rows fetched.

Note that while `LIMIT` can be used without an ORDER BY clause, the results might not be deterministic without the `ORDER BY` clause. This can still be useful, for example, when you want to inspect a quick snapshot of the data.

Example:

```sql
-- select the first 3 rows from the addresses table 
SELECT * FROM addresses LIMIT 3;
```