---
title: Practice Exercises
weight: 24
---

Practice Exercises## Practice Exercises

**3.1** Write the following queries in SQL, using the university schema. (We suggest you actually run these queries on a database, using the sample data that we provide on the Web site of the book, db-book.com. Instructions for setting up a database, and loading sample data, are provided on the above Web site.)

a. Find the titles of courses in the Comp. Sci. department that have 3 credits.

b. Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result.

c. Find the highest salary of any instructor.

d. Find all instructors earning the highest salary (there may be more than one with the same salary).

e. Find the enrollment of each section that was offered in Autumn 2009.

f. Find the maximum enrollment, across all sections, in Autumn 2009.

g. Find the sections that had the maximum enrollment in Autumn 2009.  

person (driver id, name, address)
car (license, model, year)
accident (report number, date, location)
owns (driver id, license)
participated (report number, license, driver id, damage amount)

**Figure 3.18** Insurance database for Exercises 3.4 and 3.14.

**3.2** Suppose you are given a relation _grade points_(_grade_, _points_), which provides a conversion from letter grades in the _takes_ relation to numeric scores; for example an “A” grade could be specified to correspond to 4 points, an “A−” to 3.7 points, a “B+” to 3.3 points, a “B” to 3 points, and so on. The grade points earned by a student for a course offering (section) is defined as the number of credits for the course multiplied by the numeric points for the grade that the student received.

Given the above relation, and our university schema, write each of the following queries in SQL. You can assume for simplicity that no _takes_ tuple has the _null_ value for _grade_.

a. Find the total grade-points earned by the student with ID 12345, across all courses taken by the student.

b. Find the grade-point average (_GPA_) for the above student, that is, the total grade-points divided by the total credits for the associated courses.

c. Find the ID and the grade-point average of every student.

**3.3** Write the following inserts, deletes or updates in SQL, using the university schema.

a. Increase the salary of each instructor in the Comp. Sci. department by 10%.

b. Delete all courses that have never been offered (that is, do not occur in the _section_ relation).

c. Insert every student whose _tot cred_ attribute is greater than 100 as an instructor in the same department, with a salary of $10,000.

**3.4** Consider the insurance database of Figure 3.18, where the primary keys are underlined. Construct the following SQL queries for this relational database.

a. Find the total number of people who owned cars that were involved in accidents in 2009.

b. Add a new accident to the database; assume any values for required attributes.

c. Delete the Mazda belonging to “John Smith”.  

branch(branch name, branch city, assets)
customer (customer name, customer street, customer city)
loan (loan number, branch name, amount)
borrower (customer name, loan number)
account (account number, branch name, balance )
depositor (customer name, account number)


**Figure 3.19** Banking database for Exercises 3.8 and 3.15.

**3.5** Suppose that we have a relation _marks_(ID, _score_) and we wish to assign grades to students based on the score as follows: grade _F_ if _score_ < 40, grade _C_ if 40 ≤ _score_ < 60, grade _B_ if 60 ≤ _score_ < 80, and grade _A_ if 80 ≤ _score_. Write SQL queries to do the following:

a. Display the grade for each student, based on the _marks_ relation.

b. Find the number of students with each grade.

**3.6** The SQL **like** operator is case sensitive, but the lower() function on strings can be used to perform case insensitive matching. To show how, write a query that finds departments whose names contain the string “sci” as a substring, regardless of the case.

**3.7** Consider the SQL query

<<<<<<< HEAD
**select distinct** _p.a_1 **from** _p_, _r_~1~, _r_~2~ **where** _p.a_1 = _r_~1~._a_1 **or** _p.a_1 = _r_~2~._a_1
=======
**select distinct** _p.a_1 
**from** _p_, _r_1, _r_2 
**where** _p.a_1 = _r_1._a_1 **or** _p.a_1 = _r_2._a_1
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

Under what conditions does the preceding query select values of _p.a_1 that are either in _r_~1~ or in _r_~2~? Examine carefully the cases where one of _r_~1~ or _r_~2~ may be empty.

**3.8** Consider the bank database of Figure 3.19, where the primary keys are underlined. Construct the following SQL queries for this relational database.

a. Find all customers of the bank who have an account but not a loan.

b. Find the names of all customers who live on the same street and in the same city as “Smith”.

c. Find the names of all branches with customers who have an account in the bank and who live in “Harrison”.

**3.9** Consider the employee database of Figure 3.20, where the primary keys are underlined. Give an expression in SQL for each of the following queries.

a. Find the names and cities of residence of all employees who work for “First Bank Corporation”.  

branch(branch name, branch city, assets)
customer (customer name, customer street, customer city)
loan (loan number, branch name, amount)
borrower (customer name, loan number)
account (account number, branch name, balance )
depositor (customer name, account number)


**Figure 3.10** Employee database for Exercises 3.9, 3.10, 3.16, 3.17, and 3.20.

b. Find the names, street addresses, and cities of residence of all employees who work for “First Bank Corporation” and earn more than $10,000.

c. Find all employees in the database who do not work for “First Bank Corporation”.

d. Find all employees in the database who earn more than each employee of “Small Bank Corporation”.

e. Assume that the companies may be located in several cities. Find all companies located in every city in which “Small Bank Corporation” is located.

f. Find the company that has the most employees.

g. Find those companies whose employees earn a higher salary, on average, than the average salary at “First Bank Corporation”.

**3.10** Consider the relational database of Figure 3.20. Give an expression in SQL for each of the following queries.

a. Modify the database so that “Jones” now lives in “Newtown”.

b. Give all managers of “First Bank Corporation” a 10 percent raise unless the salary becomes greater than $100,000; in such cases, give only a 3 percent raise.

**3.11** Write the following queries in SQL, using the university schema.

a. Find the names of all students who have taken at least one Comp. Sci. course; make sure there are no duplicate names in the result.

b. Find the IDs and names of all students who have not taken any course offering before Spring 2009.

c. For each department, find the maximum salary of instructors in that department. You may assume that every department has at least one instructor.

d. Find the lowest, across all departments, of the per-department maximum salary computed by the preceding query.  

**3.12** Write the following queries in SQL, using the university schema.

a. Create a new course “CS-001”, titled “Weekly Seminar”, with 0 credits.

b. Create a section of this course in Autumn 2009, with _sec id_ of 1.

c. Enroll every student in the Comp. Sci. department in the above section.

d. Delete enrollments in the above section where the student’s name is Chavez.

e. Delete the course CS-001. What will happen if you run this delete statement without first deleting offerings (sections) of this course.

f. Delete all _takes_ tuples corresponding to any section of any course with the word “database” as a part of the title; ignore case when matching the word with the title.

**3.13** Write SQL DDL corresponding to the schema in Figure 3.18. Make any reasonable assumptions about data types, and be sure to declare primary and foreign keys.

**3.14** Consider the insurance database of Figure 3.18, where the primary keys are underlined. Construct the following SQL queries for this relational database.

a. Find the number of accidents in which the cars belonging to “John Smith” were involved.

b. Update the damage amount for the car with the license number “AABB2000” in the accident with report number “AR2197” to $3000.

**3.15** Consider the bank database of Figure 3.19, where the primary keys are underlined. Construct the following SQL queries for this relational database.

a. Find all customers who have an account at _all_ the branches located in “Brooklyn”.

b. Find out the total sum of all loan amounts in the bank.

c. Find the names of all branches that have assets greater than those of at least one branch located in “Brooklyn”.

**3.16** Consider the employee database of Figure 3.20, where the primary keys are underlined. Give an expression in SQL for each of the following queries.

a. Find the names of all employees who work for “First Bank Corporation”.

b. Find all employees in the database who live in the same cities as the companies for which they work.

c. Find all employees in the database who live in the same cities and on the same streets as do their managers.  

d. Find all employees who earn more than the average salary of all employees of their company.

e. Find the company that has the smallest payroll.

**3.17** Consider the relational database of Figure 3.20. Give an expression in SQL for each of the following queries.

a. Give all employees of “First Bank Corporation” a 10 percent raise.

b. Give all managers of “First Bank Corporation” a 10 percent raise.

c. Delete all tuples in the _works_ relation for employees of “Small Bank Corporation”.

**3.18** List two reasons why null values might be introduced into the database.

**3.19** Show that, in SQL, _<>_ **all** is identical to **not in**.

**3.20** Give an SQL schema definition for the employee database of Figure 3.20. Choose an appropriate domain for each attribute and an appropriate primary key for each relation schema.

**3.21** Consider the library database of Figure 3.21. Write the following queries in SQL.

a. Print the names of members who have borrowed any book published by “McGraw-Hill”.

b. Print the names of members who have borrowed all books published by “McGraw-Hill”.

c. For each publisher, print the names of members who have borrowed more than five books of that publisher.

d. Print the average number of books borrowed per member. Take into account that if an member does not borrow any books, then that member does not appear in the _borrowed_ relation at all.

**3.22** Rewrite the **where** clause
**where unique** (**select** _title_ **from** _course_)

without using the **unique** construct.

member(memb no, name, age)
book(isbn, title, authors, publisher)
borrowed(memb no, isbn, date)

**Figure 3.21** Library database for Exercise 3.21. 

**3.23** Consider the query:

**select** _course id_, _semester_, _year_, _sec id_, **avg** (_tot cred_) 
**from** _takes_ **natural join** _student_ 
**where** _year_ \= 2009 
**group by** _course id_, _semester_, _year_, _sec id_ 
**having count** (_ID_) _\>_\= 2;

Explain why joining _section_ as well in the **from** clause would not change the result.

**3.24** Consider the query:

**with** _dept total_ (_dept name_, _value_) **as** 
(**select** _dept name_, **sum**(_salary_) 
**from** _instructor_ 
**group by** _dept name_),
_dept total avg_(_value_) **as**
(**select avg**(_value_) 
**from** _dept total_)
**select** _dept name_ 
**from** _dept total_, _dept total avg_ 
**where** _dept total.value >_\= _dept total avg.value_;

Rewrite this query without using the **with** construct.

## Practice Exercises

**4.1** Write the following queries in SQL:

a. Display a list of all instructors, showing their ID, name, and the number of sections that they have taught. Make sure to show the number of sections as 0 for instructors who have not taught any section. Your query should use an outerjoin, and should not use scalar subqueries.

b. Write the same query as above, but using a scalar subquery, without outerjoin.

c. Display the list of all course sections offered in Spring 2010, along with the names of the instructors teaching the section. If a section has more than one instructor, it should appear as many times in the result as it has instructors. If it does not have any instructor, it should still appear in the result with the instructor name set to “—”.

d. Display the list of all departments, with the total number of instructors in each department, without using scalar subqueries. Make sure to correctly handle departments with no instructors.

**4.2** Outer join expressions can be computed in SQL without using the SQL **outer join** operation. To illustrate this fact, show how to rewrite each of the following SQL queries without using the **outer join** expression.

a. **select**\* **from** _student_ **natural left outer join** _takes_

b. **select**\* **from** _student_ **natural full outer join** _takes_  

**Practice Exercises 153**

**4.3** Suppose we have three relations _r_ (_A_, _B_), _s_(_B_, _C_), and _t_(_B_, _D_), with all attributes declared as **not null**. Consider the expressions

• _r_ **natural left outer join** (_s_ **natural left outer join** _t_), and

• (_r_ **natural left outer join** _s_) **natural left outer join** _t_

a. Give instances of relations _r_ , _s_ and _t_ such that in the result of the second expression, attribute _C_ has a null value but attribute _D_ has a non-null value.

b. Is the above pattern, with _C_ null and _D_ not null possible in the result of the first expression? Explain why or why not.

**4.4 Testing SQL queries**: To test if a query specified in English has been correctly written in SQL, the SQL query is typically executed on multiple test databases, and a human checks if the SQL query result on each test database matches the intention of the specification in English.

a. In Section 3.3.3 we saw an example of an erroneous SQL query which was intended to find which courses had been taught by each instructor; the query computed the natural join of _instructor_, _teaches_, and _course_, and as a result unintentionally equated the _dept name_ attribute of _instructor_ and _course_. Give an example of a dataset that would help catch this particular error.

b. When creating test databases, it is important to create tuples in referenced relations that do not have any matching tuple in the referencing relation, for each foreign key. Explain why, using an example query on the university database.

c. When creating test databases, it is important to create tuples with null values for foreign key attributes, provided the attribute is nullable (SQL allows foreign key attributes to take on null values, as long as they are not part of the primary key, and have not been declared as **not null**). Explain why, using an example query on the university database.

_Hint_: use the queries from Exercise 4.1.

**4.5** Show how to define the view _student grades_ (_ID, GPA_) giving the gradepoint average of each student, based on the query in Exercise 3.2; recall that we used a relation _grade points_(_grade_, _points_) to get the numeric points associated with a letter grade. Make sure your view definition correctly handles the case of _null_ values for the _grade_ attribute of the _takes_ relation.

**4.6** Complete the SQL DDL definition of the university database of Figure 4.8 to include the relations _student_, _takes_, _advisor_, and _prereq_.  

employee (employee name, street, city)
works (employee name, company name, salary)
company (company name, city)
manages (employee name, manager name)

**Figure 4.11** Employee database for Figure 4.7 and 4.12.

**4.7** Consider the relational database of Figure 4.11. Give an SQL DDL definition of this database. Identify referential-integrity constraints that should hold, and include them in the DDL definition.

**4.8** As discussed in Section 4.4.7, we expect the constraint “an instructor cannot teach sections in two different classrooms in a semester in the same time slot” to hold.

a. Write an SQL query that returns all (_instructor_, _section_) combinations that violate this constraint.

b. Write an SQL assertion to enforce this constraint (as discussed in Section 4.4.7, current generation database systems do not support such assertions, although they are part of the SQL standard).

**4.9** SQL allows a foreign-key dependency to refer to the same relation, as in the following example:

**create table** _manager_ 
(_employee name_ **varchar**(20) **not null** 
_manager name_ **varchar**(20) **not null**, 
**primary key** _employee name_, 
**foreign key** (_manager name_) **references** _manager_**on delete cascade** )

Here, _employee name_ is a key to the table _manager_, meaning that each employee has at most one manager. The foreign-key clause requires that every manager also be an employee. Explain exactly what happens when a tuple in the relation _manager_ is deleted.

**4.10** SQL provides an n\-ary operation called **coalesce**, which is defined as follows: **coalesce**(A1, A2, . . . , An) returns the first nonnull Ai in the list A1, A2, . . . , An, and returns null if all of A1, A2, . . . , An are null.

Let a and b be relations with the schemas A(name, address, title), and B(name, address, salary), respectively. Show how to express a **natural full outer join** b using the **full outer-join** operation with an **on** condition and the **coalesce** operation. Make sure that the result relation does not contain two copies of the attributes name and address, and that the solution is correct even if some tuples in a and b have null values for attributes name or address.  

_salaried worker_ (_name, office, phone, salary_) 
_hourly worker_ (_name, hourly wage_) 
_address_ (_name, street, city_)

**Figure 4.12** Employee database for Exercise 4.16.

**4.11** Some researchers have proposed the concept of _marked_ nulls. A marked null ⊥_i_ is equal to itself, but if _i_ = _j_ , then ⊥_i_ = ⊥ _j_ . One application of marked nulls is to allow certain updates through views. Consider the view _instructor info_ (Section 4.2). Show how you can use marked nulls to allow the insertion of the tuple (99999, “Johnson”, “Music”) through _instructor info_.

## Practice Exercises

**5.1** Describe the circumstances in which you would choose to use embedded SQL rather than SQL alone or only a general-purpose programming language.

**5.2** Write a Java function using JDBC metadata features that takes a ResultSet as an input parameter, and prints out the result in tabular form, with appropriate names as column headings.

**5.3** Write a Java function using JDBC metadata features that prints a list of all relations in the database, displaying for each relation the names and types of its attributes.

**5.4** Show how to enforce the constraint “an instructor cannot teach in two different classrooms in a semester in the same time slot.” using a trigger (remember that the constraint can be violated by changes to the _teaches_ relation as well as to the _section_ relation).

**5.5** Write triggers to enforce the referential integrity constraint from _section_ to _time slot_, on updates to _section_, and _time slot_. Note that the ones we wrote in Figure 5.8 do not cover the **update** operation.

**5.6** To maintain the _tot cred_ attribute of the _student_ relation, carry out the following:

a. Modify the trigger on updates of _takes_, to handle all updates that can affect the value of _tot cred_.

b. Write a trigger to handle inserts to the _takes_ relation.

c. Under what assumptions is it reasonable not to create triggers on the _course_ relation?

**5.7** Consider the bank database of Figure 5.25. Let us define a view _branch cust_ as follows:

**create view** _branch cust_ **as** **select** _branch name, customer name_ 
**from** _depositor, account_ 
**where** _depositor.account number_ \= _account.account number_  
branch(branch name, branch city, assets)
customer (customer name, customer street, cust omer city)
loan (loan number, branch name, amount)
borrower (customer name, loan number)
account (account number, branch name, balance )
depositor (customer name, account number)

**Figure 5.25** Banking database for Exercises 5.7, 5.8, and 5.28 .

Suppose that the view is _materialized_; that is, the view is computed and stored. Write triggers to _maintain_ the view, that is, to keep it up-to-date on insertions to and deletions from _depositor_ or _account_. Do not bother about updates.

**5.8** Consider the bank database of Figure 5.25. Write an SQL trigger to carry out the following action: On **delete** of an account, for each owner of the account, check if the owner has any remaining accounts, and if she does not, delete her from the _depositor_ relation.

**5.9** Show how to express **group by cube**(_a , b, c, d_) using **rollup**; your answer should have only one **group by** clause.

**5.10** Given a relation _S_(_student, sub ject, marks_), write a query to find the top _n_ students by total marks, by using ranking.

**5.11** Consider the _sales_ relation from Section 5.6. Write an SQL query to compute the cube operation on the relation, giving the relation in Figure 5.21. Do not use the **cube** construct.

