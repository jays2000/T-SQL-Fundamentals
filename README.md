# T-SQL-Fundamentals

## CH. 1 - T-SQL Fundamentals
### CREATING A TABLE



# Ch.2 - Single Table Queries
## Elements of the SELECT Statement
### FROM clause
-- specifies the name of the table to query
```
--Selects orderid, custid, empid, orderdate, freight attritubtes/columns from the Orders table in Sales schema
SELECT orderid, custid, empid, orderdate, freight
FROM Sales.Orders; 
```
### WHERE clause
-- specifies a logical expression (predicate) used to filter rows returned from the FROM phase
```
-- selects orderid, empid, orderdate, freight, custid columns/attributes from orders table 
-- and returns rows where the customer id is 71
SELECT orderid, empid, orderdate, freight, custid
FROM Sales.Orders
WHERE custid = 71;
```
### GROUP BY clause
-- arranges rows returned by previous logical processsing phase into groups\
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
