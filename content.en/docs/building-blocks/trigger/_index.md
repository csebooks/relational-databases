---
title: 'Trigger'
weight: 7
--- 

> A Trigger is a database object that automatically executes a specified set of actions in response to certain events on a table or view in a database. Triggers are often used to enforce business rules, perform data validation, or maintain audit trails.

A Trigger consists of three main components:

1. **Event**: The event that triggers the execution of the trigger. This can be an INSERT, UPDATE, or DELETE operation on a table.
2. **Condition**: An optional condition that must be met for the trigger to execute. If the condition evaluates to true, the trigger's actions are executed.
3. **Action**: The set of SQL statements that are executed when the trigger is activated. These actions can include inserting, updating, or deleting data in other tables, or performing other database operations.

## Auditing with Triggers

Lets first create a table that stroes ticket information

```sql
CREATE TABLE ticket (
    title VARCHAR(20),
    description VARCHAR(20),
    salary INT
)
```

Then, we need to create the `ticket_audit` table to store the audit records.

```sql
create table ticket_audit (
    op char(1) NOT NULL,
    stamp timestamp NOT NULL,
    user_id char(20) NOT NULL,
    title varchar(20),
    description varchar(20),
    salary int
)
```
Lets consider our auditing does the follwing

```sql
create or replace
function ticket_audit_information() returns trigger  

 as  

 $ticket_audit$

 begin

 if (TG_OP = 'DELETE') then

 insert
	into
	ticket_audit
select
	'D',
	now(),
	user,
	OLD.*;

elsif (TG_OP = 'UPDATE') then

 insert
	into
	ticket_audit
select
	'U',
	now(),
	user,
	NEW.*;

elsif (TG_OP = 'INSERT') then

 insert
	into
	ticket_audit
select
	'I',
	now(),
	user,
	NEW.*;
end if;

return null;
end;

$ticket_audit$ 

 language plpgsql;
```

Next, we create the Trigger that will populate the `ticket_audit` table.

```sql
create trigger ticket_audit_trigger
after
insert
    or
update
    or delete on ticket for each row execute procedure ticket_audit_information();
```

All Set now. Lets perform the write operations on `ticket` and observe the `ticket_audit`

```sql
insert into ticket values ('title', 'description', 1);
select * from ticket_audit;
```

```sql
update ticket set title = 'title2';
select * from ticket_audit;
```

```sql
DELETE from ticket;
select * from ticket_audit;
```

In this example:
- `ticket_audit_trigger` is the name of the Trigger.
- `AFTER INSERT OR UPDATE ON ticket` specifies that the Trigger should execute after an INSERT or UPDATE operation on the `ticket` table.
- `FOR EACH ROW` indicates that the Trigger should execute once for each row affected by the operation.
- Inside the Trigger, we use conditional blocks (`IF INSERTING` and `IF UPDATING`) to determine the type of operation (INSERT or UPDATE) and insert the appropriate audit record into the `ticket_audit` table.

## Advantages of Triggers

1. **Automatic Execution**: Triggers execute automatically in response to specified events, eliminating the need for manual intervention.
2. **Enforcement of Business Rules**: Triggers can enforce complex business rules and data integrity constraints at the database level.
3. **Audit Trails**: Triggers can be used to maintain audit trails, logging changes to sensitive data for compliance and security purposes.

## Summary

Triggers are powerful database objects that automate actions based on specified events. By using triggers, you can enforce business rules, maintain audit trails, and ensure data integrity in your database applications. However, it's essential to use triggers judiciously to avoid performance issues and maintainability challenges.