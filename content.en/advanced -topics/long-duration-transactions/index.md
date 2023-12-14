---
title: Long-Duration Transactions
weight: 19
---

Long-Duration Transactions## Long-Duration Transactions

The transaction concept developed initially in the context of data-processing applications, in which most transactions are noninteractive and of short duration. Although the techniques presented here and earlier in Chapters 14, 15, and 16 work well in those applications, serious problems arise when this concept is applied to database systems that involve human interaction. Such transactions have these key properties:

- **Long duration**. Once a human interacts with an active transaction, that trans- action becomes a **long-duration transaction** from the perspective of the com- puter, since human response time is slow relative to computer speed. Further- more, in design applications, the human activity may involve hours, days, or an even longer period. Thus, transactions may be of long duration in human terms, as well as in machine terms.

- **Exposure of uncommitted data**. Data generated and displayed to a user by a long-duration transaction are uncommitted, since the transaction may abort. Thus, users—and, as a result, other transactions—may be forced to read uncommitted data. If several users are cooperating on a project, user transactions may need to exchange data prior to transaction commit.

- **Subtasks**. An interactive transaction may consist of a set of subtasks initiated by the user. The user may wish to abort a subtask without necessarily causing the entire transaction to abort.  
- **Recoverability**. It is unacceptable to abort a long-duration interactive trans- action because of a system crash. The active transaction must be recovered to a state that existed shortly before the crash so that relatively little human work is lost.

- **Performance**. Good performance in an interactive transaction system is de- fined as fast response time. This definition is in contrast to that in a non- interactive system, in which high throughput (number of transactions per second) is the goal. Systems with high throughput make efficient use of system resources. However, in the case of interactive transactions, the most costly resource is the user. If the efficiency and satisfaction of the user is to be optimized, response time should be fast (from a human perspective). In those cases where a task takes a long time, response time should be predictable (that is, the variance in response times should be low), so that users can manage their time well.

In Sections 26.6.1 through 26.6.5, we shall see why these five properties are in- compatible with the techniques presented thus far and shall discuss how those techniques can be modified to accommodate long-duration interactive transac- tions.

### Nonserializable Executions

The properties that we discussed make it impractical to enforce the requirement used in earlier chapters that only serializable schedules be permitted. Each of the concurrency-control protocols of Chapter 15 has adverse effects on long-duration transactions:

- **Two-phase locking**. When a lock cannot be granted, the transaction request- ing the lock is forced to wait for the data item in question to be unlocked. The duration of this wait is proportional to the duration of the transaction holding the lock. If the data item is locked by a short-duration transaction, we expect that the waiting time will be short (except in case of deadlock or extraordinary system load). However, if the data item is locked by a long- duration transaction, the wait will be of long duration. Long waiting times lead to both longer response time and an increased chance of deadlock.

- **Graph-based protocols**. Graph-based protocols allow for locks to be released earlier than under the two-phase locking protocols, and they prevent dead- lock. However, they impose an ordering on the data items. Transactions must lock data items in a manner consistent with this ordering. As a result, a trans- action may have to lock more data than it needs. Furthermore, a transaction must hold a lock until there is no chance that the lock will be needed again. Thus, long-duration lock waits are likely to occur.

- **Timestamp-based protocols**. Timestamp protocols never require a transac- tion to wait. However, they do require transactions to abort under certain cir- cumstances. If a long-duration transaction is aborted, a substantial amount of work is lost. For noninteractive transactions, this lost work is a performance issue. For interactive transactions, the issue is also one of user satisfaction. It is highly undesirable for a user to find that several hours’ worth of work have been undone.
- **Validation protocols**. Like timestamp-based protocols, validation protocols enforce serializability by means of transaction abort.

Thus, it appears that the enforcement of serializability results in long-duration waits, in abort of long-duration transactions, or in both. There are theoretical results, cited in the bibliographical notes, that substantiate this conclusion.

Further difficulties with the enforcement of serializability arise when we con- sider recovery issues. We previously discussed the problem of cascading rollback, in which the abort of a transaction may lead to the abort of other transactions. This phenomenon is undesirable, particularly for long-duration transactions. If locking is used, exclusive locks must be held until the end of the transaction, if cascading rollback is to be avoided. This holding of exclusive locks, however, increases the length of transaction waiting time.

Thus, it appears that the enforcement of transaction atomicity must either lead to an increased probability of long-duration waits or create a possibility of cascading rollback.

Snapshot isolation, described in Section 15.7, can provide a partial solution to these issues, as can the _optimistic concurrency control without read validation_ protocol described in Section 15.9.3. The latter protocol was in fact designed specifically to deal with long duration transactions that involve user interaction. Although it does not guarantee serializability, optimistic concurrency control without read validation is quite widely used.

However, when transactions are of long duration, conflicting updates are more likely, resulting in additional waits or aborts. These considerations are the basis for the alternative concepts of correctness of concurrent executions and transaction recovery that we consider in the remainder of this section.

### Concurrency Control

The fundamental goal of database concurrency control is to ensure that concur- rent execution of transactions does not result in a loss of database consistency. The concept of serializability can be used to achieve this goal, since all serializable schedules preserve consistency of the database. However, not all schedules that preserve consistency of the database are serializable. For an example, consider again a bank database consisting of two accounts _A_ and _B_, with the consistency requirement that the sum _A_ \+ _B_ be preserved. Although the schedule of Fig- ure 26.5 is not conflict serializable, it nevertheless preserves the sum of _A + B_. It also illustrates two important points about the concept of correctness without serializability.

- Correctness depends on the specific consistency constraints for the database.

- Correctness depends on the properties of operations performed by each trans- action.  

| T~1~ | T~2~ |
| ----------- | ----------- |
| read(_A_) _A_ := _A_ − 50 write(_A_) | read(_B_) _B_ := _B_ − 10 write(_B_) |
| read(_B_) _B_ := _B_ \+ 50 write(_B_) | read(_A_) _A_ := _A_ \+ 10 write(_A_) |

**Figure 26.5** A non-conflict-serializable schedule.

In general it is not possible to perform an automatic analysis of low-level opera- tions by transactions and check their effect on database consistency constraints. However, there are simpler techniques. One is to use the database consistency constraints as the basis for a split of the database into subdatabases on which con- currency can be managed separately. Another is to treat some operations besides **read** and **write** as fundamental low-level operations and to extend concurrency control to deal with them.

The bibliographical notes reference other techniques for ensuring consistency without requiring serializability. Many of these techniques exploit variants of multiversion concurrency control (see Section 15.6). For older data-processing applications that need only one version, multiversion protocols impose a high space overhead to store the extra versions. Since many of the new database applications require the maintenance of versions of data, concurrency-control techniques that exploit multiple versions are practical.

### Nested and Multilevel Transactions

A long-duration transaction can be viewed as a collection of related subtasks or subtransactions. By structuring a transaction as a set of subtransactions, we are able to enhance parallelism, since it may be possible to run several subtransactions in parallel. Furthermore, it is possible to deal with failure of a subtransaction (due to abort, system crash, and so on) without having to roll back the entire long-duration transaction.

A nested or multilevel transaction _T_ consists of a set _T_ \= {t~1~,t~2~, _. . ._ , _t~n~_} of subtransactions and a partial order _P_ on _T_. A subtransaction _ti_ in _T_ may abort without forcing _T_ to abort. Instead, _T_ may either restart _ti_ or simply choose not to run _t~i~_ . If _t~i~_ commits, this action does not make _ti_ permanent (unlike the situation in Chapter 16). Instead, _ti commits to T_, and may still abort (or require compensation —see Section 26.6.4) if _T_ aborts. An execution of _T_ must not violate the partial  order _P_. That is, if an edge _t~i~_ → _t~j~_ appears in the precedence graph, then _t~j~_ → _t~i~_ must not be in the transitive closure of _P_.

Nesting may be several levels deep, representing a subdivision of a transac- tion into subtasks, subsubtasks, and so on. At the lowest level of nesting, we have the standard database operations **read** and **write** that we have used previously.

If a subtransaction of _T_ is permitted to release locks on completion, _T_ is called a **multilevel transaction**. When a multilevel transaction represents a long- duration activity, the transaction is sometimes referred to as a **saga**. Alternatively, if locks held by a subtransaction _t~i~_ of _T_ are automatically assigned to _T_ on completion of _t~i~_ , _T_ is called a **nested transaction**.

Although the main practical value of multilevel transactions arises in com- plex, long-duration transactions, we shall use the simple example of Figure 26.5 to show how nesting can create higher-level operations that may enhance con- currency. We rewrite transaction _T~1~, using subtransactions _T_~1~,~1~ and _T_~1~_,~2~, which perform increment or decrement operations:

- T~1~ consists of:

  ◦ T~1~_,~1~_, which subtracts 50 from _A_.
  
  ◦ T~1~_,_~2~_,_ which adds 50 to _B_.

Similarly, we rewrite transaction _T_~2~_,_ using subtransactions T_~2~,~1~ and _T_~2~_,~2~,_ which also perform increment or decrement operations:

- T~2~ consists of:

  ◦ T~2~,~1~ which subtracts 10 from _B_.
  
  ◦T~2~,~2~ which adds 10 to _A_.

No ordering is specified on _T~1,1~, T_~1,2~_, T_~2,1~_,_ and _T~2,2~. Any execution of these sub- transactions will generate a correct result. The schedule of Figure 26.5 corresponds to the schedule _<  _T~1,1~, T_~1,2~_, T_~2,1~_,_ and _T~2,2~\>_.

To reduce the frequency of long-duration waiting, we arrange for uncommit- ted updates to be exposed to other concurrently executing transactions. Indeed, multilevel transactions may allow this exposure. However, the exposure of un- committed data creates the potential for cascading rollbacks. The concept of **com- pensating transactions** helps us to deal with this problem.

Let transaction _T_ be divided into several subtransactions _t_~1~_, t_~2~_, . . . , t~n~_. After a subtransaction _t~i~_ commits, it releases its locks. Now, if the outer-level transaction _T_ has to be aborted, the effect of its subtransactions must be undone. Suppose that subtransactions _t_~1~_, . . . , t~k~_ have committed, and that _t~k+1~ was executing when the decision to abort is made. We can undo the effects of _t~k+1~ by aborting that subtransaction. However, it is not possible to abort subtransactions _t_~1~_, . . . , t~k~_ , since they have committed already.

Instead, we execute a new subtransaction _ct~i~_ , called a _compensating transaction_, to undo the effect of a subtransaction _t~i~_ . Each subtransaction _t~i~_ is required to have a compensating transaction _ct~i~_ . The compensating transactions must be executed in the inverse order _ct~k~, . . . , ct_~1~. Here are several examples of compensation:

- Consider the schedule of Figure 26.5, which we have shown to be correct, although not conflict serializable. Each subtransaction releases its locks once it completes. Suppose that T~2~ fails just prior to termination, after T~2,2~ has re- leased its locks. We then run a compensating transaction for T~2,2~ that subtracts 10 from _A_ and a compensating transaction for T~2,1~ that adds 10 to _B_.

- Consider a database insert by transaction _T~i~_ that, as a side effect, causes a B+-tree index to be updated. The insert operation may have modified several nodes of the B+-tree index. Other transactions may have read these nodes in accessing data other than the record inserted by _T~i~_ . As mentioned in Sec- tion 16.7, we can undo the insertion by deleting the record inserted by _T~i~_ . The result is a correct, consistent B+-tree, but is not necessarily one with exactly the same structure as the one we had before _T~i~_ started. Thus, deletion is a compensating action for insertion.
- Consider a long-duration transaction T~i~ representing a travel reservation. Transaction _T_ has three subtransactions: T~i,1~, which makes airline reserva- tions; T~i,2~, which reserves rental cars; and T~i,3~, which reserves a hotel room. Suppose that the hotel cancels the reservation. Instead of undoing all of T~i~ , we compensate for the failure of T~i,3~ by deleting the old hotel reservation and making a new one.

If the system crashes in the middle of executing an outer-level transaction, its subtransactions must be rolled back when it recovers. The techniques described in Section 16.7 can be used for this purpose.

Compensation for the failure of a transaction requires that the semantics of the failed transaction be used. For certain operations, such as incrementation or insertion into a B+-tree, the corresponding compensation is easily defined. For more complex transactions, the application programmers may have to define the correct form of compensation at the time that the transaction is coded. For complex interactive transactions, it may be necessary for the system to interact with the user to determine the proper form of compensation.

### Implementation Issues

The transaction concepts discussed in this section create serious difficulties for implementation. We present a few of them here, and discuss how we can address these problems.

Long-duration transactions must survive system crashes. We can ensure that they will by performing a **redo** on committed subtransactions, and by performing either an **undo** or compensation for any short-duration subtransactions that were active at the time of the crash. However, these actions solve only part of the problem. In typical database systems, such internal system data as lock ta- bles and transaction timestamps are kept in volatile storage. For a long-duration transaction to be resumed after a crash, these data must be restored. Therefore, it is necessary to log not only changes to the database, but also changes to internal system data pertaining to long-duration transactions.

Logging of updates is made more complex when certain types of data items exist in the database. A data item may be a CAD design, text of a document, or another form of composite design. Such data items are physically large. Thus, storing both the old and new values of the data item in a log record is undesirable.

There are two approaches to reducing the overhead of ensuring the recover- ability of large data items:

- **Operation logging**. Only the operation performed on the data item and the data-item name are stored in the log. Operation logging is also called **logical logging**. For each operation, an inverse operation must exist. We perform **undo** using the inverse operation and **redo** using the operation itself. Recovery through operation logging is more difficult, since **redo** and **undo** are not idempotent. Further, using logical logging for an operation that updates multiple pages is greatly complicated by the fact that some, but not all, of the updated pages may have been written to the disk, so it is hard to apply either the **redo** or the **undo** of the operation on the disk image during recovery. Using physical redo logging and logical undo logging, as described in Section 16.7, provides the concurrency benefits of logical logging while avoiding the above pitfalls.

- **Logging and shadow paging**. Logging is used for modifications to small data items, but large data items are often made recoverable via a shadowing, or copy-on-write, technique. When we use shadowing, it is possible to reduce the overhead by keeping copies of only those pages that are actually modified.

Regardless of the technique used, the complexities introduced by long-duration transactions and large data items complicate the recovery process. Thus, it is desirable to allow certain noncritical data to be exempt from logging, and to rely instead on offline backups and human intervention.

