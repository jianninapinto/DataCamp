# 1 What's in the database?

</> Count missing values

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

</> Join tables

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

</> Foreign keys

Using what you know about foreign keys, why can't the tag column in the tag_type table be a foreign key that references the tag column in the stackoverflow table?

Possible Answers

a. stackoverflow.tag is not a primary key

b. tag_type.tag contains NULL values

`c. stackoverflow.tag contains duplicate values`

d. tag_type.tag does not contain all the values in stackoverflow.tag

Why: Foreign keys must reference a column with unique values for each row so the referenced row can be identified.


</> Read an entity relationship diagram

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
