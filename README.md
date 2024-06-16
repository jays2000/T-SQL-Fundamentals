# T-SQL-Fundamentals

## CH. 1 - T-SQL Fundamentals
### CREATING A TABLE



# Ch.2 - Single Table Queries
## Elements of the SELECT Statement
### SELECT statement - statement queries a table, applies changes, and returns a result (table)
```
SELECT empid, YEAR(orderdate) AS orderyear, COUNT(*) AS numorders
FROM Sales.Orders
WHERE custid = 71
GROUP BY empid, YEAR(orderdate)
HAVING COUNT(*) > 1
ORDER BY empid, orderyear;
```
- SELECT statement clauses are processed in this order:
  1. FROM
  2. WHERE
  3. GROUP BY
  4. HAVING
  5. SELECT
  6. ORDER BY
    
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
### TOP filter - used to return the specified number of rows
  -- can specify a number or percentage
  ```
  -- return top 5 rows from the order tables ordered by hire date
  SELECT TOP (5) empid, firstname, lastname, hiredate
  FROM HR.Employees
  ORDER BY hiredate DESC
  ```

### CASE expression - a scalar expression, returns a single value based on a logical condition
- Simple and Searched CASE expressions
- Simple - compares one value with a list of possible values and returns the first match
  ```
  SELECT supplierid, COUNT(*) AS numproducts,
  CASE COUNT(*) % 2
    WHEN 0 THEN 'Even'
    WHEN 1 THEN 'Odd'
    ELSE 'Unknown'
  END AS countparity
  FROM Production.Products
  GROUP BY supplierid;
  ```
- Searched - more flexible than simple, allows predicates in WHEN clause
  ```
  SELECT orderid, custid, val,
  CASE
    WHEN val < 1000.00  THEN 'Less than 1000'
    WHEN val <= 3000.00 THEN 'Between 1000 and 3000'
    WHEN val > 3000.00  THEN 'More than 3000'
    ELSE 'Unknown'
  END AS valuecategory
  FROM Sales.OrderValues;
  ```
