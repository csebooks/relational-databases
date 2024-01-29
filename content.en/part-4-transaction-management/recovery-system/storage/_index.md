---
title: 'Storage'
weight: 2
---

# Storage

As we saw in Chapter 10, the various data items in the database may be stored and accessed in a number of different storage media. In Section 14.3, we saw that storage media can be distinguished by their relative speed, capacity, and resilience to failure. We identified three categories of storage:

• **Volatile storage**

• **Nonvolatile storage**

• **Stable storage**

Stable storage or, more accurately, an approximation thereof, plays a critical role in recovery algorithms.

##  Stable-Storage Implementation

To implement stable storage, we need to replicate the needed information in several nonvolatile storage media (usually disk) with independent failure modes, and to update the information in a controlled manner to ensure that failure during data transfer does not damage the needed information.  


Recall (from Chapter 10) that RAID systems guarantee that the failure of a single disk (even during data transfer) will not result in loss of data. The simplest and fastest form of RAID is the mirrored disk, which keeps two copies of each block, on separate disks. Other forms of RAID offer lower costs, but at the expense of lower performance.

RAID systems, however, cannot guard against data loss due to disasters such as fires or flooding. Many systems store archival backups of tapes off site to guard against such disasters. However, since tapes cannot be carried off site continually, updates since the most recent time that tapes were carried off site could be lost in such a disaster. More secure systems keep a copy of each block of stable storage at a remote site, writing it out over a computer network, in addition to storing the block on a local disk system. Since the blocks are output to a remote system as and when they are output to local storage, once an output operation is complete, the output is not lost, even in the event of a disaster such as a fire or flood. We study such _remote backup_ systems in Section 16.9.

In the remainder of this section, we discuss how storage media can be pro- tected from failure during data transfer. Block transfer between memory and disk storage can result in:

• **Successful completion**. The transferred information arrived safely at its des- tination.

• **Partial failure**. A failure occurred in the midst of transfer, and the destination block has incorrect information.

• **Total failure**. The failure occurred sufficiently early during the transfer that the destination block remains intact.

We require that, if a **data-transfer failure** occurs, the system detects it and invokes a recovery procedure to restore the block to a consistent state. To do so, the system must maintain two physical blocks for each logical database block; in the case of mirrored disks, both blocks are at the same location; in the case of remote backup, one of the blocks is local, whereas the other is at a remote site. An output operation is executed as follows:

**1\.** Write the information onto the first physical block.

**2\.** When the first write completes successfully, write the same information onto the second physical block.

**3\.** The output is completed only after the second write completes successfully.

If the system fails while blocks are being written, it is possible that the two copies of a block are inconsistent with each other. During recovery, for each block, the system would need to examine two copies of the blocks. If both are the same and no detectable error exists, then no further actions are necessary. (Recall that errors in a disk block, such as a partial write to the block, are detected by storing a checksum with each block.) If the system detects an error in one block, then it  


replaces its content with the content of the other block. If both blocks contain no detectable error, but they differ in content, then the system replaces the content of the first block with the value of the second. This recovery procedure ensures that a write to stable storage either succeeds completely (that is, updates all copies) or results in no change.

The requirement of comparing every corresponding pair of blocks during recovery is expensive to meet. We can reduce the cost greatly by keeping track of block writes that are in progress, using a small amount of nonvolatile RAM. On recovery, only blocks for which writes were in progress need to be compared.

The protocols for writing out a block to a remote site are similar to the protocols for writing blocks to a mirrored disk system, which we examined in Chapter 10, and particularly in Practice Exercise 10.3.

We can extend this procedure easily to allow the use of an arbitrarily large number of copies of each block of stable storage. Although a large number of copies reduces the probability of a failure to even lower than two copies do, it is usually reasonable to simulate stable storage with only two copies.

## Data Access

As we saw in Chapter 10, the database system resides permanently on nonvolatile storage (usually disks) and only parts of the database are in memory at any time.1 The database is partitioned into fixed-length storage units called **blocks**. Blocks are the units of data transfer to and from disk, and may contain several data items. We shall assume that no data item spans two or more blocks. This assumption is realistic for most data-processing applications, such as a bank or a university.

Transactions input information from the disk to main memory, and then output the information back onto the disk. The input and output operations are done in block units. The blocks residing on the disk are referred to as **physical blocks**; the blocks residing temporarily in main memory are referred to as **buffer blocks**. The area of memory where blocks reside temporarily is called the **disk buffer**.

Block movements between disk and main memory are initiated through the following two operations:

**1\.** input(_B_) transfers the physical block _B_ to main memory.

**2\.** output(_B_) transfers the buffer block _B_ to the disk, and replaces the appro- priate physical block there.

Figure 16.1 illustrates this scheme. Conceptually, each transaction _Ti_ has a private work area in which copies of

data items accessed and updated by _Ti_ are kept. The system creates this work area when the transaction is initiated; the system removes it when the transaction

1There is a special category of database system, called _main-memory database systems_, where the entire database can be loaded into memory at once. We consider such systems in Section 26.4.  

![](2.1.png)

**Figure 16.1** Block storage operations.

either commits or aborts. Each data item _X_ kept in the work area of transaction _Ti_ is denoted by _xi_ . Transaction _Ti_ interacts with the database system by transferring data to and from its work area to the system buffer. We transfer data by these two operations:

**1\.** read(_X_) assigns the value of data item _X_ to the local variable _xi_ . It executes this operation as follows:

a. If block _BX_ on which _X_ resides is not in main memory, it issues input(_BX_).

b. It assigns to _xi_ the value of _X_ from the buffer block.

**2\.** write(_X_) assigns the value of local variable _xi_ to data item _X_ in the buffer block. It executes this operation as follows:

a. If block _BX_ on which _X_ resides is not in main memory, it issues input(_BX_).

b. It assigns the value of _xi_ to _X_ in buffer _BX_.

Note that both operations may require the transfer of a block from disk to main memory. They do not, however, specifically require the transfer of a block from main memory to disk.

A buffer block is eventually written out to the disk either because the buffer manager needs the memory space for other purposes or because the database system wishes to reflect the change to _B_ on the disk. We shall say that the database system performs a **force-output** of buffer _B_ if it issues an output(_B_).

When a transaction needs to access a data item _X_ for the first time, it must execute read(_X_). The system then performs all updates to _X_ on _xi_ . At any point during its execution a transaction may execute write(_X_) to reflect the change to _X_ in the database itself; write(_X_) must certainly be done after the final write to _X_.  


The output(_BX_) operation for the buffer block _BX_ on which _X_ resides does not need to take effect immediately after write(_X_) is executed, since the block _BX_ may contain other data items that are still being accessed. Thus, the actual output may take place later. Notice that, if the system crashes after the write(_X_) operation was executed but before output(_BX_) was executed, the new value of _X_ is never written to disk and, thus, is lost. As we shall see shortly, the database system executes extra actions to ensure that updates performed by committed transactions are not lost even if there is a system crash.