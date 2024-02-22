---
title: 'Serializability'
weight: 6
references:
    videos:
        - youtube:xWV1z5Du8N0
        - youtube:bjy-lxWdcqw
    links:
        - https://jepsen.io/consistency/models/serializable#:~:text=Informally%2C%20serializability%20means%20that%20transactions,sub%2Doperations%20performed%20in%20order.
        - https://www.geeksforgeeks.org/serializability-in-dbms/
    books:
        - b1:
            title: Serialization and Persistent Objects
            url: https://www.google.co.in/books/edition/Serialization_and_Persistent_Objects/DHDABAAAQBAJ?hl=en&gbpv=1&dq=Serializability+books&pg=PA20&printsec=frontcover
        - b2:
            title: Java 6 Programming Black Book, New Ed
            url: https://www.google.co.in/books/edition/Java_6_Programming_Black_Book_New_Ed/SSyuJa04uv8C?hl=en&gbpv=0
---

# Serializability

Before we can consider how the concurrency-control component of the database system can ensure serializability, we consider how to determine when a schedule is serializable. Certainly, serial schedules are serializable, but if steps of multiple transactions are interleaved, it is harder to determine whether a schedule is seri- alizable. Since transactions are programs, it is difficult to determine exactly what operations a transaction performs and how operations of various transactions in- teract. For this reason, we shall not consider the various types of operations that a transaction can perform on a data item, but instead consider only two operations: read and write. We assume that, between a read(_Q_) instruction and a write(_Q_) instruction on a data item _Q_, a transaction may perform an arbitrary sequence of operations on the copy of _Q_ that is residing in the local buffer of the transaction. In this model, the only significant operations of a transaction, from a scheduling point of view, are its read and write instructions. Commit operations, though relevant, are not considered until Section 14.7. We therefore may show only read and write instructions in schedules, as we do for schedule 3 in Figure 14.6.

In this section, we discuss different forms of schedule equivalence, but focus on a particular form called **conflict serializability**.

Let us consider a schedule _S_ in which there are two consecutive instructions, _I_ and _J_ , of transactions _Ti_ and _Tj_ , respectively (_i_ = _j_). If _I_ and _J_ refer to different data items, then we can swap _I_ and _J_ without affecting the results of any instruc- tion in the schedule. However, if _I_ and _J_ refer to the same data item _Q_, then the order of the two steps may matter. Since we are dealing with only read and write instructions, there are four cases that we need to consider:

**1\.** _I_ \= read(_Q_), _J_ \= read(_Q_). The order of _I_ and _J_ does not matter, since the same value of _Q_ is read by _Ti_ and _Tj_ , regardless of the order.

**2\.** _I_ \= read(_Q_), _J_ \= write(_Q_). If _I_ comes before _J_ , then _Ti_ does not read the value of _Q_ that is written by _Tj_ in instruction _J_ . If _J_ comes before _I_ , then _Ti_ reads the value of _Q_ that is written by _Tj_ . Thus, the order of _I_ and _J_ matters.

![](6.png)

**Figure 14.6** Schedule 3—showing only the read and write instructions.  


![](7.png)

**Figure 14.7** Schedule 5—schedule 3 after swapping of a pair of instructions.

**3\.** _I_ \= write(_Q_), _J_ \= read(_Q_). The order of _I_ and _J_ matters for reasons similar to those of the previous case.

**4\.** _I_ \= write(_Q_), _J_ \= write(_Q_). Since both instructions are write operations, the order of these instructions does not affect either _Ti_ or _Tj_ . However, the value obtained by the next read(_Q_) instruction of _S_ is affected, since the result of only the latter of the two write instructions is preserved in the database. If there is no other write(_Q_) instruction after _I_ and _J_ in _S_, then the order of _I_ and _J_ directly affects the final value of _Q_ in the database state that results from schedule _S_.

Thus, only in the case where both _I_ and _J_ are read instructions does the relative order of their execution not matter.

We say that _I_ and _J_ **conflict** if they are operations by different transactions on the same data item, and at least one of these instructions is a write operation.

To illustrate the concept of conflicting instructions, we consider schedule 3in Figure 14.6. The write(_A_) instruction of T1 conflicts with the read(_A_) instruction of T2\. However, the write(_A_) instruction of T2 does not conflict with the read(_B_) instruction of T1, because the two instructions access different data items.
![](8.png)

**Figure 14.8** Schedule 6—a serial schedule that is equivalent to schedule 3.  

![](9.png)

**Figure 14.9** Schedule 7.

Let _I_ and _J_ be consecutive instructions of a schedule _S_. If _I_ and _J_ are in- structions of different transactions and _I_ and _J_ do not conflict, then we can swap the order of _I_ and _J_ to produce a new schedule _S_′. _S_ is equivalent to _S_′, since all instructions appear in the same order in both schedules except for _I_ and _J_ , whose order does not matter.

Since the write(_A_) instruction of T2 in schedule 3 of Figure 14.6 does not conflict with the read(_B_) instruction of T1, we can swap these instructions to generate an equivalent schedule, schedule 5, in Figure 14.7. Regardless of the initial system state, schedules 3 and 5 both produce the same final system state.

We continue to swap nonconflicting instructions:

• Swap the read(_B_) instruction of T1 with the read(_A_) instruction of T2.

• Swap the write(_B_) instruction of T1 with the write(_A_) instruction of T2.

• Swap the write(_B_) instruction of T1 with the read(_A_) instruction of T2.

The final result of these swaps, schedule 6 of Figure 14.8, is a serial schedule. Note that schedule 6 is exactly the same as schedule 1, but it shows only the read and write instructions. Thus, we have shown that schedule 3 is equivalent to a serial schedule. This equivalence implies that, regardless of the initial system state, schedule 3 will produce the same final state as will some serial schedule.

If a schedule _S_ can be transformed into a schedule _S_′ by a series of swaps of nonconflicting instructions, we say that _S_ and _S_′ are **conflict equivalent**.2

Not all serial schedules are conflict equivalent to each other. For example, schedules 1 and 2 are not conflict equivalent.

The concept of conflict equivalence leads to the concept of conflict serializ- ability. We say that a schedule _S_ is **conflict serializable** if it is conflict equivalent to a serial schedule. Thus, schedule 3 is conflict serializable, since it is conflict equivalent to the serial schedule 1.

Finally, consider schedule 7 of Figure 14.9; it consists of only the significant operations (that is, the read and write) of transactions T3 and T4\. This schedule is not conflict serializable, since it is not equivalent to either the serial schedule _<T_3,T4_\>_ or the serial schedule _<T_4,T3_\>_.

2We use the term _conflict equivalent_ to distinguish the way we have just defined equivalence from other definitions that we shall discuss later on in this section.  

![](10.png)

**Figure 14.10** Precedence graph for (a) schedule 1 and (b) schedule 2.

We now present a simple and efficient method for determining conflict seri- alizability of a schedule. Consider a schedule _S_. We construct a directed graph, called a **precedence graph**, from _S_. This graph consists of a pair _G_ \= (_V, E_), where _V_ is a set of vertices and _E_ is a set of edges. The set of vertices consists of all the transactions participating in the schedule. The set of edges consists of all edges _Ti_ → _Tj_ for which one of three conditions holds:

**1\.** _Ti_ executes write(_Q_) before _Tj_ executes read(_Q_).

**2\.** _Ti_ executes read(_Q_) before _Tj_ executes write(_Q_).

**3\.** _Ti_ executes write(_Q_) before _Tj_ executes write(_Q_).

If an edge _Ti_ → _Tj_ exists in the precedence graph, then, in any serial schedule _S_′ equivalent to _S_, _Ti_ must appear before _Tj_ .

For example, the precedence graph for schedule 1 in Figure 14.10a contains the single edge T1 → T2, since all the instructions of T1 are executed before the first instruction of T2 is executed. Similarly, Figure 14.10b shows the precedence graph for schedule 2 with the single edge T2 → T1, since all the instructions of T2 are executed before the first instruction of T1 is executed.

The precedence graph for schedule 4 appears in Figure 14.11. It contains the edge T1 → T2, because T1 executes read(_A_) before T2 executes write(_A_). It also contains the edge T2 → T1, because T2 executes read(_B_) before T1 executes write(_B_).

If the precedence graph for _S_ has a cycle, then schedule _S_ is not conflict serial- izable. If the graph contains no cycles, then the schedule _S_ is conflict serializable.

A **serializability order** of the transactions can be obtained by finding a linear order consistent with the partial order of the precedence graph. This process is called **topological sorting**. There are, in general, several possible linear orders that
![](11.png)

**Figure 14.11** Precedence graph for schedule   
![](12.png)

**Figure 14.12** Illustration of topological sorting.

can be obtained through a topological sort. For example, the graph of Figure 14.12a has the two acceptable linear orderings shown in Figures 14.12b and 14.12c.

Thus, to test for conflict serializability, we need to construct the precedence graph and to invoke a cycle-detection algorithm. Cycle-detection algorithms can be found in standard textbooks on algorithms. Cycle-detection algorithms, such as those based on depth-first search, require on the order of _n_2 operations, where _n_ is the number of vertices in the graph (that is, the number of transactions).

Returning to our previous examples, note that the precedence graphs for schedules 1 and 2 (Figure 14.10) indeed do not contain cycles. The precedence graph for schedule 4 (Figure 14.11), on the other hand, contains a cycle, indicating that this schedule is not conflict serializable.

It is possible to have two schedules that produce the same outcome, but that are not conflict equivalent. For example, consider transaction T5, which transfers $10 from account _B_ to account _A_. Let schedule 8 be as defined in Figure 14.13. We claim that schedule 8 is not conflict equivalent to the serial schedule _<T_1,T5_\>_, since, in schedule 8, the write(_B_) instruction of T5 conflicts with the read(_B_) in- struction of T1\. This creates an edge T5 → T1 in the precedence graph. Similarly, we see that the write(_A_) instruction of T1 conflicts with the read instruction of T5  

![](13.png)

**Figure 14.13** Schedule 8.

creating an edge T1 → T5\. This shows that the precedence graph has a cycle and that schedule 8 is not serializable. However, the final values of accounts _A_ and _B_ after the execution of either schedule 8 or the serial schedule _<T_1,T5_\>_ are the same—$960 and $2040, respectively.

We can see from this example that there are less-stringent definitions of sched- ule equivalence than conflict equivalence. For the system to determine that sched- ule 8 produces the same outcome as the serial schedule _<T_1,T5_\>_, it must analyze the computation performed by T1 and T5, rather than just the read and write op- erations. In general, such analysis is hard to implement and is computationally expensive. In our example, the final result is the same as that of a serial schedule because of the mathematical fact that addition and subtraction are commutative. While this may be easy to see in our simple example, the general case is not so easy since a transaction may be expressed as a complex SQL statement, a Java program with JDBC calls, etc.

However, there are other definitions of schedule equivalence based purely on the read and write operations. One such definition is _view equivalence_, a definition that leads to the concept of _view serializability_. View serializability is not used in practice due to its high degree of computational complexity.3 We therefore defer discussion of view serializability to Chapter 15, but, for completeness, note here that the example of schedule 8 is not view serializable.