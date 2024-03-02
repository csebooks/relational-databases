---
title: 'Recursive Queries'
weight: 4
references:
    videos:
        - youtube:fLV3TPj6MKM
        - youtube:if1u24r0YhI
    links:
        - https://codedamn.com/news/sql/recursive-queries-in-sql
        - https://www.ibm.com/docs/en/i/7.4?topic=optimization-example
    books:
        - b1:
            title: Datalog and Recursive Query Processing 
            url: https://www.google.co.in/books/edition/Datalog_and_Recursive_Query_Processing/wSIVngEACAAJ?hl=en
        - b2:
           title: Beginning SQL Queries
            url: https://www.google.co.in/books/edition/Beginning_SQL_Queries/KkEnCgAAQBAJ?hl=en&gbpv=0
---

# Recursive Queries

Consider the instance of the relation _prereq_ shown in Figure 5.12 containing information about the various courses offered at the university and the prerequisite for each course.7

Suppose now that we want to find out which courses are a prerequisite whether directly or indirectly, for a specific course—say, CS-347. That is, we wish to find a course that is a direct prerequisite for CS-347, or is a prerequisite for a course that is a prerequisite for CS-347, and so on.

Thus, if CS-301 is a prerequisite for CS-347, and CS-201 is a prerequisite for CS-301, and CS-101 is a prerequisite for CS-201, then CS-301, CS-201, and CS-101 are all prerequisites for CS-347.

The **transitive closure** of the relation _prereq_ is a relation that contains all pairs (_cid_, _pre_) such that _pre_ is a direct or indirect prerequisite of _cid_. There are numerous applications that require computation of similar transitive closures on **hierarchies**. For instance, organizations typically consist of several levels of organizational units. Machines consist of parts that in turn have subparts, and so on; for example, a bicycle may have subparts such as wheels and pedals, which in turn have subparts such as tires, rims, and spokes. Transitive closure can be used on such hierarchies to find, for example, all parts in a bicycle.

## Transitive Closure Using Iteration

One way to write the above query is to use iteration: First find those courses that are a direct prerequisite of CS-347, then those courses that are a prerequisite of all the courses under the first set, and so on. This iterative process continues until we reach an iteration where no courses are added. Figure 5.13 shows a function _findAllPrereqs(cid)_ to carry out this task; the function takes the _course id_ of the course as a parameter (_cid_), computes the set of all direct and indirect

prerequisites of that course, and returns the set. The procedure uses three temporary tables:

• _c prereq_: stores the set of tuples to be returned.

• _new c prereq_: stores the courses found in the previous iteration.

• _temp_: used as temporary storage while sets of courses are manipulated.

Note that SQL allows the creation of temporary tables using the command **create temporary table**; such tables are available only within the transaction executing the query, and are dropped when the transaction finishes. Moreover, if two instances of _findAllPrereqs_ run concurrently, each gets its own copy of the temporary tables; if they shared a copy, their result could be incorrect.

The procedure inserts all direct prerequisites of course _cid_ into _new c prereq_ before the **repeat** loop. The **repeat** loop first adds all courses in _new c prereq_ to _c prereq_. Next, it computes prerequisites of all those courses in _new c prereq_, except those that have already been found to be prerequisites of _cid_, and stores them in the temporary table _temp_. Finally, it replaces the contents of _new c prereq_ by the contents of _temp_. The **repeat** loop terminates when it finds no new (indirect) prerequisites.

Figure 5.14 shows the prerequisites that would be found in each iteration, if the procedure were called for the course named CS-347.

We note that the use of the **except** clause in the function ensures that the function works even in the (abnormal) case where there is a cycle of prerequisites. For example, if _a_ is a prerequisite for _b_, _b_ is a prerequisite for _c_, and _c_ is a prerequisite for _a_, there is a cycle.  

**create function** _findAllPrereqs(cid_ **varchar(8)**) 
– – Finds all courses that are prerequisite (directly or indirectly) for _cid_

**returns table** (_course id_ **varchar**(8)) 
– – The relation _prereq_(_course id_, _prereq id_) specifies which course is 
– – directly a prerequisite for another course.

**begin** 
**create temporary table** _c prereq_ (_course id_ **varchar(8)**);
– – table _c prereq_ stores the set of courses to be returned 
**create temporary table** _new c prereq_ (_course id_ **varchar(8)**);
– – table _new c prereq_ contains courses found in the previous iteration **create temporary table** _temp_ (_course id_ **varchar(8)**);
– – table _temp_ is used to store intermediate results 
**insert into** _new c prereq_
**select** _prereq id_ 
**from** _prereq_ 
**where** _course id_ \= _cid_;

**repeat** 
**insert into** _c prereq_
**select** _course id_ 
**from** _new c prereq_;

**insert into** _temp_ 
(**select** _prereq_._course id_
**from** _new c prereq, prereq_ 
**where** _new c prereq_._course id_ \= _prereq_._prereq id_

) **except** (
**select** _course id_ 
**from** _c prereq_
); 
**delete from** _new c prereq_; 
**insert into** _new c prereq_
**select \***
**from** _temp_;
**delete from** _temp_;
**until not exists** (**select \* from** _new c prereq_) 
**end repeat**; 
**return table** _c prereq_;
**end**

**Figure 5.13** Finding all prerequisites of a course.

While cycles may be unrealistic in course prerequisites, cycles are possible in other applications. For instance, suppose we have a relation _flights(to, from_) that says which cities can be reached from which other cities by a direct flight. We can  

![Alt text](image-38.png)

**Figure 5.14** Prerequisites of CS-347 in iterations of function _findAllPrereqs._

write code similar to that in the _findAllPrereqs_ function, to find all cities that are reachable by a sequence of one or more flights from a given city. All we have to do is to replace _prereq_ by _flight_ and replace attribute names correspondingly. In this situation, there can be cycles of reachability, but the function would work correctly since it would eliminate cities that have already been seen.

## Recursion in SQL

It is rather inconvenient to specify transitive closure using iteration. There is an alternative approach, using recursive view definitions, that is easier to use.

We can use recursion to define the set of courses that are prerequisites of a particular course, say CS-347, as follows. The courses that are prerequisites (directly or indirectly) of CS-347 are:

**1.** Courses that are prerequisites for CS-347.

**2.** Courses that are prerequisites for those courses that are prerequisites (directly or indirectly) for CS-347.

Note that case 2 is recursive, since it defines the set of courses that are prerequisites of CS-347 in terms of the set of courses that are prerequisites of CS-347. Other examples of transitive closure, such as finding all subparts (direct or indirect) of a given part can also be defined in a similar manner, recursively.

Since the SQL:1999 version, the SQL standard supports a limited form of recursion, using the **with recursive** clause, where a view (or temporary view) is expressed in terms of itself. Recursive queries can be used, for example, to express transitive closure concisely. Recall that the **with** clause is used to define a temporary view whose definition is available only to the query in which it is defined. The additional keyword **recursive** specifies that the view is recursive.

For example, we can find every pair (_cid_,_pre_) such that _pre_ is directly or indirectly a prerequisite for course _cid_, using the recursive SQL view shown in Figure 5.15.

Any recursive view must be defined as the union of two subqueries: a **base query** that is nonrecursive and a **recursive query** that uses the recursive view. In the example in Figure 5.15, the base query is the select on _prereq_ while the recursive query computes the join of _prereq_ and _rec prereq_.  

**with recursive** _c prereq_(_course id_, _prereq id_) **as** ( 
**select** _course id_, _prereq id_ 
**from** _prereq_
**union** 
**select** _prereq_._prereq id_, _c prereq_._course id_ 
**from** _prereq_, _c prereq_ 
**where** _prereq_._course id_ \= _c prereq_._prereq id_
) 
**select** ∗ 
**from** _c prereq_;

**Figure 5.15** Recursive query in SQL.

The meaning of a recursive view is best understood as follows. First compute the base query and add all the resultant tuples to the recursively defined view relation _rec prereq_ (which is initially empty). Next compute the recursive query using the current contents of the view relation, and add all the resulting tuples back to the view relation. Keep repeating the above step until no new tuples are added to the view relation. The resultant view relation instance is called a **fixed point** of the recursive view definition. (The term “fixed” refers to the fact that there is no further change.) The view relation is thus defined to contain exactly the tuples in the fixed-point instance.

Applying the above logic to our example, we first find all direct prerequisites of each course by executing the base query. The recursive query adds one more level of courses in each iteration, until the maximum depth of the course-prereq relationship is reached. At this point no new tuples are added to the view, and a fixed point is reached.

To find the prerequisites of a specific course, such as CS-347, we can modify the outer level query by adding a where clause “**where** _rec prereq.course id_ \= ‘CS-347‘”. One way to evaluate the query with the selection is to compute the full contents of _rec prereq_ using the iterative technique, and then select from this result only those tuples whose _course id_ is CS-347. However, this would result in computing (course, prerequisite) pairs for all courses, all of which are irrelevant except for those for the course CS-347. In fact the database system is not required to use the above iterative technique to compute the full result of the recursive query and then perform the selection. It may get the same result using other techniques that may be more efficient, such as that used in the function _findAllPrereqs_ which we saw earlier. See the bibliographic notes for references to more information on this topic.

There are some restrictions on the recursive query in a recursive view; specifically, the query should be **monotonic**, that is, its result on a view relation instance _V_1 should be a superset of its result on a view relation instance _V_2 if _V_1 is a superset of _V_2\. Intuitively, if more tuples are added to the view relation, the recursive query should return at least the same set of tuples as before, and possibly return additional tuples.  

In particular, recursive queries should not use any of the following constructs, since they would make the query nonmonotonic:

• Aggregation on the recursive view.

• **not exists** on a subquery that uses the recursive view.

• Set difference (**except**) whose right-hand side uses the recursive view.

For instance, if the recursive query was of the form _r_ − _v_ where _v_ is the recursive view, if we add a tuple to _v_ the result of the query can become smaller; the query is therefore not monotonic.

The meaning of recursive views can be defined by the iterative procedure as long as the recursive query is monotonic; if the recursive query is nonmonotonic, the meaning of the view is hard to define. SQL therefore requires the queries to be monotonic. Recursive queries are discussed in more detail in the context of the Datalog query language, in Section B.3.6.

SQL also allows creation of recursively defined permanent views by using **create recursive view** in place of **with recursive**. Some implementations support recursive queries using a different syntax; see the respective system manuals for further details.
