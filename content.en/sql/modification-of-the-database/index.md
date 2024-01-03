---
title: Modification of the Database
weight: 9
---

# Modification of the Database

We have restricted our attention until now to the extraction of information from the database. Now, we show how to add, remove, or change information with SQL.

## Deletion

A delete request is expressed in much the same way as a query. We can delete only whole tuples; we cannot delete values on only particular attributes. SQL expresses a deletion by

**delete from** _r_ 
**where** _P_;

where _P_ represents a predicate and _r_ represents a relation. The **delete** statement first finds all tuples _t_ in _r_ for which _P_(_t_) is true, and then deletes them from _r_. The **where** clause can be omitted, in which case all tuples in _r_ are deleted.

Note that a **delete** command operates on only one relation. If we want to delete tuples from several relations, we must use one **delete** command for each relation. The predicate in the **where** clause may be as complex as a **select** command’s **where** clause. At the other extreme, the **where** clause may be empty. The request  

**delete from** _instructor_;

deletes all tuples from the _instructor_ relation. The _instructor_ relation itself still exists, but it is empty.

Here are examples of SQL delete requests:

• Delete all tuples in the _instructor_ relation pertaining to instructors in the Finance department.

**delete from** _instructor_ 
**where** _dept name_\= ’Finance’;

• Delete all instructors with a salary between $13,000 and $15,000.

**delete from** _instructor_ 
**where** _salary_ **between** 13000 **and** 15000;

• Delete all tuples in the _instructor_ relation for those instructors associated with a department located in the Watson building.

**delete from** _instructor_ 
**where** _dept name_ **in** (**select** _dept name_**from** _department_ **where** _building_ \= ’Watson’);

This **delete** request first finds all departments located in Watson, and then deletes all _instructor_ tuples pertaining to those departments.

Note that, although we may delete tuples from only one relation at a time, we may reference any number of relations in a **select-from-where** nested in the **where** clause of a **delete**. The **delete** request can contain a nested **select** that references the relation from which tuples are to be deleted. For example, suppose that we want to delete the records of all instructors with salary below the average at the university. We could write:

**delete from** _instructor_ 
**where** _salary<_ (**select avg** (_salary_)**from** _instructor_);

The **delete** statement first tests each tuple in the relation _instructor_ to check whether the salary is less than the average salary of instructors in the univer- sity. Then, all tuples that fail the test—that is, represent an instructor with a lower-than-average salary—are deleted. Performing all the tests before perform- ing any deletion is important—if some tuples are deleted before other tuples have been tested, the average salary may change, and the final result of the **delete** would depend on the order in which the tuples were processed!

The **delete** statement first tests each tuple in the relation _instructor_ to check whether the salary is less than the average salary of instructors in the university. Then, all tuples that fail the test—that is, represent an instructor with a lower-than-average salary—are deleted. Performing all the tests before performing any deletion is important—if some tuples are deleted before other tuples  

have been tested, the average salary may change, and the final result of the **delete** would depend on the order in which the tuples were processed!

## 3.9.2 Insertion

To insert data into a relation, we either specify a tuple to be inserted or write a query whose result is a set of tuples to be inserted. Obviously, the attribute values for inserted tuples must be members of the corresponding attribute’s domain. Similarly, tuples inserted must have the correct number of attributes.

The simplest **insert** statement is a request to insert one tuple. Suppose that we wish to insert the fact that there is a course CS-437 in the Computer Science department with title “Database Systems”, and 4 credit hours. We write:

**insert into** _course_ **values** (’CS-437’, ’Database Systems’, ’Comp. Sci.’, 4);

In this example, the values are specified in the order in which the corresponding attributes are listed in the relation schema. For the benefit of users who may not remember the order of the attributes, SQL allows the attributes to be specified as part of the **insert** statement. For example, the following SQL **insert** statements are identical in function to the preceding one:

**insert into** _course_ (_course id_, _title_, _dept name_, _credits_) 
**values** (’CS-437’, ’Database Systems’, ’Comp. Sci.’, 4);

**insert into** _course_ (_title_, _course id_, _credits_, _dept name_) 
**values** (’Database Systems’, ’CS-437’, 4, ’Comp. Sci.’);

More generally, we might want to insert tuples on the basis of the result of a query. Suppose that we want to make each student in the Music department who has earned more than 144 credit hours, an instructor in the Music department, with a salary of $18,000. We write:

**insert into** _instructor_ 
**select** _ID_, _name_, _dept name_, 18000 
**from** _student_ 
**where** _dept name_ \= ’Music’ **and** _tot cred >_ 144;

Instead of specifying a tuple as we did earlier in this section, we use a **select** to specify a set of tuples. SQL evaluates the **select** statement first, giving a set of tuples that is then inserted into the _instructor_ relation. Each tuple has an _ID_, a _name_, a _dept name_ (Music), and an salary of $18,000.

It is important that we evaluate the **select** statement fully before we carry out any insertions. If we carry out some insertions even as the **select** statement is being evaluated, a request such as:  

**insert into** _student_ 
**select** \* 
**from** _student_;

might insert an infinite number of tuples, if the primary key constraint on _student_ were absent. Without the primary key constraint, the request would insert the first tuple in _student_ again, creating a second copy of the tuple. Since this second copy is part of _student_ now, the **select** statement may find it, and a third copy would be inserted into _student_. The **select** statement may then find this third copy and insert a fourth copy, and so on, forever. Evaluating the **select** statement completely before performing insertions avoids such problems. Thus, the above **insert** statement would simply duplicate every tuple in the _student_ relation, if the relation did not have a primary key constraint.

Our discussion of the **insert** statement considered only examples in which a value is given for every attribute in inserted tuples. It is possible for inserted tuples to be given values on only some attributes of the schema. The remaining attributes are assigned a null value denoted by _null_. Consider the request:

**insert into** _student_ 
**values** (’3003’, ’Green’, ’Finance’, _null_);

The tuple inserted by this request specified that a student with _ID_ “3003” is in the Finance department, but the _tot cred_ value for this student is not known. Consider the query:

**select** _student_ 
**from** _student_ 
**where** _tot cred >_ 45;

Since the _tot cred_ value of student “3003” is not known, we cannot determine whether it is greater than 45.

Most relational database products have special “bulk loader” utilities to insert a large set of tuples into a relation. These utilities allow data to be read from formatted text files, and can execute much faster than an equivalent sequence of insert statements.

## Updates

In certain situations, we may wish to change a value in a tuple without changing _all_ values in the tuple. For this purpose, the **update** statement can be used. As we could for **insert** and **delete**, we can choose the tuples to be updated by using a query.

Suppose that annual salary increases are being made, and salaries of all instructors are to be increased by 5 percent. We write:  

**update** _instructor_ 
**set** _salary_\= _salary_ \* 1.05;

The preceding update statement is applied once to each of the tuples in _instructor_ relation.

If a salary increase is to be paid only to instructors with salary of less than $70,000, we can write:

**update** _instructor_ 
**set** _salary_ \= _salary_ \* 1.05 
**where** _salary <_ 70000;

In general, the **where** clause of the **update** statement may contain any construct legal in the **where** clause of the **select** statement (including nested **select**s). As with **insert** and **delete**, a nested **select** within an **update** statement may reference the relation that is being updated. As before, SQL first tests all tuples in the relation to see whether they should be updated, and carries out the updates afterward. For example, we can write the request “Give a 5 percent salary raise to instructors whose salary is less than average” as follows:

**update** _instructor_ 
**set** _salary_ \= _salary_ \* 1.05 
**where** _salary <_ (**select avg** (_salary_) **from** _instructor_);

Let us now suppose that all instructors with salary over $100,000 receive a 3 percent raise, whereas all others receive a 5 percent raise. We could write two **update** statements:

**update** _instructor_ 
**set** _salary_ \= _salary_ \* 1.03 
**where** _salary >_ 100000;

**update** _instructor_ 
**set** _salary_ \= _salary_ \* 1.05 
**where** _salary <_\= 100000;

Note that the order of the two **update** statements is important. If we changed the order of the two statements, an instructor with a salary just under $100,000 would receive an over 8 percent raise.

SQL provides a **case** construct that we can use to perform both the updates with a single **update** statement, avoiding the problem with the order of updates.  

**update** _instructor_ 
**set** _salary_ \= **case**
**when** _salary <_\= 100000 **then** _salary_ \* 1.05 
**else** _salary_ \* 1.03
**end**

The general form of the case statement is as follows.

**case** 
**when** _pred_1 **then** _result_1 
**when** _pred_2 **then** _result_2 
. . .
**when** _predn_ **then** _resultn_ **else** _result_0
**end**

The operation returns _resulti_ , where _i_ is the first of _pred_1, _pred_2, . . . , _predn_ that is satisfied; if none of the predicates is satisfied, the operation returns _result_0\. Case statements can be used in any place where a value is expected.

Scalar subqueries are also useful in SQL update statements, where they can be used in the **set** clause. Consider an update where we set the _tot cred_ attribute of each _student_ tuple to the sum of the credits of courses successfully completed by the student. We assume that a course is successfully completed if the student has a grade that is not ’F’ or null. To specify this update, we need to use a subquery in the **set** clause, as shown below:

**update** _student S_ 
**set** _tot cred_ \= (
**select sum**(_credits_) 
**from** _takes_ **natural join** _course_ 
**where** _S_._ID_\= _takes_._ID_ **and**_takes_._grade <>_ ’F’ **and** _takes_._grade_ **is not null**);

Observe that the subquery uses a correlation variable _S_ from the **update** statement. In case a student has not successfully completed any course, the above update statement would set the _tot cred_ attribute value to null. To set the value to 0 instead, we could use another **update** statement to replace null values by 0; a better alternative is to replace the clause “**select sum**(_credits_)” in the preceding subquery by the following **select** clause using a **case** expression:

**select case** 
**when sum**(_credits_) **is not null then sum**(_credits_) 
**else** ()
**end**  