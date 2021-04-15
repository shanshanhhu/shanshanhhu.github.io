---
title: SQL Statements
date: 2021-03-17 16:48:22 +0800
categories: [bigdata, mysql]
tags: [mysql]
---

# Some of The Most Important SQL Commands
### SELECT
```sql
SELECT column1, column2, ...
FROM table_name;

SELECT * FROM table_name;

SELECT DISTINCT column1, column2, ...
FROM table_name;

// where
SELECT column1, column2, ...
FROM table_name
WHERE condition;

// and, or, not
SELECT column1, column2, ...
FROM table_name
WHERE condition1 AND condition2 ANDcondition3 ...;

SELECT column1, column2, ...
FROM table_name
WHERE NOT condition;

// order by
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC;

// null
SELECT column_names
FROM table_name
WHERE column_name IS NULL;  // or WHERE column_name IS NOT NULL;

// select top
// (Mysql Syntax)
SELECT column_name(s)
FROM table_name
WHERE condition
LIMIT number;
//Oracle 12 Syntax:
SELECT column_name(s)
FROM table_name
ORDER BY column_name(s)
FETCH FIRST number ROWS ONLY;

// MIN(), MAX(), COUNT(), AVG(), SUM()
SELECT MIN(column_name)
FROM table_name
WHERE condition;

// LIKE
//There are two wildcards often used in conjunction with the LIKE operator:
//The percent sign (%) represents zero, one, or multiple characters
//The underscore sign (_) represents one, single character
//Note: MS Access uses an asterisk (*) instead of the percent sign (%), and a question mark (?) instead of the underscore (_).
SELECT column1, column2, ...
FROM table_name
WHERE columnN LIKE pattern;
```
- Wildcard Characters in MS Access
![](../assets/img/sample/SQL-Wildcard-Characters.png)


```sql
// IN
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);

SELECT column_name(s)
FROM table_name
WHERE column_name IN (SELECT STATEMENT);

// BETWEEN
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;

// AS---Aliases
SELECT column_name AS alias_name
FROM table_name;

SELECT column_name(s)
FROM table_name AS alias_name;

// JOIN : (INNER) JOIN, LEFT (OUTER) JOIN, RIGHT (OUTER) JOIN, FULL (OUTER) JOIN
SELECT Orders.OrderID, Customers.CustomerName, Orders.OrderDate
FROM Orders
INNER JOIN Customers ON Orders.CustomerID=Customers.CustomerID;
WHERE condition;
// Self JOIN
SELECT column_name(s)
FROM table1 T1, table1 T2  //T1 and T2 are different table aliases for the same table.
WHERE condition;

// UNION
1. Every SELECT statement within UNION must have the same number of columns
2. The columns must also have similar data types
3. The columns in every SELECT statement must also be in the same order

SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
// UNION ALL : allow duplicate values

// GROUP BY
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)   // The GROUP BY statement is often used with aggregate functions (COUNT(), MAX(), MIN(), SUM(), AVG()) to group the result-set by one or more columns.

ORDER BY column_name(s);

// HAVING
SELECT COUNT(CustomerID), Country
FROM Customers
WHERE condition   // where clause cannot use aggregate function.
GROUP BY Country
HAVING COUNT(CustomerID) > 5;
```


### UPDATE
```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```
### DELETE
```sql
DELETE FROM table_name WHERE condition;
```
### INSERT INTO
```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);

// If you are adding values for all the columns of the table, you do not need to specify the column names in the SQL query. However, make sure the order of the values is in the same order as the columns in the table.
INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```
### CREATE DATABASE
### ALTER DATABASE
### CREATE TABLE
### ALTER TABLE
### DROP TABLE
### CREATE INDEX
### DROP INDEX
