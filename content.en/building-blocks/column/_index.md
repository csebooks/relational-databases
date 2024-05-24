---
title: 'Column'
weight: 2
--- 


Columns give the table its structure by defining the name of the value, the type of the values, constraints if any, etc. Some columns can be designated as KEY columns, which are crucial for identifying records uniquely.

## Key Columns

### Primary Key

Each post office can be uniquely identified by its PIN Code. This makes the `pin_code` column the Primary Key. A Primary Key uniquely identifies each record in a table and ensures that no two rows have the same primary key value.

To set `pin_code` as the Primary Key, we use the following SQL statement:

```sql
ALTER TABLE post_office
ADD PRIMARY KEY (pin_code);
```

### Unique Key

The phone number of each post office is also unique, meaning no two post offices share the same phone number. This makes the `phone_number` column a Unique Key. A Unique Key constraint ensures that all values in a column are different.

To set `phone_number` as a Unique Key, we use the following SQL statement:

```sql
ALTER TABLE post_office
ADD UNIQUE (phone_number);
```

## Foreign Key

Post offices reside in different states, which we can represent in a separate table called `state`. This table includes the state code and state name. The `state` table might look like this:

```sql
CREATE TABLE state (
    state_code CHAR(2) PRIMARY KEY,
    state_name VARCHAR(100)
);
```

To link the `post_office` table to the `state` table, we can use a Foreign Key. The Foreign Key in the `post_office` table will refer to the Primary Key in the `state` table. Here’s how we can add a Foreign Key constraint to the `post_office` table:

1. First, we need to add a `state_code` column to the `post_office` table.

```sql
ALTER TABLE post_office
ADD state_code CHAR(2);
```

2. Then, we can define the Foreign Key constraint.

```sql
ALTER TABLE post_office
ADD CONSTRAINT fk_state
FOREIGN KEY (state_code) REFERENCES state(state_code);
```

## Summary

In this lesson, we covered the following concepts related to columns in an RDBMS table:

1. **Primary Key**: Uniquely identifies each record in a table. Example: `pin_code`.
2. **Unique Key**: Ensures all values in a column are unique. Example: `phone_number`.
3. **Foreign Key**: Creates a link between two tables by referring to the Primary Key of another table. Example: `state_code` in `post_office` referencing `state_code` in `state`.

By understanding and using these key concepts, you can effectively structure your tables and maintain data integrity in an RDBMS.