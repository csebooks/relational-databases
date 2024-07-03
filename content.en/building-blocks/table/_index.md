---
title: 'Table'
weight: 1
--- 

A table is a central part of an RDBMS (Relational Database Management System) where structured data is stored in a tabular format. Each table in an RDBMS has fixed columns (once defined) and can have an expanding number of rows. Let's consider an example where we store the post offices in India. Each post office consists of the following information:

1. **Area Name** - a text field.
2. **City Name** - a text field.
3. **PIN Code** - a number.
4. **Phone Number** - a number.

## Creating the Table

We will CREATE a TABLE named `post_office` with the above structure.

```sql
CREATE TABLE post_office (
    area_name TEXT,
    city_name TEXT,
    pin_code INT,
    phone_number BIGINT
);
```

## Inserting Data into the Table

We have two post offices, one in Madurai and another in Chennai. Their details are as follows:

1. Pethaniapuram 2, Gnana Olivupuram, Madurai, 625010, Ph: 99000 00001
2. Loyola College Campus, Nungambakkam, Chennai - 600034, Ph: 99000 00002

Let's INSERT these VALUES into the `post_office` table.

```sql
INSERT INTO post_office (area_name, city_name, pin_code, phone_number) VALUES
('Pethaniapuram 2, Gnana Olivupuram', 'Madurai', 625010, 9900000001),
('Loyola College Campus, Nungambakkam', 'Chennai', 600034, 9900000002);
```

## Selecting Data from the Table

To view the data we have just entered, we can use the SELECT statement.

```sql
SELECT * FROM post_office;
```

This query will return all rows from the `post_office` table.

## Updating Data in the Table

Suppose we made a mistake and the PIN code of the Madurai post office is not 625010 but 625016. We can UPDATE this information with.

```sql
UPDATE post_office
SET pin_code = 625016
WHERE area_name = 'Pethaniapuram 2, Gnana Olivupuram' AND city_name = 'Madurai';
```

## Deleting Data from the Table

If we want to DELETE the entry for the Chennai post office, we can use the DELETE statement.

```sql
DELETE FROM post_office
WHERE area_name = 'Loyola College Campus, Nungambakkam' AND city_name = 'Chennai';
```

## Summary

In this guide, we covered the basics of creating a table in an RDBMS, inserting data into the table, selecting data from the table, Here are the key SQL statements we used:

1. **CREATE TABLE**: Defines a new table and its columns.
2. **INSERT INTO**: Adds new rows of data to the table.
3. **SELECT**: Retrieves data from the table.
4. **UPDATE**: Modifies existing data in the table.
5. **DELETE**: Removes data from the table.

By understanding and practicing these SQL statements, you can efficiently manage and manipulate data in an RDBMS.
