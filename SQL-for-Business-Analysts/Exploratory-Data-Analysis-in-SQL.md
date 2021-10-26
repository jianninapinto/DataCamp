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

Truncate
Use trunc() to examine the distributions of attributes of the Fortune 500 companies.

1. - Use trunc() to truncate employees to the 100,000s (5 zeros).
   - Count the number of observations with each truncated value.

```sql
-- Truncate employees
SELECT trunc(employees, -5) AS employee_bin,
       -- Count number of companies with each truncated value
       Count(*)
  FROM fortune500
 -- Use alias to group
 GROUP BY employee_bin
 -- Use alias to order
 ORDER BY employee_bin;
```

2. - Repeat step 1 for companies with < 100,000 employees (most common).
   - This time, truncate employees to the 10,000s place.

```sql
-- Truncate employees
SELECT trunc(employees, -4) AS employee_bin,
       -- Count number of companies with each truncated value
       COUNT(*)
  FROM fortune500
 -- Limit to which companies?
 WHERE employees < 100000
 -- Use alias to group
 GROUP BY employee_bin
 -- Use alias to order
 ORDER BY employee_bin;
```

**</> Generate series**

Summarize the distribution of the number of questions with the tag "dropbox" on Stack Overflow per day by binning the data.

- Start by selecting the minimum and maximum of the question_count column for the tag 'dropbox' so you know the range of values to cover with the bins.

```sql
-- Select the min and max of question_count
SELECT min(question_count), 
       max(question_count)
  -- From what table?
  FROM stackoverflow
 -- For tag dropbox
 WHERE tag = 'dropbox';
```

Answer: min = 2315 max = 3072

- Next, use generate_series() to create bins of size 50 from 2200 to 3100.

	- To do this, you need an upper and lower bound to define a bin.

	- This will require you to modify the stopping value of the lower bound and the starting value of the upper bound by the bin width.

```sql
-- Create lower and upper bounds of bins
SELECT generate_series(2200, 3050, 50) AS lower,
       generate_series(2250, 3100, 50) AS upper;
```

- Select lower and upper from bins, along with the count of values within each bin bounds.

	- To do this, you'll need to join 'dropbox', which contains the question_count for tag "dropbox", to the bins created by generate_series().

	- The join should occur where the count is greater than or equal to the lower bound, and strictly less than the upper bound.

```sql
-- Bins created in Step 2
WITH bins AS (
      SELECT generate_series(2200, 3050, 50) AS lower,
             generate_series(2250, 3100, 50) AS upper),
     -- Subset stackoverflow to just tag dropbox (Step 1)
     dropbox AS (
      SELECT question_count 
        FROM stackoverflow
       WHERE tag='dropbox') 
-- Select columns for result
-- What column are you counting to summarize?
SELECT lower, upper, count(question_count) 
  FROM bins  -- Created above
       -- Join to dropbox (created above), 
       -- keeping all rows from the bins table in the join
       LEFT JOIN dropbox
       -- Compare question_count to lower and upper
         ON question_count >= lower 
        AND question_count < upper
 -- Group by lower and upper to count values in each bin
 GROUP BY lower, upper
 -- Order by lower to put bins in order
 ORDER BY lower;
```

**</> Correlation**

What's the relationship between a company's revenue and its other financial attributes? Compute the correlation between revenues and other financial variables with the corr() function.

- Compute the correlation between revenues and profits.
- Compute the correlation between revenues and assets.
- Compute the correlation between revenues and equity.

```sql
-- Correlation between revenues and profit
SELECT corr(revenues, profits) AS rev_profits,
	   -- Correlation between revenues and assets
       corr(revenues, assets) AS rev_assets,
       -- Correlation between revenues and equity
       corr(revenues, equity) AS rev_equity 
  FROM fortune500;
```

Answer: Profits, assets and equity are positively correlated with revenue for Fortune 500 companies. 
	
	rev_profits: 0.599993581572479	
	rev_assets: 0.329499521318506
	rev_equity: 0.546570999718431
	

**</> Mean and Median**

Compute the mean (avg()) and median assets of Fortune 500 companies by sector.

Use the percentile_disc() function to compute the median:

`
percentile_disc(0.5) 
WITHIN GROUP (ORDER BY column_name)
`

- Select the mean and median of assets.
- Group by sector.
- Order the results by the mean.

```sql
-- What groups are you computing statistics by?
SELECT sector,
       -- Select the mean of assets with the avg function
       avg(assets) AS mean,
       -- Select the median
       percentile_disc(0.5) WITHIN GROUP (ORDER BY assets) AS median
  FROM fortune500
 -- Computing statistics for each what?
 GROUP BY sector
 -- Order results by a value of interest
 ORDER BY mean;
```

Answer: The mean and median can differ significantly for skewed distributions that have a few extreme values.

**</> Create a temp table**

Find the Fortune 500 companies that have profits in the top 20% for their sector (compared to other Fortune 500 companies).

To do this, first, find the 80th percentile of profit for each sector with

```sql
percentile_disc(fraction) 
WITHIN GROUP (ORDER BY sort_expression)
```

and save the results in a temporary table.

Then join fortune500 to the temporary table to select companies with profits greater than the 80th percentile cut-off.

- Create a temporary table called profit80 containing the sector and 80th percentile of profits for each sector.
- Alias the percentile column as pct80.

```sql
-- To clear table if it already exists;
-- fill in name of temp table
DROP TABLE IF EXISTS profit80;

-- Create the temporary table
CREATE TEMP TABLE profit80 AS 
  -- Select the two columns you need; alias as needed
  SELECT sector, 
         percentile_disc(.8) WITHIN GROUP (ORDER BY profits) AS pct80
    -- What table are you getting the data from?
    FROM fortune500
   -- What do you need to group by?
   GROUP BY sector;
   
-- See what you created: select all columns and rows 
-- from the table you created
SELECT * 
  FROM profit80;
```

- Using the profit80 table you created in step 1, select companies that have profits greater than pct80.
- Select the title, sector, profits from fortune500, as well as the ratio of the company's profits to the 80th percentile profit.

```sql
-- Code from previous step
DROP TABLE IF EXISTS profit80;

CREATE TEMP TABLE profit80 AS
  SELECT sector, 
         percentile_disc(0.8) WITHIN GROUP (ORDER BY profits) AS pct80
    FROM fortune500 
   GROUP BY sector;

-- Select columns, aliasing as needed
SELECT title, fortune500.sector, 
       profits, profits/pct80 AS ratio
-- What tables do you need to join?  
  FROM fortune500 
       LEFT JOIN profit80
-- How are the tables joined?
       ON fortune500.sector=profit80.sector
-- What rows do you want to select?
 WHERE profits > pct80;
```

Answer: Some of these companies are Walmart, Berkshire Hathaway	, Apple, Exxon Mobil, McKesson, UnitedHealth Group, CVS Health, General Motors, AT&T and AmerisourceBergen.

**</> Create a temp table to simplify a query**

The Stack Overflow data contains daily question counts through 2018-09-25 for all tags, but each tag has a different starting date in the data.

Find out how many questions had each tag on the first date for which data for the tag is available, as well as how many questions had the tag on the last day. Also, compute the difference between these two values.

To do this, first compute the minimum date for each tag.

Then use the minimum dates to select the question_count on both the first and last day. To do this, join the temp table startdates to two different copies of the stackoverflow table: one for each column - first day and last day - aliased with different names.

- First, create a temporary table called startdates with each tag and the min() date for the tag in stackoverflow.

```sql
-- To clear table if it already exists
DROP TABLE IF EXISTS startdates;

-- Create temp table syntax
CREATE TEMP TABLE startdates AS
-- Compute the minimum date for each what?
SELECT tag,
       min(date) AS mindate
  FROM stackoverflow
 -- What do you need to compute the min date for each tag?
 GROUP BY tag;
 
 -- Look at the table you created
 SELECT * 
   FROM startdates;
```

- Join startdates to stackoverflow twice using different table aliases.
- For each tag, select mindate, question_count on the mindate, and question_count on 2018-09-25 (the max date).
- Compute the change in question_count over time.

```sql
-- To clear table if it already exists
DROP TABLE IF EXISTS startdates;

CREATE TEMP TABLE startdates AS
SELECT tag, min(date) AS mindate
  FROM stackoverflow
 GROUP BY tag;
 
-- Select tag (Remember the table name!) and mindate
SELECT startdates.tag, 
       mindate, 
       -- Select question count on the min and max days
	   so_min.question_count AS min_date_question_count,
       so_max.question_count AS max_date_question_count,
       -- Compute the change in question_count (max- min)
       so_max.question_count - so_min.question_count AS change
  FROM startdates
       -- Join startdates to stackoverflow with alias so_min
       INNER JOIN stackoverflow AS so_min
          -- What needs to match between tables?
          ON startdates.tag = so_min.tag
         AND startdates.mindate = so_min.date
       -- Join to stackoverflow again with alias so_max
       INNER JOIN stackoverflow AS so_max
       	  -- Again, what needs to match between tables?
          ON startdates.tag = so_max.tag
         AND so_max.date = '2018-09-25';
```

**</> Insert into a temp table**

While you can join the results of multiple similar queries together with UNION, sometimes it's easier to break a query down into steps. You can do this by creating a temporary table and inserting rows into it.

Compute the correlations between each pair of profits, profits_change, and revenues_change from the Fortune 500 data.

The resulting temporary table should have the following structure:

| measure         | profits | profits_change | revenues_change |
|-----------------|---------|----------------|-----------------|
| profits         | 1.00    | #              | #               |
| profits_change  | #       | 1.00           | #               | 
| revenues_change | #       | #              | 1.00            | 

Recall the round() function to make the results more readable:

`round(column_name::numeric, decimal_places)`

Note that Steps 1 and 2 do not produce output. It is normal for the query result pane to say "Your query did not generate any results."

Create a temp table correlations.

- Compute the correlation between profits and each of the three variables (i.e. correlate profits with profits, profits with profits_change, etc).
- Alias columns by the name of the variable for which the correlation with profits is being computed.

```sql
DROP TABLE IF EXISTS correlations;

-- Create temp table 
CREATE TEMP TABLE correlations AS
-- Select each correlation
SELECT 'profits'::varchar AS measure,
       -- Compute correlations
       corr(profits, profits) AS profits,
       corr(profits, profits_change) AS profits_change,
       corr(profits, revenues_change) AS revenues_change
  FROM fortune500;
```

- Insert rows into the correlations table for profits_change and revenues_change.

```sql
DROP TABLE IF EXISTS correlations;

CREATE TEMP TABLE correlations AS
SELECT 'profits'::varchar AS measure,
       corr(profits, profits) AS profits,
       corr(profits, profits_change) AS profits_change,
       corr(profits, revenues_change) AS revenues_change
  FROM fortune500;

-- Add a row for profits_change
-- Insert into what table?
INSERT INTO correlations
-- Follow the pattern of the select statement above
-- Using profits_change instead of profits
SELECT 'profits_change'::varchar AS measure,
       corr(profits_change, profits) AS profits,
       corr(profits_change, profits_change) AS profits_change,
       corr(profits_change, revenues_change) AS revenues_change
  FROM fortune500;

-- Repeat the above, but for revenues_change
INSERT INTO correlations
SELECT 'revenues_change'::varchar AS measure,
       corr(revenues_change, profits) AS profits,
       corr(revenues_change, profits_change) AS profits_change,
       corr(revenues_change, revenues_change) AS revenues_change
  FROM fortune500;
```

- Select all rows and columns from the correlations table to view the correlation matrix.

	- First, you will need to round each correlation to 2 decimal places.
	- The output of corr() is of type double precision, so you will need to also cast columns to numeric.

```sql
DROP TABLE IF EXISTS correlations;

CREATE TEMP TABLE correlations AS
SELECT 'profits'::varchar AS measure,
       corr(profits, profits) AS profits,
       corr(profits, profits_change) AS profits_change,
       corr(profits, revenues_change) AS revenues_change
  FROM fortune500;

INSERT INTO correlations
SELECT 'profits_change'::varchar AS measure,
       corr(profits_change, profits) AS profits,
       corr(profits_change, profits_change) AS profits_change,
       corr(profits_change, revenues_change) AS revenues_change
  FROM fortune500;

INSERT INTO correlations
SELECT 'revenues_change'::varchar AS measure,
       corr(revenues_change, profits) AS profits,
       corr(revenues_change, profits_change) AS profits_change,
       corr(revenues_change, revenues_change) AS revenues_change
  FROM fortune500;

-- Select each column, rounding the correlations
SELECT measure, 
       ROUND(profits::numeric, 2) AS profits,
       ROUND(profits_change::numeric, 2) AS profits_change,
       ROUND(revenues_change::numeric, 2) AS revenues_change
  FROM correlations;
```

| measure         | profits | profits_change | revenues_change |
|-----------------|---------|----------------|-----------------|
| profits         | 1.00    | 0.02           | 0.02            |
| profits_change  | 0.02    | 1.00           | -0.09           |
| revenues_change | 0.02    | -0.09          | 1.00            |



# 3. Exploring categorical data and unstructured text

**</> Count the categories**

In this chapter, we'll be working mostly with the Evanston 311 data in table evanston311. This is data on help requests submitted to the city of Evanston, IL.

This data has several character columns. Start by examining the most frequent values in some of these columns to get familiar with the common categories.

1. - How many rows does each priority level have?

```sql
-- Select the count of each level of priority
SELECT priority, count(*)
  FROM evanston311
 GROUP BY priority;
```

| priority | count |
|----------|-------|
| MEDIUM   | 5745  |
| NONE     | 30081 |
| HIGH     | 88    |
| LOW      | 517   |

2. - How many distinct values of zip appear in at least 100 rows?

```sql
-- Find values of zip that appear in at least 100 rows
-- Also get the count of each value
SELECT DISTINCT(zip), COUNT(*)
  FROM evanston311
 GROUP BY zip
  HAVING COUNT(*) > 100; 
```

| zip   | count |
|-------|-------|
| 60201 | 19054 |
| 60202 | 11165 |
| 60208 | 255   |
| null  | 5528  |


3. - How many distinct values of source appear in at least 100 rows?


```sql
-- Find values of source that appear in at least 100 rows
-- Also get the count of each value
SELECT DISTINCT(source), COUNT(*)
  FROM evanston311
 GROUP BY source
HAVING COUNT(*) > 100;
```

| source              | count |
|---------------------|-------|
| Android             | 444   |
| Iframe              | 3670  |
| gov.publicstuff.com | 30985 |
| iOS                 | 1199  |


4. - Select the five most common values of street and the count of each.

```sql
-- Find the 5 most common values of street and the count of each
SELECT street, COUNT(*)
  FROM evanston311
 GROUP BY street
 ORDER BY COUNT(*) DESC
 LIMIT 5;
```

| street         | count |
|----------------|-------|
| null           | 1699  |
| Chicago Avenue | 1440  |
| Sherman Avenue | 1276  |
| Central Street | 1211  |
| Davis Street   | 1154  |


**</> Spotting character data problems**

Explore the distinct values of the street column. Select each street value and the count of the number of rows with that value. Sort the results by street to see similar values near each other.

Look at the results.

Which of the following is NOT an issue you see with the values of street?

```sql
-- Get the distinct values of the street column
-- Count the number of rows with each street value
SELECT DISTINCT(street), COUNT(*)
-- Where are you getting the data from?
    FROM evanston311
-- Group and sort the results by street
    GROUP BY street
    ORDER BY COUNT(*);
```

Possible Answers

a. The street suffix (e.g. Street, Avenue) is sometimes abbreviated

`b. There are sometimes extra spaces at the beginning and end of values`

c. House/street numbers sometimes appear in the column

d. Capitalization is not consistent across values

e. All of the above are potential problems

**</> Trimming**

Some of the street values in evanston311 include house numbers with # or / in them. In addition, some street values end in a ..

Remove the house numbers, extra punctuation, and any spaces from the beginning and end of the street values as a first attempt at cleaning up the values.

- Trim digits 0-9, #, /, ., and spaces from the beginning and end of street.
- Select distinct original street value and the corrected street value.
- Order the results by the original street value.

```sql
SELECT distinct street,
       -- Trim off unwanted characters from street
       trim(street, '0123456789 #/.') AS cleaned_street
  FROM evanston311
 ORDER BY street;
```

| street              | cleaned_street      |
|---------------------|---------------------|
| 1/2 Chicago Ave     | Chicago Ave         |
| 1047B Chicago Ave   | B Chicago Ave       |
| 13th Street         | th Street           |
| 141A Callan Ave     | A Callan Ave        |
| 141b Callan Ave     | b Callan Ave        |
| 1624B Central St    | B Central St        |
| 217A Dodge Ave      | A Dodge Ave         |
| 221c Dodge Ave      | c Dodge Ave         |
| 300c Dodge Ave      | c Dodge Ave         |
| 3314A Central St    | A Central St        |
| 36th Street         | th Street           |
| 600A South Blvd     | A South Blvd        |
| 606B South Blvd     | B South Blvd        |
| 612C South Blvd     | C South Blvd        |
| 613B Custer Ave     | B Custer Ave        |
| 618B South Blvd     | B South Blvd        |
| 6th Street          | th Street           |
| Arlington Boulevard | Arlington Boulevard |
| Arnold Pl           | Arnold Pl           |

Note: the "cleaned" values still include letters from house numbers, and trim() stripped off some numbers that belong as part of road names. It can take several tries to find the right combination of functions to clean up messy values.


