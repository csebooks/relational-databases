---
title: 'Validation-Based Protocols' 
weight: 5
---

# Validation-Based Protocols

In cases where a majority of transactions are read-only transactions, the rate of conflicts among transactions may be low. Thus, many of these transactions, if executed without the supervision of a concurrency-control scheme, would nev- ertheless leave the system in a consistent state. A concurrency-control scheme imposes overhead of code execution and possible delay of transactions. It may be better to use an alternative scheme that imposes less overhead. A difficulty in reducing the overhead is that we do not know in advance which transactions will be involved in a conflict. To gain that knowledge, we need a scheme for _monitoring_ the system.

The **validation protocol** requires that each transaction _Ti_ executes in two or three different phases in its lifetime, depending on whether it is a read-only or an update transaction. The phases are, in order:  


**VIEW SERIALIZABILITY**

There is another form of equivalence that is less stringent than conflict equiv- alence, but that, like conflict equivalence, is based on only the read and write operations of transactions.

Consider two schedules _S_ and _S_′, where the same set of transactions partici- pates in both schedules. The schedules _S_ and _S_′ are said to be **view equivalent** if three conditions are met:

**1\.** For each data item _Q_, if transaction _Ti_ reads the initial value of _Q_ in schedule _S_, then transaction _Ti_ must, in schedule _S_′, also read the initial value of _Q_.

**2\.** For each data item _Q_, if transaction _Ti_ executes read(_Q_) in schedule _S_, and if that value was produced by a write(_Q_) operation executed by transaction _Tj_ , then the read(_Q_) operation of transaction _Ti_ must, in schedule _S_′, also read the value of _Q_ that was produced by the same write(_Q_) operation of transaction _Tj_ .

**3\.** For each data item _Q_, the transaction (if any) that performs the final write(_Q_) operation in schedule _S_ must perform the final write(_Q_) operation in schedule _S_′.

Conditions 1 and 2 ensure that each transaction reads the same values in both schedules and, therefore, performs the same computation. Condition 3, coupled with conditions 1 and 2, ensures that both schedules result in the same final system state.

The concept of view equivalence leads to the concept of view serializability. We say that a schedule _S_ is **view serializable** if it is view equivalent to a serial schedule.

As an illustration, suppose that we augment schedule 4 with transaction _T_29, and obtain the following view serializable (schedule 5):

![](1.18.1-validation.png)

Indeed, schedule 5 is view equivalent to the serial schedule _<T_27_, T_28_, T_29_\>_, since the one read(_Q_) instruction reads the initial value of _Q_ in both schedules and _T_29 performs the final write of _Q_ in both schedules.

Every conflict-serializable schedule is also view serializable, but there are view-serializable schedules that are not conflict serializable. Indeed, schedule 5 is not conflict serializable, since every pair of consecutive instructions conflicts, and, thus, no swapping of instructions is possible.

Observe that, in schedule 5, transactions _T_28 and _T_29 perform write(_Q_) oper- ations without having performed a read(_Q_) operation. Writes of this sort are called **blind writes**. Blind writes appear in any view-serializable schedule that is not conflict serializable.  


**1\. Read phase**. During this phase, the system executes transaction _Ti_ . It reads the values of the various data items and stores them in variables local to _Ti_ . It performs all write operations on temporary local variables, without updates of the actual database.

**2\. Validation phase**. The validation test (described below) is applied to trans- action _Ti_ . This determines whether _Ti_ is allowed to proceed to the write phase without causing a violation of serializability. If a transaction fails the validation test, the system aborts the transaction.

**3\. Write phase**. If the validation test succeeds for transaction _Ti_ , the temporary local variables that hold the results of any write operations performed by _Ti_ are copied to the database. Read-only transactions omit this phase.

Each transaction must go through the phases in the order shown. However, phases of concurrently executing transactions can be interleaved.

To perform the validation test, we need to know when the various phases of transactions took place. We shall, therefore, associate three different timestamps with each transaction _Ti_ :

**1\. Start**(_Ti_ ), the time when _Ti_ started its execution.

**2\. Validation**(_Ti_ ), the time when _Ti_ finished its read phase and started its validation phase.

**3\. Finish**(_Ti_ ), the time when _Ti_ finished its write phase.

We determine the serializability order by the timestamp-ordering technique, using the value of the timestamp Validation(_Ti_ ). Thus, the value TS(_Ti_ ) = Valida- tion(_Ti_ ) and, if TS(_Tj_ ) _<_ TS(_Tk_), then any produced schedule must be equivalent to a serial schedule in which transaction _Tj_ appears before transaction _Tk_ . The reason we have chosen Validation(_Ti_ ), rather than Start(_Ti_ ), as the timestamp of transaction _Ti_ is that we can expect faster response time provided that conflict rates among transactions are indeed low.

The **validation test** for transaction _Ti_ requires that, for all transactions _Tk_ with TS(_Tk_) _<_ TS(_Ti_ ), one of the following two conditions must hold:

**1\.** Finish(_Tk_) _<_ Start(_Ti_ ). Since _Tk_ completes its execution before _Ti_ started, the serializability order is indeed maintained.

**2\.** The set of data items written by _Tk_ does not intersect with the set of data items read by _Ti_ , and _Tk_ completes its write phase before _Ti_ starts its validation phase (Start(_Ti_ ) _<_ Finish(_Tk_) _<_ Validation(_Ti_ )). This condition ensures that the writes of _Tk_ and _Ti_ do not overlap. Since the writes of _Tk_ do not affect the read of _Ti_ , and since _Ti_ cannot affect the read of _Tk_ , the serializability order is indeed maintained.  

![](1.19.png)

**Figure 15.19** Schedule 6, a schedule produced by using validation.

As an illustration, consider again transactions _T_25 and _T_26\. Suppose that TS(_T_25) _<_ TS(_T_26). Then, the validation phase succeeds in the schedule 6 in Figure 15.19. Note that the writes to the actual variables are performed only after the validation phase of _T_26\. Thus, _T_25 reads the old values of _B_ and _A_, and this schedule is serializable.

The validation scheme automatically guards against cascading rollbacks, since the actual writes take place only after the transaction issuing the write has committed. However, there is a possibility of starvation of long transactions, due to a sequence of conflicting short transactions that cause repeated restarts of the long transaction. To avoid starvation, conflicting transactions must be tem- porarily blocked, to enable the long transaction to finish.

This validation scheme is called the **optimistic concurrency-control** scheme since transactions execute optimistically, assuming they will be able to finish execution and validate at the end. In contrast, locking and timestamp ordering are pessimistic in that they force a wait or a rollback whenever a conflict is detected, even though there is a chance that the schedule may be conflict serializable.