---
title: 'Multiple-Key Access'
weight: 5
---

# Multiple-Key Access

Until now, we have assumed implicitly that only one index on one attribute is used to process a query on a relation. However, for certain types of queries, it is advantageous to use multiple indices if they exist, or to use an index built on a multiattribute search key.

## Using Multiple Single-Key Indices

Assume that the _instructor_ file has two indices: one for _dept name_ and one for _salary_. Consider the following query: “Find all instructors in the Finance department with salary equal to $80,000.” We write

**select** _ID_ 
**from** _instructor_ 
**where** _dept name_ \=’Finance’ **and** _salary_\= 80000;

There are three strategies possible for processing this query:

**1\.** Use the index on _dept name_ to find all records pertaining to the Finance department. Examine each such record to see whether _salary_\= 80000.

**2\.** Use the index on _salary_ to find all records pertaining to instructors with salary of $80,000. Examine each such record to see whether the department name is “Finance”.

**3\.** Use the index on _dept name_ to find _pointers_ to all records pertaining to the Finance department. Also, use the index on _salary_ to find pointers to all records pertaining to instructors with a salary of $80,000. Take the intersec- tion of these two sets of pointers. Those pointers that are in the intersection point to records pertaining to instructors of the Finance department and with salary of $80,000.

The third strategy is the only one of the three that takes advantage of the existence of multiple indices. However, even this strategy may be a poor choice if all of the following hold:

- There are many records pertaining to the Finance department.

- There are many records pertaining to instructors with a salary of $80,000.

- There are only a few records pertaining to _both_ the Finance department and instructors with a salary of $80,000.

If these conditions hold, we must scan a large number of pointers to produce a small result. An index structure called a “bitmap index” can in some cases greatly speed up the intersection operation used in the third strategy. Bitmap indices are outlined in Section 11.9.  

## Indices on Multiple Keys

An alternative strategy for this case is to create and use an index on a composite search key (_dept name_, _salary_)—that is, the search key consisting of the department name concatenated with the instructor salary.

We can use an ordered (B+-tree) index on the above composite search key to answer efficiently queries of the form

**select** _ID_ 
**from** _instructor_ 
**where** _dept name_ \= ’Finance’ **and** _salary_\= 80000;

Queries such as the following query, which specifies an equality condition on the first attribute of the search key (_dept name_) and a range on the second attribute of the search key (_salary_), can also be handled efficiently since they correspond to a range query on the search attribute.

**select** _ID_ 
**from** _instructor_ 
**where** _dept name_ \= ’Finance’ **and** _salary<_ 80000;

We can even use an ordered index on the search key (_dept name_, _salary_) to answer the following query on only one attribute efficiently:

**select** _ID_ 
**from** _instructor_ 
**where** _dept name_ \= ’Finance’;

An equality condition _dept name_ \= “Finance” is equivalent to a range query on the range with lower end (Finance, −∞) and upper end (Finance, +∞). Range queries on just the _dept name_ attribute can be handled in a similar manner.

The use of an ordered-index structure on a composite search key, however, has a few shortcomings. As an illustration, consider the query

**select** _ID_ 
**from** _instructor_ 
**where** _dept name <_ ’Finance’ **and** _salary<_ 80000;

We can answer this query by using an ordered index on the search key (_dept name_, _salary_): For each value of _dept name_ that is less than “Finance” in alphabetic

order, the system locates records with a _salary_ value of 80000. However, each record is likely to be in a different disk block, because of the ordering of records in the file, leading to many I/O operations.

The difference between this query and the previous two queries is that the condition on the first attribute (_dept name_) is a comparison condition, rather than an equality condition. The condition does not correspond to a range query on the search key.

To speed the processing of general composite search-key queries (which can involve one or more comparison operations), we can use several special struc- tures. We shall consider _bitmap indices_ in Section 11.9. There is another structure, called the _R-tree_, that can be used for this purpose. The R-tree is an extension of the B+-tree to handle indexing on multiple dimensions. Since the R-tree is used primarily with geographical data types, we describe the structure in Chapter 25.

## Covering Indices

**Covering indices** are indices that store the values of some attributes (other than the search-key attributes) along with the pointers to the record. Storing extra attribute values is useful with secondary indices, since they allow us to answer some queries using just the index, without even looking up the actual records.

For example, suppose that we have a nonclustering index on the _ID_ attribute of the _instructor_ relation. If we store the value of the _salary_ attribute along with the record pointer, we can answer queries that require the salary (but not the other attribute, _dept name_) without accessing the _instructor_ record.

The same effect could be obtained by creating an index on the search key (_ID_, _salary_), but a covering index reduces the size of the search key, allowing a larger fanout in the nonleaf nodes, and potentially reducing the height of the index.

