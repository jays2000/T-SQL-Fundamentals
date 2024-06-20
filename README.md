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



  # CH. 3 Joins
  - Table operator - Operates on input tables, applies logical processing phases, returns a table result in the FROM clause.
  - JOIN table operator acts on 2 input tables.
  - Combines table together
 
    ## CROSS JOINS
    - Joins 2 tables together and returns a Cartesian Product of the tables as a result.
    - implements 1 Logical processing phase - the Cartesian Product
    - Cartesion Product - each row in one input table matches all rows in the other input table
    - if you have m rows in one table and n rows in the other table, you get m x n rows in the result table.
      ```
      SELECT C.custid, E.empid
      FROM Sales.Customers AS C
        CROSS JOIN HR.Employees AS E;
      ```

    ## Self JOIN
    - Joins an instance of a table with itself
    - supported by all JOIN types (cross, inner, outer)
    - Joins multiple instances of the same table
      ```
      SELECT E1.empid, E1.firstname, E1.lastname,
             E2.empid, E2.firstname, E2.lastname
      FROM HR.Employees AS E1
        CROSS JOIN HR.Employees AS E2;
      ```

    ## INNER JOIN
    - Joins 2 tables based on a JOIN condition
    - 2 logical query processsing phases
    - Applies Cartesion Product phase, then applies filter phase to filter rows using ON clause
    - Must include ON clause
    - ON Clause/ JOIN condition - specifies the predicate used to filter rows
      ```
      SELECT E.empid, E.firstname, E.lastname, O.orderid
      FROM HR.Employees AS E
        INNER JOIN Sales.Orders
          ON E.empid = O.empid;
      ```

    ## Composite JOIN
    - Join where you need to match multiple attributes from each table
    - Usually used when a primary-foreign key relationship is based on multiple attributes
      ```
      FROM dbo.Tabl1 AS T1
        INNER JOIN dbo.Table2 AS T2
        ON T1.col1 = T2.col1 AND T1.col2 = T2.col2
      ```

    ## Multi-Join queries
    
