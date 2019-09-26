# SQL

## Some basic concepts to write down
I'm going through [a leetcode problem](https://leetcode.com/problems/exchange-seats/solution/) that combines a few concepts together. The problem itself is fairly straitforward: How would you go about reordering rows based on their initial location. Specifically, you want to reverse every pair of rows. Here are some of the tools that it uses

I'm using the [w3 schools sandbox](https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all) to try these queries out

`COUNT`
``` sql
SELECT COUNT(*) AS num_rows FROM customers
```

`CASE`
``` sql
  SELECT
    (CASE
        WHEN CustomerID % 2 != 0 THEN 'odd'
        ELSE 'even'
    END) AS even_or_odd
  FROM customers
```

`CROSS JOIN`: produces rows which combine each row from the first table with each row from the second table. Here, it's stated implicitly, using a comma
```sql
  SELECT *
  from (select count(*) as total from customers) as counts, customers
```

ascending order: we can order the table by the new value using the `ASC` keyword. Here's the final query  
```sql
SELECT
  (CASE
      WHEN CustomerID % 2 != 0 AND total != CustomerID THEN CustomerID + 1
      WHEN CustomerID % 2 != 0 AND total == CustomerID THEN CustomerID
        ELSE CustomerID - 1
     END) AS new_value, CustomerName
FROM (SELECT count(*) as total from customers) AS counts, customers
ORDER BY new_value ASC
```

## SQLite
``` sql
.tables
.mode column

SELECT trackid, name, composer, unitprice
FROM tracks LIMIT 10
```