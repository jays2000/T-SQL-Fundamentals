# T-SQL-Fundamentals

## CH. 1 - T-SQL Fundamentals
### CREATING A TABLE



# Ch.2 - Single Table Queries
## Elements of the SELECT Statement
### FROM CLAUSE
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
