---
title: 'Buffer Management'
weight: 5
references:
    videos:
        - youtube:Dp3vJrjKMpo
        - youtube:-UWY_6X60jg
    links:
        - https://www.javatpoint.com/database-buffer
        - https://www.tutorialspoint.com/database-buffer-in-dbms
    books:
        - b1:
            title: Systems Programming in Unix/Linux
            url: https://www.google.co.in/books/edition/Systems_Programming_in_Unix_Linux/nexqDwAAQBAJ?hl=en&gbpv=0
        - b2:
            title: High-Performance Computing and Networking 
            url: https://www.google.co.in/books/edition/High_Performance_Computing_and_Networkin/CfnjWCLCUWEC?hl=en&gbpv=0
---

# Buffer Management

In this section, we consider several subtle details that are essential to the imple- mentation of a crash-recovery scheme that ensures data consistency and imposes a minimal amount of overhead on interactions with the database.

## Log-Record Buffering

So far, we have assumed that every log record is output to stable storage at the time it is created. This assumption imposes a high overhead on system execution for several reasons: Typically, output to stable storage is in units of blocks. In most cases, a log record is much smaller than a block. Thus, the output of each log record translates to a much larger output at the physical level. Furthermore, as we saw in Section 16.2.1, the output of a block to stable storage may involve several output operations at the physical level.

The cost of outputting a block to stable storage is sufficiently high that it is desirable to output multiple log records at once. To do so, we write log records to a log buffer in main memory, where they stay temporarily until they are output to stable storage. Multiple log records can be gathered in the log buffer and output  


to stable storage in a single output operation. The order of log records in the stable storage must be exactly the same as the order in which they were written to the log buffer.

As a result of log buffering, a log record may reside in only main memory (volatile storage) for a considerable time before it is output to stable storage. Since such log records are lost if the system crashes, we must impose additional requirements on the recovery techniques to ensure transaction atomicity:

• Transaction _Ti_ enters the commit state after the _<Ti_ commit_\>_ log record has been output to stable storage.

• Before the _<Ti_ commit_\>_ log record can be output to stable storage, all log records pertaining to transaction _Ti_ must have been output to stable storage.

• Before a block of data in main memory can be output to the database (in nonvolatile storage), all log records pertaining to data in that block must have been output to stable storage.

This rule is called the **write-ahead logging (WAL)** rule. (Strictly speaking, the WAL rule requires only that the undo information in the log has been output to stable storage, and it permits the redo information to be written later. The difference is relevant in systems where undo information and redo information are stored in separate log records.)

The three rules state situations in which certain log records _must_ have been output to stable storage. There is no problem resulting from the output of log records _earlier_ than necessary. Thus, when the system finds it necessary to output a log record to stable storage, it outputs an entire block of log records, if there are enough log records in main memory to fill a block. If there are insufficient log records to fill the block, all log records in main memory are combined into a partially full block and are output to stable storage.

Writing the buffered log to disk is sometimes referred to as a **log force**.

##  Database Buffering

In Section 16.2.2, we described the use of a two-level storage hierarchy. The system stores the database in nonvolatile storage (disk), and brings blocks of data into main memory as needed. Since main memory is typically much smaller than the entire database, it may be necessary to overwrite a block _B_1 in main memory when another block _B_2 needs to be brought into memory. If _B_1 has been modified, _B_1 must be output prior to the input of _B_2\. As discussed in Section 10.8.1 in Chapter 10, this storage hierarchy is similar to the standard operating-system concept of _virtual memory_.

One might expect that transactions would force-output all modified blocks to disk when they commit. Such a policy is called the **force** policy. The alternative, the **no-force** policy, allows a transaction to commit even if it has modified some blocks that have not yet been written back to disk. All the recovery algorithms described in this chapter work correctly even with the no-force policy. The no-  

force policy allows faster commit of transactions; moreover it allows multiple updates to accumulate on a block before it is output to stable storage, which can reduce the number of output operations greatly for frequently updated blocks. As a result, the standard approach taken by most systems is the no-force policy.

Similarly, one might expect that blocks modified by a transaction that is still active should not be written to disk. This policy is called the **no-steal** policy. The alternative, the **steal** policy, allows the system to write modified blocks to disk even if the transactions that made those modifications have not all committed. As long as the write-ahead logging rule is followed, all the recovery algorithms we study in the chapter work correctly even with the steal policy. Further, the no-steal policy does not work with transactions that perform a large number of updates, since the buffer may get filled with updated pages that cannot be evicted to disk, and the transaction cannot then proceed. As a result, the standard approach taken by most systems is the steal policy.

To illustrate the need for the write-ahead logging requirement, consider our banking example with transactions _T_0 and _T_1\. Suppose that the state of the log is:

_<T_0 start_\> <T_0_, A,_ 1000_,_ 950_\>_

and that transaction _T_0 issues a read(_B_). Assume that the block on which _B_ resides is not in main memory, and that main memory is full. Suppose that the block on which _A_ resides is chosen to be output to disk. If the system outputs this block to disk and then a crash occurs, the values in the database for accounts _A_, _B_, and _C_ are $950, $2000, and $700, respectively. This database state is inconsistent. However, because of the WAL requirements, the log record:

_<T_0_, A,_ 1000_,_ 950_\>_

must be output to stable storage prior to output of the block on which _A_ resides. The system can use the log record during recovery to bring the database back to a consistent state.

When a block _B_1 is to be output to disk, all log records pertaining to data in _B_1 must be output to stable storage before _B_1 is output. It is important that no writes to the block _B_1 be in progress while the block is being output, since such a write could violate the write-ahead logging rule. We can ensure that there are no writes in progress by using a special means of locking:

• Before a transaction performs a write on a data item, it acquires an exclu- sive lock on the block in which the data item resides. The lock is released immediately after the update has been performed.

• The following sequence of actions is taken when a block is to be output:

◦ Obtain an exclusive lock on the block, to ensure that no transaction is performing a write on the block.  


◦ Output log records to stable storage until all log records pertaining to block _B_1 have been output.

◦ Output block _B_1 to disk.

◦ Release the lock once the block output has completed.

Locks on buffer blocks are unrelated to locks used for concurrency-control of transactions, and releasing them in a non-two-phase manner does not have any implications on transaction serializability. These locks, and other similar locks that are held for a short duration, are often referred to as **latches**.

Locks on buffer blocks can also be used to ensure that buffer blocks are not updated, and log records are not generated, while a checkpoint is in progress. This restriction may be enforced by acquiring exclusive locks on all buffer blocks, as well as an exclusive lock on the log, before the checkpoint operation is performed. These locks can be released as soon as the checkpoint operation has completed.

Database systems usually have a process that continually cycles through the buffer blocks, outputting modified buffer blocks back to disk. The above locking protocol must of course be followed when the blocks are output. As a result of continuous output of modified blocks, the number of **dirty blocks** in the buffer, that is, blocks that have been modified in the buffer but have not been subsequently output, is minimized. Thus, the number of blocks that have to be output during a checkpoint is minimized; further, when a block needs to be evicted from the buffer it is likely that there will be a non-dirty block available for eviction, allowing the input to proceed immediately instead of waiting for an output to complete.

## Operating System Role in Buffer Management

We can manage the database buffer by using one of two approaches:

**1\.** The database system reserves part of main memory to serve as a buffer that it, rather than the operating system, manages. The database system manages data-block transfer in accordance with the requirements in Section 16.5.2.

This approach has the drawback of limiting flexibility in the use of main memory. The buffer must be kept small enough that other applications have sufficient main memory available for their needs. However, even when the other applications are not running, the database will not be able to make use of all the available memory. Likewise, non-database applications may not use that part of main memory reserved for the database buffer, even if some of the pages in the database buffer are not being used.

**2\.** The database system implements its buffer within the virtual memory pro- vided by the operating system. Since the operating system knows about the memory requirements of all processes in the system, ideally it should be in charge of deciding what buffer blocks must be force-output to disk, and when. But, to ensure the write-ahead logging requirements in Section 16.5.1, the operating system should not write out the database buffer pages itself,  


but instead should request the database system to force-output the buffer blocks. The database system in turn would force-output the buffer blocks to the database, after writing relevant log records to stable storage.

Unfortunately, almost all current-generation operating systems retain complete control of virtual memory. The operating system reserves space on disk for storing virtual-memory pages that are not currently in main memory; this space is called **swap space**. If the operating system decides to output a block _Bx_, that block is output to the swap space on disk, and there is no way for the database system to get control of the output of buffer blocks.

Therefore, if the database buffer is in virtual memory, transfers between database files and the buffer in virtual memory must be managed by the database system, which enforces the write-ahead logging requirements that we discussed.

This approach may result in extra output of data to disk. If a block _Bx_ is output by the operating system, that block is not output to the database. Instead, it is output to the swap space for the operating system’s virtual memory. When the database system needs to output _Bx_, the operating sys- tem may need first to input _Bx_ from its swap space. Thus, instead of a single output of _Bx_, there may be two outputs of _Bx_ (one by the operating system, and one by the database system) and one extra input of _Bx_.

Although both approaches suffer from some drawbacks, one or the other must be chosen unless the operating system is designed to support the requirements of database logging.

## Fuzzy Checkpointing

The checkpointing technique described in Section 16.3.6 requires that all updates to the database be temporarily suspended while the checkpoint is in progress. If the number of pages in the buffer is large, a checkpoint may take a long time to finish, which can result in an unacceptable interruption in processing of transactions.

To avoid such interruptions, the checkpointing technique can be modified to permit updates to start once the checkpoint record has been written, but before the modified buffer blocks are written to disk. The checkpoint thus generated is a **fuzzy checkpoint**.

Since pages are output to disk only after the checkpoint record has been written, it is possible that the system could crash before all pages are written. Thus, a checkpoint on disk may be incomplete. One way to deal with incomplete checkpoints is this: The location in the log of the checkpoint record of the last completed checkpoint is stored in a fixed position, last-checkpoint, on disk. The system does not update this information when it writes the checkpoint record. Instead, before it writes the checkpoint record, it creates a list of all modified buffer blocks. The last-checkpoint information is updated only after all buffer blocks in the list of modified buffer blocks have been output to disk.  


Even with fuzzy checkpointing, a buffer block must not be updated while it is being output to disk, although other buffer blocks may be updated concur- rently. The write-ahead log protocol must be followed so that (undo) log records pertaining to a block are on stable storage before the block is output.