---
title: Views
weight: 2
references:
    videos:
        - youtube:XmI5SwzGC8Q
        - youtube:cLSxasHg9WY
    links:
        - https://www.geeksforgeeks.org/sql-views/
        - https://www.javatpoint.com/sql-view
    books:
        - b1:
            title: SQL Server DMVs in Action
            url: https://www.google.co.in/books/edition/SQL_Server_DMVs_in_Action/9TkzEAAAQBAJ?hl=en&gbpv=0
            
        - b2:
            title: SQL Server Query Performance Tuning 
            url: https://www.google.co.in/books/edition/SQL_Server_Query_Performance_Tuning/C65qBAAAQBAJ?hl=en&gbpv=0
---

# Views

In our examples up to this point, we have operated at the logical-model level. That is, we have assumed that the relations in the collection we are given are the actual relations stored in the database.  

It is not desirable for all users to see the entire logical model. Security considerations may require that certain data be hidden from users. Consider a clerk who needs to know an instructor’s ID, name and department name, but does not have authorization to see the instructor’s salary amount. This person should see a relation described in SQL, by:

**select** _ID_, _name_, _dept name_ 
**from** _instructor_;

Aside from security concerns, we may wish to create a personalized collection of relations that is better matched to a certain user’s intuition than is the logical model. We may want to have a list of all course sections offered by the Physics department in the Fall 2009 semester, with the building and room number of each section. The relation that we would create for obtaining such a list is:

**select** _course_._course id_, _sec id_, _building_, _room number_ 
**from** _course_, _section_ 
**where** _course_._course id_ \= _section_._course id_
**and** _course_._dept name_ \= ’Physics’ 
**and** _section_._semester_ \= ’Fall’ 
**and** _section_._year_ \= ’2009’;

It is possible to compute and store the results of the above queries and then make the stored relations available to users. However, if we did so, and the underlying data in the relations _instructor_, _course_, or _section_ changes, the stored query results would then no longer match the result of reexecuting the query on the relations. In general, it is a bad idea to compute and store query results such as those in the above examples (although there are some exceptions, which we study later).

Instead, SQL allows a “virtual relation” to be defined by a query, and the relation conceptually contains the result of the query. The virtual relation is not precomputed and stored, but instead is computed by executing the query whenever the virtual relation is used.

Any such relation that is not part of the logical model, but is made visible to a user as a virtual relation, is called a **view**. It is possible to support a large number of views on top of any given set of actual relations.

## View Definition

We define a view in SQL by using the **create view** command. To define a view, we must give the view a name and must state the query that computes the view. The form of the **create view** command is:

**create view** _v_ **as** _<_query expression_\>_;  

**122 Chapter 4 Intermediate SQL**

where _<_query expression_\>_ is any legal query expression. The view name is represented by _v_.

Consider again the clerk who needs to access all data in the _instructor_ relation, except _salary_. The clerk should not be authorized to access the _instructor_ relation (we see later, in Section 4.6, how authorizations can be specified). Instead, a view relation _faculty_ can be made available to the clerk, with the view defined as follows:

**create view** _faculty_ **as** 
**select** _ID_, _name_, _dept name_ 
**from** _instructor_;

As explained earlier, the view relation conceptually contains the tuples in the query result, but is not precomputed and stored. Instead, the database system stores the query expression associated with the view relation. Whenever the view relation is accessed, its tuples are created by computing the query result. Thus, the view relation is created whenever needed, on demand.

To create a view that lists all course sections offered by the Physics department in the Fall 2009 semester with the building and room number of each section, we write:

**create view** _physics fall 2009_ **as** 
**select** _course_._course id_, _sec id_, _building_, _room number_ 
**from** _course_, _section_ 
**where** _course_._course id_ \= _section_._course id_
**and** _course_._dept name_ \= ’Physics’ 
**and** _section_._semester_ \= ’Fall’ 
**and** _section_._year_ \= ’2009’;

## Using Views in SQL Queries

Once we have defined a view, we can use the view name to refer to the virtual relation that the view generates. Using the view _physics fall 2009_, we can find all Physics courses offered in the Fall 2009 semester in the Watson building by writing:

**select** _course id_ 
**from** _physics fall 2009_ 
**where** _building_\= ’Watson’;

View names may appear in a query any place where a relation name may appear, The attribute names of a view can be specified explicitly as follows:

**create view** _departments total salary_(_dept name_, _total salary_) **as** 
**select** _dept name_, **sum** (_salary_) 
**from** _instructor_ 
**group by** _dept name_;  

The preceding view gives for each department the sum of the salaries of all the instructors at that department. Since the expression **sum**(_salary_) does not have a name, the attribute name is specified explicitly in the view definition.

Intuitively, at any given time, the set of tuples in the view relation is the result of evaluation of the query expression that defines the view. Thus, if a view relation is computed and stored, it may become out of date if the relations used to define it are modified. To avoid this, views are usually implemented as follows. When we define a view, the database system stores the definition of the view itself, rather than the result of evaluation of the query expression that defines the view. Wherever a view relation appears in a query, it is replaced by the stored query expression. Thus, whenever we evaluate the query, the view relation is recomputed.

One view may be used in the expression defining another view. For example, we can define a view _physics fall 2009 watson_ that lists the course ID and room number of all Physics courses offered in the Fall 2009 semester in the Watson building as follows:

**create view** _physics fall 2009 watson_ **as** 
**select** _course id_, _room number_ 
**from** _physics fall 2009_ 
**where** _building_\= ’Watson’;

where _physics fall 2009 watson_ is itself a view relation. This is equivalent to:

**create view** _physics fall 2009 watson_ **as** 
(**select** _course id_, _room number_ 
**from** (**select** _course_._course id_, _building_, _room number_
**from** _course_, _section_ 
**where** _course_._course id_ \= _section_._course id_
**and** _course_._dept name_ \= ’Physics’ 
**and** _section_._semester_ \= ’Fall’ 
**and** _section_._year_ \= ’2009’)
**where** _building_\= ’Watson’;

## Materialized Views

Certain database systems allow view relations to be stored, but they make sure that, if the actual relations used in the view definition change, the view is kept up-to-date. Such views are called **materialized views**.

For example, consider the view _departments total salary_. If the above view is materialized, its results would be stored in the database. However, if an _instructor_ tuple is added to or deleted from the _instructor_ relation, the result of the query defining the view would change, and as a result the materialized view’s contents must be updated. Similarly, if an instructor’s salary is updated, the tuple in _departments total salary_ corresponding to that instructor’s department must be updated.  

The process of keeping the materialized view up-to-date is called **materialized view maintenance** (or often, just **view maintenance**) and is covered in Section 13.5. View maintenance can be done immediately when any of the relations on which the view is defined is updated. Some database systems, however, perform view maintenance lazily, when the view is accessed. Some systems update materialized views only periodically; in this case, the contents of the materialized view may be stale, that is, not up-to-date, when it is used, and should not be used if the application needs up-to-date data. And some database systems permit the database administrator to control which of the above methods is used for each materialized view.

Applications that use a view frequently may benefit if the view is materialized. Applications that demand fast response to certain queries that compute aggregates over large relations can also benefit greatly by creating materialized views corresponding to the queries. In this case, the aggregated result is likely to be much smaller than the large relations on which the view is defined; as a result the materialized view can be used to answer the query very quickly, avoiding reading the large underlying relations. Of course, the benefits to queries from the materialization of a view must be weighed against the storage costs and the added overhead for updates.

SQL does not define a standard way of specifying that a view is materialized, but many database systems provide their own SQL extensions for this task. Some database systems always keep materialized views up-to-date when the underlying relations change, while others permit them to become out of date, and periodically recompute them.

## Update of a View

Although views are a useful tool for queries, they present serious problems if we express updates, insertions, or deletions with them. The difficulty is that a modification to the database expressed in terms of a view must be translated to a modification to the actual relations in the logical model of the database.

Suppose the view _faculty_, which we saw earlier, is made available to a clerk. Since we allow a view name to appear wherever a relation name is allowed, the clerk can write:

**insert into** _faculty_ **values** (’30765’, ’Green’, ’Music’);

This insertion must be represented by an insertion into the relation _instructor_, since _instructor_ is the actual relation from which the database system constructs the view _faculty_. However, to insert a tuple into _instructor_, we must have some value for _salary_. There are two reasonable approaches to dealing with this insertion:

• Reject the insertion, and return an error message to the user.

• Insert a tuple (’30765’, ’Green’, ’Music’, _null_) into the _instructor_ relation.  

Another problem with modification of the database through views occurs with a view such as:

**create view** _instructor info_ **as** 
**select** _ID_, _name_, _building_ 
**from** _instructor_, _department_ 
**where** _instructor_._dept name_\= _department_._dept name_;

This view lists the _ID_, _name_, and building-name of each instructor in the university. Consider the following insertion through this view:

**insert into** _instructor info_ 
**values** (’69987’, ’White’, ’Taylor’);

Suppose there is no instructor with ID 69987, and no department in the Taylor building. Then the only possible method of inserting tuples into the _instructor_ and _department_ relations is to insert (’69987’, ’White’, _null_, _null_) into _instructor_ and (_null_, ’Taylor’, _null_) into _department_. Then, we obtain the relations shown in Figure 4.7. However, this update does not have the desired effect, since the view relation _instructor info_ still does _not_ include the tuple (’69987’, ’White’, ’Taylor’). Thus, there is no way to update the relations _instructor_ and _department_ by using nulls to get the desired update on _instructor info_.

Because of problems such as these, modifications are generally not permitted on view relations, except in limited cases. Different database systems specify different conditions under which they permit updates on view relations; see the database system manuals for details. The general problem of database modification through views has been the subject of substantial research, and the bibliographic notes provide pointers to some of this research.

In general, an SQL view is said to be **updatable** (that is, inserts, updates or deletes can be applied on the view) if the following conditions are all satisfied by the query defining the view:

• The **from** clause has only one database relation.

• The **select** clause contains only attribute names of the relation, and does not have any expressions, aggregates, or **distinct** specification.

• Any attribute not listed in the **select** clause can be set to _null_; that is, it does not have a **not null** constraint and is not part of a primary key.

• The query does not have a **group by** or **having** clause.

Under these constraints, the **update**, **insert**, and **delete** operations would be allowed on the following view:

**create view** _history instructors_ **as** 
**select** \* **from** _instructor_ 
**where** _dept name_\= ’History’;  


![Alt text](image-35.png)

**Figure 4.7** Relations _instructor_ and _department_ after insertion of tuples.

Even with the conditions on updatability, the following problem still remains. Suppose that a user tries to insert the tuple (’25566’, ’Brown’, ’Biology’, 100000) into the _history instructors_ view. This tuple can be inserted into the _instructor_ relation, but it would not appear in the _history instructors_ view since it does not satisfy the selection imposed by the view.

By default, SQL would allow the above update to proceed. However, views can be defined with a **with check option** clause at the end of the view definition; then, if a tuple inserted into the view does not satisfy the view’s **where** clause condition, the insertion is rejected by the database system. Updates are similarly rejected if the new value does not satisfy the **where** clause conditions.

SQL:1999 has a more complex set of rules about when inserts, updates, and deletes can be executed on a view, that allows updates through a larger class of views; however, the rules are too complex to be discussed here.  

