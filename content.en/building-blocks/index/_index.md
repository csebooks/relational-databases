---
title: 'Index'
weight: 4
references:
    links:
        - https://www.enterprisedb.com/postgres-tutorials/overview-postgresql-indexes
--- 

> An Index is a data structure that improves the speed of data retrieval operations on a database table at the cost of additional space and slower inserts, updates, and deletes. It allows quick access to specific rows in a table without scanning the entire table.

An Index is similar to an index in a book—it provides a quick way to look up information. In a database, an Index is created on one or more columns of a table. When you create an Index, the database creates an internal data structure (such as a B-tree or hash table) to organize the values of the indexed columns efficiently.

## Primary Index

A Primary Index is created automatically when you define a Primary Key constraint on a table. It ensures that the values in the indexed column(s) are unique and is used to enforce the uniqueness of rows in the table. Primary Key columns are indexed automatically.

## Secondary Index

A Secondary Index is created explicitly by the user to improve the performance of queries that do not use the Primary Key. It allows fast access to rows based on the indexed column(s), but unlike a Primary Index, it does not enforce uniqueness.

## Types of Indexes

In order to undestand index lets consider we have below table

```sql
CREATE TABLE SENSOR_DATA {
    A SAMPLE FOR PK INDEX,
    TYPE OF SENSOR HASH INDEX,
    CREATE_AT TIMESTAMP,
    VALUE FLOAT
}
```

Lets create quality and quantity data

```sql
CREATE OR REPLACE FUNCTION random_between(low INT ,high INT) 
   RETURNS INT AS
$$
BEGIN
   RETURN floor(random()* (high-low + 1) + low);
END;
$$ language 'plpgsql' STRICT;


select * from  generate_series('2022-07-01 00:00'::timestamp,
	'2022-08-01 00:00',  '60 seconds');

insert into facilities.device_data(device_id, time, reading)  
select generate_series(1,44641), 
 generate_series('2022-07-01 00:00'::timestamp,
	'2022-08-01 00:00',  '60 seconds'),
 random_between (15,30);
```

### B-tree

The most common and widely used index type is the B-tree index. This is the default index type for the CREATE INDEX command, unless you explicitly mention the type during index creation. 

If the indexed column is used to perform the comparison by using comparison operators such as `<, <=, =, >=, and >`, then the  Postgres optimizer uses the index created by the B-tree option for the specified column.

```sql
EXPLAIN SELECT QUERY 
```

Let's create a Secondary Index on the `state_code` column of the `post_office` table to improve the performance of queries that filter data by state.

```sql
CREATE INDEX idx_state_code ON post_office (state_code);
```

In this example:
- `idx_state_code` is the name of the Index.
- `state_code` is the column on which the Index is created.
- The Index will improve the speed of queries that filter data based on the `state_code` column.

### Hash

The Hash index can be used only if the equality condition `=` is being used in the query. 

### GiST

### SP-GiST

### GIN

### BRIN

## Advantages of Indexes

1. **Faster Data Retrieval**: Indexes allow the database to locate specific rows quickly, especially when querying large datasets.
2. **Improved Query Performance**: Queries that filter, join, or sort data based on indexed columns execute more efficiently.
3. **Enforcement of Constraints**: Indexes can enforce uniqueness and referential integrity constraints, ensuring data integrity.

## Summary

Indexes play a crucial role in optimizing database performance by providing fast access to data. By creating indexes on appropriate columns, you can improve the speed of data retrieval operations and enhance the overall performance of your database system. However, it's essential to strike a balance between the benefits of indexes and their impact on data modification operations.