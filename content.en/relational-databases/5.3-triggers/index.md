---
title: 5.3 Triggers
weight: 35
---

5.3 Triggers## 5.3 Triggers

A **trigger** is a statement that the system executes automatically as a side effect of a modification to the database. To design a trigger mechanism, we must meet two requirements:

**1\.** Specify when a trigger is to be executed. This is broken up into an _event_ that causes the trigger to be checked and a _condition_ that must be satisfied for trigger execution to proceed.

**2\.** Specify the _actions_ to be taken when the trigger executes.

Once we enter a trigger into the database, the database system takes on the responsibility of executing it whenever the specified event occurs and the corresponding condition is satisfied.

### 5.3.1 Need for Triggers

<<<<<<< HEAD
Triggers can be used to implement certain integrity constraints that cannot be specified using the constraint mechanism of SQL. Triggers are also useful mecha 

**5.3 Triggers 181**

nisms for alerting humans or for starting certain tasks automatically when certain conditions are met. As an illustration, we could design a trigger that, whenever a tuple is inserted into the _takes_ relation, updates the tuple in the _student_ relation for the student taking the course by adding the number of credits for the course to the student’s total credits. As another example, suppose a warehouse wishes to maintain a minimum inventory of each item; when the inventory level of an item falls below the minimum level, an order can be placed automatically. On an update of the inventory level of an item, the trigger compares the current inventory level with the minimum inventory level for the item, and if the level is at or below the minimum, a new order is created.
=======
Triggers can be used to implement certain integrity constraints that cannot be specified using the constraint mechanism of SQL. Triggers are also useful mechanisms for alerting humans or for starting certain tasks automatically when certain conditions are met. As an illustration, we could design a trigger that, whenever a tuple is inserted into the _takes_ relation, updates the tuple in the _student_ relation for the student taking the course by adding the number of credits for the course to the student’s total credits. As another example, suppose a warehouse wishes to maintain a minimum inventory of each item; when the inventory level of an item falls below the minimum level, an order can be placed automatically. On an update of the inventory level of an item, the trigger compares the current inventory level with the minimum inventory level for the item, and if the level is at or below the minimum, a new order is created.
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

Note that trigger systems cannot usually perform updates outside the database, and hence, in the inventory replenishment example, we cannot use a trigger to place an order in the external world. Instead, we add an order to a relation holding reorders. We must create a separate permanently running system process that periodically scans that relation and places orders. Some database systems provide built-in support for sending email from SQL queries and triggers, using the above approach.

### 5.3.2 Triggers in SQL

We now consider how to implement triggers in SQL. The syntax we present here is defined by the SQL standard, but most databases implement nonstandard versions of this syntax. Although the syntax we present here may not be supported on such systems, the concepts we describe are applicable across implementations. We discuss nonstandard trigger implementations later in this section (page 184).

Figure 5.8 shows how triggers can be used to ensure referential integrity on the _time slot id_ attribute of the _section_ relation. The first trigger definition in the figure specifies that the trigger is initiated _after_ any insert on the relation _section_ and it ensures that the _time slot id_ value being inserted is valid. An SQL insert statement could insert multiple tuples of the relation, and the **for each row** clause in the trigger code would then explicitly iterate over each inserted row. The **referencing new row as** clause creates a variable _nrow_ (called a **transition variable**) that stores the value of an inserted row after the insertion.

The **when** statement specifies a condition. The system executes the rest of the trigger body only for tuples that satisfy the condition. The **begin atomic** _. . ._

**end** clause can serve to collect multiple SQL statements into a single compound statement. In our example, though, there is only one statement, which rolls back the transaction that caused the trigger to get executed. Thus any transaction that violates the referential integrity constraint gets rolled back, ensuring the data in the database satisfies the constraint.

It is not sufficient to check referential integrity on inserts alone, we also need to consider updates of _section_, as well as deletes and updates to the referenced table _time slot_. The second trigger definition in Figure 5.8 considers the case of deletes to _time slot_. This trigger checks that the _time slot id_ of the tuple being deleted is either still present in _time slot_, or that no tuple in _section_ contains that particular _time slot id_ value; otherwise, referential integrity would be violated.

**create trigger** _timeslot check1_ **after insert on** _section_ 
**referencing new row as** _nrow_ 
**for each row** 
**when** (_nrow.time slot id_ **not in** (
**select** _time slot id_ 
**from** _time slot_)) /\* _time slot id_ not present in _time slot_ \*/
**begin** 
**rollback**
**end**;

**create trigger** _timeslot check2_ **after delete on** _timeslot_ **referencing old row as** _orow_ **for each row when** (_orow.time slot id_ **not in** (
**select** _time slot id_ 
**from** _time slot_) /\* last tuple for _time slot id_ deleted from _time slot_ \*/

**and** _orow.time slot id_ **in** ( 
**select** _time slot id_ 
**from** _section_)) /\* and _time slot id_ still referenced from _section_\*/
**begin** 
**rollback**
**end**;

**Figure 5.8** Using triggers to maintain referential integrity.

To ensure referential integrity, we would also have to create triggers to handle updates to _section_ and _time slot_; we describe next how triggers can be executed on updates, but leave the definition of these triggers as an exercise to the reader.

For updates, the trigger can specify attributes whose update causes the trigger to execute; updates to other attributes would not cause it to be executed. For example, to specify that a trigger executes after an update to the _grade_ attribute of the _takes_ relation, we write:

**after update of** _takes_ **on** _grade_

The **referencing old row as** clause can be used to create a variable storing the old value of an updated or deleted row. The **referencing new row as** clause can be used with updates in addition to inserts.

Figure 5.9 shows how a trigger can be used to keep the _tot cred_ attribute value of _student_ tuples up-to-date when the _grade_ attribute is updated for a tuple in the _takes_ relation. The trigger is executed only when the _grade_ attribute is updated from a value that is either null or ’F’, to a grade that indicates the course is successfully completed. The **update** statement is normal SQL syntax except for the use of the variable _nrow_.  

**create trigger** credits earned **after update** of takes **on** (grade)
**referencing new row as** nrow
**referencing old row as** orow
**for each row**
**when** nrow.grade <> ’F’ **and** nrow.grade is **not null**
**and** (orow.grade = ’F’ **or** orow.grade **is null**)
**begin atomic**
**update** student
**set** tot cred= tot cred+
(**select** credits
**from** course
**where** course.course id= nrow.course id)
**where** student.id = nrow.id;
end;

**Figure 5.9** Using a trigger to maintain _credits earned_ values.

A more realistic implementation of this example trigger would also handle grade corrections that change a successful completion grade to a fail grade, and handle insertions into the _takes_ relation where the _grade_ indicates successful completion. We leave these as an exercise for the reader.

As another example of the use of a trigger, the action on **delete** of a _student_ tuple could be to check if the student has any entries in the _takes_ relation, and if so, to delete them.

Many database systems support a variety of other triggering events, such as when a user (application) logs on to the database (that is, opens a connection), the system shuts down, or changes are made to system settings.

Triggers can be activated **before** the event (insert, delete, or update) instead of **after** the event. Triggers that execute before an event can serve as extra constraints that can prevent invalid updates, inserts, or deletes. Instead of letting the invalid action proceed and cause an error, the trigger might take action to correct the problem so that the update, insert, or delete becomes valid. For example, if we attempt to insert an instructor into a department whose name does not appear in the _department_ relation, the trigger could insert a tuple into the _department_ relation for that department name before the insertion generates a foreign-key violation. As another example, suppose the value of an inserted grade is blank, presumably to indicate the absence of a grade. We can define a trigger that replaces the value by the **null** value. The **set** statement can be used to carry out such modifications. An example of such a trigger appears in Figure 5.10.

Instead of carrying out an action for each affected row, we can carry out a single action for the entire SQL statement that caused the insert, delete, or update. To do so, we use the **for each statement** clause instead of the **for each row** clause. The clauses **referencing old table as** or **referencing new table as** can then be used to refer to temporary tables (called _transition tables_) containing all the affected rows. Transition tables cannot be used with **before** triggers, but can be  

**create trigger** _setnull_ **before update on** _takes_ 
**referencing new row as** _nrow_ 
**for each row when** (_nrow.grade_ \= ’ ’) 
**begin atomic**
**set** _nrow.grade_ \= **null**; 
**end**;

**Figure 5.10** Example of using **set** to change an inserted value.

used with **after** triggers, regardless of whether they are statement triggers or row triggers. A single SQL statement can then be used to carry out multiple actions on the basis of the transition tables.

**NONSTANDARD TRIGGER SYNTAX**

Although the trigger syntax we describe here is part of the SQL standard, and is supported by IBM DB2, most other database systems have nonstandard syntax for specifying triggers, and may not implement all features in the SQL standard. We outline a few of the differences below; see the respective system manuals for further details.

For example, in the Oracle syntax, unlike the SQL standard syntax, the keyword **row** does not appear in the **referencing** statement. The keyword **atomic** does not appear after **begin**. The reference to _nrow_ in the **select** statement nested in the **update** statement must begin with a colon (:) to inform the system that the variable _nrow_ is defined externally from the SQL statement. Further, subqueries are not allowed in the **when** and **if** clauses. It is possible to work around this problem by moving complex predicates from the **when** clause into a separate query that saves the result into a local variable, and then reference that variable in an **if** clause, and the body of the trigger then moves into the corresponding **then** clause. Further, in Oracle, triggers are not allowed to execute a transaction rollback directly; however, they can instead use a function called raise application error to not only roll back the transaction, but also return an error message to the user/application that performed the update.

As another example, in Microsoft SQL Server the keyword **on** is used instead of **after**. The **referencing** clause is omitted, and old and new rows are referenced by the tuple variables **deleted** and **inserted**. Further, the **for each row** clause is omitted, and **when** is replaced by **if**. The **before** specification is not supported, but an **instead of** specification is supported.

In PostgreSQL, triggers do not have a body, but instead invoke a procedure for each row, which can access variables **new** and **old** containing the old and new values of the row. Instead of performing a rollback, the trigger can raise an exception, with an associated error message.  

**5.3 Triggers 185**

**create trigger** _reorder_ **after update of** _amount_ **on** _inventory_ 
**referencing old row as** _orow_, **new row as** _nrow_ 
**for each row when** _nrow.level <_\= (**select** _level_
**from** _minlevel_ **where** _minlevel.item_ \= _orow.item_)

**and** _orow.level >_ (**select** _level_ **from** _minlevel_ **where** _minlevel.item_ \= _orow.item_)
**begin atomic insert into** _orders_
(**select** _item, amount_ **from** _reorder_ **where** _reorder.item_ \= _orow.item_);
**end**;

**Figure 5.11** Example of trigger for reordering an item.

Triggers can be disabled or enabled; by default they are enabled when they are created, but can be disabled by using **alter trigger** _trigger name_ **disable** (some databases use alternative syntax such as **disable trigger** _trigger name_). A trigger that has been disabled can be enabled again. A trigger can instead be dropped, which removes it permanently, by using the command **drop trigger** _trigger name_.

Returning to our warehouse inventory example, suppose we have the following relations:

• _inventory_ (_item, level_), which notes the current amount of the item in the warehouse.

• _minlevel_ (_item, level_), which notes the minimum amount of the item to be maintained.

• _reorder_ (_item, amount_), which notes the amount of the item to be ordered when its level falls below the minimum.

• _orders_ (_item, amount_), which notes the amount of the item to be ordered.

Note that we have been careful to place an order only when the amount falls from above the minimum level to below the minimum level. If we check only that the new value after an update is below the minimum level, we may place an order erroneously when the item has already been reordered. We can then use the trigger shown in Figure 5.11 for reordering the item.

SQL-based database systems use triggers widely, although before SQL:1999 they were not part of the SQL standard. Unfortunately, each database system implemented its own syntax for triggers, leading to incompatibilities. The SQL:1999 syntax for triggers that we use here is similar, but not identical, to the syntax in the IBM DB2 and Oracle database systems.  

### 5.3.3 When Not to Use Triggers

There are many good uses for triggers, such as those we have just seen in Section 5.3.2, but some uses are best handled by alternative techniques. For example, we could implement the **on delete cascade** feature of a foreign-key constraint by using a trigger, instead of using the cascade feature. Not only would this be more work to implement, but also, it would be much harder for a database user to understand the set of constraints implemented in the database.

As another example, triggers can be used to maintain materialized views. For instance, if we wished to support very fast access to the total number of students registered for each course section, we could do this by creating a relation

_section registration_(_course id_, _sec id_, _semester_, _year_, _total students_)

defined by the query

**select** _course id_, _sec id_, _semester_, _year_, **count**(_ID_) **as** _total students_ 
**from** _takes_ 
**group by** _course id_, _sec id_, _semester_, _year_;

The value of _total students_ for each course must be maintained up-to-date by triggers on insert, delete, or update of the _takes_ relation. Such maintenance may require insertion, update or deletion of tuples from _section registration_, and triggers must be written accordingly.

However, many database systems now support materialized views, which are automatically maintained by the database system (see Section 4.2.3). As a result, there is no need to write trigger code for maintaining such materialized views.

Triggers have been used for maintaining copies, or replicas, of databases. A collection of triggers on insert, delete, or update can be created on each relation to record the changes in relations called **change** or **delta** relations. A separate process copies over the changes to the replica of the database. Modern database systems, however, provide built-in facilities for database replication, making triggers unnecessary for replication in most cases. Replicated databases are discussed in detail in Chapter 19.

Another problem with triggers lies in unintended execution of the triggered action when data are loaded from a backup copy,6 or when database updates at a site are replicated on a backup site. In such cases, the triggered action has already been executed, and typically should not be executed again. When loading data, triggers can be disabled explicitly. For backup replica systems that may have to take over from the primary system, triggers would have to be disabled initially, and enabled when the backup site takes over processing from the primary system. As an alternative, some database systems allow triggers to be specified as **not**

![Alt text](image-37.png)

**Figure 5.12** The _prereq_ relation.

**for replication**, which ensures that they are not executed on the backup site during database replication. Other database systems provide a system variable that denotes that the database is a replica on which database actions are being replayed; the trigger body should check this variable and exit if it is true. Both solutions remove the need for explicit disabling and enabling of triggers.

Triggers should be written with great care, since a trigger error detected at runtime causes the failure of the action statement that set off the trigger. Furthermore, the action of one trigger can set off another trigger. In the worst case, this could even lead to an infinite chain of triggering. For example, suppose an insert trigger on a relation has an action that causes another (new) insert on the same relation. The insert action then triggers yet another insert action, and so on ad infinitum. Some database systems limit the length of such chains of triggers (for example, to 16 or 32) and consider longer chains of triggering an error. Other systems flag as an error any trigger that attempts to reference the relation whose modification caused the trigger to execute in the first place.

Triggers can serve a very useful purpose, but they are best avoided when alternatives exist. Many trigger applications can be substituted by appropriate use of stored procedures, which we discussed in Section 5.2.

