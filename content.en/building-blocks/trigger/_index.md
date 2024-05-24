---
title: 'Trigger'
weight: 7
--- 

# Understanding Triggers in RDBMS

## Introduction

A Trigger is a database object that automatically executes a specified set of actions in response to certain events on a table or view in a database. Triggers are often used to enforce business rules, perform data validation, or maintain audit trails.

## Trigger

A Trigger consists of three main components:

1. **Event**: The event that triggers the execution of the trigger. This can be an INSERT, UPDATE, or DELETE operation on a table.
2. **Condition**: An optional condition that must be met for the trigger to execute. If the condition evaluates to true, the trigger's actions are executed.
3. **Action**: The set of SQL statements that are executed when the trigger is activated. These actions can include inserting, updating, or deleting data in other tables, or performing other database operations.

## Example: Auditing with Triggers

Let's create a Trigger that maintains an audit trail whenever a new post office is added or an existing one is modified in the `post_office` table. The audit trail will be stored in a separate `post_office_audit` table.

### Creating the Audit Table

First, we need to create the `post_office_audit` table to store the audit records.

```sql
CREATE TABLE post_office_audit (
    audit_id INT AUTO_INCREMENT PRIMARY KEY,
    action_type VARCHAR(10),
    post_office_id INT,
    area_name TEXT,
    city_name TEXT,
    pin_code INT,
    phone_number BIGINT,
    audit_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Creating the Trigger

Next, we create the Trigger that will populate the `post_office_audit` table.

```sql
CREATE TRIGGER post_office_audit_trigger
AFTER INSERT OR UPDATE ON post_office
FOR EACH ROW
BEGIN
    IF INSERTING THEN
        INSERT INTO post_office_audit (action_type, post_office_id, area_name, city_name, pin_code, phone_number, audit_timestamp)
        VALUES ('INSERT', :NEW.pin_code, :NEW.area_name, :NEW.city_name, :NEW.pin_code, :NEW.phone_number, SYSDATE);
    END IF;
    
    IF UPDATING THEN
        INSERT INTO post_office_audit (action_type, post_office_id, area_name, city_name, pin_code, phone_number, audit_timestamp)
        VALUES ('UPDATE', :OLD.pin_code, :OLD.area_name, :OLD.city_name, :OLD.pin_code, :OLD.phone_number, SYSDATE);
    END IF;
END;
```

In this example:
- `post_office_audit_trigger` is the name of the Trigger.
- `AFTER INSERT OR UPDATE ON post_office` specifies that the Trigger should execute after an INSERT or UPDATE operation on the `post_office` table.
- `FOR EACH ROW` indicates that the Trigger should execute once for each row affected by the operation.
- Inside the Trigger, we use conditional blocks (`IF INSERTING` and `IF UPDATING`) to determine the type of operation (INSERT or UPDATE) and insert the appropriate audit record into the `post_office_audit` table.

## Advantages of Triggers

1. **Automatic Execution**: Triggers execute automatically in response to specified events, eliminating the need for manual intervention.
2. **Enforcement of Business Rules**: Triggers can enforce complex business rules and data integrity constraints at the database level.
3. **Audit Trails**: Triggers can be used to maintain audit trails, logging changes to sensitive data for compliance and security purposes.

## Summary

Triggers are powerful database objects that automate actions based on specified events. By using triggers, you can enforce business rules, maintain audit trails, and ensure data integrity in your database applications. However, it's essential to use triggers judiciously to avoid performance issues and maintainability challenges.