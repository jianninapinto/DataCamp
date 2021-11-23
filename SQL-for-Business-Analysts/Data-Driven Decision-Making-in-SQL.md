# 1. Introduction to business intelligence for a online movie rental database

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


