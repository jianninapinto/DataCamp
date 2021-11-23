# 1. Introduction to business intelligence for an online movie rental database

**</> Exploring the database**

Explore the tables and its columns. Which of the following quantities can't be computed?

Possible Answers

a. The number of customers from each country.

`b. The number of movies with an international award.`

c. The average rating of a movie.

d. The number of movies with the actor Daniel Radcliffe.

**</> Exploring the table renting**

The table renting includes all records of movie rentals. Each record has a unique ID renting_id. It also contains information about customers (customer_id) and which movies they watched (movie_id). Furthermore, customers can give a rating after watching the movie, and the day the movie was rented is recorded.

- Select all columns from renting.

```sql
SELECT *  -- Select all
FROM renting;   
```

| renting_id | customer_id | movie_id | rating | date_renting |
|------------|-------------|----------|--------|--------------|
| 1          | 41          | 8        | null   | 2018-10-09   |
| 2          | 10          | 29       | 10     | 2017-03-01   |
| 3          | 108         | 45       | 4      | 2018-06-08   |
| 4          | 39          | 66       | 8      | 2018-10-22   |
| ...        | ...         | ...      | ...    | ...          |

- Now select only those columns from renting which are needed to calculate the average rating per movie.

| movie_id | rating |
|----------|--------|
| 8        | null   |
| 29       | 10     |
| 45       | 4      |
| 66       | 8      |
| ...      | ...    |

**</> Question**

In SQL missing values are coded with null. In which column of renting did you notice null values?

Possible Answers

a. renting_id

b. customer_id

c. movie_id

`d. rating`


**</> Working with dates**

For the analysis of monthly or annual changes, it is important to select data from specific time periods. You will select records from the table renting of movie rentals. The format of dates is 'YYYY-MM-DD'.

1. - Select all movies rented on October 9th, 2018.

```sql
SELECT *
FROM renting
WHERE date_renting = '2018-10-09'; -- Movies rented on October 9th, 2018
```

| renting_id | customer_id | movie_id | rating | date_renting |
|------------|-------------|----------|--------|--------------|
| 1          | 41          | 8        | null   | 2018-10-09   |
| 6          | 50          | 71       | 7      | 2018-10-09   |


2. - Select all records of movie rentals between beginning of April 2018 till end of August 2018.

```sql
SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-04-01' AND '2018-08-31'; -- from beginning April 2018 to end August 2018
```

| renting_id | customer_id | movie_id | rating | date_renting |
|------------|-------------|----------|--------|--------------|
| 3          | 108         | 45       | 4      | 2018-06-08   |
| 8          | 73          | 65       | 10     | 2018-06-05   |
| 12         | 52          | 65       | 10     | 2018-06-29   |
| ...        | ...         | ...      | ...    | ...          |


3. Put the most recent records of movie rentals on top of the resulting table and order them in decreasing order.

```sql
SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-04-01' AND '2018-08-31'
ORDER BY date_renting DESC; -- Order by recency in decreasing order
```

| renting_id | customer_id | movie_id | rating | date_renting |
|------------|-------------|----------|--------|--------------|
| 76         | 112         | 10       | null   | 2018-08-31   |
| 279        | 21          | 62       | null   | 2018-08-30   |
| 551        | 86          | 13       | 8      | 2018-08-29   |
| ...        | ...         | ...      | ...    | ...          |


