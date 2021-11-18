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


**</> Exploring unstructured text**

The description column of evanston311 has the details of the inquiry, while the category column groups inquiries into different types. How well does the category capture what's in the description?

LIKE and ILIKE queries will help you find relevant descriptions and categories. 

Building up the query through the steps below, find inquires that mention trash or garbage in the description without trash or garbage being in the category. What are the most frequent categories for such inquiries?

- Use ILIKE to count rows in evanston311 where the description contains 'trash' or 'garbage' regardless of case.

```sql
-- Count rows
SELECT COUNT(*)
  FROM evanston311
 -- Where description includes trash or garbage
 WHERE description ILIKE '%trash%' 
    or description ILIKE '%garbage%';
```

| count |
|-------|
| 2551  |


- category values are in title case. Use LIKE to find category values with 'Trash' or 'Garbage' in them.

```sql
-- Select categories containing Trash or Garbage
SELECT category
  FROM evanston311
 -- Use LIKE
 WHERE category LIKE '%Trash%'
    OR category LIKE '%Garbage%';
```

| category                                              |
|-------------------------------------------------------|
| THIS REQUEST IS INACTIVE...Trash Cart - Compost Bin   |
| Trash - Tire Pickup                                   |
| Trash - Special Pickup - Resident Use                 |
| Trash, Recycling, Yard Waste Cart- Repair/Replacement |
| Trash, Recycling, Yard Waste Cart- Repair/Replacement |
| Trash - Missed Garbage Pickup                         |
| THIS REQUEST IS INACTIVE...Trash Cart - Compost Bin   |

Showing 7 out of 5816 rows

- Count rows where the description includes 'trash' or 'garbage' but the category does not.

```sql
-- Count rows
SELECT Count(*)
  FROM evanston311 
 -- description contains trash or garbage (any case)
 WHERE (description ILIKE '%trash%'
    OR description ILIKE '%garbage%') 
 -- category does not contain Trash or Garbage
   AND category NOT LIKE '%Trash%'
   AND category NOT LIKE '%Garbage%';
```

| count |
|-------|
| 570   |


- Find the most common categories for rows with a description about trash that don't have a trash-related category.

```sql
-- Count rows with each category
SELECT category, COUNT(*)
  FROM evanston311 
 WHERE (description ILIKE '%trash%'
    OR description ILIKE '%garbage%') 
   AND category NOT LIKE '%Trash%'
   AND category NOT LIKE '%Garbage%'
 -- What are you counting?
 GROUP BY category
 --- order by most frequent values
 ORDER BY COUNT(*) DESC
 LIMIT 10;
```

| category                                   | count |
|--------------------------------------------|-------|
| Ask A Question / Send A Message            | 273   |
| Rodents- Rats                              | 77    |
| Recycling - Missed Pickup                  | 28    |
| Dead Animal on Public Property             | 16    |
| Graffiti                                   | 15    |
| Yard Waste - Missed Pickup                 | 14    |
| Public Transit Agency Issue                | 13    |
| Food Establishment - Unsanitary Conditions | 13    |
| Exterior Conditions                        | 10    |
| Street Sweeping                            | 9     |

**</> Concatenate strings**

House number (house_num) and street are in two separate columns in evanston311. Concatenate them together with concat() with a space in between the values.

- Concatenate house_num, a space ' ', and street into a single value using the concat().
- Use a trim function to remove any spaces from the start of the concatenated value.

```sql
-- Concatenate house_num, a space, and street
-- and trim spaces from the start of the result
SELECT ltrim(concat(house_num, ' ', street)) AS address
  FROM evanston311;
```

| address               |
|-----------------------|
| 606-612 Sheridan Road |
| 930 Washington St     |
| 1183-1223 Lincoln St  |
| 1–111 Callan Ave      |
| 1524 Crain St         |
| 2830 Central Street   |

Showing 6 out of 36431 rows


**</> Split strings on a delimiter**

The street suffix is the part of the street name that gives the type of street, such as Avenue, Road, or Street. In the Evanston 311 data, sometimes the street suffix is the full word, while other times it is the abbreviation.

Extract just the first word of each street value to find the most common streets regardless of the suffix.

To do this, use

`split_part(string_to_split, delimiter, part_number)`

- Use split_part() to select the first word in street; alias the result as street_name.
- Also select the count of each value of street_name.

```sql
-- Select the first word of the street value
SELECT split_part(street, ' ', 1) AS street_name, 
       count(*)
  FROM evanston311
 GROUP BY street_name
 ORDER BY count DESC
 LIMIT 20;
```

| street_name | count |
|-------------|-------|
| null        | 1699  |
| Chicago     | 1569  |
| Central     | 1529  |
| Sherman     | 1479  |
| Davis       | 1248  |
| Church      | 1225  |
| Main        | 880   |
| Sheridan    | 842   |
| Ridge       | 823   |
| Dodge       | 816   |
| Maple       | 778   |
| Asbury      | 675   |
| Hinman      | 586   |
| West        | 578   |
| Orrington   | 561   |
| Emerson     | 513   |
| Grove       | 498   |
| Darrow      | 489   |
| Custer      | 464   |
| Lake        | 444   |


**</> Shorten long strings**

The description column of evanston311 can be very long. You can get the length of a string with the length() function.

For displaying or quickly reviewing the data, you might want to only display the first few characters. You can use the left() function to get a specified number of characters at the start of each value.

To indicate that more data is available, concatenate '...' to the end of any shortened description. To do this, you can use a CASE WHEN statement to add '...' only when the string length is greater than 50.

Select the first 50 characters of description when description starts with the word "I".

- Select the first 50 characters of description with '...' concatenated on the end where the length() of the description is greater than 50 characters. Otherwise just select the description as is.

- Select only descriptions that begin with the word 'I' and not the letter 'I'.

	- For example, you would want to select "I like using SQL!", but would not want to select "In this course we use SQL!"

```sql
-- Select the first 50 chars when length is greater than 50
SELECT CASE WHEN length(description) > 50
            THEN left (description, 50) || '...'
       -- otherwise just select description
       ELSE description
       END
  FROM evanston311
 -- limit to descriptions that start with the word I
 WHERE description LIKE 'I %'
 ORDER BY description;
```

| description                                           |
|-------------------------------------------------------|
| I  work for Schermerhorn & Co. and manage this con... |
| I Live in a townhouse with garbage cans in back, i... |
| I Put In For Reserve Disabled Parking, A Week Ago ... |
| I SDO GOWANS #1258 RECEIVED A TELEPHONE CALL ON 3/... |
| I accidentally mistyped my license plate number - ... |
| I accidentally sent the wrong cover letter on my a... |
| I acquired c diff at north shore hospital in Evans... |
| I am a 35 year resident of Evanston (314 Custer Av... |
| I am a Cubs fan and watched game seven. But using ... |
| I am a Northwestern student that has accumulated t... |
| I am a PTA president at Dewey Elementary.  Alongsi... |
| I am a business owner at 1121 Emerson St at the co... |
| I am a current customer at 1333 Maple Ave, Unit 2E... |


**</> Create an "other" category**

If we want to summarize Evanston 311 requests by zip code, it would be useful to group all of the low frequency zip codes together in an "other" category.

Which of the following values, when substituted for ??? in the query, would give the result below?

Query:

```sql
SELECT CASE WHEN zipcount < ??? THEN 'other'
       ELSE zip
       END AS zip_recoded,
       sum(zipcount) AS zipsum
  FROM (SELECT zip, count(*) AS zipcount
          FROM evanston311
         GROUP BY zip) AS fullcounts
 GROUP BY zip_recoded
 ORDER BY zipsum DESC;
```

Result:

| zip_recoded | zipsum |
|-------------|--------|
| 60201       | 19054  |
| 60202       | 11165  |
| null        | 5528   |
| other       | 429    |
| 60208       | 255    |

**Possible Answers**

a. 255

b. 1000

`c. 100`

d. 60201


**</> Group and recode values**

There are almost 150 distinct values of evanston311.category. But some of these categories are similar, with the form "Main Category - Details". We can get a better sense of what requests are common if we aggregate by the main category.

To do this, create a temporary table recode mapping distinct category values to new, standardized values. Make the standardized values the part of the category before a dash ('-'). Extract this value with the split_part() function:

`split_part(string text, delimiter text, field int)`

You'll also need to do some additional cleanup of a few cases that don't fit this pattern.

Then the evanston311 table can be joined to recode to group requests by the new standardized category values.

- Create recode with a standardized column; use split_part() and then rtrim() to remove any remaining whitespace on the result of split_part().

```sql
-- Fill in the command below with the name of the temp table
DROP TABLE IF EXISTS recode;

-- Create and name the temporary table
CREATE TEMP TABLE recode AS
-- Write the select query to generate the table 
-- with distinct values of category and standardized values
  SELECT DISTINCT category, 
         rtrim(split_part(category, '-', 1)) AS standardized
    -- What table are you selecting the above values from?
    FROM evanston311;
    
-- Look at a few values before the next step
SELECT DISTINCT standardized 
  FROM recode
 WHERE standardized LIKE 'Trash%Cart'
    OR standardized LIKE 'Snow%Removal%';
```

| standardized                      |
|-----------------------------------|
| Snow Removal                      |
| Snow Removal/Concerns             |
| Snow/Ice/Hazard Removal           |
| Trash Cart                        |
| Trash Cart, Recycling Cart        |
| Trash, Recycling, Yard Waste Cart |


- UPDATE standardized values LIKE 'Trash%Cart' to 'Trash Cart'.
- UPDATE standardized values of 'Snow Removal/Concerns' and 'Snow/Ice/Hazard Removal' to 'Snow Removal'.

```sql
-- Code from previous step
DROP TABLE IF EXISTS recode;

CREATE TEMP TABLE recode AS
  SELECT DISTINCT category, 
         rtrim(split_part(category, '-', 1)) AS standardized
    FROM evanston311;

-- Update to group trash cart values
UPDATE recode 
   SET standardized='Trash Cart' 
 WHERE standardized LIKE 'Trash%Cart';

-- Update to group snow removal values
UPDATE recode 
   SET standardized='Snow Removal'
 WHERE standardized LIKE 'Snow Removal/Concerns' OR standardized LIKE 'Snow/Ice/Hazard Removal';
    
-- Examine effect of updates
SELECT DISTINCT standardized 
  FROM recode
 WHERE standardized LIKE 'Trash%Cart'
    OR standardized LIKE 'Snow%Removal%';
```

| standardized |
|--------------|
| Snow Removal |
| Trash Cart   |


- UPDATE recode by setting standardized values of 'THIS REQUEST IS INACTIVE...Trash Cart', '(DO NOT USE) Water Bill', 'DO NOT USE Trash', and 'NO LONGER IN USE' to 'UNUSED'.

```sql
-- Code from previous step
DROP TABLE IF EXISTS recode;

CREATE TEMP TABLE recode AS
  SELECT DISTINCT category, 
         rtrim(split_part(category, '-', 1)) AS standardized
    FROM evanston311;
  
UPDATE recode SET standardized='Trash Cart' 
 WHERE standardized LIKE 'Trash%Cart';

UPDATE recode SET standardized='Snow Removal' 
 WHERE standardized LIKE 'Snow%Removal%';

-- Update to group unused/inactive values
UPDATE recode 
   SET standardized='UNUSED' 
 WHERE standardized IN ('THIS REQUEST IS INACTIVE...Trash Cart', 
               '(DO NOT USE) Water Bill',
               'DO NOT USE Trash', 
               'NO LONGER IN USE');

-- Examine effect of updates
SELECT DISTINCT standardized 
  FROM recode
 ORDER BY standardized;
```

| standardized                            |
|-----------------------------------------|
| ADA/Inclusion Aids                      |
| Abandoned Bicycle on City Property      |
| Abandoned Vehicle                       |
| Accessibility                           |
| Advanced Disposal                       |
| Alarm Registration                      |
| Alleys                                  |
| Amplified Sounds and/or Music           |
| Animal Issue/Concern                    |
| Animal Service      			  |

Showing 10 out of 115 rows

- Now, join the evanston311 and recode tables to count the number of requests with each of the standardized values
- List the most common standardized values first.

```sql
-- Code from previous step
DROP TABLE IF EXISTS recode;
CREATE TEMP TABLE recode AS
  SELECT DISTINCT category, 
         rtrim(split_part(category, '-', 1)) AS standardized
  FROM evanston311;
UPDATE recode SET standardized='Trash Cart' 
 WHERE standardized LIKE 'Trash%Cart';
UPDATE recode SET standardized='Snow Removal' 
 WHERE standardized LIKE 'Snow%Removal%';
UPDATE recode SET standardized='UNUSED' 
 WHERE standardized IN ('THIS REQUEST IS INACTIVE...Trash Cart', 
               '(DO NOT USE) Water Bill',
               'DO NOT USE Trash', 'NO LONGER IN USE');

-- Select the recoded categories and the count of each
SELECT standardized, COUNT(*)
-- From the original table and table with recoded values
  FROM evanston311 
       LEFT JOIN recode 
       -- What column do they have in common?
       ON evanston311.category = recode.category 
 -- What do you need to group by to count?
 GROUP BY standardized
 -- Display the most common val values first
 ORDER BY COUNT(*) DESC;
```

| standardized                                                     | count |
|------------------------------------------------------------------|-------|
| Broken Parking Meter                                             | 6092  |
| Trash                                                            | 3699  |
| Ask A Question / Send A Message                                  | 2595  |
| Trash Cart                                                       | 1902  |
| Tree Evaluation                                                  | 1879  |
| Rodents                                                          | 1305  |
| Recycling                                                        | 1224  |
| Dead Animal on Public Property                                   | 1057  |
| Child Seat Installation or Inspection                            | 1028  |
| Fire Prevention                                                  | 880   |

Showing 10 out of 115


**</> Create a table with indicator variables**

Determine whether medium and high priority requests in the evanston311 data are more likely to contain requesters' contact information: an email address or phone number.

- Emails contain an @.
- Phone numbers have the pattern of three characters, dash, three characters, dash, four characters. For example: 555-555-1212.
- 
Use LIKE to match these patterns. Remember % matches any number of characters (even 0), and _ matches a single character. Enclosing a pattern in % (i.e. before and after your pattern) allows you to locate it within other text.

For example, '%___.com%'would allow you to search for a reference to a website with the top-level domain '.com' and at least three characters preceding it.

Create and store indicator variables for email and phone in a temporary table. LIKE produces True or False as a result, but casting a boolean (True or False) as an integer converts True to 1 and False to 0. This makes the values easier to summarize later.

- Create a temp table indicators from evanston311 with three columns: id, email, and phone.
- Use LIKE comparisons to detect the email and phone patterns that are in the description, ad cast the result as an integer with CAST().

	- Your phone indicator should use a combination of underscores _ and dashes - to represent a standard 10-digit phone number format.
	- Remember to start and end your patterns with % so that you can locate the pattern within other text!

```sql
-- To clear table if it already exists
DROP TABLE IF EXISTS indicators;

-- Create the indicators temp table
CREATE TEMP TABLE indicators AS
  -- Select id
  SELECT id, 
         -- Create the email indicator (find @)
         CAST (description LIKE '%@%' AS integer) AS email,
         -- Create the phone indicator
         CAST (description LIKE '%___-___-____%' AS integer) AS phone 
    -- What table contains the data? 
    FROM evanston311;

-- Inspect the contents of the new temp table
SELECT *
  FROM indicators;
```

| id      | email | phone |
|---------|-------|-------|
| 1340563 | 0     | 0     |
| 1826017 | 0     | 0     |
| 1849204 | 0     | 0     |
| 1880254 | 0     | 0     |
| 1972582 | 0     | 1     |
| 1840025 | 0     | 0     |
| 2099219 | 0     | 0     |
| 2554820 | null  | null  |
| 1770749 | 0     | 0     |

- Join the indicators table to evanston311, selecting the proportion of reports including an email or phone grouped by priority.
- Include adjustments to account for issues arising from integer division.

```sql
-- To clear table if it already exists
DROP TABLE IF EXISTS indicators;

-- Create the temp table
CREATE TEMP TABLE indicators AS
  SELECT id, 
         CAST (description LIKE '%@%' AS integer) AS email,
         CAST (description LIKE '%___-___-____%' AS integer) AS phone 
    FROM evanston311;
  
-- Select the column you'll group by
SELECT priority,
       -- Compute the proportion of rows with each indicator
       SUM(email)/COUNT(*)::numeric AS email_prop, 
       SUM(phone)/COUNT(*)::numeric AS phone_prop
  -- Tables to select from
  FROM evanston311
       LEFT JOIN indicators
       -- Joining condition
       ON evanston311.id=indicators.id
 -- What are you grouping by?
 GROUP BY priority;
```

| priority | email_prop             | phone_prop             |
|----------|------------------------|------------------------|
| MEDIUM   | 0.01966927763272410792 | 0.01845082680591818973 |
| NONE     | 0.00412220338419600412 | 0.00568465144110900568 |
| HIGH     | 0.01136363636363636364 | 0.02272727272727272727 |
| LOW      | 0.00580270793036750484 | 0.00193423597678916828 |


# 4. Working with dates and timestamps

**</> ISO 8601**

Which date format below conforms to the ISO 8601 standard?


a. June 15, 2018 3:30pm

`b. 2018-06-15 15:30:00`

c. 6/15/18 13:00

d. 2018-6-15 3:30:00

Note: The units are ordered from largest to smallest, and single digit values have a leading zero.

**</> Date comparisons**

When working with timestamps, sometimes you want to find all observations on a given day. However, if you specify only a date in a comparison, you may get unexpected results. This query:

```sql
SELECT count(*) 
  FROM evanston311
 WHERE date_created = '2018-01-02';
returns 0, even though there were 49 requests on January 2, 2018.
```

This is because dates are automatically converted to timestamps when compared to a timestamp. The time fields are all set to zero:

```sql
SELECT '2018-01-02'::timestamp;
 2018-01-02 00:00:00
 ```
When working with both timestamps and dates, you'll need to keep this in mind.

- Count the number of Evanston 311 requests created on January 31, 2017 by casting date_created to a date.

```sql 
-- Count requests created on January 31, 2017
SELECT count(*) 
  FROM evanston311
 WHERE date_created::date = '2017-01-31';
```

| count |
|-------|
| 45    |

- Count the number of Evanston 311 requests created on February 29, 2016 by using >= and < operators.

```sql
-- Count requests created on February 29, 2016
SELECT count(*)
  FROM evanston311 
 WHERE date_created::date >= '2016-02-29' 
   AND date_created::date < '2016-03-01';
```

| count |
|-------|
| 58    |

- Count the number of requests created on March 13, 2017.
- Specify the upper bound by adding 1 to the lower bound.

```sql
-- Count requests created on March 13, 2017
SELECT count(*)
  FROM evanston311
 WHERE date_created::date >= '2017-03-13'
   AND date_created::date < '2017-03-13'::date + 1;
```

| count |
|-------|
| 33    |


**</> Date arithmetic**

You can subtract dates or timestamps from each other.

You can add time to dates or timestamps using intervals. An interval is specified with a number of units and the name of a datetime field. For example:

'3 days'::interval
'6 months'::interval
'1 month 2 years'::interval
'1 hour 30 minutes'::interval
Practice date arithmetic with the Evanston 311 data and now().

1. Subtract the minimum date_created from the maximum date_created.

```sql
-- Subtract the min date_created from the max
SELECT MAX(date_created) - MIN(date_created)
  FROM evanston311;
```

| ?column?           |
|--------------------|
| 911 days, 16:33:39 |

2. Using now(), find out how old the most recent evanston311 request was created.

```sql
-- How old is the most recent request?
SELECT now() - MAX(date_created)
  FROM evanston311;
```

| ?column?                   |
|----------------------------|
| 1228 days, 11:12:47.152687 |

3. Add 100 days to the current timestamp.

```sql
-- Add 100 days to the current timestamp
SELECT now() + '100 days':: interval;
```

| ?column?                         |
|----------------------------------|
| 2022-02-18 03:50:58.972975+00:00 |

4. Select the current timestamp and the current timestamp plus 5 minutes.

```sql
-- Select the current timestamp, 
-- and the current timestamp + 5 minutes
SELECT now(),
    now() + '5 minutes':: interval;
```

| now                              | ?column?                         |
|----------------------------------|----------------------------------|
| 2021-11-10 03:52:53.397112+00:00 | 2021-11-10 03:57:53.397112+00:00 |


**</>Completion time by category**

The evanston311 data includes a date_created timestamp from when each request was created and a date_completed timestamp for when it was completed. The difference between these tells us how long a request was open.

Which category of Evanston 311 requests takes the longest to complete?

- Compute the average difference between the completion timestamp and the creation timestamp by category.
- Order the results with the largest average time to complete the request first.

```sql
-- Select the category and the average completion time by category
SELECT category, 
       AVG(date_completed-date_created) AS completion_time
  FROM evanston311
 GROUP BY category
-- Order the results
 ORDER BY completion_time DESC;
```

| category                           | completion_time          |
|------------------------------------|--------------------------|
| Rodents- Rats                      | 64 days, 10:58:23.000766 |
| Fire Prevention - Public Education | 34 days, 16:48:10        |
| Key Request - All  City Employees  | 32 days, 0:52:11         |


**</> Date parts**

In this exercise, you'll use date_part() to gain insights about when Evanston 311 requests are submitted and completed.

1. - How many requests are created in each of the 12 months during 2016-2017?

```sql
-- Extract the month from date_created and count requests
SELECT date_part('month', date_created) AS month, 
       COUNT(*)
  FROM evanston311
 -- Limit the date range
 WHERE date_created >='2016-01-01'
   AND date_created < '2018-01-01'
 -- Group by what to get monthly counts?
 GROUP BY month;
```

| month | count |
|-------|-------|
| 6     | 3404  |
| 8     | 3109  |
| 4     | 2385  |
| 3     | 2171  |
| 5     | 2674  |
| 10    | 2398  |
| 11    | 2283  |
| 9     | 2760  |
| 12    | 2000  |
| 1     | 1811  |
| 2     | 1774  |
| 7     | 3063  |

2. - What is the most common hour of the day for requests to be created?

```sql
-- Get the hour and count requests
SELECT date_part('hour', date_created) AS hour,
       count(*)
  FROM evanston311
 GROUP BY hour
 -- Order results to select most common
 ORDER BY count DESC
 LIMIT 1;
```

| hour | count |
|------|-------|
| 9    | 4089  |

3. - During what hours are requests usually completed? Count requests completed by hour.
   - Order the results by hour.

```sql
-- Count requests completed by hour
SELECT date_part('hour', date_completed) AS hour,
       count(*)
  FROM evanston311
 GROUP BY hour
 ORDER by hour;
```

| hour | count |
|------|-------|
| 0    | 13    |
| 1    | 10    |
| 2    | 11    |
| 3    | 19    |
| 4    | 9     |
| 5    | 50    |
| 6    | 1296  |
| 7    | 1870  |
| 8    | 2516  |
| 9    | 2744  |
| 10   | 3162  |
| 11   | 3351  |
| 12   | 3580  |
| 13   | 4787  |
| 14   | 5059  |
| 15   | 5242  |
| 16   | 1605  |
| 17   | 428   |
| 18   | 204   |
| 19   | 131   |
| 20   | 101   |
| 21   | 124   |
| 22   | 69    |
| 23   | 50    |


**</> Variation by day of week**

Does the time required to complete a request vary by the day of the week on which the request was created?

We can get the name of the day of the week by converting a timestamp to character data:

to_char(date_created, 'day') 
But character names for the days of the week sort in alphabetical, not chronological, order. To get the chronological order of days of the week with an integer value for each day, we can use:

EXTRACT(DOW FROM date_created)
DOW stands for "day of week."

- Select the name of the day of the week the request was created (date_created) as day.
- Select the mean time between the request completion (date_completed) and request creation as duration.
- Group by day (the name of the day of the week) and the integer value for the day of the week (use a function).
- Order by the integer value of the day of the week using the same function used in GROUP BY.

```sql
-- Select name of the day of the week the request was created 
SELECT to_char(date_created, 'day') AS day, 
       -- Select avg time between request creation and completion
       AVG(date_completed - date_created) AS duration
  FROM evanston311 
 -- Group by the name of the day of the week and 
 -- integer value of day of week the request was created
 GROUP BY day, EXTRACT(DOW FROM date_created)
 -- Order by integer value of the day of the week 
 -- the request was created
 ORDER BY EXTRACT(DOW FROM date_created);
```

| day       | duration                |
|-----------|-------------------------|
| sunday    | 9 days, 1:47:22.572982  |
| monday    | 7 days, 0:56:40.041519  |
| tuesday   | 7 days, 2:56:21.726767  |
| wednesday | 7 days, 12:07:08.185632 |
| thursday  | 7 days, 10:23:30.633975 |
| friday    | 8 days, 10:44:09.025246 |
| saturday  | 7 days, 14:37:00.356259 |

Note: Requests created at the beginning of the work week are closed sooner on average than those created at the end of the week or on the weekend.

**</> Date truncation**

Unlike date_part() or EXTRACT(), date_trunc() keeps date/time units larger than the field you specify as part of the date. So instead of just extracting one component of a timestamp, date_trunc() returns the specified unit and all larger ones as well.

Recall the syntax:

date_trunc('field', timestamp)
Using date_trunc(), find the average number of Evanston 311 requests created per day for each month of the data. Ignore days with no requests when taking the average.

- Write a subquery to count the number of requests created per day.
- Select the month and average count per month from the daily_count subquery.

```sql
-- Aggregate daily counts by month
SELECT date_trunc('month', day) AS month,
       avg(count)
  -- Subquery to compute daily counts
  FROM (SELECT date_trunc('day', date_created) AS day,
               count(*) AS count
          FROM evanston311
         GROUP BY day) AS daily_count
 GROUP BY month
 ORDER BY month;
```

| month                     | avg                 |
|---------------------------|---------------------|
| 2016-01-01 00:00:00+00:00 | 23.5161290322580645 |
| 2016-02-01 00:00:00+00:00 | 30.7241379310344828 |
| 2016-03-01 00:00:00+00:00 | 35.5483870967741935 |
| ...                       | ...                 |
| 2018-04-01 00:00:00+00:00 | 35.1333333333333333 |
| 2018-05-01 00:00:00+00:00 | 45.3548387096774194 |
| 2018-06-01 00:00:00+00:00 | 44.4666666666666667 |


**</> Find missing dates**

The generate_series() function can be useful for identifying missing dates.

Recall:

generate_series(from, to, interval)
where from and to are dates or timestamps, and interval can be specified as a string with a number and a unit of time, such as '1 month'.

Are there any days in the Evanston 311 data where no requests were created?

1. Write a subquery using generate_series() to get all dates between the min() and max() date_created in evanston311.
2. Write another subquery to select all values of date_created as dates from evanston311.
3. Both subqueries should produce values of type date (look for the ::).
4. Select dates (day) from the first subquery that are NOT IN the results of the second subquery. This gives you days that are not in date_created.

```sql
SELECT day
-- 1) Subquery to generate all dates
-- from min to max date_created
  FROM (SELECT generate_series(min(date_created),
                               max(date_created),
                               '1 day')::date AS day
          -- What table is date_created in?
          FROM evanston311) AS all_dates      
-- 4) Select dates (day from above) that are NOT IN the subquery
 WHERE day NOT IN 
       -- 2) Subquery to select all date_created values as dates
       (SELECT date_created::date
          FROM evanston311);
```

| day        |
|------------|
| 2016-05-08 |
| 2016-11-06 |
| 2017-02-05 |
| 2017-03-12 |
| 2017-12-25 |
| 2018-01-06 |
| 2018-01-14 |


**</> Custom aggregation periods**

Find the median number of Evanston 311 requests per day in each six month period from 2016-01-01 to 2018-06-30. Build the query following the three steps below.

Recall that to aggregate data by non-standard date/time intervals, such as six months, you can use generate_series() to create bins with lower and upper bounds of time, and then summarize observations that fall in each bin.

- Use generate_series() to create bins of 6 month intervals. Recall that the upper bin values are exclusive, so the values need to be one day greater than the last day to be included in the bin.

	- Notice how in the sample code, the first bin value of the upper bound is July 1st, and not June 30th.
	- Use the same approach when creating the last bin values of the lower and upper bounds (i.e. for 2018).

```sql
-- Generate 6 month bins covering 2016-01-01 to 2018-06-30

-- Create lower bounds of bins
SELECT generate_series('2016-01-01',  -- First bin lower value
                       '2018-01-30',  -- Last bin lower value
                       '6 months'::interval) AS lower,
-- Create upper bounds of bins
       generate_series('2016-07-01',  -- First bin upper value
                       '2018-07-01',  -- Last bin upper value
                       '6 months'::interval) AS upper;
```

| lower                     | upper                     |
|---------------------------|---------------------------|
| 2016-01-01 00:00:00+00:00 | 2016-07-01 00:00:00+00:00 |
| 2016-07-01 00:00:00+00:00 | 2017-01-01 00:00:00+00:00 |
| 2017-01-01 00:00:00+00:00 | 2017-07-01 00:00:00+00:00 |
| 2017-07-01 00:00:00+00:00 | 2018-01-01 00:00:00+00:00 |
| 2018-01-01 00:00:00+00:00 | 2018-07-01 00:00:00+00:00 |

- Count the number of requests created per day. Remember to not count *, or you will risk counting NULL values.

- Include days with no requests by joining evanston311 to a daily series from 2016-01-01 to 2018-06-30.

	- Note that because we are not generating bins, you can use June 30th as your series end date.

```sql
-- Count number of requests made per day
SELECT day, COUNT(date_created) AS count
-- Use a daily series from 2016-01-01 to 2018-06-30 
-- to include days with no requests
  FROM (SELECT generate_series('2016-01-01',  -- series start date
                               '2018-06-30',  -- series end date
                               '1 day'::interval)::date AS day) AS daily_series
       LEFT JOIN evanston311
       -- match day from above (which is a date) to date_created
       ON day = date_created::date
 GROUP BY day;
```

| day        | count |
|------------|-------|
| 2016-01-01 | 5     |
| 2016-01-02 | 27    |
| 2016-01-03 | 8     |
| ...        | ...   |


- Assign each daily count to a single 6 month bin by joining bins to daily_counts.
- Compute the median value per bin using percentile_disc().

```sql
-- Bins from Step 1
WITH bins AS (
	 SELECT generate_series('2016-01-01',
                            '2018-01-01',
                            '6 months'::interval) AS lower,
            generate_series('2016-07-01',
                            '2018-07-01',
                            '6 months'::interval) AS upper),
-- Daily counts from Step 2
     daily_counts AS (
     SELECT day, count(date_created) AS count
       FROM (SELECT generate_series('2016-01-01',
                                    '2018-06-30',
                                    '1 day'::interval)::date AS day) AS daily_series
            LEFT JOIN evanston311
            ON day = date_created::date
      GROUP BY day)
-- Select bin bounds
SELECT lower, 
       upper, 
       -- Compute median of count for each bin
       percentile_disc(0.5) WITHIN GROUP (ORDER BY count) AS median
  -- Join bins and daily_counts
  FROM bins
       LEFT JOIN daily_counts
       -- Where the day is between the bin bounds
       ON day >= lower
          AND day < upper
 -- Group by bin bounds
 GROUP BY lower, upper
 ORDER BY lower;
```

| lower                     | upper                     | median |
|---------------------------|---------------------------|--------|
| 2016-01-01 00:00:00+00:00 | 2016-07-01 00:00:00+00:00 | 37     |
| 2016-07-01 00:00:00+00:00 | 2017-01-01 00:00:00+00:00 | 41     |
| 2017-01-01 00:00:00+00:00 | 2017-07-01 00:00:00+00:00 | 44     |
| 2017-07-01 00:00:00+00:00 | 2018-01-01 00:00:00+00:00 | 51     |
| 2018-01-01 00:00:00+00:00 | 2018-07-01 00:00:00+00:00 | 41     |


**</> Monthly average with missing dates**

Find the average number of Evanston 311 requests created per day for each month of the data.

This time, do not ignore dates with no requests.

- Generate a series of dates from 2016-01-01 to 2018-06-30.
- Join the series to a subquery to count the number of requests created per day.
- Use date_trunc() to get months from date, which has all dates, NOT day.
- Use coalesce() to replace NULL count values with 0. Compute the average of this value.

```sql
-- generate series with all days from 2016-01-01 to 2018-06-30
WITH all_days AS 
     (SELECT generate_series('2016-01-01',
                             '2018-06-30',
                             '1 day'::interval) AS date),
     -- Subquery to compute daily counts
     daily_count AS 
     (SELECT date_trunc('day', date_created) AS day,
             count(*) AS count
        FROM evanston311
       GROUP BY day)
-- Aggregate daily counts by month using date_trunc
SELECT date_trunc('month', date) AS month,
       -- Use coalesce to replace NULL count values with 0
       avg(coalesce(count, 0)) AS average
  FROM all_days
       LEFT JOIN daily_count
       -- Joining condition
       ON all_days.date=daily_count.day
 GROUP BY month
 ORDER BY month; 
```

| month                     | average             |
|---------------------------|---------------------|
| 2016-01-01 00:00:00+00:00 | 23.5161290322580645 |
| 2016-02-01 00:00:00+00:00 | 30.7241379310344828 |
| 2016-03-01 00:00:00+00:00 | 35.5483870967741935 |
| 2016-04-01 00:00:00+00:00 | 37.3000000000000000 |
| 2016-05-01 00:00:00+00:00 | 39.4516129032258065 |
| 2016-06-01 00:00:00+00:00 | 44.0000000000000000 |
| 2016-07-01 00:00:00+00:00 | 41.4838709677419355 |
| 2016-08-01 00:00:00+00:00 | 46.5483870967741935 |
| 2016-09-01 00:00:00+00:00 | 47.3333333333333333 |
| 2016-10-01 00:00:00+00:00 | 35.8064516129032258 |
| 2016-11-01 00:00:00+00:00 | 35.4000000000000000 |
| 2016-12-01 00:00:00+00:00 | 29.3870967741935484 |
| 2017-01-01 00:00:00+00:00 | 34.9032258064516129 |
| 2017-02-01 00:00:00+00:00 | 31.5357142857142857 |
| 2017-03-01 00:00:00+00:00 | 34.4838709677419355 |
| 2017-04-01 00:00:00+00:00 | 42.2000000000000000 |
| 2017-05-01 00:00:00+00:00 | 46.8064516129032258 |
| 2017-06-01 00:00:00+00:00 | 69.4666666666666667 |
| 2017-07-01 00:00:00+00:00 | 57.3225806451612903 |
| 2017-08-01 00:00:00+00:00 | 53.7419354838709677 |
| 2017-09-01 00:00:00+00:00 | 44.6666666666666667 |
| 2017-10-01 00:00:00+00:00 | 41.5483870967741935 |
| 2017-11-01 00:00:00+00:00 | 40.7000000000000000 |
| 2017-12-01 00:00:00+00:00 | 35.1290322580645161 |
| 2018-01-01 00:00:00+00:00 | 33.1935483870967742 |
| 2018-02-01 00:00:00+00:00 | 30.5714285714285714 |
| 2018-03-01 00:00:00+00:00 | 29.6774193548387097 |
| 2018-04-01 00:00:00+00:00 | 35.1333333333333333 |
| 2018-05-01 00:00:00+00:00 | 45.3548387096774194 |
| 2018-06-01 00:00:00+00:00 | 44.4666666666666667 |

**</> Longest gap**

What is the longest time between Evanston 311 requests being submitted?

Recall the syntax for lead() and lag():

```sql
lag(column_to_adjust) OVER (ORDER BY ordering_column)
lead(column_to_adjust) OVER (ORDER BY ordering_column)
```

- Select date_created and the date_created of the previous request using lead() or lag() as appropriate.
- Compute the gap between each request and the previous request.
- Select the row with the maximum gap.

```sql
-- Compute the gaps
WITH request_gaps AS (
        SELECT date_created,
               -- lead or lag
               lag(date_created) OVER (ORDER BY date_created) AS previous,
               -- compute gap as date_created minus lead or lag
               date_created - lag(date_created) OVER (ORDER BY date_created) AS gap
          FROM evanston311)
-- Select the row with the maximum gap
SELECT *
  FROM request_gaps
-- Subquery to select maximum gap from request_gaps
 WHERE gap = (SELECT max(gap)
                FROM request_gaps);
```

| date_created              | previous                  | gap             |
|---------------------------|---------------------------|-----------------|
| 2018-01-07 18:41:34+00:00 | 2018-01-05 18:04:09+00:00 | 2 days, 0:37:25 |


**</> Rats!**

Requests in category "Rodents- Rats" average over 64 days to resolve. Why?

Investigate in 4 steps:

1. Why is the average so high? Check the distribution of completion times. Hint: date_trunc() can be used on intervals.

2. See how excluding outliers influences average completion times.

3. Do requests made in busy months take longer to complete? Check the correlation between the average completion time and requests per month.

4. Compare the number of requests created per month to the number completed.

Remember: the time to resolve, or completion time, is date_completed - date_created.


1. - Use date_trunc() to examine the distribution of rat request completion times by number of days.

```sql
-- Truncate the time to complete requests to the day
SELECT date_trunc('day', date_completed - date_created) AS completion_time,
-- Count requests with each truncated time
       count(*)
  FROM evanston311
-- Where category is rats
 WHERE category = 'Rodents- Rats'
-- Group and order by the variable of interest
 GROUP BY completion_time
 ORDER BY completion_time;
```

| completion_time | count |
|-----------------|-------|
| 0:00:00         | 73    |
| 1 day, 0:00:00  | 17    |
| 2 days, 0:00:00 | 23    |
| 3 days, 0:00:00 | 11    |
| ...             | ...   |

2. - Compute average completion time per category excluding the longest 5% of requests (outliers).

```sql
SELECT category, 
       -- Compute average completion time per category
       avg(date_completed-date_created) AS avg_completion_time
  FROM evanston311
-- Where completion time is less than the 95th percentile value
 WHERE (date_completed-date_created) <
-- Compute the 95th percentile of completion time in a subquery
         (SELECT percentile_disc(0.95) WITHIN GROUP (ORDER BY date_completed-date_created)
            FROM evanston311)
 GROUP BY category
-- Order the results
 ORDER BY avg_completion_time DESC;
```

| category                                              | avg_completion_time      |
|-------------------------------------------------------|--------------------------|
| Trash Cart - Downsize, Upsize or Remove               | 12 days, 17:47:50.586912 |
| Sanitation Billing Questions                          | 12 days, 11:13:25.888889 |
| THIS REQUEST IS INACTIVE...Trash Cart - Compost Bin   | 12 days, 6:32:42.024390  |
| Trash, Recycling, Yard Waste Cart- Repair/Replacement | 11 days, 18:48:27.488108 |
| Rodents- Rats                                         | 11 days, 8:58:00.840849  |
| ...                                                   | ...                      |

3. - Get corr() between avg. completion time and monthly requests. EXTRACT(epoch FROM interval) returns seconds in interval.

```sql
-- Compute correlation (corr) between 
-- avg_completion time and count from the subquery
SELECT corr(avg_completion, count)
  -- Convert date_created to its month with date_trunc
  FROM (SELECT date_trunc('month', date_created) AS month, 
               -- Compute average completion time in number of seconds           
               avg(EXTRACT(epoch FROM date_completed - date_created)) AS avg_completion, 
               -- Count requests per month
               count(*) AS count
          FROM evanston311
         -- Limit to rodents
         WHERE category='Rodents- Rats' 
         -- Group by month, created above
         GROUP BY month) 
         -- Required alias for subquery 
         AS monthly_avgs;
```

| corr              |
|-------------------|
| 0.234379930623994 |

4. - Select the number of requests created and number of requests completed per month.

```sql
-- Compute monthly counts of requests created
WITH created AS (
       SELECT date_trunc('month', date_created) AS month,
              count(*) AS created_count
         FROM evanston311
        WHERE category='Rodents- Rats'
        GROUP BY month),
-- Compute monthly counts of requests completed
      completed AS (
       SELECT date_trunc('month', date_completed) AS month,
              count(*) AS completed_count
         FROM evanston311
        WHERE category='Rodents- Rats'
        GROUP BY month)
-- Join monthly created and completed counts
SELECT created.month, 
       created_count, 
       completed_count
  FROM created
       INNER JOIN completed
       ON created.month=completed.month
 ORDER BY created.month;
```

| month                     | created_count | completed_count |
|---------------------------|---------------|-----------------|
| 2016-01-01 00:00:00+00:00 | 11            | 1               |
| 2016-02-01 00:00:00+00:00 | 21            | 11              |
| 2016-03-01 00:00:00+00:00 | 31            | 14              |
| 2016-04-01 00:00:00+00:00 | 36            | 16              |
| 2016-05-01 00:00:00+00:00 | 40            | 19              |
| 2016-06-01 00:00:00+00:00 | 41            | 49              |
| 2016-07-01 00:00:00+00:00 | 80            | 47              |
| 2016-08-01 00:00:00+00:00 | 79            | 43              |
| 2016-09-01 00:00:00+00:00 | 56            | 58              |
| 2016-10-01 00:00:00+00:00 | 76            | 67              |
| 2016-11-01 00:00:00+00:00 | 63            | 61              |
| 2016-12-01 00:00:00+00:00 | 20            | 83              |
| 2017-01-01 00:00:00+00:00 | 16            | 34              |
| 2017-02-01 00:00:00+00:00 | 20            | 30              |
| 2017-03-01 00:00:00+00:00 | 24            | 32              |
| 2017-04-01 00:00:00+00:00 | 43            | 14              |
| 2017-05-01 00:00:00+00:00 | 50            | 17              |
| 2017-06-01 00:00:00+00:00 | 65            | 34              |
| 2017-07-01 00:00:00+00:00 | 68            | 46              |
| 2017-08-01 00:00:00+00:00 | 87            | 53              |
| 2017-09-01 00:00:00+00:00 | 54            | 70              |
| 2017-10-01 00:00:00+00:00 | 78            | 12              |
| 2017-11-01 00:00:00+00:00 | 44            | 174             |
| 2017-12-01 00:00:00+00:00 | 12            | 65              |
| 2018-01-01 00:00:00+00:00 | 19            | 45              |
| 2018-02-01 00:00:00+00:00 | 10            | 10              |
| 2018-03-01 00:00:00+00:00 | 28            | 27              |
| 2018-04-01 00:00:00+00:00 | 35            | 23              |
| 2018-05-01 00:00:00+00:00 | 46            | 47              |
| 2018-06-01 00:00:00+00:00 | 52            | 55              |

There is a slight correlation between completion times and the number of requests per month. But the bigger issue is the disproportionately large number of requests completed in November 2017.
