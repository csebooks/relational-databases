---
title: 'Overview'
weight: 1
---

# Overview

Consider the following relational-algebra expression, for the query “Find the names of all instructors in the Music department together with the course title of all the courses that the instructors teach.”

![Alt text](image-62.png)

Note that the projection of _course_ on (_course id_,_title_) is required since _course_ shares an attribute _dept name_ with _instructor_; if we did not remove this attribute using the projection, the above expression using natural joins would return only courses from the Music department, even if some Music department instructors taught courses in other departments.

The above expression constructs a large intermediate relation, _instructor _

_teaches  πcourse id ,title_(_course_). However, we are interested in only a few tuples

![Alt text](image-51.png)

of this relation (those pertaining to instructors in the Music department), and in only two of the ten attributes of this relation. Since we are concerned with only those tuples in the _instructor_ relation that pertain to the Music department, we do not need to consider those tuples that do not have _dept name_ \= “Music”. By reducing the number of tuples of the _instructor_ relation that we need to access, we reduce the size of the intermediate result. Our query is now represented by the relational-algebra expression:

![Alt text](image-63.png)

which is equivalent to our original algebra expression, but which generates smaller intermediate relations. Figure 13.1 depicts the initial and transformed expressions.

An evaluation plan defines exactly what algorithm should be used for each operation, and how the execution of the operations should be coordinated. Fig- ure 13.2 illustrates one possible evaluation plan for the expression from Fig- ure 13.1(b). As we have seen, several different algorithms can be used for each relational operation, giving rise to alternative evaluation plans. In the figure, hash join has been chosen for one of the join operations, while the other uses merge join, after sorting the relations on the join attribute, which is ID. Where edges are marked as pipelined, the output of the producer is pipelined directly to the consumer, without being written out to disk.

Given a relational-algebra expression, it is the job of the query optimizer to come up with a query-evaluation plan that computes the same result as the given expression, and is the least-costly way of generating the result (or, at least, is not much costlier than the least-costly way).

To find the least-costly query-evaluation plan, the optimizer needs to gener- ate alternative plans that produce the same result as the given expression, and to choose the least-costly one. Generation of query-evaluation plans involves three steps: (1) generating expressions that are logically equivalent to the given ex-  

![Alt text](image-52.png)

pression, (2) annotating the resultant expressions in alternative ways to generate alternative query-evaluation plans, and (3) estimating the cost of each evaluation plan, and choosing the one whose estimated cost is the least.

Steps (1), (2), and (3) are interleaved in the query optimizer—some expres- sions are generated, and annotated to generate evaluation plans, then further expressions are generated and annotated, and so on. As evaluation plans are generated, their costs are estimated by using statistical information about the relations, such as relation sizes and index depths.

To implement the first step, the query optimizer must generate expressions equivalent to a given expression. It does so by means of _equivalence rules_ that spec- ify how to transform an expression into a logically equivalent one. We describe these rules in Section 13.2.

In Section 13.3 we describe how to estimate statistics of the results of each op- eration in a query plan. Using these statistics with the cost formulae in Chapter 12 allows us to estimate the costs of individual operations. The individual costs are combined to determine the estimated cost of evaluating a given relational-algebra expression, as outlined earlier in Section 12.7.

In Section 13.4, we describe how to choose a query-evaluation plan. We can choose one based on the estimated cost of the plans. Since the cost is an estimate, the selected plan is not necessarily the least-costly plan; however, as long as the estimates are good, the plan is likely to be the least-costly one, or not much more costly than it.

Finally, materialized views help to speed up processing of certain queries. In Section 13.5, we study how to “maintain” materialized views that is, to keep them up-to-date and how to perform query optimization with materialized views.  

**VIEWING QUERY EVALUATION PLANS**

Most database systems provide a way to view the evaluation plan chosen to execute a given query. It is usually best to use the GUI provided with the database system to view evaluation plans. However, if you use a command line interface, many databases support variations of a command “**explain** _<_query_\>_”, which displays the execution plan chosen for the specified query _<_query_\>_. The exact syntax varies with different databases:

- PostgreSQL uses the syntax shown above.

- Oracle uses the syntax **explain plan for**. However, the command stores the resultant plan in a table called _plan table_, instead of displaying it. The query “**select** \* **from table**(_dbms xplan.display_);” displays the stored plan.

- DB2 follows a similar approach to Oracle, but requires the program db2exfmt to be executed to display the stored plan.

- SQL Server requires the command **set showplan text on** to be executed before submitting the query; then, when a query is submitted, instead of executing the query, the evaluation plan is displayed.

The estimated costs for the plan are also displayed along with the plan. It is worth noting that the costs are usually not in any externally meaningful unit, such as seconds or I/O operations, but rather in units of whatever cost model the optimizer uses. Some optimizers such as PostgreSQL display two cost-estimate numbers; the first indicates the estimated cost for outputting the first result, and the second indicates the estimated cost for outputting all results.

