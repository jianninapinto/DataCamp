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


# 2. Decision Making with simple SQL queries

**</> First account for each country.**

Conduct an analysis to see when the first customer accounts were created for each country.

- Create a table with a row for each country and columns for the country name and the date when the first customer account was created.
- Use the alias first_account for the column with the dates.
- Order by date in ascending order.

```sql
SELECT country, -- For each country report the earliest date when an account was created
	MIN(date_account_start) AS first_account
FROM customers
GROUP BY country
ORDER BY first_account ASC;
```

| country      | first_account |
|--------------|---------------|
| France       | 2017-01-13    |
| Hungary      | 2017-01-18    |
| Belgium      | 2017-01-28    |
| Slovenia     | 2017-01-31    |
| Spain        | 2017-02-14    |
| Italy        | 2017-02-28    |
| Poland       | 2017-03-03    |
| Great Britan | 2017-03-31    |
| Denmark      | 2017-04-30    |
| USA          | 2017-09-13    |
| Austria      | 2017-11-22    |

Note: The first customer account was created in France. 

**</> Average movie ratings**

For each movie the average rating, the number of ratings and the number of views has to be reported. Generate a table with meaningful column names.

- Group the data in the table renting by movie_id and report the ID and the average rating.

```sql
SELECT movie_id, 
       AVG(rating)    -- Calculate average rating per movie
FROM renting
GROUP BY movie_id;
```

| movie_id | avg                |
|----------|--------------------|
| 54       | 8.1666666666666667 |
| 29       | 8.0000000000000000 |
| 71       | 8.0000000000000000 |
| ...      | ...                |

- Add two columns for the number of ratings and the number of movie rentals to the results table.
- Use alias names avg_rating, number_rating and number_renting for the corresponding columns.

```sql
SELECT movie_id, 
       AVG(rating) AS avg_rating, -- Use as alias avg_rating
       COUNT(rating) AS number_rating,                -- Add column for number of ratings with alias number_rating
       COUNT(renting_id) AS number_renting                 -- Add column for number of movie rentals with alias number_renting
FROM renting
GROUP BY movie_id;
```

| movie_id | avg_rating         | number_rating | number_renting |
|----------|--------------------|---------------|----------------|
| 54       | 8.1666666666666667 | 6             | 9              |
| 29       | 8.0000000000000000 | 7             | 11             |
| 71       | 8.0000000000000000 | 4             | 6              |
| 68       | 6.3333333333333333 | 3             | 7              |
| 4        | 7.8888888888888889 | 9             | 14             |
| 34       | 8.0000000000000000 | 6             | 8              |
| 51       | 8.4285714285714286 | 7             | 7              |
| 70       | 7.7500000000000000 | 4             | 7              |
| ...      | ...                | ...           | ...            |

- Order the rows of the table by the average rating such that it is in decreasing order.
- Observe what happens to NULL values.

```sql
SELECT movie_id, 
       AVG(rating) AS avg_rating,
       COUNT(rating) AS number_ratings,
       COUNT(*) AS number_renting
FROM renting
GROUP BY movie_id
ORDER BY avg_rating DESC; -- Order by average rating in decreasing order
```

- Question
Which statement is true for the movie with average rating null?

Possible Answers

a. The number of ratings is 6.

b. The number of movie rentals is zero.

c. The average is null because one of the ratings of the movie is null.

`d. The average is null because all of the ratings of the movie are null.`

Note: The average is null only if all values are null.

**</> Average rating per customer**

Similar to what you just did, you will now look at the average movie ratings, this time for customers. So you will obtain a table with the average rating given by each customer. Further, you will include the number of ratings and the number of movie rentals per customer. You will report these summary statistics only for customers with more than 7 movie rentals and order them in ascending order by the average rating.

- Group the data in the table renting by customer_id and report the customer_id, the average rating, the number of ratings and the number of movie rentals.
- Select only customers with more than 7 movie rentals.
- Order the resulting table by the average rating in ascending order.

```sql
SELECT customer_id, -- Report the customer_id
      AVG(rating),  -- Report the average rating per customer
      COUNT(rating),  -- Report the number of ratings per customer
      COUNT(renting_id)  -- Report the number of movie rentals per customer
FROM renting
GROUP BY customer_id
HAVING COUNT(renting_id)> 7  -- Select only customers with more than 7 movie rentals
ORDER BY AVG(rating); -- Order by the average rating in ascending order
```

| customer_id | avg                | count | count |
|-------------|--------------------|-------|-------|
| 104         | 6.2500000000000000 | 4     | 8     |
| 28          | 6.7142857142857143 | 7     | 11    |
| 111         | 7.0000000000000000 | 3     | 10    |
| 113         | 7.0000000000000000 | 7     | 15    |
| 25          | 7.2000000000000000 | 5     | 10    |
| 21          | 7.3333333333333333 | 6     | 14    |
| 92          | 7.5714285714285714 | 7     | 11    |
| 49          | 7.6250000000000000 | 8     | 13    |
| 35          | 7.6666666666666667 | 6     | 9     |
| 52          | 7.8750000000000000 | 8     | 9     |
| 108         | 8.0000000000000000 | 6     | 10    |
| 114         | 8.0000000000000000 | 6     | 11    |
| ...         | ...                | ...   | ...   |

Note: Customer number 104 gave the lowest average ratings for 4 movies.

**</> Join renting and customers**

For many analyses it is necessary to add customer information to the data in the table renting.

- Augment the table renting with all columns from the table customers with a LEFT JOIN.
- Use as alias' for the tables r and c respectively.

```sql
SELECT * -- Join renting with customers
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id;
```

| renting_id | customer_id | movie_id | rating | date_renting | customer_id | name              | country      | gender | date_of_birth | date_account_start |
|------------|-------------|----------|--------|--------------|-------------|-------------------|--------------|--------|---------------|--------------------|
| 1          | 41          | 8        | null   | 2018-10-09   | 41          | Zara Mitchell     | Great Britan | female | 1994-07-08    | 2017-06-12         |
| 2          | 10          | 29       | 10     | 2017-03-01   | 10          | Arnout Veenhuis   | Belgium      | male   | 1984-07-26    | 2017-01-28         |
| 3          | 108         | 45       | 4      | 2018-06-08   | 108         | Saúl Tafoya Meraz | Spain        | male   | 1992-05-15    | 2017-03-13         |
| 4          | 39          | 66       | 8      | 2018-10-22   | 39          | Amy Haynes        | Great Britan | female | 1975-07-28    | 2018-01-19         |
| ...        | ...         | ...      | ...    | ...          | ...         | ...               | ...          | ...    | ...           | ...                |

- Select only records from customers coming from Belgium.

```sql
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
WHERE country = 'Belgium'; -- Select only records from customers coming from Belgium
```

| renting_id | customer_id | movie_id | rating | date_renting | customer_id | name                 | country | gender | date_of_birth | date_account_start |
|------------|-------------|----------|--------|--------------|-------------|----------------------|---------|--------|---------------|--------------------|
| 2          | 10          | 29       | 10     | 2017-03-01   | 10          | Arnout Veenhuis      | Belgium | male   | 1984-07-26    | 2017-01-28         |
| 14         | 8           | 29       | null   | 2018-08-03   | 8           | Jaëla van den Dolder | Belgium | female | 1990-08-31    | 2018-02-08         |
| 27         | 7           | 36       | null   | 2019-03-14   | 7           | Annelous Sneep       | Belgium | female | 1993-11-14    | 2018-05-12         |
| 32         | 8           | 42       | 10     | 2019-02-13   | 8           | Jaëla van den Dolder | Belgium | female | 1990-08-31    | 2018-02-08         |
| ...        | ...         | ...      | ...    | ...          | ...         | ...                  | ...     | ...    | ...           | ...                |

- Average ratings of customers from Belgium.

```sql
SELECT AVG(rating) -- Average ratings of customers from Belgium
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
WHERE c.country='Belgium';
```

| avg                |
|--------------------|
| 8.9000000000000000 |


**</> Aggregating revenue, rentals and active customers**

The management of MovieNow wants to report key performance indicators (KPIs) for the performance of the company in 2018. They are interested in measuring the financial successes as well as user engagement. Important KPIs are, therefore, the profit coming from movie rentals, the number of movie rentals and the number of active customers.

- First, you need to join movies on renting to include the renting_price from the movies table for each renting record.
- Use as alias' for the tables m and r respectively.

```sql
SELECT *
FROM renting AS r
LEFT JOIN movies AS m -- Choose the correct join statment
ON r.movie_id = m.movie_id;
```

| renting_id | customer_id | movie_id | rating | date_renting | movie_id | title              | genre  | runtime | year_of_release | renting_price |
|------------|-------------|----------|--------|--------------|----------|--------------------|--------|---------|-----------------|---------------|
| 1          | 41          | 8        | null   | 2018-10-09   | 8        | Waking Up in Reno  | Comedy | 91      | 2002            | 2.59          |
| 2          | 10          | 29       | 10     | 2017-03-01   | 29       | Two for the Money  | Drama  | 122     | 2005            | 2.79          |
| 3          | 108         | 45       | 4      | 2018-06-08   | 45       | Burn After Reading | Drama  | 96      | 2008            | 2.39          |
| ...        | ...         | ...      | ...    | ...          | ...      | ...                | ...    | ...     | ...             | ...           |

- Calculate the revenue coming from movie rentals, the number of movie rentals and the number of customers who rented a movie.

```sql
SELECT 
	SUM(m.renting_price),  -- Get the revenue from movie rentals
	COUNT(r.renting_id), -- Count the number of rentals
	COUNT (distinct r.customer_id)  -- Count the number of customers
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id;
```

| sum     | count | count |
|---------|-------|-------|
| 1275.72 | 578   | 116   |

- Now, you can report these values for the year 2018. Calculate the revenue in 2018, the number of movie rentals and the number of active customers in 2018. An active customer is a customer who rented at least one movie in 2018.

```sql
SELECT 
	SUM(m.renting_price), 
	COUNT(*), 
	COUNT(DISTINCT r.customer_id)
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id
-- Only look at movie rentals in 2018
WHERE date_renting BETWEEN '2018-01-01' AND '2018-12-31';
```

| sum    | count | count |
|--------|-------|-------|
| 658.02 | 298   | 93    |

Note: We calculated a turnover of 658.02 and found the number of rentals to be 298 and the number of active users to be 93 in 2018.

**</> Movies and actors**

You are asked to give an overview of which actors play in which movie.

- Create a list of actor names and movie titles in which they act. Make sure that each combination of actor and movie appears only once.
- Use as an alias for the table actsin the two letters ai.

```sql
SELECT m.title, -- Create a list of movie titles and actor names
       a.name
FROM actsin AS ai
LEFT JOIN movies AS m
ON m.movie_id = ai.movie_id
LEFT JOIN actors AS a
ON a.actor_id = ai.actor_id;
```

| title         | name          |
|---------------|---------------|
| Candy         | Abbie Cornish |
| Jack and Jill | Adam Sandler  |
| Simone        | Al Pacino     |
| ...           | ...           |


**</> Income from movies**

How much income did each movie generate? To answer this question subsequent SELECT statements can be used.

- Use a join to get the movie title and price for each movie rental.

```sql
SELECT m.title, -- Use a join to get the movie title and price for each movie rental
       m.renting_price
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id=m.movie_id;
```

| title              | renting_price |
|--------------------|---------------|
| Waking Up in Reno  | 2.59          |
| Two for the Money  | 2.79          |
| Burn After Reading | 2.39          |
| ...                | ...           |

- Report the total income for each movie.
- Order the result by decreasing income.

```sql
SELECT title, -- Report the income from movie rentals for each movie 
       SUM(rm.renting_price) AS income_movie
FROM
       (SELECT m.title,  
               m.renting_price
       FROM renting AS r
       LEFT JOIN movies AS m
       ON r.movie_id=m.movie_id) AS rm
GROUP BY title
ORDER BY income_movie DESC; -- Order the result by decreasing income
```

| title                              | income_movie |
|------------------------------------|--------------|
| Bridget Jones - The Edge of Reason | 37.57        |
| Fair Game                          | 34.68        |
| The Kingdom                        | 31.35        |
| ...                                | ...          |

- Question

Which statement about the movie 'Django Unchained' is NOT correct?

Possible Answers

a. The income from this movie is 29.59.

b. The income from 'Django Unchained' is lower than from 'The Kingdom'.

`c. The income from 'Django Unchained' is higher than from 'Simone'.`

d. 'Django Unchained' has the 5th highest income.

Note: 'Django Unchained' and 'Simone' generated the same income.

**</> Age of actors from the USA**

Now you will explore the age of American actors and actresses. Report the date of birth of the oldest and youngest US actor and actress.

- Create a subsequent SELECT statements in the FROM clause to get all information about actors from the USA.
- Give the subsequent SELECT statement the alias a.
- Report for actors from the USA the year of birth of the oldest and the year of birth of the youngest actor and actress.

```sql
SELECT a.gender, -- Report for male and female actors from the USA 
       MIN(a.year_of_birth), -- The year of birth of the oldest actor
       MAX(a.year_of_birth) -- The year of birth of the youngest actor
FROM
    (SELECT * -- Use a subsequent SELECT to get all information about actors from the USA
    FROM actors
    WHERE nationality = 'USA') AS a -- Give the table the name a
GROUP BY a.gender;
```

Note: The oldest actor was born in 1930 and the oldest actress in 1945.

| gender | min  | max  |
|--------|------|------|
| female | 1945 | 1993 |
| male   | 1930 | 1992 |


**</> Income from movies**

How much income did each movie generate? To answer this question subsequent SELECT statements can be used.

- Use a join to get the movie title and price for each movie rental.

```sql
SELECT m.title, -- Use a join to get the movie title and price for each movie rental
       m.renting_price
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id=m.movie_id;
```

| title              | renting_price |
|--------------------|---------------|
| Waking Up in Reno  | 2.59          |
| Two for the Money  | 2.79          |
| Burn After Reading | 2.39          |
| ...                | ...           |

- Report the total income for each movie.
- Order the result by decreasing income.

```sql
SELECT rm.title, -- Report the income from movie rentals for each movie 
       SUM(rm.renting_price) AS income_movie
FROM
       (SELECT m.title,  
               m.renting_price
       FROM renting AS r
       LEFT JOIN movies AS m
       ON r.movie_id=m.movie_id) AS rm
GROUP BY rm.title
ORDER BY income_movie DESC; -- Order the result by decreasing income
```

| title                              | income_movie |
|------------------------------------|--------------|
| Bridget Jones - The Edge of Reason | 37.57        |
| Fair Game                          | 34.68        |
| The Kingdom                        | 31.35        |
| ...                                | ...          |

**Question**

Which statement about the movie 'Django Unchained' is NOT correct?

Possible Answers

a. The income from this movie is 29.59.

b. The income from 'Django Unchained' is lower than from 'The Kingdom'.

`c. The income from 'Django Unchained' is higher than from 'Simone'.`

d. 'Django Unchained' has the 5th highest income.

Note: 'Django Unchained' and 'Simone' generated the same income.


**</> Age of actors from the USA**

Now you will explore the age of American actors and actresses. Report the date of birth of the oldest and youngest US actor and actress.

- Create a subsequent SELECT statements in the FROM clause to get all information about actors from the USA.
- Give the subsequent SELECT statement the alias a.
- Report for actors from the USA the year of birth of the oldest and the year of birth of the youngest actor and actress.

```sql
SELECT a.gender, -- Report for male and female actors from the USA 
       MIN(a.year_of_birth), -- The year of birth of the oldest actor
       MAX(a.year_of_birth) -- The year of birth of the youngest actor
FROM
   (SELECT * -- Use a subsequen SELECT to get all information about actors from the USA
   FROM actors
   WHERE nationality = 'USA') AS a -- Give the table the name a
GROUP BY gender;
```

| gender | min  | max  |
|--------|------|------|
| female | 1945 | 1993 |
| male   | 1930 | 1992 |

Note: The oldest actor was born in 1930 and the oldest actress in 1945.

**</> Identify favorite movies for a group of customers**

Which is the favorite movie on MovieNow? Answer this question for a specific group of customers: for all customers born in the 70s.

- Augment the table renting with customer information and information about the movies.
- For each join use the first letter of the table name as alias.

```sql
SELECT *
FROM renting AS r
LEFT JOIN customers AS c   -- Add customer information
ON r.customer_id = c.customer_id
LEFT JOIN movies as m   -- Add movie information
ON r.movie_id = m.movie_id;
```

| renting_id | customer_id | movie_id | rating | date_renting | customer_id | name              | country      | gender | date_of_birth | date_account_start | movie_id | title              | genre  | runtime | year_of_release | renting_price |
|------------|-------------|----------|--------|--------------|-------------|-------------------|--------------|--------|---------------|--------------------|----------|--------------------|--------|---------|-----------------|---------------|
| 1          | 41          | 8        | null   | 2018-10-09   | 41          | Zara Mitchell     | Great Britan | female | 1994-07-08    | 2017-06-12         | 8        | Waking Up in Reno  | Comedy | 91      | 2002            | 2.59          |
| 2          | 10          | 29       | 10     | 2017-03-01   | 10          | Arnout Veenhuis   | Belgium      | male   | 1984-07-26    | 2017-01-28         | 29       | Two for the Money  | Drama  | 122     | 2005            | 2.79          |
| 3          | 108         | 45       | 4      | 2018-06-08   | 108         | Saúl Tafoya Meraz | Spain        | male   | 1992-05-15    | 2017-03-13         | 45       | Burn After Reading | Drama  | 96      | 2008            | 2.39          |
| ...        | ...         | ...      | ...    | ...          | ...         | ...               | ...          | ...    | ...           | ...                | ...      | ...                | ...    | ...     | ...             | ...           |


- Select only those records of customers born in the 70s.

```sql
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE date_of_birth BETWEEN '1970-01-01' AND '1979-12-31' ; -- Select customers born in the 70s
```

| renting_id | customer_id | movie_id | rating | date_renting | customer_id | name          | country      | gender | date_of_birth | date_account_start | movie_id | title                                         | genre                     | runtime | year_of_release | renting_price |
|------------|-------------|----------|--------|--------------|-------------|---------------|--------------|--------|---------------|--------------------|----------|-----------------------------------------------|---------------------------|---------|-----------------|---------------|
| 4          | 39          | 66       | 8      | 2018-10-22   | 39          | Amy Haynes    | Great Britan | female | 1975-07-28    | 2018-01-19         | 66       | The Hunger Games                              | Drama                     | 142     | 2012            | 1.59          |
| 11         | 61          | 61       | null   | 2017-06-04   | 61          | Nella Manfrin | Italy        | female | 1974-01-11    | 2017-05-14         | 61       | Harry Potter and the Deathly Hallows – Part 2 | Science Fiction & Fantasy | 130     | 2011            | 1.99          |
| ...        | ...         | ...      | ...    | ...          | ...         | ...           | ...          | ...    | ...           | ...                | ...      | ...                                           | ...                       | ...     | ...             | ...           |

- For each movie, report the number of times it was rented, as well as the average rating. Limit your results to customers born in the 1970s.

```sql
SELECT m.title, 
COUNT(*),-- Report number of views per movie
AVG(r.rating) -- Report the average rating per movie
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
GROUP BY m.title;
```

| title          | count | avg                 |
|----------------|-------|---------------------|
| V for Vendetta | 2     | 7.0000000000000000  |
| The Fighter    | 4     | 10.0000000000000000 |
| ...            | ...   | ...                 |

- Remove those movies from the table with only one rental.
- Order the result table such that movies with highest rating come first.

```sql
SELECT m.title, 
COUNT(*),
AVG(r.rating)
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
GROUP BY m.title
HAVING COUNT(*)>1 -- Remove movies with only one rental
ORDER BY AVG(r.rating) DESC; -- Order with highest rating first
```

| title                                         | count | avg                 |
|-----------------------------------------------|-------|---------------------|
| Waking Up in Reno                             | 2     | null                |
| Ray                                           | 2     | null                |
| Harry Potter and the Deathly Hallows – Part 2 | 2     | null                |
| Showtime                                      | 5     | null                |
| One Night at McCool's                         | 2     | 10.0000000000000000 |
| Django Unchained                              | 4     | 10.0000000000000000 |
| ...                                           | ...   | ...                 |

**</> Identify favorite actors for Spain**

You're now going to explore actor popularity in Spain. Use as alias the first letter of the table, except for the table actsin use ai instead.

- Augment the table renting with information about customers and actors.

```sql
SELECT *
FROM renting as r 
LEFT JOIN customers as c  -- Augment table renting with information about customers 
ON r.customer_id = c.customer_id
LEFT JOIN actsin as ai  -- Augment the table renting with the table actsin
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a  -- Augment table renting with information about actors
ON ai.actor_id = a.actor_id;
```

| renting_id | customer_id | movie_id | rating | date_renting | customer_id | name            | country      | gender | date_of_birth | date_account_start | actsin_id | movie_id | actor_id | actor_id | name               | year_of_birth | nationality  | gender |
|-----------|-------------|----------|--------|--------------|-------------|-----------------|--------------|--------|---------------|--------------------|-----------|----------|----------|----------|--------------------|---------------|--------------|--------|
| 1         | 41          | 8        | null   | 2018-10-09   | 41          | Zara Mitchell   | Great Britan | female | 1994-07-08    | 2017-06-12         | 160       | 8        | 107      | 107      | Patrick Swayze     | 1952          | USA          | male   |
| 1         | 41          | 8        | null   | 2018-10-09   | 41          | Zara Mitchell   | Great Britan | female | 1994-07-08    | 2017-06-12         | 152       | 8        | 103      | 103      | Natasha Richardson | 1963          | British      | female |
| 1         | 41          | 8        | null   | 2018-10-09   | 41          | Zara Mitchell   | Great Britan | female | 1994-07-08    | 2017-06-12         | 28        | 8        | 23       | 23       | Charlize Theron    | 1975          | South Africa | female |
| 2         | 10          | 29       | 10     | 2017-03-01   | 10          | Arnout Veenhuis | Belgium      | male   | 1984-07-26    | 2017-01-28         | 172       | 29       | 117      | 117      | Rene Russo         | 1954          | USA          | female |
| ...       | ...         | ...      | ...    | ...          | ..          | ...             | ...          | ...    | ...           | ...                | ...       | ...      | ...      | ...      | ...                | ...           | ...          | ...    |

- Report the number of movie rentals and the average rating for each actor, separately for male and female customers.
- Report only actors with more than 5 movie rentals.

```sql
SELECT a.name,  c.gender,
       COUNT(*) AS number_views, 
       AVG(r.rating) AS avg_rating
FROM renting as r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
LEFT JOIN actsin as ai
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a
ON ai.actor_id = a.actor_id

GROUP BY a.name, c.gender -- For each actor, separately for male and female customers
HAVING AVG(r.rating) IS NOT NULL 
AND COUNT(*)>5 -- Report only actors with more than 5 movie rentals
ORDER BY avg_rating DESC, number_views DESC;
```

| name            | gender | number_views | avg_rating          |
|-----------------|--------|--------------|---------------------|
| Tommy Lee Jones | female | 6            | 10.0000000000000000 |
| Javier Bardem   | female | 6            | 10.0000000000000000 |
| Josh Brolin     | female | 6            | 10.0000000000000000 |
| ...             | ...    | ...          | ...                 |

- Now, report the favorite actors only for customers from Spain.

```sql
SELECT a.name,  c.gender,
       COUNT(*) AS number_views, 
       AVG(r.rating) AS avg_rating
FROM renting as r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
LEFT JOIN actsin as ai
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a
ON ai.actor_id = a.actor_id
WHERE country = 'Spain' -- Select only customers from Spain
GROUP BY a.name, c.gender
HAVING AVG(r.rating) IS NOT NULL 
  AND COUNT(*) > 5 
ORDER BY avg_rating DESC, number_views DESC;
```

| name             | gender | number_views | avg_rating         |
|------------------|--------|--------------|--------------------|
| Catherine Keener | female | 6            | 8.0000000000000000 |
| Emma Watson      | male   | 7            | 7.6000000000000000 |
| Daniel Radcliffe | male   | 7            | 7.6000000000000000 |
| ...              | ...    | ...          | ...                |

Note: Catherine Keener is the favorite actress among female Spain customers and that male customers from Spain like the actors from Harry Potter best: Emma Watson, Daniel Radcliffe and Rupert Grint.

**</> KPIs per country**

In chapter 1 you were asked to provide a report about the development of the company. This time you have to prepare a similar report with KPIs for each country separately. Your manager is interested in the total number of movie rentals, the average rating of all movies and the total revenue for each country since the beginning of 2019.

- Augment the table renting with information about customers and movies.
- Use as alias the first latter of the table name.
- Select only records about rentals since beginning of 2019.

```sql
SELECT *
FROM renting as r -- Augment the table renting with information about customers
LEFT JOIN customers as c
ON r.customer_id = c.customer_id
LEFT JOIN movies as m -- Augment the table renting with information about movies
ON r.movie_id = m.movie_id
WHERE r.date_renting>='01-01-2019'; -- Select only records about rentals since the beginning of 2019
```

- Calculate the number of movie rentals.
- Calculate the average rating.
- Calculate the revenue from movie rentals.
- Report these KPIs for each country.

```sql
SELECT 
	c.country,                    -- For each country report
	COUNT(*) AS number_renting, -- The number of movie rentals
	AVG(r.rating) AS average_rating, -- The average rating
	SUM(m.renting_price) AS revenue         -- The revenue from movie rentals
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE date_renting >= '2019-01-01'
GROUP BY country;
```

| country      | number_renting | average_rating      | revenue |
|--------------|----------------|---------------------|---------|
| null         | 1              | 10.0000000000000000 | 1.79    |
| Spain        | 26             | 8.0769230769230769  | 57.94   |
| Great Britan | 9              | 7.2000000000000000  | 17.91   |
| ...          | ...            | ...                 | ...     |

Note: There is a total revenue of 57.94 for Spain, with 26 movie rentals and an average rating of 8.1.

# 3. Data Driven Decision Making with advanced SQL queries

**</> Often rented movies**

Your manager wants you to make a list of movies excluding those which are hardly ever watched. This list of movies will be used for advertising. List all movies with more than 5 views using a nested query which is a powerful tool to implement selection conditions.

- Select all movie IDs which have more than 5 views.

```sql
SELECT movie_id -- Select movie IDs with more than 5 views
FROM renting
GROUP BY movie_id
HAVING COUNT(*) > 5
```

| movie_id |
|----------|
| 54       |
| 29       |
| 71       |
| ...      |


- Select all information about movies with more than 5 views.

```sql
SELECT *
FROM movies
WHERE movie_id IN -- Select movie IDs from the inner query
	(SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(*) > 5)
```

| movie_id | title                 | genre  | runtime | year_of_release | renting_price |
|----------|-----------------------|--------|---------|-----------------|---------------|
| 1        | One Night at McCool's | Comedy | 93      | 2001            | 2.09          |
| 2        | Swordfish             | Drama  | 99      | 2001            | 2.19          |
| ...      | ...                   | ...    | ...     | ...             | ...           |


**</> Frequent customers**

Report a list of customers who frequently rent movies on MovieNow.

- List all customer information for customers who rented more than 10 movies.

```sql
SELECT *
FROM customers
WHERE customer_id IN           -- Select all customers with more than 10 movie rentals
	(SELECT customer_id
	FROM renting
	GROUP BY customer_id
	HAVING COUNT(*) > 10);
```

| customer_id | name                 | country | gender | date_of_birth | date_account_start |
|-------------|----------------------|---------|--------|---------------|--------------------|
| 21          | Avelaine Corbeil     | France  | female | 1986-03-17    | 2017-06-11         |
| 28          | Sidney Généreux      | France  | male   | 1980-12-01    | 2017-02-04         |
| 49          | Havasy Kristof       | Hungary | male   | 1998-06-13    | 2017-01-18         |
| 92          | Honorata Nowak       | Poland  | female | 1986-05-02    | 2017-09-21         |
| 113         | Lucy Centeno Barrios | Spain   | female | 1970-11-03    | 2017-06-13         |
| 114         | Canela Gaona Lozano  | Spain   | female | 1997-04-01    | 2017-02-14         |

Note: Avelaine Corbeil from France is one of the customers who rented more than 10 movies.

**</> Movies with rating above average**

For the advertising campaign your manager also needs a list of popular movies with high ratings. Report a list of movies with rating above average.

- Calculate the average over all ratings.

```sql
SELECT AVG(rating) -- Calculate the total average rating
FROM renting;
```

- Select movie IDs and calculate the average rating of movies with rating above average.

```sql
SELECT movie_id, -- Select movie IDs and calculate the average rating 
       AVG(rating)
FROM renting
GROUP BY movie_id
HAVING AVG(rating) >           -- Of movies with rating above average
	(SELECT AVG(rating)
	FROM renting);
```

| movie_id | avg                |
|----------|--------------------|
| 54       | 8.1666666666666667 |
| 29       | 8.0000000000000000 |
| 71       | 8.0000000000000000 |
| ...      | ...                |

- The advertising team only wants a list of movie titles. Report the movie titles of all movies with average rating higher than the total average.

```sql
SELECT title -- Report the movie titles of all movies with average rating higher than the total average
FROM movies
WHERE movie_id IN
	(SELECT movie_id
	 FROM renting
     GROUP BY movie_id
     HAVING AVG(rating) > 
		(SELECT AVG(rating)
		 FROM renting));
```

| title                                    | avg                |
|------------------------------------------|--------------------|
| What Women Want                          | 8.1666666666666667 |
| The Fellowship of the Ring               | 8.0000000000000000 |
| Harry Potter and the Philosopher's Stone | 8.0000000000000000 |
| ...                                      | ...                |

**</> Analyzing customer behavior**

A new advertising campaign is going to focus on customers who rented fewer than 5 movies. Use a correlated query to extract all customer information for the customers of interest.

- First, count number of movie rentals for customer with customer_id=45. Give the table renting the alias r.

```sql
-- Count movie rentals of customer 45
SELECT COUNT(*)
FROM renting AS r
WHERE r.customer_id = 45;
```

| count |
|-------|
| 5     |

- Now select all columns from the customer table where the number of movie rentals is smaller than 5.

```sql
-- Select customers with less than 5 movie rentals
SELECT *
FROM customers as c
WHERE 5 > 
	(SELECT count(*)
	FROM renting as r
	WHERE r.customer_id = c.customer_id);
```

| customer_id | name               | country | gender | date_of_birth | date_account_start |
|-------------|--------------------|---------|--------|---------------|--------------------|
| 2           | Wolfgang Ackermann | Austria | male   | 1971-11-17    | 2018-10-15         |
| 3           | Daniela Herzog     | Austria | female | 1974-08-07    | 2019-02-14         |
| 4           | Julia Jung         | Austria | female | 1991-01-04    | 2017-11-22         |
| ...         | ...                | ...     | ...    | ...           | ...                |


**</> Customers who gave low ratings**

Identify customers who were not satisfied with movies they watched on MovieNow. Report a list of customers with minimum rating smaller than 4.

- Calculate the minimum rating of customer with ID 7.

```sql
-- Calculate the minimum rating of customer with ID 7
SELECT MIN(rating)
FROM renting
WHERE customer_id = 7;
```

| min |
|-----|
| 8   |

- Select all customers with a minimum rating smaller than 4. Use the first letter of the table as an alias.

```sql
SELECT *
FROM customers AS c
WHERE 4 > -- Select all customers with a minimum rating smaller than 4 
	(SELECT MIN(rating)
	FROM renting AS r
	WHERE r.customer_id = c.customer_id);
```

| customer_id | name            | country      | gender | date_of_birth | date_account_start |
|-------------|-----------------|--------------|--------|---------------|--------------------|
| 28          | Sidney Généreux | France       | male   | 1980-12-01    | 2017-02-04         |
| 41          | Zara Mitchell   | Great Britan | female | 1994-07-08    | 2017-06-12         |
| 86          | Albin Jaworski  | Poland       | male   | 1984-05-01    | 2017-12-15         |
| 120         | Robin J. Himes  | USA          | male   | 1988-11-30    | 2018-08-06         |

Note: Sidney Généreux, Zara Mitchell, Albin Jaworski, and Robin J. Himes rated a movie with less than 4.

**</> Movies and ratings with correlated queries**

Report a list of movies that received the most attention on the movie platform, (i.e. report all movies with more than 5 ratings and all movies with an average rating higher than 8).

1. - Select all movies with more than 5 ratings. Use the first letter of the table as an alias.

```sql
SELECT *
FROM movies as m
WHERE 5 < -- Select all movies with more than 5 ratings
	(SELECT count(rating)
	FROM renting as r
	WHERE r.movie_id = m.movie_id);
```

| movie_id | title          | genre                     | runtime | year_of_release | renting_price |
|----------|----------------|---------------------------|---------|-----------------|---------------|
| 4        | Training Day   | Drama                     | 122     | 2001            | 1.79          |
| 10       | Simone         | Drama                     | 117     | 2002            | 2.69          |
| 12       | The Two Towers | Science Fiction & Fantasy | 179     | 2002            | 2.39          |
| ...      | ...            | ...                       | ...     | ...             | ...           |

2. - Select all movies with an average rating higher than 8.

```sql
SELECT *
FROM movies AS m
WHERE 8 < -- Select all movies with an average rating higher than 8
	(SELECT AVG(rating)
	FROM renting AS r
	WHERE r.movie_id = m.movie_id);
```

| movie_id | title                                    | genre                     | runtime | year_of_release | renting_price |
|----------|------------------------------------------|---------------------------|---------|-----------------|---------------|
| 3        | What Women Want                          | Comedy                    | 127     | 2001            | 2.59          |
| 5        | The Fellowship of the Ring               | Science Fiction & Fantasy | 178     | 2001            | 2.59          |
| 6        | Harry Potter and the Philosopher's Stone | Science Fiction & Fantasy | 152     | 2001            | 2.69          |
| ...      | ...                                      | ...                       | ...     | ...             | ...           |

Note: The comedy 'What women want' has an average rating higher than 8. We didn't need to use a GROUP BY clause to answer this request.


**</> Customers with at least one rating**

Having active customers is a key performance indicator for MovieNow. Make a list of customers who gave at least one rating.

- Select all records of movie rentals from customer with ID 115.

```sql
-- Select all records of movie rentals from customer with ID 115
SELECT *
FROM renting
WHERE customer_id = 115;
```

| renting_id | customer_id | movie_id | rating | date_renting |
|------------|-------------|----------|--------|--------------|
| 245        | 115         | 69       | null   | 2019-04-24   |
| 395        | 115         | 11       | null   | 2019-04-07   |
| 498        | 115         | 42       | null   | 2019-03-16   |


- Select all records of movie rentals from the customer with ID 115 and exclude records with null ratings.

```sql
SELECT *
FROM renting
WHERE rating IS NOT NULL -- Exclude those with null ratings
AND customer_id = 115;
```

- Select all records of movie rentals from the customer with ID 1, excluding null ratings.

```sql
SELECT *
FROM renting
WHERE rating IS NOT NULL -- Exclude null ratings
AND customer_id = 1; -- Select all ratings from customer with ID 1
```

| renting_id | customer_id | movie_id | rating | date_renting |
|------------|-------------|----------|--------|--------------|
| 421        | 1           | 71       | 10     | 2019-01-21   |
| 520        | 1           | 39       | 6      | 2018-12-29   |

- Select all customers with at least one rating. Use the first letter of the table as an alias.

```sql
SELECT *
FROM customers as c -- Select all customers with at least one rating
WHERE EXISTS
	(SELECT *
	FROM renting AS r
	WHERE rating IS NOT NULL 
	AND r.customer_id = c.customer_id);
```

| customer_id | name               | country | gender | date_of_birth | date_account_start |
|-------------|--------------------|---------|--------|---------------|--------------------|
| 2           | Wolfgang Ackermann | Austria | male   | 1971-11-17    | 2018-10-15         |
| 4           | Julia Jung         | Austria | female | 1991-01-04    | 2017-11-22         |
| 7           | Annelous Sneep     | Belgium | female | 1993-11-14    | 2018-05-12         |
| ...         | ...                | ...     | ...    | ...           | ...                |

**</> Actors in comedies**

In order to analyze the diversity of actors in comedies, first, report a list of actors who play in comedies and then, the number of actors for each nationality playing in comedies.

- Select the records of all actors who play in a Comedy. Use the first letter of the table as an alias.

```sql
SELECT *  -- Select the records of all actors who play in a Comedy
FROM actsin AS ai
LEFT JOIN movies as m
ON ai.movie_id = m.movie_id
WHERE m.genre = 'Comedy';
```

| actsin_id | movie_id | actor_id | movie_id | title                | genre  | runtime | year_of_release | renting_price |
|-----------|----------|----------|----------|----------------------|--------|---------|-----------------|---------------|
| 2         | 56       | 2        | 56       | Jack and Jill        | Comedy | 91      | 2011            | 2.09          |
| 6         | 56       | 3        | 56       | Jack and Jill        | Comedy | 91      | 2011            | 2.09          |
| 9         | 7        | 6        | 7        | The Royal Tenenbaums | Comedy | 110     | 2002            | 1.89          |
| ...       | ...      | ...      | ...      | ...                  | ...    | ...     | ...             | ...           |

- Make a table of the records of actors who play in a Comedy and select only the actor with ID 1.

```sql
SELECT *
FROM actsin AS ai
LEFT JOIN movies AS m
ON m.movie_id = ai.movie_id
WHERE m.genre = 'Comedy'
AND ai.actor_id = 1; -- Select only the actor with ID 1
```

- Create a list of all actors who play in a Comedy. Use the first letter of the table as an alias.

```sql
SELECT *
FROM actors as a
WHERE EXISTS
	(SELECT *
	 FROM actsin AS ai
	 LEFT JOIN movies AS m
	 ON m.movie_id = ai.movie_id
	 WHERE m.genre = 'Comedy'
	 AND ai.actor_id = a.actor_id);
```

| actor_id | name            | year_of_birth | nationality | gender |
|----------|-----------------|---------------|-------------|--------|
| 2        | Adam Sandler    | 1966          | USA         | male   |
| 3        | Al Pacino       | 1940          | USA         | male   |
| 6        | Anjelica Huston | 1951          | USA         | female |
| ...      | ...             | ...           | ...         | ...    |

- Report the nationality and the number of actors for each nationality.

```sql
SELECT a.nationality, count (*) -- Report the nationality and the number of actors for each nationality
FROM actors AS a
WHERE EXISTS
	(SELECT ai.actor_id
	 FROM actsin AS ai
	 LEFT JOIN movies AS m
	 ON m.movie_id = ai.movie_id
	 WHERE m.genre = 'Comedy'
	 AND ai.actor_id = a.actor_id)
GROUP BY a.nationality;
```

| nationality      | count |
|------------------|-------|
| Northern Ireland | 1     |
| USA              | 22    |
| South Africa     | 1     |
| Canada           | 1     |
| British          | 3     |

Note: There is one actor each coming from South Africa, Canada and Northen Ireland, three actors from Great Britain, and 22 from the USA who played in a Comedy.

**</> Young actors not coming from the USA**

As you've just seen, the operators UNION and INTERSECT are powerful tools when you work with two or more tables. Identify actors who are not from the USA and actors who were born after 1990.

- Report the name, nationality and the year of birth of all actors who are not from the USA.

```sql
SELECT name,  -- Report the name, nationality and the year of birth
       nationality, 
       year_of_birth
FROM actors
WHERE NOT nationality = 'USA'; -- Of all actors who are not from the USA
```

| name               | nationality | year_of_birth |
|--------------------|-------------|---------------|
| Abbie Cornish      | Australia   | 1982          |
| Andrea Riseborough | British     | 1981          |
| Anthony Hopkins    | British     | 1937          |
| ...                | ...         | ...           |

- Report the name, nationality and the year of birth of all actors who were born after 1990.

```sql
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE year_of_birth > 1990; -- Born after 1990
```

| name             | nationality | year_of_birth |
|------------------|-------------|---------------|
| Annasophia Robb  | USA         | 1993          |
| Freddie Highmore | British     | 1992          |
| Josh Hutcherson  | USA         | 1992          |

- Select all actors who are not from the USA and all actors who are born after 1990.

```sql
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE nationality <> 'USA'
UNION -- Select all actors who are not from the USA and all actors who are born after 1990
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE year_of_birth > 1990;
```

| name           | nationality | year_of_birth |
|----------------|-------------|---------------|
| Cate Blanchett | Australia   | 1969          |
| Julie Christie | British     | 1940          |
| Emma Watson    | British     | 1990          |
| ...            | ...         | ...           |

- Select all actors who are not from the USA and who are also born after 1990.

```sql
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE nationality <> 'USA'
INTERSECT -- Select all actors who are not from the USA and who are also born after 1990
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE year_of_birth > 1990;
```

| name             | nationality | year_of_birth |
|------------------|-------------|---------------|
| Freddie Highmore | British     | 1992          |


**</> Dramas with high ratings**

The advertising team has a new focus. They want to draw the attention of the customers to dramas. Make a list of all movies that are in the drama genre and have an average rating higher than 9.

- Select the IDs of all dramas.

```sql
SELECT movie_id -- Select the IDs of all dramas
FROM movies
WHERE genre = 'Drama';
```

| movie_id |
|----------|
| 2        |
| 4        |
| 9        |
| ...      |

- Select the IDs of all movies with average rating higher than 9.

```sql
SELECT movie_id -- Select the IDs of all movies with average rating higher than 9
FROM renting
GROUP BY movie_id
HAVING AVG(rating) > 9;
```
| movie_id |
|----------|
| 63       |
| 42       |
| 48       |
| 5        |

- Select the IDs of all dramas with average rating higher than 9.

```sql
SELECT movie_id
FROM movies
WHERE genre = 'Drama'
INTERSECT  -- Select the IDs of all dramas with average rating higher than 9
SELECT movie_id
FROM renting
GROUP BY movie_id
HAVING AVG(rating)>9;
```

| movie_id |
|----------|
| 42       |

- Select all movies of in the drama genre with an average rating higher than 9.

```sql
SELECT *
FROM movies
WHERE movie_id IN-- Select all movies of genre drama with average rating higher than 9
   (SELECT movie_id
    FROM movies
    WHERE genre = 'Drama'
    INTERSECT
    SELECT movie_id
    FROM renting
    GROUP BY movie_id
    HAVING AVG(rating)>9);
```

| movie_id | title                  | genre | runtime | year_of_release | renting_price |
|----------|------------------------|-------|---------|-----------------|---------------|
| 42       | No Country for Old Men | Drama | 122     | 2007            | 1.49          |


# 4 Data Driven Decision Making with OLAP SQL queries

**</> Groups of customers**

Use the CUBE operator to extract the content of a pivot table from the database. Create a table with the total number of male and female customers from each country.

- Create a table with the total number of customers, of all female and male customers, of the number of customers for each country and the number of men and women from each country.

```sql
SELECT gender, -- Extract information of a pivot table of gender and country for the number of customers
	   country,
	   COUNT(*)
FROM customers
GROUP BY CUBE (country, gender)
ORDER BY country;
```

| gender | country | count |
|--------|---------|-------|
| female | Austria | 3     |
| male   | Austria | 1     |
| null   | Austria | 4     |
| male   | Belgium | 3     |
| null   | Belgium | 6     |
| female | Belgium | 3     |
| ...    | ...     | ...   |

**</> Categories of movies**

Give an overview on the movies available on MovieNow. List the number of movies for different genres and release years.

- List the number of movies for different genres and the year of release on all aggregation levels by using the CUBE operator.

```sql
SELECT year_of_release,
       genre,
       COUNT(*)
FROM movies
GROUP BY CUBE(genre,year_of_release)
ORDER BY year_of_release;
```

| year_of_release | genre                     | count |
|-----------------|---------------------------|-------|
| 2001            | null                      | 6     |
| 2001            | Drama                     | 2     |
| 2001            | Comedy                    | 2     |
| 2001            | Science Fiction & Fantasy | 2     |
| 2002            | Comedy                    | 3     |
| 2002            | null                      | 7     |
| ...             | ...                       | ...   |

**- Question**

Which statement is NOT correct about the result table?

Possible Answers

a. From all genres (ignoring the year of release) there are most movies in the category Drama.

b. In total there are 71 movies available on MovieNow.

`c. The year of release with most movies is 2014.`

d. From 2002 there are 2 dramas available on MovieNow.

Note: Only one movie from 2014 is available on MovieNow. The highest number of movies is from 2003 with 8 movies.

**</> Analyzing average ratings**

Prepare a table for a report about the national preferences of the customers from MovieNow comparing the average rating of movies across countries and genres.

- Augment the records of movie rentals with information about movies and customers, in this order. Use the first letter of the table names as alias.

```sql
-- Augment the records of movie rentals with information about movies and customers
SELECT *
FROM movies as m
LEFT JOIN renting as r
ON m.movie_id = r.movie_id
LEFT JOIN customers as c
ON r.customer_id = c.customer_id;
```

| movie_id | title             | genre  | runtime | year_of_release | renting_price | renting_id | customer_id | movie_id | rating | date_renting | customer_id | name            | country      | gender | date_of_birth | date_account_start |
|----------|-------------------|--------|---------|-----------------|---------------|------------|-------------|----------|--------|--------------|-------------|-----------------|--------------|--------|---------------|--------------------|
| 8        | Waking Up in Reno | Comedy | 91      | 2002            | 2.59          | 1          | 41          | 8        | null   | 2018-10-09   | 41          | Zara Mitchell   | Great Britan | female | 1994-07-08    | 2017-06-12         |
| 29       | Two for the Money | Drama  | 122     | 2005            | 2.79          | 2          | 10          | 29       | 10     | 2017-03-01   | 10          | Arnout Veenhuis | Belgium      | male   | 1984-07-26    | 2017-01-28         |
| ...      | ...               | ...    | ...     | ...             | ...           | ...        | ...         | ...      | ...    | ...          | ...         | ...             | ...          | ...    | ...           | ...                |

- Calculate the average rating for each country.

```sql
-- Calculate the average rating for each country
SELECT 
	country,
    AVG(r.rating)
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY country;
```

| country      | avg                |
|--------------|--------------------|
| null         | 8.0000000000000000 |
| Spain        | 7.6415094339622642 |
| Great Britan | 7.5200000000000000 |
| Austria      | 6.8000000000000000 |
| Poland       | 8.1212121212121212 |
| Italy        | 8.1379310344827586 |
| Slovenia     | 8.4318181818181818 |
| Hungary      | 7.6285714285714286 |
| Denmark      | 7.8888888888888889 |
| Belgium      | 8.9000000000000000 |
| France       | 7.7714285714285714 |
| USA          | 8.0000000000000000 |

- Calculate the average rating for all aggregation levels of country and genre.

```sql
SELECT 
	country, 
	genre, 
	AVG(r.rating) AS avg_rating -- Calculate the average rating 
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY CUBE (country, genre); -- For all aggregation levels of country and genre
```

| country  | genre              | avg_rating         |
|----------|--------------------|--------------------|
| null     | null               | 7.9390243902439024 |
| France   | Mystery & Suspense | 6.0000000000000000 |
| Slovenia | Action & Adventure | null               |
| Spain    | Animation          | null               |
| ...      | ...                | ...                |

**- Question**

What is the average rating over all records, rounded to two digits?

Possible Answers

`a. 7.94`

b. null

c. 8.80

d. 7.86

Note: The average over all records is 7.94.


**</> Number of customers**

You have to give an overview of the number of customers for a presentation.

- Generate a table with the total number of customers, the number of customers for each country, and the number of female and male customers for each country.
- Order the result by country and gender.

```sql
-- Count the total number of customers, the number of customers for each country, and the number of female and male customers for each country
SELECT country,
       gender,
	   COUNT(*)
FROM customers
GROUP BY ROLLUP (country, gender)
ORDER BY country, gender; -- Order the result by country and gender
```

| country | gender | count |
|---------|--------|-------|
| Austria | female | 3     |
| Austria | male   | 1     |
| ...     | ...    | ...   |

**</> Analyzing preferences of genres across countries**

You are asked to study the preferences of genres across countries. Are there particular genres which are more popular in specific countries? Evaluate the preferences of customers by averaging their ratings and counting the number of movies rented from each genre.

- Augment the renting records with information about movies and customers.

```sql
-- Join the tables
SELECT *
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id;
```

| renting_id | customer_id | movie_id | rating | date_renting | movie_id | title              | genre  | runtime | year_of_release | renting_price | customer_id | name              | country      | gender | date_of_birth | date_account_start |
|------------|-------------|----------|--------|--------------|----------|--------------------|--------|---------|-----------------|---------------|-------------|-------------------|--------------|--------|---------------|--------------------|
| 1          | 41          | 8        | null   | 2018-10-09   | 8        | Waking Up in Reno  | Comedy | 91      | 2002            | 2.59          | 41          | Zara Mitchell     | Great Britan | female | 1994-07-08    | 2017-06-12         |
| 2          | 10          | 29       | 10     | 2017-03-01   | 29       | Two for the Money  | Drama  | 122     | 2005            | 2.79          | 10          | Arnout Veenhuis   | Belgium      | male   | 1984-07-26    | 2017-01-28         |
| 3          | 108         | 45       | 4      | 2018-06-08   | 45       | Burn After Reading | Drama  | 96      | 2008            | 2.39          | 108         | Saúl Tafoya Meraz | Spain        | male   | 1992-05-15    | 2017-03-13         |
| 4          | 39          | 66       | 8      | 2018-10-22   | 66       | The Hunger Games   | Drama  | 142     | 2012            | 1.59          | 39          | Amy Haynes        | Great Britan | female | 1975-07-28    | 2018-01-19         |
| ...        | ...         | ...      | ...    | ...          | ...      | ...                | ...    | ...     | ...             | ...           | ...         | ...               | ...          | ...    | ...           | ...                |

- Calculate the average ratings and the number of ratings for each country and each genre. Include the columns country and genre in the SELECT clause.

```sql
SELECT 
	c.country, -- Select country
	m.genre, -- Select genre
	avg(r.rating), -- Average ratings
	count(*)  -- Count number of movie rentals
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY (c.country, m.genre) -- Aggregate for each country and each genre
ORDER BY c.country, m.genre;
```

| country | genre                     | avg                | count |
|---------|---------------------------|--------------------|-------|
| Austria | Comedy                    | 8.0000000000000000 | 1     |
| Austria | Drama                     | 6.0000000000000000 | 2     |
| Austria | Mystery & Suspense        | null               | 1     |
| Austria | Science Fiction & Fantasy | 6.6666666666666667 | 3     |
| ...     | ...                       | ...                | ...   |

- Finally, calculate the average ratings and the number of ratings for each country and genre, as well as an aggregation over all genres for each country and the overall average and total number.

```sql
-- Group by each county and genre with OLAP extension
SELECT 
	c.country, 
	m.genre, 
	AVG(r.rating) AS avg_rating, 
	COUNT(*) AS num_rating
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY ROLLUP (c.country, m.genre)
ORDER BY c.country, m.genre;
```

| country | genre                     | avg_rating         | num_rating |
|---------|---------------------------|--------------------|------------|
| Austria | Comedy                    | 8.0000000000000000 | 1          |
| Austria | Drama                     | 6.0000000000000000 | 2          |
| Austria | Mystery & Suspense        | null               | 1          |
| Austria | Science Fiction & Fantasy | 6.6666666666666667 | 3          |
| Austria | null                      | 6.8000000000000000 | 7          |
| ...     | ...                       | ...                | ...        |


**</> Queries with GROUPING SETS**

What question CANNOT be answered by the following query?

```sql
SELECT 
  r.customer_id, 
  m.genre, 
  AVG(r.rating), 
  COUNT(*)
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id
GROUP BY GROUPING SETS ((r.customer_id, m.genre), (r.customer_id), ());
```

Possible Answers

1. How many movies were watched by each customer?

`2. What is the average rating for each genre?`

3. What is the average rating of customer 75 for movies of the Comedy genre?

4. What is the overall average rating for all movies from all customers?

Note: GROUPING SETS does not include (genre) (i.e. does not include aggregation for each genre).
