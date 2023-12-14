---
title: 2.7 Summary
weight: 13
---

2.7 Summary## 2.7 Summary

• The **relational data model** is based on a collection of tables. The user of the database system may query these tables, insert new tuples, delete tuples, and update (modify) tuples. There are several languages for expressing these operations.

• The **schema** of a relation refers to its logical design, while an **instance** of the relation refers to its contents at a point in time. The schema of a database and an instance of a database are similarly defined. The schema of a relation includes its attributes, and optionally the types of the attributes and constraints on the relation such as primary and foreign key constraints.

• A **superkey** of a relation is a set of one or more attributes whose values are guaranteed to identify tuples in the relation uniquely. A candidate key is a minimal superkey, that is, a set of attributes that forms a superkey, but none of whose subsets is a superkey. One of the candidate keys of a relation is chosen as its **primary key**.

• A **foreign key** is a set of attributes in a referencing relation, such that for each tuple in the referencing relation, the values of the foreign key attributes are guaranteed to occur as the primary key value of a tuple in the referenced relation.

• A **schema diagram** is a pictorial depiction of the schema of a database that shows the relations in the database, their attributes, and primary keys and foreign keys.

• The **relational query languages** define a set of operations that operate on tables, and output tables as their results. These operations can be combined to get expressions that express desired queries.

• The **relational algebra** provides a set of operations that take one or more relations as input and return a relation as an output. Practical query languages such as SQL are based on the relational algebra, but add a number of useful syntactic features.

**Review Terms**
• Table
• Relation
• Tuple
• Attribute
• Domain
• Atomic domain
• Attribute
• Domain
• Atomic domain
• Referential integrity constraint
• Schema diagram
• Query language
◦ Procedural language
◦ Nonprocedural language
• Operations on relations
◦ Selection of tuples
◦ Selection of attributes
◦ Natural join
◦ Cartesian product
◦ Set operations
• Relational algebra


**Practice Exercises**

**2.1** Consider the relational database of Figure 2.14. What are the appropriate primary keys?

**2.2** Consider the foreign key constraint from the _dept name_ attribute of _instructor_ to the _department_ relation. Give examples of inserts and deletes to these relations, which can cause a violation of the foreign key constraint.

**2.3** Consider the _time slot_ relation. Given that a particular time slot can meet more than once in a week, explain why _day_ and _start time_ are part of the primary key of this relation, while _end time_ is not.

**2.4** In the instance of _instructor_ shown in Figure 2.1, no two instructors have the same name. From this, can we conclude that _name_ can be used as a superkey (or primary key) of _instructor_?

**2.5** What is the result of first performing the cross product of _student_ and _advisor_, and then performing a selection operation on the result with the predicate _s id_ \= ID? (Using the symbolic notation of relational algebra, this query can be written as σ_s id_\=_I D_(_student_ × _advisor_ ).)

_employee_ (_person name_, _street_, _city_) 
_works_ (_person name_, _company name_, _salary_) 
_company_ (_company name_, _city_)

**Figure 2.14** Relational database for Exercises 2.1, 2.7, and 2.12.  

branch(branch name, branch city, assets)
customer (customer name, customer street, customer city)
loan (loan number, branch name, amount)
borrower (customer name, loan number)
account (account number, branch name, balance)
depositor (customer name, account number)

**Figure 2.15** Banking database for Exercises 2.8, 2.9, and 2.13.

**2.6** Consider the following expressions, which use the result of a relational algebra operation as the input to another operation. For each expression, explain in words what the expression does.

a. σ_year_≥2009(_takes_) _ student_

b. σ_year_≥2009(_takes  student_)

c. _ID,name,course id_ (_student  takes_)

**2.7** Consider the relational database of Figure 2.14. Give an expression in the relational algebra to express each of the following queries:

a. Find the names of all employees who live in city “Miami”.

b. Find the names of all employees whose salary is greater than $100,000.

c. Find the names of all employees who live in “Miami” and whose salary is greater than $100,000.

**2.8** Consider the bank database of Figure 2.15. Give an expression in the relational algebra for each of the following queries.

a. Find the names of all branches located in “Chicago”.

b. Find the names of all borrowers who have a loan in branch “Downtown”.

