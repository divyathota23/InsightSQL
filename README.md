
# SQL Techniques for Efficient Data Management

Whether you're a seasoned data professional or just starting out, mastering SQL is key to unlocking insights from data. Here are some powerful concepts every SQL enthusiast should know—and apply effectively:

## 1. Joins for Building Relationships
Joins combine data from multiple tables based on shared columns:

**Inner Join:** Use this when you need matching records from both tables. For instance, joining a Products table with an Inventory table to find products that have available stock.

#### Query

```sql
SELECT p.product_name, i.stock_quantity
FROM products p
INNER JOIN inventory i ON p.product_id = i.product_id;

```



**Left Join:** This helps uncover insights like which products are not in stock. A Left Join returns all records from the Products table and matches those from the Inventory table, leaving NULL where no match exists.

#### Query
```sql
SELECT p.product_name, i.stock_quantity
FROM products p
LEFT JOIN inventory i ON p.product_id = i.product_id;
```


**Self Join:** When working with hierarchical data, such as employees and their supervisors, you can use a self-join to match employees to their corresponding managers.

#### Query
```sql
SELECT e.employee_name AS Employee, m.employee_name AS Manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;

```


## 2. Aggregation & Grouping for Insights
Aggregation functions like SUM, AVG, MIN, and MAX allow you to summarize data, and GROUP BY helps in organizing it:

**Basic Aggregation:** Use aggregation functions like COUNT() or AVG() to get overall statistics. For example, calculate the average price of products in a store.

#### Query
```sql
SELECT AVG(price) AS avg_price
FROM products;

```

**GROUP BY with HAVING:** Filter grouped data using the HAVING clause. For example, find the categories where the total sales are more than $100,000.
#### Query
```sql
SELECT category, SUM(sales) AS total_sales
FROM products
GROUP BY category
HAVING SUM(sales) > 100000;

```

**Multiple Aggregations:** Combine multiple metrics in one query, such as the total revenue and the total number of transactions by each sales representative.
#### Query
```sql
SELECT product_name, SUM(price * quantity) AS total_revenue, COUNT(*) AS total_sales
FROM sales
GROUP BY product_name;

```

**Conditional Aggregation:** Count how many products have been sold by category, applying conditional logic to analyze different product types.
#### Query
```sql
SELECT department, 
       COUNT(CASE WHEN gender = 'Male' THEN 1 END) AS male_count,
       COUNT(CASE WHEN gender = 'Female' THEN 1 END) AS female_count
FROM employees
GROUP BY department;

```


## 3. Window Functions for Advanced Analysis
Window functions allow you to perform calculations across a subset of your data without collapsing rows:

**ROW_NUMBER:** Rank employees based on their performance scores to identify top performers in each department.
#### Query
```sql
SELECT employee_name, performance_score, 
       ROW_NUMBER() OVER (PARTITION BY department ORDER BY performance_score DESC) AS rank
FROM employees;

```

**RANK vs DENSE_RANK:** Use RANK to assign ranks where a tie will leave gaps (e.g., 1, 2, 4) and DENSE_RANK to avoid gaps in rank (e.g., 1, 2, 3).
#### Query
```sql
SELECT product_name, sales, 
       RANK() OVER (ORDER BY sales DESC) AS rank,
       DENSE_RANK() OVER (ORDER BY sales DESC) AS dense_rank
FROM products;

```

**LEAD/LAG:** Compare sales this month with sales last month to see how performance has changed.
#### Query
```sql
SELECT month, sales, 
       LAG(sales, 1) OVER (ORDER BY month) AS last_month_sales
FROM monthly_sales;

```

**Moving Average:** Calculate the rolling 3-month average of monthly revenue to smooth out fluctuations.

#### Query
```sql
SELECT month, AVG(sales) OVER (ORDER BY month ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg_sales
FROM monthly_sales;

```

## 4. Subqueries for Solving Complex Problems
Subqueries break down complex queries into manageable steps:

**Subquery with IN:** Identify customers who have purchased from multiple categories by checking their customer_id in the orders table.
#### Query
```sql
SELECT customer_id, customer_name
FROM customers
WHERE customer_id IN (SELECT DISTINCT customer_id FROM orders);

```

**Correlated Subquery:** Compare each product’s price to the average price in its category to highlight expensive products in the catalog.
#### Query
```sql
SELECT product_name, price
FROM products p
WHERE price > (SELECT AVG(price) FROM products WHERE category = p.category);

```

**Subquery with EXISTS:** Find suppliers who have never supplied a product in the last 12 months.
#### Query
```sql
SELECT supplier_id, supplier_name
FROM suppliers s
WHERE NOT EXISTS (SELECT 1 FROM products p WHERE p.supplier_id = s.supplier_id AND p.supply_date > CURRENT_DATE - INTERVAL '1 YEAR');
```

**Nested Subquery** Find the employee who received the second-highest salary by ranking employees and selecting the one just below the maximum.
#### Query
```sql
SELECT MAX(salary) AS second_highest_salary
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

## 5. Set Operations for Comparative Analysis
Set operations like UNION, INTERSECT, and EXCEPT allow you to combine or compare datasets:

**UNION vs UNION ALL:** UNION merges datasets while removing duplicates, whereas UNION ALL retains all records, including duplicates. For example, combine the lists of students from two different courses.
#### Query
```sql
SELECT student_name FROM course_A
UNION
SELECT student_name FROM course_B;

```

**INTERSECT:** Find common records between two datasets. For example, identify customers who purchased both from the online store and the physical store.
#### Query
```sql
SELECT customer_id FROM online_orders
INTERSECT
SELECT customer_id FROM store_orders;

```

**EXCEPT:** Identify records that exist in one dataset but not in another, such as finding products in a store’s inventory but not in any recent sales order.
#### Query
```sql
SELECT customer_id FROM online_orders
EXCEPT
SELECT customer_id FROM store_orders;

```

## 6. Date and Time Analysis
Date functions are crucial for analyzing time-based data:

**Date Difference** Calculate the number of days between an employee’s hire date and their promotion date to analyze promotion timelines.
#### Query
```sql
SELECT employee_name, DATEDIFF(promotion_date, hire_date) AS days_to_promotion
FROM employees;

```

**Extracting Parts of a Date:** Identify customers who placed orders on weekends by extracting the weekday from the order date.
#### Query
```sql
SELECT order_id, order_date
FROM orders
WHERE EXTRACT(DAYOFWEEK FROM order_date) IN (1, 7);

```

**Date Truncation:** Aggregate data by year by truncating transaction dates to the first of the year.
#### Query
```sql
SELECT YEAR(transaction_date) AS year, SUM(amount) AS total_sales
FROM transactions
GROUP BY YEAR(transaction_date);

```

**Rolling Sales:** Track cumulative sales month-over-month for each product to identify seasonal trends.
#### Query
```sql
SELECT product_id, SUM(sales_amount) OVER (PARTITION BY product_id ORDER BY sale_date) AS cumulative_sales
FROM sales;
```

## 7. Performance Optimization for Scale
When working with large datasets, optimizing queries for speed is critical:


**Indexes:** Speed up search operations on frequently queried columns, but be mindful they can slow down inserts and updates. For instance, indexing product_id for fast lookups in the Sales table.
#### Query
```sql
CREATE INDEX idx_product_id ON sales(product_id);
```

**Query Optimization:** Simplify joins, filter early in the query, and avoid unnecessary calculations to enhance performance. For example, only select the required columns instead of using SELECT *.
#### Query
```sql
SELECT customer_name, order_id, order_date
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
WHERE order_date > '2024-01-01';

```

**Partitioning:** Break large datasets into smaller, manageable chunks based on certain criteria, like partitioning by region to speed up queries on sales data.
#### Query
```sql
CREATE TABLE sales_partitioned (
    sale_id INT,
    product_id INT,
    region VARCHAR(50),
    sales_amount DECIMAL(10, 2),
    sale_date DATE
)
PARTITION BY RANGE (region);

```

**EXPLAIN Statement:** Use the EXPLAIN statement to analyze query execution plans, helping identify slow queries or inefficient operations, like unnecessary full table scans.
#### Query
```sql
EXPLAIN SELECT * FROM sales WHERE product_id = 1001;

```