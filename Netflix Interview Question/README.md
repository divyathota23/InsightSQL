
# Netflix Interview Questions

### SQL Question 1: Identify VIP Users for Netflix
***Question:*** To better cater to its most dedicated users, Netflix would like to identify its “VIP users” - those who are most active in terms of the number of hours of content they watch. Write a SQL query that will retrieve the top 10 users with the most watched hours in the last month.

***Query:***

```sql 
SELECT wa.user_id, SUM(wa.hours_watched) AS total_hours_watched
FROM watching_activity wa
WHERE wa.date_time >= CURRENT_DATE - INTERVAL '1 month'
GROUP BY wa.user_id
ORDER BY total_hours_watched DESC
LIMIT 10;

```

***Explanation:***

- We sum the hours_watched for each user_id from the watching_activity table.
- The WHERE clause filters the records to include only the last month's activities.
- The results are grouped by user_id and ordered by total_hours_watched in descending order.
- The LIMIT 10 ensures that only the top 10 users are returned.


*** ***


### SQL Question 2: Analyzing Ratings For Netflix Shows

***Question:*** Given a table of user ratings for Netflix shows, calculate the average rating for each show within a given month. Assume that there is a column for user_id, show_id, rating (out of 5 stars), and date of review. Order the results by month and then by average rating (descending order).

***Query:***

``` sql
SELECT 
    EXTRACT(YEAR FROM sr.review_date) AS review_year,
    EXTRACT(MONTH FROM sr.review_date) AS review_month,
    sr.show_id,
    AVG(sr.stars) AS avg_rating
FROM show_reviews sr
GROUP BY review_year, review_month, sr.show_id
ORDER BY review_year, review_month, avg_rating DESC;
```
***Explanation:***

- The query extracts the year and month from the review_date to group the results by month.
- We calculate the average stars for each show_id using AVG().
- Results are grouped by review_year, review_month, and show_id.
- The ORDER BY clause orders by year, then month, and finally the average rating in descending order.
*** ***
### SQL Question 3: What does EXCEPT / MINUS SQL commands do?
***Question:*** Explain the purpose and usage of the EXCEPT (or MINUS in some SQL dialects) SQL commands.

***Answer:*** The EXCEPT (or MINUS in some SQL dialects like Oracle) command is used to return the rows from the first query that are not present in the second query. It essentially performs a set difference between two result sets. The key points are:

- The two queries must have the same number of columns and compatible data types.
- EXCEPT removes duplicates from the results, showing only distinct rows from the first query that do not appear in the second.
- This can be used to find rows in one dataset that do not exist in another.

***Query***

```sql
Copy code
SELECT user_id FROM users
EXCEPT
SELECT user_id FROM users_who_cancelled;
This would return the user_ids of users who have not cancelled their subscriptions.
```
*** ***
### SQL Question 4: Filter Netflix Users Based on Viewing History and Subscription Status
***Question:*** You are given a database of Netflix’s user viewing history and their current subscription status. Write a SQL query to find all active customers who watched more than 10 episodes of a show called “Stranger Things” in the last 30 days.

***Query***

```sql

SELECT u.user_id, u.active
FROM users u
JOIN viewing_history vh ON u.user_id = vh.user_id
JOIN shows s ON vh.show_id = s.show_id
WHERE s.show_name = 'Stranger Things'
  AND vh.watch_date >= CURRENT_DATE - INTERVAL '30 days'
  AND u.active = TRUE
GROUP BY u.user_id, u.active
HAVING COUNT(vh.episode_id) > 10;
```
***Explanation:***

- The query joins the users, viewing_history, and shows tables.
- The WHERE clause filters for users who watched "Stranger Things" in the last 30 days and are active.
- We use GROUP BY to group by user_id and active to calculate the number of episodes watched.
- The HAVING clause filters to return only those users who watched more than 10 episodes.
*** ***
### SQL Question 5: What does it mean to denormalize a database?
***Question:*** Explain the concept and implications of denormalizing a database.

***Answer:*** Denormalization is the process of intentionally introducing redundancy into a database by merging tables or adding duplicate columns to reduce the complexity of queries and improve read performance. While normalization aims to minimize redundancy and avoid anomalies, denormalization sacrifices these goals for performance benefits, particularly in data retrieval.

Implications:

Pros:
- Faster query performance, especially for complex joins.
- Reduced need for complex joins in reports and dashboards.
- Can be beneficial for read-heavy databases (e.g., OLAP systems).

Cons:
- Increased storage requirements due to data redundancy.
- Data anomalies and inconsistencies could arise if the redundant data is not updated correctly.
- More complex write operations, as changes need to be propagated across multiple places.
*** ***
### SQL Question 6: Filter and Match Customer’s Viewing Records
***Question:*** As a data analyst at Netflix, you are asked to analyze the customer’s viewing records. You confirmed that Netflix is especially interested in customers who have been continuously watching a particular genre - ‘Documentary’ over the last month. The task is to find the name and email of those customers who have viewed more than five ‘Documentary’ movies within the last month. ‘Documentary’ could be a part of a broader genre category in the genre field (for example, ‘Documentary, History’). Therefore, the matching pattern could occur anywhere within the string.

***Query***

```sql

SELECT c.name, c.email
FROM customer c
JOIN movies m ON c.last_movie_watched = m.movie_id
WHERE m.genre LIKE '%Documentary%'
  AND c.date_watched >= CURRENT_DATE - INTERVAL '1 month'
GROUP BY c.user_id, c.name, c.email
HAVING COUNT(m.movie_id) > 5;
```

***Explanation:***

- The JOIN is performed between the customer and movies tables based on the last_movie_watched.
- The WHERE clause filters movies in the "Documentary" genre, checking for the keyword anywhere in the genre field using the LIKE '%Documentary%' condition.
- We filter for customers who watched these movies within the last month.
- The HAVING clause ensures that only customers who watched more than five movies are selected.


