---
title: 'Timestamp-Based Protocols' 
weight: 4
references:
    videos:
        - youtube:aeykOjWjT5Q
        - youtube:tskULH6YV2Q
    links:
        - https://www.javatpoint.com/dbms-timestamp-ordering-protocol
        - https://www.geeksforgeeks.org/timestamp-based-concurrency-control/
    books:
        - b1:
            title: GATE 2019 Computer Science & Information Technology 
            url: https://www.google.co.in/books/edition/GATE_2019_Computer_Science_Information_T/9-t1DwAAQBAJ?hl=en&gbpv=0
        - b2:
            title: Hands On Relational Database Management System 
            url: https://www.google.co.in/books/edition/Hands_On_Relational_Database_Management/7QNuDwAAQBAJ?hl=en&gbpv=0
---

# Timestamp-Based Protocols

The locking protocols that we have described thus far determine the order be- tween every pair of conflicting transactions at execution time by the first lock that both members of the pair request that involves incompatible modes. An- other method for determining the serializability order is to select an ordering among transactions in advance. The most common method for doing so is to use a _timestamp-ordering_ scheme.

## Timestamps

With each transaction _Ti_ in the system, we associate a unique fixed timestamp, denoted by TS(_Ti_ ). This timestamp is assigned by the database system before the transaction _Ti_ starts execution. If a transaction _Ti_ has been assigned timestamp TS(_Ti_ ), and a new transaction _Tj_ enters the system, then TS(_Ti_ ) _<_ TS(_Tj_ ). There are two simple methods for implementing this scheme:

**1\.** Use the value of the **s ¯ ystem clock** as the timestamp; that is, a transaction’s

timestamp is equal to the value of the clock when the transaction enters the system.

**2\.** Use a **logical counter** that is incremented after a new timestamp has been assigned; that is, a transaction’s timestamp is equal to the value of the counter when the transaction enters the system.  


The timestamps of the transactions determine the serializability order. Thus, if TS(_Ti_ ) _<_ TS(_Tj_ ), then the system must ensure that the produced schedule is equivalent to a serial schedule in which transaction _Ti_ appears before transaction _Tj_ .

To implement this scheme, we associate with each data item _Q_ two timestamp values:

• **W-timestamp**(_Q_) denotes the largest timestamp of any transaction that exe- cuted write(_Q_) successfully.

• **R-timestamp**(_Q_) denotes the largest timestamp of any transaction that exe- cuted read(_Q_) successfully.

These timestamps are updated whenever a new read(_Q_) or write(_Q_) instruction is executed.

## The Timestamp-Ordering Protocol

The **timestamp-ordering protocol** ensures that any conflicting read and write operations are executed in timestamp order. This protocol operates as follows:

**1\.** Suppose that transaction _Ti_ issues read(_Q_).

a. If TS(_Ti_ ) _<_ W-timestamp(_Q_), then _Ti_ needs to read a value of _Q_ that was already overwritten. Hence, the read operation is rejected, and _Ti_ is rolled back.

b. If TS(_Ti_ ) ≥ W-timestamp(_Q_), then the read operation is executed, and R-timestamp(_Q_) is set to the maximum of R-timestamp(_Q_) and TS(_Ti_ ).

**2\.** Suppose that transaction _Ti_ issues write(_Q_).

a. If TS(_Ti_ ) _<_ R-timestamp(_Q_), then the value of _Q_ that _Ti_ is producing was needed previously, and the system assumed that that value would never be produced. Hence, the system rejects the write operation and rolls _Ti_ back.

b. If TS(_Ti_ ) _<_ W-timestamp(_Q_), then _Ti_ is attempting to write an obsolete value of _Q_. Hence, the system rejects this write operation and rolls _Ti_ back.

c. Otherwise, the system executes the write operation and sets W-time- stamp(_Q_) to TS(_Ti_ ).

If a transaction _Ti_ is rolled back by the concurrency-control scheme as result of issuance of either a read or write operation, the system assigns it a new timestamp and restarts it.

To illustrate this protocol, we consider transactions T25 and T26\. Transaction T25 displays the contents of accounts _A_ and _B_:  


T25: read(_B_);
 read(_A_);
  display(_A_ \+ _B_).

Transaction T26 transfers $50 from account _B_ to account _A_, and then displays the contents of both:

T26: read(_B_);
 _B_ := _B_ − 50;
  write(_B_); 
  read(_A_); 
  _A_ := _A_ \+ 50; 
  write(_A_); 
  display(_A_ \+ _B_).

In presenting schedules under the timestamp protocol, we shall assume that a transaction is assigned a timestamp immediately before its first instruction. Thus, in schedule 3 of Figure 15.17, TS(_T_25) _<_ TS(_T_26), and the schedule is possible under the timestamp protocol.

We note that the preceding execution can also be produced by the two-phase locking protocol. There are, however, schedules that are possible under the two- phase locking protocol, but are not possible under the timestamp protocol, and vice versa (see Exercise 15.29).

The timestamp-ordering protocol ensures conflict serializability. This is be- cause conflicting operations are processed in timestamp order.

The protocol ensures freedom from deadlock, since no transaction ever waits. However, there is a possibility of starvation of long transactions if a sequence of conflicting short transactions causes repeated restarting of the long transaction. If a transaction is suffering from repeated restarts, conflicting transactions need to be temporarily blocked to enable the transaction to finish.

![](1.17.png)

**Figure 15.17** Schedule 3.  


The protocol can generate schedules that are not recoverable. However, it can be extended to make the schedules recoverable, in one of several ways:

• Recoverability and cascadelessness can be ensured by performing all writes together at the end of the transaction. The writes must be atomic in the following sense: While the writes are in progress, no transaction is permitted to access any of the data items that have been written.

• Recoverability and cascadelessness can also be guaranteed by using a limited form of locking, whereby reads of uncommitted items are postponed until the transaction that updated the item commits (see Exercise 15.30).

• Recoverability alone can be ensured by tracking uncommitted writes, and al- lowing a transaction _Ti_ to commit only after the commit of any transaction that wrote a value that _Ti_ read. Commit dependencies, outlined in Section 15.1.5, can be used for this purpose.

## Thomas’ Write Rule

We now present a modification to the timestamp-ordering protocol that allows greater potential concurrency than does the protocol of Section 15.4.2. Let us consider schedule 4 of Figure 15.18, and apply the timestamp-ordering protocol. Since _T_27 starts before _T_28, we shall assume that TS(_T_27) _<_ TS(_T_28). The read(_Q_) operation of _T_27 succeeds, as does the write(_Q_) operation of _T_28\. When _T_27 at- tempts its write(_Q_) operation, we find that TS(_T_27) _<_ W-timestamp(_Q_), since W- timestamp(_Q_) = TS(_T_28). Thus, the write(_Q_) by _T_27 is rejected and transaction _T_27 must be rolled back.

Although the rollback of _T_27 is required by the timestamp-ordering protocol, it is unnecessary. Since _T_28 has already written _Q_, the value that _T_27 is attempting to write is one that will never need to be read. Any transaction _Ti_ with TS(_Ti_ ) _<_

TS(_T_28) that attempts a read(_Q_) will be rolled back, since TS(_Ti_) _<_ W-timestamp(_Q_). Any transaction _Tj_ with TS(_Tj_ ) _\>_ TS(_T_28) must read the value of _Q_ written by _T_28, rather than the value that _T_27 is attempting to write.

This observation leads to a modified version of the timestamp-ordering proto- col in which obsolete write operations can be ignored under certain circumstances. The protocol rules for read operations remain unchanged. The protocol rules for write operations, however, are slightly different from the timestamp-ordering protocol of Section 15.4.2.

![](1.18.png)

**Figure 15.18** Schedule 4.  


The modification to the timestamp-ordering protocol, called **Thomas’ write rule**, is this: Suppose that transaction _Ti_ issues write(_Q_).

**1\.** If TS(_Ti_ ) _<_ R-timestamp(_Q_), then the value of _Q_ that _Ti_ is producing was previously needed, and it had been assumed that the value would never be produced. Hence, the system rejects the write operation and rolls _Ti_ back.

**2\.** If TS(_Ti_ ) _<_ W-timestamp(_Q_), then _Ti_ is attempting to write an obsolete value of _Q_. Hence, this write operation can be ignored.

**3\.** Otherwise, the system executes the write operation and sets W-timestamp(_Q_) to TS(_Ti_ ).

The difference between these rules and those of Section 15.4.2 lies in the second rule. The timestamp-ordering protocol requires that _Ti_ be rolled back if _Ti_ issues write(_Q_) and TS(_Ti_ ) _<_ W-timestamp(_Q_). However, here, in those cases where TS(_Ti_ ) ≥ R-timestamp(_Q_), we ignore the obsolete write.

By ignoring the write, Thomas’ write rule allows schedules that are not conflict serializable but are nevertheless correct. Those non-conflict-serializable sched- ules allowed satisfy the definition of _view serializable_ schedules (see example box). Thomas’ write rule makes use of view serializability by, in effect, deleting ob- solete write operations from the transactions that issue them. This modification of transactions makes it possible to generate serializable schedules that would not be possible under the other protocols presented in this chapter. For example, schedule 4 of Figure 15.18 is not conflict serializable and, thus, is not possible un- der the two-phase locking protocol, the tree protocol, or the timestamp-ordering protocol. Under Thomas’ write rule, the write(_Q_) operation of _T_27 would be ig- nored. The result is a schedule that is _view equivalent_ to the serial schedule _<T_27, _T_28_\>_.