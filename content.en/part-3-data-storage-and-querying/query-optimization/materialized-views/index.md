---
title: 'Materialized Views'
weight: 5
references:
    videos:
        - youtube:WzkBZ0byoYE
        - youtube:ZomzrCTxFeQ
    links:
        - https://aws.amazon.com/what-is/materialized-view/#:~:text=A%20materialized%20view%20is%20a,for%20customer%20and%20product%20data.
        - https://docs.snowflake.com/en/user-guide/views-materialized
    books:
        - b1:
            title: Materialized Views
            url: https://www.google.co.in/books/edition/Materialized_Views/QIAekwEACAAJ?hl=en
        - b2:
           title: Oracle  SQL 
            url: https://www.google.co.in/books/edition/Oracle_SQL/NU-d9TIae9IC?hl=en&gbpv=0
---

# Materialized Views

When a view is defined, normally the database stores only the query defining the view. In contrast, a **materialized view** is a view whose contents are computed and stored. Materialized views constitute redundant data, in that their contents can be inferred from the view definition and the rest of the database contents. However, it is much cheaper in many cases to read the contents of a materialized view than to compute the contents of the view by executing the query defining the view.

Materialized views are important for improving performance in some appli- cations. Consider this view, which gives the total salary in each department:

**create view** _department total salary_(_dept name_, _total salary_) **as** 
**select** _dept name_, **sum** (_salary_) 
**from** _instructor_ 
**group by** _dept name_;  

Suppose the total salary amount at a department is required frequently. Comput- ing the view requires reading every _instructor_ tuple pertaining to a department, and summing up the salary amounts, which can be time-consuming. In contrast, if the view definition of the total salary amount were materialized, the total salary amount could be found by looking up a single tuple in the materialized view.5

## View Maintenance

A problem with materialized views is that they must be kept up-to-date when the data used in the view definition changes. For instance, if the _salary_ value of an instructor is updated, the materialized view will become inconsistent with the underlying data, and it must be updated. The task of keeping a materialized view up-to-date with the underlying data is known as **view maintenance**.

Views can be maintained by manually written code: That is, every piece of code that updates the _salary_ value can be modified to also update the total salary amount for the corresponding department. However, this approach is error prone, since it is easy to miss some places where the _salary_ is updated, and the materialized view will then no longer match the underlying data.

Another option for maintaining materialized views is to define triggers on insert, delete, and update of each relation in the view definition. The triggers must modify the contents of the materialized view, to take into account the change that caused the trigger to fire. A simplistic way of doing so is to completely recompute the materialized view on every update.

A better option is to modify only the affected parts of the materialized view, which is known as **incremental view maintenance**. We describe how to perform incremental view maintenance in Section 13.5.2.

Modern database systems provide more direct support for incremental view maintenance. Database-system programmers no longer need to define triggers for view maintenance. Instead, once a view is declared to be materialized, the database system computes the contents of the view and incrementally updates the contents when the underlying data change.

Most database systems perform **immediate view maintenance**; that is, incre- mental view maintenance is performed as soon as an update occurs, as part of the updating transaction. Some database systems also support **deferred view main- tenance**, where view maintenance is deferred to a later time; for example, updates may be collected throughout a day, and materialized views may be updated at night. This approach reduces the overhead on update transactions. However, ma- terialized views with deferred view maintenance may not be consistent with the underlying relations on which they are defined.

## Incremental View Maintenance

To understand how to maintain materialized views incrementally, we start off by considering individual operations, and then we see how to handle a complete expression.

The changes to a relation that can cause a materialized view to become out- of-date are inserts, deletes, and updates. To simplify our description, we replace updates to a tuple by deletion of the tuple followed by insertion of the updated tuple. Thus, we need to consider only inserts and deletes. The changes (inserts and deletes) to a relation or expression are referred to as its **differential**.

### Join Operation

Consider the materialized view _v_ \= _r  s_. Suppose we modify _r_ by inserting a set of tuples denoted by _ir_ . If the old value of _r_ is denoted by _r^old^_ , and the new value of _r_ by _r^new^_, _r^new^_ \= _r^old^_ ∪ _ir_ . Now, the old value of the view, _v^old^_ , is given by _r^old^  s_, and the new value _v^new^_ is given by _r^new^  s_. We can rewrite _r^new^  s_ as (_r^old^_ ∪ _ir_ ) _ s_, which we can again rewrite as (_r^old^  s_) ∪ (_ir  s_). In other words:

![Alt text](image-99.png)

Thus, to update the materialized view _v_, we simply need to add the tuples _ir  s_ to the old contents of the materialized view. Inserts to _s_ are handled in an exactly symmetric fashion.

Now suppose _r_ is modified by deleting a set of tuples denoted by _dr_ . Using the same reasoning as above, we get:

![Alt text](image-101.png)

Deletes on _s_ are handled in an exactly symmetric fashion.

### Selection and Projection Operations

Consider a view _v_ \= σθ(_r_ ). If we modify _r_ by inserting a set of tuples _ir_ , the new value of _v_ can be computed as:

![Alt text](image-100.png)

Similarly, if _r_ is modified by deleting a set of tuples _dr_ , the new value of _v_ can be computed as:

![Alt text](image-102.png)

Projection is a more difficult operation with which to deal. Consider a ma- terialized view _v_ \= _πA_(_r_ ). Suppose the relation _r_ is on the schema _R_ \= (_A, B_), and _r_ contains two tuples (_a ,_ 2) and (_a ,_ 3). Then, _πA_(_r_ ) has a single tuple (_a_ ). If we delete the tuple (_a ,_ 2) from _r_ , we cannot delete the tuple (_a_ ) from _πA_(_r_ ): If we did so, the result would be an empty relation, whereas in reality _πA_(_r_ ) still has a single tuple (_a_ ). The reason is that the same tuple (_a_ ) is derived in two ways, and deleting one tuple from _r_ removes only one of the ways of deriving (_a_ ); the other is still present.

This reason also gives us the intuition for solution: For each tuple in a projec- tion such as _πA_(_r_ ), we will keep a count of how many times it was derived.

When a set of tuples _dr_ is deleted from _r_ , for each tuple _t_ in _dr_ we do the following: Let _t.A_ denote the projection of _t_ on the attribute _A_. We find (_t.A_) in the materialized view, and decrease the count stored with it by 1. If the count becomes 0, (_t.A_) is deleted from the materialized view.

Handling insertions is relatively straightforward. When a set of tuples _ir_ is inserted into _r_ , for each tuple _t_ in _ir_ we do the following: If (_t.A_) is already present in the materialized view, we increase the count stored with it by 1. If not, we add (_t.A_) to the materialized view, with the count set to 1.

### Aggregation Operations

Aggregation operations proceed somewhat like projections. The aggregate oper- ations in SQL are **count, sum, avg, min,** and **max**:

- **count**: Consider a materialized view _v_ \= _AGcount_(_B_)(_r_ ), which computes the count of the attribute _B_, after grouping _r_ by attribute _A_.

When a set of tuples _ir_ is inserted into _r_ , for each tuple _t_ in _ir_ we do the following: We look for the group _t.A_ in the materialized view. If it is not present, we add (_t.A,_ 1) to the materialized view. If the group _t.A_ is present, we add 1 to the count of the group.

When a set of tuples _dr_ is deleted from _r_ , for each tuple _t_ in _dr_ we do the following: We look for the group _t.A_ in the materialized view, and subtract 1 from the count for the group. If the count becomes 0, we delete the tuple for the group _t.A_ from the materialized view.

- **sum**: Consider a materialized view _v_ \= _AGsum_(_B_)(_r_ ). When a set of tuples _ir_ is inserted into _r_ , for each tuple _t_ in _ir_ we do

the following: We look for the group _t.A_ in the materialized view. If it is not present, we add (_t.A, t.B_) to the materialized view; in addition, we store a count of 1 associated with (_t.A, t.B_), just as we did for projection. If the group _t.A_ is present, we add the value of _t.B_ to the aggregate value for the group, and add 1 to the count of the group.

When a set of tuples _dr_ is deleted from _r_ , for each tuple _t_ in _dr_ we do the following: We look for the group _t.A_ in the materialized view, and subtract _t.B_ from the aggregate value for the group. We also subtract 1 from the count for the group, and if the count becomes 0, we delete the tuple for the group _t.A_ from the materialized view.

Without keeping the extra count value, we would not be able to distinguish a case where the sum for a group is 0 from the case where the last tuple in a group is deleted.  

- **avg**: Consider a materialized view _v_ \= _AGavg_(_B_)(_r_ ). Directly updating the average on an insert or delete is not possible, since

it depends not only on the old average and the tuple being inserted/deleted, but also on the number of tuples in the group.

Instead, to handle the case of **avg**, we maintain the **sum** and **count** aggregate values as described earlier, and compute the average as the sum divided by the count.

- **min, max**: Consider a materialized view _v_ \= _AGmin_(_B_)(_r_ ). (The case of **max** is exactly equivalent.)

Handling insertions on _r_ is straightforward. Maintaining the aggregate values **min** and **max** on deletions may be more expensive. For example, if the tuple corresponding to the minimum value for a group is deleted from _r_ , we have to look at the other tuples of _r_ that are in the same group to find the new minimum value.

**13.5.2.4 Other Operations**

The set operation _intersection_ is maintained as follows: Given materialized view _v_ \= _r_ ∩ _s_, when a tuple is inserted in _r_ we check if it is present in _s_, and if so we add it to _v_. If a tuple is deleted from _r_ , we delete it from the intersection if it is present. The other set operations, _union_ and _set difference_, are handled in a similar fashion; we leave details to you.

Outer joins are handled in much the same way as joins, but with some extra work. In the case of deletion from _r_ we have to handle tuples in _s_ that no longer match any tuple in _r_ . In the case of insertion to _r_ , we have to handle tuples in _s_ that did not match any tuple in _r_ . Again we leave details to you.

**13.5.2.5 Handling Expressions**

So far we have seen how to update incrementally the result of a single operation. To handle an entire expression, we can derive expressions for computing the incremental change to the result of each subexpression, starting from the smallest subexpressions.

For example, suppose we wish to incrementally update a materialized view _E_1 _ E_2 when a set of tuples _ir_ is inserted into relation _r_ . Let us assume _r_ is used in _E_1 alone. Suppose the set of tuples to be inserted into _E_1 is given by expression _D_1\. Then the expression _D_1 _ E_2 gives the set of tuples to be inserted into _E_1 _ E_2.

See the bibliographical notes for further details on incremental view mainte- nance with expressions.

## Query Optimization and Materialized Views

Query optimization can be performed by treating materialized views just like regular relations. However, materialized views offer further opportunities for optimization:

- Rewriting queries to use materialized views:  

Suppose a materialized view _v_ \= _r  s_ is available, and a user submits a query _r  s  t_. Rewriting the query as _v  t_ may provide a more efficient query plan than optimizing the query as submitted. Thus, it is the job of the query optimizer to recognize when a materialized view can be used to speed up a query.

- Replacing a use of a materialized view with the view definition: Suppose a materialized view _v_ \= _r  s_ is available, but without any index on it, and a user submits a query _A_\=10(_v_). Suppose also that _s_ has an index on the common attribute _B_, and _r_ has an index on attribute _A_. The best plan for this query may be to replace _v_ with _r  s_, which can lead to the query plan _A_\=10(_r_ ) _ s_; the selection and join can be performed efficiently by using the indices on _r.A_ and _s.B_, respectively. In contrast, evaluating the selection directly on _v_ may require a full scan of _v_, which may be more expensive.

The bibliographical notes give pointers to research showing how to efficiently perform query optimization with materialized views.

## Materialized View and Index Selection

Another related optimization problem is that of **materialized view selection**, namely, “What is the best set of views to materialize?” This decision must be made on the basis of the system **workload**, which is a sequence of queries and updates that reflects the typical load on the system. One simple criterion would be to select a set of materialized views that minimizes the overall execution time of the workload of queries and updates, including the time taken to maintain the materialized views. Database administrators usually modify this criterion to take into account the importance of different queries and updates: Fast response may be required for some queries and updates, but a slow response may be acceptable for others.

Indices are just like materialized views, in that they too are derived data, can speed up queries, and may slow down updates. Thus, the problem of **index selection** is closely related to that of materialized view selection, although it is simpler. We examine index and materialized view selection in more detail in Sections 24.1.6 and 24.1.7.

Most database systems provide tools to help the database administrator with index and materialized view selection. These tools examine the history of queries and updates, and suggest indices and views to be materialized. The Microsoft SQL Server Database Tuning Assistant, the IBM DB2 Design Advisor, and the Oracle SQL Tuning Wizard are examples of such tools.

