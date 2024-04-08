---
title: Basic Structure of SQL Queries
weight: 3
references:
    videos:
        - youtube:aLMcHQKU5ww
        - youtube:aLMcHQKU5ww
    links:
        - https://beingintelligent.com/basic-structure-of-sql-queries.html
        - https://padakuu.com/basic-structure-of-sql-queries-372-article
    books:
        - b1:
            title: Learning SQL
            url: https://www.google.co.in/books/edition/Learning_SQL/FwKHDwAAQBAJ?hl=en&gbpv=0
        - b2:
            title:  SQL Queries for Mere Mortals 
            url: https://www.google.co.in/books/edition/SQL_Queries_for_Mere_Mortals/EtrHAwAAQBAJ?hl=en&gbpv=0
 
---

# Basic Structure of SQL Queries

The basic structure of an SQL query consists of three clauses: **select**, **from**, and **where**. The query takes as its input the relations listed in the **from** clause, operates on them as specified in the **where** and **select** clauses, and then produces a relation as the result. We introduce the SQL syntax through examples, and describe the general structure of SQL queries later.

## Queries on a Single Relation

Let us consider a simple query using our university example, “Find the names of all instructors.” Instructor names are found in the _instructor_ relation, so we 

| name |
| :------: |
| Srinivasan |
| Wu |
| Mozart |
| Einstein | 
| El Said |
| Gold |
| Katz |
| Califieri |
| Singh |
| Crick | 
| Brandt |
| Kim | 

**Figure 3.2** Result of “**select** _name_ **from** _instructor_”.

put that relation in the **from** clause. The instructor’s name appears in the _name_ attribute, so we put that in the **select** clause.

**select** _name_ 
**from** _instructor_;

The result is a relation consisting of a single attribute with the heading _name_. If the _instructor_ relation is as shown in Figure 2.1, then the relation that results from the preceding query is shown in Figure 3.2.

Now consider another query, “Find the department names of all instructors,” which can be written as:

**select** _dept name_ 
**from** _instructor_;

Since more than one instructor can belong to a department, a department name could appear more than once in the _instructor_ relation. The result of the above query is a relation containing the department names, shown in Figure 3.3.

In the formal, mathematical definition of the relational model, a relation is a set. Thus, duplicate tuples would never appear in relations. In practice, duplicate elimination is time-consuming. Therefore, SQL allows duplicates in relations as well as in the results of SQL expressions. Thus, the preceding SQL query lists each department name once for every tuple in which it appears in the _instructor_ relation.

In those cases where we want to force the elimination of duplicates, we insert the keyword **distinct** after **select**. We can rewrite the preceding query as:

**select distinct** _dept name_
**from** _instructor_;  

| dept_name |
| :------: |
| Comp. Sci. |
| Finance|
| Music |
| Physics |
| History |
| Physics |
| Comp. Sci. |
| History |
| Finance |
| Biology |
| Comp. Sci. |
| Elec. Eng. |

**Figure 3.3** Result of “**select** _dept name_ **from** _instructor_”.

if we want duplicates removed. The result of the above query would contain each department name at most once.

SQL allows us to use the keyword **all** to specify explicitly that duplicates are not removed:

**select all** _dept name_ 
**from** _instructor_;

Since duplicate retention is the default, we shall not use **all** in our examples. To ensure the elimination of duplicates in the results of our example queries, we shall use **distinct** whenever it is necessary.

The **select** clause may also contain arithmetic expressions involving the operators +, −, ∗, and / operating on constants or attributes of tuples. For example, the query:

**select** _ID_, _name_, _dept name_, _salary_ \* 1.1 
**from** _instructor_;

returns a relation that is the same as the _instructor_ relation, except that the attribute _salary_ is multiplied by 1.1. This shows what would result if we gave a 10% raise to each instructor; note, however, that it does not result in any change to the _instructor_ relation.

SQL also provides special data types, such as various forms of the _date_ type, and allows several arithmetic functions to operate on these types. We discuss this further in Section 4.5.1.

The **where** clause allows us to select only those rows in the result relation of the **from** clause that satisfy a specified predicate. Consider the query “Find the names of all instructors in the Computer Science department who have salary greater than $70,000.” This query can be written in SQL as:  


| name |
| :------: |
| Katz |
| Brandt |

**Figure 3.4** Result of “Find the names of all instructors in the Computer Science department who have salary greater than $70,000.”

**select** _name_ 
**from** _instructor_ 
**where** _dept name_ \= ’Comp. Sci.’ **and** _salary >_ 70000;

If the _instructor_ relation is as shown in Figure 2.1, then the relation that results from the preceding query is shown in Figure 3.4.

SQL allows the use of the logical connectives **and**, **or**, and **not** in the **where** clause. The operands of the logical connectives can be expressions involving the comparison operators _<_, _<_\=, _\>_, _\>_\=, =, and _<>_. SQL allows us to use the comparison operators to compare strings and arithmetic expressions, as well as special types, such as date types.

We shall explore other features of **where** clause predicates later in this chapter.

## Queries on Multiple Relations

So far our example queries were on a single relation. Queries often need to access information from multiple relations. We now study how to write such queries.

An an example, suppose we want to answer the query “Retrieve the names of all instructors, along with their department names and department building name.”

Looking at the schema of the relation _instructor_, we realize that we can get the department name from the attribute _dept name_, but the department building name is present in the attribute _building_ of the relation _department_. To answer the query, each tuple in the _instructor_ relation must be matched with the tuple in the _department_ relation whose _dept name_ value matches the _dept name_ value of the _instructor_ tuple.

In SQL, to answer the above query, we list the relations that need to be accessed in the **from** clause, and specify the matching condition in the **where** clause. The above query can be written in SQL as

**select** _name_, _instructor_._dept name_, _building_ 
**from** _instructor_, _department_
**where** _instructor_._dept name_\=_department_._dept name_;

If the _instructor_ and _department_ relations are as shown in Figures 2.1 and 2.5 respectively, then the result of this query is shown in Figure 3.5.

Note that the attribute _dept name_ occurs in both the relations _instructor_ and _department_, and the relation name is used as a prefix (in _instructor_._dept name_, and  

| name | dept_name | building |
| :------: | :------: | :----: |
| Srinivasan | Comp. Sci. | Taylor |
| Wu | Finance | Painter |
| Mozart | Music | Packard |
| Einstein | Physics | Watson |
| El Said | History | Painter |
| Gold | Physics | Watson |
| Katz | Comp. Sci. | Taylor |
| Califieri | History | Painter |
| Singh | Finance | Painter |
| Crick | Biology | Watson |
| Brandt | Comp. Sci. | Taylor |
| Kim | Elec. Eng. | Taylor |

**Figure 3.5** The result of “Retrieve the names of all instructors, along with their department names and department building name.”

_department_._dept name_) to make clear to which attribute we are referring. In contrast, the attributes _name_ and _building_ appear in only one of the relations, and therefore do not need to be prefixed by the relation name.

This naming convention _requires_ that the relations that are present in the **from** clause have distinct names. This requirement causes problems in some cases, such as when information from two different tuples in the same relation needs to be combined. In Section 3.4.1, we see how to avoid these problems by using the rename operation.

We now consider the general case of SQL queries involving multiple relations. As we have seen earlier, an SQL query can contain three types of clauses, the **select** clause, the **from** clause, and the **where** clause. The role of each clause is as follows:

• The **select** clause is used to list the attributes desired in the result of a query.

• The **from** clause is a list of the relations to be accessed in the evaluation of the query.

• The **where** clause is a predicate involving attributes of the relation in the **from** clause.

A typical SQL query has the form

**select** _A_1_, A_2_, . . . , An_ **from** _r_~1~_, r_2_, . . . , rm_ **where** _P_;


Each _Ai_ represents an attribute, and each _ri_ a relation. _P_ is a predicate. If the **where** clause is omitted, the predicate _P_ is **true**.  

Although the clauses must be written in the order **select**, **from**, **where**, the easiest way to understand the operations specified by the query is to consider the clauses in operational order: first **from**, then **where**, and then **select**.1

The **from** clause by itself defines a Cartesian product of the relations listed in the clause. It is defined formally in terms of set theory, but is perhaps best understood as an iterative process that generates tuples for the result relation of the **from** clause.

**for each** tuple _t_~1~ **in** relation _r_~1~ **for each** tuple _t_~2~  **in** relation _r_~2~

_. . ._

**for each** tuple _tm_ **in** relation _rm_ Concatenate _t_~1~_, t_2_, . . . , tm_ into a single tuple _t_ Add _t_ into the result relation


The result relation has all attributes from all the relations in the **from** clause. Since the same attribute name may appear in both _ri_ and _r j_ , as we saw earlier, we prefix the the name of the relation from which the attribute originally came, before the attribute name.

For example, the relation schema for the Cartesian product of relations _instructor_ and _teaches_ is:

(_instructor_._ID_, _instructor_._name_, _instructor_._dept name_, _instructor_._salary teaches_._ID_, _teaches_._course id_, _teaches_._sec id_, _teaches_._semester_, _teaches_._year_)

With this schema, we can distinguish _instructor_._ID_ from _teaches_._ID_. For those attributes that appear in only one of the two schemas, we shall usually drop the relation-name prefix. This simplification does not lead to any ambiguity. We can then write the relation schema as:

(_instructor_._ID_, _name_, _dept name_, _salary teaches_._ID_, _course id_, _sec id_, _semester_, _year_)

To illustrate, consider the _instructor_ relation in Figure 2.1 and the _teaches_ relation in Figure 2.7. Their Cartesian product is shown in Figure 3.6, which includes only a portion of the tuples that make up the Cartesian product result.2

The Cartesian product by itself combines tuples from _instructor_ and _teaches_ that are unrelated to each other. Each tuple in _instructor_ is combined with _every_ tuple in _teaches_, even those that refer to a different instructor. The result can be an extremely large relation, and it rarely makes sense to create such a Cartesian product.

![Alt text](image-17.png)

Instead, the predicate in the **where** clause is used to restrict the combinations created by the Cartesian product to those that are meaningful for the desired answer. We would expect a query involving _instructor_ and _teaches_ to combine a particular tuple _t_ in _instructor_ with only those tuples in _teaches_ that refer to the same instructor to which _t_ refers. That is, we wish only to match _teaches_ tuples with _instructor_ tuples that have the same _ID_ value. The following SQL query ensures this condition, and outputs the instructor name and course identifiers from such matching tuples.

**select** _name_, _course id_ 
**from** _instructor_, _teaches_ 
**where** _instructor_._ID_\= _teaches_._ID_;

Note that the above query outputs only instructors who have taught some course. Instructors who have not taught any course are not output; if we wish to output such tuples, we could use an operation called the _outer join_, which is described in Section 4.1.2.

If the _instructor_ relation is as shown in Figure 2.1 and the _teaches_ relation is as shown in Figure 2.7, then the relation that results from the preceding query is shown in Figure 3.7. Observe that instructors Gold, Califieri, and Singh, who have not taught any course, do not appear in the above result.

If we only wished to find instructor names and course identifiers for instructors in the Computer Science department, we could add an extra predicate to the **where** clause, as shown below.

**select** _name_, _course id_ 
**from** _instructor_, _teaches_ 
**where** _instructor_._ID_\= _teaches_._ID_ **and** _instructor_._dept name_ \= ’Comp. Sci.’;

Note that since the _dept name_ attribute occurs only in the _instructor_ relation, we could have used just _dept name_, instead of _instructor_._dept name_ in the above query.

In general, the meaning of an SQL query can be understood as follows:

![Alt text](image-18.png)

**Figure 3.7** Result of “For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught.”

**1.** Generate a Cartesian product of the relations listed in the **from** clause

**2.** Apply the predicates specified in the **where** clause on the result of Step 1.

**3.** For each tuple in the result of Step 2, output the attributes (or results of expressions) specified in the **select** clause.

The above sequence of steps helps make clear what the result of an SQL query should be, _not_ how it should be executed. A real implementation of SQL would not execute the query in this fashion; it would instead optimize evaluation by generating (as far as possible) only elements of the Cartesian product that satisfy the **where** clause predicates. We study such implementation techniques later, in Chapters 12 and 13.

When writing queries, you should be careful to include appropriate **where** clause conditions. If you omit the **where** clause condition in the preceding SQL query, it would output the Cartesian product, which could be a huge relation. For the example _instructor_ relation in Figure 2.1 and the example _teaches_ relation in Figure 2.7, their Cartesian product has 12 ∗ 13 = 156 tuples — more than we can show in the text! To make matters worse, suppose we have a more realistic number of instructors than we show in our sample relations in the figures, say 200 instructors. Let’s assume each instructor teaches 3 courses, so we have 600 tuples in the _teaches_ relation. Then the above iterative process generates 200 ∗ 600 = 120,000 tuples in the result.

## The Natural Join

In our example query that combined information from the _instructor_ and _teaches_ table, the matching condition required _instructor_._ID_ to be equal to _teaches_._ID_. These are the only attributes in the two relations that have the same name. In fact this is a common case; that is, the matching condition in the **from** clause most often requires all attributes with matching names to be equated.

To make the life of an SQL programmer easier for this common case, SQL supports an operation called the _natural join_, which we describe below. In fact SQL supports several other ways in which information from two or more relations can be **joined** together. We have already seen how a Cartesian product along with a **where** clause predicate can be used to join information from multiple relations. Other ways of joining information from multiple relations are discussed in Section 4.1.

The **natural join** operation operates on two relations and produces a relation as the result. Unlike the Cartesian product of two relations, which concatenates each tuple of the first relation with every tuple of the second, natural join considers only those pairs of tuples with the same value on those attributes that appear in the schemas of both relations. So, going back to the example of the relations _instructor_ and _teaches_, computing _instructor_ **natural join** _teaches_ considers only those pairs of tuples where both the tuple from _instructor_ and the tuple from _teaches_ have the same value on the common attribute, _ID_.  

![Alt text](image-19.png)

The result relation, shown in Figure 3.8, has only 13 tuples, the ones that give information about an instructor and a course that that instructor actually teaches. Notice that we do not repeat those attributes that appear in the schemas of both relations; rather they appear only once. Notice also the order in which the attributes are listed: first the attributes common to the schemas of both relations, second those attributes unique to the schema of the first relation, and finally, those attributes unique to the schema of the second relation.

Consider the query “For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught”, which we wrote earlier as:

**select** _name_, _course id_ 
**from** _instructor_, _teaches_ 
**where** _instructor_._ID_\= _teaches_._ID_;

This query can be written more concisely using the natural-join operation in SQL as:

**select** _name_, _course id_ 
**from** _instructor_ **natural join** _teaches_;

Both of the above queries generate the same result. As we saw earlier, the result of the natural join operation is a relation. Concep-

tually, expression “_instructor_ **natural join** _teaches_” in the **from** clause is replaced by the relation obtained by evaluating the natural join.3 The **where** and **select** clauses are then evaluated on this relation, as we saw earlier in Section 3.3.2.

A **from** clause in an SQL query can have multiple relations combined using natural join, as shown here:

**select** _A_1_, A_2_, . . . , An_ **from** _r_~1~ **natural join** _r_~2~ **natural join** . . . **natural join** _rm_ **where** _P_;


More generally, a **from** clause can be of the form

**from** _E_1, _E_2, . . . , _En_

where each _Ei_ can be a single relation or an expression involving natural joins. For example, suppose we wish to answer the query “List the names of instructors along with the the titles of courses that they teach.” The query can be written in SQL as follows:

**select** _name_, _title_ 
**from** _instructor_ **natural join** _teaches_, _course_ 
**where** _teaches_._course id_\= _course_._course id_;

The natural join of _instructor_ and _teaches_ is first computed, as we saw earlier, and a Cartesian product of this result with _course_ is computed, from which the **where** clause extracts only those tuples where the course identifier from the join result matches the course identifier from the _course_ relation. Note that _teaches_._course id_ in the **where** clause refers to the _course id_ field of the natural join result, since this field in turn came from the _teaches_ relation.

In contrast the following SQL query does _not_ compute the same result:

**select** _name_, _title_ **from** _instructor_ **natural join** _teaches_ **natural join** _course_;

To see why, note that the natural join of _instructor_ and _teaches_ contains the attributes (_ID_, _name_, _dept name_, _salary_, _course id_, _sec id_), while the _course_ relation contains the attributes (_course id_, _title_, _dept name_, _credits_). As a result, the natural join of these two would require that the _dept name_ attribute values from the two inputs be the same, in addition to requiring that the _course id_ values be the same. This query would then omit all (instructor name, course title) pairs where the instructor teaches a course in a department other than the instructor’s own department. The previous query, on the other hand, correctly outputs such pairs.


3As a consequence, it is not possible to use attribute names containing the original relation names, for instance _instructor_._name_or _teaches_._course id_, to refer to attributes in the natural join result; we can, however, use attribute names such as _name_ and _course id_, without the relation names.  


To provide the benefit of natural join while avoiding the danger of equating attributes erroneously, SQL provides a form of the natural join construct that allows you to specify exactly which columns should be equated. This feature is illustrated by the following query:

**select** _name_, _title_ 
**from** (_instructor_ **natural join** _teaches_) **join** _course_ **using** (_course id_);

The operation **join** _. . ._ **using** requires a list of attribute names to be specified. Both inputs must have attributes with the specified names. Consider the operation _r_~1~ **join** _r_~2~ **using**(_A_1_, A_2). The operation is similar to _r_~1~ **natural join** _r_~2~, except that a pair of tuples _t_~1~ from _r_~1~ and _t_~2~  from _r_~2~ match if _t_~1~_.A_1 = _t_~2~ _.A_1 and _t_~1~_.A_2 = _t_~2~ _.A_2; even if _r_~1~ and _r_~2~ both have an attribute named _A_3, it is _not_ required that _t_~1~_.A_3 = _t_~2~ _.A_3.

Thus, in the preceding SQL query, the **join** construct permits _teaches_._dept name_ and _course_._dept name_ to differ, and the SQL query gives the correct answer.

