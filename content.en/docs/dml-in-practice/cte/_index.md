---
title: 'Common table expression'
weight: 2
references:
    links:
        - https://stackoverflow.com/questions/2944297/postgresql-function-for-last-inserted-id
--- 

> A Common Table Expression (CTE) allows you to create a temporary result set within a query. It enhances the readability of complex queries by breaking them down into smaller and more reusable parts.

## Advantages

1. **Improved Readability**: CTEs make complex queries more organized and easier to understand.
2. **Reusable Query Parts**: CTEs allow you to define reusable query parts, simplifying the query structure.
3. **Recursive Queries**: CTEs support recursive queries, useful for hierarchical data.
4. **Combination with Window Functions**: CTEs can be used with window functions for advanced data analysis.

## Explanation

### Basic Syntax

Here’s the basic syntax for creating a common table expression:

```sql
WITH cte_name (column1, column2, ...) AS (
    -- CTE query
    SELECT ...
)
-- Main query using the CTE
SELECT ...
FROM cte_name;
```

In this syntax:

- **WITH clause**: Introduces the CTE. It is followed by the name of the CTE and a list of column names in parentheses. The column list is optional and is only necessary if you want to explicitly define the columns for the CTE.
- **CTE name**: Specifies the name of the CTE. The CTE name exists within the scope of the query.
- **Column List (optional)**: Specifies the list of column names within parentheses after the CTE name. If not specified, the columns implicitly inherit the column names from the SELECT statement inside the CTE.
- **AS keyword**: Indicates the beginning of the CTE definition.
- **CTE query**: The query that defines the CTE, which may include JOINs, WHERE, GROUP BY clauses, and other valid SQL constructs.
- **Main query**: After defining the CTE, you can reference it in the main query by its name. In the main query, you can use the CTE as if it were a regular table, simplifying the structure of complex queries.

## PostgreSQL CTE Examples

### Basic Usecase

Lets revisit how we add localized movie where we need generated movie id to be passed to localized table.

```sql
INSERT INTO movies(title, year_of_release)
	VALUES ('Avatar', 2009);
	
INSERT INTO movies_localized(movie_id, locale, title) 
	VALUES (currval('movies_id_seq'), 'ta', 'அவதார்');
```

We have used `currval` function. which can also be done with `lastval()` but they can cause side effects with triggers. `INSERT with RETURNING` will be safe option lets try

```sql
INSERT INTO movies_localized(movie_id, locale, title) 
	VALUES (
    INSERT INTO movies(title, year_of_release) VALUES ('Avatar', 2009) RETURNING id
    , 'ta', 'அவதார்');
```

But wait. POSTGRES does not like it. It throws an error ?! This is where CTE can help us as given below

```sql
-- Step 1: Insert into the movies table and get the id
WITH movie_insert AS (
    INSERT INTO movies(title, year_of_release)
    VALUES ('Avatar', 2009)
    RETURNING id
)

-- Step 2: Use the returned id to insert into the movies_localized table
INSERT INTO movies_localized(movie_id, locale, title)
SELECT id, 'ta', 'அவதார்'
FROM movie_insert;
```


### Join a CTE with a Table Example

The following example joins a CTE with a table to find the staff and rental count for each:

```sql
WITH cte_rental AS (
  SELECT 
    staff_id, 
    COUNT(rental_id) rental_count 
  FROM 
    rental 
  GROUP BY 
    staff_id
) 
SELECT 
  s.staff_id, 
  first_name, 
  last_name, 
  rental_count 
FROM 
  staff s 
  INNER JOIN cte_rental USING (staff_id);
```

Output:

| staff_id | first_name | last_name | rental_count |
|----------|------------|-----------|--------------|
|        1 | Mike       | Hillyer   |         8040 |
|        2 | Jon        | Stephens  |         8004 |
| (2 rows) |

In this example:

- The CTE returns a result set that includes the staff ID and the rental counts.
- The main query joins the `staff` table with the CTE using the `staff_id` column.

### Multiple CTEs Example

The following example uses multiple CTEs to calculate various statistics related to films and customers:

```sql
WITH film_stats AS (
    -- CTE 1: Calculate film statistics
    SELECT
        AVG(rental_rate) AS avg_rental_rate,
        MAX(length) AS max_length,
        MIN(length) AS min_length
    FROM film
),
customer_stats AS (
    -- CTE 2: Calculate customer statistics
    SELECT
        COUNT(DISTINCT customer_id) AS total_customers,
        SUM(amount) AS total_payments
    FROM payment
)
-- Main query using the CTEs
SELECT
    ROUND((SELECT avg_rental_rate FROM film_stats), 2) AS avg_film_rental_rate,
    (SELECT max_length FROM film_stats) AS max_film_length,
    (SELECT min_length FROM film_stats) AS min_film_length,
    (SELECT total_customers FROM customer_stats) AS total_customers,
    (SELECT total_payments FROM customer_stats) AS total_payments;
```

Output:

| avg_film_rental_rate | max_film_length | min_film_length | total_customers | total_payments |
|----------------------|-----------------|-----------------|-----------------|----------------|
|                 2.98 |             185 |              46 |             599 |       61312.04 |
| (1 row) |

In this example, we create two CTEs:

- **film_stats**: Calculates statistics related to films, including the average rental rate, maximum length, and minimum length.
- **customer_stats**: Calculates statistics related to customers, including the total number of distinct customers and the overall payments made.

The main query retrieves specific values from each CTE to create a summary report.

## Summary

- **Definition**: A Common Table Expression (CTE) is a temporary result set within a query.
- **Advantages**: CTEs improve readability, allow for reusable query parts, support recursive queries, and can be used with window functions.
- **Explanation**: CTEs are defined using the WITH clause, followed by the CTE name, optional column list, AS keyword, CTE query, and the main query that references the CTE.
- **Examples**: The examples provided demonstrate how to use CTEs to simplify complex queries, join CTEs with tables, and use multiple CTEs for advanced data analysis.

## Summary

A Common Table Expression (CTE) is a temporary result set within a query that improves readability by breaking down complex queries into smaller, reusable parts. CTEs enhance query clarity, support recursive queries, and can be combined with window functions for advanced data analysis. Defined using the WITH clause, CTEs allow for organized and efficient querying, as demonstrated through various examples that simplify complex joins and calculations.