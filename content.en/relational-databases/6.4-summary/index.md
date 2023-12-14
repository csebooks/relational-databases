---
title: 6.4 Summary
weight: 44
---

6.4 Summary## 6.4 Summary

• The **relational algebra** defines a set of algebraic operations that operate on tables, and output tables as their results. These operations can be combined to get expressions that express desired queries. The algebra defines the basic operations used within relational query languages.

• The operations in relational algebra can be divided into:

◦ Basic operations

◦ Additional operations that can be expressed in terms of the basic operations

◦ Extended operations, some of which add further expressive power to relational algebra

• The relational algebra is a terse, formal language that is inappropriate for casual users of a database system. Commercial database systems, therefore, use languages with more “syntactic sugar.” In Chapters 3 through 5, we cover the most influential language—**SQL**, which is based on relational algebra.

• The **tuple relational calculus** and the **domain relational calculus** are nonprocedural languages that represent the basic power required in a relational query language. The basic relational algebra is a procedural language that is equivalent in power to both forms of the relational calculus when they are restricted to safe expressions.

• The relational calculi are terse, formal languages that are inappropriate for casual users of a database system. These two formal languages form the basis for two more user-friendly languages, QBE and Datalog, that we cover in Appendix B.

**Review Terms**
• Relational algebra
• Relational-algebra operations
◦ Select 
◦ Project 
◦ Union ∪
◦ Set difference −
◦ Cartesian product ×
◦ Rename 
• Additional operations
◦ Set intersection ∩
◦ Natural join ✶
◦ Assignment operation
◦ Outer join
 Left outer join ✶
 Right outer join ✶
 Full outer join ✶
• Multisets
• Grouping
• Null value
• Tuple relational calculus
• Domain relational calculus
• Safety of expressions
• Expressive power of languages

**Practice Exercises**

**6.1** Write the following queries in relational algebra, using the university schema.

a. Find the titles of courses in the Comp. Sci. department that have 3 credits.

b. Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result.

c. Find the highest salary of any instructor.

d. Find all instructors earning the highest salary (there may be more than one with the same salary).  

employee (person name, street, city )
works (person name, company name, salary)
company (company name, city)
manages (person name, manager name)

**Figure 6.22** Relational database for Exercises 6.2, 6.8, 6.11, 6.13, and 6.15

e. Find the enrollment of each section that was offered in Autumn 2009.

f. Find the maximum enrollment, across all sections, in Autumn 2009.

g. Find the sections that had the maximum enrollment in Autumn 2009.

**6.2** Consider the relational database of Figure 6.22, where the primary keys are underlined. Give an expression in the relational algebra to express each of the following queries:

a. Find the names of all employees who live in the same city and on the same street as do their managers.

b. Find the names of all employees in this database who do not work for “First Bank Corporation”.

c. Find the names of all employees who earn more than every employee of “Small Bank Corporation”.

**6.3** The natural outer-join operations extend the natural-join operation so that tuples from the participating relations are not lost in the result of the join. Describe how the theta-join operation can be extended so that tuples from the left, right, or both relations are not lost from the result of a theta join.

**6.4** (**Division operation**): The division operator of relational algebra, “÷”, is defined as follows. Let _r_ (_R_) and _s_(_S_) be relations, and let _S_ ⊆ _R_; that is, every attribute of schema _S_ is also in schema _R_. Then _r_ ÷ _s_ is a relation on schema _R_ − _S_ (that is, on the schema containing all attributes of schema _R_ that are not in schema _S_). A tuple _t_ is in _r_ ÷ _s_ if and only if both of two conditions hold:

• _t_ is in _πR_−_S_(_r_ )

• For every tuple _ts_ in _s_, there is a tuple _tr_ in _r_ satisfying both of the following: a. _tr_ [_S_] = _ts_[_S_] b. _tr_ [_R_ − _S_] = _t_

Given the above definition:

a. Write a relational algebra expression using the division operator to find the IDs of all students who have taken all Comp. Sci. courses. (Hint: project _takes_ to just ID and _course id_, and generate the set of all Comp. Sci. _course id_s using a select expression, before doing the division.)

b. Show how to write the above query in relational algebra, without using division. (By doing so, you would have shown how to define the division operation using the other relational algebra operations.)

**6.5** Let the following relation schemas be given:

_R_ \= (_A, B, C_) _S_ \= (_D, E, F_ )

Let relations _r_(_R_) and _s_(_S_) be given. Give an expression in the tuple relational calculus that is equivalent to each of the following:

![Alt text](image-89.png)

**6.6** Let _R_ \= (_A, B, C_), and let _r_~1~ and _r_~2~ both be relations on schema _R_. Give an expression in the domain relational calculus that is equivalent to each of the following:

<<<<<<< HEAD
a. _A_(_r_~1~)

b. _B_ \= 17 (_r_~1~)

c. _r_~1~ ∪ _r_~2~

d. _r_~1~ ∩ _r_~2~

e. _r_~1~ − _r_~2~

f. _A,B_(_r_~1~) _B,C_ (_r_~2~)
=======
![Alt text](image-90.png)
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

**6.7** Let _R_ \= (_A, B_) and _S_ \= (_A, C_), and let _r_ (_R_) and _s_(_S_) be relations. Write expressions in relational algebra for each of the following queries:

![Alt text](image-91.png)

**6.8** Consider the relational database of Figure 6.22 where the primary keys are underlined. Give an expression in tuple relational calculus for each of the following queries:  

a. Find all employees who work directly for “Jones.”

b. Find all cities of residence of all employees who work directly for “Jones.”

c. Find the name of the manager of the manager of “Jones.”

d. Find those employees who earn more than all employees living in the city “Mumbai.”

**6.9** Describe how to translate join expressions in SQL to relational algebra.

