---
title: 'Insert Operations, Delete Operations, and Predicate Reads' 
weight: 8
---

# Insert Operations, Delete Operations, and Predicate Reads

Until now, we have restricted our attention to read and write operations. This restriction limits transactions to data items already in the database. Some trans- actions require not only access to existing data items, but also the ability to create new data items. Others require the ability to delete data items. To examine how such transactions affect concurrency control, we introduce these additional oper- ations:

• **delete**(_Q_) deletes data item _Q_ from the database.

• **insert**(_Q_) inserts a new data item _Q_ into the database and assigns _Q_ an initial value.

An attempt by a transaction _Ti_ to perform a read(_Q_) operation after _Q_ has been deleted results in a logical error in _Ti_ . Likewise, an attempt by a transaction _Ti_ to perform a read(_Q_) operation before _Q_ has been inserted results in a logical error in _Ti_ . It is also a logical error to attempt to delete a nonexistent data item.

## Deletion

To understand how the presence of **delete** instructions affects concurrency control, we must decide when a **delete** instruction conflicts with another instruction. Let _Ii_ and _I j_ be instructions of _Ti_ and _Tj_ , respectively, that appear in schedule _S_ in consecutive order. Let _Ii_ \= **delete**(_Q_). We consider several instructions _I j_ .

• _I j_ \= read(_Q_). _Ii_ and _I j_ conflict. If _Ii_ comes before _I j_ , _Tj_ will have a logical error. If _I j_ comes before _Ii_ , _Tj_ can execute the read operation successfully.

• _I j_ \= write(_Q_). _Ii_ and _I j_ conflict. If _Ii_ comes before _I j_ , _Tj_ will have a logical error. If _I j_ comes before _Ii_ , _Tj_ can execute the write operation successfully.

• _I j_ \= **delete**(_Q_). _Ii_ and _I j_ conflict. If _Ii_ comes before _I j_ , _Ti_ will have a logical error. If _I j_ comes before _Ii_ , _Ti_ will have a logical error.

• _I j_ \= **insert**(_Q_). _Ii_ and _I j_ conflict. Suppose that data item _Q_ did not exist prior to the execution of _Ii_ and _I j_ . Then, if _Ii_ comes before _I j_ , a logical error results for _Ti_ . If _I j_ comes before _Ii_ , then no logical error results. Likewise, if _Q_ existed prior to the execution of _Ii_ and _I j_ , then a logical error results if _I j_ comes before _Ii_ , but not otherwise.  


We can conclude the following:

• Under the two-phase locking protocol, an exclusive lock is required on a data item before that item can be deleted.

• Under the timestamp-ordering protocol, a test similar to that for a write must be performed. Suppose that transaction _Ti_ issues **delete**(_Q_).

◦ If TS(_Ti_ ) _<_ R-timestamp(_Q_), then the value of _Q_ that _Ti_ was to delete has already been read by a transaction _Tj_ with TS(_Tj_ ) _\>_ TS(_Ti_ ). Hence, the **delete** operation is rejected, and _Ti_ is rolled back.

◦ If TS(_Ti_ ) _<_ W-timestamp(_Q_), then a transaction _Tj_ with TS(_Tj_ ) _\>_ TS(_Ti_ ) has written _Q_. Hence, this **delete** operation is rejected, and _Ti_ is rolled back.

◦ Otherwise, the **delete** is executed.

## Insertion

We have already seen that an **insert**(_Q_) operation conflicts with a **delete**(_Q_) opera- tion. Similarly, **insert**(_Q_) conflicts with a read(_Q_) operation or a write(_Q_) operation; no read or write can be performed on a data item before it exists.

Since an **insert**(_Q_) assigns a value to data item _Q_, an **insert** is treated similarly to a write for concurrency-control purposes:

• Under the two-phase locking protocol, if _Ti_ performs an **insert**(_Q_) operation, _Ti_ is given an exclusive lock on the newly created data item _Q_.

• Under the timestamp-ordering protocol, if _Ti_ performs an **insert**(_Q_) operation, the values R-timestamp(_Q_) and W-timestamp(_Q_) are set to TS(_Ti_ ).

## Predicate Reads and The Phantom Phenomenon

Consider transaction _T_30 that executes the following SQL query on the university database:

**select count**(\*) **from** _instructor_ **where** _dept name_ \= ’Physics’ ;

Transaction _T_30 requires access to all tuples of the _instructor_ relation pertaining to the Physics department.

Let _T_31 be a transaction that executes the following SQL insertion:

**insert into** _instructor_ **values** (11111,’Feynman’, ’Physics’, 94000);

Let _S_ be a schedule involving _T_30 and _T_31\. We expect there to be potential for a conflict for the following reasons:  


• If _T_30 uses the tuple newly inserted by _T_31 in computing **count**(\*), then _T_30 reads a value written by _T_31\. Thus, in a serial schedule equivalent to _S_, _T_31 must come before _T_30.

• If _T_30 does not use the tuple newly inserted by _T_31 in computing **count**(\*), then in a serial schedule equivalent to _S_, _T_30 must come before _T_31.

The second of these two cases is curious. _T_30 and _T_31 do not access any tuple in common, yet they conflict with each other! In effect, _T_30 and _T_31 conflict on a phantom tuple. If concurrency control is performed at the tuple granularity, this conflict would go undetected. As a result, the system could fail to prevent a nonserializable schedule. This problem is called the **phantom phenomenon**.

In addition to the phantom problem, we also need to deal with the situation we saw in Section 14.10, where a transaction used an index to find only tuples with _dept name_ \= “Physics”, and as a result did not read any tuples with other department names. If another transaction updates one of these tuples, changing its department name to Physics, a problem equivalent to the phantom problem occurs. Both problems are rooted in predicate reads, and have a common solution.

To prevent the above problems, we allow transaction _T_30 to prevent other transactions from creating new tuples in the _instructor_ relation with _dept name_ \= “Physics”, and from updating the department name of an existing _instructor_ tuple to Physics.

To find all _instructor_ tuples with _dept name_ \= “Physics”, _T_30 must search either the whole _instructor_ relation, or at least an index on the relation. Up to now, we have assumed implicitly that the only data items accessed by a transaction are tuples. However, _T_30 is an example of a transaction that reads information about what tuples are in a relation, and _T_31 is an example of a transaction that updates that information.

Clearly, it is not sufficient merely to lock the tuples that are accessed; the information used to find the tuples that are accessed by the transaction must also be locked.

Locking of information used to find tuples can be implemented by associating a data item with the relation; the data item represents the information used to find the tuples in the relation. Transactions, such as _T_30, that read the information about what tuples are in a relation would then have to lock the data item corre- sponding to the relation in shared mode. Transactions, such as _T_31, that update the information about what tuples are in a relation would have to lock the data item in exclusive mode. Thus, _T_30 and _T_31 would conflict on a real data item, rather than on a phantom. Similarly, transactions that use an index to retrieve tuples must lock the index itself.

Do not confuse the locking of an entire relation, as in multiple-granularity locking, with the locking of the data item corresponding to the relation. By locking the data item, a transaction only prevents other transactions from updating infor- mation about what tuples are in the relation. Locking is still required on tuples. A transaction that directly accesses a tuple can be granted a lock on the tuples even  


when another transaction has an exclusive lock on the data item corresponding to the relation itself.

The major disadvantage of locking a data item corresponding to the relation, or locking an entire index, is the low degree of concurrency— two transactions that insert different tuples into a relation are prevented from executing concurrently.

A better solution is an **index-locking** technique that avoids locking the whole index. Any transaction that inserts a tuple into a relation must insert information into every index maintained on the relation. We eliminate the phantom phe- nomenon by imposing a locking protocol for indices. For simplicity we shall consider only B+-tree indices.

As we saw in Chapter 11, every search-key value is associated with an index leaf node. A query will usually use one or more indices to access a relation. An insert must insert the new tuple in all indices on the relation. In our example, we assume that there is an index on _instructor_ for _dept name_. Then, _T_31 must modify the leaf containing the key “Physics”. If _T_30 reads the same leaf node to locate all tuples pertaining to the Physics department, then _T_30 and _T_31 conflict on that leaf node.

The **index-locking protocol** takes advantage of the availability of indices on a relation, by turning instances of the phantom phenomenon into conflicts on locks on index leaf nodes. The protocol operates as follows:

• Every relation must have at least one index.

• A transaction _Ti_ can access tuples of a relation only after first finding them through one or more of the indices on the relation. For the purpose of the index-locking protocol, a relation scan is treated as a scan through all the leaves of one of the indices.

• A transaction _Ti_ that performs a lookup (whether a range lookup or a point lookup) must acquire a shared lock on all the index leaf nodes that it accesses.

• A transaction _Ti_ may not insert, delete, or update a tuple _ti_ in a relation _r_ without updating all indices on _r_. The transaction must obtain exclusive locks on all index leaf nodes that are affected by the insertion, deletion, or update. For insertion and deletion, the leaf nodes affected are those that contain (after insertion) or contained (before deletion) the search-key value of the tuple. For updates, the leaf nodes affected are those that (before the modification) contained the old value of the search key, and nodes that (after the modification) contain the new value of the search key.

• Locks are obtained on tuples as usual.

• The rules of the two-phase locking protocol must be observed.

Note that the index-locking protocol does not address concurrency control on internal nodes of an index; techniques for concurrency control on indices, which minimize lock conflicts, are presented in Section 15.10.

Locking an index leaf node prevents any update to the node, even if the update did not actually conflict with the predicate. A variant called key-value  


locking, which minimizes such false lock conflicts, is presented in Section 15.10 as part of index concurrency control.

As noted in Section 14.10, it would appear that the existence of a conflict between transactions depends on a low-level query-processing decision by the system that is unrelated to a user-level view of the meaning of the two transac- tions. An alternative approach to concurrency control acquires shared locks on predicates in a query, such as the predicate “_salary >_ 90000” on the _instructor_ relation. Inserts and deletes of the relation must then be checked to see if they satisfy the predicate; if they do, there is a lock conflict, forcing the insert or delete to wait till the predicate lock is released. For updates, both the initial value and the final value of the tuple must be checked against the predicate. Such conflict- ing inserts, deletes and updates affect the set of tuples selected by the predicate, and cannot be allowed to execute concurrently with the query that acquired the (shared) predicate lock. We call the above protocol **predicate locking**;3 predicate locking is not used in practice since it is more expensive to implement than the index-locking protocol, and does not give significant additional benefits.

Variants of the predicate-locking technique can be used for eliminating the phantom phenomenon under the other concurrency-control protocols presented in this chapter. However, many database systems, such as PostgreSQL (as of ver- sion 8.1) and (to the best of our knowledge) Oracle (as of version 10g) do not implement index locking or predicate locking, and are vulnerable to nonserializ- ability due to phantom problems even if the isolation level is set to **serializable**.