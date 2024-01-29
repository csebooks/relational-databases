---
title: 'Recovery and Atomicity'
weight: 3
---

# Recovery and Atomicity

Consider again our simplified banking system and a transaction _Ti_ that transfers $50 from account _A_ to account _B_, with initial values of _A_ and _B_ being $1000 and $2000, respectively. Suppose that a system crash has occurred during the execution of _Ti_ , after output(_BA_) has taken place, but before output(_BB_) was executed, where _BA_ and _BB_ denote the buffer blocks on which _A_ and _B_ reside. Since the memory contents were lost, we do not know the fate of the transaction.

When the system restarts, the value of _A_ would be $950, while that of _B_ would be $2000, which is clearly inconsistent with the atomicity requirement for transaction _Ti_ . Unfortunately, there is no way to find out by examining the database state what blocks had been output, and what had not, before the crash. It is possible that the transaction completed, updating the database on stable storage from an initial state with the values of _A_ and _B_ being $1000 and $1950; it is also possible that the transaction did not affect the stable storage at all, and the values of _A_ and _B_ were $950 and $2000 initially; or that the updated _B_ was output but not the updated _A_; or that the updated _A_ was output but the updated _B_ was not.

Our goal is to perform either all or no database modifications made by _Ti_ . However, if _Ti_ performed multiple database modifications, several output opera- tions may be required, and a failure may occur after some of these modifications have been made, but before all of them are made.

To achieve our goal of atomicity, we must first output to stable storage infor- mation describing the modifications, without modifying the database itself. As we shall see, this information can help us ensure that all modifications performed by committed transactions are reflected in the database (perhaps during the course of recovery actions after a crash). This information can also help us ensure that no modifications made by an aborted transaction persist in the database.

## Log Records

The most widely used structure for recording database modifications is the **log**. The log is a sequence of **log records**, recording all the update activities in the database.

There are several types of log records. An **update log record** describes a single database write. It has these fields:  


**SHADOW COPIES AND SHADOW PAGING**

In the **shadow-copy** scheme, a transaction that wants to update the database first creates a complete copy of the database. All updates are done on the new database copy, leaving the original copy, the **shadow copy**, untouched. If at any point the transaction has to be aborted, the system merely deletes the new copy. The old copy of the database has not been affected. The current copy of the database is identified by a pointer, called db-pointer, which is stored on disk.

If the transaction partially commits (that is, executes its final statement) it is committed as follows: First, the operating system is asked to make sure that all pages of the new copy of the database have been written out to disk. (Unix systems use the fsync command for this purpose.) After the operating system has written all the pages to disk, the database system updates the pointer db- pointer to point to the new copy of the database; the new copy then becomes the current copy of the database. The old copy of the database is then deleted. The transaction is said to have been _committed_ at the point where the updated db-pointer is written to disk.

The implementation actually depends on the write to db-pointer being atomic; that is, either all its bytes are written or none of its bytes are written. Disk sys- tems provide atomic updates to entire blocks, or at least to a disk sector. In other words, the disk system guarantees that it will update db-pointer atomically, as long as we make sure that db-pointer lies entirely in a single sector, which we can ensure by storing db-pointer at the beginning of a block.

Shadow copy schemes are commonly used by text editors (saving the file is equivalent to transaction commit, while quitting without saving the file is equiv- alent to transaction abort). Shadow copying can be used for small databases, but copying a large database would be extremely expensive. A variant of shadow- copying, called **shadow-paging**, reduces copying as follows: the scheme uses a page table containing pointers to all pages; the page table itself and all updated pages are copied to a new location. Any page which is not updated by a trans- action is not copied, but instead the new page table just stores a pointer to the original page. When a transaction commits, it atomically updates the pointer to the page table, which acts as db-pointer, to point to the new copy.

Shadow paging unfortunately does not work well with concurrent transac- tions and is not widely used in databases.

• **Transaction identifier,** which is the unique identifier of the transaction that performed the write operation.

• **Data-item identifier,** which is the unique identifier of the data item written. Typically, it is the location on disk of the data item, consisting of the block identifier of the block on which the data item resides, and an offset within the block.

• **Old value**, which is the value of the data item prior to the write.  

**728 Chapter 16 Recovery System**

• **New value**, which is the value that the data item will have after the write.

We represent an update log record as _<Ti, Xj , V_1_, V_2_\>_, indicating that transaction _Ti_ has performed a write on data item _Xj_ . _Xj_ had value _V_1 before the write, and has value _V_2 after the write. Other special log records exist to record significant events during transaction processing, such as the start of a transaction and the commit or abort of a transaction. Among the types of log records are:

• _<Ti_ start_\>_. Transaction _Ti_ has started.

• _<Ti_ commit_\>_. Transaction _Ti_ has committed.

• _<Ti_ abort_\>_. Transaction _Ti_ has aborted.

We shall introduce several other types of log records later. Whenever a transaction performs a write, it is essential that the log record

for that write be created and added to the log, before the database is modified. Once a log record exists, we can output the modification to the database if that is desirable. Also, we have the ability to _undo_ a modification that has already been output to the database. We undo it by using the old-value field in log records.

For log records to be useful for recovery from system and disk failures, the log must reside in stable storage. For now, we assume that every log record is written to the end of the log on stable storage as soon as it is created. In Section 16.5, we shall see when it is safe to relax this requirement so as to reduce the overhead imposed by logging. Observe that the log contains a complete record of all database activity. As a result, the volume of data stored in the log may become unreasonably large. In Section 16.3.6, we shall show when it is safe to erase log information.

## Database Modification

As we noted earlier, a transaction creates a log record prior to modifying the database. The log records allow the system to undo changes made by a transaction in the event that the transaction must be aborted; they allow the system also to redo changes made by a transaction if the transaction has committed but the system crashed before those changes could be stored in the database on disk. In order for us to understand the role of these log records in recovery, we need to consider the steps a transaction takes in modifying a data item:

**1\.** The transaction performs some computations in its own private part of main memory.

**2\.** The transaction modifies the data block in the disk buffer in main memory holding the data item.

**3\.** The database system executes the output operation that writes the data block to disk.  


We say a transaction _modifies the database_ if it performs an update on a disk buffer, or on the disk itself; updates to the private part of main memory do not count as database modifications. If a transaction does not modify the database until it has committed, it is said to use the **deferred-modification** technique. If database modifications occur while the transaction is still active, the transaction is said to use the **immediate-modification** technique. Deferred modification has the overhead that transactions need to make local copies of all updated data items; further, if a transaction reads a data item that it has updated, it must read the value from its local copy.

The recovery algorithms we describe in this chapter support immediate mod- ification. As described, they work correctly even with deferred modification, but can be optimized to reduce overhead when used with deferred modification; we leave details as an exercise.

A recovery algorithm must take into account a variety of factors, including:

• The possibility that a transaction may have committed although some of its database modifications exist only in the disk buffer in main memory and not in the database on disk.

• The possibility that a transaction may have modified the database while in the active state and, as a result of a subsequent failure, may need to abort.

Because all database modifications must be preceded by the creation of a log record, the system has available both the old value prior to the modification of the data item and the new value that is to be written for the data item. This allows the system to perform _undo_ and _redo_ operations as appropriate.

• **Undo** using a log record sets the data item specified in the log record to the old value.

• **Redo** using a log record sets the data item specified in the log record to the new value.

## Concurrency Control and Recovery

If the concurrency control scheme allows a data item _X_ that has been modified by a transaction _T_1 to be further modified by another transaction _T_2 before _T_1 commits, then undoing the effects of _T_1 by restoring the old value of _X_ (before _T_1 updated _X_) would also undo the effects of _T_2\. To avoid such situations, recovery algorithms usually require that if a data item has been modified by a transaction, no other transaction can modify the data item until the first transaction commits or aborts.

This requirement can be ensured by acquiring an exclusive lock on any up- dated data item and holding the lock until the transaction commits; in other words, by using strict two-phase locking. Snapshot-isolation and validation-      based concurrency-control techniques also acquire exclusive locks on data items at the time of validation, before modifying the data items, and hold the locks until the transaction is committed; as a result the above requirement is satisfied even by these concurrency control protocols.

We discuss later, in Section 16.7, how the above requirement can be relaxed in certain cases.

When either snapshot-isolation or validation is used for concurrency control, database updates of a transaction are (conceptually) deferred until the transac- tion is partially committed; the deferred-modification technique is a natural fit with these concurrency control schemes. However, it is worth noting that some implementations of snapshot isolation use immediate modification, but provide a logical snapshot on demand: when a transaction needs to read an item that a concurrent transaction has updated, a copy of the (already updated) item is made, and updates made by concurrent transactions are rolled back on the copy of the item. Similarly, immediate modification of the database is a natural fit with two-phase locking, but deferred modification can also be used with two-phase locking.

## Transaction Commit

We say that a transaction has **committed** when its commit log record, which is the last log record of the transaction, has been output to stable storage; at that point all earlier log records have already been output to stable storage. Thus, there is enough information in the log to ensure that even if there is a system crash, the updates of the transaction can be redone. If a system crash occurs before a log record _< Ti_ commit_\>_ is output to stable storage, transaction _Ti_ will be rolled back. Thus, the output of the block containing the commit log record is the single atomic action that results in a transaction getting committed.2

With most log-based recovery techniques, including the ones we describe in this chapter, blocks containing the data items modified by a transaction do not have to be output to stable storage when the transaction commits, but can be output some time later. We discuss this issue further in Section 16.5.2.

## Using the Log to Redo and Undo Transactions

We now provide an overview of how the log can be used to recover from a system crash, and to roll back transactions during normal operation. However, we postpone details of the procedures for failure recovery and rollback to Section 16.4.

Consider our simplified banking system. Let _T_0 be a transaction that transfers $50 from account _A_ to account _B_:

2The output of a block can be made atomic by techniques for dealing with data-transfer failure, as described earlier in Section 16.2.1.  



![](2.2.png)

**Figure 16.2** Portion of the system log corresponding to _T_0 and _T_1.

![](2.2.1.png)
Let _T_1 be a transaction that withdraws $100 from account _C_:

![](2.2.2.png)

The portion of the log containing the relevant information concerning these two transactions appears in Figure 16.2.

Figure 16.3 shows one possible order in which the actual outputs took place in both the database system and the log as a result of the execution of _T_0 and _T_1.3

Using the log, the system can handle any failure that does not result in the loss of information in nonvolatile storage. The recovery scheme uses two recovery procedures. Both these procedures make use of the log to find the set of data items updated by each transaction _Ti_ , and their respective old and new values.

• redo(_Ti_ ) sets the value of all data items updated by transaction _Ti_ to the new values.

The order in which updates are carried out by redo is important; when recovering from a system crash, if updates to a particular data item are applied in an order different from the order in which they were applied originally, the final state of that data item will have a wrong value. Most recovery algorithms, including the one we describe in Section 16.4, do not perform redo of each transaction separately; instead they perform a single scan of the log, during which redo actions are performed for each log record as it is encountered. This approach ensures the order of updates is preserved,

3Notice that this order could not be obtained using the deferred-modification technique, because the database is modified by _T_0 before it commits, and likewise for _T_1.  


**Log Database**

![](2.3.png)

**Figure 16.3** State of system log and database corresponding to _T_0 and _T_1.

and is more efficient since the log needs to be read only once overall, instead of once per transaction.

• undo(_Ti_ ) restores the value of all data items updated by transaction _Ti_ to the old values.

In the recovery scheme that we describe in Section 16.4:

◦ The undo operation not only restores the data items to their old value, but also writes log records to record the updates performed as part of the undo process. These log records are special **redo-only** log records, since they do not need to contain the old-value of the updated data item.

As with the redo procedure, the order in which undo operations are performed is important; again we postpone details to Section 16.4.

◦ When the undo operation for transaction _Ti_ completes, it writes a _<Ti_ abort_\>_ log record, indicating that the undo has completed.

As we shall see in Section 16.4, the undo(_Ti_ ) procedure is executed only once for a transaction, if the transaction is rolled back during normal processing or if on recovering from a system crash, neither a commit nor an abort record is found for transaction _Ti_ . As a result, every transaction will eventually have either a commit or an abort record in the log.

After a system crash has occurred, the system consults the log to determine which transactions need to be redone, and which need to be undone so as to ensure atomicity.

• Transaction _Ti_ needs to be undone if the log contains the record _<Ti_ start_\>_, but does not contain either the record _<Ti_ commit_\>_ or the record _<Ti_ abort_\>_.

• Transaction _Ti_ needs to be redone if the log contains the record _<Ti_ start_\>_ and either the record _<Ti_ commit_\>_ or the record _<Ti_ abort_\>_. It may seem strange to redo _Ti_ if the record _<Ti_ abort_\>_ is in the log. To see why this works, note  


![](2.4.png)
**Figure 16.4** The same log, shown at three different times.

that if _<Ti_ abort_\>_ is in the log, so are the redo-only records written by the undo operation. Thus, the end result will be to undo _Ti_ ’s modifications in this case. This slight redundancy simplifies the recovery algorithm and enables faster overall recovery time.

As an illustration, return to our banking example, with transaction _T_0 and _T_1 executed one after the other in the order _T_0 followed by _T_1\. Suppose that the system crashes before the completion of the transactions. We shall consider three cases. The state of the logs for each of these cases appears in Figure 16.4.

First, let us assume that the crash occurs just after the log record for the step:

write(_B_)

of transaction _T_0 has been written to stable storage (Figure 16.4a). When the system comes back up, it finds the record _<T_0 start_\>_ in the log, but no corresponding _<T_0 commit_\>_ or _<T_0 abort_\>_ record. Thus, transaction _T_0 must be undone, so an undo(_T_0) is performed. As a result, the values in accounts _A_ and _B_ (on the disk) are restored to $1000 and $2000, respectively.

Next, let us assume that the crash comes just after the log record for the step:

write(_C_)

of transaction _T_1 has been written to stable storage (Figure 16.4b). When the system comes back up, two recovery actions need to be taken. The operation undo(_T_1) must be performed, since the record _<T_1 start_\>_ appears in the log, but there is no record _<T_1 commit_\>_ or _<T_1 abort_\>_. The operation redo(_T_0) must be performed, since the log contains both the record _<T_0 start_\>_ and the record _<T_0 commit_\>_. At the end of the entire recovery procedure, the values of accounts _A_, _B_, and _C_ are $950, $2050, and $700, respectively.

Finally, let us assume that the crash occurs just after the log record:

_<T_1 commit_\>_  

has been written to stable storage (Figure 16.4c). When the system comes back up, both _T_0 and _T_1 need to be redone, since the records _<T_0 start_\>_ and _<T_0 commit_\>_ appear in the log, as do the records _<T_1 start_\>_ and _<T_1 commit_\>_. After the system performs the recovery procedures redo(_T_0) and redo(_T_1), the values in accounts _A_, _B_, and _C_ are $950, $2050, and $600, respectively.

## Checkpoints

When a system crash occurs, we must consult the log to determine those trans- actions that need to be redone and those that need to be undone. In principle, we need to search the entire log to determine this information. There are two major difficulties with this approach:

**1\.** The search process is time-consuming.

**2\.** Most of the transactions that, according to our algorithm, need to be redone have already written their updates into the database. Although redoing them will cause no harm, it will nevertheless cause recovery to take longer.

To reduce these types of overhead, we introduce checkpoints. We describe below a simple checkpoint scheme that (a) does not permit any

updates to be performed while the checkpoint operation is in progress, and (b) outputs all modified buffer blocks to disk when the checkpoint is performed. We discuss later how to modify the checkpointing and recovery procedures to provide more flexibility by relaxing both these requirements.

A checkpoint is performed as follows:

**1\.** Output onto stable storage all log records currently residing in main mem- ory.

**2\.** Output to the disk all modified buffer blocks.

**3\.** Output onto stable storage a log record of the form _<_checkpoint _L>_, where _L_ is a list of transactions active at the time of the checkpoint.

Transactions are not allowed to perform any update actions, such as writing to a buffer block or writing a log record, while a checkpoint is in progress. We discuss how this requirement can be enforced, later, in Section 16.5.2.

The presence of a _<_checkpoint _L>_ record in the log allows the system to streamline its recovery procedure. Consider a transaction _Ti_ that completed prior to the checkpoint. For such a transaction, the _<Ti_ commit_\>_ record (or _< Ti_ abort_\>_ record) appears in the log before the _<_checkpoint_\>_ record. Any database mod- ifications made by _Ti_ must have been written to the database either prior to the checkpoint or as part of the checkpoint itself. Thus, at recovery time, there is no need to perform a redo operation on _Ti_ .

After a system crash has occurred, the system examines the log to find the last _<_checkpoint _L>_ record (this can be done by searching the log backward, from the end of the log, until the first _<_checkpoint _L>_ record is found).  


The redo or undo operations need to be applied only to transactions in _L_, and to all transactions that started execution after the _<_checkpoint _L>_ record was written to the log. Let us denote this set of transactions as _T_ .

• For all transactions _Tk_ in _T_ that have no _<Tk_ commit_\>_ record or _<Tk_ abort_\>_ record in the log, execute undo(_Tk_).

• For all transactions _Tk_ in _T_ such that either the record _<Tk_ commit_\>_ or the record _<Tk_ abort_\>_ appears in the log, execute redo(_Tk_).

Note that we need only examine the part of the log starting with the last check- point log record to find the set of transactions _T_ , and to find out whether a commit or abort record occurs in the log for each transaction in _T_ .

As an illustration, consider the set of transactions {_T_0, _T_1_, . . . , T_100}. Suppose that the most recent checkpoint took place during the execution of transaction _T_67 and _T_69, while _T_68 and all transactions with subscripts lower than 67 completed before the checkpoint. Thus, only transactions _T_67, _T_69_, . . . , T_100 need to be con- sidered during the recovery scheme. Each of them needs to be redone if it has completed (that is, either committed or aborted); otherwise, it was incomplete, and needs to be undone.

Consider the set of transactions _L_ in a checkpoint log record. For each trans- action _Ti_ in _L_, log records of the transaction that occur prior to the checkpoint log record may be needed to undo the transaction, in case it does not commit. However, all log records prior to the earliest of the _< Ti_ start_\>_ log records, among transactions _Ti_ in _L_, are not needed once the checkpoint has completed. These log records can be erased whenever the database system needs to reclaim the space occupied by these records.

The requirement that transactions must not perform any updates to buffer blocks or to the log during checkpointing can be bothersome, since transaction processing has to halt while a checkpoint is in progress. A **fuzzy checkpoint** is a checkpoint where transactions are allowed to perform updates even while buffer blocks are being written out. Section 16.5.4 describes fuzzy-checkpointing schemes. Later in Section 16.8 we describe a checkpoint scheme that is not only fuzzy, but does not even require all modified buffer blocks to be output to disk at the time of the checkpoint.