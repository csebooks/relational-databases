---
title: 'Multiversion Schemes' 
weight: 6
references:
    videos:
        - youtube:ktDutIfAd9E
        - youtube:iM71d2krbS4
    links:
        - https://www.tutorialspoint.com/multiversion-concurrency-control-techniques
        - https://www.geeksforgeeks.org/concurrency-control-techniques/
    books:
        - b1:
            title: krishna's Database Management System 
            url: https://www.google.co.in/books/edition/krishna_s_Database_Management_System/YM0AvJp6ocUC?hl=en&gbpv=0
        - b2:
            title: Readings in Database Systems 
            url: https://www.google.co.in/books/edition/Readings_in_Database_Systems/7a48qSMuVcUC?hl=en&gbpv=0
---

# Multiversion Schemes

The concurrency-control schemes discussed thus far ensure serializability by ei- ther delaying an operation or aborting the transaction that issued the operation. For example, a read operation may be delayed because the appropriate value has not been written yet; or it may be rejected (that is, the issuing transaction must be aborted) because the value that it was supposed to read has already been overwritten. These difficulties could be avoided if old copies of each data item were kept in a system.

In **multiversion concurrency-control** schemes, each write(_Q_) operation cre- ates a new **version** of _Q_. When a transaction issues a read(_Q_) operation, the  


concurrency-control manager selects one of the versions of _Q_ to be read. The concurrency-control scheme must ensure that the version to be read is selected in a manner that ensures serializability. It is also crucial, for performance reasons, that a transaction be able to determine easily and quickly which version of the data item should be read.

## Multiversion Timestamp Ordering

The timestamp-ordering protocol can be extended to a multiversion protocol. With each transaction _Ti_ in the system, we associate a unique static timestamp, denoted by TS(_Ti_ ). The database system assigns this timestamp before the trans- action starts execution, as described in Section 15.4.

With each data item _Q_, a sequence of versions _<Q_1, _Q_2_, . . . , Qm>_ is associated. Each version _Qk_ contains three data fields:

• **Content** is the value of version _Qk_ .

• **W-timestamp**(_Qk_) is the timestamp of the transaction that created version _Qk_ .

• **R-timestamp**(_Qk_) is the largest timestamp of any transaction that successfully read version _Qk_ .

A transaction—say, _Ti_ —creates a new version _Qk_ of data item _Q_ by issuing a write(_Q_) operation. The content field of the version holds the value written by _Ti_ . The system initializes the W-timestamp and R-timestamp to TS(_Ti_ ). It updates the R-timestamp value of _Qk_ whenever a transaction _Tj_ reads the content of _Qk_ , and R-timestamp(_Qk_) _<_ TS(_Tj_ ).

The **multiversion timestamp-ordering scheme** presented next ensures seri- alizability. The scheme operates as follows: Suppose that transaction _Ti_ issues a read(_Q_) or write(_Q_) operation. Let _Qk_ denote the version of _Q_ whose write timestamp is the largest write timestamp less than or equal to TS(_Ti_ ).

**1\.** If transaction _Ti_ issues a read(_Q_), then the value returned is the content of version _Qk_ .

**2\.** If transaction _Ti_ issues write(_Q_), and if TS(_Ti_ ) _<_ R-timestamp(_Qk_), then the system rolls back transaction _Ti_ . On the other hand, if TS(_Ti_ ) = W- timestamp(_Qk_), the system overwrites the contents of _Qk_ ; otherwise (if TS(_Ti_ ) _\>_ R-timestamp(_Qk_)), it creates a new version of _Q_.

The justification for rule 1 is clear. A transaction reads the most recent version that comes before it in time. The second rule forces a transaction to abort if it is “too late” in doing a write. More precisely, if _Ti_ attempts to write a version that some other transaction would have read, then we cannot allow that write to succeed.

Versions that are no longer needed are removed according to the following rule: Suppose that there are two versions, _Qk_ and _Q j_ , of a data item, and that both  


versions have a W-timestamp less than the timestamp of the oldest transaction in the system. Then, the older of the two versions _Qk_ and _Q j_ will not be used again, and can be deleted.

The multiversion timestamp-ordering scheme has the desirable property that a read request never fails and is never made to wait. In typical database systems, where reading is a more frequent operation than is writing, this advantage may be of major practical significance.

The scheme, however, suffers from two undesirable properties. First, the read- ing of a data item also requires the updating of the R-timestamp field, resulting in two potential disk accesses, rather than one. Second, the conflicts between trans- actions are resolved through rollbacks, rather than through waits. This alternative may be expensive. Section 15.6.2 describes an algorithm to alleviate this problem.

This multiversion timestamp-ordering scheme does not ensure recoverabil- ity and cascadelessness. It can be extended in the same manner as the basic timestamp-ordering scheme, to make it recoverable and cascadeless.

## Multiversion Two-Phase Locking

The **multiversion two-phase locking protocol** attempts to combine the advan- tages of multiversion concurrency control with the advantages of two-phase locking. This protocol differentiates between **read-only transactions** and **update transactions**.

Update transactions perform rigorous two-phase locking; that is, they hold all locks up to the end of the transaction. Thus, they can be serialized according to their commit order. Each version of a data item has a single timestamp. The timestamp in this case is not a real clock-based timestamp, but rather is a counter, which we will call the ts-counter, that is incremented during commit processing.

The database system assigns read-only transactions a timestamp by read- ing the current value of ts-counter before they start execution; they follow the multiversion timestamp-ordering protocol for performing reads. Thus, when a read-only transaction _Ti_ issues a read(_Q_), the value returned is the contents of the version whose timestamp is the largest timestamp less than or equal to TS(_Ti_ ).

When an update transaction reads an item, it gets a shared lock on the item, and reads the latest version of that item. When an update transaction wants to write an item, it first gets an exclusive lock on the item, and then creates a new version of the data item. The write is performed on the new version, and the timestamp of the new version is initially set to a value ∞, a value greater than that of any possible timestamp.

When the update transaction _Ti_ completes its actions, it carries out commit processing: First, _Ti_ sets the timestamp on every version it has created to 1 more than the value of ts-counter; then, _Ti_ increments ts-counter by 1. Only one update transaction is allowed to perform commit processing at a time.

As a result, read-only transactions that start after _Ti_ increments ts-counter will see the values updated by _Ti_ , whereas those that start before _Ti_ increments ts-counter will see the value before the updates by _Ti_ . In either case, read-only  

transactions never need to wait for locks. Multiversion two-phase locking also ensures that schedules are recoverable and cascadeless.

Versions are deleted in a manner like that of multiversion timestamp ordering. Suppose there are two versions, _Qk_ and _Q j_ , of a data item, and that both versions have a timestamp less than or equal to the timestamp of the oldest read-only transaction in the system. Then, the older of the two versions _Qk_ and _Q j_ will not be used again and can be deleted.