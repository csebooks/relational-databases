---
title: 'Snapshot Isolation' 
weight: 7
references:
    videos:
        - youtube: 9NVu17LjPSA
        -  youtube:fLEztcGPUA
    links:
        - https://en.wikipedia.org/wiki/Snapshot_isolation
        - https://www.geeksforgeeks.org/what-is-snapshot-isolation/
    books:
        - b1:
            title: Sql Server 2005 Black Book
            url: https://www.google.co.in/books/edition/Sql_Server_2005_Black_Book_New_Ed_With_C/Klj20GNs5egC?hl=en&gbpv=0
        - b2:
            title: Microsoft SQL Server Notes for Professionals book
            url: https://www.google.co.in/books/edition/Microsoft_SQL_Server_Notes_for_Professio/1deyEAAAQBAJ?hl=en&gbpv=0
---

# Snapshot Isolation

Snapshot isolation is a particular type of concurrency-control scheme that has gained wide acceptance in commercial and open-source systems, including Ora- cle, PostgreSQL, and SQL Server. We introduced snapshot isolation in Section 14.9.3. Here, we take a more detailed look into how it works.

Conceptually, snapshot isolation involves giving a transaction a “snapshot” of the database at the time when it begins its execution. It then operates on that snap- shot in complete isolation from concurrent transactions. The data values in the snapshot consist only of values written by committed transactions. This isolation is ideal for read-only transactions since they never wait and are never aborted by the concurrency manager. Transactions that update the database must, of course, interact with potentially conflicting concurrent update transactions before up- dates are actually placed in the database. Updates are kept in the transaction’s private workspace until the transaction successfully commits, at which point the updates are written to the database. When a transaction _T_ is allowed to commit, the transition of _T_ to the committed state and the writing of all of the updates made by _T_ to the database must be done as an atomic action so that any snapshot created for another transaction either includes all updates by transaction _T_ or none of them.

## Validation Steps for Update Transactions

Deciding whether or not to allow an update transaction to commit requires some care. Potentially, two transactions running concurrently might both update the same data item. Since these two transactions operate in isolation using their own private snapshots, neither transaction sees the update made by the other. If both transactions are allowed to write to the database, the first update written will be overwritten by the second. The result is a **lost update**. Clearly, this must be prevented. There are two variants of snapshot isolation, both of which prevent lost updates. They are called _first committer wins_ and _first updater wins_. Both approaches are based on testing the transaction against concurrent transactions. A transaction is said to be **concurrent with** _T_ if it was active or partially committed at any point from the start of _T_ up to and including the time when this test is being performed.

Under **first committer wins**, when a transaction _T_ enters the partially com- mitted state, the following actions are taken in an atomic action:  


• A test is made to see if any transaction that was concurrent with _T_ has already written an update to the database for some data item that _T_ intends to write.

• If some such transaction is found, then _T_ aborts.

• If no such transaction is found, then _T_ commits and its updates are written to the database.

This approach is called “first committer wins” because if transactions conflict, the first one to be tested using the above rule succeeds in writing its updates, while the subsequent ones are forced to abort. Details of how to implement the above tests are addressed in Exercise 15.19.

Under **first updater wins** the system uses a locking mechanism that applies only to updates (reads are unaffected by this, since they do not obtain locks). When a transaction _Ti_ attempts to update a data item, it requests a _write lock_ on that data item. If the lock is not held by a concurrent transaction, the following steps are taken after the lock is acquired:

• If the item has been updated by any concurrent transaction, then _Ti_ aborts.

• Otherwise _Ti_ may proceed with its execution including possibly committing.

If, however, some other concurrent transaction _Tj_ already holds a write lock on that data item, then _Ti_ cannot proceed and the following rules are followed:

• _Ti_ waits until _Tj_ aborts or commits.

◦ If _Tj_ aborts, then the lock is released and _Ti_ can obtain the lock. After the lock is acquired, the check for an update by a concurrent transaction is performed as described earlier: _Ti_ aborts if a concurrent transaction had updated the data item, and proceeds with its execution otherwise.

◦ If _Tj_ commits, then _Ti_ must abort.

Locks are released when the transaction commits or aborts. This approach is called “first updater wins” because if transactions conflict,

the first one to obtain the lock is the one that is permitted to commit and perform its update. Those that attempt the update later abort unless the first updater subsequently aborts for some other reason. (As an alternative to waiting to see if the first updater _Tj_ aborts, a subsequent updater _Ti_ can be aborted as soon as it finds that the write lock it wishes to obtain is held by _Tj_ .)

## Serializability Issues**

Snapshot isolation is attractive in practice because the overhead is low and no aborts occur unless two concurrent transactions update the same data item.

There is, however, one serious problem with the snapshot isolation scheme as we have presented it, and as it is implemented in practice: _snapshot isolation does_ **not** _ensure serializability_. This is true even in Oracle, which uses snapshot isolation  


as the implementation for the serializable isolation level! Next, we give examples of possible nonserializable executions under snapshot isolation and show how to deal with them.

**1\.** Suppose that we have two concurrent transactions _Ti_ and _Tj_ and two data items _A_ and _B_. Suppose that _Ti_ reads _A_ and _B_, then updates _B_, while _Tj_ reads _A_ and _B_, then updates _A_. For simplicity, we assume there are no other concurrent transactions. Since _Ti_ and _Tj_ are concurrent, neither transaction sees the update by the other in its snapshot. But, since they update different data items, both are allowed to commit regardless of whether the system uses the first-update-wins policy or the first-committer-wins policy.

However, the precedence graph has a cycle. There is an edge in the precedence graph from _Ti_ to _Tj_ because _Ti_ reads the value of _A_ that existed before _Tj_ writes _A_. There is also an edge in the precedence graph from _Tj_ to _Ti_ because _Tj_ reads the value of _B_ that existed before _Ti_ writes _B_. Since there is a cycle in the precedence graph, the result is a nonserializable schedule.

This situation, where each of a pair of transactions has read data that is written by the other, but there is no data written by both transactions, is referred to as **write skew**. As a concrete example of write skew, consider a banking scenario. Suppose that the bank enforces the integrity constraint that the sum of the balances in the checking and the savings account of a customer must not be negative. Suppose the checking and savings balances for a customer are $100 and $200, respectively. Suppose that transaction _T_36 withdraws $200 from the checking account, after verifying the integrity con- straint by reading both balances. Suppose that concurrently transaction _T_37 withdraws $200 from the savings account, again after verifying the integrity constraint. Since each of the transactions checks the integrity constraint on its own snapshot, if they run concurrently each will believe that the sum of the balances after the withdrawal is $100, and therefore its withdrawal does not violate the constraint. Since the two transactions update different data items, they do not have any update conflict, and under snapshot isolation both of them can commit.

Unfortunately, in the final state after both _T_36 and _T_37 have committed, the sum of the balances is $-100, violating the integrity constraint. Such a violation could never have occurred in any serial execution of _T_36 and _T_37.

It is worth noting that integrity constraints that are enforced by the database, such as primary-key and foreign-key constraints, cannot be check- ed on a snapshot; otherwise it would be possible for two concurrent trans- actions to insert two tuples with the same primary key value, or for a transaction to insert a foreign key value that is concurrently deleted from the referenced table. Instead, the database system must check these con- straints on the current state of the database, as part of validation at the time of commit.

**2\.** For the next example, we shall consider two concurrent update transactions that do not themselves present any problem as regards serializability unless  


a read-only transaction happens to show up at just the right time to cause a problem.

Suppose that we have two concurrent transactions _Ti_ and _Tj_ and two data items _A_and _B_. Suppose that _Ti_ reads _B_ and then updates _B_, while _Tj_ reads _A_ and _B_, then updates _A_. Running these two transactions concurrently causes no problem. Since _Ti_ accesses only data item _B_, there are no conflicts on data item _A_ and therefore there is no cycle in the precedence graph. The only edge in the precedence graph is the edge from _Tj_ to _Ti_ because _Tj_ reads the value of _B_ that existed before _Ti_ writes _B_.

However, let us suppose that _Ti_ commits while _Tj_ is still active. Suppose that, after _Ti_ commits but before _Tj_ commits, a new read-only transaction _Tk_ enters the system and _Tk_ reads both _A_ and _B_. Its snapshot includes the update by _Ti_ because _Ti_ has already committed. However, since _Tj_ has not committed, its update has not yet been written to the database and is not included in the snapshot seen by _Tk_ .

Consider the edges that are added to the precedence graph on account of _Tk_ . There is an edge in the precedence graph from _Ti_ to _Tk_ because _Ti_ writes the value of _B_ that existed before _Tk_ reads _B_. There is an edge in the precedence graph from _Tk_ to _Tj_ because _Tk_ reads the value of _A_ that existed before _Tj_ writes _A_. That leads to a cycle in the precedence graph, showing that the resulting schedule is nonserializable.

The above anomalies may not be as troublesome as they first appear. Recall that the reason for serializability is to ensure that, despite concurrent execution of transactions, database consistency is preserved. Since consistency is the goal, we can accept the potential for nonserializable executions if we are sure that those nonserializable executions that might occur will not lead to inconsistency. The second example above is a problem only if the application that submits the read-only transaction (_Tk_) cares about seeing updates to _A_ and _B_ out of order. In that example, we did not specify the database consistency constraints that each transaction expects to hold. If we are dealing with a financial database, it might be a very serious matter for _Tk_ to read updates out of proper serial order. On the other hand, if _A_ and _B_ are enrollments in two sections of the same course, then _Tk_ may not demand perfect serialization and we may know from our applications that update rates are low enough that any inaccuracy in what _Tk_ reads is not significant.

The fact that the database must check integrity constraints at the time of com- mit, and not on a snapshot, also helps avoid inconsistencies in some situations. Some financial applications create consecutive sequence numbers, for example to number bills, by taking the maximum current bill number and adding 1 to the value to get a new bill number. If two such transactions run concurrently, each would see the same set of bills in its snapshot, and each would create a new bill with the same number. Both transactions pass the validation tests for snapshot isolation, since they do not update any tuple in common. However, the execution is not serializable; the resultant database state cannot be obtained by any serial  


execution of the two transactions. Creating two bills with the same number could have serious legal implications.

The above problem is an example of the phantom phenomenon, since the insert performed by each transaction conflicts with the read performed by the other transaction to find the maximum bill number, but the conflict is not detected by snapshot isolation.1

Luckily, in most such applications the bill number would have been declared as a primary key, and the database system would detect the primary key violation outside the snapshot, and roll back one of the two transactions.2

An application developer can guard against certain snapshot anomalies by appending a **for update** clause to the SQL select query as illustrated below:

**select** \* **from** _instructor_ **where** ID = 22222 **for update**;

Adding the **for update** clause causes the system to treat data that are read as if they had been updated for purposes of concurrency control. In our first example of write skew, if the **for update** clause is appended to the select queries that read the account balances, only one of the two concurrent transactions would be allowed to commit since it appears that both transactions have updated both the checking and savings balances.

In our second example of nonserializable execution, if the author of transac- tion _Tk_ wished to avoid this anomaly, the **for update** clause could be appended to the **select** query, even though there is in fact no update. In our example, if _Tk_ used **select for update**, it would be treated as if it had updated _A_ and _B_ when it read them. The result would be that either _Tk_ or _Tj_ would be aborted, and retried later as a new transaction. This would lead to a serializable execution. In this example, the queries in the other two transactions do not need the **for update** clause to be added; unnecessary use of the **for update** clause can cause significant reduction in concurrency.

Formal methods exist (see the bibliographical notes) to determine whether a given mix of transactions runs the risk of nonserializable execution under snap- shot isolation, and to decide on what conflicts to introduce (using the **for update** clause, for example), to ensure serializability. Of course, such methods can work only if we know in advance what transactions are being executed. In some ap- plications, all transactions are from a predetermined set of transactions making this analysis possible. However, if the application allows unrestricted, ad-hoc transactions, then no such analysis is possible.

1The SQL standard uses the term phantom problem to refer to non-repeatable predicate reads, leading some to claim that snapshot isolation avoids the phantom problem; however, such a claim is not valid under our definition of phantom conflict. 2The problem of duplicate bill numbers actually occurred several times in a financial application in I.I.T. Bombay, where (for reasons too complex to discuss here) the bill number was not a primary key, and was detected by financial auditors.  


Of the three widely used systems that support snapshot isolation, SQL Server offers the option of a _serializable_ isolation level that truly ensures serializability along with a _snapshot_ isolation level that provides the performance advantages of snapshot isolation (along with the potential for the anomalies discussed above). In Oracle and PostgreSQL, the _serializable_ isolation level offers only snapshot iso- lation.