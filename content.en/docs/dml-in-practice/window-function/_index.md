---
title: 'Window Functions'
weight: 3
--- 

**Window Functions** in SQL are a class of functions that perform calculations across a set of table rows that are related to the current row. Unlike aggregate functions that summarize data into a single result (thus reducing the number of rows), window functions allow you to retain the original rows while performing the calculations. These functions operate over a "window" or subset of rows defined by the `OVER` clause, which can include elements like partitioning and ordering.

### Key Characteristics:

1. **Retention of Rows**: Window functions do not reduce the number of rows returned by a query. Each row retains its individuality while the function computes its result based on a defined set of rows.

2. **Partitioning**: You can divide the result set into partitions using the `PARTITION BY` clause. The window function is then applied to each partition independently.

3. **Ordering**: The `ORDER BY` clause within the `OVER` clause specifies the logical order of rows within each partition.

4. **Frames**: You can define a frame, a subset of rows within a partition, over which the window function operates. This is done using the `RANGE` or `ROWS` clause.

### Examples of Window Functions:

- **Aggregate Functions**: Used as window functions, such as `AVG()`, `SUM()`, `COUNT()`, `MIN()`, `MAX()`.
- **Ranking Functions**: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE()`.
- **Value Functions**: `FIRST_VALUE()`, `LAST_VALUE()`, `LAG()`, `LEAD()`.


```sql
window_function(arg1, arg2, ...) OVER (
   [PARTITION BY partition_expression]
   [ORDER BY sort_expression [ASC | DESC] [NULLS {FIRST | LAST }])
```

Window functions are widely used in data analysis tasks where you need to compute cumulative sums, moving averages, rankings, and differences between current and previous or next rows, all while maintaining the full detail of the dataset.

### Setting up Sample Tables

First, create two tables named `product_groups` and `products` for the demonstration:

```sql
CREATE TABLE product_groups (
    group_id serial PRIMARY KEY,
    group_name VARCHAR (255) NOT NULL
);

CREATE TABLE products (
    product_id serial PRIMARY KEY,
    product_name VARCHAR (255) NOT NULL,
    price DECIMAL (11, 2),
    group_id INT NOT NULL,
    FOREIGN KEY (group_id) REFERENCES product_groups (group_id)
);
```

Second, insert some rows into these tables:

```sql
INSERT INTO product_groups (group_name)
VALUES
    ('Smartphone'),
    ('Laptop'),
    ('Tablet');

INSERT INTO products (product_name, group_id, price)
VALUES
    ('Microsoft Lumia', 1, 200),
    ('HTC One', 1, 400),
    ('Nexus', 1, 500),
    ('iPhone', 1, 900),
    ('HP Elite', 2, 1200),
    ('Lenovo Thinkpad', 2, 700),
    ('Sony VAIO', 2, 700),
    ('Dell Vostro', 2, 800),
    ('iPad', 3, 700),
    ('Kindle Fire', 3, 150),
    ('Samsung Galaxy Tab', 3, 200);
```

### Introduction to PostgreSQL Window Functions

The easiest way to understand window functions is to start by reviewing aggregate functions. An aggregate function aggregates data from a set of rows into a single row.

For example, the following query uses the `AVG()` aggregate function to calculate the average price of all products in the `products` table:

```sql
SELECT
    AVG(price)
FROM
    products;
```

To apply the aggregate function to subsets of rows, you use the `GROUP BY` clause. The following example returns the average price for every product group:

```sql
SELECT
    group_name,
    AVG(price)
FROM
    products
INNER JOIN product_groups USING (group_id)
GROUP BY
    group_name;
```

### Window Function Concept

Similar to an aggregate function, a window function operates on a set of rows. However, it does not reduce the number of rows returned by the query.

The term window describes the set of rows on which the window function operates. A window function returns values from the rows in a window.

For instance, the following query returns the product name, the price, product group name, along with the average prices of each product group:

```sql
SELECT
    product_name,
    price,
    group_name,
    AVG(price) OVER (
        PARTITION BY group_name
    )
FROM
    products
INNER JOIN product_groups USING (group_id);
```

### Window Function Syntax

PostgreSQL has a sophisticated syntax for window function calls. The following illustrates the simplified version:

```sql
window_function(arg1, arg2, ...) OVER (
    [PARTITION BY partition_expression]
    [ORDER BY sort_expression [ASC | DESC] [NULLS {FIRST | LAST }])
```

#### Key Components:

- **window_function(arg1,arg2,...)**: The name of the window function.
- **PARTITION BY clause**: Divides rows into multiple groups or partitions.
- **ORDER BY clause**: Specifies the order of rows in each partition.
- **frame_clause**: Defines a subset of rows in the current partition to which the window function is applied.

### Example Window Functions

#### ROW_NUMBER(), RANK(), and DENSE_RANK()

These functions assign an integer to each row based on its order in its result set.

- `ROW_NUMBER()`: Assigns a sequential number to each row in each partition.

```sql
SELECT
    product_name,
    group_name,
    price,
    ROW_NUMBER() OVER (
        PARTITION BY group_name
        ORDER BY price
    )
FROM
    products
INNER JOIN product_groups USING (group_id);
```

- `RANK()`: Assigns ranking within an ordered partition, with gaps for ties.

```sql
SELECT
    product_name,
    group_name,
    price,
    RANK() OVER (
        PARTITION BY group_name
        ORDER BY price
    )
FROM
    products
INNER JOIN product_groups USING (group_id);
```

- `DENSE_RANK()`: Assigns a rank to each row within an ordered partition, without gaps.

```sql
SELECT
    product_name,
    group_name,
    price,
    DENSE_RANK() OVER (
        PARTITION BY group_name
        ORDER BY price
    )
FROM
    products
INNER JOIN product_groups USING (group_id);
```

#### FIRST_VALUE and LAST_VALUE

- `FIRST_VALUE()`: Returns a value evaluated against the first row within its partition.

```sql
SELECT
    product_name,
    group_name,
    price,
    FIRST_VALUE(price) OVER (
        PARTITION BY group_name
        ORDER BY price
    ) AS lowest_price_per_group
FROM
    products
INNER JOIN product_groups USING (group_id);
```

- `LAST_VALUE()`: Returns a value evaluated against the last row in its partition.

```sql
SELECT
    product_name,
    group_name,
    price,
    LAST_VALUE(price) OVER (
        PARTITION BY group_name
        ORDER BY price RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS highest_price_per_group
FROM
    products
INNER JOIN product_groups USING (group_id);
```

#### LAG and LEAD

- `LAG()`: Accesses data from the previous row.

```sql
SELECT
    product_name,
    group_name,
    price,
    LAG(price, 1) OVER (
        PARTITION BY group_name
        ORDER BY price
    ) AS prev_price,
    price - LAG(price, 1) OVER (
        PARTITION BY group_name
        ORDER BY price
    ) AS cur_prev_diff
FROM
    products
INNER JOIN product_groups USING (group_id);
```

- `LEAD()`: Accesses data from the next row.

```sql
SELECT
    product_name,
    group_name,
    price,
    LEAD(price, 1) OVER (
        PARTITION BY group_name
        ORDER BY price
    ) AS next_price,
    price - LEAD(price, 1) OVER (
        PARTITION BY group_name
        ORDER BY price
    ) AS cur_next_diff
FROM
    products
INNER JOIN product_groups USING (group_id);
```

### Summary

Window functions in PostgreSQL provide a powerful way to perform calculations across sets of table rows related to the current row. They allow for detailed data analysis and can be used to assign ranks, calculate running totals, find differences between row values, and more. Unlike aggregate functions, window functions do not reduce the number of rows returned, making them ideal for complex data analysis tasks.