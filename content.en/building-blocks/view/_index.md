---
title: 'View'
weight: 5
--- 

> A View is a virtual table in an RDBMS that is based on the result set of a SQL query. Views simplify complex queries, enhance security by restricting access to data, and provide a way to present data in a specific format without altering the underlying tables.

There are two main types of views:

1. **Normal Views** (or simply Views)
2. **Materialized Views**

## Normal Views

A Normal View is a stored query that can be treated as a table. It does not store data physically; instead, it retrieves data from the underlying tables each time it is queried.

### Creating a Normal View

Let's create a view that shows the details of post offices along with their state names.

First, we have two tables: `post_office` and `state`.

```sql
CREATE TABLE post_office (
    post_office_id INT PRIMARY KEY AUTO_INCREMENT,
    area_name TEXT,
    city_name TEXT,
    pin_code INT,
    phone_number BIGINT,
    state_code CHAR(2)
);

CREATE TABLE state (
    state_code CHAR(2) PRIMARY KEY,
    state_name VARCHAR(100)
);
```

We insert some sample data into these tables:

```sql
INSERT INTO state (state_code, state_name) VALUES ('TN', 'Tamil Nadu'), ('KA', 'Karnataka');

INSERT INTO post_office (area_name, city_name, pin_code, phone_number, state_code) VALUES
('Pethaniapuram 2, Gnana Olivupuram', 'Madurai', 625016, 9900000001, 'TN'),
('Loyola College Campus, Nungambakkam', 'Chennai', 600034, 9900000002, 'TN'),
('MG Road', 'Bangalore', 560001, 9900000003, 'KA');
```

Now, we create a view to display post office details along with the state name:

```sql
CREATE VIEW post_office_view AS
SELECT po.post_office_id, po.area_name, po.city_name, po.pin_code, po.phone_number, s.state_name
FROM post_office po
JOIN state s ON po.state_code = s.state_code;
```

To retrieve data from the view, we use:

```sql
SELECT * FROM post_office_view;
```

This will output:

| post_office_id | area_name                          | city_name    | pin_code | phone_number | state_name  |
|----------------|------------------------------------|--------------|----------|--------------|-------------|
| 1              | Pethaniapuram 2, Gnana Olivupuram  | Madurai      | 625016   | 9900000001   | Tamil Nadu  |
| 2              | Loyola College Campus, Nungambakkam| Chennai      | 600034   | 9900000002   | Tamil Nadu  |
| 3              | MG Road                            | Bangalore    | 560001   | 9900000003   | Karnataka   |

### Advantages of Normal Views

1. **Simplified Queries**: Views can encapsulate complex queries and present simplified results to the user.
2. **Security**: Views can restrict access to specific columns and rows of the underlying tables.
3. **Data Abstraction**: Views can provide a different representation of the data without modifying the underlying tables.

## Materialized Views

A Materialized View stores the result set of a query physically. Unlike normal views, materialized views do not retrieve data from the underlying tables each time they are queried. Instead, they store the query results and can be refreshed periodically to reflect changes in the underlying tables.

### Creating a Materialized View

Let's create a materialized view that stores the total number of post offices in each state.

```sql
CREATE MATERIALIZED VIEW state_post_office_count AS
SELECT state_code, COUNT(*) AS post_office_count
FROM post_office
GROUP BY state_code;
```

To retrieve data from the materialized view, we use:

```sql
SELECT * FROM state_post_office_count;
```

This will output:

| state_code | post_office_count |
|------------|-------------------|
| TN         | 2                 |
| KA         | 1                 |

### Advantages of Materialized Views

1. **Improved Performance**: Since the results are stored physically, querying a materialized view is faster than querying the underlying tables.
2. **Data Aggregation**: Materialized views are useful for storing aggregated data that does not need to be recalculated frequently.
3. **Decoupled Query Processing**: Complex calculations can be offloaded to materialized views, reducing the load on the base tables.

## Summary

Views and Materialized Views are powerful tools in an RDBMS for presenting data in different formats, enhancing query performance, and improving data security. Normal Views provide a dynamic, real-time representation of data, while Materialized Views store precomputed results for faster access. By leveraging these tools, you can simplify your database queries, improve performance, and maintain data abstraction and security.