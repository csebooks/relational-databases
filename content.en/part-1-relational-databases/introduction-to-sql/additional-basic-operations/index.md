---
title:  Additional Basic Operations
weight: 4
references:
    videos:
        - youtubeRD9tMyFY_F0
        - youtube:rFCKrC2NWgI
    links:
        - https://upscfever.com/upsc-fever/en/gatecse/en-gatecse-chp34.html
        - https://padakuu.com/additional-basic-operations-the-rename-operation-385-article
    books:
        - b1:
            title: Building Intuition 
            url: https://www.google.co.in/books/edition/Building_Intuition/YkFMfY7qgBwC?hl=en&gbpv=0
        - b2:
            title: SQL Cookbook
            url: https://www.google.co.in/books/edition/SQL_Cookbook/2j-bAgAAQBAJ?hl=en&gbpv=0
---


# Additional Basic Operations

There are number of additional basic operations that are supported in SQL.

## The Rename Operation

Consider again the query that we used earlier:

**select** _name_, _course id_ 
**from** _instructor_, _teaches_ 
**where** _instructor_._ID_\= _teaches_._ID_;

The result of this query is a relation with the following attributes:

_name_, _course id_

The names of the attributes in the result are derived from the names of the attributes in the relations in the **from** clause.

We cannot, however, always derive names in this way, for several reasons: First, two relations in the **from** clause may have attributes with the same name, in which case an attribute name is duplicated in the result. Second, if we used an arithmetic expression in the **select** clause, the resultant attribute does not have a name. Third, even if an attribute name can be derived from the base relations as in the preceding example, we may want to change the attribute name in the result. Hence, SQL provides a way of renaming the attributes of a result relation. It uses the **as** clause, taking the form:

_old-name_ **as** _new-name_  

The **as** clause can appear in both the **select** and **from** clauses.4 For example, if we want the attribute name _name_ to be replaced with the name

_instructor name_, we can rewrite the preceding query as:

**select** _name_ **as** _instructor name_, _course id_ 
**from** _instructor_, _teaches_ 
**where** _instructor_._ID_\= _teaches_._ID_;

The **as** clause is particularly useful in renaming relations. One reason to rename a relation is to replace a long relation name with a shortened version that is more convenient to use elsewhere in the query. To illustrate, we rewrite the query “For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught.”

**select** _T_._name_, _S_._course id_ 
**from** _instructor_ **as** _T_, _teaches_ **as** _S_ 
**where** _T_._ID_\= _S_._ID_;

Another reason to rename a relation is a case where we wish to compare tuples in the same relation. We then need to take the Cartesian product of a relation with itself and, without renaming, it becomes impossible to distinguish one tuple from the other. Suppose that we want to write the query “Find the names of all instructors whose salary is greater than at least one instructor in the Biology department.” We can write the SQL expression:

**select distinct** _T_._name_ 
**from** _instructor_ **as** _T_, _instructor_ **as** _S_ 
**where** _T.salary > S.salary_ **and** _S.dept name_ \= ’Biology’;

Observe that we could not use the notation _instructor.salary_, since it would not be clear which reference to _instructor_ is intended.

In the above query, _T_ and _S_ can be thought of as copies of the relation _instructor_, but more precisely, they are declared as aliases, that is as alternative names, for the relation _instructor_. An identifier, such as _T_ and _S_, that is used to rename a relation is referred to as a **correlation name** in the SQL standard, but is also commonly referred to as a **table alias**, or a **correlation variable**, or a **tuple variable**.

Note that a better way to phrase the previous query in English would be “Find the names of all instructors who earn more than the lowest paid instructor in the Biology department.” Our original wording fits more closely with the SQL that we wrote, but the latter wording is more intuitive, and can in fact be expressed directly in SQL as we shall see in Section 3.8.2.

## String Operations

SQL specifies strings by enclosing them in single quotes, for example, ’Computer’. A single quote character that is part of a string can be specified by using two single quote characters; for example, the string “It’s right” can be specified by “It”s right”.

The SQL standard specifies that the equality operation on strings is case sensitive; as a result the expression “’comp. sci.’ = ’Comp. Sci.’” evaluates to false. However, some database systems, such as MySQL and SQL Server, do not distinguish uppercase from lowercase when matching strings; as a result “’comp. sci.’ = ’Comp. Sci.’” would evaluate to true on these databases. This default behavior can, however, be changed, either at the database level or at the level of specific attributes.

SQL also permits a variety of functions on character strings, such as concatenating (using “‖”), extracting substrings, finding the length of strings, converting strings to uppercase (using the function **upper**(_s_) where _s_ is a string) and lowercase (using the function **lower**(_s_)), removing spaces at the end of the string (using **trim**(_s_)) and so on. There are variations on the exact set of string functions supported by different database systems. See your database system’s manual for more details on exactly what string functions it supports.

Pattern matching can be performed on strings, using the operator **like**. We describe patterns by using two special characters:

• Percent (%): The % character matches any substring.

• Underscore ( ): The character matches any character.

Patterns are case sensitive; that is, uppercase characters do not match lowercase characters, or vice versa. To illustrate pattern matching, we consider the following examples:

• ’Intro%’ matches any string beginning with “Intro”.

• ’%Comp%’ matches any string containing “Comp” as a substring, for example, ’Intro. to Computer Science’, and ’Computational Biology’.

• ’ ’ matches any string of exactly three characters.

• ’ %’ matches any string of at least three characters.

SQL expresses patterns by using the **like** comparison operator. Consider the query “Find the names of all departments whose building name includes the substring ‘Watson’.” This query can be written as:

**select** _dept name_ 
**from** _department_ 
**where** _building_ **like** ’%Watson%’;  

For patterns to include the special pattern characters (that is, % and ), SQL allows the specification of an escape character. The escape character is used immediately before a special pattern character to indicate that the special pattern character is to be treated like a normal character. We define the escape character for a **like** comparison using the **escape** keyword. To illustrate, consider the following patterns, which use a backslash (\\) as the escape character:

• **like** ’ab\\%cd%’ **escape** ’\\’ matches all strings beginning with “ab%cd”.

• **like** ’ab\\\\cd%’ **escape** ’\\’ matches all strings beginning with “ab\\cd”.

SQL allows us to search for mismatches instead of matches by using the **not like** comparison operator. Some databases provide variants of the **like** operation which do not distinguish lower and upper case.

SQL:1999 also offers a **similar to** operation, which provides more powerful pattern matching than the **like** operation; the syntax for specifying patterns is similar to that used in Unix regular expressions.

## Attribute Specification in Select Clause

The asterisk symbol “ \* ” can be used in the **select** clause to denote “all attributes.” Thus, the use of _instructor_.\* in the **select** clause of the query:

**select** _instructor_.\* 
**from** _instructor_, _teaches_ 
**where** _instructor_._ID_\= _teaches_._ID_;

indicates that all attributes of _instructor_ are to be selected. A **select** clause of the form **select** \* indicates that all attributes of the result relation of the **from** clause are selected.

## Ordering the Display of Tuples

SQL offers the user some control over the order in which tuples in a relation are displayed. The **order by** clause causes the tuples in the result of a query to appear in sorted order. To list in alphabetic order all instructors in the Physics department, we write:

**select** _name_ 
**from** _instructor_ 
**where** _dept name_ \= ’Physics’
**order by** _name_;

By default, the **order by** clause lists items in ascending order. To specify the sort order, we may specify **desc** for descending order or **asc** for ascending order. Furthermore, ordering can be performed on multiple attributes. Suppose that we wish to list the entire _instructor_ relation in descending order of _salary_. If several instructors have the same salary, we order them in ascending order by name. We express this query in SQL as follows:

**select** \* 
**from** _instructor_ 
**order by** _salary_ **desc**, _name_ **asc**;

## Where Clause Predicates

SQL includes a **between** comparison operator to simplify **where** clauses that specify that a value be less than or equal to some value and greater than or equal to some other value. If we wish to find the names of instructors with salary amounts between $90,000 and $100,000, we can use the **between** comparison to write:

**select** _name_ 
**from** _instructor_ 
**where** _salary_ **between** 90000 **and** 100000;

instead of:

**select** _name_ 
**from** _instructor_ 
**where** _salary <_\= 100000 *and** _salary >_\= 90000;

Similarly, we can use the **not between** comparison operator. We can extend the preceding query that finds instructor names along with

course identifiers, which we saw earlier, and consider a more complicated case in which we require also that the instructors be from the Biology department: “Find the instructor names and the courses they taught for all instructors in the Biology department who have taught some course.” To write this query, we can modify either of the SQL queries we saw earlier, by adding an extra condition in the **where** clause. We show below the modified form of the SQL query that does not use natural join.

**select** _name_, _course id_
**from** _instructor_, _teaches_ 
**where** _instructor_._ID_\= _teaches_._ID_ **and** _dept name_ \= ’Biology’;

SQL permits us to use the notation (v1, v2, . . . , vn) to denote a tuple of arity n containing values v1, v2, . . . , vn. The comparison operators can be used on tuples, and the ordering is defined lexicographically. For example, (a1, a2) <\= (b1, b2)  

![Alt text](image-20.png)

**Figure 3.9** The _c1_ relation, listing courses taught in Fall 2009.

is true if _a_1 _<_\= _b_1 **and** _a_2 _<_\= _b_2; similarly, the two tuples are equal if all their attributes are equal. Thus, the preceding SQL query can be rewritten as follows:5

**select** _name_, _course id_ 
**from** _instructor_, _teaches_
**where** (_instructor_._ID_, _dept name_) = (_teaches_._ID_, ’Biology’);

