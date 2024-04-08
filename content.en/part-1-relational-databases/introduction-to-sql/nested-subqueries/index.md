---
title: Nested Subqueries
weight: 8
references:
    videos:
        - youtube:ffd-Mw7A9ew
        - youtube:lBpSMeQjNqQ
    links:
        - https://www.geeksforgeeks.org/nested-queries-in-sql/
        - https://learnsql.com/blog/sql-nested-select/
    books:
        - b1:
            title: Query Processing in Database Systems
            url: https://www.google.co.in/books/edition/Query_Processing_in_Database_Systems/AOGpCAAAQBAJ?hl=en&gbpv=0
            
        - b2:
            title: MySQL Reference Manual 
            url: https://www.google.co.in/books/edition/MySQL_Reference_Manual/9c-pkLaNmqoC?hl=en&gbpv=0
---
# Nested Subqueries

SQL provides a mechanism for nesting subqueries. A subquery is a **select**\-**from**\**where** expression that is nested within another query. A common use of subqueries is to perform tests for set membership, make set comparisons, and determine set cardinality, by nesting subqueries in the **where** clause. We study such uses of nested subqueries in the **where** clause in Sections 3.8.1 through 3.8.4. In Section 3.8.5, we study nesting of subqueries in the **from** clause. In Section 3.8.7, we see how a class of subqueries called scalar subqueries can appear wherever an expression returning a value can occur.

## Set Membership

SQL allows testing tuples for membership in a relation. The **in** connective tests for set membership, where the set is a collection of values produced by a **select** clause. The **not in** connective tests for the absence of set membership.

As an illustration, reconsider the query “Find all the courses taught in the both the Fall 2009 and Spring 2010 semesters.” Earlier, we wrote such a query by intersecting two sets: the set of courses taught in Fall 2009 and the set of courses taught in Spring 2010. We can take the alternative approach of finding all courses that were taught in Fall 2009 and that are also members of the set of courses taught in Spring 2010. Clearly, this formulation generates the same results as the previous one did, but it leads us to write our query using the **in** connective of SQL. We begin by finding all courses taught in Spring 2010, and we write the subquery

(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Spring’ **and** _year_\= 2010)

We then need to find those courses that were taught in the Fall 2009 and that appear in the set of courses obtained in the subquery. We do so by nesting the subquery in the **where** clause of an outer query. The resulting query is

**select distinct** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Fall’ **and** _year_\= 2009 **and**
_course id_ **in** (**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Spring’ **and** _year_\= 2010);  

This example shows that it is possible to write the same query several ways in SQL. This flexibility is beneficial, since it allows a user to think about the query in the way that seems most natural. We shall see that there is a substantial amount of redundancy in SQL.

We use the **not in** construct in a way similar to the **in** construct. For example, to find all the courses taught in the Fall 2009 semester but not in the Spring 2010 semester, we can write:

**select distinct** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Fall’ **and** _year_\= 2009 **and** _course id_ **not in** 
(**select** _course id_
**from** _section_ 
**where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The **in** and **not in** operators can also be used on enumerated sets. The following query selects the names of instructors whose names are neither “Mozart” nor “Einstein”.

**select distinct** _name_ 
**from** _instructor_ 
**where** _name_ **not in** (’Mozart’, ’Einstein’);

In the preceding examples, we tested membership in a one-attribute relation. It is also possible to test for membership in an arbitrary relation in SQL. For example, we can write the query “find the total number of (distinct) students who have taken course sections taught by the instructor with _ID_ 110011” as follows:

**select count** (**distinct** _ID_)
**from** _takes_ 
**where** (_course id_, _sec id_, _semester_, _year_) **in** (**select** _course id_, _sec id_, _semester_, _year_**from** _teaches_ **where** _teaches_._ID_\= 10101);

## Set Comparison

As an example of the ability of a nested subquery to compare sets, consider the query “Find the names of all instructors whose salary is greater than at least one instructor in the Biology department.” In Section 3.4.1, we wrote this query as follows:

**select distinct** _T_._name_ 
**from** _instructor_ **as** _T_, _instructor_ **as** _S_ 
**where** _T.salary > S.salary_ **and** _S.dept name_ \= ’Biology’;  

SQL does, however, offer an alternative style for writing the preceding query. The phrase “greater than at least one” is represented in SQL by _\>_ **some**. This construct allows us to rewrite the query in a form that resembles closely our formulation of the query in English.

**select** _name_ 
**from** _instructor_ 
**where** _salary >_ **some** (**select** _salary_

**from** _instructor_ **where** _dept name_ \= ’Biology’);

The subquery:

(**select** _salary_ 
**from** _instructor_ 
**where** _dept name_ \= ’Biology’)

generates the set of all salary values of all instructors in the Biology department. The _\>_ **some** comparison in the **where** clause of the outer **select** is true if the _salary_ value of the tuple is greater than at least one member of the set of all salary values for instructors in Biology.

SQL also allows _<_ **some**, _<_\= **some**, _\>_\= **some**, = **some**, and _<>_ **some** comparisons. As an exercise, verify that = **some** is identical to **in**, whereas _<>_ **some** is _not_ the same as **not in**.8

Now we modify our query slightly. Let us find the names of all instructors that have a salary value greater than that of each instructor in the Biology department. The construct _\>_ **all** corresponds to the phrase “greater than all.” Using this construct, we write the query as follows:

**select** _name_ **from** _instructor_ **where** _salary >_ **all** (**select** _salary_

**from** _instructor_ **where** _dept name_ \= ’Biology’);

As it does for **some**, SQL also allows _<_ **all**, _<_\= **all**, _\>_\= **all**, = **all**, and _<>_ **all** comparisons. As an exercise, verify that _<>_ **all** is identical to **not in**, whereas = **all** is _not_ the same as **in**.

As another example of set comparisons, consider the query “Find the departments that have the highest average salary.” We begin by writing a query to find all average salaries, and then nest it as a subquery of a larger query that finds

8The keyword **any** is synonymous to **some** in SQL. Early versions of SQL allowed only **any**. Later versions added the alternative **some** to avoid the linguistic ambiguity of the word _any_ in English.  

those departments for which the average salary is greater than or equal to all average salaries:

**select** _dept name_ **from** _instructor_ **group by** _dept name_ **having avg** (_salary_) _\>_\= **all** (**select avg** (_salary_)

**select** _dept name_ 
**from** _instructor_ 
**group by** _dept name_ 
**having avg** (_salary_) _\>_\= **all** (**select avg** (_salary_)
**from** _instructor_ **group by** _dept name_);

## Test for Empty Relations

SQL includes a feature for testing whether a subquery has any tuples in its result. The **exists** construct returns the value **true** if the argument subquery is nonempty. Using the **exists** construct, we can write the query “Find all courses taught in both the Fall 2009 semester and in the Spring 2010 semester” in still another way:

**select** _course id_ 
**from** _section_ **as** _S_ 
**where** _semester_ \= ’Fall’**and** _year_\= 2009 **and**
**exists** (**select** \* **from** _section_ **as** _T_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010 **and**_S_._course id_\= _T_._course id_);

The above query also illustrates a feature of SQL where a correlation name from an outer query (_S_ in the above query), can be used in a subquery in the **where** clause. A subquery that uses a correlation name from an outer query is called a **correlated subquery**.

In queries that contain subqueries, a scoping rule applies for correlation names. In a subquery, according to the rule, it is legal to use only correlation names defined in the subquery itself or in any query that contains the subquery. If a correlation name is defined both locally in a subquery and globally in a containing query, the local definition applies. This rule is analogous to the usual scoping rules used for variables in programming languages.

We can test for the nonexistence of tuples in a subquery by using the **not exists** construct. We can use the **not exists** construct to simulate the set containment (that is, superset) operation: We can write “relation _A_contains relation _B_” as “**not exists** (_B_ **except** _A_).” (Although it is not part of the current SQL standards, the **contains** operator was present in some early relational systems.) To illustrate the **not exists** operator, consider the query “Find all students who have taken all courses offered in the Biology department.” Using the **except** construct, we can write the query as follows:  

**select distinct** _S_._ID_, _S_._name_ 
**from** _student_ **as** _S_ 
**where not exists** ((**select** _course id_**from** _course_ **where** _dept name_ \= ’Biology’) **except** (**select** _T_._course id_ **from** _takes_ **as** _T_ **where** _S_._ID_ \= _T_._ID_));

Here, the subquery:

(**select** _course id_ 
**from** _course_ 
**where** _dept name_ \= ’Biology’)

finds the set of all courses offered in the Biology department. The subquery:

(**select** _T_._course id_ **from** _takes_ **as** _T_ **where** _S_._ID_ \= _T_._ID_)

finds all the courses that student _S_._ID_ has taken. Thus, the outer **select** takes each student and tests whether the set of all courses that the student has taken contains the set of all courses offered in the Biology department.

## Test for the Absence of Duplicate Tuples

SQL includes a boolean function for testing whether a subquery has duplicate tuples in its result. The **unique** construct9 returns the value **true** if the argument subquery contains no duplicate tuples. Using the **unique** construct, we can write the query “Find all courses that were offered at most once in 2009” as follows:

**select** _T_._course id_ 
**from** _course_ **as** _T_ 
**where unique** (**select** _R_._course id_
**from** _section_ **as** _R_ 
**where** _T_._course id_\= _R_._course id_ **and**_R_._year_ \= 2009);

Note that if a course is not offered in 2009, the subquery would return an empty result, and the **unique** predicate would evaluate to true on the empty set.

An equivalent version of the above query not using the **unique** construct is:

**select** _T_._course id_ 
**from** _course_ **as** _T_ 
**where** 1 _<_\= (**select count**(_R_._course id_)**from** _section_ **as** _R_ **where** _T_._course id_\= _R_._course id_ **and**_R_._year_ \= 2009);

We can test for the existence of duplicate tuples in a subquery by using the **not unique** construct. To illustrate this construct, consider the query “Find all courses that were offered at least twice in 2009” as follows:

**select** _T_._course id_ 
**from** _course_ **as** _T_ 
**where not unique** (**select** _R_._course id_**from** _section_ **as** _R_ **where** _T_._course id_\= _R_._course id_ **and**_R_._year_ \= 2009);

Formally, the **unique** test on a relation is defined to fail if and only if the relation contains two tuples _t_~1~ and _t_~2~  such that _t_~1~ = _t_~2~ \. Since the test _t_~1~ = _t_~2~  fails if any of the fields of _t_~1~ or _t_~2~  are null, it is possible for **unique** to be true even if there are multiple copies of a tuple, as long as at least one of the attributes of the tuple is null.

## Subqueries in the From Clause

SQL allows a subquery expression to be used in the **from** clause. The key concept applied here is that any **select**\-**from**\-**where** expression returns a relation as a result and, therefore, can be inserted into another **select**\-**from**\-**where** anywhere that a relation can appear.

Consider the query “Find the average instructors’ salaries of those departments where the average salary is greater than $42,000.” We wrote this query in Section 3.7 by using the **having** clause. We can now rewrite this query, without using the **having** clause, by using a subquery in the **from** clause, as follows:

**select** _dept name_, _avg salary_ 
**from** (**select** _dept name_, **avg** (_salary_) **as** _avg salary_
**from** _instructor_ **group by** _dept name_)
**where** _avg salary >_ 42000;

The subquery generates a relation consisting of the names of all departments and their corresponding average instructors’ salaries. The attributes of the subquery result can be used in the outer query, as can be seen in the above example.  

Note that we do not need to use the **having** clause, since the subquery in the **from** clause computes the average salary, and the predicate that was in the **having** clause earlier is now in the **where** clause of the outer query.

We can give the subquery result relation a name, and rename the attributes, using the **as** clause, as illustrated below.

**select** _dept name_, _avg salary_ 
**from** (**select** _dept name_, 
**avg** (_salary_)
**from** _instructor_ 
**group by** _dept name_) 
**as** _dept avg_ (_dept name_, _avg salary_)
**where** _avg salary >_ 42000;

The subquery result relation is named _dept avg_, with the attributes _dept name_ and _avg salary_.

Nested subqueries in the **from** clause are supported by most but not all SQL implementations. However, some SQL implementations, notably Oracle, do not support renaming of the result relation in the **from** clause.

As another example, suppose we wish to find the maximum across all departments of the total salary at each department. The **having** clause does not help us in this task, but we can write this query easily by using a subquery in the **from** clause, as follows:

**select max** (_tot salary_) 
**from** (**select** _dept name_, **sum**(_salary_)
**from** _instructor_ 
**group by** _dept name_) **as** _dept total_ (_dept name_, _tot salary_);

We note that nested subqueries in the **from** clause cannot use correlation variables from other relations in the **from** clause. However, SQL:2003 allows a subquery in the **from** clause that is prefixed by the **lateral** keyword to access attributes of preceding tables or subqueries in the **from** clause. For example, if we wish to print the names of each instructor, along with their salary and the average salary in their department, we could write the query as follows:

**select** _name_, _salary_, _avg salary_ 
**from** _instructor I1_, **lateral** (**select avg**(_salary_) as _avg salary_**from** _instructor I2_ **where** _I2_._dept name_\= _I1_._dept name_);

Without the **lateral** clause, the subquery cannot access the correlation variable _I1_ from the outer query. Currently, only a few SQL implementations, such as IBM DB2, support the **lateral** clause.  

## The with Clause

The **with** clause provides a way of defining a temporary relation whose definition is available only to the query in which the **with** clause occurs. Consider the following query, which finds those departments with the maximum budget.

**with** _max budget_ (_value_) **as** 
(**select max**(_budget_) 
**from** _department_)
**select** _budget_ 
**from** _department_, _max budget_ 
**where** _department_._budget_ \= _max budget.value_;

The **with** clause defines the temporary relation _max budget_, which is used in the immediately following query. The **with** clause, introduced in SQL:1999, is supported by many, but not all, database systems.

We could have written the above query by using a nested subquery in either the **from** clause or the **where** clause. However, using nested subqueries would have made the query harder to read and understand. The **with** clause makes the query logic clearer; it also permits a view definition to be used in multiple places within a query.

For example, suppose we want to find all departments where the total salary is greater than the average of the total salary at all departments. We can write the query using the **with** clause as follows.

**with** _dept total_ (_dept name_, _value_) **as** 
(**select** _dept name_, **sum**(_salary_) 
**from** _instructor_ 
**group by** _dept name_),
_dept total avg_(_value_) **as** (**select avg**(_value_) 
**from** _dept total_)
**select** _dept name_ 
**from** _dept total_, _dept total avg_ 
**where** _dept total.value >_\= _dept total avg.value_;

We can, of course, create an equivalent query without the **with** clause, but it would be more complicated and harder to understand. You can write the equivalent query as an exercise.

## Scalar Subqueries

SQL allows subqueries to occur wherever an expression returning a value is permitted, provided the subquery returns only one tuple containing a single attribute; such subqueries are called **scalar subqueries**. For example, a subquery can be used in the **select** clause as illustrated in the following example that lists all departments along with the number of instructors in each department:

**select** _dept name_, 
(**select count**(\*) **from** _instructor_ **where** _department_._dept name_ \= _instructor_._dept name_)
**as** _num instructors_ 
**from** _department_;

The subquery in the above example is guaranteed to return only a single value since it has a **count**(\*) aggregate without a **group by**. The example also illustrates the usage of correlation variables, that is, attributes of relations in the **from** clause of the outer query, such as _department_._dept name_ in the above example.

Scalar subqueries can occur in **select**, **where**, and **having** clauses. Scalar subqueries may also be defined without aggregates. It is not always possible to figure out at compile time if a subquery can return more than one tuple in its result; if the result has more than one tuple when the subquery is executed, a run-time error occurs.

Note that technically the type of a scalar subquery result is still a relation, even if it contains a single tuple. However, when a scalar subquery is used in an expression where a value is expected, SQL implicitly extracts the value from the single attribute of the single tuple in the relation, and returns that value.
