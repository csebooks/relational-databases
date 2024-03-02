---
title: 'Advanced Topics in Query Optimization'
weight: 6
references:
    videos:
        - youtube:_luHqhzrDD8
        - youtube:3W566_eFtxM
    links:
        - https://www.javatpoint.com/dbms-transforming-relational-expressions
        - https://tutorialcup.com/dbms/transformation-of-relational-expressions.htm
    books:
        - b1:
            title: Hands On Relational Database Management System RDBM
            url: https://www.google.co.in/books/edition/Hands_On_Relational_Database_Management/7QNuDwAAQBAJ?hl=en&gbpv=1&dq=Transformation+of+Relational+Expressions+books&pg=PA3&printsec=frontcover
        - b2:
           title: Database in Depth
            url: https://www.google.co.in/books/edition/Database_in_Depth/sZc7B6wnmNcC?hl=en&gbpv=0
---

# Advanced Topics in Query Optimization

There are a number of opportunities for optimizing queries, beyond those we have seen so far. We examine a few of these in this section.  

## Top-_K_ Optimization

Many queries fetch results sorted on some attributes, and require only the top _K_ results for some _K_ . Sometimes the bound _K_ is specified explicitly. For example, some databases support a **limit** _K_ clause which results in only the top _K_ results being returned by the query. Other databases support alternative ways of speci- fying similar limits. In other cases, the query may not specify such a limit, but the optimizer may allow a hint to be specified, indicating that only the top _K_ results of the query are likely to be retrieved, even if the query generates more results.

When _K_ is small, a query optimization plan that generates the entire set of results, then sorts and generates the top _K_ , is very inefficient since it discards most of the intermediate results that it computes. Several techniques have been proposed to optimize such _top-K queries_. One approach is to use pipelined plans that can generate the results in sorted order. Another approach is to estimate what is the highest value on the sorted attributes that will appear in the top-_K_ output, and introduce selection predicates that eliminate larger values. If extra tuples beyond the top-_K_ are generated they are discarded, and if too few tuples are generated then the selection condition is changed and the query is re-executed. See the bibliographical notes for references to work on top-_K_ optimization.

## Join Minimization

When queries are generated through views, sometimes more relations are joined than are needed for computation of the query. For example, a view _v_ may in- clude the join of _instructor_ and _department_, but a use of the view _v_ may use only attributes from _instructor_. The join attribute _dept name_ of _instructor_ is a foreign key referencing _department_. Assuming that _instructor_._dept name_ has been declared **not null**, the join with _department_ can be dropped, with no impact on the query. For, under the above assumption, the join with _department_ does not eliminate any tuples from _instructor_, nor does it result in extra copies of any _instructor_ tuple.

Dropping a relation from a join as above is an example of join minimization. In fact, join minimization can be performed in other situations as well. See the bibliographical notes for references on join minimization.

## Optimization of Updates

Update queries often involve subqueries in the **set** and **where** clauses, which must also be taken into account in optimizing the update. Updates that involve a selection on the updated column (e.g., give a 10 percent salary raise to all employees whose salary is ≥ $100,000) must be handled carefully. If the update is done while the selection is being evaluated by an index scan, an updated tuple may be reinserted in the index ahead of the scan and seen again by the scan; the same employee tuple may then get incorrectly updated multiple times (an infinite number of times, in this case). A similar problem also arises with updates involving subqueries whose result is affected by the update.

The problem of an update affecting the execution of a query associated with the update is known as the **Halloween problem** (named so because it was first recognized on a Halloween day, at IBM). The problem can be avoided by executing the queries defining the update first, creating a list of affected tuples, and updating the tuples and indices as the last step. However, breaking up the execution plan in such a fashion increases the execution cost. Update plans can be optimized by checking if the Halloween problem can occur, and if it cannot occur, updates can be performed while the query is being processed, reducing the update overheads. For example, the Halloween problem cannot occur if the update does not affect index attributes. Even if it does, if the updates decrease the value, while the index is scanned in increasing order, updated tuples will not be encountered again during the scan. In such cases, the index can be updated even while the query is being executed, reducing the overall cost.

Update queries that result in a large number of updates can also be optimized by collecting the updates as a batch, and then applying the batch of updates separately to each affected index. When applying the batch of updates to an index, the batch is first sorted in the index order for that index; such sorting can greatly reduce the amount of random I/O required for updating indices.

Such optimizations of updates are implemented in most database systems. See the bibliographical notes for references to such optimization.

## Multiquery Optimization and Shared Scans

When a batch of queries are submitted together, a query optimizer can poten- tially exploit common subexpressions between the different queries, evaluating them once and reusing them where required. Complex queries may in fact have subexpressions repeated in different parts of the query, which can be similarly exploited, to reduce query evaluation cost. Such optimization is known as **multi- query optimization**.

**Common subexpression elimination** optimizes subexpressions shared by different expressions in a program, by computing and storing the result, and reusing it wherever the subexpression occurs. Common subexpression elimina- tion is a standard optimization applied on arithmetic expressions by programming- language compilers. Exploiting common subexpressions among evaluation plans chosen for each of a batch of queries is just as useful in database query evalua- tion, and is implemented by some databases. However, multiquery optimization can do even better in some cases: A query typically has more than one evalua- tion plan, and a judiciously chosen set of query evaluation plans for the queries may provide for a greater sharing and lesser cost than that afforded by choos- ing the lowest cost evaluation plan for each query. More details on multiquery optimization may be found in references cited in the bibliographical notes.

Sharing of relation scans between queries is another limited form of mul- tiquery optimization that is implemented in some databases. The **shared-scan** optimization works as follows: Instead of reading the relation repeatedly from disk, once for each query that needs to scan a relation, data are read once from disk, and pipelined to each of the queries. The shared-scan optimization is par- ticularly useful when multiple queries perform a scan on a single large relation (typically a “fact table”).  

## Parametric Query Optimization

Plan caching, which we saw earlier in Section 13.4.3, is used as a heuristic in many databases. Recall that with plan caching, if a query is invoked with some constants, the plan chosen by the optimizer is cached, and reused if the query is submitted again, even if the constants in the query are different. For example, suppose a query takes a department name as a parameter, and retrieves all courses of the department. With plan caching, a plan chosen when the query is executed for the first time, say for the Music department, is reused if the query is executed for any other department.

Such reuse of plans by plan caching is reasonable if the optimal query plan is not significantly affected by the exact value of the constants in the query. However, if the plan is affected by the value of the constants, parametric query optimization is an alternative.

In **parametric query optimization**, a query is optimized without being pro- vided specific values for its parameters, for example, _dept name_ in the preceding example. The optimizer then outputs several plans, each optimal for a different parameter value. A plan would be output by the optimizer only if it is optimal for some possible value of the parameters. The set of alternative plans output by the optimizer are stored. When a query is submitted with specific values for its parameters, instead of performing a full optimization, the cheapest plan from the set of alternative plans computed earlier is used. Finding the cheapest such plan usually takes much less time than reoptimization. See the bibliographical notes for references on parametric query optimization.

