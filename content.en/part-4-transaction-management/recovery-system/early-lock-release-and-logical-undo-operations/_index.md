---
title: 'Early Lock Release and Logical Undo Operations'
weight: 7
references:
    videos:
        - youtube:Vhumg6jClM0
        - youtube:JAMRnR1dkQc
    links:
        - https://text-id.123dok.com/document/ky6l341gy-early-lock-release-and-logical-undo-operations-indian-institute-of-technology-bombay.html
        - https://ayende.com/blog/165090/early-lock-release-transactions-and-errors
    books:
        - b1:
            title: Oracle Essentials 
            url: https://www.google.co.in/books/edition/Oracle_Essentials/oSKwAAAAQBAJ?hl=en&gbpv=0
        - b2:
            title: VLDB '95
            url:  https://www.google.co.in/books/edition/VLDB_95/QlI_AQAAIAAJ?hl=en&gbpv=0&bsq=Early%20Lock%20Release%20and%20Logical%20Undo%20Operations%20books
---

# Early Lock Release and Logical Undo Operations

Any index used in processing a transaction, such as a B+-tree, can be treated as normal data, but to increase concurrency, we can use the B+-tree concurrency- control algorithm described in Section 15.10 to allow locks to be released early, in a non-two-phase manner. As a result of early lock release, it is possible that a value in a B+-tree node is updated by one transaction _T_1, which inserts an entry (_V_1_, R_1), and subsequently by another transaction _T_2, which inserts an entry (_V_2_, R_2) in the same node, moving the entry (_V_1_, R_1) even before _T_1 completes execution.4 At this point, we cannot undo transaction _T_1 by replacing the contents of the node with the old value prior to _T_1 performing its insert, since that would also undo the insert performed by _T_2; transaction _T_2 may still commit (or may have already committed). In this example, the only way to undo the effect of insertion of (_V_1_, R_1) is to execute a corresponding delete operation.

In the rest of this section, we see how to extend the recovery algorithm of Section 16.4 to support early lock release.

## Logical Operations

The insertion and deletion operations are examples of a class of operations that require logical undo operations since they release locks early; we call such opera- tions **logical operations**. Such early lock release is important not only for indices, but also for operations on other system data structures that are accessed and updated very frequently; examples include data structures that track the blocks containing records of a relation, the free space in a block, and the free blocks in a database. If locks were not released early after performing operations on such data structures, transactions would tend to run serially, affecting system performance.

The theory of conflict serializability has been extended to operations, based on what operations conflict with what other operations. For example, two insert

4Recall that an entry consists of a key value and a record identifier, or a key value and a record in the case of the leaf level of a B+-tree file organization.  


operations on a B+-tree do not conflict if they insert different key values, even if they both update overlapping areas of the same index page. However, insert and delete operations conflict with other insert and delete operations, as well as with read operations, if they use the same key value. See the bibliographical notes for references to more information on this topic.

Operations acquire _lower-level locks_ while they execute, but release them when they complete; the corresponding transaction must however retain a _higher-level lock_ in a two-phase manner to prevent concurrent transactions from executing conflicting actions. For example, while an insert operation is being performed on a B+-tree page, a short-term lock is obtained on the page, allowing entries in the page to be shifted during the insert; the short-term lock is released as soon as the page has been updated. Such early lock release allows a second insert to execute on the same page. However, each transaction must obtain a lock on the key values being inserted or deleted, and retain it in a two-phase manner, to prevent a concurrent transaction from executing a conflicting read, insert or delete operation on the same key value.

Once the lower-level lock is released, the operation cannot be undone by using the old values of updated data items, and must instead be undone by executing a compensating operation; such an operation is called a **logical undo operation**. It is important that the lower-level locks acquired during an operation are sufficient to perform a subsequent logical undo of the operation, for reasons explained later in Section 16.7.4.

## Logical Undo Log Records

To allow logical undo of operations, before an operation is performed to modify an index, the transaction creates a log record _<Ti , Oj ,_ operation-begin_\>_, where _Oj_ is a unique identifier for the operation instance.5 While the system is executing the operation, it creates update log records in the normal fashion for all updates performed by the operation. Thus, the usual old-value and new-value information is written out as usual for each update performed by the operation; the old-value information is required in case the transaction needs to be rolled back before the operation completes. When the operation finishes, it writes an operation-end log record of the form _<Ti , Oj ,_ operation-end, _U>_, where the _U_ denotes undo information.

For example, if the operation inserted an entry in a B+-tree, the undo infor- mation _U_ would indicate that a deletion operation is to be performed, and would identify the B+-tree and what entry to delete from the tree. Such logging of infor- mation about operations is called **logical logging**. In contrast, logging of old-value and new-value information is called **physical logging**, and the corresponding log records are called **physical log records**.

Note that in the above scheme, logical logging is used only for undo, not for redo; redo operations are performed exclusively using physical log record. This is because the state of the database after a system failure may reflect some updates

5The position in the log of the operation-begin log record can be used as the unique identifier.  

of an operation and not other operations, depending on what buffer blocks had been written to disk before the failure. Data structures such as B+-trees would not be in a consistent state, and neither logical redo nor logical undo operations can be performed on an inconsistent data structure. To perform logical redo or undo, the database state on disk must be **operation consistent**, that is, it should not have partial effects of any operation. However, as we shall see, the physical redo processing in the redo phase of the recovery scheme, along with undo processing using physical log records ensures that the parts of the database accessed by a logical undo operation are in an operation consistent state, before the logical undo operation is performed.

An operation is said to be **idempotent** if executing it several times in a row gives the same result as executing it once. Operations such as inserting an entry into a B+-tree may not be idempotent, and the recovery algorithm must therefore make sure that an operation that has already been performed is not performed again. On the other hand, a physical log record is idempotent, since the corre- sponding data item would have the same value regardless of whether the logged update is executed one or multiple times.

## Transaction Rollback With Logical Undo

When rolling back a transaction _Ti_ , the log is scanned backwards, and log records corresponding to _Ti_ are processed as follows:

**1\.** Physical log records encountered during the scan are handled as described earlier, except those that are skipped as described shortly. Incomplete logical operations are undone using the physical log records generated by the operation.

**2\.** Completed logical operations, identified by operation-end records, are rolled back differently. Whenever the system finds a log record _<Ti , Oj ,_ operation- end, _U>_, it takes special actions:

a. It rolls back the operation by using the undo information _U_ in the log record. It logs the updates performed during the rollback of the operation just like updates performed when the operation was first executed.

At the end of the operation rollback, instead of generating a log record _<Ti , Oj_ , operation-end, _U>_, the database system generates a log record _<Ti , Oj ,_ operation-abort_\>_.

b. As the backward scan of the log continues, the system skips all log records of transaction _Ti_ until it finds the log record _<Ti , Oj ,_ operation- begin_\>_. After it finds the operation-begin log record, it processes log records of transaction _Ti_ in the normal manner again.

Observe that the system logs physical undo information for the updates performed during rollback, instead of using a redo-only compensation log records. This is because a crash may occur while a logical undo is in progress,  


and on recovery the system has to complete the logical undo; to do so, restart recovery will undo the partial effects of the earlier undo, using the physical undo information, and then perform the logical undo again.

Observe also that skipping over physical log records when the operation- end log record is found during rollback ensures that the old values in the physical log record are not used for rollback, once the operation completes.

**3\.** If the system finds a record _<Ti , Oj_ , operation-abort_\>_, it skips all preceding records (including the operation-end record for _Oj_ ) until it finds the record _<Ti , Oj_ , operation-begin_\>_.

An operation-abort log record would be found only if a transaction that is being rolled back had been partially rolled back earlier. Recall that logical operations may not be idempotent, and hence a logical undo operation must not be performed multiple times. These preceding log records must be skipped to prevent multiple rollback of the same operation, in case there had been a crash during an earlier rollback, and the transaction had already been partly rolled back.

**4\.** As before, when the _<Ti_ start_\>_ log record has been found, the transaction rollback is complete, and the system adds a record _<Ti_ abort_\>_ to the log.

If a failure occurs while a logical operation is in progress, the operation-end log record for the operation will not be found when the transaction is rolled back. However, for every update performed by the operation, undo information—in the form of the old value in the physical log records—is available in the log. The physical log records will be used to roll back the incomplete operation.

Now suppose an operation undo was in progress when the system crash occurred, which could happen if a transaction was being rolled back when the crash occurred. Then the physical log records written during operation undo would be found, and the partial operation undo would itself be undone using these physical log records. Continuing in the backward scan of the log, the original operation’s operation-end record would then be found, and the operation undo would be executed again. Rolling back the partial effects of the earlier undo operation using the physical log records brings the database to a consistent state, allowing the logical undo operation to be executed again.

Figure 16.6 shows an example of a log generated by two transactions, which add or subtract a value from a data item. Early lock release on the data item _C_ by transaction _T_0 after operation _O_1 completes allows transaction _T_1 to update the data item using _O_2, even before _T_0 completes, but necessitates logical undo. The logical undo operation needs to add or subtract a value from the data item, instead of restoring an old value to the data item.

The annotations on the figure indicate that before an operation completes, rollback can perform physical undo; after the operation completes and releases lower-level locks, the undo must be performed by subtracting or adding a value, instead of restoring the old value. In the example in the figure, _T_0 rolls back operation _O_1 by adding 100 to _C_ ; on the other hand, for data item _B_, which was not subject to early lock release, undo is performed physically. Observe that _T_1,  

![](2.6.png)
**Figure 16.6** Transaction rollback with logical undo operations.

which had performed an update on _C_ commits, and its update _O_2, which added 200 to _C_ and was performed before the undo of _O_1, has persisted even though _O_1 has been undone.

Figure 16.7 shows an example of recovery from a crash, with logical undo logging. In this example, operation _T_1 was active and executing operation _O_4 at the time of checkpoint. In the redo pass, the actions of _O_4 that are after the checkpoint log record are redone. At the time of crash, operation _O_5 was being executed by _T_2, but the operation was not complete. The undo-list contains _T_1 and _T_2 at the end of the redo pass. During the undo pass, the undo of operation _O_5 is carried out using the old value in the physical log record, setting _C_ to 400; this operation is logged using a redo-only log record. The start record of _T_2 is encountered next, resulting in the addition of _< T_2 abort_\>_ to the log, and removal of _T_2 from undo-list.

The next log record encountered is the operation-end record of _O_4; logical undo is performed for this operation by adding 300 to _C_ , which is logged physi- cally, and an operation-abort log record is added for _O_4\. The physical log records that were part of _O_4 are skipped until the operation-begin log record for _O_4 is encountered. In this example, there are no other intervening log records, but in general log records from other transactions may be found before we reach the operation-begin log record; such log records should of course not be skipped (unless they are part of a completed operation for the corresponding transaction and the algorithm skips those records). After the operation-begin log record is  

![](2.7.png)
**Figure 16.7** Failure recovery actions with logical undo operations

found for _O_4, a physical log record is found for _T_1, which is rolled back physically. Finally the start log record for _T_1 is found; this results in _< T_1 abort_\>_ being added to the log, and _T_1 being deleted from undo-list. At this point undo-list is empty, and the undo phase is complete.

## Concurrency Issues in Logical Undo

As mentioned earlier, it is important that the lower-level locks acquired during an operation are sufficient to perform a subsequent logical undo of the oper- ation; otherwise concurrent operations that execute during normal processing may cause problems in the undo-phase. For example, suppose the logical undo of operation _O_1 of transaction _T_1 can conflict at the data item level with a concurrent operation _O_2 of transaction _T_2, and _O_1 completes while _O_2 does not. Assume also that neither transaction had committed when the system crashed. The physical update log records of _O_2 may appear before and after the operation-end record for _O_1, and during recovery updates done during the logical undo of _O_1 may get fully or partially overwritten by old values during the physical undo of _O_2\. This problem cannot occur if _O_1 had obtained all the lower-level locks required for the logical undo of _O_1, since then there cannot be such a concurrent _O_2.  


If both the original operation and its logical undo operation access a single page (such operations are called physiological operations, and are discussed in Section 16.8), the locking requirement above is met easily. Otherwise the details of the specific operation need to be considered when deciding on what lower-level locks need to be obtained. For example, update operations on a B+-tree could obtain a short-term lock on the root, to ensure that operations execute serially. See the bibliographical notes for references on B+-tree concurrency control and recovery exploiting logical undo logging. See the bibliographical notes also for references to an alternative approach, called multi-level recovery, which relaxes this locking requirement.