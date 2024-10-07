
**Problem Statement:**
Given a dataset of employee attendance, you need to produce a summary that consolidates consecutive statuses into date ranges.


**Using the ROW_NUMBER() window function to help with grouping consecutive records.**


![1727707608813](https://github.com/user-attachments/assets/d7af3d7a-96ec-4c6b-a037-d7297e239b36)


**Solution 1**

WITH ranked_data AS (

 SELECT 
 
 Employee 
 Date, 
 
 Status,
 
 ROW_NUMBER() OVER (PARTITION BY employee ORDER BY Date) 

 -ROW_NUMBER() OVER (PARTITION BY employee, status ORDER BY Date) AS grp

 FROM your_table
)

SELECT 
 
 Employee,

 MIN(Date) AS fromdate,
 
 MAX(Date) AS todate,

 Status

FROM ranked_data

GROUP BY employee, status, grp

ORDER BY employee, fromdate;


**             **


**Solution 2**

with cte as (

Select *, row number() over (partition by employee order by date) as rn 

from Employee_attendance), 

cte1 as (

select *, rn- row_number() over (partition by employee,status order by date) as group1 

from cte)

select Employee, min(date) as from_date, max(date) as to_date, status from cte1

group by employee, group1 

order by employee, date
