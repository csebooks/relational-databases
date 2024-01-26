---
title: 'A Simple Transaction Model'
weight: 2
---

# A Simple Transaction Model

Because SQL is a powerful and complex language, we begin our study of transac- tions with a simple database language that focuses on when data are moved from disk to main memory and from main memory to disk. In doing this, we ignore SQL **insert** and **delete** operations, and defer considering them until Section 15.8. The only actual operations on the data are restricted in our simple language to arithmetic operations. Later we shall discuss transactions in a realistic, SQL-based context with a richer set of operations. The data items in our simplified model con- tain a single data value (a number in our examples). Each data item is identified by a name (typically a single letter in our examples, that is, _A_, _B_, _C_ , etc.).

We shall illustrate the transaction concept using a simple bank application consisting of several accounts and a set of transactions that access and update those accounts. Transactions access data using two operations:

• read(_X_), which transfers the data item _X_ from the database to a variable, also called _X_, in a buffer in main memory belonging to the transaction that executed the read operation.

• write(_X_), which transfers the value in the variable _X_ in the main-memory buffer of the transaction that executed the write to the data item _X_ in the database.

It is important to know if a change to a data item appears only in main memory or if it has been written to the database on disk. In a real database system, the write operation does not necessarily result in the immediate update of the data on the disk; the write operation may be temporarily stored elsewhere and executed on the disk later. For now, however, we shall assume that the write operation updates the database immediately. We shall return to this subject in Chapter 16.

Let _Ti_ be a transaction that transfers $50 from account _A_ to account _B_. This transaction can be defined as:

   Ti : read(_A_);
        A_ := _A_ − 50;
        write(_A_); 
        read(_B_);
        B_ := _B_ \+ 50;
        write(_B_). 


Let us now consider each of the ACID properties. (For ease of presentation, we consider them in an order different from the order A-C-I-D.)

• **Consistency**: The consistency requirement here is that the sum of _A_ and _B_ be unchanged by the execution of the transaction. Without the consistency requirement, money could be created or destroyed by the transaction! It can be verified easily that, if the database is consistent before an execution of the transaction, the database remains consistent after the execution of the transaction.

Ensuring consistency for an individual transaction is the responsibility of the application programmer who codes the transaction. This task may be facilitated by automatic testing of integrity constraints, as we discussed in Section 4.4.

• **Atomicity**: Suppose that, just before the execution of transaction _Ti_ , the val- ues of accounts _A_ and _B_ are $1000 and $2000, respectively. Now suppose that, during the execution of transaction _Ti_ , a failure occurs that prevents _Ti_ from completing its execution successfully. Further, suppose that the failure happened after the write(_A_) operation but before the write(_B_) operation. In this case, the values of accounts _A_ and _B_ reflected in the database are $950 and $2000. The system destroyed $50 as a result of this failure. In particular, we note that the sum _A_ \+ _B_ is no longer preserved.

Thus, because of the failure, the state of the system no longer reflects a real state of the world that the database is supposed to capture. We term such a state an **inconsistent state**. We must ensure that such inconsistencies are not visible in a database system. Note, however, that the system must at some point be in an inconsistent state. Even if transaction _Ti_ is executed to completion, there exists a point at which the value of account _A_ is $950 and the value of account _B_ is $2000, which is clearly an inconsistent state. This state, however, is eventually replaced by the consistent state where the value of account _A_ is $950, and the value of account _B_ is $2050. Thus, if the transaction never started or was guaranteed to complete, such an inconsistent state would not be visible except during the execution of the transaction. That is the reason for the atomicity requirement: If the atomicity property is present, all actions of the transaction are reflected in the database, or none are.

The basic idea behind ensuring atomicity is this: The database system keeps track (on disk) of the old values of any data on which a transaction performs a write. This information is written to a file called the _log_. If the transaction does not complete its execution, the database system restores the old values from the log to make it appear as though the transaction never executed. We discuss these ideas further in Section 14.4. Ensuring atomicity is the responsibility of the database system; specifically, it is handled by a component of the database called the **recovery system**, which we describe in detail in Chapter 16.

• **Durability:** Once the execution of the transaction completes successfully, and the user who initiated the transaction has been notified that the transfer of  


funds has taken place, it must be the case that no system failure can result in a loss of data corresponding to this transfer of funds. The durability property guarantees that, once a transaction completes successfully, all the updates that it carried out on the database persist, even if there is a system failure after the transaction completes execution.

We assume for now that a failure of the computer system may result in loss of data in main memory, but data written to disk are never lost. Protection against loss of data on disk is discussed in Chapter 16. We can guarantee durability by ensuring that either:

**1\.** The updates carried out by the transaction have been written to disk before the transaction completes.

**2\.** Information about the updates carried out by the transaction and writ- ten to disk is sufficient to enable the database to reconstruct the updates when the database system is restarted after the failure.

The **recovery system** of the database, described in Chapter 16, is responsible for ensuring durability, in addition to ensuring atomicity.

• **Isolation:** Even if the consistency and atomicity properties are ensured for each transaction, if several transactions are executed concurrently, their op- erations may interleave in some undesirable way, resulting in an inconsistent state.

For example, as we saw earlier, the database is temporarily inconsistent while the transaction to transfer funds from _A_ to _B_ is executing, with the deducted total written to _A_ and the increased total yet to be written to _B_. If a second concurrently running transaction reads _A_ and _B_ at this intermediate point and computes _A_\+ _B_, it will observe an inconsistent value. Furthermore, if this second transaction then performs updates on _A_ and _B_ based on the inconsistent values that it read, the database may be left in an inconsistent state even after both transactions have completed.

A way to avoid the problem of concurrently executing transactions is to execute transactions serially—that is, one after the other. However, concur- rent execution of transactions provides significant performance benefits, as we shall see in Section 14.5. Other solutions have therefore been developed; they allow multiple transactions to execute concurrently.

We discuss the problems caused by concurrently executing transactions in Section 14.5. The isolation property of a transaction ensures that the con- current execution of transactions results in a system state that is equivalent to a state that could have been obtained had these transactions executed one at a time in some order. We shall discuss the principles of isolation further in Section 14.6. Ensuring the isolation property is the responsibility of a com- ponent of the database system called the **concurrency-control system**, which we discuss later, in Chapter 15.  
