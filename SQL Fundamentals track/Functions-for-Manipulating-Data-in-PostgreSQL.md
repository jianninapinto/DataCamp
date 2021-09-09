# 1. Overview of Common Data Types

**</> Getting information about your database**

 In this exercise we will look at how to query the tables table of the INFORMATION_SCHEMA database to discover information about tables in the DVD Rentals database including the name, type, schema, and catalog of all tables and views and then how to use the results to get additional information about columns in our tables.

- Select all columns from the INFORMATION_SCHEMA.TABLES system database. Limit results that have a public table_schema.

```sql
 -- Select all columns from the TABLES system database
 SELECT * 
 FROM INFORMATION_SCHEMA.TABLES
 -- Filter by schema
 WHERE table_schema = 'public';
```

- Select all columns from the INFORMATION_SCHEMA.COLUMNS system database. Limit by table_name to actor

```sql
 -- Select all columns from the COLUMNS system database
 SELECT * 
 FROM INFORMATION_SCHEMA.COLUMNS 
 WHERE table_name = 'actor';
```

**</> Determining data types**

Using the techniques you learned in the lesson, let's explore the customer table of our DVD Rental database.

- Select the column name and data type from the INFORMATION_SCHEMA.COLUMNS system database.
- Limit results to only include the customer table.

```sql
-- Get the column name and data type
SELECT
 	column_name, 
    data_type
-- From the system database information schema
FROM INFORMATION_SCHEMA.COLUMNS 
-- For the customer table
WHERE table_name = 'customer';
```

**</> Interval data types**

INTERVAL data types provide you with a very useful tool for performing arithmetic on date and time data types. For example, let's say our rental policy requires a DVD to be returned within 3 days. We can calculate the expected_return_date for a given DVD rental by adding an INTERVAL of 3 days to the rental_date from the rental table. We can then compare this result to the actual return_date to determine if the DVD was returned late.

Let's try this example in the exercise.

- Select the rental date and return date from the rental table.
- Add an INTERVAL of 3 days to the rental_date to calculate the expected return date.

```sql
SELECT
 	-- Select the rental and return dates
	rental_date,
	return_date,
 	-- Calculate the expected_return_date
	rental_date + INTERVAL '3 days' AS expected_return_date
FROM rental;
```

**</> Accessing data in an ARRAY**

In our DVD Rentals database, the film table contains an ARRAY for special_features which has a type of TEXT[]. Much like any ARRAY data type in PostgreSQL, a TEXT[] array can store an array of TEXT values. This comes in handy when you want to store things like phone numbers or email addresses as we saw in the lesson.

Let's take a look at the special_features column and also practice accessing data in the ARRAY.

1. Select the title and special features from the film table and compare the results between the two columns.

```sql
-- Select the title and special features column 
SELECT 
  title, 
  special_features 
FROM film;
```

2. Select all films that have a special feature Trailers by filtering on the first index of the special_features ARRAY.

```sql
-- Select the title and special features column 
SELECT 
  title, 
  special_features 
FROM film
-- Use the array index of the special_features column
WHERE special_features[1] = 'Trailers';
```

3. Now let's select all films that have Deleted Scenes in the second index of the special_features ARRAY.

```sql
-- Select the title and special features column 
SELECT 
  title, 
  special_features 
FROM film
-- Use the array index of the special_features column
WHERE special_features[2] = 'Deleted Scenes';
```

**</> Searching an ARRAY with ANY**

- Match 'Trailers' in any index of the special_features ARRAY regardless of position.

```sql
SELECT
  title, 
  special_features 
FROM film 
-- Modify the query to use the ANY function 
WHERE 'Trailers' = ANY (special_features);
```
