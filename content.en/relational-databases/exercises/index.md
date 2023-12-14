---
title: Exercises
weight: 14
---

Exercises## Exercises

**2.9** Consider the bank database of Figure 2.15.

a. What are the appropriate primary keys?

b. Given your choice of primary keys, identify appropriate foreign keys.

**2.10** Consider the _advisor_ relation shown in Figure 2.8, with _s id_ as the primary key of _advisor_. Suppose a student can have more than one advisor. Then, would _s id_ still be a primary key of the _advisor_ relation? If not, what should the primary key of _advisor_ be?

**2.11** Describe the differences in meaning between the terms _relation_ and _relation schema_. 

**2.12** Consider the relational database of Figure 2.14. Give an expression in the relational algebra to express each of the following queries:

a. Find the names of all employees who work for “First Bank Corporation”.

b. Find the names and cities of residence of all employees who work for “First Bank Corporation”.

c. Find the names, street address, and cities of residence of all employees who work for “First Bank Corporation” and earn more than $10,000.

**2.13** Consider the bank database of Figure 2.15. Give an expression in the relational algebra for each of the following queries:

a. Find all loan numbers with a loan value greater than $10,000.

b. Find the names of all depositors who have an account with a value greater than $6,000.

c. Find the names of all depositors who have an account with a value greater than $6,000 at the “Uptown” branch.

**2.14** List two reasons why null values might be introduced into the database.

**2.15** Discuss the relative merits of procedural and nonprocedural languages.

## Exercises

**4.12** For the database of Figure 4.11, write a query to find those employees with no manager. Note that an employee may simply have no manager listed or may have a _null_ manager. Write your query using an outer join and then write it again using no outer join at all.

**4.13** Under what circumstances would the query

**select** \* 
**from** _student_ **natural full outer join** _takes_ **natural full outer join** _course_

include tuples with null values for the _title_ attribute?

**4.14** Show how to define a view _tot credits_ (_year, num credits_), giving the total number of credits taken by students in each year.

**4.15** Show how to express the **coalesce** operation from Exercise 4.10 using the **case** operation.

**4.16** Referential-integrity constraints as defined in this chapter involve exactly two relations. Consider a database that includes the relations shown in Figure 4.12. Suppose that we wish to require that every name that appears in _address_ appears in either _salaried worker_ or _hourly worker_, but not necessarily in both.

a. Propose a syntax for expressing such constraints.

b. Discuss the actions that the system must take to enforce a constraint of this form.

**4.17** Explain why, when a manager, say Satoshi, grants an authorization, the grant should be done by the manager role, rather than by the user Satoshi.  

**4.18** Suppose user _A_, who has all authorizations on a relation _r_ , grants select on relation _r_ to **public** with grant option. Suppose user _B_ then grants select on _r_ to _A_. Does this cause a cycle in the authorization graph? Explain why.

**4.19** Database systems that store each relation in a separate operating-system file may use the operating system’s authorization scheme, instead of defining a special scheme themselves. Discuss an advantage and a disadvantage of such an approach.

**Bibliographical Notes**

See the bibliographic notes of Chapter 3 for SQL reference material. The rules used by SQL to determine the updatability of a view, and how

updates are reflected on the underlying database relations, are defined by the SQL:1999 standard, and are summarized in Melton and Simon [2001].  

# C H A P T E R 5 Advanced SQL

In Chapters 3 and 4, we provided detailed coverage of the basic structure of SQL. In this chapter, we cover some of the more advanced features of SQL.1 We address the issue of how to access SQL from a general-purpose programming language, which is very important for building applications that use a database to store and retrieve data. We describe how procedural code can be executed within the database, either by extending the SQL language to support procedural actions, or by allowing functions defined in procedural languages to be executed within the database. We describe triggers, which can be used to specify actions that are to be carried out automatically on certain events such as insertion, deletion, or update of tuples in a specified relation. We discuss recursive queries and advanced aggregation features supported by SQL. Finally, we describe online analytic processing (OLAP) systems, which support interactive analysis of very large datasets.

## Exercises

**5.12** Consider the following relations for a company database:

• _emp_ (_ename_, _dname_, _salary_)

• _mgr_ (_ename_, _mname_)

and the Java code in Figure 5.26, which uses the JDBC API. Assume that the userid, password, machine name, etc. are all okay. Describe in concise English what the Java program does. (That is, produce an English sentence like “It finds the manager of the toy department,” not a line-by-line description of what each Java statement does.)

**5.13** Suppose you were asked to define a class MetaDisplay in Java, containing a method static void printTable(String r); the method takes a relation name _r_ as input, executes the query “**select** \* **from** _r_”, and prints the result out in nice tabular format, with the attribute names displayed in the header of the table.  

```
import java.sql.*;
public class Mystery {
public static void main(String[] args) {
try {
Connection con=null;
Class.forName("oracle.jdbc.driver.OracleDriver");
con=DriverManager.getConnection(
"jdbc:oracle:thin:star/X@//edgar.cse.lehigh.edu:1521/XE");
Statement s=con.createStatement();
String q;
String empName = "dog";
boolean more;
ResultSet result;
do {
q = "select mname from mgr where ename = ’" + empName + "’";
result = s.executeQuery(q);
more = result.next();
if (more) {
empName = result.getString("mname");
System.out.println (empName);
}
} while (more);
s.close();
con.close();
} catch(Exception e){e.printStackTrace();} }}
```

**Figure 5.26** Java code for Exercise 5.12.

a. What do you need to know about relation _r_ to be able to print the result in the specified tabular format.

b. What JDBC methods(s) can get you the required information?

c. Write the method printTable(String r) using the JDBC API.

**5.14** Repeat Exercise 5.13 using ODBC, defining void printTable(char \*r) as a function instead of a method.

**5.15** Consider an employee database with two relations

_employee_ (_employee name_, _street_, _city_) 
_works_ (_employee name_, _company name_, _salary_)

where the primary keys are underlined. Write a query to find companies whose employees earn a higher salary, on average, than the average salary at “First Bank Corporation”.  

a. Using SQL functions as appropriate.

b. Without using SQL functions.

**5.16** Rewrite the query in Section 5.2.1 that returns the name and budget of all departments with more than 12 instructors, using the **with** clause instead of using a function call.

**5.17** Compare the use of embedded SQL with the use in SQL of functions defined in a general-purpose programming language. Under what circumstances would you use each of these features?

**5.18** Modify the recursive query in Figure 5.15 to define a relation

_prereq depth_(_course id_, _prereq id_, _depth_)

where the attribute _depth_ indicates how many levels of intermediate prerequisites are there between the course and the prerequisite. Direct prerequisites have a depth of 0.

**5.19** Consider the relational schema

_part_(_part id_, _name_, _cost_) 
_subpart_(_part id_, _subpart id_, _count_)

A tuple (_p_1_, p_2_,_ 3) in the _subpart_ relation denotes that the part with part-id _p_2 is a direct subpart of the part with part-id _p_1, and _p_1 has 3 copies of _p_2\. Note that _p_2 may itself have further subparts. Write a recursive SQL query that outputs the names of all subparts of the part with part-id “P-100”.

**5.20** Consider again the relational schema from Exercise 5.19. Write a JDBC function using non-recursive SQL to find the total cost of part “P-100”, including the costs of all its subparts. Be sure to take into account the fact that a part may have multiple occurrences of a subpart. You may use recursion in Java if you wish.

**5.21** Suppose there are two relations _r_ and _s_, such that the foreign key _B_ of _r_ references the primary key _A_of _s_. Describe how the trigger mechanism can be used to implement the **on delete cascade** option, when a tuple is deleted from _s_.

**5.22** The execution of a trigger can cause another action to be triggered. Most database systems place a limit on how deep the nesting can be. Explain why they might place such a limit.

**5.23** Consider the relation, _r_ , shown in Figure 5.27. Give the result of the following query:  

![Alt text](image-48.png)

**Figure 5.27** The relation _r_ for Exercise 5.23.

**select** _building_, _room number_, _time slot id_, **count**(\*) 
**from** r 
**group by rollup** (_building_, _room number_, _time slot id_)

**5.24** For each of the SQL aggregate functions **sum, count, min**, and **max**, show how to compute the aggregate value on a multiset _S_1 ∪ _S_2, given the aggregate values on multisets _S_1 and _S_2.

On the basis of the above, give expressions to compute aggregate values with grouping on a subset _S_ of the attributes of a relation _r_ (_A, B, C, D, E_), given aggregate values for grouping on attributes _T_ ⊇ _S_, for the following aggregate functions:

a. **sum, count, min,** and **max**

b. **avg**

c. Standard deviation

**5.25** In Section 5.5.1, we used the _student grades_ view of Exercise 4.5 to write a query to find the rank of each student based on grade-point average. Modify that query to show only the top 10 students (that is, those students whose rank is 1 through 10).

**5.26** Give an example of a pair of groupings that cannot be expressed by using a single **group by** clause with **cube** and **rollup**.

**5.27** Given relation _s_(_a , b, c_), show how to use the extended SQL features to generate a histogram of _c_ versus _a_ , dividing _a_ into 20 equal-sized partitions (that is, where each partition contains 5 percent of the tuples in _s_, sorted by _a_ ).

**5.28** Consider the bank database of Figure 5.25 and the _balance_ attribute of the _account_ relation. Write an SQL query to compute a histogram of _balance_ values, dividing the range 0 to the maximum account balance present, into three equal ranges.  

## Exercises

**6.10** Write the following queries in relational algebra, using the university schema.

a. Find the names of all students who have taken at least one Comp. Sci. course.

b. Find the IDs and names of all students who have not taken any course offering before Spring 2009.

c. For each department, find the maximum salary of instructors in that department. You may assume that every department has at least one instructor.

d. Find the lowest, across all departments, of the per-department maximum salary computed by the preceding query.

**6.11** Consider the relational database of Figure 6.22, where the primary keys are underlined. Give an expression in the relational algebra to express each of the following queries:

a. Find the names of all employees who work for “First Bank Corporation”.

b. Find the names and cities of residence of all employees who work for “First Bank Corporation”.

c. Find the names, street addresses, and cities of residence of all employees who work for “First Bank Corporation” and earn more than $10,000.

d. Find the names of all employees in this database who live in the same city as the company for which they work.

e. Assume the companies may be located in several cities. Find all companies located in every city in which “Small Bank Corporation” is located.  

**Exercises 253**

**6.12** Using the university example, write relational-algebra queries to find the course sections taught by more than one instructor in the following ways:

a. Using an aggregate function.

b. Without using any aggregate functions.

**6.13** Consider the relational database of Figure 6.22. Give a relational-algebra expression for each of the following queries:

a. Find the company with the most employees.

b. Find the company with the smallest payroll.

c. Find those companies whose employees earn a higher salary, on average, than the average salary at First Bank Corporation.

**6.14** Consider the following relational schema for a library:

member(memb no, name, dob)
books(isbn, title, authors, publisher)
borrowed(memb no, isbn, date)

Write the following queries in relational algebra.

a. Find the names of members who have borrowed any book published by “McGraw-Hill”.

b. Find the name of members who have borrowed all books published by “McGraw-Hill”.

c. Find the name and membership number of members who have borrowed more than five different books published by “McGraw-Hill”.

d. For each publisher, find the name and membership number of members who have borrowed more than five books of that publisher.

e. Find the average number of books borrowed per member. Take into account that if an member does not borrow any books, then that member does not appear in the _borrowed_ relation at all.

**6.15** Consider the employee database of Figure 6.22. Give expressions in tuple relational calculus and domain relational calculus for each of the following queries:

a. Find the names of all employees who work for “First Bank Corporation”.

b. Find the names and cities of residence of all employees who work for “First Bank Corporation”.

c. Find the names, street addresses, and cities of residence of all employees who work for “First Bank Corporation” and earn more than $10,000.  

d. Find all employees who live in the same city as that in which the company for which they work is located.

e. Find all employees who live in the same city and on the same street as their managers.

f. Find all employees in the database who do not work for “First Bank Corporation”.

g. Find all employees who earn more than every employee of “Small Bank Corporation”.

h. Assume that the companies may be located in several cities. Find all companies located in every city in which “Small Bank Corporation” is located.

**6.16** Let _R_ \= (_A, B_) and _S_ \= (_A, C_), and let _r_ (_R_) and _s_(_S_) be relations. Write relational-algebra expressions equivalent to the following domainrelational-calculus expressions:

a. {_< a >_ | ∃ _b_ (_< a , b >_ ∈ _r_ ∧ _b_ \= 17)} 
b. {_< a , b, c >_ | _< a , b >_ ∈ _r_ ∧ _< a , c >_ ∈ _s_} 
c. {_< a >_ | ∃ _b_ (_< a , b >_ ∈ _r_ ) ∨ ∀ _c_ (∃ _d_ (_< d, c >_ ∈ _s_) ⇒ _< a , c >_ ∈ _s_)} 
d. {_< a >_ | ∃ _c_ (_< a , c >_ ∈ _s_ ∧ ∃ _b_1_, b_2 (_< a , b_1 _\>_ ∈ _r_ ∧ _< c, b_2 _\>_

∈ _r_ ∧ _b_1 _\> b_2))} **6.17** Repeat Exercise 6.16, writing SQL queries instead of relational-algebra ex-

pressions.

**6.18** Let _R_ \= (_A, B_) and _S_ \= (_A, C_), and let _r_ (_R_) and _s_(_S_) be relations. Using the special constant _null_, write tuple-relational-calculus expressions equivalent to each of the following:

![Alt text](image-92.png)

**6.19** Give a tuple-relational-calculus expression to find the maximum value in relation _r_ (_A_).

