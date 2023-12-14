---
title: Table Inheritance
weight: 4
---

Table Inheritance## Table Inheritance

Subtables in SQL correspond to the E-R notion of specialization/generalization. For instance, suppose we define the people table as follows:

**create table** people **of** Person;

We can then define tables students and teachers as **subtables** of people, as follows:

**create table** students **of** Student **under** people;

**create table** teachers **of** Teacher **under** people;

The types of the subtables (Student and Teacher in the above example) are subtypes of the type of the parent table (Person in the above example). As a result, every attribute present in the table people is also present in the subtables students and teachers.

Further, when we declare students and teachers as subtables of people, every tuple present in students or teachers becomes implicitly present in people. Thus, if a query uses the table people, it will find not only tuples directly inserted into that table, but also tuples inserted into its subtables, namely students and teachers. However, only those attributes that are present in people can be accessed by that query.

SQL permits us to find tuples that are in people but not in its subtables by using “**only** people” in place of people in a query. The **only** keyword can also be used in delete and update statements. Without the **only** keyword, a delete statement on a supertable, such as people, also deletes tuples that were originally inserted in subtables (such as students); for example, a statement:

**delete from** people **where** P;

would delete all tuples from the table people, as well as its subtables students and teachers, that satisfy P. If the **only** keyword is added to the above statement, tuples that were inserted in subtables are not affected, even if they satisfy the **where** clause conditions. Subsequent queries on the supertable would continue to find these tuples.

Conceptually, multiple inheritance is possible with tables, just as it is possible with types. For example, we can create a table of type TeachingAssistant:

**create table** teaching assistants **of** TeachingAssistant

**under** students, teachers;  

As a result of the declaration, every tuple present in the teaching assistants table is also implicitly present in the teachers and in the students table, and in turn in the people table. We note, however, that multiple inheritance of tables is not supported by SQL.

There are some consistency requirements for subtables. Before we state the constraints, we need a definition: we say that tuples in a subtable and parent table **correspond** if they have the same values for all inherited attributes. Thus, corresponding tuples represent the same entity.

The consistency requirements for subtables are:

**1\.** Each tuple of the supertable can correspond to at most one tuple in each of its immediate subtables.

**2\.** SQL has an additional constraint that all the tuples corresponding to each other must be derived from one tuple (inserted into one table).

For example, without the first condition, we could have two tuples in students (or teachers) that correspond to the same person.

The second condition rules out a tuple in people corresponding to both a tuple in students and a tuple in teachers, unless all these tuples are implicitly present because a tuple was inserted in a table teaching assistants, which is a subtable of both teachers and students.

Since SQL does not support multiple inheritance, the second condition actu- ally prevents a person from being both a teacher and a student. Even if multiple inheritance were supported, the same problem would arise if the subtable teaching assistants were absent. Obviously it would be useful to model a situation where

a person can be a teacher and a student, even if a common subtable teaching assistants is not present. Thus, it can be useful to remove the second consis-

tency constraint. Doing so would allow an object to have multiple types, without requiring it to have a most-specific type.

For example, suppose we again have the type Person, with subtypes Student and Teacher, and the corresponding table people, with subtables teachers and stu- dents. We can then have a tuple in teachers and a tuple in students corresponding to the same tuple in people. There is no need to have a type TeachingAssistant that is a subtype of both Student and Teacher. We need not create a type TeachingAssistant unless we wish to store extra attributes or redefine methods in a manner specific to people who are both students and teachers.

We note, however, that SQL unfortunately prohibits such a situation, because of consistency requirement 2. Since SQL also does not support multiple inheri- tance, we cannot use inheritance to model a situation where a person can be both a student and a teacher. As a result, SQL subtables cannot be used to represent overlapping specializations from the E-R model.

We can of course create separate tables to represent the overlapping special- izations/generalizations without using inheritance. The process was described earlier, in Section 7.8.6.1. In the above example, we would create tables people, stu- dents, and teachers, with the students and teachers tables containing the primary-key attribute of Person and other attributes specific to Student and Teacher, respectively. The people table would contain information about all persons, including students and teachers. We would then have to add appropriate referential-integrity constraints to ensure that students and teachers are also represented in the people table.

In other words, we can create our own improved implementation of the subtable mechanism using existing features of SQL, with some extra effort in defining the table, as well as some extra effort at query time to specify joins to access required attributes.

We note that SQL defines a privilege called **under**, which is required in order to create a subtype or subtable under another type or table. The motivation for this privilege is similar to that for the **references** privilege.

