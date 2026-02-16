# SQL Mentor User Performance Analysis | Project No.10


## Project Overview

This project is designed to help  understand SQL querying and performance analysis using real-time data from SQL Mentor datasets. In this project, you will analyze user performance by creating and querying a table of user submissions. The goal is to solve a series of SQL problems to extract meaningful insights from user data.

## Objectives

- Learn how to use SQL for data analysis tasks such as aggregation, filtering, and ranking.
- Understand how to calculate and manipulate data in a real-world dataset.
- Gain hands-on experience with SQL functions like `COUNT`, `SUM`, `AVG`, `EXTRACT()`, and `DENSE_RANK()`.
- Develop skills for performance analysis using SQL by solving different types of data problems related to user performance.


## SQL Mentor User Performance Dataset

The dataset consists of information about user submissions for an online learning platform. Each submission includes:
- **User ID**
- **Question ID**
- **Points Earned**
- **Submission Timestamp**
- **Username**

This data allows you to analyze user performance in terms of correct and incorrect submissions, total points earned, and daily/weekly activity.

## SQL Problems and Questions

Here are the SQL problems that you will solve as part of this project:

### Q1. List All Distinct Users and Their Stats
- **Description**: Return the user name, total submissions, and total points earned by each user.
- **Expected Output**: A list of users with their submission count and total points.

### Q2. Calculate the Daily Average Points for Each User
- **Description**: For each day, calculate the average points earned by each user.
- **Expected Output**: A report showing the average points per user for each day.

### Q3. Find the Top 3 Users with the Most Correct Submissions for Each Day
- **Description**: Identify the top 3 users with the most correct submissions for each day.
- **Expected Output**: A list of users and their correct submissions, ranked daily.

### Q4. Find the Top 5 Users with the Highest Number of Incorrect Submissions
- **Description**: Identify the top 5 users with the highest number of incorrect submissions.
- **Expected Output**: A list of users with the count of incorrect submissions.

### Q5. Find the Top 10 Performers for Each Week
- **Description**: Identify the top 10 users with the highest total points earned each week.
- **Expected Output**: A report showing the top 10 users ranked by total points per week.

## Key SQL Concepts Covered

- **Aggregation**: Using `COUNT`, `SUM`, `AVG` to aggregate data.
- **Date Functions**: Using `EXTRACT()` and `TO_CHAR()` for manipulating dates.
- **Conditional Aggregation**: Using `CASE WHEN` to handle positive and negative submissions.
- **Ranking**: Using `DENSE_RANK()` to rank users based on their performance.
- **Group By**: Aggregating results by groups (e.g., by user, by day, by week).

## SQL Queries Solutions

Below are the solutions for each question in this project:

### Q1: List All Distinct Users and Their Stats
```sql
select 
	distinct username,
    count(id) as total_submissions,
    sum(points) as total_points
from user_submissions
group by 1
order by total_submissions desc;
```

### Q2: Calculate the Daily Average Points for Each User
```sql
select 
	DATE_FORMAT(submitted_at, '%d-%m') AS day,
    username, 
    avg(points) as daily_avg_points
from user_submissions
group by 1,2
order by username;
```

### Q3: Find the Top 3 Users with the Most Correct Submissions for Each Day
```sql
with cte as
(
select *,
case 
when points>0 then 'correct_sub'
else 'wrong_sub'
end  as submission_type
from user_submissions
),
cte1
as
(select 
	date_format(submitted_at, '%d-%m') as day,
    username,
    count(*) as correct_sub,
    dense_rank() over(partition by date_format(submitted_at, '%d-%m') order by count(*) desc) as dn
from cte
where submission_type='correct_sub'
group by 1,2
) 
select day as daily,username,correct_sub
from cte1
where dn <=3;
```

### Q4: Find the Top 5 Users with the Highest Number of Incorrect Submissions
```sql
with cte as
(
select *,
case
when points>0 then 1
else 0
end as sub_type

 from user_submissions),
 cte2
 as
 (
 select username,
  count(*) as wrong_sub,
  dense_rank() over(order by count(*) desc) as dn
  from cte 
  where sub_type=0
  group by 1
  )
  select username,wrong_sub from cte2
  where dn <=5;
```

### Q5: Find the Top 10 Performers for Each Week
```sql
with cte as
(select 
	username,
	week(submitted_at) as weeknum,
    sum(points) as total_points,
    dense_rank() over(partition by week(submitted_at) order by sum(points) desc) as dn
from user_submissions
group by 1,2)
select 
	username,
    weeknum,
    total_points
from cte 
where dn <=10;
```

## Conclusion

This project provides a clear analytical view of user performance by summarizing submissions, points, accuracy, and weekly rankings. Through daily and weekly breakdowns, it highlights top performers and identifies behavioral patterns across users. Overall, it delivers actionable insights that help understand engagement, consistency, and performance trends within the platform.




