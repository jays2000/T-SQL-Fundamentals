# T-SQL-Fundamentals

## CH. 1 - T-SQL Fundamentals
### CREATING A TABLE



# Ch.2 - Single Table Queries
## Elements of the SELECT Statement
### SELECT statement - statement queries a table, applies changes, and returns a result (table)
```
SELECT empid, YEAR(orderdate) AS orderyear, COUNT(*) AS numorders FROM Sales.Orders
WHERE custid = 71
GROUP BY empid, YEAR(orderdate)
HAVING COUNT(*) > 1
ORDER BY empid, orderyear;
```
- SELECT statement clauses are processed in this order:\
  1. FROM
  2. WHERE
  3. GROUP BY
  4. HAVING
  5. ORDER BY
  6. SELECT
    
### FROM clause - specifies the name of the table to query
```
--Selects orderid, custid, empid, orderdate, freight attritubtes/columns from the Orders table in Sales schema
SELECT orderid, custid, empid, orderdate, freight
FROM Sales.Orders; 
```
### WHERE clause - used to filter rows returned from the FROM phase
-- specifies a logical expression (predicate) used to filter the rows
```
-- selects orderid, empid, orderdate, freight, custid columns/attributes from orders table 
-- and returns rows where the customer id is 71
SELECT orderid, empid, orderdate, freight, custid
FROM Sales.Orders
WHERE custid = 71;
```
### GROUP BY clause - arranges rows returned by previous logical processsing phase into groups\
-- You specify how rows are grouped in GROUP BY clause with elements/expressions\
-- Produces a group for each distinct combo of specified elements/expressions in the clause\
-- All phases after GROUP BY performs operations on groups instead of individual rows.\
-- Each group is represented by a single row < br / > 
```
-- Selects empid and order year from the orders table and groups them by empid and order year
SELECT empid, YEAR(orderdate) as orderyear
FROM Sales.Orders
WHERE custid = 71
GROUP BY empid, YEAR(orderdate)

```

### HAVING clause - Filters groups using a predicate
-- Only groups for which the predicate evaluates to TRUE are returned\
-- can use aggregate functions in the HAVING predicate\
```
-- Return empid and order year for groups with more than 1 row(order)
SELECT empid, YEAR(orderdate) as orderyear
FROM Sales.Orders
GROUP BY empid, YEAR(orderdate)
HAVING COUNT(*) > 1;
```

### SELECT clause - specifies the attributes/columns to return in the result table
-- AS clause - used as an alias\
```
SELECT orderid, orderdate
FROM Sales.Orders;
```
### ORDER BY clause - sorts the rows of the result 
-- guarantees order in a table\
-- specify elements(columns/attributes) to order\
-- Specify ASC-ascending (default) or DESC-descending order\
```
-- returns employee information sorted by newest hire date to oldest
SELECT empid, firstname, lastname, hiredate
FROM HR.Employees
ORDER BY hiredate DESC
```
