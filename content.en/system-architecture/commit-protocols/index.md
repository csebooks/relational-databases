---
title: Commit Protocols
weight: 21
---

Commit Protocols## Commit Protocols

If we are to ensure atomicity, all the sites in which a transaction _T_ executed must agree on the final outcome of the execution. _T_ must either commit at all sites, or it must abort at all sites. To ensure this property, the transaction coordinator of _T_ must execute a _commit protocol_.

Among the simplest and most widely used commit protocols is the **two-phase commit protocol** (**2PC**), which is described in Section 19.4.1. An alternative is the **three-phase commit protocol** (**3PC**), which avoids certain disadvantages of the 2PC protocol but adds to complexity and overhead. Section 19.4.2 briefly outlines the 3PC protocol.

### Two-Phase Commit

We first describe how the two-phase commit protocol (2PC) operates during normal operation, then describe how it handles failures and finally how it carries out recovery and concurrency control.

Consider a transaction _T_ initiated at site _S_~i~ , where the transaction coordinator is _C_~i~ .

#### 19.4.1.1 The Commit Protocol

When _T_ completes its execution that is, when all the sites at which _T_ has executed inform _C_~i~ that _T_ has completed—_C_~i~ starts the 2PC protocol.

- **Phase 1**. _C_~i~ adds the record _<_prepare _T>_ to the log, and forces the log onto stable storage. It then sends a prepare _T_ message to all sites at which _T_ executed. On receiving such a message, the transaction manager at that site determines whether it is willing to commit its portion of _T_. If the answer is no, it adds a record _<_no _T>_ to the log, and then responds by sending an abort _T_ message to _C_~i~ . If the answer is yes, it adds a record _<_ready _T>_ to the log, and forces the log (with all the log records corresponding to _T_) onto stable storage. The transaction manager then replies with a ready _T_ message to _C_~i~ .

- **Phase 2**. When _C_~i~ receives responses to the prepare _T_ message from all the sites, or when a prespecified interval of time has elapsed since the prepare _T_ message was sent out, _C_~i~ can determine whether the transaction _T_ can be committed or aborted. Transaction _T_ can be committed if _C_~i~ received a ready _T_ message from all the participating sites. Otherwise, transaction _T_ must be aborted. Depending on the verdict, either a record _<_commit _T>_ or a record _<_abort _T>_ is added to the log and the log is forced onto stable storage. At this point, the fate of the transaction has been sealed. Following this point, the coordinator sends either a commit _T_ or an abort _T_ message to all participating sites. When a site receives that message, it records the message in the log.

A site at which _T_ executed can unconditionally abort _T_ at any time before it sends the message ready _T_ to the coordinator. Once the message is sent, the transaction is said to be in the **ready state** at the site. The ready _T_ message is, in effect, a promise by a site to follow the coordinator’s order to commit _T_ or to abort _T_. To make such a promise, the needed information must first be stored in stable storage. Otherwise, if the site crashes after sending ready _T_, it may be unable to make good on its promise. Further, locks acquired by the transaction must continue to be held until the transaction completes.

Since unanimity is required to commit a transaction, the fate of _T_ is sealed as soon as at least one site responds abort _T_. Since the coordinator site _S_~i~ is one of the sites at which _T_ executed, the coordinator can decide unilaterally to abort _T_. The final verdict regarding _T_ is determined at the time that the coordinator writes that verdict (commit or abort) to the log and forces that verdict to stable storage. In some implementations of the 2PC protocol, a site sends an acknowledge _T_ message to the coordinator at the end of the second phase of the protocol. When the coordinator receives the acknowledge _T_ message from all the sites, it adds the record _<_complete _T>_ to the log.

#### 19.4.1.2 Handling of Failures

The 2PC protocol responds in different ways to various types of failures:

- **Failure of a participating site**. If the coordinator _C_~i~ detects that a site has failed, it takes these actions: If the site fails before responding with a ready _T_ message to _C_~i~ , the coordinator assumes that it responded with an abort _T_ message. If the site fails after the coordinator has received the ready _T_ message from the site, the coordinator executes the rest of the commit protocol in the normal fashion, ignoring the failure of the site.

When a participating site _S_~k~ recovers from a failure, it must examine its log to determine the fate of those transactions that were in the midst of execution when the failure occurred. Let _T_ be one such transaction. We consider each of the possible cases:

>- The log contains a _<_commit _T>_ record. In this case, the site executes redo(_T_).

>- The log contains an _<_abort _T>_ record. In this case, the site executes undo(_T_).

>- The log contains a _<_ready _T>_ record. In this case, the site must consult _C_~i~ to determine the fate of _T_. If _C_~i~ is up, it notifies _S_~k~ regarding whether _T_ committed or aborted. In the former case, it executes redo(_T_); in the latter case, it executes undo(_T_). If _C_~i~ is down, _S_~k~ must try to find the fate of _T_ from other sites. It does so by sending a querystatus _T_ message to all the sites in the system. On receiving such a message, a site must consult its log to determine whether _T_ has executed there, and if _T_ has, whether _T_ committed or aborted. It then notifies _S_~k~ about this outcome. If no site has the appropriate information (that is, whether _T_ committed or aborted), then _S_~k~ can neither abort nor commit _T_. The decision concerning _T_ is postponed until _S_~k~ can obtain the needed information. Thus, _S_~k~ must periodically resend the querystatus message to the other sites. It continues to do so until a site that contains the needed information recovers. Note that the site at which _C_~i~ resides always has the needed information.

>- The log contains no control records (abort, commit, ready) concerning _T_. Thus, we know that _S_~k~ failed before responding to the prepare _T_ message from _C_~i~ . Since the failure of _S_~k~ precludes the sending of such a response, by our algorithm _C_~i~ must abort _T_. Hence, _S_~K~ must execute undo(_T_).

- **Failure of the coordinator**. If the coordinator fails in the midst of the execution of the commit protocol for transaction _T_, then the participating sites must decide the fate of _T_. We shall see that, in certain cases, the participating sites cannot decide whether to commit or abort _T_, and therefore these sites must wait for the recovery of the failed coordinator.

>- If an active site contains a _<_commit _T>_ record in its log, then _T_ must be committed.

>- If an active site contains an _<_abort _T>_ record in its log, then _T_ must be aborted.

>- If some active site does _not_ contain a _<_ready _T>_ record in its log, then the failed coordinator _C_~i~ cannot have decided to commit _T_, because a site that does not have a _<_ready _T>_ record in its log cannot have sent a ready _T_ message to _C_~i~ . However, the coordinator may have decided to abort _T_, but not to commit _T_. Rather than wait for _C_~i~ to recover, it is preferable to abort _T_.

>- If none of the preceding cases holds, then all active sites must have a _<_ready _T>_ record in their logs, but no additional control records (such as _<_abort _T>_ or _<_commit _T>_). Since the coordinator has failed, it is impossible to determine whether a decision has been made, and if one has, what that decision is, until the coordinator recovers. Thus, the active sites must wait for _C_~i~ to recover. Since the fate of _T_ remains in doubt, _T_ may continue to hold system resources. For example, if locking is used, _T_ may hold locks on data at active sites. Such a situation is undesirable, because it may be hours or days before _C_~i~ is again active. During this time, other transactions may be forced to wait for _T_. As a result, data items may be unavailable not only on the failed site (_C_~i~ ), but on active sites as well. This situation is called the **blocking** problem, because _T_ is blocked pending the recovery of site _C_~i~ .

- **Network partition**. When a network partitions, two possibilities exist:

**1\.** The coordinator and all its participants remain in one partition. In this case, the failure has no effect on the commit protocol.

**2\.** The coordinator and its participants belong to several partitions. From the viewpoint of the sites in one of the partitions, it appears that the sites in other partitions have failed. Sites that are not in the partition containing the coordinator simply execute the protocol to deal with failure of the coordinator. The coordinator and the sites that are in the same partition as the coordinator follow the usual commit protocol, assuming that the sites in the other partitions have failed.

Thus, the major disadvantage of the 2PC protocol is that coordinator failure may result in blocking, where a decision either to commit or to abort _T_ may have to be postponed until _C_~i~ recovers.

#### 19.4.1.3 Recovery and Concurrency Control

When a failed site restarts, we can perform recovery by using, for example, the recovery algorithm described in Section 16.4. To deal with distributed commit protocols, the recovery procedure must treat **in-doubt transactions** specially; in- doubt transactions are transactions for which a _<_ready _T>_ log record is found, but neither a _<_commit _T>_ log record nor an _<_abort _T>_ log record is found. The recovering site must determine the commit–abort status of such transactions by contacting other sites, as described in Section 19.4.1.2.

If recovery is done as just described, however, normal transaction processing at the site cannot begin until all in-doubt transactions have been committed or rolled back. Finding the status of in-doubt transactions can be slow, since multiple sites may have to be contacted. Further, if the coordinator has failed, and no other site has information about the commit–abort status of an incomplete transaction, recovery potentially could become blocked if 2PC is used. As a result, the site performing restart recovery may remain unusable for a long period.

To circumvent this problem, recovery algorithms typically provide support for noting lock information in the log. (We are assuming here that locking is used for concurrency control.) Instead of writing a _<_ready _T>_ log record, the algorithm writes a _<_ready _T_, _L>_ log record, where _L_ is a list of all write locks held by the transaction _T_ when the log record is written. At recovery time, after performing local recovery actions, for every in-doubt transaction _T_ , all the write locks noted in the _<_ready _T_, _L>_ log record (read from the log) are reacquired.

After lock reacquisition is complete for all in-doubt transactions, transaction processing can start at the site, even before the commit–abort status of the in- doubt transactions is determined. The commit or rollback of in-doubt transactions proceeds concurrently with the execution of new transactions. Thus, site recovery is faster, and never gets blocked. Note that new transactions that have a lock conflict with any write locks held by in-doubt transactions will be unable to make progress until the conflicting in-doubt transactions have been committed or rolled back.

### Three-Phase Commit

The three-phase commit (3PC) protocol is an extension of the two-phase commit protocol that avoids the blocking problem under certain assumptions. In particular, it is assumed that no network partition occurs, and not more than _k_ sites fail, where _k_ is some predetermined number. Under these assumptions, the protocol avoids blocking by introducing an extra third phase where multiple sites are involved in the decision to commit. Instead of directly noting the commit decision in its persistent storage, the coordinator first ensures that at least _k_ other sites know that it intended to commit the transaction. If the coordinator fails, the remaining sites first select a new coordinator. This new coordinator checks the status of the protocol from the remaining sites; if the coordinator had decided to commit, at least one of the other _k_ sites that it informed will be up and will ensure that the commit decision is respected. The new coordinator restarts the third phase of the protocol if some site knew that the old coordinator intended to commit the transaction. Otherwise the new coordinator aborts the transaction.

While the 3PC protocol has the desirable property of not blocking unless _k_ sites fail, it has the drawback that a partitioning of the network may appear to be the same as more than _k_ sites failing, which would lead to blocking. The protocol also has to be implemented carefully to ensure that network partitioning (or more than _k_ sites failing) does not result in inconsistencies, where a transaction is committed in one partition and aborted in another. Because of its overhead, the 3PC protocol is not widely used. See the bibliographical notes for references giving more details of the 3PC protocol.

### Alternative Models of Transaction Processing

For many applications, the blocking problem of two-phase commit is not acceptable. The problem here is the notion of a single transaction that works across multiple sites. In this section, we describe how to use _persistent messaging_ to avoid the problem of distributed commit, and then briefly outline the larger issue of _workflows;_ workflows are considered in more detail in Section 26.2.

To understand persistent messaging, consider how one might transfer funds between two different banks, each with its own computer. One approach is to have a transaction span the two sites and use two-phase commit to ensure atomicity. However, the transaction may have to update the total bank balance, and blocking could have a serious impact on all other transactions at each bank, since almost all transactions at the bank would update the total bank balance.

In contrast, consider how funds transfer by a bank check occurs. The bank first deducts the amount of the check from the available balance and prints out a check. The check is then physically transferred to the other bank where it is deposited. After verifying the check, the bank increases the local balance by the amount of the check. The check constitutes a message sent between the two banks. So that funds are not lost or incorrectly increased, the check must not be lost, and must not be duplicated and deposited more than once. When the bank computers are connected by a network, persistent messages provide the same service as the check (but much faster, of course).

**Persistent messages** are messages that are guaranteed to be delivered to the recipient exactly once (neither less nor more), regardless of failures, if the transaction sending the message commits, and are guaranteed to not be delivered if the transaction aborts. Database recovery techniques are used to implement persistent messaging on top of the normal network channels, as we shall see shortly. In contrast, regular messages may be lost or may even be delivered multiple times in some situations.

Error handling is more complicated with persistent messaging than with two- phase commit. For instance, if the account where the check is to be deposited has been closed, the check must be sent back to the originating account and credited back there. Both sites must therefore be provided with error-handling code, along with code to handle the persistent messages. In contrast, with two-phase commit, the error would be detected by the transaction, which would then never deduct the amount in the first place.

The types of exception conditions that may arise depend on the application, so it is not possible for the database system to handle exceptions automatically. The application programs that send and receive persistent messages must include code to handle exception conditions and bring the system back to a consistent state. For instance, it is not acceptable to just lose the money being transferred if the receiving account has been closed; the money must be credited back to the originating account, and if that is not possible for some reason, humans must be alerted to resolve the situation manually.

There are many applications where the benefit of eliminating blocking is well worth the extra effort to implement systems that use persistent messages. In fact, few organizations would agree to support two-phase commit for transactions originating outside the organization, since failures could result in blocking of access to local data. Persistent messaging therefore plays an important role in carrying out transactions that cross organizational boundaries.

_Workflows_ provide a general model of transaction processing involving multiple sites and possibly human processing of certain steps. For instance, when a bank receives a loan application, there are many steps it must take, including contacting external credit-checking agencies, before approving or rejecting a loan application. The steps, together, form a workflow. We study workflows in more detail in Section 26.2. We also note that persistent messaging forms the underlying basis for workflows in a distributed environment.

We now consider the **implementation** of persistent messaging. Persistent messaging can be implemented on top of an unreliable messaging infrastructure, which may lose messages or deliver them multiple times, by these protocols:

- **Sending site protocol**. When a transaction wishes to send a persistent mes- sage, it writes a record containing the message in a special relation _messages to send_, instead of directly sending out the message. The message is also given a unique message identifier. A _message delivery process_ monitors the relation, and when a new message is found, it sends the message to its destination. The usual database concurrency-control mechanisms ensure that the system process reads the message only after the transaction that wrote the message commits; if the transaction aborts, the usual recovery mechanism would delete the message from the relation.

The message delivery process deletes a message from the relation only after it receives an acknowledgment from the destination site. If it receives no acknowledgement from the destination site, after some time it sends the message again. It repeats this until an acknowledgment is received. In case of permanent failures, the system will decide, after some period of time, that the message is undeliverable. Exception handling code provided by the application is then invoked to deal with the failure.

Writing the message to a relation and processing it only after the transaction commits ensures that the message will be delivered if and only if the transaction commits. Repeatedly sending it guarantees it will be delivered even if there are (temporary) system or network failures.

- **Receiving site protocol**. When a site receives a persistent message, it runs a transaction that adds the message to a special _received messages_ relation, provided it is not already present in the relation (the unique message iden- tifier allows duplicates to be detected). After the transaction commits, or if the message was already present in the relation, the receiving site sends an acknowledgment back to the sending site.

Note that sending the acknowledgment before the transaction commits is not safe, since a system failure may then result in loss of the message. Checking whether the message has been received earlier is essential to avoid multiple deliveries of the message.

In many messaging systems, it is possible for messages to get delayed arbitrarily, although such delays are very unlikely. Therefore, to be safe, the message must never be deleted from the _received messages_ relation. Deleting it could result in a duplicate delivery not being detected. But as a result, the _received messages_ relation may grow indefinitely. To deal with this prob- lem, each message is given a timestamp, and if the timestamp of a received message is older than some cutoff, the message is discarded. All messages recorded in the _received messages_ relation that are older than the cutoff can be deleted.  

