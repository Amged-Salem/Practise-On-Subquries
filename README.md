# SQL Queries with Subqueries

```sql
-- 1. Subquery in the `FROM` Clause

-- Find products that have a price higher than the average price of all products:
SELECT * 
FROM (
    -- Subquery
    SELECT
        productid,
        price,
        AVG(price) OVER() AS AvgPrice
    FROM sales.products
) t 
WHERE price > AvgPrice;

-- Rank customers based on their total amount of sales:
SELECT *, 
    RANK() OVER (ORDER BY TotalSales DESC) AS Customer_Rank
FROM (
    -- Subquery
    SELECT
        CustomerID,
        SUM(sales) AS TotalSales
    FROM Sales.Orders
    GROUP BY CustomerID
) t;

-- 2. Subquery in the `SELECT` Clause

-- Show product IDs, names, and the total number of orders:
SELECT
    ProductID,
    product,
    price,
    (SELECT COUNT(*) FROM sales.orders) AS NumberOfOrders
FROM sales.Products;

-- 3. Subquery in a `JOIN`

-- Show all customer details and their total number of orders:
SELECT * 
FROM sales.Customers c
LEFT JOIN (
    SELECT customerid, COUNT(*) AS Total_Orders
    FROM sales.orders
    GROUP BY CustomerID
) t
ON c.CustomerID = t.CustomerID;

-- 4. Subquery in the `WHERE` Clause

-- Find products that have a price higher than the average price of all products:
SELECT 
    productid,
    price,
    (SELECT AVG(price) FROM sales.products) AS AvgPrice
FROM sales.products
WHERE price > (SELECT AVG(price) FROM sales.products);

-- Show the details of orders made by customers living in Germany:
SELECT 
    orderid,
    customerid,
    sales
FROM sales.orders
WHERE customerid IN (
    SELECT customerid 
    FROM sales.Customers
    WHERE country = 'Germany'
);

-- 5. Subquery with `!=`

-- Find female employees whose salaries are greater than the salaries of any male employees:
SELECT employeeid, firstname, salary
FROM Sales.Employees
WHERE gender = 'F'
AND salary > ANY (
    SELECT salary 
    FROM Sales.Employees 
    WHERE gender = 'M'
);

-- 6. Correlated Subqueries

-- Show all customer details and find the total orders of each customer:
SELECT *, 
    (SELECT COUNT(*) 
     FROM sales.orders o 
     WHERE o.customerid = c.customerid) AS TotalSales
FROM sales.customers c;
