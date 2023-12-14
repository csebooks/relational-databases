---
title: 4.1 Join Expressions
weight: 26
---

4.1 Join Expressions## 4.1 Join Expressions

In Section 3.3.3, we introduced the **natural join** operation. SQL provides other forms of the join operation, including the ability to specify an explicit **join predicate**, and the ability to include in the result tuples that are excluded by **natural join**. We shall discuss these forms of join in this section.

The examples in this section involve the two relations _student_ and _takes_, shown in Figures 4.1 and 4.2, respectively. Observe that the attribute _grade_ has a value null for the student with _ID_ 98988, for the course BIO-301, section 1, taken in Summer 2010. The null value indicates that the grade has not been awarded yet.


![Alt text](image-29.png)

**Figure 4.1** The _student_ relation.

<<<<<<< HEAD
**113**  

**114 Chapter 4 Intermediate SQL**

_ID course id sec id semester year grade_

00128 CS-101 1 Fall 2009 A 00128 CS-347 1 Fall 2009 A12345 CS-101 1 Fall 2009 C 12345 CS-190 2 Spring 2009 A 12345 CS-315 1 Spring 2010 A 12345 CS-347 1 Fall 2009 A 19991 HIS-351 1 Spring 2010 B 23121 FIN-201 1 Spring 2010 C+ 44553 PHY-101 1 Fall 2009 B45678 CS-101 1 Fall 2009 F 45678 CS-101 1 Spring 2010 B+ 45678 CS-319 1 Spring 2010 B 54321 CS-101 1 Fall 2009 A54321 CS-190 2 Spring 2009 B+ 55739 MU-199 1 Spring 2010 A76543 CS-101 1 Fall 2009 A 76543 CS-319 2 Spring 2010 A 76653 EE-181 1 Spring 2009 C 98765 CS-101 1 Fall 2009 C98765 CS-315 1 Spring 2010 B 98988 BIO-101 1 Summer 2009 A 98988 BIO-301 1 Summer 2010 _null_
=======
![Alt text](image-30.png)
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

**Figure 4.2** The _takes_ relation.

### 4.1.1 Join Conditions

In Section 3.3.3, we saw how to express natural joins, and we saw the **join** _. . ._

**using** clause, which is a form of natural join that only requires values to match on specified attributes. SQL supports another form of join, in which an arbitrary join condition can be specified.

The **on** condition allows a general predicate over the relations being joined. This predicate is written like a **where** clause predicate except for the use of the keyword **on** rather than **where**. Like the **using** condition, the **on** condition appears at the end of the join expression.

Consider the following query, which has a join expression containing the **on** condition.

**select** \* 
**from** _student_ **join** _takes_ **on** _student_._ID_\= _takes_._ID_;

The **on** condition above specifies that a tuple from _student_ matches a tuple from _takes_ if their _ID_ values are equal. The join expression in this case is almost the same as the join expression _student_ **natural join** _takes_, since the natural join operation also requires that for a _student_ tuple and a _takes_ tuple to match. The one difference is that the result has the _ID_ attribute listed twice, in the join result, once for _student_ and once for _takes_, even though their _ID_ values must be the same.

In fact, the above query is equivalent to the following query (in other words, they generate exactly the same results):

**select** \* 
**from** _student_, _takes_ 
**where** _student_._ID_\= _takes_._ID_;

As we have seen earlier, the relation name is used to disambiguate the attribute name _ID_, and thus the two occurrences can be referred to as _student_._ID_ and _takes_._ID_ respectively. A version of this query that displays the _ID_ value only once is as follows:

**select** _student_._ID_ **as** _ID_, _name_, _dept name_, _tot cred_, _course id_, _sec id_, _semester_, _year_, _grade_
**from** _student_ **join** _takes_ **on** _student_._ID_\= _takes_._ID_;

The result of the above query is shown in Figure 4.3. The **on** condition can express any SQL predicate, and thus a join expressions

using the **on** condition can express a richer class of join conditions than **natural join**. However, as illustrated by our preceding example, a query using a join expression with an **on** condition can be replaced by an equivalent expression without the **on** condition, with the predicate in the **on** clause moved to the **where** clause. Thus, it may appear that the **on** condition is a redundant feature of SQL.

However, there are two good reasons for introducing the **on** condition. First, we shall see shortly that for a kind of join called an outer join, **on** conditions do behave in a manner different from **where** conditions. Second, an SQL query is often more readable by humans if the join condition is specified in the **on** clause and the rest of the conditions appear in the **where** clause.

### 4.1.2 Outer Joins

Suppose we wish to display a list of all students, displaying their _ID_, and _name_, _dept name_, and _tot cred_, along with the courses that they have taken. The following SQL query may appear to retrieve the required information:

**select** \* 
**from** _student_ **natural join** _takes_;

Unfortunately, the above query does not work quite as intended. Suppose that there is some student who takes no courses. Then the tuple in the _student_ relation for that particular student would not satisfy the condition of a natural join with any tuple in the _takes_ relation, and that student’s data would not appear in the result. We would thus not see any information about students who have not taken  

<<<<<<< HEAD
**116 Chapter 4 Intermediate SQL**

_ID name dept name tot cred course id sec id semester year grade_ 00128 Zhang Comp. Sci. 102 CS-101 1 Fall 2009 A 00128 Zhang Comp. Sci. 102 CS-347 1 Fall 2009 A12345 Shankar Comp. Sci. 32 CS-101 1 Fall 2009 C 12345 Shankar Comp. Sci. 32 CS-190 2 Spring 2009 A 12345 Shankar Comp. Sci. 32 CS-315 1 Spring 2010 A 12345 Shankar Comp. Sci. 32 CS-347 1 Fall 2009 A 19991 Brandt History 80 HIS-351 1 Spring 2010 B 23121 Chavez Finance 110 FIN-201 1 Spring 2010 C+ 44553 Peltier Physics 56 PHY-101 1 Fall 2009 B45678 Levy Physics 46 CS-101 1 Fall 2009 F 45678 Levy Physics 46 CS-101 1 Spring 2010 B+ 45678 Levy Physics 46 CS-319 1 Spring 2010 B 54321 Williams Comp. Sci. 54 CS-101 1 Fall 2009 A54321 Williams Comp. Sci. 54 CS-190 2 Spring 2009 B+ 55739 Sanchez Music 38 MU-199 1 Spring 2010 A76543 Brown Comp. Sci. 58 CS-101 1 Fall 2009 A 76543 Brown Comp. Sci. 58 CS-319 2 Spring 2010 A 76653 Aoi Elec. Eng. 60 EE-181 1 Spring 2009 C 98765 Bourikas Elec. Eng. 98 CS-101 1 Fall 2009 C98765 Bourikas Elec. Eng. 98 CS-315 1 Spring 2010 B 98988 Tanaka Biology 120 BIO-101 1 Summer 2009 A 98988 Tanaka Biology 120 BIO-301 1 Summer 2010 _null_
=======
![Alt text](image-31.png)
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

**Figure 4.3** The result of _student_ **join** _takes_ **on** _student_._ID_\= _takes_._ID_ with second occurrence of _ID_ omitted.

any courses. For example, in the _student_ and _takes_ relations of Figures 4.1 and 4.2, note that student Snow, with ID 70557, has not taken any courses. Snow appears in _student_, but Snow’s ID number does not appear in the _ID_ column of _takes_. Thus, Snow does not appear in the result of the natural join.

More generally, some tuples in either or both of the relations being joined may be “lost” in this way. The **outer join** operation works in a manner similar to the join operations we have already studied, but preserve those tuples that would be lost in a join, by creating tuples in the result containing null values.

For example, to ensure that the student named Snow from our earlier example appears in the result, a tuple could be added to the join result with all attributes from the _student_ relation set to the corresponding values for the student Snow, and all the remaining attributes which come from the _takes_ relation, namely _course id_, _sec id_, _semester_, and _year_, set to _null_. Thus the tuple for the student Snow is

preserved in the result of the outer join. There are in fact three forms of outer join:

• The **left outer join** preserves tuples only in the relation named before (to the left of) the **left outer join** operation.  

The **right outer join** preserves tuples only in the relation named after (to the right of) the **right outer join** operation.

• The **full outer join** preserves tuples in both relations.

In contrast, the join operations we studied earlier that do not preserve nonmatched tuples are called **inner join** operations, to distinguish them from the outer-join operations.

We now explain exactly how each form of outer join operates. We can compute the left outer-join operation as follows. First, compute the result of the inner join as before. Then, for every tuple _t_ in the left-hand-side relation that does not match any tuple in the right-hand-side relation in the inner join, add a tuple _r_ to the result of the join constructed as follows:

• The attributes of tuple _r_ that are derived from the left-hand-side relation are filled in with the values from tuple _t_.

• The remaining attributes of _r_ are filled with null values.

Figure 4.4 shows the result of:

**select** \* 
**from** _student_ **natural left outer join** _takes_;

That result includes student Snow (_ID_ 70557), unlike the result of an inner join, but the tuple for Snow includes nulls for the attributes that appear only in the schema of the _takes_ relation.

As another example of the use of the outer-join operation, we can write the query “Find all students who have not taken a course” as:

**select** _ID_
**from** _student_ **natural left outer join** _takes_ 
**where** _course id_ **is** _null_;

The **right outer join** is symmetric to the **left outer join**. Tuples from the righthand-side relation that do not match any tuple in the left-hand-side relation are padded with nulls and are added to the result of the right outer join. Thus, if we rewrite our above query using a right outer join and swapping the order in which we list the relations as follows:

**select** \* 
**from** _takes_ **natural right outer join** _student_;

we get the same result except for the order in which the attributes appear in the result (see Figure 4.5).

The **full outer join** is a combination of the left and right outer-join types. After the operation computes the result of the inner join, it extends with nulls those tuples from the left-hand-side relation that did not match with any from the  

<<<<<<< HEAD
**118 Chapter 4 Intermediate SQL**

_ID name dept name tot cred course id sec id semester year grade_ 00128 Zhang Comp. Sci. 102 CS-101 1 Fall 2009 A 00128 Zhang Comp. Sci. 102 CS-347 1 Fall 2009 A12345 Shankar Comp. Sci. 32 CS-101 1 Fall 2009 C 12345 Shankar Comp. Sci. 32 CS-190 2 Spring 2009 A 12345 Shankar Comp. Sci. 32 CS-315 1 Spring 2010 A 12345 Shankar Comp. Sci. 32 CS-347 1 Fall 2009 A 19991 Brandt History 80 HIS-351 1 Spring 2010 B 23121 Chavez Finance 110 FIN-201 1 Spring 2010 C+ 44553 Peltier Physics 56 PHY-101 1 Fall 2009 B45678 Levy Physics 46 CS-101 1 Fall 2009 F 45678 Levy Physics 46 CS-101 1 Spring 2010 B+ 45678 Levy Physics 46 CS-319 1 Spring 2010 B 54321 Williams Comp. Sci. 54 CS-101 1 Fall 2009 A54321 Williams Comp. Sci. 54 CS-190 2 Spring 2009 B+ 55739 Sanchez Music 38 MU-199 1 Spring 2010 A70557 Snow Physics 0 _null null null null null_ 76543 Brown Comp. Sci. 58 CS-101 1 Fall 2009 A 76543 Brown Comp. Sci. 58 CS-319 2 Spring 2010 A 76653 Aoi Elec. Eng. 60 EE-181 1 Spring 2009 C 98765 Bourikas Elec. Eng. 98 CS-101 1 Fall 2009 C- 98765 Bourikas Elec. Eng. 98 CS-315 1 Spring 2010 B 98988 Tanaka Biology 120 BIO-101 1 Summer 2009 A 98988 Tanaka Biology 120 BIO-301 1 Summer 2010 _null_
=======
![Alt text](image-32.png)
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

**Figure 4.4** Result of _student_ **natural left outer join** _takes_.

right-hand side relation, and adds them to the result. Similarly, it extends with nulls those tuples from the right-hand-side relation that did not match with any tuples from the left-hand-side relation and adds them to the result.

As an example of the use of full outer join, consider the following query: “Display a list of all students in the Comp. Sci. department, along with the course sections, if any, that they have taken in Spring 2009; all course sections from Spring 2009 must be displayed, even if no student from the Comp. Sci. department has taken the course section.” This query can be written as:

**select** \* 
**from** (**select** \*
**from** _student_ 
**where** _dept name_\= ’Comp. Sci’)
**natural full outer join** (**select** \* 
**from** _takes_ 
**where** _semester_ \= ’Spring’ **and** _year_ \= 2009);  

![Alt text](image-33.png)

**Figure 4.5** The result of _takes_ **natural right outer join** _student_.

The **on** clause can be used with outer joins. The following query is identical to the first query we saw using “_student_ **natural left outer join** _takes_,” except that the attribute _ID_ appears twice in the result.

**select** \* 
**from** _student_ **left outer join** _takes_ **on** _student_._ID_\= _takes_._ID_;

As we noted earlier, **on** and **where** behave differently for outer join. The reason for this is that outer join adds null-padded tuples only for those tuples that do not contribute to the result of the corresponding inner join. The **on** condition is part of the outer join specification, but a **where** clause is not. In our example, the case of the _student_ tuple for student “Snow” with ID 70557, illustrates this distinction. Suppose we modify the preceding query by moving the **on** clause predicate to the **where** clause, and instead using an **on** condition of _true_.

**select** \* 
**from** _student_ **left outer join** _takes_ **on** _true_ 
**where** _student_._ID_\= _takes_._ID_;  

![Alt text](image-34.png)

**Figure 4.6** Join types and join conditions.

The earlier query, using the left outer join with the **on** condition, includes a tuple (70557, Snow, Physics, 0, _null_, _null_, _null_, _null_, _null_, _null_ ), because there is no tuple in _takes_ with _ID_ \= 70557. In the latter query, however, every tuple satisfies the join condition _true_, so no null-padded tuples are generated by the outer join. The outer join actually generates the Cartesian product of the two relations. Since there is no tuple in _takes_ with _ID_ \= 70557, every time a tuple appears in the outer join with _name_ \= “Snow”, the values for _student_._ID_ and _takes_._ID_ must be different, and such tuples would be eliminated by the **where** clause predicate. Thus student Snow never appears in the result of the latter query.

### 4.1.3 Join Types and Conditions

To distinguish normal joins from outer joins, normal joins are called **inner joins** in SQL. A join clause can thus specify **inner join** instead of **outer join** to specify that a normal join is to be used. The keyword **inner** is, however, optional. The default join type, when the **join** clause is used without the **outer** prefix is the **inner join**. Thus,

**select** \* 
**from** _student_ **join** _takes_ **using** (_ID_);

is equivalent to:

**select** \* 
**from** _student_ **inner join** _takes_ **using** (_ID_);

Similarly, **natural join** is equivalent to **natural inner join**. Figure 4.6 shows a full list of the various types of join that we have discussed.

As can be seen from the figure, any form of join (inner, left outer, right outer, or full outer) can be combined with any join condition (natural, using, or on).

