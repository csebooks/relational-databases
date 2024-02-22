---
title: 'Lock-Based Protocols' 
weight: 1
references:
    videos:
        - youtube:a74V14OnDvw
        - youtube:s1YEg1L0sH0
    links:
        - https://www.javatpoint.com/dbms-lock-based-protocol
        - https://www.geeksforgeeks.org/lock-based-concurrency-control-protocol-in-dbms/
    books:
        - b1:
            title: Transaction Processing 
            url: https://www.google.co.in/books/edition/Transaction_Processing/TN1sBgAAQBAJ?hl=en&gbpv=0
        - b2:
            title: Transactional Information Systems 
            url: https://www.google.co.in/books/edition/Transactional_Information_Systems/wV5Ran71zNoC?hl=en&gbpv=0
---

# Lock-Based Protocols

One way to ensure isolation is to require that data items be accessed in a mutually exclusive manner; that is, while one transaction is accessing a data item, no other transaction can modify that data item. The most common method used to implement this requirement is to allow a transaction to access a data item only if it is currently holding a **lock** on that item. We introduced the concept of locking in Section 14.9.

## Locks

There are various modes in which a data item may be locked. In this section, we restrict our attention to two modes:

**1\. Shared**. If a transaction _Ti_ has obtained a **shared-mode lock** (denoted by S) on item _Q_, then _Ti_ can read, but cannot write, _Q_.

**2\. Exclusive**. If a transaction _Ti_ has obtained an **exclusive-mode lock** (denoted by X) on item _Q_, then _Ti_ can both read and write _Q_.

![](1.1.png)

**Figure 15.1** Lock-compatibility matrix comp.

We require that every transaction **request** a lock in an appropriate mode on data item _Q_, depending on the types of operations that it will perform on _Q_. The transaction makes the request to the concurrency-control manager. The transaction can proceed with the operation only after the concurrency-control manager **grants** the lock to the transaction. The use of these two lock modes allows multiple transactions to read a data item but limits write access to just one transaction at a time.

To state this more generally, given a set of lock modes, we can define a **compatibility function** on them as follows: Let _A_ and _B_ represent arbitrary lock modes. Suppose that a transaction _Ti_ requests a lock of mode _A_ on item _Q_ on which transaction _Tj_ (_Ti_ = _Tj_ ) currently holds a lock of mode _B_. If transaction _Ti_ can be granted a lock on _Q_ immediately, in spite of the presence of the mode _B_ lock, then we say mode _A_ is **compatible** with mode _B_. Such a function can be represented conveniently by a matrix. The compatibility relation between the two modes of locking discussed in this section appears in the matrix comp of Figure 15.1. An element comp(_A, B_) of the matrix has the value _true_ if and only if mode _A_ is compatible with mode _B_.

Note that shared mode is compatible with shared mode, but not with exclusive mode. At any time, several shared-mode locks can be held simultaneously (by different transactions) on a particular data item. A subsequent exclusive-mode lock request has to wait until the currently held shared-mode locks are released.

A transaction requests a shared lock on data item _Q_ by executing the lock- S(_Q_) instruction. Similarly, a transaction requests an exclusive lock through the lock-X(_Q_) instruction. A transaction can unlock a data item _Q_ by the unlock(_Q_) instruction.

To access a data item, transaction _Ti_ must first lock that item. If the data item is already locked by another transaction in an incompatible mode, the concurrency- control manager will not grant the lock until all incompatible locks held by other transactions have been released. Thus, _Ti_ is made to **wait** until all incompatible locks held by other transactions have been released.

Transaction _Ti_ may unlock a data item that it had locked at some earlier point. Note that a transaction must hold a lock on a data item as long as it accesses that item. Moreover, it is not necessarily desirable for a transaction to unlock a data item immediately after its final access of that data item, since serializability may not be ensured.

As an illustration, consider again the banking example that we introduced in Chapter 14. Let _A_ and _B_ be two accounts that are accessed by transactions T1  

![](1.2.png)

**Figure 15.2** Transaction T1.

and T2\. Transaction T1 transfers $50 from account _B_ to account _A_ (Figure 15.2). Transaction T2 displays the total amount of money in accounts _A_ and _B_—that is, the sum _A_ \+ _B_ (Figure 15.3).

Suppose that the values of accounts _A_ and _B_ are $100 and $200, respectively. If these two transactions are executed serially, either in the order T1, T2 or the order T2, T1, then transaction T2 will display the value $300. If, however, these transactions are executed concurrently, then schedule 1, in Figure 15.4, is possible. In this case, transaction T2 displays $250, which is incorrect. The reason for this mistake is that the transaction T1 unlocked data item _B_ too early, as a result of which T2 saw an inconsistent state.

The schedule shows the actions executed by the transactions, as well as the points at which the concurrency-control manager grants the locks. The transac- tion making a lock request cannot execute its next action until the concurrency- control manager grants the lock. Hence, the lock must be granted in the interval of time between the lock-request operation and the following action of the trans- action. Exactly when within this interval the lock is granted is not important; we can safely assume that the lock is granted just before the following action of the transaction. We shall therefore drop the column depicting the actions of the concurrency-control manager from all schedules depicted in the rest of the chapter. We let you infer when locks are granted.

![](1.3.png)
**Figure 15.3** Transaction T2.  

![](1.4.png)


**Figure 15.4** Schedule 1.

Suppose now that unlocking is delayed to the end of the transaction. Trans- action T3 corresponds to T1 with unlocking delayed (Figure 15.5). Transaction T4 corresponds to T2 with unlocking delayed (Figure 15.6).

You should verify that the sequence of reads and writes in schedule 1, which lead to an incorrect total of $250 being displayed, is no longer possible with T3
![](1.5.png)
**Figure 15.5** Transaction T3 (transaction T1 with unlocking delayed).  


![](1.6.png)

**Figure 15.6** Transaction T4 (transaction T2 with unlocking delayed).

and T4\. Other schedules are possible. T4 will not print out an inconsistent result in any of them; we shall see why later.

Unfortunately, locking can lead to an undesirable situation. Consider the partial schedule of Figure 15.7 for T3 and T4\. Since T3 is holding an exclusive- mode lock on _B_ and T4 is requesting a shared-mode lock on _B_, T4 is waiting for T3 to unlock _B_. Similarly, since T4 is holding a shared-mode lock on _A_ and T3 is requesting an exclusive-mode lock on _A_, T3 is waiting for T4 to unlock _A_. Thus, we have arrived at a state where neither of these transactions can ever proceed with its normal execution. This situation is called **deadlock**. When deadlock occurs, the system must roll back one of the two transactions. Once a transaction has been rolled back, the data items that were locked by that transaction are unlocked. These data items are then available to the other transaction, which can continue with its execution. We shall return to the issue of deadlock handling in Section 15.2.

If we do not use locking, or if we unlock data items too soon after reading or writing them, we may get inconsistent states. On the other hand, if we do not unlock a data item before requesting a lock on another data item, deadlocks may occur. There are ways to avoid deadlock in some situations, as we shall see in Section 15.1.5. However, in general, deadlocks are a necessary evil associated with locking, if we want to avoid inconsistent states. Deadlocks are definitely

![](1.7.png)

**Figure 15.7** Schedule 2.  


preferable to inconsistent states, since they can be handled by rolling back trans- actions, whereas inconsistent states may lead to real-world problems that cannot be handled by the database system.

We shall require that each transaction in the system follow a set of rules, called a **locking protocol**, indicating when a transaction may lock and unlock each of the data items. Locking protocols restrict the number of possible schedules. The set of all such schedules is a proper subset of all possible serializable schedules. We shall present several locking protocols that allow only conflict-serializable schedules, and thereby ensure isolation. Before doing so, we introduce some terminology.

Let _{T_0, T1_, . . . , Tn}_ be a set of transactions participating in a schedule _S_. We say that _Ti_ **precedes** _Tj_ in _S_, written _Ti_ → _Tj_ , if there exists a data item _Q_ such that _Ti_ has held lock mode _A_ on _Q_, and _Tj_ has held lock mode _B_ on _Q_ later, and comp(_A,B_) = false. If _Ti_ → _Tj_ , then that precedence implies that in any equivalent serial schedule, _Ti_ must appear before _Tj_ . Observe that this graph is similar to the precedence graph that we used in Section 14.6 to test for conflict serializability. Conflicts between instructions correspond to noncompatibility of lock modes.

We say that a schedule _S_ is **legal** under a given locking protocol if _S_ is a possible schedule for a set of transactions that follows the rules of the locking protocol. We say that a locking protocol **ensures** conflict serializability if and only if all legal schedules are conflict serializable; in other words, for all legal schedules the associated → relation is acyclic.

## Granting of Locks

When a transaction requests a lock on a data item in a particular mode, and no other transaction has a lock on the same data item in a conflicting mode, the lock can be granted. However, care must be taken to avoid the following scenario. Suppose a transaction T2 has a shared-mode lock on a data item, and another transaction T1 requests an exclusive-mode lock on the data item. Clearly, T1 has to wait for T2 to release the shared-mode lock. Meanwhile, a transaction T3 may request a shared-mode lock on the same data item. The lock request is compatible with the lock granted to T2, so T3 may be granted the shared-mode lock. At this point T2 may release the lock, but still T1 has to wait for T3 to finish. But again, there may be a new transaction T4 that requests a shared-mode lock on the same data item, and is granted the lock before T3 releases it. In fact, it is possible that there is a sequence of transactions that each requests a shared-mode lock on the data item, and each transaction releases the lock a short while after it is granted, but T1 never gets the exclusive-mode lock on the data item. The transaction T1 may never make progress, and is said to be **starved**.

We can avoid starvation of transactions by granting locks in the following manner: When a transaction _Ti_ requests a lock on a data item _Q_ in a particular mode _M_, the concurrency-control manager grants the lock provided that:

**1\.** There is no other transaction holding a lock on _Q_ in a mode that conflicts with _M_.  


**2\.** There is no other transaction that is waiting for a lock on _Q_ and that made its lock request before _Ti_ .

Thus, a lock request will never get blocked by a lock request that is made later.

## The Two-Phase Locking Protocol

One protocol that ensures serializability is the **two-phase locking protocol**. This protocol requires that each transaction issue lock and unlock requests in two phases:

**1\. Growing phase**. A transaction may obtain locks, but may not release any lock.

**2\. Shrinking phase**. A transaction may release locks, but may not obtain any new locks.

Initially, a transaction is in the growing phase. The transaction acquires locks as needed. Once the transaction releases a lock, it enters the shrinking phase, and it can issue no more lock requests.

For example, transactions T3 and T4 are two phase. On the other hand, trans- actions T1 and T2 are not two phase. Note that the unlock instructions do not need to appear at the end of the transaction. For example, in the case of transaction T3, we could move the unlock(_B_) instruction to just after the lock-X(_A_) instruction, and still retain the two-phase locking property.

We can show that the two-phase locking protocol ensures conflict serializabil- ity. Consider any transaction. The point in the schedule where the transaction has obtained its final lock (the end of its growing phase) is called the **lock point** of the transaction. Now, transactions can be ordered according to their lock points— this ordering is, in fact, a serializability ordering for the transactions. We leave the proof as an exercise for you to do (see Practice Exercise 15.1).

Two-phase locking does _not_ ensure freedom from deadlock. Observe that transactions T3 and T4 are two phase, but, in schedule 2 (Figure 15.7), they are deadlocked.

Recall from Section 14.7.2 that, in addition to being serializable, schedules should be cascadeless. Cascading rollback may occur under two-phase locking. As an illustration, consider the partial schedule of Figure 15.8. Each transaction observes the two-phase locking protocol, but the failure of T5 after the read(A) step of T7 leads to cascading rollback of T6 and T7.

Cascading rollbacks can be avoided by a modification of two-phase locking called the **strict two-phase locking protocol**. This protocol requires not only that locking be two phase, but also that all exclusive-mode locks taken by a transaction be held until that transaction commits. This requirement ensures that any data written by an uncommitted transaction are locked in exclusive mode until the transaction commits, preventing any other transaction from reading the data.

Another variant of two-phase locking is the **rigorous two-phase locking protocol**, which requires that all locks be held until the transaction commits.  


![](1.8.png)

**Figure 15.8** Partial schedule under two-phase locking.

We can easily verify that, with rigorous two-phase locking, transactions can be serialized in the order in which they commit.

Consider the following two transactions, for which we have shown only some of the significant read and write operations:

T8: read(_a_1); read(_a_2); . . . read(_an_); write(_a_1).

T9: read(_a_1); read(_a_2); display(_a_1 + _a_2).

If we employ the two-phase locking protocol, then T8 must lock _a_1 in exclusive mode. Therefore, any concurrent execution of both transactions amounts to a serial execution. Notice, however, that T8 needs an exclusive lock on _a_1 only at the end of its execution, when it writes _a_1\. Thus, if T8 could initially lock _a_1 in shared mode, and then could later change the lock to exclusive mode, we could get more concurrency, since T8 and T9 could access _a_1 and _a_2 simultaneously.

This observation leads us to a refinement of the basic two-phase locking protocol, in which **lock conversions** are allowed. We shall provide a mechanism for upgrading a shared lock to an exclusive lock, and downgrading an exclusive lock to a shared lock. We denote conversion from shared to exclusive modes by **upgrade**, and from exclusive to shared by **downgrade**. Lock conversion cannot be allowed arbitrarily. Rather, upgrading can take place in only the growing phase, whereas downgrading can take place in only the shrinking phase.  

![](1.9.png)

**Figure 15.9** Incomplete schedule with a lock conversion.

Returning to our example, transactions T8 and T9 can run concurrently under the refined two-phase locking protocol, as shown in the incomplete schedule of Figure 15.9, where only some of the locking instructions are shown.

Note that a transaction attempting to upgrade a lock on an item _Q_ may be forced to wait. This enforced wait occurs if _Q_ is currently locked by _another_ transaction in shared mode.

Just like the basic two-phase locking protocol, two-phase locking with lock conversion generates only conflict-serializable schedules, and transactions can be serialized by their lock points. Further, if exclusive locks are held until the end of the transaction, the schedules are cascadeless.

For a set of transactions, there may be conflict-serializable schedules that cannot be obtained through the two-phase locking protocol. However, to obtain conflict-serializable schedules through non-two-phase locking protocols, we need either to have additional information about the transactions or to impose some structure or ordering on the set of data items in the database. We shall see examples when we consider other locking protocols later in this chapter.

Strict two-phase locking and rigorous two-phase locking (with lock conver- sions) are used extensively in commercial database systems.

A simple but widely used scheme automatically generates the appropriate lock and unlock instructions for a transaction, on the basis of read and write requests from the transaction:

• When a transaction _Ti_ issues a read(_Q_) operation, the system issues a lock- S(_Q_) instruction followed by the read(_Q_) instruction.

• When _Ti_ issues a write(_Q_) operation, the system checks to see whether _Ti_ already holds a shared lock on _Q_. If it does, then the system issues an up- grade(_Q_) instruction, followed by the write(_Q_) instruction. Otherwise, the system issues a lock-X(_Q_) instruction, followed by the write(_Q_) instruction.

• All locks obtained by a transaction are unlocked after that transaction com- mits or aborts.  


## Implementation of Locking

A **lock manager** can be implemented as a process that receives messages from transactions and sends messages in reply. The lock-manager process replies to lock-request messages with lock-grant messages, or with messages requesting rollback of the transaction (in case of deadlocks). Unlock messages require only an acknowledgment in response, but may result in a grant message to another waiting transaction.

The lock manager uses this data structure: For each data item that is currently locked, it maintains a linked list of records, one for each request, in the order in which the requests arrived. It uses a hash table, indexed on the name of a data item, to find the linked list (if any) for a data item; this table is called the **lock table**. Each record of the linked list for a data item notes which transaction made the request, and what lock mode it requested. The record also notes if the request has currently been granted.

Figure 15.10 shows an example of a lock table. The table contains locks for five different data items, I4, I7, I23, I44, and I912. The lock table uses overflow chaining, so there is a linked list of data items for each entry in the lock table. There is also a list of transactions that have been granted locks, or are waiting for locks, for each of the data items. Granted locks are the rectangles filled in a darker shade, while waiting requests are the rectangles filled in a lighter shade. We have omitted the lock mode to keep the figure simple. It can be seen, for example, that T23 has been granted locks on I912 and I7, and is waiting for a lock on I4.

Although the figure does not show it, the lock table should also maintain an index on transaction identifiers, so that it is possible to determine efficiently the set of locks held by a given transaction.

The lock manager processes requests this way:

• When a lock request message arrives, it adds a record to the end of the linked list for the data item, if the linked list is present. Otherwise it creates a new linked list, containing only the record for the request.

It always grants a lock request on a data item that is not currently locked. But if the transaction requests a lock on an item on which a lock is currently held, the lock manager grants the request only if it is compatible with the locks that are currently held, and all earlier requests have been granted already. Otherwise the request has to wait.

• When the lock manager receives an unlock message from a transaction, it deletes the record for that data item in the linked list corresponding to that transaction. It tests the record that follows, if any, as described in the previous paragraph, to see if that request can now be granted. If it can, the lock manager grants that request, and processes the record following it, if any, similarly, and so on.

• If a transaction aborts, the lock manager deletes any waiting request made by the transaction. Once the database system has taken appropriate actions to undo the transaction (see Section 16.3), it releases all locks held by the aborted transaction.  

![](1.10.png)

**Figure 15.10** Lock table.

This algorithm guarantees freedom from starvation for lock requests, since a request can never be granted while a request received earlier is waiting to be granted. We study how to detect and handle deadlocks later, in Section 15.2.2. Section 17.2.1 describes an alternative implementation—one that uses shared memory instead of message passing for lock request/grant.

## Graph-Based Protocols

As noted in Section 15.1.3, if we wish to develop protocols that are not two phase, we need additional information on how each transaction will access the database. There are various models that can give us the additional information, each dif- fering in the amount of information provided. The simplest model requires that we have prior knowledge about the order in which the database items will be accessed. Given such information, it is possible to construct locking protocols that are not two phase, but that, nevertheless, ensure conflict serializability.

To acquire such prior knowledge, we impose a partial ordering → on the set **D** \= _{d_1, _d_2_, . . . , dh}_ of all data items. If _di_ → _d j_ , then any transaction accessing  


both _di_ and _d j_ must access _di_ before accessing _d j_ . This partial ordering may be the result of either the logical or the physical organization of the data, or it may be imposed solely for the purpose of concurrency control.

The partial ordering implies that the set **D** may now be viewed as a directed acyclic graph, called a **database graph**. In this section, for the sake of simplicity, we will restrict our attention to only those graphs that are rooted trees. We shall present a simple protocol, called the _tree protocol_, which is restricted to employ only _exclusive_ locks. References to other, more complex, graph-based locking protocols are in the bibliographical notes.

In the **tree protocol**, the only lock instruction allowed is lock-X. Each trans- action _Ti_ can lock a data item at most once, and must observe the following rules:

**1\.** The first lock by _Ti_ may be on any data item.

**2\.** Subsequently, a data item _Q_ can be locked by _Ti_ only if the parent of _Q_ is currently locked by _Ti_ .

**3\.** Data items may be unlocked at any time.

**4\.** A data item that has been locked and unlocked by _Ti_ cannot subsequently be relocked by _Ti_ .

All schedules that are legal under the tree protocol are conflict serializable. To illustrate this protocol, consider the database graph of Figure 15.11. The

following four transactions follow the tree protocol on this graph. We show only the lock and unlock instructions:

![](1.11.png)

**Figure 15.11** Tree-structured database graph.  


T10: lock-X(_B_); lock-X(_E_); lock-X(_D_); unlock(_B_); unlock(_E_); lock-X(_G_); unlock(_D_); unlock(_G_).

T11: lock-X(_D_); lock-X(_H_); unlock(_D_); unlock(_H_). T12: lock-X(_B_); lock-X(_E_); unlock(_E_); unlock(_B_). T13: lock-X(_D_); lock-X(_H_); unlock(_D_); unlock(_H_).

One possible schedule in which these four transactions participated appears in Figure 15.12. Note that, during its execution, transaction T10 holds locks on two _disjoint_ subtrees.

Observe that the schedule of Figure 15.12 is conflict serializable. It can be shown not only that the tree protocol ensures conflict serializability, but also that this protocol ensures freedom from deadlock.

The tree protocol in Figure 15.12 does not ensure recoverability and cas- cadelessness. To ensure recoverability and cascadelessness, the protocol can be modified to not permit release of exclusive locks until the end of the transaction. Holding exclusive locks until the end of the transaction reduces concurrency. Here is an alternative that improves concurrency, but ensures only recoverabil- ity: For each data item with an uncommitted write, we record which transaction performed the last write to the data item. Whenever a transaction _Ti_ performs a read of an uncommitted data item, we record a **commit dependency** of _Ti_ on the

![](1.12.png)

**Figure 15.12** Serializable schedule under the tree protocol.  


transaction that performed the last write to the data item. Transaction _Ti_ is then not permitted to commit until the commit of all transactions on which it has a commit dependency. If any of these transactions aborts, _Ti_ must also be aborted.

The tree-locking protocol has an advantage over the two-phase locking pro- tocol in that, unlike two-phase locking, it is deadlock-free, so no rollbacks are required. The tree-locking protocol has another advantage over the two-phase locking protocol in that unlocking may occur earlier. Earlier unlocking may lead to shorter waiting times, and to an increase in concurrency.

However, the protocol has the disadvantage that, in some cases, a transaction may have to lock data items that it does not access. For example, a transaction that needs to access data items _A_ and _J_ in the database graph of Figure 15.11 must lock not only _A_ and _J_, but also data items _B_, _D_, and _H_. This additional locking results in increased locking overhead, the possibility of additional waiting time, and a potential decrease in concurrency. Further, without prior knowledge of what data items will need to be locked, transactions will have to lock the root of the tree, and that can reduce concurrency greatly.

For a set of transactions, there may be conflict-serializable schedules that cannot be obtained through the tree protocol. Indeed, there are schedules possible under the two-phase locking protocol that are not possible under the tree protocol, and vice versa. Examples of such schedules are explored in the exercises.