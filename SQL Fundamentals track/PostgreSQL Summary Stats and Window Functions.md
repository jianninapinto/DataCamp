# 1. Introduction to window functions

**</> Numbering rows**

The simplest application for window functions is numbering rows. Numbering rows allows you to easily fetch the nth row. For example, it would be very difficult to get the 35th row in any given table if you didn't have a column with each row's number.

- Number each row in the dataset.

```sql
SELECT
  *,
  -- Assign numbers to each row
  ROW_NUMBER() OVER() AS Row_N
FROM Summer_Medals
ORDER BY Row_N ASC;
```

**</> Numbering Olympic games in ascending order**

The Summer Olympics dataset contains the results of the games between 1896 and 2012. The first Summer Olympics were held in 1896, the second in 1900, and so on. What if you want to easily query the table to see in which year the 13th Summer Olympics were held? You'd need to number the rows for that.

- Assign a number to each year in which Summer Olympic games were held.

```sql
SELECT
  Year,

  -- Assign numbers to each year
  ROW_NUMBER() OVER() AS Row_N
FROM (
  SELECT DISTINCT Year
  FROM Summer_Medals
  ORDER BY Year ASC
) AS Years
ORDER BY Year ASC;
```

Answer: The 13th Summer Olympics were held in 1956.


**</> Numbering Olympic games in descending order**

You've already numbered the rows in the Summer Medals dataset. What if you need to reverse the row numbers so that the most recent Olympic games' rows have a lower number?

- Assign a number to each year in which Summer Olympic games were held so that rows with the most recent years have lower row numbers.

```sql
SELECT
  Year,
  -- Assign the lowest numbers to the most recent years
  ROW_NUMBER() OVER (ORDER BY Year DESC) AS Row_N
FROM (
  SELECT DISTINCT Year
  FROM Summer_Medals
) AS Years
ORDER BY Year;
```
