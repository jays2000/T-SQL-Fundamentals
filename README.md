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
    - When more than 1 table operator appears in the FROM clause, table operators are logically processed in           order.
    - The result of the first table operator is used as the left input table to the second table operator and so       on.
    - The first JOIN operates on 2 base tables but all other joins get the result from the preceding JOINs as           their left input table.
      ```
      SELECT
      C.custid, C.companyname, O.orderid,
      OD.productid, OD.qty
      FROM Sales.Customers AS C
        INNER JOIN Sales.Orders AS O
          ON C.custid = O.custid
        INNER JOIN Sales.OrderDetails AS OD
          ON O.orderid = OD.orderid;
      ```

    ## OUTER JOINS
    - Adds rows from the preserved table with no matches in other input table to the result table.
    - includes inner rows and outer rows.
    - identitifies the rows with no matches from the preserved table in the other input table based on the ON           clause, and adds those rows to the result table produced by the first 2 phases of the JOIN.
    - 3 processing phases - Cartesian Product, filter, Add outer rows
    - Make a table the preserved table by using the keywords LEFT OUTER JOIN, RIGHT OUTER JOIN, or FULL OUTER           JOIN.
    - OUTER keyword is optional
    - LEFT - means that the table to the left of the JOIN keyword is preserved.
    - RIGHT - means that the table to the right of the JOIN keyword is preserved.
    - FULL - means that the table both tables to the left and right of the JOIN keyword is preserved.
    - NULLS are used as placeholders for the attributes in the nonpreserved side of the JOIN in outer rows.
      ```
      -- Returns customers and their orders, also returns customers without orders
      SELECT C.custid, C.companyname, O.orderid
      FROM Sales.Customers AS C
        LEFT OUTER JOIN Sales.Orders AS O
          ON C.custid = O.custid
      ```
    # Ch. 4 - Subqueries
    - Sub query - A query within a query used to avoid using separate queries for a solution
    - Outer query - Outermost query whose result is returned to the caller
    - Inner query (subquery) - result is used by the outer query.
        - Acts in place of an expression that is based on a constant or variable.
        - results of a subqery can change based on changes in the queried table.
    - Used to avoid the need for separate steps in your solution that stores intermediate results in a variables.
    ## Self Contained subqueries - Subquery independent of outer query tables.
    - Evaluated once, then the outer query used the results from the subquery
      ### Self contained Scalar subquery - returns a single value
      - can appear anywhere a single valued expression can appear (ex: WHERE clause)
      - MUST return a single value
        ```
        SELECT orderid, orderdate, empid, custid
        FROM Sales.Orders
        WHERE orderid = ( SELECT Max(O.orderid)
                FROM Sales.Orders AS O)
        ```
      ### Self Contained MultiValued subquery - returns multiple values as a column
      - IN predicate operates on a multivalued subquery
        ```
        WHERE empid IN (SELECT E.empid
                        FROM HR.Employees AS E
                        WHERE E.lastname LIKE N 'D%') 
        ```
    ## Correlated subqueries
    - subqueries that refer to attributes from tables in the outer query.
    - dependent on outer query tables
    - subquery is evaluated separately for each outer row in the logical query processing step in which the subquery appears.
      ```
      SELECT custid, orderid, orderdate, empid
      FROM Sales.Orders AS O1
      WHERE orderid = (SELECT MAX(O2.orderid)
                       FROM Sales.Orders AS O2
                       WHERE O2.custid = O1.custid)
      ```

      ## EXISTS predicate
      - accepts a subquery as input and returns True if subquery returns any rows, and false if not.
      - Uses 2 value predicate logic
        ```
        SELECT custid, companyname
        FROM Sales.Customers AS C
        WHERE country = N'Spain'
          AND EXISTS
            (SELECT * FROM Sales.Orders AS O
        WHERE O.custid = C.custid);
        ```
# Ch.5 - Table Expressions
- Table expression - An expression, typically a query, that returns a table result
- Used to help simplify code, improve maintainability, and encapsulate querying logic
- Named table expression is a table expression assigned a name and you can interact with it like a base table
- 4 types - Derived Tables, Common Table Expression (CTE), View, Inline Table-valued Function (ITVF)
- When queryihg a table expression, the inner query is merged with the outer query into one query against the underlying object
  ## Derived Tables
  - table expression defined in the FROM clause of the outer query
  - scope of existance is the outer query, once the query is finished the derived table is gone
  - Use parenthesis to define the derived table, followed by AS and the derived table name
    ```
    SELECT *
    FROM (SELECT custid, companyname
          FROM Sales.Customers
          WHERE country = N'USA') AS USACusts  <-- Derived Table name
    ```
  - For valid inner query in table expressions, order is not guaranteed (can't use order by clause for presentation), All columns must have names, all column names must be unqiue.
  - Arguments (variables, functions) can be used in inner query
  - Outer query can access aliases from inner query
    
  ## Common Table Expressions (CTE)
  - Table expression defined using WITH AS CTE_name, wrapped in parenthesis
  - more flexible than derived table
    ```
    WITH USACusts AS       <-- CTE name defined as USACusts
    (  
      SELECT custid, companyname
      FROM Sales.Customers            <---- Inner query for CTE defined in parenthesis
      WHERE country = N'USA'
    )
    SELECT * FROM USACusts       <--- The outer query against the CTE
    ```
  - CTE's are not nested, but separated by commas to define multiple CTE's
    - Each CTE can refer to all previously defined CTE's
    - Outer query can refer to all CTE's
  ```
  
  WITH C1 AS (
    SELECT YEAR(orderdate) AS orderyear, custid
    FROM Sales.Orders
  ),   <---- separated by comma
  C2 AS (
    SELECT orderyear, COUNT(DISTINCT custid) AS numcusts
    FROM C1
    GROUP BY orderyear
  )
  SELECT orderyear, numcusts
  FROM C2
  WHERE numcusts > 70;
  ```
  - Can refer to the same instance of a CTE in table operators like JOIN because the CTE is defined before the outer query's FROM clause
  ### Recursive CTE
  - Defined by at least 2 queries, the anchor member, and the recursive member
  - both queries must be compatible ( same num of columns and same data types in columns)
  ```
  WITH EmpsCTE AS
  (
    SELECT empid, mgrid, firstname, lastname
    FROM HR.Employees                           <--- anchor member - invoke only once to return table result
    WHERE empid = 2
  
  UNION ALL
  
    SELECT C.empid, C.mgrid, C.firstname, C.lastname
    FROM EmpsCTE AS P                                <--- recursive member - used to call the CTE recursively and uses the results from the last CTE unti         INNER JOIN HR.Employees AS C                            a NULL value is returned then recursive call stops
        ON C.mgrid = P.empid
  )
  SELECT empid, mgrid, firstname, lastname            <-- Outer query used to query the table results from the recursive CTE
  FROM EmpsCTE;

  ```

## View
- A reusable table expression that is stored as an object in the DB
- reusable unlike Derived Tables, and CTE's but treated the same way
- Can manage access to the view since it is an object
- Can add attributes and options like encryption to the view
```
CREATE OR ALTER VIEW Sales.USACusts  <-- define the view name
AS
SELECT custid, companyname
FROM Sales.Customers          <--- query for the view
WHERE country = N'USA'
GO 
```

## Inline Table-Valued Functions (ITVF)
- reusable table expression that supports input parameters
- also an object
- "parameterized view"
- allows input to be passed into function
  ```
  -- defines the itvf 
  GO
      CREATE OR ALTER FUNCTION dbo.GetCustOrders
        (@cid AS INT) RETURNS TABLE
      AS
      RETURN
        SELECT orderid, custid, empid, orderdate, requireddate,
          shippeddate, shipperid, freight, shipname, shipaddress, shipcity,
          shipregion, shippostalcode, shipcountry
        FROM Sales.Orders
        WHERE custid = @cid;
  GO

  SELECT * FROM dbo.GetCustOrders(5)   <--- query to use tvf and pass in parameters
  ```
## APPLY operator
- Table operator used in FROM clause , operates on 2 tables
- CROSS APPLY - applies the right table to each row of the left table and produces a table result.
  - Similar to JOIN  but can reference the table on the left side because it is evaluated first.
  - The right table is evaluated per row from the left table
  ```
  SELECT C.custid, A.orderid, A.orderdate
  FROM Sales.Customers AS C
    CROSS APPLY
      (SELECT TOP (3) orderid, empid, orderdate, requireddate
  FROM Sales.Orders AS O
  WHERE O.custid = C.custid
  ORDER BY orderdate DESC, orderid DESC) AS A;
  ```
- OUTER APPLY - returns rows from the left table even if there is no match
  - preserves all left table rows if no match
