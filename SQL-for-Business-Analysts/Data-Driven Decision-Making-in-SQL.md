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


3. - Put the most recent records of movie rentals on top of the resulting table and order them in decreasing order.

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

**</>Selecting movies**

The table movies contains all movies available on the online platform.

1. - Select all movies which are not dramas.

```sql
SELECT *
FROM movies
WHERE genre <> 'Drama'; -- All genres except drama
```

| movie_id | title                      | genre                     | runtime | year_of_release | renting_price |
|----------|----------------------------|---------------------------|---------|-----------------|---------------|
| 1        | One Night at McCool's      | Comedy                    | 93      | 2001            | 2.09          |
| 3        | What Women Want            | Comedy                    | 127     | 2001            | 2.59          |
| 5        | The Fellowship of the Ring | Science Fiction & Fantasy | 178     | 2001            | 2.59          |
| ...      | ...                        | ...                       | ...     | ...             | ...           |

2. - Select the movies 'Showtime', 'Love Actually' and 'The Fighter'.

```sql
SELECT *
FROM movies
WHERE title IN ('Showtime', 'Love Actually', 'The Fighter'); -- Select all movies with the given titles
```

| movie_id | title         | genre  | runtime | year_of_release | renting_price |
|----------|---------------|--------|---------|-----------------|---------------|
| 11       | Showtime      | Comedy | 95      | 2002            | 1.79          |
| 20       | Love Actually | Comedy | 135     | 2003            | 2.29          |
| 53       | The Fighter   | Other  | 116     | 2010            | 2.49          |

3. - Order the movies by increasing renting price.

```sql
SELECT *
FROM movies
ORDER BY renting_price ASC; -- Order the movies by increasing renting price
```

| movie_id | title                                  | genre                     | runtime | year_of_release | renting_price |
|----------|----------------------------------------|---------------------------|---------|-----------------|---------------|
| 42       | No Country for Old Men                 | Drama                     | 122     | 2007            | 1.49          |
| 16       | 25th Hour                              | Drama                     | 135     | 2003            | 1.59          |
| 49       | Harry Potter and the Half-Blood Prince | Science Fiction & Fantasy | 153     | 2009            | 1.59          |
| 37       | Candy                                  | Drama                     | 116     | 2006            | 1.59          |


**</> Select from renting**

Only some users give a rating after watching a movie. Sometimes it is interesting to explore only those movie rentals where a rating was provided.

- Select from table renting all movie rentals from 2018.
- Filter only those records which have a movie rating.

```sql
SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-01-01' AND '2018-12-31' -- Renting in 2018
AND rating IS NOT NULL; -- Rating exists
```

| renting_id | customer_id | movie_id | rating | date_renting |
|------------|-------------|----------|--------|--------------|
| 3          | 108         | 45       | 4      | 2018-06-08   |
| 4          | 39          | 66       | 8      | 2018-10-22   |
| 6          | 50          | 71       | 7      | 2018-10-09   |
| 8          | 73          | 65       | 10     | 2018-06-05   |
| ...        | ...         | ...      | ...    | ...          |
