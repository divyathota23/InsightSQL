
# Ey interview Question

**Question:** The task is to find the average transaction amount for each customer who made more than 5 transactions in September 2023.


You have the following two tables:

### Transactions:

```sql
transaction_id (INT)	customer_id (INT)	transaction_date (DATE)	amount (DECIMAL)
1	                          1	                 2023-09-05	        100.00
2	                          1	                 2023-09-10	        150.00
3	                          1                  2023-09-15         200.00
4	                          1	                 2023-09-18	        120.00
5	                          1	                 2023-09-22	        180.00
6	                          1	                 2023-09-25	        140.00
7	                          2	                 2023-09-10     	300.00
8	                          2	                 2023-09-14	        250.00
9	                          2	                 2023-09-20     	500.00
10	                          2	                 2023-09-22	        100.00
11                            3	                 2023-09-05	        50.00
12                         	  3	                 2023-09-10	        75.00
13	                          3	                 2023-09-12	        80.00

```

### Customers:

```sql
customer_id (INT)	customer_name (VARCHAR)
1	                   Alice
2	                   Bob
3	                   Charlie

```

### Query

```sql
SELECT c.customer_name, 
       AVG(t.amount) AS average_transaction
FROM Customers c
JOIN Transactions t ON c.customer_id = t.customer_id
WHERE c.customer_id IN (
    SELECT customer_id
    FROM Transactions
    WHERE transaction_date BETWEEN '2023-09-01' AND '2023-09-30'
    GROUP BY customer_id
    HAVING COUNT(transaction_id) > 5
)
GROUP BY c.customer_name;
Expected Output:
customer_name	average_transaction
Alice	148.33
Bob	287.50
}
```

### Step-by-Step Explanation:
**1.Filter Transactions for September 2023:**
The subquery filters the Transactions table to only include transactions that occurred between September 1, 2023 and September 30, 2023.


**2.Identify Customers with More Than 5 Transactions:**
In the subquery, we group the filtered transactions by customer_id and count the number of transactions for each customer. We then use the HAVING clause to only include customers who made more than 5 transactions.

**3.Join Customers with Transactions:**
In the main query, we join the Customers table with the Transactions table based on the customer_id field to combine customer details with their transactions.

**4.Calculate Average Transaction Amount:**
The AVG(t.amount) function calculates the average transaction amount for each customer who meets the condition (more than 5 transactions in September).

**5.Group by Customer:**
Finally, we use GROUP BY c.customer_name to ensure that the result includes one row per customer, showing their name and the average transaction amount

### Expected Output:

```sql
customer_name	average_transaction
Alice	         148.33

```
- Alice made 6 transactions in September with an average amount of $148.33.
- Bob made 4 transactions, so he is excluded from the final result.