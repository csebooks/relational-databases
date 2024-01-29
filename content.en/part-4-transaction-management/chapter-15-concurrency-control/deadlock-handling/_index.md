---
title: 'Deadlock Handling' 
weight: 2
---

# Deadlock Handling

A system is in a deadlock state if there exists a set of transactions such that every transaction in the set is waiting for another transaction in the set. More precisely, there exists a set of waiting transactions _{T_0, T1_, . . . , Tn}_ such that T0 is waiting for a data item that T1 holds, and T1 is waiting for a data item that T2 holds, and _. . ._ , and _Tn_−1 is waiting for a data item that _Tn_ holds, and _Tn_ is waiting for a data item that T0 holds. None of the transactions can make progress in such a situation.

The only remedy to this undesirable situation is for the system to invoke some drastic action, such as rolling back some of the transactions involved in the deadlock. Rollback of a transaction may be partial: That is, a transaction may be rolled back to the point where it obtained a lock whose release resolves the deadlock.

There are two principal methods for dealing with the deadlock problem. We can use a **deadlock prevention** protocol to ensure that the system will _never_ enter a deadlock state. Alternatively, we can allow the system to enter a deadlock state, and then try to recover by using a **deadlock detection** and **deadlock recovery** scheme. As we shall see, both methods may result in transaction rollback. Preven- tion is commonly used if the probability that the system would enter a deadlock state is relatively high; otherwise, detection and recovery are more efficient.

Note that a detection and recovery scheme requires overhead that includes not only the run-time cost of maintaining the necessary information and of executing the detection algorithm, but also the potential losses inherent in recovery from a deadlock.  

## Deadlock Prevention

There are two approaches to deadlock prevention. One approach ensures that no cyclic waits can occur by ordering the requests for locks, or requiring all locks to be acquired together. The other approach is closer to deadlock recovery, and performs transaction rollback instead of waiting for a lock, whenever the wait could potentially result in a deadlock.

The simplest scheme under the first approach requires that each transaction locks all its data items before it begins execution. Moreover, either all are locked in one step or none are locked. There are two main disadvantages to this protocol: (1) it is often hard to predict, before the transaction begins, what data items need to be locked; (2) data-item utilization may be very low, since many of the data items may be locked but unused for a long time.

Another approach for preventing deadlocks is to impose an ordering of all data items, and to require that a transaction lock data items only in a sequence consistent with the ordering. We have seen one such scheme in the tree protocol, which uses a partial ordering of data items.

A variation of this approach is to use a total order of data items, in conjunction with two-phase locking. Once a transaction has locked a particular item, it cannot request locks on items that precede that item in the ordering. This scheme is easy to implement, as long as the set of data items accessed by a transaction is known when the transaction starts execution. There is no need to change the underlying concurrency-control system if two-phase locking is used: All that is needed is to ensure that locks are requested in the right order.

The second approach for preventing deadlocks is to use preemption and transaction rollbacks. In preemption, when a transaction _Tj_ requests a lock that transaction _Ti_ holds, the lock granted to _Ti_ may be **preempted** by rolling back of _Ti_ , and granting of the lock to _Tj_ . To control the preemption, we assign a unique timestamp, based on a counter or on the system clock, to each transaction when it begins. The system uses these timestamps only to decide whether a transaction should wait or roll back. Locking is still used for concurrency control. If a transaction is rolled back, it retains its _old_ timestamp when restarted. Two different deadlock-prevention schemes using timestamps have been proposed:

**1\.** The **wait–die** scheme is a nonpreemptive technique. When transaction _Ti_ requests a data item currently held by _Tj_ , _Ti_ is allowed to wait only if it has a timestamp smaller than that of _Tj_ (that is, _Ti_ is older than _Tj_ ). Otherwise, _Ti_ is rolled back (dies).

For example, suppose that transactions T14, T15, and T16 have timestamps 5, 10, and 15, respectively. If T14 requests a data item held by T15, then T14 will wait. If T24 requests a data item held by T15, then T16 will be rolled back.

**2\.** The **wound–wait** scheme is a preemptive technique. It is a counterpart to the wait–die scheme. When transaction _Ti_ requests a data item currently held by _Tj_ , _Ti_ is allowed to wait only if it has a timestamp larger than that of _Tj_ (that is, _Ti_ is younger than _Tj_ ). Otherwise, _Tj_ is rolled back (_Tj_ is _wounded_ by _Ti_ ).  


Returning to our example, with transactions T14, T15, and T16, if T14 requests a data item held by T15, then the data item will be preempted from T15, and T15 will be rolled back. If T16 requests a data item held by T15, then T16 will wait.

The major problem with both of these schemes is that unnecessary rollbacks may occur.

Another simple approach to deadlock prevention is based on **lock timeouts**. In this approach, a transaction that has requested a lock waits for at most a specified amount of time. If the lock has not been granted within that time, the transaction is said to time out, and it rolls itself back and restarts. If there was in fact a deadlock, one or more transactions involved in the deadlock will time out and roll back, allowing the others to proceed. This scheme falls somewhere between deadlock prevention, where a deadlock will never occur, and deadlock detection and recovery, which Section 15.2.2 discusses.

The timeout scheme is particularly easy to implement, and works well if transactions are short and if long waits are likely to be due to deadlocks. However, in general it is hard to decide how long a transaction must wait before timing out. Too long a wait results in unnecessary delays once a deadlock has occurred. Too short a wait results in transaction rollback even when there is no deadlock, leading to wasted resources. Starvation is also a possibility with this scheme. Hence, the timeout-based scheme has limited applicability.

## Deadlock Detection and Recovery

If a system does not employ some protocol that ensures deadlock freedom, then a detection and recovery scheme must be used. An algorithm that examines the state of the system is invoked periodically to determine whether a deadlock has occurred. If one has, then the system must attempt to recover from the deadlock. To do so, the system must:

• Maintain information about the current allocation of data items to transac- tions, as well as any outstanding data item requests.

• Provide an algorithm that uses this information to determine whether the system has entered a deadlock state.

• Recover from the deadlock when the detection algorithm determines that a deadlock exists.

In this section, we elaborate on these issues.

### Deadlock Detection

Deadlocks can be described precisely in terms of a directed graph called a **wait- for graph**. This graph consists of a pair _G_ \= (_V_, _E_), where _V_ is a set of vertices and _E_ is a set of edges. The set of vertices consists of all the transactions in the system. Each element in the set _E_ of edges is an ordered pair _Ti_ → _Tj_ . If _Ti_ → _Tj_ is in _E_,  

![](1.13.png)

**Figure 15.13** Wait-for graph with no cycle.

then there is a directed edge from transaction _Ti_ to _Tj_ , implying that transaction _Ti_ is waiting for transaction _Tj_ to release a data item that it needs.

When transaction _Ti_ requests a data item currently being held by transaction _Tj_ , then the edge _Ti_ → _Tj_ is inserted in the wait-for graph. This edge is removed only when transaction _Tj_ is no longer holding a data item needed by transaction _Ti_ .

A deadlock exists in the system if and only if the wait-for graph contains a cycle. Each transaction involved in the cycle is said to be deadlocked. To detect deadlocks, the system needs to maintain the wait-for graph, and periodically to invoke an algorithm that searches for a cycle in the graph.

To illustrate these concepts, consider the wait-for graph in Figure 15.13, which depicts the following situation:

• Transaction T17 is waiting for transactions T18 and T19.

• Transaction T19 is waiting for transaction T18.

• Transaction T18 is waiting for transaction T20.

Since the graph has no cycle, the system is not in a deadlock state. Suppose now that transaction T20 is requesting an item held by T19\. The edge

T20 → T19 is added to the wait-for graph, resulting in the new system state in Figure 15.14. This time, the graph contains the cycle:

T18 → T20 → T19 → T18

implying that transactions T18, T19, and T20 are all deadlocked. Consequently, the question arises: When should we invoke the detection

algorithm? The answer depends on two factors:

**1\.** How often does a deadlock occur?

**2\.** How many transactions will be affected by the deadlock?

If deadlocks occur frequently, then the detection algorithm should be in- voked more frequently. Data items allocated to deadlocked transactions will be  

![](1.14.png)

**Figure 15.14** Wait-for graph with a cycle.

unavailable to other transactions until the deadlock can be broken. In addition, the number of cycles in the graph may also grow. In the worst case, we would invoke the detection algorithm every time a request for allocation could not be granted immediately.

### Recovery from Deadlock

When a detection algorithm determines that a deadlock exists, the system must **recover** from the deadlock. The most common solution is to roll back one or more transactions to break the deadlock. Three actions need to be taken:

**1\. Selection of a victim**. Given a set of deadlocked transactions, we must deter- mine which transaction (or transactions) to roll back to break the deadlock. We should roll back those transactions that will incur the minimum cost. Unfortunately, the term _minimum cost_ is not a precise one. Many factors may determine the cost of a rollback, including:

a. How long the transaction has computed, and how much longer the transaction will compute before it completes its designated task.

b. How many data items the transaction has used.

c. How many more data items the transaction needs for it to complete.

d. How many transactions will be involved in the rollback.

**2\. Rollback**. Once we have decided that a particular transaction must be rolled back, we must determine how far this transaction should be rolled back.

The simplest solution is a **total rollback**: Abort the transaction and then restart it. However, it is more effective to roll back the transaction only as far as necessary to break the deadlock. Such **partial rollback** requires the system to maintain additional information about the state of all the running transactions. Specifically, the sequence of lock requests/grants and updates performed by the transaction needs to be recorded. The deadlock detection mechanism should decide which locks the selected transaction needs to release in order to break the deadlock. The selected transaction must be rolled back to the point where it obtained the first of these locks, undoing all actions it took after that point. The recovery mechanism must be capable  

of performing such partial rollbacks. Furthermore, the transactions must be capable of resuming execution after a partial rollback. See the bibliographical notes for relevant references.

**3\. Starvation**. In a system where the selection of victims is based primarily on cost factors, it may happen that the same transaction is always picked as a victim. As a result, this transaction never completes its designated task, thus there is **starvation**. We must ensure that a transaction can be picked as a victim only a (small) finite number of times. The most common solution is to include the number of rollbacks in the cost factor.

