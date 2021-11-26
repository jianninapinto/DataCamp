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


**</> Summarizing customer information**
In most business decisions customers are analyzed in groups, such as customers per country or customers per age group.

1. - Count the number of customers born in the 80s.

```sql
SELECT COUNT(*) -- Count the total number of customers
FROM customers
WHERE date_of_birth BETWEEN '1980-01-01' AND '1989-12-31'; -- Select customers born between 1980-01-01 and 1989-12-31
```
| count |
|-------|
| 33    |

2. - Count the number of customers from Germany.

```sql
SELECT COUNT(*)   -- Count the total number of customers
FROM customers
WHERE country = 'Germany'; -- Select all customers from Germany
```
| count |
|-------|
| 0     |

3. - Count the number of countries where MovieNow has customers.

```sql
SELECT COUNT(DISTINCT country)   -- Count the number of countries
FROM customers;
```
| count |
|-------|
| 11    |

**</> Ratings of movie 25**

The movie ratings give us insight into the preferences of our customers. Report summary statistics, such as the minimum, maximum, average, and count, of ratings for the movie with ID 25.

- Select all movie rentals of the movie with movie_id 25 from the table renting.
- For those records, calculate the minimum, maximum and average rating and count the number of ratings for this movie.

```sql
SELECT MIN(rating) AS min_rating, -- Calculate the minimum rating and use alias min_rating
	   MAX(rating) AS max_rating, -- Calculate the maximum rating and use alias max_rating
	   AVG(rating) AS avg_rating, -- Calculate the average rating and use alias avg_rating
	   COUNT(RATING) AS number_ratings -- Count the number of ratings and use alias number_ratings
FROM renting
WHERE movie_id = 25; -- Select all records of the movie with ID 25
```
| min_rating | max_rating | avg_rating         | number_ratings |
|------------|------------|--------------------|----------------|
| 5          | 10         | 7.5000000000000000 | 8              |

Note: This movie has 8 ratings with minimum 5, maximum 10 and average 7.5 rating.

**</> Examining annual rentals**

You are asked to provide a report about the development of the company. Specifically, your manager is interested in the total number of movie rentals, the total number of ratings and the average rating of all movies since the beginning of 2019.

- First, select all records of movie rentals since January 1st 2019.

```sql
SELECT * -- Select all records of movie rentals since January 1st 2019
FROM renting
WHERE date_renting >= '2019-01-01'; 
```
| enting_id | customer_id | movie_id | rating | date_renting |
|-----------|-------------|----------|--------|--------------|
| 5         | 104         | 15       | 7      | 2019-03-18   |
| 17        | 22          | 46       | 10     | 2019-02-16   |
| 18        | 36          | 39       | 10     | 2019-03-20   |
| 27        | 7           | 36       | null   | 2019-03-14   |
| 32        | 8           | 42       | 10     | 2019-02-13   |
| ...       | ...         | ...      | ...    | ...          |

- Now, count the number of movie rentals and calculate the average rating since the beginning of 2019.

```sql
SELECT 
	COUNT(renting_id), -- Count the total number of rented movies
	AVG(rating) -- Add the average rating
FROM renting
WHERE date_renting >= '2019-01-01';
```

| count | avg                |
|-------|--------------------|
| 159   | 7.9462365591397849 |

- Use as alias column names number_renting and average_rating respectively.

```sql
SELECT 
	COUNT(*) AS number_renting, -- Give it the column name number_renting
	AVG(rating) AS average_rating  -- Give it the column name average_rating
FROM renting
WHERE date_renting >= '2019-01-01';
```
| number_renting | average_rating     |
|----------------|--------------------|
| 159            | 7.9462365591397849 |

- Finally, count how many ratings exist since 2019-01-01.

```sql
SELECT 
	COUNT(*) AS number_renting,
	AVG(rating) AS average_rating, 
    COUNT(rating) AS number_ratings -- Add the total number of ratings here.
FROM renting
WHERE date_renting >= '2019-01-01';
```

| number_renting | average_rating     | number_ratings |
|----------------|--------------------|----------------|
| 159            | 7.9462365591397849 | 93             |


