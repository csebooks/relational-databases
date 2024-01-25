---
title: 'Index Definition in SQL'
weight: 10
---

# Index Definition in SQL

The SQL standard does not provide any way for the database user or administra- tor to control what indices are created and maintained in the database system. Indices are not required for correctness, since they are redundant data structures. However, indices are important for efficient processing of transactions, includ- ing both update transactions and queries. Indices are also important for efficient enforcement of integrity constraints.

In principle, a database system can decide automatically what indices to create. However, because of the space cost of indices, as well as the effect of indices on update processing, it is not easy to automatically make the right choices about what indices to maintain. Therefore, most SQL implementations provide the programmer control over creation and removal of indices via data-definition- language commands.

We illustrate the syntax of these commands next. Although the syntax that we show is widely used and supported by many database systems, it is not part of the SQL standard. The SQL standard does not support control of the physical database schema; it restricts itself to the logical database schema.

We create an index with the **create index** command, which takes the form:  


**create index** _<_index-name_\>_ **on** _<_relation-name_\>_ (_<_attribute-list_\>_);

The _attribute-list_ is the list of attributes of the relations that form the search key for the index.

To define an index named _dept index_ on the _instructor_ relation with _dept name_ as the search key, we write:

**create index** _dept index_ **on** _instructor_ (_dept name_);

If we wish to declare that the search key is a candidate key, we add the attribute **unique** to the index definition. Thus, the command:

**create unique index** _dept index_ **on** _instructor_ (_dept name_);

declares _dept name_ to be a candidate key for _instructor_ (which is probably not what we actually would want for our university database). If, at the time we enter the **create unique index** command, _dept name_ is not a candidate key, the system will display an error message, and the attempt to create the index will fail. If the index-creation attempt succeeds, any subsequent attempt to insert a tuple that violates the key declaration will fail. Note that the **unique** feature is redundant if the database system supports the **unique** declaration of the SQL standard.

Many database systems also provide a way to specify the type of index to be used (such as B+-tree or hashing). Some database systems also permit one of the indices on a relation to be declared to be clustered; the system then stores the relation sorted by the search-key of the clustered index.

The index name we specified for an index is required to drop an index. The **drop index** command takes the form:

**drop index** _<_index-name_\>_;

