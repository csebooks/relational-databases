---
title: 'Recovery Algorithm'
weight: 4
---

# Recovery Algorithm

Until now, in discussing recovery, we have identified transactions that need to be redone and those that need to be undone, but we have not given a precise algorithm for performing these actions. We are now ready to present the full recovery algorithm using log records for recovery from transaction failure and a combination of the most recent checkpoint and log records to recover from a system crash.  


The recovery algorithm described in this section requires that a data item that has been updated by an uncommitted transaction cannot be modified by any other transaction, until the first transaction has either committed or aborted. Recall that this restriction was discussed earlier, in Section 16.3.3.

## Transaction Rollback

First consider transaction rollback during normal operation (that is, not during recovery from a system crash). Rollback of a transaction _Ti_ is performed as follows:

**1\.** The log is scanned backward, and for each log record of _Ti_ of the form _<Ti , Xj , V_1_, V_2_\>_ that is found:

a. The value _V_1 is written to data item _Xj_ , and

b. A special redo-only log record _<Ti, Xj , V_1_\>_ is written to the log, where _V_1 is the value being restored to data item _Xj_ during the rollback. These log records are sometimes called **compensation log records**. Such records do not need undo information, since we never need to undo such an undo operation. We shall explain later how they are used.

**2\.** Once the log record _<Ti_ start_\>_ is found the backward scan is stopped, and a log record _<Ti_ abort_\>_ is written to the log.

Observe that every update action performed by the transaction or on behalf of the transaction, including actions taken to restore data items to their old value, have now been recorded in the log. In Section 16.4.2 we shall see why this is a good idea.

## Recovery After a System Crash

Recovery actions, when the database system is restarted after a crash, take place in two phases:

**1\.** In the **redo phase**, the system replays updates of _all_ transactions by scanning the log forward from the last checkpoint. The log records that are replayed include log records for transactions that were rolled back before system crash, and those that had not committed when the system crash occurred. This phase also determines all transactions that were incomplete at the time of the crash, and must therefore be rolled back. Such incomplete transactions would either have been active at the time of the checkpoint, and thus would appear in the transaction list in the checkpoint record, or would have started later; further, such incomplete transactions would have neither a _<Ti_ abort_\>_ nor a _<Ti_ commit_\>_ record in the log.

The specific steps taken while scanning the log are as follows:

a. The list of transactions to be rolled back, undo-list, is initially set to the list _L_ in the _<_checkpoint _L>_ log record.  



b. Whenever a normal log record of the form _<Ti , Xj , V_1_, V_2_\>_, or a redo-only log record of the form _<Ti , Xj , V_2_\>_ is encountered, the operation is redone; that is, the value _V_2 is written to data item _Xj_ .

c. Whenever a log record of the form _<Ti_ start_\>_ is found, _Ti_ is added to undo-list.

d. Whenever a log record of the form _<Ti_ abort_\>_ or _<Ti_ commit_\>_ is found, _Ti_ is removed from undo-list.

At the end of the redo phase, undo-list contains the list of all transactions that are incomplete, that is, they neither committed nor completed rollback before the crash.

**2\.** In the **undo phase**, the system rolls back all transactions in the undo-list. It performs rollback by scanning the log backward from the end.

a. Whenever it finds a log record belonging to a transaction in the undo- list, it performs undo actions just as if the log record had been found during the rollback of a failed transaction.

b. When the system finds a _<Ti_ start_\>_ log record for a transaction _Ti_ in undo-list, it writes a _<Ti_ abort_\>_ log record to the log, and removes _Ti_ from undo-list.

c. The undo phase terminates once undo-list becomes empty, that is, the system has found _<Ti_ start_\>_ log records for all transactions that were initially in undo-list.

After the undo phase of recovery terminates, normal transaction processing can resume.

Observe that the redo phase replays every log record since the most recent checkpoint record. In other words, this phase of restart recovery repeats all the update actions that were executed after the checkpoint, and whose log records reached the stable log. The actions include actions of incomplete transactions and the actions carried out to rollback failed transactions. The actions are repeated in the same order in which they were originally carried out; hence, this process is called **repeating history**. Although it may appear wasteful, repeating history even for failed transactions simplifies recovery schemes.

Figure 16.5 shows an example of actions logged during normal operation, and actions performed during failure recovery. In the log shown in the figure, transaction _T_1 had committed, and transaction _T_0 had been completely rolled back, before the system crashed. Observe how the value of data item _B_ is restored during the rollback of _T_0\. Observe also the checkpoint record, with the list of active transactions containing _T_0 and _T_1.

When recovering from a crash, in the redo phase, the system performs a redo of all operations after the last checkpoint record. In this phase, the list undo-list initially contains _T_0 and _T_1; _T_1 is removed first when its commit log record is found, while _T_2 is added when its start log record is found. Transaction _T_0 is  

![](2.5.png)
**Figure 16.5** Example of logged actions, and actions during recovery.

removed from undo-list when its abort log record is found, leaving only _T_2 in undo-list. The undo phase scans the log backwards from the end, and when it finds a log record of _T_2 updating _A_, the old value of _A_ is restored, and a redo-only log record written to the log. When the start record for _T_2 is found, an abort record is added for _T_2\. Since undo-list contains no more transactions, the undo phase terminates, completing recovery.