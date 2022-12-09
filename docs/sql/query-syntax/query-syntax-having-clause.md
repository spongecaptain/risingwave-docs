---
id: query-syntax-having-clause
slug: /query-syntax-having-clause
title: HAVING clause
---

The `HAVING` clause is optional and eliminates group rows that do not satisfy a given condition. `HAVING` is similar to the `WHERE` clause, but `WHERE` occurs before the grouping, and `HAVING` occurs after. 

This means that the `WHERE` clause places conditions on selected columns, whereas the `HAVING` clause places conditions on groups created by the GROUP BY clause.

Here's an example showing the position of the `HAVING` clause in a SELECT query:

```sql
SELECT column1, column2
FROM table1, table2
WHERE [ conditions ]
GROUP BY column1, column2
HAVING [ conditions ]
ORDER BY column1, column2
```

Basic `HAVING` clause example:

```sql
-- count the number of entries in the "addresses" table that belong to each different city
-- filtering out cities with a count below 40
SELECT city, COUNT(*)
FROM addresses
GROUP BY city
HAVING COUNT(*) >= 40;
```
