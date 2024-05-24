---
title: 'Index'
weight: 4
--- 

# Understanding Indexes in RDBMS

## Introduction

An Index is a data structure that improves the speed of data retrieval operations on a database table at the cost of additional space and slower inserts, updates, and deletes. It allows quick access to specific rows in a table without scanning the entire table.

## Index

An Index is similar to an index in a book—it provides a quick way to look up information. In a database, an Index is created on one or more columns of a table. When you create an Index, the database creates an internal data structure (such as a B-tree or hash table) to organize the values of the indexed columns efficiently.

## Types of Indexes

### Primary Index

A Primary Index is created automatically when you define a Primary Key constraint on a table. It ensures that the values in the indexed column(s) are unique and is used to enforce the uniqueness of rows in the table. Primary Key columns are indexed automatically.

### Secondary Index

A Secondary Index is created explicitly by the user to improve the performance of queries that do not use the Primary Key. It allows fast access to rows based on the indexed column(s), but unlike a Primary Index, it does not enforce uniqueness.

## Example: Creating an Index on the State Code Column

Let's create a Secondary Index on the `state_code` column of the `post_office` table to improve the performance of queries that filter data by state.

```sql
CREATE INDEX idx_state_code ON post_office (state_code);
```

In this example:
- `idx_state_code` is the name of the Index.
- `state_code` is the column on which the Index is created.
- The Index will improve the speed of queries that filter data based on the `state_code` column.

## Advantages of Indexes

1. **Faster Data Retrieval**: Indexes allow the database to locate specific rows quickly, especially when querying large datasets.
2. **Improved Query Performance**: Queries that filter, join, or sort data based on indexed columns execute more efficiently.
3. **Enforcement of Constraints**: Indexes can enforce uniqueness and referential integrity constraints, ensuring data integrity.

## Summary

Indexes play a crucial role in optimizing database performance by providing fast access to data. By creating indexes on appropriate columns, you can improve the speed of data retrieval operations and enhance the overall performance of your database system. However, it's essential to strike a balance between the benefits of indexes and their impact on data modification operations.