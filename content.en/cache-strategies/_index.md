---
title: 'Cache Strategies'
weight: 18
references:
    links:
        - https://www.educba.com/postgresql-caching/
        - https://martinheinz.dev/blog/105
        - https://www.enterprisedb.com/blog/the-cache-that-wasnt-optimizing-postgres-for-select-performance
--- 

> PostgreSQL caches Data in tables, Indexes and Query execution plans. While the query execution plan caching focus is on saving CPU cycles; caching for Table data and Index data is focused to save costly disk I/O operation.

When we think about caching, that means it increases the speed of operations and data retrieval speed. Let’s discuss different scenarios to increase the performance of caching as follows.

# Bottleneck situation in the query performance

Before discussing the solution to the problem, let’s understand why queries are slow. The first reason is the performance of your system. That means when we run an application on a system at that time, we check the performance of the system. For that instance, any CPU or network bottleneck may occur that affects your process means fetching data from the database. The solution for that problem is to check the health of your system regularly and for bottleneck increase or scale up the database size.

# Internal Cache Performance

Internal cache performance means the hit rate of the database table. Insufficient internal buffer space of the database is the main reason for the low hit rate of the database table. So in PostgreSQL, we use the shared buffer concept to avoid a low rate of database table. A shared buffer is used to manage the buffer space of a database.

```sql
CREATE Table tblDummy

(

id serial primary key,

p_id int,

c_id int,

entry_time timestamp,

entry_value int,

description varchar(50)  

);

CREATE INDEX ON tblDummy(c_id );
```
Lets create some data
```sql
DO $$
DECLARE
    random_value integer;
    p_id_ctr integer;
    c_id_ctr integer;
BEGIN
    FOR p_id_ctr IN 1..10000 LOOP
        FOR c_id_ctr IN 1..200 LOOP
            random_value := floor(random() * 75 + 25); -- Corrected assignment syntax for random_value
            INSERT INTO tblDummy (p_id, c_id, entry_time, entry_value, description)
            VALUES (p_id_ctr, c_id_ctr, now(), random_value, CONCAT('Description for :', p_id_ctr, c_id_ctr));
        END LOOP;
    END LOOP;
END $$;
```
 Lets check the caching informations.


```sql
SELECT pg_stat_reset();

SELECT * from tbldummy where c_id < 150;

SELECT heap_blks_read, heap_blks_hit from pg_statio_user_tables where relname='tbldummy';
```

In this context, "heap" is not to be confused with the Computer Science term of "heap," which refers to a data structure or segment of memory. In PostgreSQL, "heap" refers to the data file(s) related to a particular table. Columns prefixed with idx_ refer to indexes created to speed up the lookup of data (in the heap).

