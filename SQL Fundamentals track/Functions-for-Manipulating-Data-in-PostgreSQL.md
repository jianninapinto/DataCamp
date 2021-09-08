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
