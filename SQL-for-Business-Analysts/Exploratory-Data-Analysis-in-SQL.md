# 1 What's in the database?

**</> Count missing values**

Which column of fortune500 has the most missing values? To find out, you'll need to check each column individually, although here we'll check just three.

- First, figure out how many rows are in fortune500 by counting them.

```sql
-- Select the count of the number of rows
SELECT COUNT(*)
  FROM fortune500;
```

Answer: We have 500 rows in the fortune500 table.

- Subtract the count of the non-NULL ticker values from the total number of rows; alias the difference as missing.

```sql
-- Select the count of ticker, 
-- subtract from the total number of rows, 
-- and alias as missing
SELECT count(*) - COUNT(ticker) AS missing
  FROM fortune500;
```

Answer: We have 32 missing values on the ticker column.

- Repeat for the profits_change column.

```sql
-- Select the count of profits_change, 
-- subtract from total number of rows, and alias as missing
SELECT COUNT(*) - COUNT(profits_change) AS missing
FROM fortune500;
```

Answer: We got 63 missing values on the profits_change column.

- Repeat for the industry column.

```sql
-- Select the count of industry, 
-- subtract from total number of rows, and alias as missing
SELECT COUNT(*) - COUNT(industry) AS missing
FROM fortune500;
```

Answer: We have 13 missing values on the industry column. 

**</> Join tables**

- Look at the contents of the company and fortune500 tables. Find a column that they have in common where the values for each company are the same in both tables.
- Join the company and fortune500 tables with an INNER JOIN.
- Select only company.name for companies that appear in both tables.

```sql
SELECT company.name
-- Table(s) to select from
  FROM company
       INNER JOIN fortune500
       ON company.ticker=fortune500.ticker;
 ```

Answer: 

`Apple Incorporated
Amazon.com Inc
Alphabet
Microsoft Corp.
International Business Machines Corporation
PayPal Holdings Incorporated
eBay, Inc.
Adobe Systems Incorporated`

**</> Foreign keys**

Using what you know about foreign keys, why can't the tag column in the tag_type table be a foreign key that references the tag column in the stackoverflow table?

Possible Answers

a. stackoverflow.tag is not a primary key

b. tag_type.tag contains NULL values

`c. stackoverflow.tag contains duplicate values`

d. tag_type.tag does not contain all the values in stackoverflow.tag

Why: Foreign keys must reference a column with unique values for each row so the referenced row can be identified.


**</> Read an entity relationship diagram**

The information you need is sometimes split across multiple tables in the database.

What is the most common stackoverflow tag_type? What companies have a tag of that type?

To generate a list of such companies, you'll need to join three tables together.

- First, using the tag_type table, count the number of tags with each type.
- Order the results to find the most common tag type.

```sql
-- Count the number of tags with each type
SELECT type, COUNT(tag) AS count
  FROM tag_type
 -- To get the count for each type, what do you need to do?
 GROUP BY type
 -- Order the results with the most common
 -- tag types listed first
 ORDER BY count DESC;
```

Answer: The most common stackoverflow tag_type is cloud.

- Join the tag_company, company, and tag_type tables, keeping only mutually occurring records.
- Select company.name, tag_type.tag, and tag_type.type for tags with the most common type from the previous step.

```sql
-- Select the 3 columns desired
SELECT company.name, tag_type.tag, tag_type.type
  FROM company
  	   -- Join to the tag_company table
       INNER JOIN tag_company 
       ON company.id = tag_company.company_id
       -- Join to the tag_type table
       INNER JOIN tag_type
       ON tag_company.tag = tag_type.tag
  -- Filter to most common type
  WHERE type='cloud';
```

**</> Coalesce**

In the fortune500 data, industry contains some missing values. Use coalesce() to use the value of sector as the industry when industry is NULL. Then find the most common industry.

- Use coalesce() to select the first non-NULL value from industry, sector, or 'Unknown' as a fallback value.
- Alias the result of the call to coalesce() as industry2.
- Count the number of rows with each industry2 value.
- Find the most common value of industry2.

```sql
-- Use coalesce
SELECT coalesce(industry, sector, 'Unknown') AS industry2,
       -- Don't forget to count!
       COUNT(*) 
  FROM fortune500 
-- Group by what? (What are you counting by?)
 GROUP BY industry2
-- Order results to see most common first
 ORDER BY COUNT(*) DESC
-- Limit results to get just the one value you want
 LIMIT 1;
```

Answer: Utilities: Gas and Electric	22

**</> Coalesce with a self-join**

You previously joined the company and fortune500 tables to find out which companies are in both tables. Now, also include companies from company that are subsidiaries of Fortune 500 companies as well.

To include subsidiaries, you will need to join company to itself to associate a subsidiary with its parent company's information. To do this self-join, use two different aliases for company.

coalesce will help you combine the two ticker columns in the result of the self-join to join to fortune500.

- Join company to itself to add information about a company's parent to the original company's information.
- Use coalesce to get the parent company ticker if available and the original company ticker otherwise.
- INNER JOIN to fortune500 using the ticker.
- Select original company name, fortune500 title and rank.

```sql
SELECT company_original.name, title, rank
  -- Start with original company information
  FROM company AS company_original
       -- Join to another copy of company with parent
       -- company information
	   LEFT JOIN company AS company_parent
       ON company_original.parent_id = company_parent.id 
       -- Join to fortune500, only keep rows that match
       INNER JOIN fortune500 
       -- Use parent ticker if there is one, 
       -- otherwise original ticker
       ON coalesce(company_original.ticker, 
                   company_parent.ticker) = 
             fortune500.ticker
 -- For clarity, order by rank
 ORDER BY rank; 
```

**</> Effects of casting**

When you cast data from one type to another, information can be lost or changed. See how the casting changes values and practice casting data using the CAST() function and the :: syntax.

`SELECT CAST(value AS new_type);

SELECT value::new_type;`

1. - Select profits_change and profits_change cast as integer from fortune500.
   - Look at how the values were converted.
   
```-- Select the original value
SELECT profits_change, 
	   -- Cast profits_change
       CAST(profits_change AS integer) AS profits_change_int
  FROM fortune500;
  ```
  

2. - Compare the results of casting of dividing the integer value 10 by 3 to the result of dividing the numeric value 10 by 3.

```sql
-- Divide 10 by 3
SELECT 10/3, 
       -- Cast 10 as numeric and divide by 3
       10::numeric/3;
```

Answer:
3 | 3.3333333333333333

3. - Now cast numbers that appear as text as numeric.
   - Note: 1e3 is scientific notation.

```sql
SELECT '3.2'::numeric,
       '-123'::numeric,
       '1e3'::numeric,
       '1e-3'::numeric,
       '02314'::numeric,
       '0002'::numeric;
```

**</> Summarize the distribution of numeric values**

Was 2017 a good or bad year for revenue of Fortune 500 companies? Examine how revenue changed from 2016 to 2017 by first looking at the distribution of revenues_change and then counting companies whose revenue increased.

- Use GROUP BY and count() to examine the values of revenues_change.
- Order the results by revenues_change to see the distribution.

```sql
-- Select the count of each value of revenues_change
SELECT revenues_change, COUNT(revenues_change)
  FROM fortune500
 GROUP BY revenues_change
 -- order by the values of revenues_change
 ORDER BY revenues_change;
```

- Repeat step 1, but this time, cast revenues_change as an integer to reduce the number of different values.

```sql
-- Select the count of each revenues_change integer value
SELECT revenues_change::integer, COUNT(revenues_change)
  FROM fortune500
 GROUP BY revenues_change::integer
 -- order by the values of revenues_change
 ORDER BY revenues_change;
```

- How many of the Fortune 500 companies had revenues increase in 2017 compared to 2016? To find out, count the rows of fortune500 where revenues_change indicates an increase.

```sql
-- Count rows 
SELECT count(*)
  FROM fortune500
 -- Where...
 WHERE revenues_change > 0;
```

Answer: 298



# 2. Summarizing and aggregating numeric data

**</> Division**

Compute the average revenue per employee for Fortune 500 companies by sector.

- Compute revenue per employee by dividing revenues by employees; use casting to produce a numeric result.
- Take the average of revenue per employee with avg(); alias this as avg_rev_employee.
- Group by sector.
- Order by the average revenue per employee.

```sql
-- Select average revenue per employee by sector
SELECT sector, 
       AVG(revenues/employees::numeric) AS avg_rev_employee
  FROM fortune500
 GROUP BY sector
 -- Use the column alias to order the results
 ORDER BY avg_rev_employee;
```

**</> Explore with division**

In exploring a new database, it can be unclear what the data means and how columns are related to each other.

What information does the unanswered_pct column in the stackoverflow table contain? Is it the percent of questions with the tag that are unanswered (unanswered ?s with tag/all ?s with tag)? Or is it something else, such as the percent of all unanswered questions on the site with the tag (unanswered ?s with tag/all unanswered ?s)?

Divide unanswered_count (unanswered ?s with tag) by question_count (all ?s with tag) to see if the value matches that of unanswered_pct to determine the answer.

- Exclude rows where question_count is 0 to avoid a divide by zero error.
- Limit the result to 10 rows.

```sql-- Divide unanswered_count by question_count
SELECT unanswered_count/question_count::numeric AS computed_pct, 
       -- What are you comparing the above quantity to?
       unanswered_pct
  FROM stackoverflow
 -- Select rows where question_count is not 0
 WHERE question_count > 0
 LIMIT 10;
```

**</> Summarize numeric columns**

Summarize the profit column in the fortune500 table using the functions you've learned.

1. Compute the min(), avg(), max(), and stddev() of profits.

```sql
-- Select min, avg, max, and stddev of fortune500 profits
SELECT min(profits),
       avg(profits),
       max(profits),
       stddev(profits)
  FROM fortune500;
```

2. - Now repeat step 1, but summarize profits by sector.
   - Order the results by the average profits for each sector

```sql
-- Select sector and summary measures of fortune500 profits
SELECT sector,
       min(profits),
       avg(profits),
       max(profits),
       stddev(profits)
  FROM fortune500
 -- What to group by?
 GROUP BY sector
 -- Order by the average profits
 ORDER BY avg;
```

**</> Summarize group statistics**

Sometimes you want to understand how a value varies across groups. For example, how does the maximum value per group vary across groups?

To find out, first summarize by group, and then compute summary statistics of the group results. One way to do this is to compute group values in a subquery, and then summarize the results of the subquery.

For this exercise, what is the standard deviation across tags in the maximum number of Stack Overflow questions per day? What about the mean, min, and max of the maximums as well?

- Start by writing a subquery to compute the max() of question_count per tag; alias the subquery result as maxval.
- Then compute the standard deviation of maxval with stddev().
- Compute the min(), max(), and avg() of maxval too.

```sql
-- Compute standard deviation of maximum values
SELECT stddev(maxval),
	   -- min
       min(maxval),
       -- max
       max(maxval),
       -- avg
       avg(maxval)
  -- Subquery to compute max of question_count by tag
  FROM (SELECT max(question_count) AS maxval
          FROM stackoverflow
         -- Compute max by...
         GROUP BY tag) AS max_results; -- alias for subquery
```

Note: A subquery was necessary here because the tag maximums must be computed before you can summarize them.


