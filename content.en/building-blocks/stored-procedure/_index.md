---
title: 'Stored Procedure'
weight: 6
--- 

A Stored Procedure is a special feature in RDBMS that offers performance, security, and simplicity by allowing a set of SQL statements to be grouped together as one unit of work. These procedures are stored inside the database and can effectively perform operations on the stored data.

## Procedure

A Procedure is a set of SQL statements put together as one unit of work. It allows you to encapsulate a sequence of operations that perform a specific task. These tasks can range from simple queries to complex data manipulation operations.

## Stored Procedure

A Stored Procedure is a Procedure that is stored inside the database. This means that the procedure's code resides within the database server itself. This offers several advantages, including:

1. **Performance**: Stored Procedures are compiled and optimized by the database server, leading to faster execution times.
2. **Security**: Access to the database objects can be controlled through Stored Procedures, providing an additional layer of security.
3. **Simplicity**: By encapsulating complex operations into Stored Procedures, application code can be simplified and centralized.

## Example: Retrieving Post Offices by State

Let's create a Stored Procedure that retrieves all post offices in a given state.

```sql
CREATE PROCEDURE GetPostOfficesByState (IN state_code CHAR(2))
BEGIN
    SELECT *
    FROM post_office
    WHERE state_code = state_code;
END;
```

In this example:
- `GetPostOfficesByState` is the name of the Stored Procedure.
- `(IN state_code CHAR(2))` defines an input parameter for the procedure.
- `BEGIN` and `END` delimit the body of the procedure.
- Inside the procedure, we execute a SELECT statement to retrieve post offices based on the provided state code.

## Summary

Stored Procedures offer a powerful way to encapsulate and execute SQL logic within the database server itself. By storing procedures directly in the database, you can achieve better performance, security, and simplicity in your applications.