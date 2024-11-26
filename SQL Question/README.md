
# SQL Questions

### Q1: Difference between RANK(), DENSE_RANK(), and ROW_NUMBER()
Explanation:

***ROW_NUMBER():*** Assigns a unique sequential integer to rows starting from 1.

***RANK():*** Assigns ranks to rows, but skips ranks for duplicate values.

***DENSE_RANK():*** Assigns ranks without skipping ranks for duplicate values.
Example (Assume a Scores table):
```
Student	  Subject	Marks
A	        Math	  90
B	        Math	  90
C	        Math	  80
D	        Math	  70
```
Query:

```sql
SELECT 
    Student,
    Marks,
    ROW_NUMBER() OVER (ORDER BY Marks DESC) AS RowNumber,
    RANK() OVER (ORDER BY Marks DESC) AS Rank,
    DENSE_RANK() OVER (ORDER BY Marks DESC) AS DenseRank
FROM Scores;
```

Result:
```
Student	 Marks	 RowNumber	Rank	DenseRank
A	        90	   1         1	        1
B	        90	   2	     1	        1
C	        80	   3	     3	        2
D	        70	   4	     4	        3
```
### Q2: Find the nth highest salary
Query:

```sql
WITH RankedSalaries AS (
    SELECT 
        Salary,
        RANK() OVER (ORDER BY Salary DESC) AS Rank
    FROM Employee
)
SELECT Salary
FROM RankedSalaries
WHERE Rank = N; -- Replace N with the desired value
```
### Q3: Find all employees under a specific manager, including their subordinates
Query:

```sql
WITH Hierarchy AS (
    SELECT EmpID, ManagerID
    FROM Employee
    WHERE ManagerID = @ManagerID -- Replace @ManagerID with the manager's ID
    UNION ALL
    SELECT e.EmpID, e.ManagerID
    FROM Employee e
    INNER JOIN Hierarchy h ON e.ManagerID = h.EmpID
)
SELECT EmpID
FROM Hierarchy;
```

### Q4: Cumulative salary of employees department-wise who joined in the last 30 days
Query:

```sql
SELECT 
    Dept,
    SUM(Salary) AS CumulativeSalary
FROM Employee
WHERE JoinDate >= DATEADD(DAY, -30, GETDATE())
GROUP BY Dept;
```
### Q5: Top 2 customers with the highest order amount for each product category
Query:

```sql
WITH RankedOrders AS (
    SELECT 
        CustomerID,
        ProductCategory,
        OrderAmount,
        RANK() OVER (PARTITION BY ProductCategory ORDER BY OrderAmount DESC) AS Rank
    FROM Customer
)
SELECT CustomerID, ProductCategory, OrderAmount
FROM RankedOrders
WHERE Rank <= 2;
```
