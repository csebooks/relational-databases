---
title: 'PART THREE: DATA STORAGE AND QUERYING'
weight: 8
---

  

**PART 3**

**DATA STORAGE AND QUERYING**

Although a database system provides a high-level view of data, ultimately data have to be stored as bits on one or more storage devices. A vast majority of databases today store data on magnetic disk (and, increasingly, on flash storage) and fetch data into main memory for processing, or copy data onto tapes and other backup devices for archival storage. The physical characteristics of storage devices play a major role in the way data are stored, in particular because access to a random piece of data on disk is much slower than memory access: Disk access takes tens of milliseconds, whereas memory access takes a tenth of a microsecond.

Chapter 10 begins with an overview of physical storage media, including mechanisms to minimize the chance of data loss due to device failures. The chapter then describes how records are mapped to files, which in turn are mapped to bits on the disk.

Many queries reference only a small proportion of the records in a file. An index is a structure that helps locate desired records of a relation quickly, without examining all records. The index in this textbook is an example, although, unlike database indices, it is meant for human use. Chapter 11 describes several types of indices used in database systems.

User queries have to be executed on the database contents, which reside on storage devices. It is usually convenient to break up queries into smaller oper- ations, roughly corresponding to the relational-algebra operations. Chapter 12 describes how queries are processed, presenting algorithms for implementing individual operations, and then outlining how the operations are executed in synchrony, to process a query.

There are many alternative ways of processing a query, which can have widely varying costs. Query optimization refers to the process of finding the lowest-cost method of evaluating a given query. Chapter 13 describes the process of query optimization.

**427**  

_This page intentionally left blank_  

**_C H A P T E R_10 Storage and File Structure**

In preceding chapters, we have emphasized the higher-level models of a database. For example, at the _conceptual_ or _logical_ level, we viewed the database, in the re- lational model, as a collection of tables. Indeed, the logical model of the database is the correct level for database _users_ to focus on. This is because the goal of a database system is to simplify and facilitate access to data; users of the system should not be burdened unnecessarily with the physical details of the implemen- tation of the system.

In this chapter, however, as well as in Chapters 11, 12, and 13, we probe be- low the higher levels as we describe various methods for implementing the data models and languages presented in preceding chapters. We start with character- istics of the underlying storage media, such as disk and tape systems. We then define various data structures that allow fast access to data. We consider several alternative structures, each best suited to a different kind of access to data. The final choice of data structure needs to be made on the basis of the expected use of the system and of the physical characteristics of the specific machine.

**10.1 Overview of Physical Storage Media**

Several types of data storage exist in most computer systems. These storage media are classified by the speed with which data can be accessed, by the cost per unit of data to buy the medium, and by the medium’s reliability. Among the media typically available are these:

• **Cache**. The cache is the fastest and most costly form of storage. Cache memory is relatively small; its use is managed by the computer system hardware. We shall not be concerned about managing cache storage in the database system. It is, however, worth noting that database implementors do pay attention to cache effects when designing query processing data structures and algorithms.

• **Main memory**. The storage medium used for data that are available to be op- erated on is main memory. The general-purpose machine instructions operate

**429**  

**430 Chapter 10 Storage and File Structure**

on main memory. Although main memory may contain several gigabytes of data on a personal computer, or even hundreds of gigabytes of data in large server systems, it is generally too small (or too expensive) for storing the entire database. The contents of main memory are usually lost if a power failure or system crash occurs.

• **Flash memory**. Flash memory differs from main memory in that stored data are retained even if power is turned off (or fails). There are two types of flash memory, called _NAND_ and _NOR_ flash. Of these, NAND flash has a much higher storage capacity for a given cost, and is widely used for data storage in devices such as cameras, music players, and cell phones, and increasingly, in laptop computers as well. Flash memory has a lower cost per byte than main memory, in addition to being nonvolatile; that is, it retains stored data even if power is switched off.

Flash memory is also widely used for storing data in “USB keys,” which can be plugged into the **Universal Serial Bus** (USB) slots of computing devices. Such USB keys have become a popular means of transporting data between computer systems (“floppy disks” played the same role in earlier days, but their limited capacity has made them obsolete now).

Flash memory is also increasingly used as a replacement for magnetic disks for storing moderate amounts of data. Such disk-drive replacements are called _solid-state drives_. As of 2009, a 64 GB solid-state hard drive costs less than $200, and capacities range up to 160 GB. Further, flash memory is increasingly being used in server systems to improve performance by caching frequently used data, since it provides faster access than disk, with larger storage capacity than main memory (for a given cost).

• **Magnetic-disk storage**. The primary medium for the long-term online stor- age of data is the magnetic disk. Usually, the entire database is stored on magnetic disk. The system must move the data from disk to main memory so that they can be accessed. After the system has performed the designated operations, the data that have been modified must be written to disk.

As of 2009, the size of magnetic disks ranges from 80 gigabytes to 1.5 terabytes, and a 1 terabyte disk costs about $100. Disk capacities have been growing at about 50 percent per year, and we can expect disks of much larger capacity every year. Disk storage survives power failures and system crashes. Disk-storage devices themselves may sometimes fail and thus destroy data, but such failures usually occur much less frequently than do system crashes.

• **Optical storage**. The most popular forms of optical storage are the _compact disk_ (CD), which can hold about 700 megabytes of data and has a playtime of about 80 minutes, and the _digital video disk_ (DVD), which can hold 4.7 or 8.5 gigabytes of data per side of the disk (or up to 17 gigabytes on a two-sided disk). The expression **digital versatile disk** is also used in place of **digital video disk**, since DVDs can hold any digital data, not just video data. Data are stored optically on a disk, and are read by a laser. A higher capacity format called _Blu-ray DVD_ can store 27 gigabytes per layer, or 54 gigabytes in a double-layer disk.  

**10.1 Overview of Physical Storage Media 431**

The optical disks used in read-only compact disks (CD-ROM) or read-only digital video disks (DVD-ROM) cannot be written, but are supplied with data prerecorded. There are also “record-once” versions of compact disk (called CD-R) and digital video disk (called DVD-R and DVD+R), which can be written only once; such disks are also called **write-once, read-many** (WORM) disks. There are also “multiple-write” versions of compact disk (called CD-RW) and digital video disk (DVD-RW, DVD+RW, and DVD-RAM), which can be written multiple times.

Optical disk **jukebox** systems contain a few drives and numerous disks that can be loaded into one of the drives automatically (by a robot arm) on demand.

• **Tape storage**. Tape storage is used primarily for backup and archival data. Although magnetic tape is cheaper than disks, access to data is much slower, because the tape must be accessed sequentially from the beginning. For this reason, tape storage is referred to as **sequential-access** storage. In contrast, disk storage is referred to as **direct-access** storage because it is possible to read data from any location on disk.

Tapes have a high capacity (40- to 300-gigabyte tapes are currently avail- able), and can be removed from the tape drive, so they are well suited to cheap archival storage. Tape libraries (jukeboxes) are used to hold exception- ally large collections of data such as data from satellites, which could include as much as hundreds of terabytes (1 terabyte = 1012 bytes), or even multiple petabytes (1 petabyte = 1015 bytes) of data in a few cases.

The various storage media can be organized in a hierarchy (Figure 10.1) according to their speed and their cost. The higher levels are expensive, but are fast. As we move down the hierarchy, the cost per bit decreases, whereas the access time increases. This trade-off is reasonable; if a given storage system were both faster and less expensive than another—other properties being the same —then there would be no reason to use the slower, more expensive memory. In fact, many early storage devices, including paper tape and core memories, are relegated to museums now that magnetic tape and semiconductor memory have become faster and cheaper. Magnetic tapes themselves were used to store active data back when disks were expensive and had low storage capacity. Today, almost all active data are stored on disks, except in very rare cases where they are stored on tape or in optical jukeboxes.

The fastest storage media—for example, cache and main memory—are re- ferred to as **primary storage**. The media in the next level in the hierarchy—for example, magnetic disks—are referred to as **secondary storage**, or **online stor- age**. The media in the lowest level in the hierarchy—for example, magnetic tape and optical-disk jukeboxes—are referred to as **tertiary storage**, or **offline storage**.

In addition to the speed and cost of the various storage systems, there is also the issue of storage volatility. **Volatile storage** loses its contents when the power to the device is removed. In the hierarchy shown in Figure 10.1, the storage systems from main memory up are volatile, whereas the storage systems below  

**432 Chapter 10 Storage and File Structure**

cache

main memory

flash memory

magnetic disk

optical disk

magnetic tapes

**Figure 10.1** Storage device hierarchy.

main memory are nonvolatile. Data must be written to **nonvolatile storage** for safekeeping. We shall return to this subject in Chapter 16.

**10.2 Magnetic Disk and Flash Storage**

Magnetic disks provide the bulk of secondary storage for modern computer systems. Although disk capacities have been growing year after year, the storage requirements of large applications have also been growing very fast, in some cases even faster than the growth rate of disk capacities. A very large database may require hundreds of disks. In recent years, flash-memory storage sizes have grown rapidly, and flash storage is increasingly becoming a competitor to magnetic disk storage for several applications.

**10.2.1 Physical Characteristics of Disks**

Physically, disks are relatively simple (Figure 10.2). Each disk **platter** has a flat, circular shape. Its two surfaces are covered with a magnetic material, and infor- mation is recorded on the surfaces. Platters are made from rigid metal or glass.

When the disk is in use, a drive motor spins it at a constant high speed (usually 60, 90, or 120 revolutions per second, but disks running at 250 revolutions per second are available). There is a read–write head positioned just above the surface of the platter. The disk surface is logically divided into **tracks**, which are subdivided into **sectors**. A **sector** is the smallest unit of information that can be read from or written to the disk. In currently available disks, sector sizes are  

**10.2 Magnetic Disk and Flash Storage 433**

track _t_

sector _s_

spindle

cylinder _c_

platter arm

read–write head

arm assembly

rotation

**Figure 10.2** Moving head disk mechanism.

typically 512 bytes; there are about 50,000 to 100,000 tracks per platter, and 1 to 5 platters per disk. The inner tracks (closer to the spindle) are of smaller length, and in current-generation disks, the outer tracks contain more sectors than the inner tracks; typical numbers are around 500 to 1000 sectors per track in the inner tracks, and around 1000 to 2000 sectors per track in the outer tracks. The numbers vary among different models; higher-capacity models usually have more sectors per track and more tracks on each platter.

The **read–write head** stores information on a sector magnetically as reversals of the direction of magnetization of the magnetic material.

Each side of a platter of a disk has a read–write head that moves across the platter to access different tracks. A disk typically contains many platters, and the read–write heads of all the tracks are mounted on a single assembly called a **disk arm**, and move together. The disk platters mounted on a spindle and the heads mounted on a disk arm are together known as **head–disk assemblies**. Since the heads on all the platters move together, when the head on one platter is on the _i_th track, the heads on all other platters are also on the _i_th track of their respective platters. Hence, the _i_th tracks of all the platters together are called the _i_th **cylinder**.

Today, disks with a platter diameter of 3 1 2 inches dominate the market. They

have a lower cost and faster seek times (due to smaller seek distances) than do the larger-diameter disks (up to 14 inches) that were common earlier, yet they provide high storage capacity. Disks with even smaller diameters are used in portable devices such as laptop computers, and some handheld computers and portable music players.

The read–write heads are kept as close as possible to the disk surface to increase the recording density. The head typically floats or flies only microns  

**434 Chapter 10 Storage and File Structure**

from the disk surface; the spinning of the disk creates a small breeze, and the head assembly is shaped so that the breeze keeps the head floating just above the disk surface. Because the head floats so close to the surface, platters must be machined carefully to be flat.

Head crashes can be a problem. If the head contacts the disk surface, the head can scrape the recording medium off the disk, destroying the data that had been there. In older-generation disks, the head touching the surface caused the removed medium to become airborne and to come between the other heads and their platters, causing more crashes; a head crash could thus result in failure of the entire disk. Current-generation disk drives use a thin film of magnetic metal as recording medium. They are much less susceptible to failure by head crashes than the older oxide-coated disks.

A **disk controller** interfaces between the computer system and the actual hardware of the disk drive; in modern disk systems, the disk controller is im- plemented within the disk drive unit. A disk controller accepts high-level com- mands to read or write a sector, and initiates actions, such as moving the disk arm to the right track and actually reading or writing the data. Disk controllers also attach **checksums** to each sector that is written; the checksum is computed from the data written to the sector. When the sector is read back, the controller computes the checksum again from the retrieved data and compares it with the stored checksum; if the data are corrupted, with a high probability the newly computed checksum will not match the stored checksum. If such an error occurs, the controller will retry the read several times; if the error continues to occur, the controller will signal a read failure.

Another interesting task that disk controllers perform is **remapping of bad sectors**. If the controller detects that a sector is damaged when the disk is initially formatted, or when an attempt is made to write the sector, it can logically map the sector to a different physical location (allocated from a pool of extra sectors set aside for this purpose). The remapping is noted on disk or in nonvolatile memory, and the write is carried out on the new location.

Disks are connected to a computer system through a high-speed interconnec- tion. There are a number of common interfaces for connecting disks to computers of which the most commonly used today are (1) **SATA** (which stands for **serial ATA**,1 and a newer version of SATA called SATA II or SATA3 Gb (older versions of the ATA standard called **PATA**, or Parallel ATA, and IDE, were widely used earlier, and are still available), (2) **small-computer-system interconnect** (SCSI; pronounced “scuzzy”) , (3) SAS (which stands for **serial attached SCSI**), and (4) the Fibre Chan- nel interface. Portable external disk systems often use the USB interface or the IEEE 1394 FireWire interface.

While disks are usually connected directly by cables to the disk interface of the computer system, they can be situated remotely and connected by a high-speed network to the disk controller. In the **storage area network** (**SAN**) architecture, large numbers of disks are connected by a high-speed network to a number

1ATA is a storage-device connection standard from the 1980s.  

**10.2 Magnetic Disk and Flash Storage 435**

of server computers. The disks are usually organized locally using a storage organization technique called **redundant arrays of independent disks** (RAID) (described later, in Section 10.3), to give the servers a logical view of a very large and very reliable disk. The computer and the disk subsystem continue to use the SCSI, SAS, or Fiber Channel interface protocols to talk with each other, although they may be separated by a network. Remote access to disks across a storage area network means that disks can be shared by multiple computers that could run different parts of an application in parallel. Remote access also means that disks containing important data can be kept in a central server room where they can be monitored and maintained by system administrators, instead of being scattered in different parts of an organization.

**Network attached storage (NAS)** is an alternative to SAN. NAS is much like SAN, except that instead of the networked storage appearing to be a large disk, it provides a file system interface using networked file system protocols such as NFS or CIFS.

**10.2.2 Performance Measures of Disks**

The main measures of the qualities of a disk are capacity, access time, data-transfer rate, and reliability.

**Access time** is the time from when a read or write request is issued to when data transfer begins. To access (that is, to read or write) data on a given sector of a disk, the arm first must move so that it is positioned over the correct track, and then must wait for the sector to appear under it as the disk rotates. The time for repositioning the arm is called the **seek time**, and it increases with the distance that the arm must move. Typical seek times range from 2 to 30 milliseconds, depending on how far the track is from the initial arm position. Smaller disks tend to have lower seek times since the head has to travel a smaller distance.

The **average seek time** is the average of the seek times, measured over a sequence of (uniformly distributed) random requests. If all tracks have the same number of sectors, and we disregard the time required for the head to start moving and to stop moving, we can show that the average seek time is one-third the worst-case seek time. Taking these factors into account, the average seek time is around one-half of the maximum seek time. Average seek times currently range between 4 and 10 milliseconds, depending on the disk model.

Once the head has reached the desired track, the time spent waiting for the sector to be accessed to appear under the head is called the **rotational latency time**. Rotational speeds of disks today range from 5400 rotations per minute (90 rotations per second) up to 15,000 rotations per minute (250 rotations per second), or, equivalently, 4 milliseconds to 11.1 milliseconds per rotation. On an average, one-half of a rotation of the disk is required for the beginning of the desired sector to appear under the head. Thus, the **average latency time** of the disk is one-half the time for a full rotation of the disk.

The access time is then the sum of the seek time and the latency, and ranges from 8 to 20 milliseconds. Once the first sector of the data to be accessed has come under the head, data transfer begins. The **data-transfer rate** is the rate at which  

**436 Chapter 10 Storage and File Structure**

data can be retrieved from or stored to the disk. Current disk systems support maximum transfer rates of 25 to 100 megabytes per second; transfer rates are significantly lower than the maximum transfer rates for inner tracks of the disk, since they have fewer sectors. For example, a disk with a maximum transfer rate of 100 megabytes per second may have a sustained transfer rate of around 30 megabytes per second on its inner tracks.

The final commonly used measure of a disk is the **mean time to failure** (**MTTF**), which is a measure of the reliability of the disk. The mean time to failure of a disk (or of any other system) is the amount of time that, on average, we can expect the system to run continuously without any failure. According to vendors’ claims, the mean time to failure of disks today ranges from 500,000 to 1,200,000 hours— about 57 to 136 years. In practice the claimed mean time to failure is computed on the probability of failure when the disk is new—the figure means that given 1000 relatively new disks, if the MTTF is 1,200,000 hours, on an average one of them will fail in 1200 hours. A mean time to failure of 1,200,000 hours does not imply that the disk can be expected to function for 136 years! Most disks have an expected life span of about 5 years, and have significantly higher rates of failure once they become more than a few years old.

Disk drives for desktop machines typically support the Serial ATA(SATA) in- terface, which supports 150 megabytes per second, or the SATA-II 3Gb interface, which supports 300 megabytes per second. The PATA 5 interface supported trans- fer rates of 133 megabytes per second. Disk drives designed for server systems typically support the Ultra320 SCSI interface, which provides transfer rates of up to 320 megabytes per second, or the Serial Attached SCSI (SAS) interface, versions of which provide transfer rates of 3 or 6 gigabits per second. Storage area network (SAN) devices, which are connected to servers by a network, typically use Fiber Channel FC 2-Gb or 4-Gb interface, which provides transfer rates of up to 256 or 512 megabytes per second. The transfer rate of an interface is shared between all disks attached to the interface, except for the serial interfaces which allow only one disk to be connected to each interface.

**10.2.3 Optimization of Disk-Block Access**

Requests for disk I/O are generated both by the file system and by the virtual memory manager found in most operating systems. Each request specifies the address on the disk to be referenced; that address is in the form of a _block number_. A **block** is a logical unit consisting of a fixed number of contiguous sectors. Block sizes range from 512 bytes to several kilobytes. Data are transferred between disk and main memory in units of blocks. The term **page** is often used to refer to blocks, although in a few contexts (such as flash memory) they refer to different things.

A sequence of requests for blocks from disk may be classified as a sequential access pattern or a random access pattern. In a **sequential access** pattern, succes- sive requests are for successive block numbers, which are on the same track, or on adjacent tracks. To read blocks in sequential access, a disk seek may be required for the first block, but successive requests would either not require a seek, or  

**10.2 Magnetic Disk and Flash Storage 437**

require a seek to an adjacent track, which is faster than a seek to a track that is farther away.

In contrast, in a **random access** pattern, successive requests are for blocks that are randomly located on disk. Each such request would require a seek. The number of random block accesses that can be satisfied by a single disk in a second depends on the seek time, and is typically about 100 to 200 accesses per second. Since only a small amount (one block) of data is read per seek, the transfer rate is significantly lower with a random access pattern than with a sequential access pattern.

A number of techniques have been developed for improving the speed of access to blocks.

• **Buffering**. Blocks that are read from disk are stored temporarily in an in- memory buffer, to satisfy future requests. Buffering is done by both the op- erating system and the database system. Database buffering is discussed in more detail in Section 10.8.

• **Read-ahead**. When a disk block is accessed, consecutive blocks from the same track are read into an in-memory buffer even if there is no pending request for the blocks. In the case of sequential access, such read-ahead ensures that many blocks are already in memory when they are requested, and minimizes the time wasted in disk seeks and rotational latency per block read. Operat- ing systems also routinely perform read-ahead for consecutive blocks of an operating system file. Read-ahead is, however, not very useful for random block accesses.

• **Scheduling**. If several blocks from a cylinder need to be transferred from disk to main memory, we may be able to save access time by requesting the blocks in the order in which they will pass under the heads. If the desired blocks are on different cylinders, it is advantageous to request the blocks in an order that minimizes disk-arm movement. **Disk-arm–scheduling** algorithms attempt to order accesses to tracks in a fashion that increases the number of accesses that can be processed. A commonly used algorithm is the **elevator algorithm**, which works in the same way many elevators do. Suppose that, initially, the arm is moving from the innermost track toward the outside of the disk. Under the elevator algorithm’s control, for each track for which there is an access request, the arm stops at that track, services requests for the track, and then continues moving outward until there are no waiting requests for tracks farther out. At this point, the arm changes direction, and moves toward the inside, again stopping at each track for which there is a request, until it reaches a track where there is no request for tracks farther toward the center. Then, it reverses direction and starts a new cycle. Disk controllers usually perform the task of reordering read requests to improve performance, since they are intimately aware of the organization of blocks on disk, of the rotational position of the disk platters, and of the position of the disk arm.  

**438 Chapter 10 Storage and File Structure**

• **File organization**. To reduce block-access time, we can organize blocks on disk in a way that corresponds closely to the way we expect data to be accessed. For example, if we expect a file to be accessed sequentially, then we should ideally keep all the blocks of the file sequentially on adjacent cylinders. Older operating systems, such as the IBM mainframe operating systems, provided programmers fine control on placement of files, allowing a programmer to reserve a set of cylinders for storing a file. However, this control places a burden on the programmer or system administrator to decide, for example, how many cylinders to allocate for a file, and may require costly reorganization if data are inserted to or deleted from the file.

Subsequent operating systems, such as Unix and Microsoft Windows, hide the disk organization from users, and manage the allocation internally. Although they do not guarantee that all blocks of a file are laid out sequen- tially, they allocate multiple consecutive blocks (an **extent**) at a time to a file. Sequential access to the file then only needs one seek per extent, instead of one seek per block. Over time, a sequential file that has multiple small ap- pends may become **fragmented**; that is, its blocks become scattered all over the disk. To reduce fragmentation, the system can make a backup copy of the data on disk and restore the entire disk. The restore operation writes back the blocks of each file contiguously (or nearly so). Some systems (such as dif- ferent versions of the Windows operating system) have utilities that scan the disk and then move blocks to decrease the fragmentation. The performance increases realized from these techniques can be large.

• **Nonvolatile write buffers**. Since the contents of main memory are lost in a power failure, information about database updates has to be recorded on disk to survive possible system crashes. For this reason, the performance of update-intensive database applications, such as transaction-processing sys- tems, is heavily dependent on the speed of disk writes.

We can use **nonvolatile random-access memory** (NVRAM) to speed up disk writes drastically. The contents of NVRAM are not lost in power failure. A common way to implement NVRAM is to use battery–backed-up RAM, al- though flash memory is also increasingly being used for nonvolatile write buffering. The idea is that, when the database system (or the operating sys- tem) requests that a block be written to disk, the disk controller writes the block to an NVRAM buffer, and immediately notifies the operating system that the write completed successfully. The controller writes the data to their destination on disk whenever the disk does not have any other requests, or when the NVRAM buffer becomes full. When the database system requests a block write, it notices a delay only if the NVRAM buffer is full. On recovery from a system crash, any pending buffered writes in the NVRAM are written back to the disk. NVRAM buffers are found in certain high end disks, but are more frequently found in “RAID controllers”; we study RAID in Section 10.3.

• **Log disk**. Another approach to reducing write latencies is to use a log disk— that is, a disk devoted to writing a sequential log—in much the same way as a nonvolatile RAM buffer. All access to the log disk is sequential, essentially  

**10.2 Magnetic Disk and Flash Storage 439**

eliminating seek time, and several consecutive blocks can be written at once, making writes to the log disk several times faster than random writes. As before, the data have to be written to their actual location on disk as well, but the log disk can do the write later, without the database system having to wait for the write to complete. Furthermore, the log disk can reorder the writes to minimize disk-arm movement. If the system crashes before some writes to the actual disk location have completed, when the system comes back up it reads the log disk to find those writes that had not been completed, and carries them out then.

File systems that support log disks as above are called **journaling file systems**. Journaling file systems can be implemented even without a separate log disk, keeping data and the log on the same disk. Doing so reduces the monetary cost, at the expense of lower performance.

Most modern file systems implement journaling, and use the log disk when writing internal file system information such as file allocation information. Earlier-generation file systems allowed write reordering without using a log disk, and ran the risk that the file system data structures on disk would be corrupted if the system crashed. Suppose, for example, that a file system used a linked list, and inserted a new node at the end by first writing the data for the new node, then updating the pointer from the previous node. Suppose also that the writes were reordered, so the pointer was updated first, and the system crashes before the new node is written. The contents of the node would then be whatever junk was on disk earlier, resulting in a corrupted data structure.

To deal with the possibility of such data structure corruption, earlier- generation file systems had to perform a file system consistency check on system restart, to ensure that the data structures were consistent. And if they were not, extra steps had to be taken to restore them to consistency. These checks resulted in long delays in system restart after a crash, and the delays became worse as disk systems grew to higher capacities. Journaling file systems allow quick restart without the need for such file system consistency checks.

However, writes performed by applications are usually not written to the log disk. Database systems implement their own forms of logging, which we study later in Chapter 16.

**10.2.4 Flash Storage**

As mentioned in Section 10.1, there are two types of flash memory, NOR flash and NAND flash. NOR flash allows random access to individual words of memory, and has read time comparable to main memory. However, unlike NOR flash, reading from NAND flash requires an entire _page_ of data, typically consisting of between 512 and 4096 bytes, to be fetched from NAND flash into main memory. Pages in a NAND flash are thus similar to sectors in a magnetic disk. But NAND flash is significantly cheaper than NOR flash, and has much higher storage capacity, and is by far the more widely used.  

**440 Chapter 10 Storage and File Structure**

Storage systems built using NAND flash provide the same block-oriented interface as disk storage. Compared to magnetic disks, flash memory can provide much faster random access: a page of data can be retrieved in around 1 or 2 microseconds from flash, whereas a random access on disk would take 5 to 10 milliseconds. Flash memory has a lower transfer rate than magnetic disks, with 20 megabytes per second being common. Some more recent flash memories have increased transfer rates of 100 to 200 megabytes per second. However, solid state drives use multiple flash memory chips in parallel, to increase transfer rates to over 200 megabytes per second, which is faster than transfer rates of most disks.

Writes to flash memory are a little more complicated. A write to a page of flash memory typically takes a few microseconds. However, once written, a page of flash memory cannot be directly overwritten. Instead, it has to be erased and rewritten subsequently. The erase operation can be performed on a number of pages, called an **erase block**, at once, and takes about 1 to 2 milliseconds. The size of an erase block (often referred to as just “block” in flash literature) is usually significantly larger than the block size of the storage system. Further, there is a limit to how many times a flash page can be erased, typically around 100,000 to 1,000,000 times. Once this limit is reached, errors in storing bits are likely to occur.

Flash memory systems limit the impact of both the slow erase speed and the update limits by mapping logical page numbers to physical page numbers. When a logical page is updated, it can be remapped to any already erased physical page, and the original location can be erased later. Each physical page has a small area of memory where its logical address is stored; if the logical address is remapped to a different physical page, the original physical page is marked as deleted. Thus by scanning the physical pages, we can find where each logical page resides. The logical-to-physical page mapping is replicated in an in-memory **translation table** for quick access.

Blocks containing multiple deleted pages are periodically erased, taking care to first copy nondeleted pages in those blocks to a different block (the transla- tion table is updated for these nondeleted pages). Since each physical page can be updated only a fixed number of times, physical pages that have been erased many times are assigned “cold data,” that is, data that are rarely updated, while pages that have not been erased many times are used to store “hot data,” that is, data that are updated frequently. This principle of evenly distributing erase op- erations across physical blocks is called **wear leveling**, and is usually performed transparently by flash-memory controllers. If a physical page is damaged due to an excessive number of updates, it can be removed from usage, without affecting the flash memory as a whole.

All the above actions are carried out by a layer of software called the **flash translation layer**; above this layer, flash storage looks identical to magnetic disk storage, providing the same page/sector-oriented interface, except that flash stor- age is much faster. File systems and database storage structures can thus see an identical logical view of the underlying storage structure, regardless of whether it is flash or magnetic storage.

**Hybrid disk drives** are hard-disk systems that combine magnetic storage with a smaller amount of flash memory, which is used as a cache for frequently  

**10.3 RAID 441**

accessed data. Frequently accessed data that are rarely updated are ideal for caching in flash memory.

**10.3 RAID**

The data-storage requirements of some applications (in particular Web, database, and multimedia applications) have been growing so fast that a large number of disks are needed to store their data, even though disk-drive capacities have been growing very fast.

Having a large number of disks in a system presents opportunities for im- proving the rate at which data can be read or written, if the disks are operated in parallel. Several independent reads or writes can also be performed in parallel. Furthermore, this setup offers the potential for improving the reliability of data storage, because redundant information can be stored on multiple disks. Thus, failure of one disk does not lead to loss of data.

A variety of disk-organization techniques, collectively called **redundant ar- rays of independent disks** (**RAID)**, have been proposed to achieve improved performance and reliability.

In the past, system designers viewed storage systems composed of several small, cheap disks as a cost-effective alternative to using large, expensive disks; the cost per megabyte of the smaller disks was less than that of larger disks. In fact, the I in RAID, which now stands for _independent_, originally stood for _inexpensive_. Today, however, all disks are physically small, and larger-capacity disks actually have a lower cost per megabyte. RAID systems are used for their higher reliability and higher performance rate, rather than for economic reasons. Another key justification for RAID use is easier management and operations.

**10.3.1 Improvement of Reliability via Redundancy**

Let us first consider reliability. The chance that at least one disk out of a set of _N_ disks will fail is much higher than the chance that a specific single disk will fail. Suppose that the mean time to failure of a disk is 100,000 hours, or slightly over 11 years. Then, the mean time to failure of some disk in an array of 100 disks will be 100,000_/_100 = 1000 hours, or around 42 days, which is not long at all! If we store only one copy of the data, then each disk failure will result in loss of a significant amount of data (as discussed in Section 10.2.1). Such a high frequency of data loss is unacceptable.

The solution to the problem of reliability is to introduce **redundancy**; that is, we store extra information that is not needed normally, but that can be used in the event of failure of a disk to rebuild the lost information. Thus, even if a disk fails, data are not lost, so the effective mean time to failure is increased, provided that we count only failures that lead to loss of data or to nonavailability of data.

The simplest (but most expensive) approach to introducing redundancy is to duplicate every disk. This technique is called **mirroring** (or, sometimes, _shadow- ing_). A logical disk then consists of two physical disks, and every write is carried  

**442 Chapter 10 Storage and File Structure**

out on both disks. If one of the disks fails, the data can be read from the other. Data will be lost only if the second disk fails before the first failed disk is repaired.

The mean time to failure (where failure is the loss of data) of a mirrored disk depends on the mean time to failure of the individual disks, as well as on the **mean time to repair**, which is the time it takes (on an average) to replace a failed disk and to restore the data on it. Suppose that the failures of the two disks are _independent_; that is, there is no connection between the failure of one disk and the failure of the other. Then, if the mean time to failure of a single disk is 100,000 hours, and the mean time to repair is 10 hours, the **mean time to data loss** of a mirrored disk system is 100_,_ 0002_/_(2 ∗ 10) = 500 ∗ 106 hours, or 57,000 years! (We do not go into the derivations here; references in the bibliographical notes provide the details.)

You should be aware that the assumption of independence of disk failures is not valid. Power failures, and natural disasters such as earthquakes, fires, and floods, may result in damage to both disks at the same time. As disks age, the probability of failure increases, increasing the chance that a second disk will fail while the first is being repaired. In spite of all these considerations, however, mirrored-disk systems offer much higher reliability than do single-disk systems. Mirrored-disk systems with mean time to data loss of about 500,000 to 1,000,000 hours, or 55 to 110 years, are available today.

Power failures are a particular source of concern, since they occur far more frequently than do natural disasters. Power failures are not a concern if there is no data transfer to disk in progress when they occur. However, even with mirroring of disks, if writes are in progress to the same block in both disks, and power fails before both blocks are fully written, the two blocks can be in an inconsistent state. The solution to this problem is to write one copy first, then the next, so that one of the two copies is always consistent. Some extra actions are required when we restart after a power failure, to recover from incomplete writes. This matter is examined in Practice Exercise 10.3.

**10.3.2 Improvement in Performance via Parallelism**

Now let us consider the benefit of parallel access to multiple disks. With disk mirroring, the rate at which read requests can be handled is doubled, since read requests can be sent to either disk (as long as both disks in a pair are functional, as is almost always the case). The transfer rate of each read is the same as in a single-disk system, but the number of reads per unit time has doubled.

With multiple disks, we can improve the transfer rate as well (or instead) by **striping data** across multiple disks. In its simplest form, data striping consists of splitting the bits of each byte across multiple disks; such striping is called **bit- level striping**. For example, if we have an array of eight disks, we write bit _i_ of each byte to disk _i_ . The array of eight disks can be treated as a single disk with sectors that are eight times the normal size, and, more important, that has eight times the transfer rate. In such an organization, every disk participates in every access (read or write), so the number of accesses that can be processed per second is about the same as on a single disk, but each access can read eight times as many  

**10.3 RAID 443**

data in the same time as on a single disk. Bit-level striping can be generalized to a number of disks that either is a multiple of 8 or a factor of 8. For example, if we use an array of four disks, bits _i_ and 4 + _i_ of each byte go to disk _i_ .

**Block-level striping** stripes blocks across multiple disks. It treats the array of disks as a single large disk, and it gives blocks logical numbers; we assume the block numbers start from 0. With an array of _n_ disks, block-level striping assigns logical block _i_ of the disk array to disk (_i_ mod _n_) + 1; it uses the _i/n_th physical

(a) RAID 0: nonredundant striping

(b) RAID 1: mirrored disks

(c) RAID 2: memory-style error-correcting codes

(d) RAID 3: bit-interleaved parity

(e) RAID 4: block-interleaved parity

(f) RAID 5: block-interleaved distributed parity

(g) RAID 6: P + Q redundancy

P

P P

P P

P P

P P

P

C CCC

P PP

P

P P

**Figure 10.3** RAID levels.  

**444 Chapter 10 Storage and File Structure**

block of the disk to store logical block _i_ . For example, with 8 disks, logical block 0 is stored in physical block 0 of disk 1, while logical block 11 is stored in physical block 1 of disk 4. When reading a large file, block-level striping fetches _n_ blocks at a time in parallel from the _n_ disks, giving a high data-transfer rate for large reads. When a single block is read, the data-transfer rate is the same as on one disk, but the remaining _n_ − 1 disks are free to perform other actions.

Block-level striping is the most commonly used form of data striping. Other levels of striping, such as bytes of a sector or sectors of a block, also are possible.

In summary, there are two main goals of parallelism in a disk system:

**1\.** Load-balance multiple small accesses (block accesses), so that the through- put of such accesses increases.

**2\.** Parallelize large accesses so that the response time of large accesses is re- duced.

**10.3.3 RAID Levels**

Mirroring provides high reliability, but it is expensive. Striping provides high data-transfer rates, but does not improve reliability. Various alternative schemes aim to provide redundancy at lower cost by combining disk striping with “parity” bits (which we describe next). These schemes have different cost–performance trade-offs. The schemes are classified into **RAID levels**, as in Figure 10.3. (In the figure, P indicates error-correcting bits, and C indicates a second copy of the data.) For all levels, the figure depicts four disks’ worth of data, and the extra disks depicted are used to store redundant information for failure recovery.

• **RAID level 0** refers to disk arrays with striping at the level of blocks, but without any redundancy (such as mirroring or parity bits). Figure 10.3a shows an array of size 4.

• **RAID level 1** refers to disk mirroring with block striping. Figure 10.3b shows a mirrored organization that holds four disks’ worth of data. Note that some vendors use the term **RAID level 1+0** or **RAID level 10** to refer to mirroring with striping, and use the term RAID level 1 to refer to mirroring without striping. Mirroring without striping can also be used with arrays of disks, to give the appearance of a single large, reliable disk: if each disk has _M_ blocks, logical blocks 0 to _M_ − 1 are stored on disk 0, _M_ to 2_M_ − 1 on disk 1(the second disk), and so on, and each disk is mirrored.2

• **RAID level 2**, known as memory-style error-correcting-code (ECC) organiza- tion, employs parity bits. Memory systems have long used parity bits for

2Note that some vendors use the term RAID 0+1 to refer to a version of RAID that uses striping to create a RAID 0 array, and mirrors the array onto another array, with the difference from RAID 1 being that if a disk fails, the RAID 0 array containing the disk becomes unusable. The mirrored array can still be used, so there is no loss of data. This arrangement is inferior to RAID 1 when a disk has failed, since the other disks in the RAID 0 array can continue to be used in RAID 1, but remain idle in RAID 0+1.  

**10.3 RAID 445**

error detection and correction. Each byte in a memory system may have a parity bit associated with it that records whether the numbers of bits in the byte that are set to 1 is even (parity = 0) or odd (parity = 1). If one of the bits in the byte gets damaged (either a 1 becomes a 0, or a 0 becomes a 1), the parity of the byte changes and thus will not match the stored parity. Similarly, if the stored parity bit gets damaged, it will not match the computed parity. Thus, all 1-bit errors will be detected by the memory system. Error-correcting schemes store 2 or more extra bits, and can reconstruct the data if a single bit gets damaged.

The idea of error-correcting codes can be used directly in disk arrays by striping bytes across disks. For example, the first bit of each byte could be stored in disk 0, the second bit in disk 1, and so on until the eighth bit is stored in disk 7, and the error-correction bits are stored in further disks.

Figure 10.3c shows the level 2 scheme. The disks labeled _P_ store the error- correction bits. If one of the disks fails, the remaining bits of the byte and the associated error-correction bits can be read from other disks, and can be used to reconstruct the damaged data. Figure 10.3c shows an array of size 4; note RAID level 2 requires only three disks’ overhead for four disks of data, unlike RAID level 1, which required four disks’ overhead.

• **RAID level 3**, bit-interleaved parity organization, improves on level 2 by exploiting the fact that disk controllers, unlike memory systems, can detect whether a sector has been read correctly, so a single parity bit can be used for error correction, as well as for detection. The idea is as follows: If one of the sectors gets damaged, the system knows exactly which sector it is, and, for each bit in the sector, the system can figure out whether it is a 1 or a 0 by computing the parity of the corresponding bits from sectors in the other disks. If the parity of the remaining bits is equal to the stored parity, the missing bit is 0; otherwise, it is 1.

RAID level 3 is as good as level 2, but is less expensive in the number of extra disks (it has only a one-disk overhead), so level 2 is not used in practice. Figure 10.3d shows the level 3 scheme.

RAID level 3 has two benefits over level 1. It needs only one parity disk for several regular disks, whereas level 1 needs one mirror disk for every disk, and thus level 3 reduces the storage overhead. Since reads and writes of a byte are spread out over multiple disks, with _N_\-way striping of data, the transfer rate for reading or writing a single block is _N_ times faster than a RAID level 1 organization using _N_\-way striping. On the other hand, RAID level 3 supports a lower number of I/O operations per second, since every disk has to participate in every I/O request.

• **RAID level 4**, block-interleaved parity organization, uses block-level striping, like RAID 0, and in addition keeps a parity block on a separate disk for corresponding blocks from _N_ other disks. This scheme is shown pictorially in Figure 10.3e. If one of the disks fails, the parity block can be used with the corresponding blocks from the other disks to restore the blocks of the failed disk.  

**446 Chapter 10 Storage and File Structure**

A block read accesses only one disk, allowing other requests to be processed by the other disks. Thus, the data-transfer rate for each access is slower, but multiple read accesses can proceed in parallel, leading to a higher overall I/O rate. The transfer rates for large reads is high, since all the disks can be read in parallel; large writes also have high transfer rates, since the data and parity can be written in parallel.

Small independent writes, on the other hand, cannot be performed in parallel. A write of a block has to access the disk on which the block is stored, as well as the parity disk, since the parity block has to be updated. Moreover, both the old value of the parity block and the old value of the block being written have to be read for the new parity to be computed. Thus, a single write requires four disk accesses: two to read the two old blocks, and two to write the two blocks.

• **RAID level 5**, block-interleaved distributed parity, improves on level 4 by partitioning data and parity among all _N_ \+ 1 disks, instead of storing data in _N_ disks and parity in one disk. In level 5, all disks can participate in satisfying read requests, unlike RAID level 4, where the parity disk cannot participate, so level 5 increases the total number of requests that can be met in a given amount of time. For each set of _N_ logical blocks, one of the disks stores the parity, and the other _N_ disks store the blocks.

Figure 10.3f shows the setup. The P’s are distributed across all the disks. For example, with an array of 5 disks, the parity block, labeled Pk, for logical blocks 4_k,_ 4_k_ \+ 1_,_ 4_k_ \+ 2_,_ 4_k_ \+ 3 is stored in disk _k_ mod 5; the corresponding blocks of the other four disks store the 4 data blocks 4_k_ to 4_k_+3. The following table indicates how the first 20 blocks, numbered 0 to 19, and their parity blocks are laid out. The pattern shown gets repeated on further blocks.

P0 4 8

12 16

0 P1

9 13 17

1 5

P2 14 18

2 6

10 P3 19

3 7

11 15 P4

Note that a parity block cannot store parity for blocks in the same disk, since then a disk failure would result in loss of data as well as of parity, and hence would not be recoverable. Level 5 subsumes level 4, since it offers better read –write performance at the same cost, so level 4 is not used in practice.

• **RAID level 6**, the P + Q redundancy scheme, is much like RAID level 5, but stores extra redundant information to guard against multiple disk failures. Instead of using parity, level 6 uses error-correcting codes such as the Reed– Solomon codes (see the bibliographical notes). In the scheme in Figure 10.3g, 2 bits of redundant data are stored for every 4 bits of data—unlike 1 parity bit in level 5—and the system can tolerate two disk failures.  

**10.3 RAID 447**

Finally, we note that several variations have been proposed to the basic RAID schemes described here, and different vendors use different terminologies for the variants.

**10.3.4 Choice of RAID Level**

The factors to be taken into account in choosing a RAID level are:

• Monetary cost of extra disk-storage requirements.

• Performance requirements in terms of number of I/O operations.

• Performance when a disk has failed.

• Performance during rebuild (that is, while the data in a failed disk are being rebuilt on a new disk).

The time to rebuild the data of a failed disk can be significant, and it varies with the RAID level that is used. Rebuilding is easiest for RAID level 1, since data can be copied from another disk; for the other levels, we need to access all the other disks in the array to rebuild data of a failed disk. The **rebuild performance** of a RAID system may be an important factor if continuous availability of data is required, as it is in high-performance database systems. Furthermore, since rebuild time can form a significant part of the repair time, rebuild performance also influences the mean time to data loss.

RAID level 0 is used in high-performance applications where data safety is not critical. Since RAID levels 2 and 4 are subsumed by RAID levels 3 and 5, the choice of RAID levels is restricted to the remaining levels. Bit striping (level 3) is inferior to block striping (level 5), since block striping gives as good data-transfer rates for large transfers, while using fewer disks for small transfers. For small transfers, the disk access time dominates anyway, so the benefit of parallel reads diminishes. In fact, level 3 may perform worse than level 5 for a small transfer, since the transfer completes only when corresponding sectors on all disks have been fetched; the average latency for the disk array thus becomes very close to the worst-case latency for a single disk, negating the benefits of higher transfer rates. Level 6 is not supported currently by many RAID implementations, but it offers better reliability than level 5 and can be used in applications where data safety is very important.

The choice between RAID level 1 and level 5 is harder to make. RAID level 1 is popular for applications such as storage of log files in a database system, since it offers the best write performance. RAID level 5 has a lower storage overhead than level 1, but has a higher time overhead for writes. For applications where data are read frequently, and written rarely, level 5 is the preferred choice.

Disk-storage capacities have been growing at a rate of over 50 percent per year for many years, and the cost per byte has been falling at the same rate. As a result, for many existing database applications with moderate storage requirements, the monetary cost of the extra disk storage needed for mirroring has become relatively small (the extra monetary cost, however, remains a significant issue for storage-  

**448 Chapter 10 Storage and File Structure**

intensive applications such as video data storage). Access speeds have improved at a much slower rate (around a factor of 3 over 10 years), while the number of I/O operations required per second has increased tremendously, particularly for Web application servers.

RAID level 5, which increases the number of I/O operations needed to write a single logical block, pays a significant time penalty in terms of write performance. RAID level 1 is therefore the RAID level of choice for many applications with moderate storage requirements and high I/O requirements.

RAID system designers have to make several other decisions as well. For example, how many disks should there be in an array? How many bits should be protected by each parity bit? If there are more disks in an array, data-transfer rates are higher, but the system will be more expensive. If there are more bits protected by a parity bit, the space overhead due to parity bits is lower, but there is an increased chance that a second disk will fail before the first failed disk is repaired, and that will result in data loss.

**10.3.5 Hardware Issues**

Another issue in the choice of RAID implementations is at the level of hardware. RAID can be implemented with no change at the hardware level, using only soft- ware modification. Such RAID implementations are called **software RAID**. How- ever, there are significant benefits to be had by building special-purpose hardware to support RAID, which we outline below; systems with special hardware support are called **hardware RAID** systems.

Hardware RAID implementations can use nonvolatile RAM to record writes before they are performed. In case of power failure, when the system comes back up, it retrieves information about any incomplete writes from nonvolatile RAM and then completes the writes. Without such hardware support, extra work needs to be done to detect blocks that may have been partially written before power failure (see Practice Exercise 10.3).

Even if all writes are completed properly, there is a small chance of a sector in a disk becoming unreadable at some point, even though it was successfully written earlier. Reasons for loss of data on individual sectors could range from manufacturing defects, to data corruption on a track when an adjacent track is written repeatedly. Such loss of data that were successfully written earlier is sometimes referred to as a _latent failure_, or as _bit rot_. When such a failure happens, if it is detected early the data can be recovered from the remaining disks in the RAID organization. However, if such a failure remains undetected, a single disk failure could lead to data loss if a sector in one of the other disks has a latent failure.

To minimize the chance of such data loss, good RAID controllers perform **scrubbing**; that is, during periods when disks are idle, every sector of every disk is read, and if any sector is found to be unreadable, the data are recovered from the remaining disks in the RAID organization, and the sector is written back. (If the physical sector is damaged, the disk controller would remap the logical sector address to a different physical sector on disk.)  

**10.4 Tertiary Storage 449**

Some hardware RAID implementations permit **hot swapping**; that is, faulty disks can be removed and replaced by new ones without turning power off. Hot swapping reduces the mean time to repair, since replacement of a disk does not have to wait until a time when the system can be shut down. In fact many critical systems today run on a 24 × 7 schedule; that is, they run 24 hours a day, 7 days a week, providing no time for shutting down and replacing a failed disk. Further, many RAID implementations assign a spare disk for each array (or for a set of disk arrays). If a disk fails, the spare disk is immediately used as a replacement. As a result, the mean time to repair is reduced greatly, minimizing the chance of any data loss. The failed disk can be replaced at leisure.

The power supply, or the disk controller, or even the system interconnection in a RAID system could become a single point of failure that could stop functioning of the RAID system. To avoid this possibility, good RAID implementations have multiple redundant power supplies (with battery backups so they continue to function even if power fails). Such RAID systems have multiple disk interfaces, and multiple interconnections to connect the RAID system to the computer system (or to a network of computer systems). Thus, failure of any single component will not stop the functioning of the RAID system.

**10.3.6 Other RAID Applications**

The concepts of RAID have been generalized to other storage devices, including arrays of tapes, and even to the broadcast of data over wireless systems. When applied to arrays of tapes, the RAID structures are able to recover data even if one of the tapes in an array of tapes is damaged. When applied to broadcast of data, a block of data is split into short units and is broadcast along with a parity unit; if one of the units is not received for any reason, it can be reconstructed from the other units.

**10.4 Tertiary Storage**

In a large database system, some of the data may have to reside on tertiary storage. The two most common tertiary storage media are optical disks and magnetic tapes.

**10.4.1 Optical Disks**

Compact disks have been a popular medium for distributing software, multime- dia data such as audio and images, and other electronically published informa- tion. They have a storage capacity of 640 to 700 megabytes, and they are cheap to mass-produce. Digital video disks (DVDs) have now replaced compact disks in applications that require larger amounts of data. Disks in the DVD-5 format can store 4.7 gigabytes of data (in one recording layer), while disks in the DVD-9 format can store 8.5 gigabytes of data (in two recording layers). Recording on both sides of a disk yields even larger capacities; DVD-10 and DVD-18 formats, which are the two-sided versions of DVD-5 and DVD-9, can store 9.4 gigabytes  

**450 Chapter 10 Storage and File Structure**

and 17 gigabytes, respectively. The _Blu-ray DVD_ format has a significantly higher capacity of 27 to 54 gigabytes per disk.

CD and DVD drives have much longer seek times (100 milliseconds is common) than do magnetic-disk drives, since the head assembly is heavier. Rotational speeds are typically lower than those of magnetic disks, although the faster CD and DVD drives have rotation speeds of about 3000 rotations per minute, which is comparable to speeds of lower-end magnetic-disk drives. Rotational speeds of CD drives originally corresponded to the audio CD standards, and the speeds of DVD drives originally corresponded to the DVD video standards, but current- generation drives rotate at many times the standard rate.

Data-transfer rates are somewhat less than for magnetic disks. Current CD drives read at around 3 to 6 megabytes per second, and current DVD drives read at 8 to 20 megabytes per second. Like magnetic-disk drives, optical disks store more data in outside tracks and less data in inner tracks. The transfer rate of optical drives is characterized as _n_×, which means the drive supports transfers at _n_ times the standard rate; rates of around 50× for CD and 16× for DVD are now common.

The record-once versions of optical disks (CD-R, DVD-R, and DVD+R) are pop- ular for distribution of data and particularly for archival storage of data because they have a high capacity, have a longer lifetime than magnetic disks, and can be removed and stored at a remote location. Since they cannot be overwritten, they can be used to store information that should not be modified, such as audit trails. The multiple-write versions (CD-RW, DVD-RW, DVD+RW, and DVD-RAM) are also used for archival purposes.

**Jukeboxes** are devices that store a large number of optical disks (up to several hundred) and load them automatically on demand to one of a small number of drives (usually 1 to 10). The aggregate storage capacity of such a system can be many terabytes. When a disk is accessed, it is loaded by a mechanical arm from a rack onto a drive (any disk that was already in the drive must first be placed back on the rack). The disk load/unload time is usually of the order of a few seconds —very much longer than disk access times.

**10.4.2 Magnetic Tapes**

Although magnetic tapes are relatively permanent, and can hold large volumes of data, they are slow in comparison to magnetic and optical disks. Even more im- portant, magnetic tapes are limited to sequential access. Thus, they cannot provide random access for secondary-storage requirements, although historically, prior to the use of magnetic disks, tapes were used as a secondary-storage medium.

Tapes are used mainly for backup, for storage of infrequently used informa- tion, and as an off-line medium for transferring information from one system to another. Tapes are also used for storing large volumes of data, such as video or image data, that either do not need to be accessible quickly or are so voluminous that magnetic-disk storage would be too expensive.

A tape is kept in a spool, and is wound or rewound past a read–write head. Moving to the correct spot on a tape can take seconds or even minutes, rather than  

**10.5 File Organization 451**

milliseconds; once positioned, however, tape drives can write data at densities and speeds approaching those of disk drives. Capacities vary, depending on the length and width of the tape and on the density at which the head can read and write. The market is currently fragmented among a wide variety of tape formats. Currently available tape capacities range from a few gigabytes with the **Digital Audio Tape** (DAT) format, 10 to 40 gigabytes with the **Digital Linear Tape** (DLT) format, 100 gigabytes and higher with the **Ultrium** format, to 330 gigabytes with **Ampex helical scan** tape formats. Data-transfer rates are of the order of a few to tens of megabytes per second.

Tape devices are quite reliable, and good tape drive systems perform a read of the just-written data to ensure that it has been recorded correctly. Tapes, however, have limits on the number of times that they can be read or written reliably.

**Tape jukeboxes**, like optical disk jukeboxes, hold large numbers of tapes, with a few drives onto which the tapes can be mounted; they are used for storing large volumes of data, ranging up to many petabytes (1015 bytes), with access times on the order of seconds to a few minutes. Applications that need such enormous data storage include imaging systems that gather data from remote-sensing satellites, and large video libraries for television broadcasters.

Some tape formats (such as the Accelis format) support faster seek times (of the order of tens of seconds), and are intended for applications that retrieve information from jukeboxes. Most other tape formats provide larger capacities, at the cost of slower access; such formats are ideal for data backup, where fast seeks are not important.

Tape drives have been unable to keep up with the enormous improvements in disk drive capacity and corresponding reduction in storage cost. While the cost of tapes is low, the cost of tape drives and tape libraries is significantly higher than the cost of a disk drive: a tape library capable of storing a few terabytes can costs tens of thousands of dollars. Backing up data to disk drives has become a cost-effective alternative to tape backup for a number of applications.

**10.5 File Organization**

A database is mapped into a number of different files that are maintained by the underlying operating system. These files reside permanently on disks. A **file** is organized logically as a sequence of records. These records are mapped onto disk blocks. Files are provided as a basic construct in operating systems, so we shall assume the existence of an underlying _file system_. We need to consider ways of representing logical data models in terms of files.

Each file is also logically partitioned into fixed-length storage units called **blocks**, which are the units of both storage allocation and data transfer. Most databases use block sizes of 4 to 8 kilobytes by default, but many databases allow the block size to be specified when a database instance is created. Larger block sizes can be useful in some database applications.

A block may contain several records; the exact set of records that a block contains is determined by the form of physical data organization being used. We  

**452 Chapter 10 Storage and File Structure**

shall assume that _no record is larger than a block_. This assumption is realistic for most data-processing applications, such as our university example. There are certainly several kinds of large data items, such as images, that can be significantly larger than a block. We briefly discuss how to handle such large data items later, in Section 10.5.2, by storing large data items separately, and storing a pointer to the data item in the record.

In addition, we shall require that _each record is entirely contained in a single block_; that is, no record is contained partly in one block, and partly in another. This restriction simplifies and speeds up access to data items.

In a relational database, tuples of distinct relations are generally of different sizes. One approach to mapping the database to files is to use several files, and to store records of only one fixed length in any given file. An alternative is to structure our files so that we can accommodate multiple lengths for records; however, files of fixed-length records are easier to implement than are files of variable-length records. Many of the techniques used for the former can be applied to the variable-length case. Thus, we begin by considering a file of fixed-length records, and consider storage of variable-length records later.

**10.5.1 Fixed-Length Records**

As an example, let us consider a file of _instructor_ records for our university database. Each record of this file is defined (in pseudocode) as:

**type** _instructor_ \= **record** _ID_ **varchar** (5); _name_ **varchar**(20); _dept name_ **varchar** (20); _salary_ **numeric** (8,2);

**end**

Assume that each character occupies 1 byte and that numeric (8,2) occupies 8 bytes. Suppose that instead of allocating a variable amount of bytes for the attributes _ID_, _name_, and _dept name_, we allocate the maximum number of bytes that each attribute can hold. Then, the _instructor_ record is 53 bytes long. A simple approach is to use the first 53 bytes for the first record, the next 53 bytes for the second record, and so on (Figure 10.4). However, there are two problems with this simple approach:

**1\.** Unless the block size happens to be a multiple of 53 (which is unlikely), some records will cross block boundaries. That is, part of the record will be stored in one block and part in another. It would thus require two block accesses to read or write such a record.

**2\.** It is difficult to delete a record from this structure. The space occupied by the record to be deleted must be filled with some other record of the file, or we must have a way of marking deleted records so that they can be ignored.  

**10.5 File Organization 453**

Srinivasan Comp. Sci. 65000 Wu Finance 90000 Mozart Music 40000 Einstein Physics 95000 El Said History 60000 Gold Physics 87000 Katz Comp. Sci. 75000 Califieri History 62000 Singh Finance 80000 Crick Biology 72000 Brandt Comp. Sci. 92000

15151

10101 12121

22222 32343 33456 45565 58583 76543 76766 83821 98345 Kim Elec. Eng. 80000

record 0 record 1 record 2 record 3 record 4 record 5 record 6 record 7 record 8 record 9 record 10 record 11

**Figure 10.4** File containing _instructor_ records.

To avoid the first problem, we allocate only as many records to a block as would fit entirely in the block (this number can be computed easily by dividing the block size by the record size, and discarding the fractional part). Any remaining bytes of each block are left unused.

When a record is deleted, we could move the record that came after it into the space formerly occupied by the deleted record, and so on, until every record fol- lowing the deleted record has been moved ahead (Figure 10.5). Such an approach requires moving a large number of records. It might be easier simply to move the final record of the file into the space occupied by the deleted record (Figure 10.6).

It is undesirable to move records to occupy the space freed by a deleted record, since doing so requires additional block accesses. Since insertions tend to be more frequent than deletions, it is acceptable to leave open the space occupied by the

Srinivasan Comp. Sci. 65000 Wu Finance 90000 Mozart Music 40000 El Said History 60000 Gold Physics 87000 Katz Comp. Sci. 75000 Califieri History 62000 Singh Finance 80000 Crick Biology 72000 Brandt Comp. Sci. 92000

15151

10101 12121

32343 33456 45565 58583 76543 76766 83821 98345 Kim Elec. Eng. 80000

record 0 record 1 record 2 record 4 record 5 record 6 record 7 record 8 record 9 record 10 record 11

**Figure 10.5** File of Figure 10.4, with record 3 deleted and all records moved.  

**454 Chapter 10 Storage and File Structure**

Srinivasan Comp. Sci. 65000 Wu Finance 90000 Mozart Music 40000

El Said History 60000 Gold Physics 87000 Katz Comp. Sci. 75000 Califieri History 62000 Singh Finance 80000 Crick Biology 72000 Brandt Comp. Sci. 92000

15151

10101 12121

32343 33456 45565 58583 76543 76766 83821

record 0 record 1 record 2

record 4 record 5 record 6 record 7 record 8 record 9 record 10

98345 Kim Elec. Eng. 80000record 11

**Figure 10.6** File of Figure 10.4, with record 3 deleted and final record moved.

deleted record, and to wait for a subsequent insertion before reusing the space. A simple marker on a deleted record is not sufficient, since it is hard to find this available space when an insertion is being done. Thus, we need to introduce an additional structure.

At the beginning of the file, we allocate a certain number of bytes as a **file header**. The header will contain a variety of information about the file. For now, all we need to store there is the address of the first record whose contents are deleted. We use this first record to store the address of the second available record, and so on. Intuitively, we can think of these stored addresses as _pointers_, since they point to the location of a record. The deleted records thus form a linked list, which is often referred to as a **free list**. Figure 10.7 shows the file of Figure 10.4, with the free list, after records 1, 4, and 6 have been deleted.

On insertion of a new record, we use the record pointed to by the header. We change the header pointer to point to the next available record. If no space is available, we add the new record to the end of the file.

Insertion and deletion for files of fixed-length records are simple to imple- ment, because the space made available by a deleted record is exactly the space needed to insert a record. If we allow records of variable length in a file, this match no longer holds. An inserted record may not fit in the space left free by a deleted record, or it may fill only part of that space.

**10.5.2 Variable-Length Records**

Variable-length records arise in database systems in several ways:

• Storage of multiple record types in a file.

• Record types that allow variable lengths for one or more fields.

• Record types that allow repeating fields, such as arrays or multisets.  

**10.5 File Organization 455**

header record 0 record 1 record 2 record 3 record 4 record 5 record 6 record 7 record 8 record 9 record 10 record 11

72000 92000 80000

65000

40000 95000

87000

62000

76766 83821 98345

10101

15151 22222

33456

58583 76543

Crick Brandt Kim

Srinivasan

Mozart Einstein

Gold

Califieri Singh

Biology

Elec. Eng.

Comp. Sci.

Comp. Sci.

Music Physics

Physics

History Finance 80000

**Figure 10.7** File of Figure 10.4, with free list after deletion of records 1, 4, and 6.

Different techniques for implementing variable-length records exist. Two different problems must be solved by any such technique:

• How to represent a single record in such a way that individual attributes can be extracted easily.

• How to store variable-length records within a block, such that records in a block can be extracted easily.

The representation of a record with variable-length attributes typically has two parts: an initial part with fixed length attributes, followed by data for variable- length attributes. Fixed-length attributes, such as numeric values, dates, or fixed- length character strings are allocated as many bytes as required to store their value. Variable-length attributes, such as varchar types, are represented in the initial part of the record by a pair (_offset_, _length_), where _offset_ denotes where the data for that attribute begins within the record, and _length_ is the length in bytes of the variable-sized attribute. The values for these attributes are stored consecutively, after the initial fixed-length part of the record. Thus, the initial part of the record stores a fixed size of information about each attribute, whether it is fixed-length or variable-length.

An example of such a record representation is shown in Figure 10.8. The figure shows an _instructor_ record, whose first three attributes _ID_, _name_, and _dept name_ are variable-length strings, and whose fourth attribute _salary_ is a fixed-sized number. We assume that the offset and length values are stored in two bytes each, for a total of 4 bytes per attribute. The _salary_ attribute is assumed to be stored in 8 bytes, and each string takes as many bytes as it has characters.  

**456 Chapter 10 Storage and File Structure**

21, 5 26, 10 36, 10 65000 10101 Srinivasan Comp. Sci. Bytes 0 4 8 12 20 21 26 36 45

0000 Null bitmap (stored in 1 byte)

**Figure 10.8** Representation of variable-length record.

The figure also illustrates the use of a **null bitmap**, which indicates which attributes of the record have a null value. In this particular record, if the salary were null, the fourth bit of the bitmap would be set to 1, and the _salary_ value stored in bytes 12 through 19 would be ignored. Since the record has four attributes, the null bitmap for this record fits in 1 byte, although more bytes may be required with more attributes. In some representations, the null bitmap is stored at the beginning of the record, and for attributes that are null, no data (value, or offset/length) are stored at all. Such a representation would save some storage space, at the cost of extra work to extract attributes of the record. This representation is particularly useful for certain applications where records have a large number of fields, most of which are null.

We next address the problem of storing variable-length records in a block. The **slotted-page structure** is commonly used for organizing records within a block, and is shown in Figure 10.9.3 There is a header at the beginning of each block, containing the following information:

**1\.** The number of record entries in the header.

**2\.** The end of free space in the block.

**3\.** An array whose entries contain the location and size of each record.

The actual records are allocated _contiguously_ in the block, starting from the end of the block. The free space in the block is contiguous, between the final entry in the header array, and the first record. If a record is inserted, space is allocated for it at the end of free space, and an entry containing its size and location is added to the header.

If a record is deleted, the space that it occupies is freed, and its entry is set to _deleted_ (its size is set to −1, for example). Further, the records in the block before the deleted record are moved, so that the free space created by the deletion gets occupied, and all free space is again between the final entry in the header array and the first record. The end-of-free-space pointer in the header is appropriately updated as well. Records can be grown or shrunk by similar techniques, as long as there is space in the block. The cost of moving the records is not too high, since the size of a block is limited: typical values are around 4 to 8 kilobytes.

The slotted-page structure requires that there be no pointers that point directly to records. Instead, pointers must point to the entry in the header that contains the

3Here, “page” is synonymous with “block.”  

**10.6 Organization of Records in Files 457**

\# EntriesSize Location

Block Header Records

Free Space

End of Free Space

**Figure 10.9** Slotted-page structure.

actual location of the record. This level of indirection allows records to be moved to prevent fragmentation of space inside a block, while supporting indirect pointers to the record.

Databases often store data that can be much larger than a disk block. For instance, an image or an audio recording may be multiple megabytes in size, while a video object may be multiple gigabytes in size. Recall that SQL supports the types **blob** and **clob**, which store binary and character large objects.

Most relational databases restrict the size of a record to be no larger than the size of a block, to simplify buffer management and free-space management. Large objects are often stored in a special file (or collection of files) instead of being stored with the other (short) attributes of records in which they occur. A (logical) pointer to the object is then stored in the record containing the large object. Large objects are often represented using B+-tree file organizations, which we study in Section 11.4.1. B+-tree file organizations permit us to read an entire object, or specified byte ranges in the object, as well as to insert and delete parts of the object.

**10.6 Organization of Records in Files**

So far, we have studied how records are represented in a file structure. A relation is a set of records. Given a set of records, the next question is how to organize them in a file. Several of the possible ways of organizing records in files are:

• **Heap file organization**. Any record can be placed anywhere in the file where there is space for the record. There is no ordering of records. Typically, there is a single file for each relation.

• **Sequential file organization**. Records are stored in sequential order, accord- ing to the value of a “search key” of each record. Section 10.6.1 describes this organization.

• **Hashing file organization**. A hash function is computed on some attribute of each record. The result of the hash function specifies in which block of the  

**458 Chapter 10 Storage and File Structure**

10101 Srinivasan

45565 Katz 58583 Califieri 76543 Singh 76766 Crick 83821 Brandt 98345 Kim

12121 Wu 15151 Mozart 22222 Einstein 32343 El Said 33456 Gold

Comp. Sci.

Comp. Sci.

Comp. Sci. History Finance Biology

Elec. Eng.

Finance Music Physics History Physics

65000

75000 62000 80000 72000 92000 80000

90000 40000 95000 60000 87000

**Figure 10.10** Sequential file for _instructor_ records.

file the record should be placed. Chapter 11 describes this organization; it is closely related to the indexing structures described in that chapter.

Generally, a separate file is used to store the records of each relation. However, in a **multitable clustering file organization**, records of several different relations are stored in the same file; further, related records of the different relations are stored on the same block, so that one I/O operation fetches related records from all the relations. For example, records of the two relations can be considered to be related if they would match in a join of the two relations. Section 10.6.2 describes this organization.

**10.6.1 Sequential File Organization**

A **sequential file** is designed for efficient processing of records in sorted order based on some search key. A **search key** is any attribute or set of attributes; it need not be the primary key, or even a superkey. To permit fast retrieval of records in search-key order, we chain together records by pointers. The pointer in each record points to the next record in search-key order. Furthermore, to minimize the number of block accesses in sequential file processing, we store records physically in search-key order, or as close to search-key order as possible.

Figure 10.10 shows a sequential file of _instructor_ records taken from our uni- versity example. In that example, the records are stored in search-key order, using _ID_ as the search key.

The sequential file organization allows records to be read in sorted order; that can be useful for display purposes, as well as for certain query-processing algorithms that we shall study in Chapter 12.

It is difficult, however, to maintain physical sequential order as records are inserted and deleted, since it is costly to move many records as a result of a single  

**10.6 Organization of Records in Files 459**

10101

45565

76543 76766 83821 98345

12121 15151 22222 32343 33456

58583

Srinivasan

Katz

Singh Crick Brandt Kim

Wu Mozart Einstein El Said Gold

Califieri

Comp. Sci.

Comp. Sci.

Finance Biology Comp. Sci. Elec. Eng.

Finance Music Physics History Physics

History

65000

75000

80000 72000 92000 80000

90000 40000 95000 60000 87000

62000

48000MusicVerdi32222

**Figure 10.11** Sequential file after an insertion.

insertion or deletion. We can manage deletion by using pointer chains, as we saw previously. For insertion, we apply the following rules:

**1\.** Locate the record in the file that comes before the record to be inserted in search-key order.

**2\.** If there is a free record (that is, space left after a deletion) within the same block as this record, insert the new record there. Otherwise, insert the new record in an _overflow block_. In either case, adjust the pointers so as to chain together the records in search-key order.

Figure 10.11 shows the file of Figure 10.10 after the insertion of the record (32222, Verdi, Music, 48000). The structure in Figure 10.11 allows fast insertion of new records, but forces sequential file-processing applications to process records in an order that does not match the physical order of the records.

If relatively few records need to be stored in overflow blocks, this approach works well. Eventually, however, the correspondence between search-key order and physical order may be totally lost over a period of time, in which case se- quential processing will become much less efficient. At this point, the file should be **reorganized** so that it is once again physically in sequential order. Such reorga- nizations are costly, and must be done during times when the system load is low. The frequency with which reorganizations are needed depends on the frequency of insertion of new records. In the extreme case in which insertions rarely occur, it is possible always to keep the file in physically sorted order. In such a case, the pointer field in Figure 10.10 is not needed.  

**460 Chapter 10 Storage and File Structure**

**10.6.2 Multitable Clustering File Organization**

Many relational database systems store each relation in a separate file, so that they can take full advantage of the file system that the operating system provides. Usually, tuples of a relation can be represented as fixed-length records. Thus, relations can be mapped to a simple file structure. This simple implementation of a relational database system is well suited to low-cost database implementations as in, for example, embedded systems or portable devices. In such systems, the size of the database is small, so little is gained from a sophisticated file structure. Furthermore, in such environments, it is essential that the overall size of the object code for the database system be small. A simple file structure reduces the amount of code needed to implement the system.

This simple approach to relational database implementation becomes less satisfactory as the size of the database increases. We have seen that there are per- formance advantages to be gained from careful assignment of records to blocks, and from careful organization of the blocks themselves. Clearly, a more compli- cated file structure may be beneficial, even if we retain the strategy of storing each relation in a separate file.

However, many large-scale database systems do not rely directly on the un- derlying operating system for file management. Instead, one large operating- system file is allocated to the database system. The database system stores all relations in this one file, and manages the file itself.

Even if multiple relations are stored in a single file, by default most databases store records of only one relation in a given block. This simplifies data man- agement. However, in some cases it can be useful to store records of more than one relation in a single block. To see the advantage of storing records of multi- ple relations in one block, consider the following SQL query for the university database:

**select** _dept name_, _building_, _budget_, _ID_, _name_, _salary_ **from** _department_ **natural join** _instructor_;

This query computes a join of the _department_ and _instructor_ relations. Thus, for each tuple of _department_, the system must locate the _instructor_ tuples with the same value for _dept name_. Ideally, these records will be located with the help of _indices_, which we shall discuss in Chapter 11. Regardless of how these records are located, however, they need to be transferred from disk into main memory. In the worst case, each record will reside on a different block, forcing us to do one block read for each record required by the query.

_dept name building budget_

Comp. Sci. Taylor 100000 Physics Watson 70000

**Figure 10.12** The _department_ relation.  

**10.6 Organization of Records in Files 461**

_ID name dept name salary_

10101 Srinivasan Comp. Sci. 65000 33456 Gold Physics 87000 45565 Katz Comp. Sci. 75000 83821 Brandt Comp. Sci. 92000

**Figure 10.13** The _instructor_ relation.

As a concrete example, consider the _department_ and _instructor_ relations of Figures 10.12 and 10.13, respectively (for brevity, we include only a subset of the tuples of the relations we have used thus far). In Figure 10.14, we show a file structure designed for efficient execution of queries involving the natural join of _department_ and _instructor_. The _instructor_ tuples for each _ID_ are stored near the _department_ tuple for the corresponding _dept name_. This structure mixes together tuples of two relations, but allows for efficient processing of the join. When a tuple of the _department_ relation is read, the entire block containing that tuple is copied from disk into main memory. Since the corresponding _instructor_ tuples are stored on the disk near the _department_ tuple, the block containing the _department_ tuple contains tuples of the _instructor_ relation needed to process the query. If a department has so many instructors that the _instructor_ records do not fit in one block, the remaining records appear on nearby blocks.

A **multitable clustering file organization** is a file organization, such as that illustrated in Figure 10.14, that stores related records of two or more relations in each block. Such a file organization allows us to read records that would satisfy the join condition by using one block read. Thus, we are able to process this particular query more efficiently.

In the representation shown in Figure 10.14, the _dept name_ attribute is omitted from _instructor_ records since it can be inferred from the associated _department_ record; the attribute may be retained in some implementations, to simplify access to the attributes. We assume that each record contains the identifier of the relation to which it belongs, although this is not shown in Figure 10.14.

Our use of clustering of multiple tables into a single file has enhanced pro- cessing of a particular join (that of _department_ and _instructor_), but it results in slowing processing of other types of queries. For example,

Katz Srinivasan

Taylor

Brandt Watson Gold

100000

70000 87000

92000

75000 65000

Comp. Sci. 45564 10101 83821 Physics 33456

**Figure 10.14** Multitable clustering file structure.  

**462 Chapter 10 Storage and File Structure**

Katz Srinivasan

Taylor

Brandt Watson Gold

100000

70000 87000

92000

75000 65000

Comp. Sci. 45564 10101 83821 Physics 33456

**Figure 10.15** Multitable clustering file structure with pointer chains.

**select** \* **from** _department_;

requires more block accesses than it did in the scheme under which we stored each relation in a separate file, since each block now contains significantly fewer _department_ records. To locate efficiently all tuples of the _department_ relation in the structure of Figure 10.14, we can chain together all the records of that relation using pointers, as in Figure 10.15.

When multitable clustering is to be used depends on the types of queries that the database designer believes to be most frequent. Careful use of multitable clustering can produce significant performance gains in query processing.

**10.7 Data-Dictionary Storage**

So far, we have considered only the representation of the relations themselves. A relational database system needs to maintain data _about_ the relations, such as the schema of the relations. In general, such “data about data” is referred to as **metadata**.

Relational schemas and other metadata about relations are stored in a struc- ture called the **data dictionary** or **system catalog**. Among the types of information that the system must store are these:

• Names of the relations.

• Names of the attributes of each relation.

• Domains and lengths of attributes.

• Names of views defined on the database, and definitions of those views.

• Integrity constraints (for example, key constraints).

In addition, many systems keep the following data on users of the system:

• Names of authorized users.

• Authorization and accounting information about users.  

**10.7 Data-Dictionary Storage 463**

• Passwords or other information used to authenticate users.

Further, the database may store statistical and descriptive data about the relations, such as:

• Number of tuples in each relation.

• Method of storage for each relation (for example, clustered or nonclustered).

The data dictionary may also note the storage organization (sequential, hash, or heap) of relations, and the location where each relation is stored:

• If relations are stored in operating system files, the dictionary would note the names of the file (or files) containing each relation.

• If the database stores all relations in a single file, the dictionary may note the blocks containing records of each relation in a data structure such as a linked list.

In Chapter 11, in which we study indices, we shall see a need to store information about each index on each of the relations:

• Name of the index.

• Name of the relation being indexed.

• Attributes on which the index is defined.

• Type of index formed.

All this metadata information constitutes, in effect, a miniature database. Some database systems store such metadata by using special-purpose data struc- tures and code. It is generally preferable to store the data about the database as relations in the database itself. By using database relations to store system meta- data, we simplify the overall structure of the system and harness the full power of the database for fast access to system data.

The exact choice of how to represent system metadata by relations must be made by the system designers. One possible representation, with primary keys underlined, is shown in Figure 10.16. In this representation, the attribute _index attributes_ of the relation _Index metadata_ is assumed to contain a list of one or more

attributes, which can be represented by a character string such as “_dept name_, _building_”. The _Index metadata_ relation is thus not in first normal form; it can be normalized, but the above representation is likely to be more efficient to access. The data dictionary is often stored in a nonnormalized form to achieve fast access.

Whenever the database system needs to retrieve records from a relation, it must first consult the _Relation metadata_ relation to find the location and storage organization of the relation, and then fetch records using this information. How- ever, the storage organization and location of the _Relation metadata_ relation itself  

**464 Chapter 10 Storage and File Structure**

_Relation\_metadata relation\_name number\_of\_aributes storage\_organization location_

_Index\_metadata index\_name relation\_name index\_type index\_aributes_

_View\_metadata view\_name definition_

_Aribute\_metadata relation\_name aribute\_name domain\_type position length_

_User\_metadata user\_name encrypted\_password group_

**Figure 10.16** Relational schema representing system metadata.

must be recorded elsewhere (for example, in the database code itself, or in a fixed location in the database), since we need this information to find the contents of _Relation metadata_.

**10.8 Database Buffer**

A major goal of the database system is to minimize the number of block transfers between the disk and memory. One way to reduce the number of disk accesses is to keep as many blocks as possible in main memory. The goal is to maximize the chance that, when a block is accessed, it is already in main memory, and, thus, no disk access is required.

Since it is not possible to keep all blocks in main memory, we need to manage the allocation of the space available in main memory for the storage of blocks. The **buffer** is that part of main memory available for storage of copies of disk blocks. There is always a copy kept on disk of every block, but the copy on disk may be a version of the block older than the version in the buffer. The subsystem responsible for the allocation of buffer space is called the **buffer manager**.

**10.8.1 Buffer Manager**

Programs in a database system make requests (that is, calls) on the buffer manager when they need a block from disk. If the block is already in the buffer, the buffer manager passes the address of the block in main memory to the requester. If the  

**10.8 Database Buffer 465**

block is not in the buffer, the buffer manager first allocates space in the buffer for the block, throwing out some other block, if necessary, to make space for the new block. The thrown-out block is written back to disk only if it has been modified since the most recent time that it was written to the disk. Then, the buffer manager reads in the requested block from the disk to the buffer, and passes the address of the block in main memory to the requester. The internal actions of the buffer manager are transparent to the programs that issue disk-block requests.

If you are familiar with operating-system concepts, you will note that the buffer manager appears to be nothing more than a virtual-memory manager, like those found in most operating systems. One difference is that the size of the database might be larger than the hardware address space of a machine, so memory addresses are not sufficient to address all disk blocks. Further, to serve the database system well, the buffer manager must use techniques more sophisticated than typical virtual-memory management schemes:

• **Buffer replacement strategy**. When there is no room left in the buffer, a block must be removed from the buffer before a new one can be read in. Most operating systems use a **least recently used** (**LRU**) scheme, in which the block that was referenced least recently is written back to disk and is removed from the buffer. This simple approach can be improved on for database applications.

• **Pinned blocks**. For the database system to be able to recover from crashes (Chapter 16), it is necessary to restrict those times when a block may be written back to disk. For instance, most recovery systems require that a block should not be written to disk while an update on the block is in progress. A block that is not allowed to be written back to disk is said to be **pinned**. Although many operating systems do not support pinned blocks, such a feature is essential for a database system that is resilient to crashes.

• **Forced output of blocks**. There are situations in which it is necessary to write back the block to disk, even though the buffer space that it occupies is not needed. This write is called the **forced output** of a block. We shall see the reason for forced output in Chapter 16; briefly, main-memory contents and thus buffer contents are lost in a crash, whereas data on disk usually survive a crash.

**10.8.2 Buffer-Replacement Policies**

The goal of a replacement strategy for blocks in the buffer is to minimize accesses to the disk. For general-purpose programs, it is not possible to predict accurately which blocks will be referenced. Therefore, operating systems use the past pattern of block references as a predictor of future references. The assumption generally made is that blocks that have been referenced recently are likely to be referenced again. Therefore, if a block must be replaced, the least recently referenced block is replaced. This approach is called the **least recently used** (**LRU**) block-replacement scheme.  

**466 Chapter 10 Storage and File Structure**

**for each** tuple _i_ of _instructor_ **do for each** tuple _d_ of _department_ **do**

**if** _i_\[_dept name_\] = _d_\[_dept name_\] **then begin**

let _x_ be a tuple defined as follows: _x_\[_ID_\] := _i_\[_ID_\] _x_\[_dept name_\] := _i_\[_dept name_\] _x_\[_name_\] := _i_\[_name_\] _x_\[_salary_\] := _i_\[_salary_\] _x_\[_building_\] := _d_\[_building_\] _x_\[_budget_\] := _d_\[_budget_\] include tuple _x_ as part of result of _instructor  department_

**end end**

**end**

**Figure 10.17** Procedure for computing join.

LRU is an acceptable replacement scheme in operating systems. However, a database system is able to predict the pattern of future references more accurately than an operating system. A user request to the database system involves several steps. The database system is often able to determine in advance which blocks will be needed by looking at each of the steps required to perform the user- requested operation. Thus, unlike operating systems, which must rely on the past to predict the future, database systems may have information regarding at least the short-term future.

To illustrate how information about future block access allows us to improve the LRU strategy, consider the processing of the SQL query:

**select** \* **from** _instructor_ **natural join** _department_;

Assume that the strategy chosen to process this request is given by the pseu- docode program shown in Figure 10.17. (We shall study other, more efficient, strategies in Chapter 12.)

Assume that the two relations of this example are stored in separate files. In this example, we can see that, once a tuple of _instructor_ has been processed, that tuple is not needed again. Therefore, once processing of an entire block of _instructor_ tuples is completed, that block is no longer needed in main memory, even though it has been used recently. The buffer manager should be instructed to free the space occupied by an _instructor_ block as soon as the final tuple has been processed. This buffer-management strategy is called the **toss-immediate** strategy.

Now consider blocks containing _department_ tuples. We need to examine every block of _department_ tuples once for each tuple of the _instructor_ relation. When  

**10.8 Database Buffer 467**

processing of a _department_ block is completed, we know that that block will not be accessed again until all other _department_ blocks have been processed. Thus, the most recently used _department_ block will be the final block to be re-referenced, and the least recently used _department_ block is the block that will be referenced next. This assumption set is the exact opposite of the one that forms the basis for the LRU strategy. Indeed, the optimal strategy for block replacement for the above procedure is the **most recently used** (**MRU**) strategy. If a _department_ block must be removed from the buffer, the MRU strategy chooses the most recently used block (blocks are not eligible for replacement while they are being used).

For the MRU strategy to work correctly for our example, the system must pin the _department_ block currently being processed. After the final _department_ tuple has been processed, the block is unpinned, and it becomes the most recently used block.

In addition to using knowledge that the system may have about the request being processed, the buffer manager can use statistical information about the probability that a request will reference a particular relation. For example, the data dictionary that (as we will see in detail in Section 10.7) keeps track of the logical schema of the relations as well as their physical storage information is one of the most frequently accessed parts of the database. Thus, the buffer manager should try not to remove data-dictionary blocks from main memory, unless other factors dictate that it do so. In Chapter 11, we discuss indices for files. Since an index for a file may be accessed more frequently than the file itself, the buffer manager should, in general, not remove index blocks from main memory if alternatives are available.

The ideal database block-replacement strategy needs knowledge of the data- base operations—both those being performed and those that will be performed in the future. No single strategy is known that handles all the possible scenarios well. Indeed, a surprisingly large number of database systems use LRU, despite that strategy’s faults. The practice questions and exercises explore alternative strategies.

The strategy that the buffer manager uses for block replacement is influenced by factors other than the time at which the block will be referenced again. If the system is processing requests by several users concurrently, the concurrency- control subsystem (Chapter 15) may need to delay certain requests, to ensure preservation of database consistency. If the buffer manager is given informa- tion from the concurrency-control subsystem indicating which requests are being delayed, it can use this information to alter its block-replacement strategy. Specif- ically, blocks needed by active (nondelayed) requests can be retained in the buffer at the expense of blocks needed by the delayed requests.

The crash-recovery subsystem (Chapter 16) imposes stringent constraints on block replacement. If a block has been modified, the buffer manager is not allowed to write back the new version of the block in the buffer to disk, since that would destroy the old version. Instead, the block manager must seek permission from the crash-recovery subsystem before writing out a block. The crash-recovery subsystem may demand that certain other blocks be force-output before it grants permission to the buffer manager to output the block requested. In Chapter 16,  

**468 Chapter 10 Storage and File Structure**

we define precisely the interaction between the buffer manager and the crash- recovery subsystem.

**10.9 Summary**

• Several types of data storage exist in most computer systems. They are clas- sified by the speed with which they can access data, by their cost per unit of data to buy the memory, and by their reliability. Among the media available are cache, main memory, flash memory, magnetic disks, optical disks, and magnetic tapes.

• Two factors determine the reliability of storage media: whether a power failure or system crash causes data to be lost, and what the likelihood is of physical failure of the storage device.

• We can reduce the likelihood of physical failure by retaining multiple copies of data. For disks, we can use mirroring. Or we can use more sophisticated methods based on redundant arrays of independent disks (RAID). By striping data across disks, these methods offer high throughput rates on large accesses; by introducing redundancy across disks, they improve reliability greatly. Several different RAID organizations are possible, each with different cost, performance, and reliability characteristics. RAID level 1 (mirroring) and RAID level 5 are the most commonly used.

• We can organize a file logically as a sequence of records mapped onto disk blocks. One approach to mapping the database to files is to use several files, and to store records of only one fixed length in any given file. An alternative is to structure files so that they can accommodate multiple lengths for records. The slotted-page method is widely used to handle varying length records within a disk block.

• Since data are transferred between disk storage and main memory in units of a block, it is worthwhile to assign file records to blocks in such a way that a single block contains related records. If we can access several of the records we want with only one block access, we save disk accesses. Since disk accesses are usually the bottleneck in the performance of a database system, careful assignment of records to blocks can pay significant performance dividends.

• The data dictionary, also referred to as the system catalog, keeps track of metadata, that is data about data, such as relation names, attribute names and types, storage information, integrity constraints, and user information.

• One way to reduce the number of disk accesses is to keep as many blocks as possible in main memory. Since it is not possible to keep all blocks in main memory, we need to manage the allocation of the space available in main memory for the storage of blocks. The _buffer_ is that part of main memory available for storage of copies of disk blocks. The subsystem responsible for the allocation of buffer space is called the _buffer manager_.  

**Review Terms 469**

**Review Terms**

• Physical storage media

◦ Cache

◦ Main memory

◦ Flash memory

◦ Magnetic disk

◦ Optical storage

• Magnetic disk

◦ Platter

◦ Hard disks

◦ Floppy disks

◦ Tracks

◦ Sectors

◦ Read–write head

◦ Disk arm

◦ Cylinder

◦ Disk controller

◦ Checksums

◦ Remapping of bad sectors

• Performance measures of disks

◦ Access time

◦ Seek time

◦ Rotational latency

◦ Data-transfer rate

◦ Mean time to failure (MTTF)

• Disk block • Optimization of disk-block access

◦ Disk-arm scheduling

◦ Elevator algorithm

◦ File organization

◦ Defragmenting

◦ Nonvolatile write buffers

◦ Nonvolatile random-access memory (NVRAM)

◦ Log disk

• Redundant arrays of independent disks (RAID)

◦ Mirroring

◦ Data striping

◦ Bit-level striping

◦ Block-level striping

• RAID levels

◦ Level 0 (block striping, no redundancy)

◦ Level 1 (block striping, mirroring)

◦ Level 3 (bit striping, parity)

◦ Level 5 (block striping, distributed parity)

◦ Level 6 (block striping, P + Q re- dundancy)

• Rebuild performance • Software RAID

• Hardware RAID

• Hot swapping • Tertiary storage

◦ Optical disks

◦ Magnetic tapes

◦ Jukeboxes

• File • File organization

◦ File header  

**470 Chapter 10 Storage and File Structure**

◦ Free list

• Variable-length records

◦ Slotted-page structure

• Large objects • Heap file organization • Sequential file organization • Hashing file organization • Multitable clustering file organiza-

tion • Search key • Data dictionary

• System catalog • Buffer

◦ Buffer manager

◦ Pinned blocks

◦ Forced output of blocks

• Buffer-replacement policies

◦ Least recently used (LRU)

◦ Toss-immediate

◦ Most recently used (MRU)

**Practice Exercises**

**10.1** Consider the data and parity-block arrangement on four disks depicted in Figure 10.18. The _Bi_ s represent data blocks; the _Pi_ s represent parity blocks. Parity block _Pi_ is the parity block for data blocks _B_4_i_−3 to _B_4_i_ . What, if any, problem might this arrangement present?

**10.2** Flash storage:

a. How is the flash translation table, which is used to map logical page numbers to physical page numbers, created in memory?

b. Suppose you have a 64 gigabyte flash storage system, with a 4096 byte page size. How big would the flash translation table be, as- suming each page has a 32 bit address, and the table is stored as an array.

c. Suggest how to reduce the size of the translation table if very often long ranges of consecutive logical page numbers are mapped to consecutive physical page numbers.

Disk 1 Disk 2 Disk 3 Disk 4 _B1 P1 B8_…

_B2 B5 P2_…

_B3 B6 B9_…

_B4 B7 B10_…

**Figure 10.18** Data and parity block arrangement.  

**Practice Exercises 471**

**10.3** A power failure that occurs while a disk block is being written could result in the block being only partially written. Assume that partially written blocks can be detected. An atomic block write is one where either the disk block is fully written or nothing is written (i.e., there are no partial writes). Suggest schemes for getting the effect of atomic block writes with the following RAID schemes. Your schemes should involve work on recovery from failure.

a. RAID level 1 (mirroring)

b. RAID level 5 (block interleaved, distributed parity)

**10.4** Consider the deletion of record 5 from the file of Figure 10.6. Compare the relative merits of the following techniques for implementing the deletion:

a. Move record 6 to the space occupied by record 5, and move record 7 to the space occupied by record 6.

b. Move record 7 to the space occupied by record 5.

c. Mark record 5 as deleted, and move no records.

**10.5** Show the structure of the file of Figure 10.7 after each of the following steps:

a. Insert (24556, Turnamian, Finance, 98000).

b. Delete record 2.

c. Insert (34556, Thompson, Music, 67000).

**10.6** Consider the relations _section_ and _takes_. Give an example instance of these two relations, with three sections, each of which has five students. Give a file structure of these relations that uses multitable clustering.

**10.7** Consider the following bitmap technique for tracking free space in a file. For each block in the file, two bits are maintained in the bitmap. If the block is between 0 and 30 percent full the bits are 00, between 30 and 60 percent the bits are 01, between 60 and 90 percent the bits are 10, and above 90 percent the bits are 11. Such bitmaps can be kept in memory even for quite large files.

a. Describe how to keep the bitmap up to date on record insertions and deletions.

b. Outline the benefit of the bitmap technique over free lists in search- ing for free space and in updating free space information.

**10.8** It is important to be able to quickly find out if a block is present in the buffer, and if so where in the buffer it resides. Given that database buffer sizes are very large, what (in-memory) data structure would you use for the above task?  

**472 Chapter 10 Storage and File Structure**

**10.9** Give an example of a relational-algebra expression and a query-processing strategy in each of the following situations:

a. MRU is preferable to LRU.

b. LRU is preferable to MRU.

**Exercises**

**10.10** List the physical storage media available on the computers you use rou- tinely. Give the speed with which data can be accessed on each medium.

**10.11** How does the remapping of bad sectors by disk controllers affect data- retrieval rates?

**10.12** RAID systems typically allow you to replace failed disks without stopping access to the system. Thus, the data in the failed disk must be rebuilt and written to the replacement disk while the system is in operation. Which of the RAID levels yields the least amount of interference between the rebuild and ongoing disk accesses? Explain your answer.

**10.13** What is scrubbing, in the context of RAID systems, and why is scrubbing important?

**10.14** In the variable-length record representation, a null bitmap is used to indicate if an attribute has the null value.

a. For variable length fields, if the value is null, what would be stored in the offset and length fields?

b. In some applications, tuples have a very large number of attributes, most of which are null. Can you modify the record representation such that the only overhead for a null attribute is the single bit in the null bitmap.

**10.15** Explain why the allocation of records to blocks affects database-system performance significantly.

**10.16** If possible, determine the buffer-management strategy used by the operat- ing system running on your local computer system and what mechanisms it provides to control replacement of pages. Discuss how the control on replacement that it provides would be useful for the implementation of database systems.

**10.17** List two advantages and two disadvantages of each of the following strate- gies for storing a relational database:

a. Store each relation in one file.

b. Store multiple relations (perhaps even the entire database) in one file.  

**Bibliographical Notes 473**

**10.18** In the sequential file organization, why is an overflow _block_ used even if there is, at the moment, only one overflow record?

**10.19** Give a normalized version of the _Index metadata_ relation, and explain why using the normalized version would result in worse performance.

**10.20** If you have data that should not be lost on disk failure, and the data are write intensive, how would you store the data?

**10.21** In earlier generation disks the number of sectors per track was the same across all tracks. Current generation disks have more sectors per track on outer tracks, and fewer sectors per track on inner tracks (since they are shorter in length). What is the effect of such a change on each of the three main indicators of disk speed?

**10.22** Standard buffer managers assume each block is of the same size and costs the same to read. Consider a buffer manager that, instead of LRU, uses the rate of reference to objects, that is, how often an object has been accessed in the last _n_ seconds. Suppose we want to store in the buffer objects of varying sizes, and varying read costs (such as Web pages, whose read cost depends on the site from which they are fetched). Suggest how a buffer manager may choose which block to evict from the buffer.

**Bibliographical Notes**

Hennessy et al. \[2006\] is a popular textbook on computer architecture, which includes coverage of hardware aspects of translation look-aside buffers, caches, and memory-management units. Rosch \[2003\] presents an excellent overview of computer hardware, including extensive coverage of all types of storage technol- ogy such as magnetic disks, optical disks, tapes, and storage interfaces. Patterson \[2004\] provides a good discussion on how latency improvements have lagged behind bandwidth (transfer rate) improvements.

With the rapid increase in CPU speeds, cache memory located along with the CPU has become much faster than main memory. Although database systems do not control what data is kept in cache, there is an increasing motivation to organize data in memory and write programs in such a way that cache utilization is maximized. Work in this area includes Rao and Ross \[2000\], Ailamaki et al. \[2001\], Zhou and Ross \[2004\], Garcia and Korth \[2005\], and Cieslewicz et al. \[2009\].

The specifications of current-generation disk drives can be obtained from the Web sites of their manufacturers, such as Hitachi, LaCie, Iomega, Seagate, Maxtor, and Western Digital.

Discussions of redundant arrays of inexpensive disks (RAID) are presented by Patterson et al. \[1988\]. Chen et al. \[1994\] presents an excellent survey of RAID principles and implementation. Reed–Solomon codes are covered in Pless \[1998\].

Buffering data in mobile systems is discussed in Imielinski and Badrinath \[1994\], Imielinski and Korth \[1996\], and Chandrasekaran et al. \[2003\].  

**474 Chapter 10 Storage and File Structure**

The storage structure of specific database systems, such as IBM DB2, Oracle, Microsoft SQL Server, and PostgreSQL are documented in their respective system manuals.

Buffer management is discussed in most operating-system texts, including in Silberschatz et al. \[2008\]. Chou and Dewitt \[1985\] presents algorithms for buffer management in database systems, and describes a performance evaluation.  

**_C H A P T E R_11 Indexing and Hashing**

Many queries reference only a small proportion of the records in a file. For ex- ample, a query like “Find all instructors in the Physics department” or “Find the total number of credits earned by the student with _ID_ 22201” references only a fraction of the student records. It is inefficient for the system to read every tuple in the _instructor_ relation to check if the _dept name_ value is “Physics”. Likewise, it is inefficient to read the entire _student_ relation just to find the one tuple for the _ID_ “32556,”. Ideally, the system should be able to locate these records directly. To allow these forms of access, we design additional structures that we associate with files.

**11.1 Basic Concepts**

An index for a file in a database system works in much the same way as the index in this textbook. If we want to learn about a particular topic (specified by a word or a phrase) in this textbook, we can search for the topic in the index at the back of the book, find the pages where it occurs, and then read the pages to find the information for which we are looking. The words in the index are in sorted order, making it easy to find the word we want. Moreover, the index is much smaller than the book, further reducing the effort needed.

Database-system indices play the same role as book indices in libraries. For example, to retrieve a _student_ record given an _ID_, the database system would look up an index to find on which disk block the corresponding record resides, and then fetch the disk block, to get the appropriate _student_ record.

Keeping a sorted list of students’ _ID_ would not work well on very large databases with thousands of students, since the index would itself be very big; further, even though keeping the index sorted reduces the search time, finding a student can still be rather time-consuming. Instead, more sophisticated indexing techniques may be used. We shall discuss several of these techniques in this chapter.

There are two basic kinds of indices:

• **Ordered indices**. Based on a sorted ordering of the values.

**475**  

**476 Chapter 11 Indexing and Hashing**

• **Hash indices**. Based on a uniform distribution of values across a range of buckets. The bucket to which a value is assigned is determined by a function, called a _hash function_.

We shall consider several techniques for both ordered indexing and hashing. No one technique is the best. Rather, each technique is best suited to particular database applications. Each technique must be evaluated on the basis of these factors:

• **Access types**: The types of access that are supported efficiently. Access types can include finding records with a specified attribute value and finding records whose attribute values fall in a specified range.

• **Access time**: The time it takes to find a particular data item, or set of items, using the technique in question.

• **Insertion time**: The time it takes to insert a new data item. This value includes the time it takes to find the correct place to insert the new data item, as well as the time it takes to update the index structure.

• **Deletion time**: The time it takes to delete a data item. This value includes the time it takes to find the item to be deleted, as well as the time it takes to update the index structure.

• **Space overhead**: The additional space occupied by an index structure. Pro- vided that the amount of additional space is moderate, it is usually worth- while to sacrifice the space to achieve improved performance.

We often want to have more than one index for a file. For example, we may wish to search for a book by author, by subject, or by title.

An attribute or set of attributes used to look up records in a file is called a **search key**. Note that this definition of _key_ differs from that used in _primary key_, _candidate key_, and _superkey_. This duplicate meaning for _key_ is (unfortunately) well established in practice. Using our notion of a search key, we see that if there are several indices on a file, there are several search keys.

**11.2 Ordered Indices**

To gain fast random access to records in a file, we can use an index structure. Each index structure is associated with a particular search key. Just like the index of a book or a library catalog, an ordered index stores the values of the search keys in sorted order, and associates with each search key the records that contain it.

The records in the indexed file may themselves be stored in some sorted order, just as books in a library are stored according to some attribute such as the Dewey decimal number. A file may have several indices, on different search keys. If the file containing the records is sequentially ordered, a **clustering index** is an index whose search key also defines the sequential order of the file. Clustering indices  

**11.2 Ordered Indices 477**

10101 Srinivasan

45565 Katz 58583 Califieri 76543 Singh 76766 Crick 83821 Brandt 98345 Kim

12121 Wu 15151 Mozart 22222 Einstein 32343 El Said 33456 Gold

Comp. Sci.

Comp. Sci.

Comp. Sci. History Finance Biology

Elec. Eng.

Finance Music Physics History Physics

65000

75000 62000 80000 72000 92000 80000

90000 40000 95000 60000 87000

**Figure 11.1** Sequential file for _instructor_ records.

are also called **primary indices**; the term primary index may appear to denote an index on a primary key, but such indices can in fact be built on any search key. The search key of a clustering index is often the primary key, although that is not necessarily so. Indices whose search key specifies an order different from the sequential order of the file are called **nonclustering indices**, or **secondary** indices. The terms “clustered” and “nonclustered” are often used in place of “clustering” and “nonclustering.”

In Sections 11.2.1 through 11.2.3, we assume that all files are ordered sequen- tially on some search key. Such files, with a clustering index on the search key, are called **index-sequential files.** They represent one of the oldest index schemes used in database systems. They are designed for applications that require both sequential processing of the entire file and random access to individual records. In Section 11.2.4 we cover secondary indices.

Figure 11.1 shows a sequential file of _instructor_ records taken from our uni- versity example. In the example of Figure 11.1, the records are stored in sorted order of instructor _ID_, which is used as the search key.

**11.2.1 Dense and Sparse Indices**

An **index entry**, or **index record**, consists of a search-key value and pointers to one or more records with that value as their search-key value. The pointer to a record consists of the identifier of a disk block and an offset within the disk block to identify the record within the block.

There are two types of ordered indices that we can use:  

**478 Chapter 11 Indexing and Hashing**

10101 12121 15151 22222 32343 33456 45565 58583 76543 76766 83821 98345

10101 Srinivasan

45565 Katz 58583 Califieri 76543 Singh 76766 Crick 83821 Brandt 98345 Kim

12121 Wu 15151 Mozart 22222 Einstein 32343 El Said 33456 Gold

Comp. Sci.

Comp. Sci.

Comp. Sci. History Finance Biology

Elec. Eng.

Finance Music Physics History Physics

65000

75000 62000 80000 72000 92000 80000

90000 40000 95000 60000 87000

**Figure 11.2** Dense index.

• **Dense index**: In a dense index, an index entry appears for every search-key value in the file. In a dense clustering index, the index record contains the search-key value and a pointer to the first data record with that search-key value. The rest of the records with the same search-key value would be stored sequentially after the first record, since, because the index is a clustering one, records are sorted on the same search key.

In a dense nonclustering index, the index must store a list of pointers to all records with the same search-key value.

• **Sparse index**: In a sparse index, an index entry appears for only some of the search-key values. Sparse indices can be used only if the relation is stored in sorted order of the search key, that is, if the index is a clustering index. As is true in dense indices, each index entry contains a search-key value and a pointer to the first data record with that search-key value. To locate a record, we find the index entry with the largest search-key value that is less than or equal to the search-key value for which we are looking. We start at the record pointed to by that index entry, and follow the pointers in the file until we find the desired record.

Figures 11.2 and 11.3 show dense and sparse indices, respectively, for the _instructor_ file. Suppose that we are looking up the record of instructor with _ID_ “22222”. Using the dense index of Figure 11.2, we follow the pointer directly to the desired record. Since _ID_ is a primary key, there exists only one such record and the search is complete. If we are using the sparse index (Figure 11.3), we do not find an index entry for “22222”. Since the last entry (in numerical order) before “22222” is “10101”, we follow that pointer. We then read the _instructor_ file in sequential order until we find the desired record.  

**11.2 Ordered Indices 479**

Consider a (printed) dictionary. The header of each page lists the first word alphabetically on that page. The words at the top of each page of the book index together form a sparse index on the contents of the dictionary pages.

As another example, suppose that the search-key value is not not a primary key. Figure 11.4 shows a dense clustering index for the _instructor_ file with the search key being _dept name_. Observe that in this case the _instructor_ file is sorted on the search key _dept name_, instead of _ID_, otherwise the index on _dept name_ would be a nonclustering index. Suppose that we are looking up records for the History department. Using the dense index of Figure 11.4, we follow the pointer directly to the first History record. We process this record, and follow the pointer in that record to locate the next record in search-key (_dept name_) order. We continue processing records until we encounter a record for a department other than History.

As we have seen, it is generally faster to locate a record if we have a dense index rather than a sparse index. However, sparse indices have advantages over dense indices in that they require less space and they impose less maintenance overhead for insertions and deletions.

There is a trade-off that the system designer must make between access time and space overhead. Although the decision regarding this trade-off depends on the specific application, a good compromise is to have a sparse index with one index entry per block. The reason this design is a good trade-off is that the dominant cost in processing a database request is the time that it takes to bring a block from disk into main memory. Once we have brought in the block, the time to scan the entire block is negligible. Using this sparse index, we locate the block containing the record that we are seeking. Thus, unless the record is on an overflow block (see Section 10.6.1), we minimize block accesses while keeping the size of the index (and thus our space overhead) as small as possible.

10101 32343 76766

10101 Srinivasan

45565 Katz 58583 Califieri 76543 Singh 76766 Crick 83821 Brandt 98345 Kim

12121 Wu 15151 Mozart 22222 Einstein 32343 El Said 33456 Gold

Comp. Sci.

Comp. Sci.

Comp. Sci. History Finance Biology

Elec. Eng.

Finance Music Physics History Physics

65000

75000 62000 80000 72000 92000 80000

90000 40000 95000 60000 87000

**Figure 11.3** Sparse index.  

**480 Chapter 11 Indexing and Hashing**

Biology Comp. Sci. Elec. Eng. Finance History Music Physics

76766 Crick

76543 Singh 32343 El Said 58583 Califieri 15151 Mozart 22222 Einstein 33465 Gold

10101 Srinivasan 45565 Katz 83821 Brandt 98345 Kim 12121 Wu

Biology

Physics

Finance History History Music

Physics

Comp. Sci. Comp. Sci. Comp. Sci. Elec. Eng. Finance

72000

80000 60000 62000 40000 95000 87000

65000 75000 92000 80000 90000

**Figure 11.4** Dense index with search key _dept name_.

For the preceding technique to be fully general, we must consider the case where records for one search-key value occupy several blocks. It is easy to modify our scheme to handle this situation.

**11.2.2 Multilevel Indices**

Suppose we build a dense index on a relation with 1,000,000 tuples. Index entries are smaller than data records, so let us assume that 100 index entries fit on a 4 kilobyte block. Thus, our index occupies 10,000 blocks. If the relation instead had 100,000,000 tuples, the index would instead occupy 1,000,000 blocks, or 4 gigabytes of space. Such large indices are stored as sequential files on disk.

If an index is small enough to be kept entirely in main memory, the search time to find an entry is low. However, if the index is so large that not all of it can be kept in memory, index blocks must be fetched from disk when required. (Even if an index is smaller than the main memory of a computer, main memory is also required for a number of other tasks, so it may not be possible to keep the entire index in memory.) The search for an entry in the index then requires several disk-block reads.

Binary search can be used on the index file to locate an entry, but the search still has a large cost. If the index would occupy _b_ blocks, binary search requires as many as log2(_b_) blocks to be read. (_x_ denotes the least integer that is greater than or equal to _x_; that is, we round upward.) For a 10,000-block index, binary search requires 14 block reads. On a disk system where a block read takes on average 10 milliseconds, the index search will take 140 milliseconds. This may not seem much, but we would be able to carry out only seven index searches a second, whereas a more efficient search mechanism would let us carry out far more searches per second, as we shall see shortly. Note that, if overflow blocks have been used, binary search is not possible. In that case, a sequential search is typically used, and that requires _b_ block reads, which will take even longer. Thus, the process of searching a large index may be costly.  

**11.2 Ordered Indices 481**

…

… …

…

outer index

index block 0

index block 1

data block 0

data block 1

inner index

**Figure 11.5** Two-level sparse index.

To deal with this problem, we treat the index just as we would treat any other sequential file, and construct a sparse outer index on the original index, which we now call the inner index, as shown in Figure 11.5. Note that the index entries are always in sorted order, allowing the outer index to be sparse. To locate a record, we first use binary search on the outer index to find the record for the largest search-key value less than or equal to the one that we desire. The pointer points to a block of the inner index. We scan this block until we find the record that has the largest search-key value less than or equal to the one that we desire. The pointer in this record points to the block of the file that contains the record for which we are looking.

In our example, an inner index with 10,000 blocks would require 10,000 entries in the outer index, which would occupy just 100 blocks. If we assume that the outer index is already in main memory, we would read only one index block for a search using a multilevel index, rather than the 14 blocks we read with binary search. As a result, we can perform 14 times as many index searches per second.

If our file is extremely large, even the outer index may grow too large to fit in main memory. With a 100,000,000 tuple relation, the inner index would occupy  

**482 Chapter 11 Indexing and Hashing**

1,000,000 blocks, and the outer index occupies 10,000 blocks, or 40 megabytes. Since there are many demands on main memory, it may not be possible to reserve that much main memory just for this particular outer index. In such a case, we can create yet another level of index. Indeed, we can repeat this process as many times as necessary. Indices with two or more levels are called **multilevel** indices. Searching for records with a multilevel index requires significantly fewer I/O operations than does searching for records by binary search.1

Multilevel indices are closely related to tree structures, such as the binary trees used for in-memory indexing. We shall examine the relationship later, in Section 11.3.

**11.2.3 Index Update**

Regardless of what form of index is used, every index must be updated whenever a record is either inserted into or deleted from the file. Further, in case a record in the file is updated, any index whose search-key attribute is affected by the update must also be updated; for example, if the department of an instructor is changed, an index on the _dept name_ attribute of _instructor_ must be updated correspondingly. Such a record update can be modeled as a deletion of the old record, followed by an insertion of the new value of the record, which results in an index deletion followed by an index insertion. As a result we only need to consider insertion and deletion on an index, and do not need to consider updates explicitly.

We first describe algorithms for updating single-level indices.

• **Insertion**. First, the system performs a lookup using the search-key value that appears in the record to be inserted. The actions the system takes next depend on whether the index is dense or sparse:

◦ Dense indices: **1\.** If the search-key value does not appear in the index, the system inserts

an index entry with the search-key value in the index at the appropriate position.

**2\.** Otherwise the following actions are taken: a. If the index entry stores pointers to all records with the same search-

key value, the system adds a pointer to the new record in the index entry.

b. Otherwise, the index entry stores a pointer to only the first record with the search-key value. The system then places the record being inserted after the other records with the same search-key values.

◦ Sparse indices: We assume that the index stores an entry for each block. If the system creates a new block, it inserts the first search-key value (in

1In the early days of disk-based indices, each level of the index corresponded to a unit of physical storage. Thus, we may have indices at the track, cylinder, and disk levels. Such a hierarchy does not make sense today since disk subsystems hide the physical details of disk storage, and the number of disks and platters per disk is very small compared to the number of cylinders or bytes per track.  

**11.2 Ordered Indices 483**

search-key order) appearing in the new block into the index. On the other hand, if the new record has the least search-key value in its block, the system updates the index entry pointing to the block; if not, the system makes no change to the index.

• **Deletion**. To delete a record, the system first looks up the record to be deleted. The actions the system takes next depend on whether the index is dense or sparse:

◦ Dense indices: **1\.** If the deleted record was the only record with its particular search-key

value, then the system deletes the corresponding index entry from the index.

**2\.** Otherwise the following actions are taken: a. If the index entry stores pointers to all records with the same search-

key value, the system deletes the pointer to the deleted record from the index entry.

b. Otherwise, the index entry stores a pointer to only the first record with the search-key value. In this case, if the deleted record was the first record with the search-key value, the system updates the index entry to point to the next record.

◦ Sparse indices: **1\.** If the index does not contain an index entry with the search-key value

of the deleted record, nothing needs to be done to the index. **2\.** Otherwise the system takes the following actions:

a. If the deleted record was the only record with its search key, the system replaces the corresponding index record with an index rec- ord for the next search-key value (in search-key order). If the next search-key value already has an index entry, the entry is deleted instead of being replaced.

b. Otherwise, if the index entry for the search-key value points to the record being deleted, the system updates the index entry to point to the next record with the same search-key value.

Insertion and deletion algorithms for multilevel indices are a simple extension of the scheme just described. On deletion or insertion, the system updates the lowest-level index as described. As far as the second level is concerned, the lowest-level index is merely a file containing records—thus, if there is any change in the lowest-level index, the system updates the second-level index as described. The same technique applies to further levels of the index, if there are any.

**11.2.4 Secondary Indices**

Secondary indices must be dense, with an index entry for every search-key value, and a pointer to every record in the file. A clustering index may be sparse, storing  

**484 Chapter 11 Indexing and Hashing**

only some of the search-key values, since it is always possible to find records with intermediate search-key values by a sequential access to a part of the file, as described earlier. If a secondary index stores only some of the search-key values, records with intermediate search-key values may be anywhere in the file and, in general, we cannot find them without searching the entire file.

A secondary index on a candidate key looks just like a dense clustering index, except that the records pointed to by successive values in the index are not stored sequentially. In general, however, secondary indices may have a different structure from clustering indices. If the search key of a clustering index is not a candidate key, it suffices if the index points to the first record with a particular value for the search key, since the other records can be fetched by a sequential scan of the file.

In contrast, if the search key of a secondary index is not a candidate key, it is not enough to point to just the first record with each search-key value. The remaining records with the same search-key value could be anywhere in the file, since the records are ordered by the search key of the clustering index, rather than by the search key of the secondary index. Therefore, a secondary index must contain pointers to all the records.

We can use an extra level of indirection to implement secondary indices on search keys that are not candidate keys. The pointers in such a secondary index do not point directly to the file. Instead, each points to a bucket that contains pointers to the file. Figure 11.6 shows the structure of a secondary index that uses an extra level of indirection on the _instructor_ file, on the search key _salary_.

A sequential scan in clustering index order is efficient because records in the file are stored physically in the same order as the index order. However, we cannot (except in rare special cases) store a file physically ordered by both the search key of the clustering index and the search key of a secondary index.

40000 60000 62000 65000 72000 75000 80000 87000 90000 92000 95000

10101 Srinivasan Comp. Sci. 65000 12121 Wu Finance 90000 15151 Mozart Music 40000 22222 Einstein Physics 95000 32343 El Said History 60000 33456 Gold Physics 87000 45565 Katz Comp. Sci. 75000 58583 Califieri History 62000 76543 Singh Finance 80000 76766 Crick Biology 72000 83821 Brandt Comp. Sci. 92000 98345 Kim Elec. Eng. 80000

**Figure 11.6** Secondary index on _instructor_ file, on noncandidate key _salary_.  

**11.3 B+-Tree Index Files 485**

**AUTOMATIC CREATION OF INDICES**

If a relation is declared to have a primary key, most database implementations automatically create an index on the primary key. Whenever a tuple is inserted into the relation, the index can be used to check that the primary key constraint is not violated (that is, there are no duplicates on the primary key value). Without the index on the primary key, whenever a tuple is inserted, the entire relation would have to be read to ensure that the primary-key constraint is satisfied.

Because secondary-key order and physical-key order differ, if we attempt to scan the file sequentially in secondary-key order, the reading of each record is likely to require the reading of a new block from disk, which is very slow.

The procedure described earlier for deletion and insertion can also be applied to secondary indices; the actions taken are those described for dense indices storing a pointer to every record in the file. If a file has multiple indices, whenever the file is modified, _every_ index must be updated.

Secondary indices improve the performance of queries that use keys other than the search key of the clustering index. However, they impose a significant overhead on modification of the database. The designer of a database decides which secondary indices are desirable on the basis of an estimate of the relative frequency of queries and modifications.

**11.2.5 Indices on Multiple Keys**

Although the examples we have seen so far have had a single attribute in a search key, in general a search key can have more than one attribute. A search key containing more than one attribute is referred to as a **composite search key**. The structure of the index is the same as that of any other index, the only difference being that the search key is not a single attribute, but rather is a list of attributes. The search key can be represented as a tuple of values, of the form (_a_1_, . . . , an_), where the indexed attributes are _A_1_, . . . , An_. The ordering of search-key values is the _lexicographic ordering_. For example, for the case of two attribute search keys, (_a_1_, a_2) _<_ (_b_1_, b_2) if either _a_1 _< b_1 or _a_1 = _b_1 and _a_2 _< b_2\. Lexicographic ordering is basically the same as alphabetic ordering of words.

As an example, consider an index on the _takes_ relation, on the composite search key (_course id_, _semester_, _year_). Such an index would be useful to find all students who have registered for a particular course in a particular semester/year. An ordered index on a composite key can also be used to answer several other kinds of queries efficiently, as we shall see later in Section 11.5.2.

**11.3 B+-Tree Index Files**

The main disadvantage of the index-sequential file organization is that perfor- mance degrades as the file grows, both for index lookups and for sequential scans  

**486 Chapter 11 Indexing and Hashing**

through the data. Although this degradation can be remedied by reorganization of the file, frequent reorganizations are undesirable.

The **B**+**\-tree** index structure is the most widely used of several index structures that maintain their efficiency despite insertion and deletion of data. A B+-tree index takes the form of a **balanced tree** in which every path from the root of the tree to a leaf of the tree is of the same length. Each nonleaf node in the tree has between _n/_2 and _n_ children, where _n_ is fixed for a particular tree.

We shall see that the B+-tree structure imposes performance overhead on in- sertion and deletion, and adds space overhead. The overhead is acceptable even for frequently modified files, since the cost of file reorganization is avoided. Fur- thermore, since nodes may be as much as half empty (if they have the minimum number of children), there is some wasted space. This space overhead, too, is acceptable given the performance benefits of the B+-tree structure.

**11.3.1 Structure of a B+-Tree**

A B+-tree index is a multilevel index, but it has a structure that differs from that of the multilevel index-sequential file. Figure 11.7 shows a typical node of a B+- tree. It contains up to _n_ − 1 search-key values _K_1_, K_2_, . . . , Kn_ − 1, and _n_ pointers _P_1_, P_2_, . . . , Pn_. The search-key values within a node are kept in sorted order; thus, if _i < j_ , then _Ki < K j_ .

We consider first the structure of the **leaf nodes**. For _i_ \= 1_,_ 2_, . . . , n_−1, pointer _Pi_ points to a file record with search-key value _Ki_ . Pointer _Pn_ has a special purpose that we shall discuss shortly.

Figure 11.8 shows one leaf node of a B+-tree for the _instructor_ file, in which we have chosen _n_ to be 4, and the search key is _name_.

Now that we have seen the structure of a leaf node, let us consider how search-key values are assigned to particular nodes. Each leaf can hold up to _n_ − 1 values. We allow leaf nodes to contain as few as (_n_ − 1)_/_2 values. With _n_ \= 4 in our example B+-tree, each leaf must contain at least 2 values, and at most 3 values.

The ranges of values in each leaf do not overlap, except if there are duplicate search-key values, in which case a value may be present in more than one leaf. Specifically, if _Li_ and _L j_ are leaf nodes and _i < j_ , then every search-key value in _Li_ is less than or equal to every search-key value in _L j_ . If the B+-tree index is used as a dense index (as is usually the case) every search-key value must appear in some leaf node.

Now we can explain the use of the pointer _Pn_. Since there is a linear order on the leaves based on the search-key values that they contain, we use _Pn_ to chain

_P_1 _K_1 _P_2 _Pn-_1 _Kn-_1 _Pn_…

**Figure 11.7** Typical node of a B+-tree.  

**11.3 B+-Tree Index Files 487**

leaf node Pointer to next leaf node

_instructor file_

Brandt

Srinivasan

Califieri Crick

Comp. Sci. 65000 Wu Finance 90000 Mozart Music 40000 Einstein Physics 95000 El Said History 80000 Gold Physics 87000 Katz Comp. Sci. 75000 Califieri History 60000 Singh Finance 80000 Crick Biology 72000 Brandt Comp. Sci. 92000

15151

10101 12121

22222 32343 33456 45565 58583 76543 76766 83821 98345 Kim Elec. Eng. 80000

**Figure 11.8** A leaf node for _instructor_ B+-tree index (_n_ \= 4).

together the leaf nodes in search-key order. This ordering allows for efficient sequential processing of the file.

The **nonleaf nodes** of the B+-tree form a multilevel (sparse) index on the leaf nodes. The structure of nonleaf nodes is the same as that for leaf nodes, except that all pointers are pointers to tree nodes. A nonleaf node may hold up to _n_ pointers, and _must_ hold at least _n/_2 pointers. The number of pointers in a node is called the _fanout_ of the node. Nonleaf nodes are also referred to as **internal nodes**.

Let us consider a node containing _m_ pointers (_m_ ≤ _n_). For _i_ \= 2_,_ 3_, . . . , m_ − 1, pointer _Pi_ points to the subtree that contains search-key values less than _Ki_ and greater than or equal to _Ki_ − 1. Pointer _Pm_ points to the part of the subtree that contains those key values greater than or equal to _Km_ − 1, and pointer _P_1 points to the part of the subtree that contains those search-key values less than _K_1.

Unlike other nonleaf nodes, the root node can hold fewer than _n/_2 pointers; however, it must hold at least two pointers, unless the tree consists of only one node. It is always possible to construct a B+-tree, for any _n_, that satisfies the preceding requirements.

Figure 11.9 shows a complete B+-tree for the _instructor_ file (with _n_ \= 4). We have shown instructor names abbreviated to 3 characters in order to depict the tree clearly; in reality, the tree nodes would contain the full names. We have also omitted null pointers for simplicity; any pointer field in the figure that does not have an arrow is understood to have a null value.

Figure 11.10 shows another B+-tree for the _instructor_ file, this time with _n_ \= 6. As before, we have abbreviated instructor names only for clarity of presentation.  

**488 Chapter 11 Indexing and Hashing**

Gold Katz Kim Mozart Singh Srinivasan Wu

Internal nodes

Root node

Leaf nodes

Einstein

Einstein El Said

Gold

Mozart

Srinivasan

Srinivasan Comp. Sci. 65000 Wu Finance 90000 Mozart Music 40000 Einstein Physics 95000 El Said History 80000 Gold Physics 87000 Katz Comp. Sci. 75000 Califieri History 60000 Singh Finance 80000 Crick Biology 72000 Brandt Comp. Sci. 92000

15151

10101

Brandt Califieri Crick

12121

22222 32343 33456 45565 58583 76543 76766 83821 98345 Kim Elec. Eng. 80000

**Figure 11.9** B+-tree for _instructor_ file (_n_ \= 4).

Observe that the height of this tree is less than that of the previous tree, which had _n_ \= 4.

These examples of B+-trees are all balanced. That is, the length of every path from the root to a leaf node is the same. This property is a requirement for a B+- tree. Indeed, the “B” in B+-tree stands for “balanced.” It is the balance property of B+-trees that ensures good performance for lookup, insertion, and deletion.

**11.3.2 Queries on B+-Trees**

Let us consider how we process queries on a B+-tree. Suppose that we wish to find records with a search-key value of _V_. Figure 11.11 presents pseudocode for a function find() to carry out this task.

Intuitively, the function starts at the root of the tree, and traverses the tree down until it reaches a leaf node that would contain the specified value if it exists in the tree. Specifically, starting with the root as the current node, the function repeats the following steps until a leaf node is reached. First, the current node is examined, looking for the smallest _i_ such that search-key value _Ki_ is greater

Brandt CrickCalifieri Einstein El Said Gold Katz Kim Mozart Singh Srinivasan Wu

El Said Mozart

**Figure 11.10** B+-tree for _instructor_ file with _n_ \= 6.  

**11.3 B+-Tree Index Files 489**

**function** find(value _V_) /\* Returns leaf node _C_ and index _i_ such that _C.Pi_ points to first record \* with search key value _V_ \*/

Set _C_ \= root node **while** (_C_ is not a leaf node) **begin**

Let _i_ \= smallest number such that _V_ ≤ _C.Ki_ **if** there is no such number _i_ **then begin**

Let _Pm_ \= last non-null pointer in the node Set _C_ \= _C.Pm_

**end else if** (_V_ \= _C.Ki_ )

**then** Set _C_ \= _C.Pi_+1 **else** _C_ \= _C.Pi_ /\* _V < C.Ki_ \*/

**end** /\* _C_ is a leaf node \*/ Let _i_ be the least value such that _Ki_ \= _V_ **if** there is such a value _i_

**then** return (_C, i_) **else** return null ; /\* No record with key value _V_ exists\*/

**procedure** printAll(_value V_) /\* prints all records with search key value _V_ \*/

Set done = false; Set (_L , i_) = find(V); **if** ((_L , i_) is null) **return repeat**

**repeat** Print record pointed to by _L .Pi_ Set _i_ \= _i_ \+ 1

**until** (_i >_ number of keys in _L_ **or** _L .Ki > V_) **if** (_i >_ number of keys in _L_)

**then** _L_ \= _L .Pn_ **else** Set done = true;

**until** (done **or** _L_ is null)

**Figure 11.11** Querying a B+-tree.

than or equal to _V_. Suppose such a value is found; then, if _Ki_ is equal to _V_, the current node is set to the node pointed to by _Pi_+1, otherwise _Ki > V_, and the current node is set to the node pointed to by _Pi_ . If no such value _Ki_ is found, then clearly _V > Km_−1, where _Pm_ is the last nonnull pointer in the node. In this case the current node is set to that pointed to by _Pm_. The above procedure is repeated, traversing down the tree until a leaf node is reached.

At the leaf node, if there is a search-key value equal to _V_, let _Ki_ be the first such value; pointer _Pi_ directs us to a record with search-key value _Ki_ . The function  

**490 Chapter 11 Indexing and Hashing**

then returns the leaf node _L_ and the index _i_ . If no search-key with value _V_ is found in the leaf node, no record with key value _V_ exists in the relation, and function find returns null, to indicate failure.

If there is at most one record with a search key value _V_ (for example, if the index is on a primary key) the procedure that calls the find function simply uses the pointer _L .Pi_ to retrieve the record and is done. However, in case there may be more than one matching record, the remaining records also need to be fetched.

Procedure printAll shown in Figure 11.11 shows how to fetch all records with a specified search key _V_. The procedure first steps through the remaining keys in the node _L_, to find other records with search-key value _V_. If node _L_ contains at least one search-key value greater than _V_, then there are no more records matching _V_. Otherwise, the next leaf, pointed to by _Pn_ may contain further entries for _V_. The node pointed to by _Pn_ must then be searched to find further records with search-key value _V_. If the highest search-key value in the node pointed to by _Pn_ is also _V_, further leaves may have to be traversed, in order to find all matching records. The **repeat** loop in printAll carries out the task of traversing leaf nodes until all matching records have been found.

A real implementation would provide a version offind supporting an iterator interface similar to that provided by the JDBC ResultSet, which we saw in Section 5.1.1. Such an iterator interface would provide a method next(), which can be called repeatedly to fetch successive records with the specified search-key. The next() method would step through the entries at the leaf level, in a manner similar to printAll, but each call takes only one step, and records where it left off, so that successive calls next step through successive records. We omit details for simplicity, and leave the pseudocode for the iterator interface as an exercise for the interested reader.

B+-trees can also be used to find all records with search key values in a specified range (_L , U_). For example, with a B+-tree on attribute _salary_ of _instruc- tor_, we can find all _instructor_ records with salary in a specified range such as (50000_,_ 100000) (in other words, all salaries between 50000 and 100000). Such queries are called **range queries**. To execute such queries, we can create a proce- dure printRange(_L , U_), whose body is the same as printAll except for these dif- ferences: printRange calls find(_L_), instead of find(_V_), and then steps through records as in procedure printAll, but with the stopping condition being that _L .Ki > U_, instead of _L .Ki > V_.

In processing a query, we traverse a path in the tree from the root to some leaf node. If there are _N_ records in the file, the path is no longer than log_n/_2(_N_).

In practice, only a few nodes need to be accessed. Typically, a node is made to be the same size as a disk block, which is typically 4 kilobytes. With a search-key size of 12 bytes, and a disk-pointer size of 8 bytes, _n_ is around 200. Even with a more conservative estimate of 32 bytes for the search-key size, _n_ is around 100. With _n_ \= 100, if we have 1 million search-key values in the file, a lookup requires only log50(1_,_000_,_000) = 4 nodes to be accessed. Thus, at most four blocks need to be read from disk for the lookup. The root node of the tree is usually heavily accessed and is likely to be in the buffer, so typically only three or fewer blocks need to be read from disk.  

**11.3 B+-Tree Index Files 491**

An important difference between B+-tree structures and in-memory tree struc- tures, such as binary trees, is the size of a node, and as a result, the height of the tree. In a binary tree, each node is small, and has at most two pointers. In a B+-tree, each node is large—typically a disk block—and a node can have a large number of pointers. Thus, B+-trees tend to be fat and short, unlike thin and tall binary trees. In a balanced binary tree, the path for a lookup can be of length log2(_N_), where _N_ is the number of records in the file being indexed. With _N_ \= 1_,_000_,_000 as in the previous example, a balanced binary tree requires around 20 node accesses. If each node were on a different disk block, 20 block reads would be required to process a lookup, in contrast to the four block reads for the B+-tree. The difference is significant, since each block read could require a disk arm seek, and a block read together with the disk arm seek takes about 10 milliseconds on a typical disk.

**11.3.3 Updates on B+-Trees**

When a record is inserted into, or deleted from a relation, indices on the relation must be updated correspondingly. Recall that updates to a record can be modeled as a deletion of the old record followed by insertion of the updated record. Hence we only consider the case of insertion and deletion.

Insertion and deletion are more complicated than lookup, since it may be necessary to **split** a node that becomes too large as the result of an insertion, or to **coalesce** nodes (that is, combine nodes) if a node becomes too small (fewer than _n/_2 pointers). Furthermore, when a node is split or a pair of nodes is combined, we must ensure that balance is preserved. To introduce the idea behind insertion and deletion in a B+-tree, we shall assume temporarily that nodes never become too large or too small. Under this assumption, insertion and deletion are performed as defined next.

• **Insertion**. Using the same technique as for lookup from the find() function (Figure 11.11), we first find the leaf node in which the search-key value would appear. We then insert an entry (that is, a search-key value and record pointer pair) in the leaf node, positioning it such that the search keys are still in order.

• **Deletion**. Using the same technique as for lookup, we find the leaf node containing the entry to be deleted, by performing a lookup on the search-key value of the deleted record; if there are multiple entries with the same search- key value, we search across all entries with the same search-key value until we find the entry that points to the record being deleted. We then remove the entry from the leaf node. All entries in the leaf node that are to the right of the deleted entry are shifted left by one position, so that there are no gaps in the entries after the entry is deleted.

We now consider the general case of insertion and deletion, dealing with node splitting and node coalescing.

.  

**492 Chapter 11 Indexing and Hashing**

Adams Califieri CrickBrandt

**Figure 11.12** Split of leaf node on insertion of “Adams”

**11.3.3.1 Insertion**

We now consider an example of insertion in which a node must be split. Assume that a record is inserted on the _instructor_ relation, with the _name_ value being Adams. We then need to insert an entry for “Adams” into the B+-tree of Figure 11.9. Using the algorithm for lookup, we find that “Adams” should appear in the leaf node containing “Brandt”, “Califieri”, and “Crick.” There is no room in this leaf to insert the search-key value “Adams.” Therefore, the node is _split_ into two nodes. Figure 11.12 shows the two leaf nodes that result from the split of the leaf node on inserting “Adams”. The search-key values “Adams” and “Brandt” are in one leaf, and “Califieri” and “Crick” are in the other. In general, we take the _n_ search-key values (the _n_ − 1 values in the leaf node plus the value being inserted), and put the first _n/_2 in the existing node and the remaining values in a newly created node.

Having split a leaf node, we must insert the new leaf node into the B+-tree structure. In our example, the new node has “Califieri” as its smallest search-key value. We need to insert an entry with this search-key value, and a pointer to the new node, into the parent of the leaf node that was split. The B+-tree of Figure 11.13 shows the result of the insertion. It was possible to perform this insertion with no further node split, because there was room in the parent node for the new entry. If there were no room, the parent would have had to be split, requiring an entry to be added to its parent. In the worst case, all nodes along the path to the root must be split. If the root itself is split, the entire tree becomes deeper.

Splitting of a nonleaf node is a little different from splitting of a leaf node. Figure 11.14 shows the result of inserting a record with search key “Lamport” into the tree shown in Figure 11.13. The leaf node in which “Lamport” is to be inserted already has entries “Gold”, “Katz”, and “Kim”, and as a result the leaf node has to be split. The new right-hand-side node resulting from the split contains the search-key values “Kim” and “Lamport”. An entry (Kim, _n_1) must then be added

Adams Brandt Einstein El Said Gold Katz Kim Mozart Singh Srinivasan Wu

Gold Srinivasan

Mozart

EinsteinCalifieri

CrickCalifieri

**Figure 11.13** Insertion of “Adams” into the B+-tree of Figure 11.9.  

**11.3 B+-Tree Index Files 493**

Srinivasan

Gold

Califieri Einstein

Mozart

Kim

Adams Brandt Einstein El Said Gold Katz Kim Lamport Mozart Singh Srinivasan WuCrickCalifieri

**Figure 11.14** Insertion of “Lamport” into the B+-tree of Figure 11.13.

to the parent node, where _n_1 is a pointer to the new node, However, there is no space in the parent node to add a new entry, and the parent node has to be split. To do so, the parent node is conceptually expanded temporarily, the entry added, and the overfull node is then immediately split.

When an overfull nonleaf node is split, the child pointers are divided among the original and the newly created nodes; in our example, the original node is left with the first three pointers, and the newly created node to the right gets the remaining two pointers. The search key values are, however, handled a little differently. The search key values that lie between the pointers moved to the right node (in our example, the value “Kim”) are moved along with the pointers, while those that lie between the pointers that stay on the left (in our example, “Califieri” and “Einstein”) remain undisturbed.

However, the search key value that lies between the pointers that stay on the left, and the pointers that move to the right node is treated differently. In our example, the search key value “Gold” lies between the three pointers that went to the left node, and the two pointers that went to the right node. The value “Gold” is not added to either of the split nodes. Instead, an entry (Gold, _n_2) is added to the parent node, where _n_2 is a pointer to the newly created node that resulted from the split. In this case, the parent node is the root, and it has enough space for the new entry.

The general technique for insertion into a B+-tree is to determine the leaf node _l_ into which insertion must occur. If a split results, insert the new node into the parent of node _l_. If this insertion causes a split, proceed recursively up the tree until either an insertion does not cause a split or a new root is created.

Figure 11.15 outlines the insertion algorithm in pseudocode. The procedure insert inserts a key-value pointer pair into the index, using two subsidiary procedures insert in leaf and insert in parent. In the pseudocode, _L , N, P_ and _T_ denote pointers to nodes, with _L_ being used to denote a leaf node. _L .Ki_ and _L .Pi_ denote the _i_th value and the _i_th pointer in node _L_, respectively; _T.Ki_ and _T.Pi_ are used similarly. The pseudocode also makes use of the function parent(_N_) to find the parent of a node _N_. We can compute a list of nodes in the path from the root to the leaf while initially finding the leaf node, and can use it later to find the parent of any node in the path efficiently.

The procedure insert in parent takes as parameters _N, K_ ′_, N_′, where node _N_ was split into _N_ and _N_′, with _K_ ′ being the least value in _N_′. The procedure  

**494 Chapter 11 Indexing and Hashing**

**procedure** insert(_value K_ , _pointer P_) **if** (tree is empty) create an empty leaf node _L_ , which is also the root **else** Find the leaf node _L_ that should contain key value _K_ **if** (_L_ has less than _n_ − 1 key values)

**then** insert in leaf (_L , K , P_) **else begin** /\* _L_ has _n_ − 1 key values already, split it \*/

Create node _L_ ′ Copy _L .P_1 _. . . L .Kn_−1 to a block of memory _T_ that can

hold _n_ (pointer, key-value) pairs insert in leaf (_T, K , P_) Set _L_ ′_.Pn_ \= _L .Pn_; Set _L .Pn_ \= _L_ ′ Erase _L .P_1 through _L .Kn_−1 from _L_ Copy _T.P_1 through _T.K__n/_2 from _T_ into _L_ starting at _L .P_1 Copy _T.P__n/_2+1 through _T.Kn_ from _T_ into _L_ ′ starting at _L_ ′_.P_1 Let _K_ ′ be the smallest key-value in _L_ ′ insert in parent(_L_ , _K_ ′, _L_ ′)

**end**

**procedure** insert in leaf (_node L_, _value K_ , _pointer P_) **if** (_K < L .K_1)

**then** insert _P, K_ into _L_ just before _L .P_1 **else begin**

Let _Ki_ be the highest value in _L_ that is less than _K_ Insert _P, K_ into _L_ just after _T.Ki_

**end**

**procedure** insert in parent(_node N_, _value K_ ′, _node N_′) **if** (_N_ is the root of the tree)

**then begin** Create a new node _R_ containing _N, K_ ′_, N_′ /\* _N_ and _N_′ are pointers \*/ Make _R_ the root of the tree **return**

**end** Let _P_ \= _parent_ (_N_) **if** (_P_ has less than _n_ pointers)

**then** insert (_K_ ′_, N_′) in _P_ just after _N_ **else begin** /\* Split _P_ \*/

Copy _P_ to a block of memory _T_ that can hold _P_ and (_K_ ′_, N_′) Insert (_K_ ′_, N_′) into _T_ just after _N_ Erase all entries from _P_ ; Create node _P_ ′ Copy _T.P_1 _. . . T.P__n/_2 into _P_ Let _K_ ′′ = _T.K__n/_2 Copy _T.P__n/_2+1 _. . . T.Pn_+1 into _P_ ′ insert in parent(_P_ , _K_ ′′, _P_ ′)

**end**

**Figure 11.15** Insertion of entry in a B+-tree.  

**11.3 B+-Tree Index Files 495**

Adams Brandt Califieri Crick Einstein El Said Gold Katz Kim Mozart Singh Wu

Califieri

Gold

MozartEinstein

**Figure 11.16** Deletion of “Srinivasan” from the B+-tree of Figure 11.13.

modifies the parent of _N_ to record the split. The procedures insert into index and insert in parent use a temporary area of memory _T_ to store the contents of a node being split. The procedures can be modified to copy data from the node being split directly to the newly created node, reducing the time required for copying data. However, the use of the temporary space _T_ simplifies the procedures.

**11.3.3.2 Deletion**

We now consider deletions that cause tree nodes to contain too few pointers. First, let us delete “Srinivasan” from the B+-tree of Figure 11.13. The resulting B+-tree appears in Figure 11.16. We now consider how the deletion is performed. We first locate the entry for “Srinivasan” by using our lookup algorithm. When we delete the entry for “Srinivasan” from its leaf node, the node is left with only one entry, “Wu”. Since, in our example, _n_ \= 4 and 1 _<_ (_n_ − 1)_/_2, we must either merge the node with a sibling node, or redistribute the entries between the nodes, to ensure that each node is at least half-full. In our example, the underfull node with the entry for “Wu” can be merged with its left sibling node. We merge the nodes by moving the entries from both the nodes into the left sibling, and deleting the now empty right sibling. Once the node is deleted, we must also delete the entry in the parent node that pointed to the just deleted node.

In our example, the entry to be deleted is (Srinivasan, _n_3), where _n_3 is a pointer to the leaf containing “Srinivasan”. (In this case the entry to be deleted in the nonleaf node happens to be the same value as that deleted from the leaf; that would not be the case for most deletions.) After deleting the above entry, the parent node, which had a search key value “Srinivasan” and two pointers, now has one pointer (the leftmost pointer in the node) and no search-key values. Since 1 _<_ _n/_2 for _n_ \= 4, the parent node is underfull. (For larger _n_, a node that becomes underfull would still have some values as well as pointers.)

In this case, we look at a sibling node; in our example, the only sibling is the nonleaf node containing the search keys “Califieri”, “Einstein”, and “Gold”. If possible, we try to coalesce the node with its sibling. In this case, coalescing is not possible, since the node and its sibling together have five pointers, against a maximum of four. The solution in this case is to **redistribute** the pointers between  

**496 Chapter 11 Indexing and Hashing**

Adams Brandt Califieri Crick Einstein El Said Gold Katz Kim Mozart

Califieri Einstein Kim

Gold

**Figure 11.17** Deletion of “Singh” and “Wu” from the B+-tree of Figure 11.16.

the node and its sibling, such that each has at least _n/_2 = 2 child pointers. To do so, we move the rightmost pointer from the left sibling (the one pointing to the leaf node containing “Mozart”) to the underfull right sibling. However, the underfull right sibling would now have two pointers, namely its leftmost pointer, and the newly moved pointer, with no value separating them. In fact, the value separating them is not present in either of the nodes, but is present in the parent node, between the pointers from the parent to the node and its sibling. In our example, the value “Mozart” separates the two pointers, and is present in the right sibling after the redistribution. Redistribution of the pointers also means that the value “Mozart” in the parent no longer correctly separates search-key values in the two siblings. In fact, the value that now correctly separates search-key values in the two sibling nodes is the value “Gold”, which was in the left sibling before redistribution.

As a result, as can be seen in the B+-tree in Figure 11.16, after redistribution of pointers between siblings, the value “Gold” has moved up into the parent, while the value that was there earlier, “Mozart”, has moved down into the right sibling.

We next delete the search-key values “Singh” and “Wu” from the B+-tree of Figure 11.16. The result is shown in Figure 11.17. The deletion of the first of these values does not make the leaf node underfull, but the deletion of the second value does. It is not possible to merge the underfull node with its sibling, so a redistribution of values is carried out, moving the search-key value “Kim” into the node containing “Mozart”, resulting in the tree shown in Figure 11.17. The value separating the two siblings has been updated in the parent, from “Mozart” to “Kim”.

Now we delete “Gold” from the above tree; the result is shown in Figure 11.18. This results in an underfull leaf, which can now be merged with its sibling. The resultant deletion of an entry from the parent node (the nonleaf node containing “Kim”) makes the parent underfull (it is left with just one pointer). This time around, the parent node can be merged with its sibling. This merge results in the search-key value “Gold” moving down from the parent into the merged node. As a result of this merge, an entry is deleted from its parent, which happens to be the root of the tree. And as a result of that deletion, the root is left with only one child pointer and no search-key value, violating the condition that the root  

**11.3 B+-Tree Index Files 497**

Adams Brandt Einstein El Said Katz Kim Mozart

GoldCalifieri

Califieri

Einstein

Crick

**Figure 11.18** Deletion of “Gold” from the B+-tree of Figure 11.17.

have at least two children. As a result, the root node is deleted and its sole child becomes the root, and the depth of the B+-tree has been decreased by 1.

It is worth noting that, as a result of deletion, a key value that is present in a nonleaf node of the B+-tree may not be present at any leaf of the tree. For example, in Figure 11.18, the value “Gold” has been deleted from the leaf level, but is still present in a nonleaf node.

In general, to delete a value in a B+-tree, we perform a lookup on the value and delete it. If the node is too small, we delete it from its parent. This deletion results in recursive application of the deletion algorithm until the root is reached, a parent remains adequately full after deletion, or redistribution is applied.

Figure 11.19 outlines the pseudocode for deletion from a B+-tree. The proce- dure swap variables(_N, N_′) merely swaps the values of the (pointer) variables _N_ and _N_′; this swap has no effect on the tree itself. The pseudocode uses the condition “too few pointers/values.” For nonleaf nodes, this criterion means less than _n/_2 pointers; for leaf nodes, it means less than (_n_ − 1)_/_2 values. The pseudocode redistributes entries by borrowing a single entry from an adjacent node. We can also redistribute entries by repartitioning entries equally between the two nodes. The pseudocode refers to deleting an entry (_K , P_) from a node. In the case of leaf nodes, the pointer to an entry actually precedes the key value, so the pointer _P_ precedes the key value _K_ . For nonleaf nodes, _P_ follows the key value _K_ .

**11.3.4 Nonunique Search Keys**

If a relation can have more than one record containing the same search key value (that is, two or more records can have the same values for the indexed attributes), the search key is said to be a **nonunique search key**.

One problem with nonunique search keys is in the efficiency of record dele- tion. Suppose a particular search-key value occurs a large number of times, and one of the records with that search key is to be deleted. The deletion may have to search through a number of entries, potentially across multiple leaf nodes, to find the entry corresponding to the particular record being deleted.

A simple solution to this problem, used by most database systems, is to make search keys unique by creating a composite search key containing the original search key and another attribute, which together are unique across all records. The extra attribute can be a record-id, which is a pointer to the record, or any other attribute whose value is unique among all records with the same search-  

**498 Chapter 11 Indexing and Hashing**

**procedure** delete(_value K_ , _pointer P_) find the leaf node _L_ that contains (_K , P_) delete entry(_L , K , P_)

**procedure** delete entry(_node N_, _value K_ , _pointer P_) delete (_K , P_) from _N_ **if** (_N_ is the root **and** _N_ has only one remaining child) **then** make the child of _N_ the new root of the tree and delete _N_ **else if** (_N_ has too few values/pointers) **then begin**

Let _N_′ be the previous or next child of _parent_(_N_) Let _K_ ′ be the value between pointers _N_ and _N_′ in _parent_(_N_) **if** (entries in _N_ and _N_′ can fit in a single node)

**then begin** /\* Coalesce nodes \*/ **if** (_N_ is a predecessor of _N_′) **then** swap variables(_N, N_′) **if** (_N_ is not a leaf)

**then** append _K_ ′ and all pointers and values in _N_ to _N_′ **else** append all (_Ki , Pi_ ) pairs in _N_ to _N_′; set _N_′_.Pn_ \= _N.Pn_

delete entry(_parent_(_N_), _K_ ′, _N_); delete node _N_ **end**

**else begin** /\* Redistribution: borrow an entry from _N_′ \*/ **if** (_N_′ is a predecessor of _N_) **then begin**

**if** (_N_ is a nonleaf node) **then begin** let _m_ be such that _N_′_.Pm_ is the last pointer in _N_′ remove (_N_′_.Km_−1_, N_′_.Pm_) from _N_′ insert (_N_′_.Pm, K_ ′) as the first pointer and value in _N_,

by shifting other pointers and values right replace _K_ ′ in _parent_(_N_) by _N_′_.Km_−1

**end else begin**

let _m_ be such that (_N_′_.Pm, N_′_.Km_) is the last pointer/value pair in _N_′

remove (_N_′_.Pm, N_′_.Km_) from _N_′ insert (_N_′_.Pm, N_′_.Km_) as the first pointer and value in _N_,

by shifting other pointers and values right replace _K_ ′ in _parent_(_N_) by _N_′_.Km_

**end end else** _. . ._ symmetric to the **then** case _. . ._

**end end**

**Figure 11.19** Deletion of entry from a B+-tree.

key value. The extra attribute is called a **uniquifier** attribute. When a record is to be deleted, the composite search-key value is computed from the record, and then used to look up the index. Since the value is unique, the corresponding leaf-  

**11.3 B+-Tree Index Files 499**

level entry can be found with a single traversal from root to leaf, with no further accesses at the leaf level. As a result, record deletion can be done efficiently.

A search with the original search-key attribute simply ignores the value of the uniquifier attribute when comparing search-key values.

With nonunique search keys, our B+-tree structure stores each key value as many times as there are records containing that value. An alternative is to store each key value only once in the tree, and to keep a bucket (or list) of record pointers with a search-key value, to handle nonunique search keys. This approach is more space efficient since it stores the key value only once; however, it creates several complications when B+-trees are implemented. If the buckets are kept in the leaf node, extra code is needed to deal with variable-size buckets, and to deal with buckets that grow larger than the size of the leaf node. If the buckets are stored in separate blocks, an extra I/O operation may be required to fetch records. In addition to these problems, the bucket approach also has the problem of inefficiency for record deletion if a search-key value occurs a large number of times.

**11.3.5 Complexity of B+-Tree Updates**

Although insertion and deletion operations on B+-trees are complicated, they require relatively few I/O operations, which is an important benefit since I/O operations are expensive. It can be shown that the number of I/O operations needed in the worst case for an insertion is proportional to log_n/_2(_N_), where _n_ is the maximum number of pointers in a node, and _N_ is the number of records in the file being indexed.

The worst-case complexity of the deletion procedure is also proportional to log_n/_2(_N_), provided there are no duplicate values for the search key. If there are duplicate values, deletion may have to search across multiple records with the same search-key value to find the correct entry to be deleted, which can be inefficient. However, making the search key unique by adding a uniquifier attribute, as described in Section 11.3.4, ensures the worst-case complexity of deletion is the same even if the original search key is nonunique.

In other words, the cost of insertion and deletion operations in terms of I/O operations is proportional to the height of the B+-tree, and is therefore low. It is the speed of operation on B+-trees that makes them a frequently used index structure in database implementations.

In practice, operations on B+-trees result in fewer I/O operations than the worst-case bounds. With fanout of 100, and assuming accesses to leaf nodes are uniformly distributed, the parent of a leaf node is 100 times more likely to get accessed than the leaf node. Conversely, with the same fanout, the total number of nonleaf nodes in a B+-tree would be just a little more than 1/100th of the number of leaf nodes. As a result, with memory sizes of several gigabytes being common today, for B+-trees that are used frequently, even if the relation is very large it is quite likely that most of the nonleaf nodes are already in the database buffer when they are accessed. Thus, typically only one or two I/O operations are required to perform a lookup. For updates, the probability of a node split  

**500 Chapter 11 Indexing and Hashing**

occurring is correspondingly very small. Depending on the ordering of inserts, with a fanout of 100, only between 1 in 100 to 1 in 50 insertions will result in a node split, requiring more than one block to be written. As a result, on an average an insert will require just a little more than one I/O operation to write updated blocks.

Although B+-trees only guarantee that nodes will be at least half full, if entries are inserted in random order, nodes can be expected to be more than two-thirds full on average. If entries are inserted in sorted order, on the other hand, nodes will be only half full. (We leave it as an exercise to the reader to figure out why nodes would be only half full in the latter case.)

**11.4 B+-Tree Extensions**

In this section, we discuss several extensions and variations of the B+-tree index structure.

**11.4.1 B+-Tree File Organization**

As mentioned in Section 11.3, the main drawback of index-sequential file orga- nization is the degradation of performance as the file grows: With growth, an increasing percentage of index entries and actual records become out of order, and are stored in overflow blocks. We solve the degradation of index lookups by using B+-tree indices on the file. We solve the degradation problem for storing the actual records by using the leaf level of the B+-tree to organize the blocks containing the actual records. We use the B+-tree structure not only as an index, but also as an organizer for records in a file. In a **B**+**\-tree file organization**, the leaf nodes of the tree store records, instead of storing pointers to records. Figure 11.20 shows an example of a B+-tree file organization. Since records are usually larger than pointers, the maximum number of records that can be stored in a leaf node is less than the number of pointers in a nonleaf node. However, the leaf nodes are still required to be at least half full.

I

C MK

(A,4) (C,1)(B,8) (D,9) (E,4) (F,7) (G,3) (H,3)

(I,4) (J,8) (K,1) (L,6) (M,4) (N,8) (P,6)

F

**Figure 11.20** B+-tree file organization.  

**11.4 B+-Tree Extensions 501**

Insertion and deletion of records from a B+-tree file organization are handled in the same way as insertion and deletion of entries in a B+-tree index. When a record with a given key value _v_ is inserted, the system locates the block that should contain the record by searching the B+-tree for the largest key in the tree that is ≤ _v_. If the block located has enough free space for the record, the system stores the record in the block. Otherwise, as in B+-tree insertion, the system splits the block in two, and redistributes the records in it (in the B+-tree–key order) to create space for the new record. The split propagates up the B+-tree in the normal fashion. When we delete a record, the system first removes it from the block containing it. If a block _B_ becomes less than half full as a result, the records in _B_ are redistributed with the records in an adjacent block _B_ ′. Assuming fixed-sized records, each block will hold at least one-half as many records as the maximum that it can hold. The system updates the nonleaf nodes of the B+-tree in the usual fashion.

When we use a B+-tree for file organization, space utilization is particularly important, since the space occupied by the records is likely to be much more than the space occupied by keys and pointers. We can improve the utilization of space in a B+-tree by involving more sibling nodes in redistribution during splits and merges. The technique is applicable to both leaf nodes and nonleaf nodes, and works as follows:

During insertion, if a node is full the system attempts to redistribute some of its entries to one of the adjacent nodes, to make space for a new entry. If this attempt fails because the adjacent nodes are themselves full, the system splits the node, and splits the entries evenly among one of the adjacent nodes and the two nodes that it obtained by splitting the original node. Since the three nodes together contain one more record than can fit in two nodes, each node will be about two-thirds full. More precisely, each node will have at least 2_n/_3 entries, where _n_ is the maximum number of entries that the node can hold. (_x_ denotes the greatest integer that is less than or equal to _x_; that is, we drop the fractional part, if any.)

During deletion of a record, if the occupancy of a node falls below 2_n/_3, the system attempts to borrow an entry from one of the sibling nodes. If both sibling nodes have 2_n/_3 records, instead of borrowing an entry, the system redistributes the entries in the node and in the two siblings evenly between two of the nodes, and deletes the third node. We can use this approach because the total number of entries is 32_n/_3 − 1, which is less than 2_n_. With three adjacent nodes used for redistribution, each node can be guaranteed to have 3_n/_4 entries. In general, if _m_ nodes (_m_ − 1 siblings) are involved in redistribution, each node can be guaranteed to contain at least (_m_ − 1)_n/m_ entries. However, the cost of update becomes higher as more sibling nodes are involved in the redistribution.

Note that in a B+-tree index or file organization, leaf nodes that are adjacent to each other in the tree may be located at different places on disk. When a file organization is newly created on a set of records, it is possible to allocate blocks that are mostly contiguous on disk to leaf nodes that are contiguous in the tree. Thus a sequential scan of leaf nodes would correspond to a mostly sequential scan on disk. As insertions and deletions occur on the tree, sequentiality is increasingly  

**502 Chapter 11 Indexing and Hashing**

lost, and sequential access has to wait for disk seeks increasingly often. An index rebuild may be required to restore sequentiality.

B+-tree file organizations can also be used to store large objects, such as SQL clobs and blobs, which may be larger than a disk block, and as large as multiple gigabytes. Such large objects can be stored by splitting them into sequences of smaller records that are organized in a B+-tree file organization. The records can be sequentially numbered, or numbered by the byte offset of the record within the large object, and the record number can be used as the search key.

**11.4.2 Secondary Indices and Record Relocation**

Some file organizations, such as the B+-tree file organization, may change the location of records even when the records have not been updated. As an example, when a leaf node is split in a B+-tree file organization, a number of records are moved to a new node. In such cases, all secondary indices that store pointers to the relocated records would have to be updated, even though the values in the records may not have changed. Each leaf node may contain a fairly large number of records, and each of them may be in different locations on each secondary index. Thus a leaf-node split may require tens or even hundreds of I/O operations to update all affected secondary indices, making it a very expensive operation.

A widely used solution for this problem is as follows: In secondary indices, in place of pointers to the indexed records, we store the values of the primary- index search-key attributes. For example, suppose we have a primary index on the attribute _ID_ of relation _instructor_; then a secondary index on _dept name_ would store with each department name a list of instructor’s _ID_ values of the corresponding records, instead of storing pointers to the records.

Relocation of records because of leaf-node splits then does not require any update on any such secondary index. However, locating a record using the sec- ondary index now requires two steps: First we use the secondary index to find the primary-index search-key values, and then we use the primary index to find the corresponding records.

The above approach thus greatly reduces the cost of index update due to file reorganization, although it increases the cost of accessing data using a secondary index.

**11.4.3 Indexing Strings**

Creating B+-tree indices on string-valued attributes raises two problems. The first problem is that strings can be of variable length. The second problem is that strings can be long, leading to a low fanout and a correspondingly increased tree height.

With variable-length search keys, different nodes can have different fanouts even if they are full. A node must then be split if it is full, that is, there is no space to add a new entry, regardless of how many search entries it has. Similarly, nodes can be merged or entries redistributed depending on what fraction of the space in the nodes is used, instead of being based on the maximum number of entries that the node can hold.  

**11.4 B+-Tree Extensions 503**

The fanout of nodes can be increased by using a technique called **prefix compression**. With prefix compression, we do not store the entire search key value at nonleaf nodes. We only store a prefix of each search key value that is sufficient to distinguish between the key values in the subtrees that it separates. For example, if we had an index on names, the key value at a nonleaf node could be a prefix of a name; it may suffice to store “Silb” at a nonleaf node, instead of the full “Silberschatz” if the closest values in the two subtrees that it separates are, say, “Silas” and “Silver” respectively.

**11.4.4 Bulk Loading of B+-Tree Indices**

As we saw earlier, insertion of a record in a B+-tree requires a number of I/O operations that in the worst case is proportional to the height of the tree, which is usually fairly small (typically five or less, even for large relations).

Now consider the case where a B+-tree is being built on a large relation. Suppose the relation is significantly larger than main memory, and we are con- structing a nonclustering index on the relation such that the index is also larger than main memory. In this case, as we scan the relation and add entries to the B+-tree, it is quite likely that each leaf node accessed is not in the database buffer when it is accessed, since there is no particular ordering of the entries. With such randomly ordered accesses to blocks, each time an entry is added to the leaf, a disk seek will be required to fetch the block containing the leaf node. The block will probably be evicted from the disk buffer before another entry is added to the block, leading to another disk seek to write the block back to disk. Thus a random read and a random write operation may be required for each entry inserted.

For example, if the relation has 100 million records, and each I/O operation takes about 10 milliseconds, it would take at least 1 million seconds to build the index, counting only the cost of reading leaf nodes, not even counting the cost of writing the updated nodes back to disk. This is clearly a very large amount of time; in contrast, if each record occupies 100 bytes, and the disk subsystem can transfer data at 50 megabytes per second, it would take just 200 seconds to read the entire relation.

Insertion of a large number of entries at a time into an index is referred to as **bulk loading** of the index. An efficient way to perform bulk loading of an index is as follows. First, create a temporary file containing index entries for the relation, then sort the file on the search key of the index being constructed, and finally scan the sorted file and insert the entries into the index. There are efficient algorithms for sorting large relations, which are described later in Section 12.4, which can sort even a large file with an I/O cost comparable to that of reading the file a few times, assuming a reasonable amount of main memory is available.

There is a significant benefit to sorting the entries before inserting them into the B+-tree. When the entries are inserted in sorted order, all entries that go to a particular leaf node will appear consecutively, and the leaf needs to be written out only once; nodes will never have to be read from disk during bulk load, if the B+-tree was empty to start with. Each leaf node will thus incur only one I/O operation even though many entries may be inserted into the node. If each leaf  

**504 Chapter 11 Indexing and Hashing**

contains 100 entries, the leaf level will contain 1 million nodes, resulting in only 1 million I/O operations for creating the leaf level. Even these I/O operations can be expected to be sequential, if successive leaf nodes are allocated on successive disk blocks, and few disk seeks would be required. With current disks, 1 millisecond per block is a reasonable estimate for mostly sequential I/O operations, in contrast to 10 milliseconds per block for random I/O operations.

We shall study the cost of sorting a large relation later, in Section 12.4, but as a rough estimate, the index which would have taken a million seconds to build otherwise, can be constructed in well under 1000 seconds by sorting the entries before inserting them into the B+-tree, in contrast to more than 1,000,000 seconds for inserting in random order.

If the B+-tree is initially empty, it can be constructed faster by building it bottom-up, from the leaf level, instead of using the usual insert procedure. In **bottom-up B**+**\-tree construction**, after sorting the entries as we just described, we break up the sorted entries into blocks, keeping as many entries in a block as can fit in the block; the resulting blocks form the leaf level of the B+-tree. The minimum value in each block, along with the pointer to the block, is used to create entries in the next level of the B+-tree, pointing to the leaf blocks. Each further level of the tree is similarly constructed using the minimum values associated with each node one level below, until the root is created. We leave details as an exercise for the reader.

Most database systems implement efficient techniques based on sorting of en- tries, and bottom-up construction, when creating an index on a relation, although they use the normal insertion procedure when tuples are added one at a time to a relation with an existing index. Some database systems recommend that if a very large number of tuples are added at once to an already existing relation, indices on the relation (other than any index on the primary key) should be dropped, and then re-created after the tuples are inserted, to take advantage of efficient bulk-loading techniques.

**11.4.5 B-Tree Index Files**

**B-tree indices** are similar to B+-tree indices. The primary distinction between the two approaches is that a B-tree eliminates the redundant storage of search-key val- ues. In the B+-tree of Figure 11.13, the search keys “Califieri”, “Einstein”, “Gold”, “Mozart”, and “Srinivasan” appear in nonleaf nodes, in addition to appearing in the leaf nodes. Every search-key value appears in some leaf node; several are repeated in nonleaf nodes.

A B-tree allows search-key values to appear only once (if they are unique), unlike a B+-tree, where a value may appear in a nonleaf node, in addition to appearing in a leaf node. Figure 11.21 shows a B-tree that represents the same search keys as the B+-tree of Figure 11.13. Since search keys are not repeated in the B-tree, we may be able to store the index in fewer tree nodes than in the corresponding B+-tree index. However, since search keys that appear in nonleaf nodes appear nowhere else in the B-tree, we are forced to include an additional  

**11.4 B+-Tree Extensions 505**

Brandt Califieri Crick El Said Gold Kim Mozart Srinivasan Wu

Einstein Katz Singh

Einstein record

Katz record

Singh record

Brandt record

Califieri record ... and soon for other records...

**Figure 11.21** B-tree equivalent of B+-tree in Figure 11.13.

pointer field for each search key in a nonleaf node. These additional pointers point to either file records or buckets for the associated search key.

It is worth noting that many database system manuals, articles in industry literature, and industry professionals use the term B-tree to refer to the data structure that we call the B+-tree. In fact, it would be fair to say that in current usage, the term B-tree is assumed to be synonymous with B+-tree. However, in this book we use the terms B-tree and B+-tree as they were originally defined, to avoid confusion between the two data structures.

A generalized B-tree leaf node appears in Figure 11.22a; a nonleaf node ap- pears in Figure 11.22b. Leaf nodes are the same as in B+-trees. In nonleaf nodes, the pointers _Pi_ are the tree pointers that we used also for B+-trees, while the pointers _Bi_ are bucket or file-record pointers. In the generalized B-tree in the figure, there are _n_− 1 keys in the leaf node, but there are _m_ − 1 keys in the nonleaf node. This discrepancy occurs because nonleaf nodes must include pointers _Bi_ , thus reducing the number of search keys that can be held in these nodes. Clearly, _m < n_, but the exact relationship between _m_ and _n_ depends on the relative size of search keys and pointers.

The number of nodes accessed in a lookup in a B-tree depends on where the search key is located. A lookup on a B+-tree requires traversal of a path from the root of the tree to some leaf node. In contrast, it is sometimes possible to find the desired value in a B-tree before reaching a leaf node. However, roughly _n_ times as many keys are stored in the leaf level of a B-tree as in the nonleaf levels, and, since _n_ is typically large, the benefit of finding certain values early is relatively

_P_1 _K_1 _P_2 _Pn_\-1 _Kn_\-1 _Pn_…

_P_1 _B_1 _K_1 _P_2 _B_2 _K_2 … _Pm_\-1 _Bm_\-1 _Km_\-1 _Pm_

(a)

(b)

**Figure 11.22** Typical nodes of a B-tree. (a) Leaf node. (b) Nonleaf node.  

**506 Chapter 11 Indexing and Hashing**

small. Moreover, the fact that fewer search keys appear in a nonleaf B-tree node, compared to B+-trees, implies that a B-tree has a smaller fanout and therefore may have depth greater than that of the corresponding B+-tree. Thus, lookup in a B-tree is faster for some search keys but slower for others, although, in general, lookup time is still proportional to the logarithm of the number of search keys.

Deletion in a B-tree is more complicated. In a B+-tree, the deleted entry always appears in a leaf. In a B-tree, the deleted entry may appear in a nonleaf node. The proper value must be selected as a replacement from the subtree of the node containing the deleted entry. Specifically, if search key _Ki_ is deleted, the smallest search key appearing in the subtree of pointer _Pi_ \+ 1 must be moved to the field formerly occupied by _Ki_ . Further actions need to be taken if the leaf node now has too few entries. In contrast, insertion in a B-tree is only slightly more complicated than is insertion in a B+-tree.

The space advantages of B-trees are marginal for large indices, and usually do not outweigh the disadvantages that we have noted. Thus, pretty much all database-system implementations use the B+-tree data structure, even if (as we discussed earlier) they refer to the data structure as a B-tree.

**11.4.6 Flash Memory**

In our description of indexing so far, we have assumed that data are resident on magnetic disks. Although this assumption continues to be true for the most part, flash memory capacities have grown significantly, and the cost of flash memory per gigabyte has dropped equally significantly, making flash memory storage a serious contender for replacing magnetic-disk storage for many applications. A natural question is, how would this change affect the index structure.

Flash-memory storage is structured as blocks, and the B+-tree index structure can be used for flash-memory storage. The benefit of the much faster access speeds is clear for index lookups. Instead of requiring an average of 10 milliseconds to seek to and read a block, a random block can be read in about a microsecond from flash-memory. Thus lookups run significantly faster than with disk-based data. The optimum B+-tree node size for flash-memory is typically smaller than that with disk.

The only real drawback with flash memory is that it does not permit in- place updates to data at the physical level, although it appears to do so logically. Every update turns into a copy+write of an entire flash-memory block, requiring the old copy of the block to be erased subsequently; a block erase takes about 1 millisecond. There is ongoing research aimed at developing index structures that can reduce the number of block erases. Meanwhile, standard B+-tree indices can continue to be used even on flash-memory storage, with acceptable update performance, and significantly improved lookup performance compared to disk storage.

**11.5 Multiple-Key Access**

Until now, we have assumed implicitly that only one index on one attribute is used to process a query on a relation. However, for certain types of queries, it is  

**11.5 Multiple-Key Access 507**

advantageous to use multiple indices if they exist, or to use an index built on a multiattribute search key.

**11.5.1 Using Multiple Single-Key Indices**

Assume that the _instructor_ file has two indices: one for _dept name_ and one for _salary_. Consider the following query: “Find all instructors in the Finance department with salary equal to $80,000.” We write

**select** _ID_ **from** _instructor_ **where** _dept name_ \=’Finance’ **and** _salary_\= 80000;

There are three strategies possible for processing this query:

**1\.** Use the index on _dept name_ to find all records pertaining to the Finance department. Examine each such record to see whether _salary_\= 80000.

**2\.** Use the index on _salary_ to find all records pertaining to instructors with salary of $80,000. Examine each such record to see whether the department name is “Finance”.

**3\.** Use the index on _dept name_ to find _pointers_ to all records pertaining to the Finance department. Also, use the index on _salary_ to find pointers to all records pertaining to instructors with a salary of $80,000. Take the intersec- tion of these two sets of pointers. Those pointers that are in the intersection point to records pertaining to instructors of the Finance department and with salary of $80,000.

The third strategy is the only one of the three that takes advantage of the existence of multiple indices. However, even this strategy may be a poor choice if all of the following hold:

• There are many records pertaining to the Finance department.

• There are many records pertaining to instructors with a salary of $80,000.

• There are only a few records pertaining to _both_ the Finance department and instructors with a salary of $80,000.

If these conditions hold, we must scan a large number of pointers to produce a small result. An index structure called a “bitmap index” can in some cases greatly speed up the intersection operation used in the third strategy. Bitmap indices are outlined in Section 11.9.  

**508 Chapter 11 Indexing and Hashing**

**11.5.2 Indices on Multiple Keys**

An alternative strategy for this case is to create and use an index on a composite search key (_dept name_, _salary_)—that is, the search key consisting of the department name concatenated with the instructor salary.

We can use an ordered (B+-tree) index on the above composite search key to answer efficiently queries of the form

**select** _ID_ **from** _instructor_ **where** _dept name_ \= ’Finance’ **and** _salary_\= 80000;

Queries such as the following query, which specifies an equality condition on the first attribute of the search key (_dept name_) and a range on the second attribute of the search key (_salary_), can also be handled efficiently since they correspond to a range query on the search attribute.

**select** _ID_ **from** _instructor_ **where** _dept name_ \= ’Finance’ **and** _salary<_ 80000;

We can even use an ordered index on the search key (_dept name_, _salary_) to answer the following query on only one attribute efficiently:

**select** _ID_ **from** _instructor_ **where** _dept name_ \= ’Finance’;

An equality condition _dept name_ \= “Finance” is equivalent to a range query on the range with lower end (Finance, −∞) and upper end (Finance, +∞). Range queries on just the _dept name_ attribute can be handled in a similar manner.

The use of an ordered-index structure on a composite search key, however, has a few shortcomings. As an illustration, consider the query

**select** _ID_ **from** _instructor_ **where** _dept name <_ ’Finance’ **and** _salary<_ 80000;

We can answer this query by using an ordered index on the search key (_dept name_, _salary_): For each value of _dept name_ that is less than “Finance” in alphabetic

order, the system locates records with a _salary_ value of 80000. However, each record is likely to be in a different disk block, because of the ordering of records in the file, leading to many I/O operations.

The difference between this query and the previous two queries is that the condition on the first attribute (_dept name_) is a comparison condition, rather than  

**11.6 Static Hashing 509**

an equality condition. The condition does not correspond to a range query on the search key.

To speed the processing of general composite search-key queries (which can involve one or more comparison operations), we can use several special struc- tures. We shall consider _bitmap indices_ in Section 11.9. There is another structure, called the _R-tree_, that can be used for this purpose. The R-tree is an extension of the B+-tree to handle indexing on multiple dimensions. Since the R-tree is used primarily with geographical data types, we describe the structure in Chapter 25.

**11.5.3 Covering Indices**

**Covering indices** are indices that store the values of some attributes (other than the search-key attributes) along with the pointers to the record. Storing extra attribute values is useful with secondary indices, since they allow us to answer some queries using just the index, without even looking up the actual records.

For example, suppose that we have a nonclustering index on the _ID_ attribute of the _instructor_ relation. If we store the value of the _salary_ attribute along with the record pointer, we can answer queries that require the salary (but not the other attribute, _dept name_) without accessing the _instructor_ record.

The same effect could be obtained by creating an index on the search key (_ID_, _salary_), but a covering index reduces the size of the search key, allowing a larger fanout in the nonleaf nodes, and potentially reducing the height of the index.

**11.6 Static Hashing**

One disadvantage of sequential file organization is that we must access an index structure to locate data, or must use binary search, and that results in more I/O operations. File organizations based on the technique of **hashing** allow us to avoid accessing an index structure. Hashing also provides a way of constructing indices. We study file organizations and indices based on hashing in the following sections.

In our description of hashing, we shall use the term **bucket** to denote a unit of storage that can store one or more records. A bucket is typically a disk block, but could be chosen to be smaller or larger than a disk block.

Formally, let _K_ denote the set of all search-key values, and let _B_ denote the set of all bucket addresses. A **hash function** _h_ is a function from _K_ to _B_. Let _h_ denote a hash function.

To insert a record with search key _Ki_ , we compute _h_(_Ki_ ), which gives the address of the bucket for that record. Assume for now that there is space in the bucket to store the record. Then, the record is stored in that bucket.

To perform a lookup on a search-key value _Ki_ , we simply compute _h_(_Ki_ ), then search the bucket with that address. Suppose that two search keys, _K_5 and _K_7, have the same hash value; that is, _h_(_K_5) = _h_(_K_7). If we perform a lookup on _K_5, the bucket _h_(_K_5) contains records with search-key values _K_5 and records  

**510 Chapter 11 Indexing and Hashing**

with search-key values _K_7\. Thus, we have to check the search-key value of every record in the bucket to verify that the record is one that we want.

Deletion is equally straightforward. If the search-key value of the record to be deleted is _Ki_ , we compute _h_(_Ki_ ), then search the corresponding bucket for that record, and delete the record from the bucket.

Hashing can be used for two different purposes. In a **hash file organization**, we obtain the address of the disk block containing a desired record directly by computing a function on the search-key value of the record. In a **hash index organization** we organize the search keys, with their associated pointers, into a hash file structure.

**11.6.1 Hash Functions**

The worst possible hash function maps all search-key values to the same bucket. Such a function is undesirable because all the records have to be kept in the same bucket. A lookup has to examine every such record to find the one desired. An ideal hash function distributes the stored keys uniformly across all the buckets, so that every bucket has the same number of records.

Since we do not know at design time precisely which search-key values will be stored in the file, we want to choose a hash function that assigns search-key values to buckets in such a way that the distribution has these qualities:

• The distribution is _uniform_. That is, the hash function assigns each bucket the same number of search-key values from the set of _all_ possible search-key values.

• The distribution is _random_. That is, in the average case, each bucket will have nearly the same number of values assigned to it, regardless of the actual distribution of search-key values. More precisely, the hash value will not be correlated to any externally visible ordering on the search-key values, such as alphabetic ordering or ordering by the length of the search keys; the hash function will appear to be random.

As an illustration of these principles, let us choose a hash function for the _instructor_ file using the search key _dept name_. The hash function that we choose must have the desirable properties not only on the example _instructor_ file that we have been using, but also on an _instructor_ file of realistic size for a large university with many departments.

Assume that we decide to have 26 buckets, and we define a hash function that maps names beginning with the _i_th letter of the alphabet to the _i_th bucket. This hash function has the virtue of simplicity, but it fails to provide a uniform distribution, since we expect more names to begin with such letters as B and R than Q and X, for example.

Now suppose that we want a hash function on the search key _salary_. Suppose that the minimum salary is $30,000 and the maximum salary is $130,000, and we use a hash function that divides the values into 10 ranges, $30,000–$40,000, $40,001–$50,000 and so on. The distribution of search-key values is uniform (since  

**11.6 Static Hashing 511**

bucket 0

bucket 1

bucket 2

bucket 3

bucket 4

bucket 5

bucket 6

bucket 7

45565

15151 Mozart Music 40000

80000 Wu12121 Finance 90000

76543 FinanceSingh

10101 Comp. Sci.Srinivasan Katz Comp. Sci. 75000

92000

6500032343 58583

El Said Califieri

History History

80000 60000

Einstein Gold Kim

22222 33456 98345

Physics Physics Elec. Eng.

95000 87000 80000

Brandt83821 Comp. Sci.

76766 Crick Biology 72000

**Figure 11.23** Hash organization of _instructor_ file, with _dept name_ as the key.

each bucket has the same number of different _salary_ values), but is not random. Records with salaries between $60,001 and $70,000 are far more common than are records with salaries between $30,001 and $40,000. As a result, the distribution of records is not uniform—some buckets receive more records than others do. If the function has a random distribution, even if there are such correlations in the search keys, the randomness of the distribution will make it very likely that all buckets will have roughly the same number of records, as long as each search key occurs in only a small fraction of the records. (If a single search key occurs in a large fraction of the records, the bucket containing it is likely to have more records than other buckets, regardless of the hash function used.)

Typical hash functions perform computation on the internal binary machine representation of characters in the search key. A simple hash function of this type first computes the sum of the binary representations of the characters of a key, then returns the sum modulo the number of buckets.

Figure 11.23 shows the application of such a scheme, with eight buckets, to the _instructor_ file, under the assumption that the _i_th letter in the alphabet is represented by the integer _i_.

The following hash function is a better alternative for hashing strings. Let _s_ be a string of length _n_, and let _s_\[_i_\] denote the _i_th byte of the string. The hash function is defined as:

_s_\[0\] ∗ 31(_n_−1) + _s_\[1\] ∗ 31(_n_−2) + · · · + _s_\[_n_ − 1\]  

**512 Chapter 11 Indexing and Hashing**

The function can be implemented efficiently by setting the hash result initially to 0, and iterating from the first to the last character of the string, at each step multiplying the hash value by 31 and then adding the next character (treated as an integer). The above expression would appear to result in a very large number, but it is actually computed with fixed-size positive integers; the result of each multiplication and addition is thus automatically computed modulo the largest possible integer value plus 1. The result of the above function modulo the number of buckets can then be used for indexing.

Hash functions require careful design. A bad hash function may result in lookup taking time proportional to the number of search keys in the file. A well- designed function gives an average-case lookup time that is a (small) constant, independent of the number of search keys in the file.

**11.6.2 Handling of Bucket Overflows**

So far, we have assumed that, when a record is inserted, the bucket to which it is mapped has space to store the record. If the bucket does not have enough space, a **bucket overflow** is said to occur. Bucket overflow can occur for several reasons:

• **Insufficient buckets**. The number of buckets, which we denote _nB_ , must be chosen such that _nB > nr/ fr_ , where _nr_ denotes the total number of records that will be stored and _fr_ denotes the number of records that will fit in a bucket. This designation, of course, assumes that the total number of records is known when the hash function is chosen.

• **Skew**. Some buckets are assigned more records than are others, so a bucket may overflow even when other buckets still have space. This situation is called bucket **skew**. Skew can occur for two reasons:

**1\.** Multiple records may have the same search key.

**2\.** The chosen hash function may result in nonuniform distribution of search keys.

So that the probability of bucket overflow is reduced, the number of buckets is chosen to be (_nr/ fr_ ) ∗ (1 + _d_), where _d_ is a fudge factor, typically around 0_._2\. Some space is wasted: About 20 percent of the space in the buckets will be empty. But the benefit is that the probability of overflow is reduced.

Despite allocation of a few more buckets than required, bucket overflow can still occur. We handle bucket overflow by using **overflow buckets**. If a record must be inserted into a bucket _b_, and _b_ is already full, the system provides an overflow bucket for _b_, and inserts the record into the overflow bucket. If the overflow bucket is also full, the system provides another overflow bucket, and so on. All the overflow buckets of a given bucket are chained together in a linked list, as in Figure 11.24. Overflow handling using such a linked list is called **overflow chaining**.

We must change the lookup algorithm slightly to handle overflow chaining. As before, the system uses the hash function on the search key to identify a bucket  

**11.6 Static Hashing 513**

overflow buckets for bucket 1

bucket 0

bucket 1

bucket 2

bucket 3

**Figure 11.24** Overflow chaining in a hash structure.

_b_. The system must examine all the records in bucket _b_ to see whether they match the search key, as before. In addition, if bucket _b_ has overflow buckets, the system must examine the records in all the overflow buckets also.

The form of hash structure that we have just described is sometimes referred to as **closed hashing**. Under an alternative approach, called **open hashing**, the set of buckets is fixed, and there are no overflow chains. Instead, if a bucket is full, the system inserts records in some other bucket in the initial set of buckets _B_. One policy is to use the next bucket (in cyclic order) that has space; this policy is called _linear probing_. Other policies, such as computing further hash functions, are also used. Open hashing has been used to construct symbol tables for compilers and assemblers, but closed hashing is preferable for database systems. The rea- son is that deletion under open hashing is troublesome. Usually, compilers and assemblers perform only lookup and insertion operations on their symbol tables. However, in a database system, it is important to be able to handle deletion as well as insertion. Thus, open hashing is of only minor importance in database implementation.

An important drawback to the form of hashing that we have described is that we must choose the hash function when we implement the system, and it cannot be changed easily thereafter if the file being indexed grows or shrinks. Since the function _h_ maps search-key values to a fixed set _B_ of bucket addresses, we waste space if _B_ is made large to handle future growth of the file. If _B_ is too small, the buckets contain records of many different search-key values, and bucket overflows can occur. As the file grows, performance suffers. We study later, in Section 11.7, how the number of buckets and the hash function can be changed dynamically.  

**514 Chapter 11 Indexing and Hashing**

bucket 0

bucket 1

bucket 2

bucket 3

bucket 4

bucket 5

bucket 6

76766

45565 76543

10101

15151 33456

58583

83821

22222

98345

bucket 7 12121 32343

76766 Crick

76543 Singh 32343 El Said 58583 Califieri 15151 Mozart 22222 Einstein 33465 Gold

10101 Srinivasan 45565 Katz 83821 Brandt 98345 Kim 12121 Wu

Biology

Physics

Finance History History Music

Physics

Comp. Sci. Comp. Sci. Comp. Sci. Elec. Eng. Finance

72000

80000 60000 62000 40000 95000 87000

65000 75000 92000 80000 90000

**Figure 11.25** Hash index on search key _ID_ of _instructor_ file.

**11.6.3 Hash Indices**

Hashing can be used not only for file organization, but also for index-structure creation. A **hash index** organizes the search keys, with their associated pointers, into a hash file structure. We construct a hash index as follows. We apply a hash function on a search key to identify a bucket, and store the key and its associated pointers in the bucket (or in overflow buckets). Figure 11.25 shows a secondary hash index on the _instructor_ file, for the search key _ID_. The hash function in the figure computes the sum of the digits of the _ID_ modulo 8. The hash index has eight buckets, each of size 2 (realistic indices would, of course, have much larger bucket sizes). One of the buckets has three keys mapped to it, so it has an overflow bucket. In this example, _ID_ is a primary key for _instructor_, so each search key has  

**11.7 Dynamic Hashing 515**

only one associated pointer. In general, multiple pointers can be associated with each key.

We use the term _hash index_ to denote hash file structures as well as secondary hash indices. Strictly speaking, hash indices are only secondary index structures. A hash index is never needed as a clustering index structure, since, if a file itself is organized by hashing, there is no need for a separate hash index structure on it. However, since hash file organization provides the same direct access to records that indexing provides, we pretend that a file organized by hashing also has a clustering hash index on it.

**11.7 Dynamic Hashing**

As we have seen, the need to fix the set _B_ of bucket addresses presents a serious problem with the static hashing technique of the previous section. Most databases grow larger over time. If we are to use static hashing for such a database, we have three classes of options:

**1\.** Choose a hash function based on the current file size. This option will result in performance degradation as the database grows.

**2\.** Choose a hash function based on the anticipated size of the file at some point in the future. Although performance degradation is avoided, a significant amount of space may be wasted initially.

**3\.** Periodically reorganize the hash structure in response to file growth. Such a reorganization involves choosing a new hash function, recomputing the hash function on every record in the file, and generating new bucket as- signments. This reorganization is a massive, time-consuming operation. Furthermore, it is necessary to forbid access to the file during reorganiza- tion.

Several **dynamic hashing** techniques allow the hash function to be modified dynamically to accommodate the growth or shrinkage of the database. In this section we describe one form of dynamic hashing, called **extendable hashing**. The bibliographical notes provide references to other forms of dynamic hashing.

**11.7.1 Data Structure**

Extendable hashing copes with changes in database size by splitting and coalesc- ing buckets as the database grows and shrinks. As a result, space efficiency is retained. Moreover, since the reorganization is performed on only one bucket at a time, the resulting performance overhead is acceptably low.

With extendable hashing, we choose a hash function _h_ with the desirable properties of uniformity and randomness. However, this hash function generates values over a relatively large range—namely, _b_\-bit binary integers. A typical value for _b_ is 32.  

**516 Chapter 11 Indexing and Hashing**

i i1

i2

i3

bucket 1

bucket 2

bucket 3

00.. 01..

10..

11..

bucket address table

hash prefix

…

…

**Figure 11.26** General extendable hash structure.

We do not create a bucket for each hash value. Indeed, 232 is over 4 billion, and that many buckets is unreasonable for all but the largest databases. Instead, we create buckets on demand, as records are inserted into the file. We do not use the entire _b_ bits of the hash value initially. At any point, we use _i_ bits, where 0 ≤ _i_ ≤ _b_. These _i_ bits are used as an offset into an additional table of bucket addresses. The value of _i_ grows and shrinks with the size of the database.

Figure 11.26 shows a general extendable hash structure. The _i_ appearing above the bucket address table in the figure indicates that _i_ bits of the hash value _h_(_K_ ) are required to determine the correct bucket for _K_. This number will, of course, change as the file grows. Although _i_ bits are required to find the correct entry in the bucket address table, several consecutive table entries may point to the same bucket. All such entries will have a common hash prefix, but the length of this prefix may be less than _i_. Therefore, we associate with each bucket an integer giving the length of the common hash prefix. In Figure 11.26 the integer associated with bucket _j_ is shown as _i j_ . The number of bucket-address-table entries that point to bucket _j_ is

2(_i_ − _i j_ )

**11.7.2 Queries and Updates**

We now see how to perform lookup, insertion, and deletion on an extendable hash structure.  

**11.7 Dynamic Hashing 517**

To locate the bucket containing search-key value _Kl_ , the system takes the first _i_ high-order bits of _h_(_Kl_), looks at the corresponding table entry for this bit string, and follows the bucket pointer in the table entry.

To insert a record with search-key value _Kl_ , the system follows the same procedure for lookup as before, ending up in some bucket—say, _j_. If there is room in the bucket, the system inserts the record in the bucket. If, on the other hand, the bucket is full, it must split the bucket and redistribute the current records, plus the new one. To split the bucket, the system must first determine from the hash value whether it needs to increase the number of bits that it uses.

• If _i_ \= _i j_ , only one entry in the bucket address table points to bucket _j_. Therefore, the system needs to increase the size of the bucket address table so that it can include pointers to the two buckets that result from splitting bucket _j_. It does so by considering an additional bit of the hash value. It increments the value of _i_ by 1, thus doubling the size of the bucket address table. It replaces each entry by two entries, both of which contain the same pointer as the original entry. Now two entries in the bucket address table point to bucket _j_. The system allocates a new bucket (bucket _z_), and sets the second entry to point to the new bucket. It sets _i j_ and _iz_ to _i_. Next, it rehashes each record in bucket _j_ and, depending on the first _i_ bits (remember the system has added 1 to _i_), either keeps it in bucket _j_ or allocates it to the newly created bucket.

The system now reattempts the insertion of the new record. Usually, the attempt will succeed. However, if all the records in bucket _j_, as well as the new record, have the same hash-value prefix, it will be necessary to split a bucket again, since all the records in bucket _j_ and the new record are assigned to the same bucket. If the hash function has been chosen carefully, it is unlikely that a single insertion will require that a bucket be split more than once, unless there are a large number of records with the same search key. If all the records in bucket _j_ have the same search-key value, no amount of splitting will help. In such cases, overflow buckets are used to store the records, as in static hashing.

• If _i > i j_ , then more than one entry in the bucket address table points to bucket _j_. Thus, the system can split bucket _j_ without increasing the size of the bucket address table. Observe that all the entries that point to bucket _j_ correspond to hash prefixes that have the same value on the leftmost _i j_ bits. The system allocates a new bucket (bucket _z_), and sets _i j_ and _iz_ to the value that results from adding 1 to the original _i j_ value. Next, the system needs to adjust the entries in the bucket address table that previously pointed to bucket _j_. (Note that with the new value for _i j_ , not all the entries correspond to hash prefixes that have the same value on the leftmost _i j_ bits.) The system leaves the first half of the entries as they were (pointing to bucket _j_), and sets all the remaining entries to point to the newly created bucket (bucket _z_). Next, as in the previous case, the system rehashes each record in bucket _j_, and allocates it either to bucket _j_ or to the newly created bucket _z_.  

**518 Chapter 11 Indexing and Hashing**

_dept\_name_ h(_dept\_name_)

Biology 0010 1101 1111 1011 0010 1100 0011 0000 Comp. Sci. 1111 0001 0010 0100 1001 0011 0110 1101 Elec. Eng. 0100 0011 1010 1100 1100 0110 1101 1111 Finance 1010 0011 1010 0000 1100 0110 1001 1111 History 1100 0111 1110 1101 1011 1111 0011 1010 Music 0011 0101 1010 0110 1100 1001 1110 1011 Physics 1001 1000 0011 1111 1001 1100 0000 0001

**Figure 11.27** Hash function for _dept name_.

The system then reattempts the insert. In the unlikely case that it again fails, it applies one of the two cases, _i_ \= _i j_ or _i > i j_ , as appropriate.

Note that, in both cases, the system needs to recompute the hash function on only the records in bucket _j_.

To delete a record with search-key value _Kl_ , the system follows the same procedure for lookup as before, ending up in some bucket—say, _j_. It removes both the search key from the bucket and the record from the file. The bucket, too, is removed if it becomes empty. Note that, at this point, several buckets can be coalesced, and the size of the bucket address table can be cut in half. The procedure for deciding on which buckets can be coalesced and how to coalesce buckets is left to you to do as an exercise. The conditions under which the bucket address table can be reduced in size are also left to you as an exercise. Unlike coalescing of buckets, changing the size of the bucket address table is a rather expensive operation if the table is large. Therefore it may be worthwhile to reduce the bucket-address-table size only if the number of buckets reduces greatly.

To illustrate the operation of insertion, we use the _instructor_ file in Figure 11.1 and assume that the search key is _dept name_ with the 32-bit hash values as appear in Figure 11.27. Assume that, initially, the file is empty, as in Figure 11.28. We insert the records one by one. To illustrate all the features of extendable hashing in a small structure, we shall make the unrealistic assumption that a bucket can hold only two records.

0 0

bucket 1bucket address table

hash prefix

**Figure 11.28** Initial extendable hash structure.  

**11.7 Dynamic Hashing 519**

1 1

bucket address table

hash prefix

1

15151 Music 40000

10101 12121

Srinivasan 90000 Wu 90000

Mozart

Comp. Sci. Finance

**Figure 11.29** Hash structure after three insertions.

We insert the record (10101, Srinivasan, Comp. Sci., 65000). The bucket address table contains a pointer to the one bucket, and the system inserts the record. Next, we insert the record (12121, Wu, Finance, 90000). The system also places this record in the one bucket of our structure.

When we attempt to insert the next record (15151, Mozart, Music, 40000), we find that the bucket is full. Since _i_ \= _i_0, we need to increase the number of bits that we use from the hash value. We now use 1 bit, allowing us 21 = 2 buckets. This increase in the number of bits necessitates doubling the size of the bucket address table to two entries. The system splits the bucket, placing in the new bucket those records whose search key has a hash value beginning with 1, and leaving in the original bucket the other records. Figure 11.29 shows the state of our structure after the split.

Next, we insert (22222, Einstein, Physics, 95000). Since the first bit of _h_(Physics) is 1, we must insert this record into the bucket pointed to by the “1” entry in the

2 1

2

2

bucket address table

hash prefix

15151 Music 40000Mozart

12121 Finance 90000Wu

10101 Comp. Sci. 65000Srinivasan

22222 Einstein Physics 95000

**Figure 11.30** Hash structure after four insertions.  

**520 Chapter 11 Indexing and Hashing**

3

1

3

3

bucket address table

hash prefix

2

22222 33456

Physics 95000 Physics 87000

Music15151 40000Mozart

Einstein Gold

12121 Wu 90000Finance

10101 32343

Srinivasan El Said

Comp. Sci. History 60000

65000

**Figure 11.31** Hash structure after six insertions.

3

1

3

3

bucket address table

hash prefix

3

22222 33456

Physics 95000 Physics 87000

Music15151 40000Mozart

Einstein Gold

12121 Wu 90000Finance

10101 45565

Srinivasan Katz

Comp. Sci. Comp. Sci. 75000

65000

32343 El Said History 60000

3

**Figure 11.32** Hash structure after seven insertions.  

**11.7 Dynamic Hashing 521**

bucket address table. Once again, we find the bucket full and _i_ \= _i_1\. We increase the number of bits that we use from the hash to 2. This increase in the number of bits necessitates doubling the size of the bucket address table to four entries, as in Figure 11.30. Since the bucket of Figure 11.29 for hash prefix 0 was not split, the two entries of the bucket address table of 00 and 01 both point to this bucket.

For each record in the bucket of Figure 11.29 for hash prefix 1 (the bucket being split), the system examines the first 2 bits of the hash value to determine which bucket of the new structure should hold it.

Next, we insert (32343, El Said, History, 60000), which goes in the same bucket as Comp. Sci. The following insertion of (33456, Gold, Physics, 87000) results in a bucket overflow, leading to an increase in the number of bits, and a doubling of the size of the bucket address table (see Figure 11.31).

The insertion of (45565, Katz, Comp. Sci., 75000) leads to another bucket over- flow; this overflow, however, can be handled without increasing the number of bits, since the bucket in question has two pointers pointing to it (see Figure 11.32).

Next, we insert the records of “Califieri”, “Singh”, and “Crick” without any bucket overflow. The insertion of the third Comp. Sci. record (83821, Brandt, Comp. Sci., 92000), however, leads to another overflow. This overflow cannot be handled by increasing the number of bits, since there are three records with exactly the same hash value. Hence the system uses an overflow bucket, as in

3

bucket address table

hash prefix

2

3

3

3

22222 33456

Physics 95000 Physics 87000

Music Biology

15151 40000 72000

Mozart

Einstein Gold

12121 Wu 90000Finance

10101 45565

Srinivasan Katz

Comp. Sci. Comp. Sci. 75000

65000

Crick76766

Singh76543 Finance

92000Comp. Sci.Brandt83821

32343 58583

El Said Califieri

History History

60000 62000

80000

3

**Figure 11.33** Hash structure after eleven insertions.  

**522 Chapter 11 Indexing and Hashing**

3

2

3

3

bucket address table

hash prefix

3

22222

33456 Physics 95000 Physics 87000

Music15151 40000Mozart

Einstein Gold

12121 Wu 90000Finance

10101 45565

Srinivasan Katz

Comp. Sci. Comp. Sci. 75000

65000

Crick Biology 7200076766

Singh76543 Finance

80000Elec. Eng.Kim98345

92000Comp. Sci.Brandt83821

32343 58583

El Said Califieri

History History

60000 62000

2

80000

3

**Figure 11.34** Extendable hash structure for the _instructor_ file.

Figure 11.33. We continue in this manner until we have inserted all the _instructor_ records of Figure 11.1. The resulting structure appears in Figure 11.34.

**11.7.3 Static Hashing versus Dynamic Hashing**

We now examine the advantages and disadvantages of extendable hashing, com- pared with static hashing. The main advantage of extendable hashing is that performance does not degrade as the file grows. Furthermore, there is minimal space overhead. Although the bucket address table incurs additional overhead, it contains one pointer for each hash value for the current prefix length. This table is thus small. The main space saving of extendable hashing over other forms of hashing is that no buckets need to be reserved for future growth; rather, buckets can be allocated dynamically.

A disadvantage of extendable hashing is that lookup involves an additional level of indirection, since the system must access the bucket address table before accessing the bucket itself. This extra reference has only a minor effect on per- formance. Although the hash structures that we discussed in Section 11.6 do not  

**11.8 Comparison of Ordered Indexing and Hashing 523**

have this extra level of indirection, they lose their minor performance advantage as they become full.

Thus, extendable hashing appears to be a highly attractive technique, pro- vided that we are willing to accept the added complexity involved in its im- plementation. The bibliographical notes reference more detailed descriptions of extendable hashing implementation.

The bibliographical notes also provide references to another form of dynamic hashing called **linear hashing**, which avoids the extra level of indirection associ- ated with extendable hashing, at the possible cost of more overflow buckets.

**11.8 Comparison of Ordered Indexing and Hashing**

We have seen several ordered-indexing schemes and several hashing schemes. We can organize files of records as ordered files by using index-sequential organi- zation or B+-tree organizations. Alternatively, we can organize the files by using hashing. Finally, we can organize them as heap files, where the records are not ordered in any particular way.

Each scheme has advantages in certain situations. A database-system imple- mentor could provide many schemes, leaving the final decision of which schemes to use to the database designer. However, such an approach requires the imple- mentor to write more code, adding both to the cost of the system and to the space that the system occupies. Most database systems support B+-trees and may additionally support some form of hash file organization or hash indices.

To make a choice of file organization and indexing techniques for a relation, the implementor or the database designer must consider the following issues:

• Is the cost of periodic reorganization of the index or hash organization ac- ceptable?

• What is the relative frequency of insertion and deletion?

• Is it desirable to optimize average access time at the expense of increasing the worst-case access time?

• What types of queries are users likely to pose?

We have already examined the first three of these issues, first in our review of the relative merits of specific indexing techniques, and again in our discussion of hashing techniques. The fourth issue, the expected type of query, is critical to the choice of ordered indexing or hashing.

If most queries are of the form:

**select** _A_1_, A_2_, . . . , An_ **from** _r_ **where** _Ai_ \= _c_;  

**524 Chapter 11 Indexing and Hashing**

then, to process this query, the system will perform a lookup on an ordered index or a hash structure for attribute _Ai_ , for value _c_. For queries of this form, a hashing scheme is preferable. An ordered-index lookup requires time proportional to the log of the number of values in _r_ for _Ai_ . In a hash structure, however, the average lookup time is a constant independent of the size of the database. The only advantage to an index over a hash structure for this form of query is that the worst-case lookup time is proportional to the log of the number of values in _r_ for _Ai_ . By contrast, for hashing, the worst-case lookup time is proportional to the number of values in _r_ for _Ai_ . However, the worst-case lookup time is unlikely to occur with hashing, and hashing is preferable in this case.

Ordered-index techniques are preferable to hashing in cases where the query specifies a range of values. Such a query takes the following form:

**select** _A_1_, A_2_, ..., An_ **from** _r_ **where** _Ai_ ≤ _c_2 **and** _Ai_ ≥ _c_1;

In other words, the preceding query finds all the records with _Ai_ values between _c_1 and _c_2.

Let us consider how we process this query using an ordered index. First, we perform a lookup on value _c_1\. Once we have found the bucket for value _c_1, we follow the pointer chain in the index to read the next bucket in order, and we continue in this manner until we reach _c_2.

If, instead of an ordered index, we have a hash structure, we can perform a lookup on _c_1 and can locate the corresponding bucket—but it is not easy, in general, to determine the next bucket that must be examined. The difficulty arises because a good hash function assigns values randomly to buckets. Thus, there is no simple notion of “next bucket in sorted order.” The reason we cannot chain buckets together in sorted order on _Ai_ is that each bucket is assigned many search- key values. Since values are scattered randomly by the hash function, the values in the specified range are likely to be scattered across many or all of the buckets. Therefore, we have to read all the buckets to find the required search keys.

Usually the designer will choose ordered indexing unless it is known in advance that range queries will be infrequent, in which case hashing would be chosen. Hash organizations are particularly useful for temporary files created during query processing, if lookups based on a key value are required, but no range queries will be performed.

**11.9 Bitmap Indices**

Bitmap indices are a specialized type of index designed for easy querying on multiple keys, although each bitmap index is built on a single key.

For bitmap indices to be used, records in a relation must be numbered se- quentially, starting from, say, 0. Given a number _n_, it must be easy to retrieve  

**11.9 Bitmap Indices 525**

the record numbered _n_. This is particularly easy to achieve if records are fixed in size, and allocated on consecutive blocks of a file. The record number can then be translated easily into a block number and a number that identifies the record within the block.

Consider a relation _r_ , with an attribute _A_ that can take on only one of a small number (for example, 2 to 20) values. For instance, a relation _instructor info_ may have an attribute _gender_, which can take only values m (male) or f (female). Another example would be an attribute _income level_, where income has been broken up into 5 levels: _L_1: $0−9999, _L_2: $10,000−19,999, _L_3: 20,000−39,999, _L_4: 40,000−74,999, and _L_5: 75,000−∞. Here, the raw data can take on many values, but a data analyst has split the values into a small number of ranges to simplify analysis of the data.

**11.9.1 Bitmap Index Structure**

A **bitmap** is simply an array of bits. In its simplest form, a **bitmap index** on the attribute _A_ of relation _r_ consists of one bitmap for each value that _A_ can take. Each bitmap has as many bits as the number of records in the relation. The _i_th bit of the bitmap for value _v j_ is set to 1 if the record numbered _i_ has the value _v j_ for attribute _A_. All other bits of the bitmap are set to 0.

In our example, there is one bitmap for the value m and one for f. The _i_th bit of the bitmap for m is set to 1 if the _gender_ value of the record numbered _i_ is m. All other bits of the bitmap for m are set to 0. Similarly, the bitmap for f has the value 1 for bits corresponding to records with the value f for the _gender_ attribute; all other bits have the value 0. Figure 11.35 shows an example of bitmap indices on a relation _instructor info_.

We now consider when bitmaps are useful. The simplest way of retrieving all records with value m (or value f) would be to simply read all records of the relation and select those records with value m (or f, respectively). The bitmap index doesn’t really help to speed up such a selection. While it would allow us to

_ID income\_levelgender_

76766

22222

12121

15151

58583

m

m

f

f

f

L1

L1

L2

L4

L3

record number

1

0

2

3

4

m

f

Bitmaps for _gender_

10010

01101

Bitmaps for _income\_level_

L1

L2

L3

L4

L5

10100

01000

00001

00010

00000

**Figure 11.35** Bitmap indices on relation _instructor info_.  

**526 Chapter 11 Indexing and Hashing**

read only those records for a specific gender, it is likely that every disk block for the file would have to be read anyway.

In fact, bitmap indices are useful for selections mainly when there are selec- tions on multiple keys. Suppose we create a bitmap index on attribute _income level_, which we described earlier, in addition to the bitmap index on _gender_.

Consider now a query that selects women with income in the range $10,000 _to_ $19_,_ 999\. This query can be expressed as

**select** \* **from** _r_ **where** _gender_ \= ’f’ **and** _income level_ \= ’L2’;

To evaluate this selection, we fetch the bitmaps for _gender_ value f and the bitmap for _income level_ value _L_2, and perform an **intersection** (logical-and) of the two bitmaps. In other words, we compute a new bitmap where bit _i_ has value 1 if the _i_th bit of the two bitmaps are both 1, and has a value 0 otherwise. In the example in Figure 11.35, the intersection of the bitmap for _gender_ \= f (01101) and the bitmap for _income level_ \= _L_2 (01000) gives the bitmap 01000.

Since the first attribute can take two values, and the second can take five values, we would expect only about 1 in 10 records, on an average, to satisfy a combined condition on the two attributes. If there are further conditions, the fraction of records satisfying all the conditions is likely to be quite small. The system can then compute the query result by finding all bits with value 1 in the intersection bitmap and retrieving the corresponding records. If the fraction is large, scanning the entire relation would remain the cheaper alternative.

Another important use of bitmaps is to count the number of tuples satisfying a given selection. Such queries are important for data analysis. For instance, if we wish to find out how many women have an income level _L_2, we compute the intersection of the two bitmaps and then count the number of bits that are 1 in the intersection bitmap. We can thus get the desired result from the bitmap index, without even accessing the relation.

Bitmap indices are generally quite small compared to the actual relation size. Records are typically at least tens of bytes to hundreds of bytes long, whereas a single bit represents the record in a bitmap. Thus the space occupied by a single bitmap is usually less than 1 percent of the space occupied by the relation. For instance, if the record size for a given relation is 100 bytes, then the space occupied by a single bitmap would be 1

8 of 1 percent of the space occupied by the relation. If an attribute _A_ of the relation can take on only one of eight values, a bitmap index on attribute _A_ would consist of eight bitmaps, which together occupy only 1 percent of the size of the relation.

Deletion of records creates gaps in the sequence of records, since shifting records (or record numbers) to fill gaps would be extremely expensive. To recog- nize deleted records, we can store an **existence bitmap**, in which bit _i_ is 0 if record _i_ does not exist and 1 otherwise. We shall see the need for existence bitmaps in Section 11.9.2. Insertion of records should not affect the sequence numbering of  

**11.9 Bitmap Indices 527**

other records. Therefore, we can do insertion either by appending records to the end of the file or by replacing deleted records.

**11.9.2 Efficient Implementation of Bitmap Operations**

We can compute the intersection of two bitmaps easily by using a **for** loop: the _i_th iteration of the loop computes the **and** of the _i_th bits of the two bitmaps. We can speed up computation of the intersection greatly by using bit-wise **and** instruc- tions supported by most computer instruction sets. A _word_ usually consists of 32 or 64 bits, depending on the architecture of the computer. A bit-wise **and** instruc- tion takes two words as input and outputs a word where each bit is the logical **and** of the bits in corresponding positions of the input words. What is important to note is that a single bit-wise **and** instruction can compute the intersection of 32 or 64 bits _at once_.

If a relation had 1 million records, each bitmap would contain 1 million bits, or equivalently 128 kilobytes. Only 31,250 instructions are needed to compute the intersection of two bitmaps for our relation, assuming a 32-bit word length. Thus, computing bitmap intersections is an extremely fast operation.

Just as bitmap intersection is useful for computing the **and** of two conditions, bitmap union is useful for computing the **or** of two conditions. The procedure for bitmap union is exactly the same as for intersection, except we use bit-wise **or** instructions instead of bit-wise **and** instructions.

The complement operation can be used to compute a predicate involving the negation of a condition, such as **not** (_income-level_ \= _L_1). The complement of a bitmap is generated by complementing every bit of the bitmap (the complement of 1 is 0 and the complement of 0 is 1). It may appear that **not** (_income level_ \= _L_1) can be implemented by just computing the complement of the bitmap for income level _L_1\. If some records have been deleted, however, just computing the complement of a bitmap is not sufficient. Bits corresponding to such records would be 0 in the original bitmap, but would become 1 in the complement, although the records don’t exist. A similar problem also arises when the value of an attribute is _null_. For instance, if the value of _income level_ is null, the bit would be 0 in the original bitmap for value _L_1, and 1 in the complement bitmap.

To make sure that the bits corresponding to deleted records are set to 0 in the result, the complement bitmap must be intersected with the existence bitmap to turn off the bits for deleted records. Similarly, to handle null values, the complement bitmap must also be intersected with the complement of the bitmap for the value _null_.2

Counting the number of bits that are 1 in a bitmap can be done quickly by a clever technique. We can maintain an array with 256 entries, where the _i_th entry stores the number of bits that are 1 in the binary representation of _i_ . Set the total count initially to 0. We take each byte of the bitmap, use it to index into this array, and add the stored count to the total count. The number of addition operations is

2Handling predicates such as **is unknown** would cause further complications, which would in general require use of an extra bitmap to track which operation results are unknown.  

**528 Chapter 11 Indexing and Hashing**

1 8 of the number of tuples, and thus the counting process is very efficient. A large array (using 216 = 65,536 entries), indexed by pairs of bytes, would give even higher speedup, but at a higher storage cost.

**11.9.3 Bitmaps and B+-Trees**

Bitmaps can be combined with regular B+-tree indices for relations where a few attribute values are extremely common, and other values also occur, but much less frequently. In a B+-tree index leaf, for each value we would normally maintain a list of all records with that value for the indexed attribute. Each element of the list would be a record identifier, consisting of at least 32 bits, and usually more. For a value that occurs in many records, we store a bitmap instead of a list of records.

Suppose a particular value _vi_ occurs in 1 16 of the records of a relation. Let _N_

be the number of records in the relation, and assume that a record has a 64-bit number identifying it. The bitmap needs only 1 bit per record, or _N_ bits in total. In contrast, the list representation requires 64 bits per record where the value occurs, or 64 ∗ _N/_16 = 4_N_ bits. Thus, a bitmap is preferable for representing the list of records for value _vi_ . In our example (with a 64-bit record identifier), if fewer than 1 in 64 records have a particular value, the list representation is preferable for identifying records with that value, since it uses fewer bits than the bitmap representation. If more than 1 in 64 records have that value, the bitmap representation is preferable.

Thus, bitmaps can be used as a compressed storage mechanism at the leaf nodes of B+-trees for those values that occur very frequently.

**11.10 Index Definition in SQL**

The SQL standard does not provide any way for the database user or administra- tor to control what indices are created and maintained in the database system. Indices are not required for correctness, since they are redundant data structures. However, indices are important for efficient processing of transactions, includ- ing both update transactions and queries. Indices are also important for efficient enforcement of integrity constraints.

In principle, a database system can decide automatically what indices to create. However, because of the space cost of indices, as well as the effect of indices on update processing, it is not easy to automatically make the right choices about what indices to maintain. Therefore, most SQL implementations provide the programmer control over creation and removal of indices via data-definition- language commands.

We illustrate the syntax of these commands next. Although the syntax that we show is widely used and supported by many database systems, it is not part of the SQL standard. The SQL standard does not support control of the physical database schema; it restricts itself to the logical database schema.

We create an index with the **create index** command, which takes the form:  

**11.11 Summary 529**

**create index** _<_index-name_\>_ **on** _<_relation-name_\>_ (_<_attribute-list_\>_);

The _attribute-list_ is the list of attributes of the relations that form the search key for the index.

To define an index named _dept index_ on the _instructor_ relation with _dept name_ as the search key, we write:

**create index** _dept index_ **on** _instructor_ (_dept name_);

If we wish to declare that the search key is a candidate key, we add the attribute **unique** to the index definition. Thus, the command:

**create unique index** _dept index_ **on** _instructor_ (_dept name_);

declares _dept name_ to be a candidate key for _instructor_ (which is probably not what we actually would want for our university database). If, at the time we enter the **create unique index** command, _dept name_ is not a candidate key, the system will display an error message, and the attempt to create the index will fail. If the index-creation attempt succeeds, any subsequent attempt to insert a tuple that violates the key declaration will fail. Note that the **unique** feature is redundant if the database system supports the **unique** declaration of the SQL standard.

Many database systems also provide a way to specify the type of index to be used (such as B+-tree or hashing). Some database systems also permit one of the indices on a relation to be declared to be clustered; the system then stores the relation sorted by the search-key of the clustered index.

The index name we specified for an index is required to drop an index. The **drop index** command takes the form:

**drop index** _<_index-name_\>_;

**11.11 Summary**

• Many queries reference only a small proportion of the records in a file. To reduce the overhead in searching for these records, we can construct _indices_ for the files that store the database.

• Index-sequential files are one of the oldest index schemes used in database systems. To permit fast retrieval of records in search-key order, records are stored sequentially, and out-of-order records are chained together. To allow fast random access, we use an index structure.

• There are two types of indices that we can use: dense indices and sparse indices. Dense indices contain entries for every search-key value, whereas sparse indices contain entries only for some search-key values.  

**530 Chapter 11 Indexing and Hashing**

• If the sort order of a search key matches the sort order of a relation, an index on the search key is called a _clustering index_. The other indices are called _nonclustering_ or _secondary indices_. Secondary indices improve the performance of queries that use search keys other than the search key of the clustering index. However, they impose an overhead on modification of the database.

• The primary disadvantage of the index-sequential file organization is that performance degrades as the file grows. To overcome this deficiency, we can use a B+-_tree index_.

• A B+-tree index takes the form of a _balanced_ tree, in which every path from the root of the tree to a leaf of the tree is of the same length. The height of a B+-tree is proportional to the logarithm to the base _N_ of the number of records in the relation, where each nonleaf node stores _N_ pointers; the value of _N_ is often around 50 or 100. B+-trees are much shorter than other balanced binary-tree structures such as AVL trees, and therefore require fewer disk accesses to locate records.

• Lookup on B+-trees is straightforward and efficient. Insertion and deletion, however, are somewhat more complicated, but still efficient. The number of operations required for lookup, insertion, and deletion on B+-trees is propor- tional to the logarithm to the base _N_ of the number of records in the relation, where each nonleaf node stores _N_ pointers.

• We can use B+-trees for indexing a file containing records, as well as to organize records into a file.

• B-tree indices are similar to B+-tree indices. The primary advantage of a B-tree is that the B-tree eliminates the redundant storage of search-key values. The major disadvantages are overall complexity and reduced fanout for a given node size. System designers almost universally prefer B+-tree indices over B-tree indices in practice.

• Sequential file organizations require an index structure to locate data. File organizations based on hashing, by contrast, allow us to find the address of a data item directly by computing a function on the search-key value of the desired record. Since we do not know at design time precisely which search- key values will be stored in the file, a good hash function to choose is one that assigns search-key values to buckets such that the distribution is both uniform and random.

• _Static hashing_ uses hash functions in which the set of bucket addresses is fixed. Such hash functions cannot easily accommodate databases that grow significantly larger over time. There are several _dynamic hashing techniques_ that allow the hash function to be modified. One example is _extendable hashing_, which copes with changes in database size by splitting and coalescing buckets as the database grows and shrinks.  

**Review Terms 531**

• We can also use hashing to create secondary indices; such indices are called _hash indices_. For notational convenience, we assume hash file organizations have an implicit hash index on the search key used for hashing.

• Ordered indices such as B+-trees and hash indices can be used for selec- tions based on equality conditions involving single attributes. When multi- ple attributes are involved in a selection condition, we can intersect record identifiers retrieved from multiple indices.

• Bitmap indices provide a very compact representation for indexing attributes with very few distinct values. Intersection operations are extremely fast on bitmaps, making them ideal for supporting queries on multiple attributes.

**Review Terms**

• Access types • Access time • Insertion time • Deletion time • Space overhead • Ordered index • Clustering index • Primary index • Nonclustering index • Secondary index • Index-sequential file • Index entry/record • Dense index • Sparse index • Multilevel index • Composite key • Sequential scan • B+-tree index • Leaf node • Nonleaf node • Balanced tree • Range query • Node split • Node coalesce

• Nonunique search key • B+-tree file organization • Bulk load • Bottom-up B+-tree construction • B-tree index • Static hashing • Hash file organization • Hash index • Bucket • Hash function • Bucket overflow • Skew • Closed hashing • Dynamic hashing • Extendable hashing • Multiple-key access • Indices on multiple keys • Bitmap index • Bitmap operations

◦ Intersection

◦ Union

◦ Complement

◦ Existence bitmap  

**532 Chapter 11 Indexing and Hashing**

**Practice Exercises**

**11.1** Indices speed query processing, but it is usually a bad idea to create indices on every attribute, and every combinations of attributes, that is a potential search keys. Explain why.

**11.2** Is it possible in general to have two clustering indices on the same relation for different search keys? Explain your answer.

**11.3** Construct a B+-tree for the following set of key values:

(2, 3, 5, 7, 11, 17, 19, 23, 29, 31)

Assume that the tree is initially empty and values are added in ascending order. Construct B+-trees for the cases where the number of pointers that will fit in one node is as follows:

a. Four

b. Six

c. Eight

**11.4** For each B+-tree of Practice Exercise 11.3, show the form of the tree after each of the following series of operations:

a. Insert 9.

b. Insert 10.

c. Insert 8.

d. Delete 23.

e. Delete 19.

**11.5** Consider the modified redistribution scheme for B+-trees described on page 501. What is the expected height of the tree as a function of _n_?

**11.6** Suppose that we are using extendable hashing on a file that contains records with the following search-key values:

2, 3, 5, 7, 11, 17, 19, 23, 29, 31

Show the extendable hash structure for this file if the hash function is _h_(_x_) = _x_ mod 8 and buckets can hold three records.

**11.7** Show how the extendable hash structure of Practice Exercise 11.6 changes as the result of each of the following steps:

a. Delete 11.

b. Delete 31.  

**Practice Exercises 533**

c. Insert 1.

d. Insert 15.

**11.8** Give pseudocode for a B+-tree function findIterator(), which is like the function find(), except that it returns an iterator object, as described in Section 11.3.2. Also give pseudocode for the iterator class, including the variables in the iterator object, and the next() method.

**11.9** Give pseudocode for deletion of entries from an extendable hash structure, including details of when and how to coalesce buckets. Do not bother about reducing the size of the bucket address table.

**11.10** Suggest an efficient way to test if the bucket address table in extendable hashing can be reduced in size, by storing an extra count with the bucket address table. Give details of how the count should be maintained when buckets are split, coalesced, or deleted. (_Note_: Reducing the size of the bucket address table is an expensive operation, and subsequent inserts may cause the table to grow again. Therefore, it is best not to reduce the size as soon as it is possible to do so, but instead do it only if the number of index entries becomes small compared to the bucket-address-table size.)

**11.11** Consider the _instructor_ relation shown in Figure 11.1.

a. Construct a bitmap index on the attribute _salary_, dividing _salary_ values into 4 ranges: below 50000, 50000 to below 60000, 60000 to below 70000, and 70000 and above.

b. Consider a query that requests all instructors in the Finance depart- ment with salary of 80000 or more. Outline the steps in answering the query, and show the final and intermediate bitmaps constructed to answer the query.

**11.12** What would the occupancy of each leaf node of a B+-tree be, if index entries are inserted in sorted order? Explain why.

**11.13** Suppose you have a relation _r_ with _nr_ tuples on which a secondary B+-tree is to be constructed.

a. Give a formula for the cost of building the B+-tree index by inserting one record at a time. Assume each block will hold an average of _f_ entries, and that all levels of the tree above the leaf are in memory.

b. Assuming a random disk access takes 10 milliseconds, what is the cost of index construction on a relation with 10 million records?

c. Write pseudocode for bottom-up construction of a B+-tree, which was outlined in Section 11.4.4. You can assume that a function to efficiently sort a large file is available.

**11.14** Why might the leaf nodes of a B+-tree file organization lose sequentiality?  

**534 Chapter 11 Indexing and Hashing**

a. Suggest how the file organization may be reorganized to restore sequentiality.

b. An alternative to reorganization is to allocate leaf pages in units of _n_ blocks, for some reasonably large _n_. When the first leaf of a B+- tree is allocated, only one block of an _n_\-block unit is used, and the remaining pages are free. If a page splits, and its _n_\-block unit has a free page, that space is used for the new page. If the _n_\-block unit is full, another _n_\-block unit is allocated, and the first _n/_2 leaf pages are placed in one _n_\-block unit, and the remaining in the second _n_\-block unit. For simplicity, assume that there are no delete operations. i. What is the worst case occupancy of allocated space, assuming

no delete operations, after the first _n_\-block unit is full. ii. Is it possible that leaf nodes allocated to an _n_\-node block unit

are not consecutive, that is, is it possible that two leaf nodes are allocated to one _n_\-node block, but another leaf node in between the two is allocated to a different _n_\-node block?

iii. Under the reasonable assumption that buffer space is sufficient to store a _n_\-page block, how many seeks would be required for a leaf-level scan of the B+-tree, in the worst case? Compare this number with the worst case if leaf pages are allocated a block at a time.

iv. The technique of redistributing values to siblings to improve space utilization is likely to be more efficient when used with the above allocation scheme for leaf blocks. Explain why.

**Exercises**

**11.15** When is it preferable to use a dense index rather than a sparse index? Explain your answer.

**11.16** What is the difference between a clustering index and a secondary index?

**11.17** For each B+-tree of Practice Exercise 11.3, show the steps involved in the following queries:

a. Find records with a search-key value of 11.

b. Find records with a search-key value between 7 and 17, inclusive.

**11.18** The solution presented in Section 11.3.4 to deal with nonunique search keys added an extra attribute to the search key. What effect could this change have on the height of the B+-tree?

**11.19** Explain the distinction between closed and open hashing. Discuss the relative merits of each technique in database applications.  

**Exercises 535**

**11.20** What are the causes of bucket overflow in a hash file organization? What can be done to reduce the occurrence of bucket overflows?

**11.21** Why is a hash structure not the best choice for a search key on which range queries are likely?

**11.22** Suppose there is a relation _r_ (_A, B, C_), with a B+-tree index with search key (_A, B_).

a. What is the worst-case cost of finding records satisfying 10 _< A <_ 50 using this index, in terms of the number of records retrieved _n_1 and the height _h_ of the tree?

b. What is the worst-case cost of finding records satisfying 10 _< A <_

50 ∧ 5 _< B <_ 10 using this index, in terms of the number of records _n_2 that satisfy this selection, as well as _n_1 and _h_ defined above?

c. Under what conditions on _n_1 and _n_2 would the index be an efficient way of finding records satisfying 10 _< A <_ 50 ∧ 5 _< B <_ 10?

**11.23** Suppose you have to create a B+-tree index on a large number of names, where the maximum size of a name may be quite large (say 40 characters) and the average name is itself large (say 10 characters). Explain how prefix compression can be used to maximize the average fanout of nonleaf nodes.

**11.24** Suppose a relation is stored in a B+-tree file organization. Suppose sec- ondary indices stored record identifiers that are pointers to records on disk.

a. What would be the effect on the secondary indices if a node split happens in the file organization?

b. What would be the cost of updating all affected records in a sec- ondary index?

c. How does using the search key of the file organization as a logical record identifier solve this problem?

d. What is the extra cost due to the use of such logical record identifiers?

**11.25** Show how to compute existence bitmaps from other bitmaps. Make sure that your technique works even in the presence of null values, by using a bitmap for the value _null_.

**11.26** How does data encryption affect index schemes? In particular, how might it affect schemes that attempt to store data in sorted order?

**11.27** Our description of static hashing assumes that a large contiguous stretch of disk blocks can be allocated to a static hash table. Suppose you can allocate only _C_ contiguous blocks. Suggest how to implement the hash table, if it can be much larger than _C_ blocks. Access to a block should still be efficient.  

**536 Chapter 11 Indexing and Hashing**

**Bibliographical Notes**

Discussions of the basic data structures in indexing and hashing can be found in Cormen et al. \[1990\]. B-tree indices were first introduced in Bayer \[1972\] and Bayer and McCreight \[1972\]. B+-trees are discussed in Comer \[1979\], Bayer and Unterauer \[1977\], and Knuth \[1973\]. The bibliographical notes in Chapter 15 provide references to research on allowing concurrent accesses and updates on B+-trees. Gray and Reuter \[1993\] provide a good description of issues in the implementation of B+-trees.

Several alternative tree and treelike search structures have been proposed. **Tries** are trees whose structure is based on the “digits” of keys (for example, a dictionary thumb index, which has one entry for each letter). Such trees may not be balanced in the sense that B+-trees are. Tries are discussed by Ramesh et al. \[1989\], Orenstein \[1982\], Litwin \[1981\], and Fredkin \[1960\]. Related work includes the digital B-trees of Lomet \[1981\].

Knuth \[1973\] analyzes a large number of different hashing techniques. Several dynamic hashing schemes exist. Extendable hashing was introduced by Fagin et al. \[1979\]. Linear hashing was introduced by Litwin \[1978\] and Litwin \[1980\]. A performance comparison with extendable hashing is given by Rathi et al. \[1990\]. An alternative given by Ramakrishna and Larson \[1989\] allows retrieval in a single disk access at the price of a high overhead for a small fraction of database modifications. Partitioned hashing is an extension of hashing to multiple attributes, and is covered in Rivest \[1976\], Burkhard \[1976\], and Burkhard \[1979\].

Vitter \[2001\] provides an extensive survey of external-memory data structures and algorithms.

Bitmap indices, and variants called **bit-sliced indices** and **projection indices**, are described in O’Neil and Quass \[1997\]. They were first introduced in the IBM Model 204 file manager on the AS 400 platform. They provide very large speedups on certain types of queries, and are today implemented on most database sys- tems. Research on bitmap indices includes Wu and Buchmann \[1998\], Chan and Ioannidis \[1998\], Chan and Ioannidis \[1999\], and Johnson \[1999\].  

**_C H A P T E R_12 Query Processing**

**Query processing** refers to the range of activities involved in extracting data from a database. The activities include translation of queries in high-level database languages into expressions that can be used at the physical level of the file system, a variety of query-optimizing transformations, and actual evaluation of queries.

**12.1 Overview**

The steps involved in processing a query appear in Figure 12.1. The basic steps are:

**1\.** Parsing and translation.

**2\.** Optimization.

**3\.** Evaluation.

Before query processing can begin, the system must translate the query into a usable form. A language such as SQL is suitable for human use, but is ill suited to be the system’s internal representation of a query. A more useful internal representation is one based on the extended relational algebra.

Thus, the first action the system must take in query processing is to translate a given query into its internal form. This translation process is similar to the work performed by the parser of a compiler. In generating the internal form of the query, the parser checks the syntax of the user’s query, verifies that the relation names appearing in the query are names of the relations in the database, and so on. The system constructs a parse-tree representation of the query, which it then translates into a relational-algebra expression. If the query was expressed in terms of a view, the translation phase also replaces all uses of the view by the relational-algebra expression that defines the view.1 Most compiler texts cover parsing in detail.

1For materialized views, the expression defining the view has already been evaluated and stored. Therefore, the stored relation can be used, instead of uses of the view being replaced by the expression defining the view. Recursive views are handled differently, via a fixed-point procedure, as discussed in Section 5.4 and Appendix B.3.6.

**537**  

**538 Chapter 12 Query Processing**

query output

query parser and translator

evaluation engine

relational-algebra expression

execution plan

optimizer

data statistics about data

**Figure 12.1** Steps in query processing.

Given a query, there are generally a variety of methods for computing the answer. For example, we have seen that, in SQL, a query could be expressed in several different ways. Each SQL query can itself be translated into a relational- algebra expression in one of several ways. Furthermore, the relational-algebra representation of a query specifies only partially how to evaluate a query; there are usually several ways to evaluate relational-algebra expressions. As an illustration, consider the query:

**select** _salary_ **from** _instructor_ **where** _salary <_ 75000;

This query can be translated into either of the following relational-algebra ex- pressions:

• _salary <_75000 (_salary_ (_instructor_ ))

• _salary_ (_salary<_75000 (_instructor_ ))

Further, we can execute each relational-algebra operation by one of several different algorithms. For example, to implement the preceding selection, we can search every tuple in _instructor_ to find tuples with salary less than 75000. If a B+-tree index is available on the attribute _salary_, we can use the index instead to locate the tuples.

To specify fully how to evaluate a query, we need not only to provide the relational-algebra expression, but also to annotate it with instructions specifying how to evaluate each operation. Annotations may state the algorithm to be used  

**12.1 Overview 539**

_salary_

_salary_ < 75000; use index 1

_instructor_

σ

π

**Figure 12.2** A query-evaluation plan.

for a specific operation, or the particular index or indices to use. A relational- algebra operation annotated with instructions on how to evaluate it is called an **evaluation primitive**. A sequence of primitive operations that can be used to evaluate a query is a **query-execution plan** or **query-evaluation plan**. Figure 12.2 illustrates an evaluation plan for our example query, in which a particular index (denoted in the figure as “index 1”) is specified for the selection operation. The **query-execution engine** takes a query-evaluation plan, executes that plan, and returns the answers to the query.

The different evaluation plans for a given query can have different costs. We do not expect users to write their queries in a way that suggests the most efficient evaluation plan. Rather, it is the responsibility of the system to construct a query- evaluation plan that minimizes the cost of query evaluation; this task is called _query optimization_. Chapter 13 describes query optimization in detail.

Once the query plan is chosen, the query is evaluated with that plan, and the result of the query is output.

The sequence of steps already described for processing a query is represen- tative; not all databases exactly follow those steps. For instance, instead of using the relational-algebra representation, several databases use an annotated parse- tree representation based on the structure of the given SQL query. However, the concepts that we describe here form the basis of query processing in databases.

In order to optimize a query, a query optimizer must know the cost of each operation. Although the exact cost is hard to compute, since it depends on many parameters such as actual memory available to the operation, it is possible to get a rough estimate of execution cost for each operation.

In this chapter we study how to evaluate individual operations in a query plan, and how to estimate their cost; we return to query optimization in Chap- ter 13. Section 12.2 outlines how we measure the cost of a query. Sections 12.3 through 12.6 cover the evaluation of individual relational-algebra operations. Several operations may be grouped together into a **pipeline**, in which each of the operations starts working on its input tuples even as they are being generated by another operation. In Section 12.7, we examine how to coordinate the execu- tion of multiple operations in a query evaluation plan, in particular, how to use pipelined operations to avoid writing intermediate results to disk.  

**540 Chapter 12 Query Processing**

**12.2 Measures of Query Cost**

There are multiple possible evaluation plans for a query, and it is important to be able to compare the alternatives in terms of their (estimated) cost, and choose the best plan. To do so, we must estimate the cost of individual operations, and combine them to get the cost of a query evaluation plan. Thus, as we study evaluation algorithms for each operation later in this chapter, we also outline how to estimate the cost of the operation.

The cost of query evaluation can be measured in terms of a number of dif- ferent resources, including disk accesses, CPU time to execute a query, and, in a distributed or parallel database system, the cost of communication (which we discuss later, in Chapters 18 and 19).

In large database systems, the cost to access data from disk is usually the most important cost, since disk accesses are slow compared to in-memory operations. Moreover, CPU speeds have been improving much faster than have disk speeds. Thus, it is likely that the time spent in disk activity will continue to dominate the total time to execute a query. The CPU time taken for a task is harder to estimate since it depends on low-level details of the execution code. Although real-life query optimizers do take CPU costs into account, for simplicity in this book we ignore CPU costs and use only disk-access costs to measure the cost of a query-evaluation plan.

We use the _number of block transfers_ from disk and the _number of disk seeks_ to estimate the cost of a query-evaluation plan. If the disk subsystem takes an average of _tT_ seconds to transfer a block of data, and has an average block-access time (disk seek time plus rotational latency) of _tS_ seconds, then an operation that transfers _b_ blocks and performs _S_ seeks would take _b_ ∗ _tT_ \+ _S_ ∗ _tS_ seconds. The values of _tT_ and _tS_ must be calibrated for the disk system used, but typical values for high-end disks today would be _tS_ \= 4 milliseconds and _tT_ \= 0_._1 milliseconds, assuming a 4-kilobyte block size and a transfer rate of 40 megabytes per second.2

We can refine our cost estimates further by distinguishing block reads from block writes, since block writes are typically about twice as expensive as reads (this is because disk systems read sectors back after they are written to verify that the write was successful). For simplicity, we ignore this detail, and leave it to you to work out more precise cost estimates for various operations.

The cost estimates we give do not include the cost of writing the final result of an operation back to disk. These are taken into account separately where required. The costs of all the algorithms that we consider depend on the size of the buffer in main memory. In the best case, all data can be read into the buffers, and the disk does not need to be accessed again. In the worst case, we assume that the buffer can hold only a few blocks of data—approximately one block per relation. When presenting cost estimates, we generally assume the worst case.

2Some database systems perform test seeks and block transfers to estimate average seek and block transfer costs, as part of the software installation process.  

**12.3 Selection Operation 541**

In addition, although we assume that data must be read from disk initially, it is possible that a block that is accessed is already present in the in-memory buffer. Again, for simplicity, we ignore this effect; as a result, the actual disk-access cost during the execution of a plan may be less than the estimated cost.

The **response time** for a query-evaluation plan (that is, the wall-clock time required to execute the plan), assuming no other activity is going on in the computer, would account for all these costs, and could be used as a measure of the cost of the plan. Unfortunately, the response time of a plan is very hard to estimate without actually executing the plan, for the following reasons:

**1\.** The response time depends on the contents of the buffer when the query begins execution; this information is not available when the query is opti- mized, and is hard to account for even if it were available.

**2\.** In a system with multiple disks, the response time depends on how accesses are distributed among disks, which is hard to estimate without detailed knowledge of data layout on disk.

Interestingly, a plan may get a better response time at the cost of extra resource consumption. For example, if a system has multiple disks, a plan _A_ that requires extra disk reads, but performs the reads in parallel across multiple disks may finish faster than another plan _B_ that has fewer disk reads, but from only one disk. However, if many instances of a query using plan _A_ run concurrently, the overall response time may actually be more than if the same instances are executed using plan _B_, since plan _A_ generates more load on the disks.

As a result, instead of trying to minimize the response time, optimizers gen- erally try to minimize the total **resource consumption** of a query plan. Our model of estimating the total disk access time (including seek and data transfer) is an example of such a resource consumption–based model of query cost.

**12.3 Selection Operation**

In query processing, the **file scan** is the lowest-level operator to access data. File scans are search algorithms that locate and retrieve records that fulfill a selection condition. In relational systems, a file scan allows an entire relation to be read in those cases where the relation is stored in a single, dedicated file.

**12.3.1 Selections Using File Scans and Indices**

Consider a selection operation on a relation whose tuples are stored together in one file. The most straightforward way of performing a selection is as follows:

• **A1** (**linear search**). In a linear search, the system scans each file block and tests all records to see whether they satisfy the selection condition. An initial seek is required to access the first block of the file. In case blocks of the file  

**542 Chapter 12 Query Processing**

are not stored contiguously, extra seeks may be required, but we ignore this effect for simplicity.

Although it may be slower than other algorithms for implementing selec- tion, the linear-search algorithm can be applied to any file, regardless of the ordering of the file, or the availability of indices, or the nature of the selection operation. The other algorithms that we shall study are not applicable in all cases, but when applicable they are generally faster than linear search.

Cost estimates for linear scan, as well as for other selection algorithms, are shown in Figure 12.3. In the figure, we use _hi_ to represent the height of the B+- tree. Real-life optimizers usually assume that the root of the tree is present in the in-memory buffer since it is frequently accessed. Some optimizers even assume that all but the leaf level of the tree is present in memory, since they are accessed relatively frequently, and usually less than 1 percent of the nodes of a B+-tree are nonleaf nodes. The cost formulae can be modified appropriately.

Index structures are referred to as **access paths**, since they provide a path through which data can be located and accessed. In Chapter 11, we pointed out that it is efficient to read the records of a file in an order corresponding closely to physical order. Recall that a _primary index_ (also referred to as a _clustering index_) is an index that allows the records of a file to be read in an order that corresponds to the physical order in the file. An index that is not a primary index is called a _secondary index_.

Search algorithms that use an index are referred to as **index scans**. We use the selection predicate to guide us in the choice of the index to use in processing the query. Search algorithms that use an index are:

• **A2** (**primary index, equality on key**). For an equality comparison on a key attribute with a primary index, we can use the index to retrieve a single record that satisfies the corresponding equality condition. Cost estimates are shown in Figure 12.3.

• **A3** (**primary index, equality on nonkey**). We can retrieve multiple records by using a primary index when the selection condition specifies an equality comparison on a nonkey attribute, _A_. The only difference from the previous case is that multiple records may need to be fetched. However, the records must be stored consecutively in the file since the file is sorted on the search key. Cost estimates are shown in Figure 12.3.

• **A4** (**secondary index, equality**). Selections specifying an equality condition can use a secondary index. This strategy can retrieve a single record if the equality condition is on a key; multiple records may be retrieved if the index- ing field is not a key.

In the first case, only one record is retrieved. The time cost in this case is the same as that for a primary index (case A2).

In the second case, each record may be resident on a different block, which may result in one I/O operation per retrieved record, with each I/O operation requiring a seek and a block transfer. The worst-case time cost in this case is  

**12.3 Selection Operation 543**

Algorithm Cost Reason A1 Linear Search _tS_ \+ _br_ ∗ _tT_ One initial seek plus _br_ block transfers,

where _br_ denotes the number of blocks in the file.

A1 Linear Search, Equality on Key

Average case _tS_ \+ (_br/_2) ∗ _tT_

Since at most one record satisfies con- dition, scan can be terminated as soon as the required record is found. In the worst case, _br_ blocks transfers are still required.

A2 Primary B+-tree Index, Equality on Key

(_hi_ \+ 1) ∗ (_tT_ \+ _tS_)

(Where _hi_ denotes the height of the in- dex.) Index lookup traverses the height of the tree plus one I/O to fetch the record; each of these I/O operations re- quires a seek and a block transfer.

A3 Primary B+-tree Index, Equality on Nonkey

_hi_ ∗ (_tT_ \+ _tS_) + _b_ ∗ _tT_

One seek for each level of the tree, one seek for the first block. Here _b_ is the number of blocks containing records with the specified search key, all of which are read. These blocks are leaf blocks assumed to be stored sequen- tially (since it is a primary index) and don’t require additional seeks.

A4 Secondary B+-tree Index, Equality on Key

(_hi_ \+ 1) ∗ (_tT_ \+ _tS_)

This case is similar to primary index.

A4 Secondary B+-tree Index, Equality on Nonkey

(_hi_ \+ _n_) ∗ (_tT_ \+ _tS_)

(Where _n_ is the number of records fetched.) Here, cost of index traversal is the same as for A3, but each record may be on a different block, requiring a seek per record. Cost is potentially very high if _n_ is large.

A5 Primary B+-tree Index, Comparison

_hi_ ∗ (_tT_ \+ _tS_) + _b_ ∗ _tT_

Identical to the case of A3, equality on nonkey.

A6 Secondary B+-tree Index, Comparison

(_hi_ \+ _n_) ∗ (_tT_ \+ _tS_)

Identical to the case of A4, equality on nonkey.

**Figure 12.3** Cost estimates for selection algorithms.

(_hi_ \+ _n_) ∗ (_tS_ \+ _tT_ ), where _n_ is the number of records fetched, if each record is in a different disk block, and the block fetches are randomly ordered. The worst-case cost could become even worse than that of linear search if a large number of records are retrieved.  

**544 Chapter 12 Query Processing**

If the in-memory buffer is large, the block containing the record may already be in the buffer. It is possible to construct an estimate of the _average_ or _expected_ cost of the selection by taking into account the probability of the block containing the record already being in the buffer. For large buffers, that estimate will be much less than the worst-case estimate.

In certain algorithms, including A2, the use of a B+-tree file organization can save one access since records are stored at the leaf-level of the tree.

As described in Section 11.4.2, when records are stored in a B+-tree file organi- zation or other file organizations that may require relocation of records, secondary indices usually do not store pointers to the records.3 Instead, secondary indices store the values of the attributes used as the search key in a B+-tree file organiza- tion. Accessing a record through such a secondary index is then more expensive: First the secondary index is searched to find the primary index search-key val- ues, then the primary index is looked up to find the records. The cost formulae described for secondary indices have to be modified appropriately if such indices are used.

**12.3.2 Selections Involving Comparisons**

Consider a selection of the form _A_≤_v_(_r_ ). We can implement the selection either by using linear search or by using indices in one of the following ways:

• **A5** (**primary index, comparison**). A primary ordered index (for example, a primary B+-tree index) can be used when the selection condition is a com- parison. For comparison conditions of the form _A > v_ or _A_ ≥ _v_, a primary index on _A_ can be used to direct the retrieval of tuples, as follows: For _A_ ≥ _v_, we look up the value _v_ in the index to find the first tuple in the file that has a value of _A_ \= _v_. A file scan starting from that tuple up to the end of the file returns all tuples that satisfy the condition. For _A > v_, the file scan starts with the first tuple such that _A > v_. The cost estimate for this case is identical to that for case A3.

For comparisons of the form _A < v_ or _A_ ≤ _v_, an index lookup is not required. For _A < v_, we use a simple file scan starting from the beginning of the file, and continuing up to (but not including) the first tuple with attribute _A_ \= _v_. The case of _A_ ≤ _v_ is similar, except that the scan continues up to (but not including) the first tuple with attribute _A > v_. In either case, the index is not useful.

• **A6** (**secondary index, comparison**). We can use a secondary ordered index to guide retrieval for comparison conditions involving _<,_ ≤_,_ ≥, or _\>_. The lowest-level index blocks are scanned, either from the smallest value up to _v_

(for _<_ and ≤), or from _v_ up to the maximum value (for _\>_ and ≥).

3Recall that if B+-tree file organizations are used to store relations, records may be moved between blocks when leaf nodes are split or merged, and when records are redistributed.  

**12.3 Selection Operation 545**

The secondary index provides pointers to the records, but to get the actual records we have to fetch the records by using the pointers. This step may require an I/O operation for each record fetched, since consecutive records may be on different disk blocks; as before, each I/O operation requires a disk seek and a block transfer. If the number of retrieved records is large, using the secondary index may be even more expensive than using linear search. Therefore the secondary index should be used only if very few records are selected.

**12.3.3 Implementation of Complex Selections**

So far, we have considered only simple selection conditions of the form _A op B_, where _op_ is an equality or comparison operation. We now consider more complex selection predicates.

• **Conjunction:** A _conjunctive selection_ is a selection of the form:

1∧2∧···∧_n_(_r_ )

• **Disjunction:** A _disjunctive selection_ is a selection of the form:

1∨2∨···∨_n_(_r_ )

A disjunctive condition is satisfied by the union of all records satisfying the individual, simple conditions _i_ .

• **Negation:** The result of a selection ¬(_r_ ) is the set of tuples of _r_ for which the condition  evaluates to false. In the absence of nulls, this set is simply the set of tuples in _r_ that are not in (_r_ ).

We can implement a selection operation involving either a conjunction or a disjunction of simple conditions by using one of the following algorithms:

• **A7** (**conjunctive selection using one index**). We first determine whether an access path is available for an attribute in one of the simple conditions. If one is, one of the selection algorithms A2 through A6 can retrieve records satisfying that condition. We complete the operation by testing, in the memory buffer, whether or not each retrieved record satisfies the remaining simple conditions.

To reduce the cost, we choose a _i_ and one of algorithms A1 through A6 for which the combination results in the least cost for _i_ (_r_ ). The cost of algorithm A7 is given by the cost of the chosen algorithm.

• **A8** (**conjunctive selection using composite index**). An appropriate _composite index_ (that is, an index on multiple attributes) may be available for some conjunctive selections. If the selection specifies an equality condition on two or more attributes, and a composite index exists on these combined attribute  

**546 Chapter 12 Query Processing**

fields, then the index can be searched directly. The type of index determines which of algorithms A2, A3, or A4 will be used.

• **A9** (**conjunctive selection by intersection of identifiers).** Another alterna- tive for implementing conjunctive selection operations involves the use of record pointers or record identifiers. This algorithm requires indices with record pointers, on the fields involved in the individual conditions. The al- gorithm scans each index for pointers to tuples that satisfy an individual condition. The intersection of all the retrieved pointers is the set of pointers to tuples that satisfy the conjunctive condition. The algorithm then uses the pointers to retrieve the actual records. If indices are not available on all the individual conditions, then the algorithm tests the retrieved records against the remaining conditions.

The cost of algorithm **A9** is the sum of the costs of the individual index scans, plus the cost of retrieving the records in the intersection of the retrieved lists of pointers. This cost can be reduced by sorting the list of pointers and retrieving records in the sorted order. Thereby, (1) all pointers to records in a block come together, hence all selected records in the block can be retrieved using a single I/O operation, and (2) blocks are read in sorted order, minimizing disk-arm movement. Section 12.4 describes sorting algorithms.

• **A10** (**disjunctive selection by union of identifiers).** If access paths are avail- able on all the conditions of a disjunctive selection, each index is scanned for pointers to tuples that satisfy the individual condition. The union of all the retrieved pointers yields the set of pointers to all tuples that satisfy the disjunctive condition. We then use the pointers to retrieve the actual records.

However, if even one of the conditions does not have an access path, we have to perform a linear scan of the relation to find tuples that satisfy the condition. Therefore, if there is even one such condition in the disjunct, the most efficient access method is a linear scan, with the disjunctive condition tested on each tuple during the scan.

The implementation of selections with negation conditions is left to you as an exercise (Practice Exercise 12.6).

**12.4 Sorting**

Sorting of data plays an important role in database systems for two reasons. First, SQL queries can specify that the output be sorted. Second, and equally important for query processing, several of the relational operations, such as joins, can be implemented efficiently if the input relations are first sorted. Thus, we discuss sorting here before discussing the join operation in Section 12.5.

We can sort a relation by building an index on the sort key, and then using that index to read the relation in sorted order. However, such a process orders the relation only _logically_, through an index, rather than _physically_. Hence, the reading of tuples in the sorted order may lead to a disk access (disk seek plus  

**12.4 Sorting 547**

block transfer) for each record, which can be very expensive, since the number of records can be much larger than the number of blocks. For this reason, it may be desirable to order the records physically.

The problem of sorting has been studied extensively, both for relations that fit entirely in main memory and for relations that are bigger than memory. In the first case, standard sorting techniques such as quick-sort can be used. Here, we discuss how to handle the second case.

**12.4.1 External Sort-Merge Algorithm**

Sorting of relations that do not fit in memory is called **external sorting**. The most commonly used technique for external sorting is the **external sort–merge** algorithm. We describe the external sort–merge algorithm next. Let _M_ denote the number of blocks in the main-memory buffer available for sorting, that is, the number of disk blocks whose contents can be buffered in available main memory.

**1\.** In the first stage, a number of sorted **runs** are created; each run is sorted, but contains only some of the records of the relation.

_i_ \= 0; **repeat**

read _M_ blocks of the relation, or the rest of the relation, whichever is smaller;

sort the in-memory part of the relation; write the sorted data to run file _Ri_ ; _i_ \= _i_ \+ 1;

**until** the end of the relation

**2\.** In the second stage, the runs are _merged_. Suppose, for now, that the total number of runs, _N_, is less than _M_, so that we can allocate one block to each run and have space left to hold one block of output. The merge stage operates as follows:

read one block of each of the _N_ files _Ri_ into a buffer block in memory; **repeat**

choose the first tuple (in sort order) among all buffer blocks; write the tuple to the output, and delete it from the buffer block; **if** the buffer block of any run _Ri_ is empty **and not** end-of-file(Ri )

**then** read the next block of _Ri_ into the buffer block; **until** all input buffer blocks are empty

The output of the merge stage is the sorted relation. The output file is buffered to reduce the number of disk write operations. The preceding merge operation is a generalization of the two-way merge used by the standard in-memory sort– merge algorithm; it merges _N_ runs, so it is called an _N_**\-way merge**.  

**548 Chapter 12 Query Processing**

g a

d 31

c 33

b 14

e 16

r 16

d 21

m 3

p 2

d 7

a 14

a 14

a 19

b 14

c 33

d 7

d 21

d 31

e 16

g 24

m 3

p 2

r 16

a 19

b 14

c 33

d 31

e 16

g 24

a 14

d 7

d 21

m 3

p 2

r 16

a 19

d 31

g 24

b 14

c 33

e 16

d 21

m 3

r 16

a 14

d 7

p 2 initial

relation create runs

merge pass–1

merge pass–2

runs runs sorted output

24

19

**Figure 12.4** External sorting using sort–merge.

In general, if the relation is much larger than memory, there may be _M_ or more runs generated in the first stage, and it is not possible to allocate a block for each run during the merge stage. In this case, the merge operation proceeds in multiple passes. Since there is enough memory for _M_ − 1 input buffer blocks, each merge can take _M_ − 1 runs as input.

The initial _pass_ functions in this way: It merges the first _M_ − 1 runs (as desc- ribed in item 2 above) to get a single run for the next pass. Then, it merges the next _M_ − 1 runs similarly, and so on, until it has processed all the initial runs. At this point, the number of runs has been reduced by a factor of _M_ − 1. If this reduced number of runs is still greater than or equal to _M_, another pass is made, with the runs created by the first pass as input. Each pass reduces the number of runs by a factor of _M_ − 1. The passes repeat as many times as required, until the number of runs is less than _M_; a final pass then generates the sorted output.

Figure 12.4 illustrates the steps of the external sort–merge for an example relation. For illustration purposes, we assume that only one tuple fits in a block ( _fr_ \= 1), and we assume that memory holds at most three blocks. During the merge stage, two blocks are used for input and one for output.

**12.4.2 Cost Analysis of External Sort-Merge**

We compute the disk-access cost for the external sort–merge in this way: Let _br_ denote the number of blocks containing records of relation _r_ . The first stage  

**12.5 Join Operation 549**

reads every block of the relation and writes them out again, giving a total of 2_br_ block transfers. The initial number of runs is _br/M_. Since the number of runs decreases by a factor of _M_ − 1 in each merge pass, the total number of merge passes required is log_M_−1(_br/M_). Each of these passes reads every block of the relation once and writes it out once, with two exceptions. First, the final pass can produce the sorted output without writing its result to disk. Second, there may be runs that are not read in or written out during a pass—for example, if there are _M_ runs to be merged in a pass, _M_ − 1 are read in and merged, and one run is not accessed during the pass. Ignoring the (relatively small) savings due to the latter effect, the total number of block transfers for external sorting of the relation is:

_br_ (2log_M_−1(_br/M_) + 1)

Applying this equation to the example in Figure 12.4, we get a total of 12 ∗ (4 + 1) = 60 block transfers, as you can verify from the figure. Note that the above numbers do not include the cost of writing out the final result.

We also need to add the disk-seek costs. Run generation requires seeks for reading data for each of the runs as well as for writing the runs. During the merge phase, if data are read _bb_ blocks at a time from each run (that is, _bb_ buffer blocks are allocated to each run), then each merge pass would require around _br /bb_ seeks for reading data.4 Although the output is written sequentially, if it is on the same disk as the input runs the head may have moved away between writes of consecutive blocks. Thus we would have to add a total of 2_br /bb_ seeks for each merge pass, except the final pass (since we assume the final result is not written back to disk). The total number of seeks is then:

2_br/M_ + _br/bb_(2log_M_−1(_br/M_) − 1)

Applying this equation to the example in Figure 12.4, we get a total of 8 + 12 ∗ (2 ∗ 2 − 1) = 44 disk seeks if we set the number of buffer blocks per run, _bb_ to 1.

**12.5 Join Operation**

In this section, we study several algorithms for computing the join of relations, and we analyze their respective costs.

We use the term **equi-join** to refer to a join of the form _r r.A_\=_s.B s_, where _A_ and _B_ are attributes or sets of attributes of relations _r_ and _s_, respectively.

We use as a running example the expression:

_student  takes_

4To be more precise, since we read each run separately and may get fewer than _bb_ blocks when reading the end of a run, we may require an extra seek for each run. We ignore this detail for simplicity.  

**550 Chapter 12 Query Processing**

using the same relation schemas that we used in Chapter 2. We assume the following information about the two relations:

• Number of records of _student_: _nstudent_ \= 5_,_ 000.

• Number of blocks of _student_: _bstudent_ \= 100.

• Number of records of _takes_: _ntakes_ \= 10_,_ 000.

• Number of blocks of _takes_: _btakes_ \= 400.

**12.5.1 Nested-Loop Join**

Figure 12.5 shows a simple algorithm to compute the theta join, _r _ _s_, of two relations _r_ and _s_. This algorithm is called the **nested-loop join** algorithm, since it basically consists of a pair of nested **for** loops. Relation _r_ is called the **outer relation** and relation _s_ the **inner relation** of the join, since the loop for _r_ encloses the loop for _s_. The algorithm uses the notation _tr_ · _ts_ , where _tr_ and _ts_ are tuples; _tr_ · _ts_ denotes the tuple constructed by concatenating the attribute values of tuples _tr_ and _ts_ .

Like the linear file-scan algorithm for selection, the nested-loop join algorithm requires no indices, and it can be used regardless of what the join condition is. Extending the algorithm to compute the natural join is straightforward, since the natural join can be expressed as a theta join followed by elimination of repeated attributes by a projection. The only change required is an extra step of deleting repeated attributes from the tuple _tr_ · _ts_ , before adding it to the result.

The nested-loop join algorithm is expensive, since it examines every pair of tuples in the two relations. Consider the cost of the nested-loop join algorithm. The number of pairs of tuples to be considered is _nr_ ∗ _ns_ , where _nr_ denotes the number of tuples in _r_ , and _ns_ denotes the number of tuples in _s_. For each record in _r_ , we have to perform a complete scan on _s_. In the worst case, the buffer can hold only one block of each relation, and a total of _nr_ ∗ _bs_ \+ _br_ block transfers would be required, where _br_ and _bs_ denote the number of blocks containing tuples of _r_ and _s_, respectively. We need only one seek for each scan on the inner relation _s_ since it is read sequentially, and a total of _br_ seeks to read _r_ , leading to a total of _nr_ \+ _br_ seeks. In the best case, there is enough space for both relations to fit simultaneously in memory, so each block would have to be read only once; hence, only _br_ \+ _bs_ block transfers would be required, along with 2 seeks.

**for each** tuple _tr_ **in** _r_ **do begin for each** tuple _ts_ **in** _s_ **do begin**

test pair (_tr , ts_) to see if they satisfy the join condition  if they do, add _tr_ · _ts_ to the result;

**end end**

**Figure 12.5** Nested-loop join.  

**12.5 Join Operation 551**

**for each** block _Br_ **of** _r_ **do begin for each** block _Bs_ **of** _s_ **do begin**

**for each** tuple _tr_ **in** _Br_ **do begin for each** tuple _ts_ **in** _Bs_ **do begin**

test pair (_tr , ts_) to see if they satisfy the join condition if they do, add _tr_ · _ts_ to the result;

**end end**

**end end**

**Figure 12.6** Block nested-loop join.

If one of the relations fits entirely in main memory, it is beneficial to use that relation as the inner relation, since the inner relation would then be read only once. Therefore, if _s_ is small enough to fit in main memory, our strategy requires only a total _br_ \+ _bs_ block transfers and 2 seeks—the same cost as that for the case where both relations fit in memory.

Now consider the natural join of _student_ and _takes_. Assume for now that we have no indices whatsoever on either relation, and that we are not willing to create any index. We can use the nested loops to compute the join; assume that _student_ is the outer relation and _takes_ is the inner relation in the join. We will have to examine 5000 ∗ 10_,_ 000 = 50 ∗ 106 pairs of tuples. In the worst case, the number of block transfers is 5000∗400+100 = 2,000,100, plus 5000+100 = 5100 seeks. In the best- case scenario, however, we can read both relations only once, and perform the computation. This computation requires at most 100 + 400 = 500 block transfers, plus 2 seeks—a significant improvement over the worst-case scenario. If we had used _takes_ as the relation for the outer loop and _student_ for the inner loop, the worst-case cost of our final strategy would have been 10,000 ∗ 100 + 400 = 1,000,400 block transfers, plus 10,400 disk seeks. The number of block transfers is significantly less, and although the number of seeks is higher, the overall cost is reduced, assuming _tS_ \= 4 milliseconds and _tT_ \= 0_._1 milliseconds.

**12.5.2 Block Nested-Loop Join**

If the buffer is too small to hold either relation entirely in memory, we can still obtain a major saving in block accesses if we process the relations on a per-block basis, rather than on a per-tuple basis. Figure 12.6 shows **block nested-loop join**, which is a variant of the nested-loop join where every block of the inner relation is paired with every block of the outer relation. Within each pair of blocks, every tuple in one block is paired with every tuple in the other block, to generate all pairs of tuples. As before, all pairs of tuples that satisfy the join condition are added to the result.

The primary difference in cost between the block nested-loop join and the basic nested-loop join is that, in the worst case, each block in the inner relation _s_ is read only once for each _block_ in the outer relation, instead of once for each _tuple_  

**552 Chapter 12 Query Processing**

in the outer relation. Thus, in the worst case, there will be a total of _br_ ∗ _bs_ \+ _br_ block transfers, where _br_ and _bs_ denote the number of blocks containing records of _r_ and _s_, respectively. Each scan of the inner relation requires one seek, and the scan of the outer relation requires one seek per block, leading to a total of 2 ∗ _br_ seeks. Clearly, it is more efficient to use the smaller relation as the outer relation, in case neither of the relations fits in memory. In the best case, where the inner relation fits in memory, there will be _br_ \+ _bs_ block transfers and just 2 seeks (we would choose the smaller relation as the inner relation in this case).

Now return to our example of computing _student  takes_ , using the block nested-loop join algorithm. In the worst case, we have to read each block of _takes_ once for each block of _student_. Thus, in the worst case, a total of 100 ∗ 400 + 100 = 40,100 block transfers plus 2∗100 = 200 seeks are required. This cost is a significant improvement over the 5000∗400+100 = 2,000,100 block transfers plus 5100 seeks needed in the worst case for the basic nested-loop join. The best-case cost remains the same—namely, 100 + 400 = 500 block transfers and 2 seeks.

The performance of the nested-loop and block nested-loop procedures can be further improved:

• If the join attributes in a natural join or an equi-join form a key on the inner relation, then for each outer relation tuple the inner loop can terminate as soon as the first match is found.

• In the block nested-loop algorithm, instead of using disk blocks as the block- ing unit for the outer relation, we can use the biggest size that can fit in memory, while leaving enough space for the buffers of the inner relation and the output. In other words, if memory has _M_ blocks, we read in _M_− 2 blocks of the outer relation at a time, and when we read each block of the inner relation we join it with all the _M_ − 2 blocks of the outer relation. This change reduces the number of scans of the inner relation from _br_ to _br /_(_M_ − 2), where _br_ is the number of blocks of the outer relation. The total cost is then _br/_(_M_ − 2) ∗ _bs_ \+ _br_ block transfers and 2_br/_(_M_ − 2) seeks.

• We can scan the inner loop alternately forward and backward. This scanning method orders the requests for disk blocks so that the data remaining in the buffer from the previous scan can be reused, thus reducing the number of disk accesses needed.

• If an index is available on the inner loop’s join attribute, we can replace file scans with more efficient index lookups. Section 12.5.3 describes this optimization.

**12.5.3 Indexed Nested-Loop Join**

In a nested-loop join (Figure 12.5), if an index is available on the inner loop’s join attribute, index lookups can replace file scans. For each tuple _tr_ in the outer relation _r_, the index is used to look up tuples in _s_ that will satisfy the join condition with tuple _tr_ .  

**12.5 Join Operation 553**

This join method is called an **indexed nested-loop join**; it can be used with existing indices, as well as with temporary indices created for the sole purpose of evaluating the join.

Looking up tuples in _s_ that will satisfy the join conditions with a given tuple _tr_ is essentially a selection on _s_. For example, consider _student  takes_ . Suppose that we have a _student_ tuple with _ID_ “00128”. Then, the relevant tuples in _takes_ are those that satisfy the selection “_ID_ \= 00128”.

The cost of an indexed nested-loop join can be computed as follows: For each tuple in the outer relation _r_ , a lookup is performed on the index for _s_, and the relevant tuples are retrieved. In the worst case, there is space in the buffer for only one block of _r_ and one block of the index. Then, _br_ I/O operations are needed to read relation _r_ , where _br_ denotes the number of blocks containing records of _r_ ; each I/O requires a seek and a block transfer, since the disk head may have moved in between each I/O. For each tuple in _r_ , we perform an index lookup on _s_. Then, the time cost of the join can be computed as _br_ (_tT_ \+ _tS_) + _nr_ ∗ _c_, where _nr_ is the number of records in relation _r_ , and _c_ is the cost of a single selection on _s_ using the join condition. We have seen in Section 12.3 how to estimate the cost of a single selection algorithm (possibly using indices); that estimate gives us the value of _c_.

The cost formula indicates that, if indices are available on both relations _r_ and _s_, it is generally most efficient to use the one with fewer tuples as the outer relation.

For example, consider an indexed nested-loop join of _student  takes_ , with _student_ as the outer relation. Suppose also that _takes_ has a primary B+-tree index on the join attribute _ID_, which contains 20 entries on average in each index node. Since _takes_ has 10,000 tuples, the height of the tree is 4, and one more access is needed to find the actual data. Since _nstudent_ is 5000, the total cost is 100+5000∗5 = 25,100 disk accesses, each of which requires a seek and a block transfer. In contrast, as we saw before, 40,100 block transfers plus 200 seeks were needed for a block nested- loop join. Although the number of block transfers has been reduced, the seek cost has actually increased, increasing the total cost since a seek is considerably more expensive than a block transfer. However, if we had a selection on the _student_ relation that reduces the number of rows significantly, indexed nested-loop join could be significantly faster than block nested-loop join.

**12.5.4 Merge Join**

The **merge-join** algorithm (also called the **sort-merge-join** algorithm) can be used to compute natural joins and equi-joins. Let _r_ (_R_) and _s_(_S_) be the relations whose natural join is to be computed, and let _R_ ∩ _S_ denote their common attributes. Suppose that both relations are sorted on the attributes _R_ ∩ _S_. Then, their join can be computed by a process much like the merge stage in the merge–sort algorithm.

**12.5.4.1 Merge-Join Algorithm**

Figure 12.7 shows the merge-join algorithm. In the algorithm, _JoinAttrs_ refers to the attributes in _R_ ∩ _S_, and _tr  ts_ , where _tr_ and _ts_ are tuples that have the same  

**554 Chapter 12 Query Processing**

_pr_ := address of first tuple of _r_; _ps_ := address of first tuple of _s_; **while** (_ps_ \= null **and** _pr_ \= null) **do**

**begin** _ts_ := tuple to which _ps_ points; _Ss_ := _{ts}_; set _ps_ to point to next tuple of _s_; _done_ := _false_; **while** (**not** _done_ **and** _ps_ \= null) **do**

**begin** _ts_ ′ := tuple to which _ps_ points; **if** (_ts_ ′\[_JoinAttrs_\] = _ts_\[_JoinAttrs_\])

**then begin** _Ss_ := _Ss_ ∪ _{ts_ ′_}_; set _ps_ to point to next tuple of s;

**end else** _done_ := _true_;

**end** _tr_ := tuple to which _pr_ points; **while** (_pr_ \= null **and** _tr_ \[_JoinAttrs_\] _< ts_\[_JoinAttrs_\]) **do**

**begin** set _pr_ to point to next tuple of _r_; _tr_ := tuple to which _pr_ points;

**end while** (_pr_ \= null **and** _tr_ \[_JoinAttrs_\] = _ts_\[_JoinAttrs_\]) **do**

**begin for each** _ts_ **in** _Ss_ **do**

**begin** add _ts  tr_ to result;

**end** set _pr_ to point to next tuple of _r_; _tr_ := tuple to which _pr_ points;

**end end**.

**Figure 12.7** Merge join.

values for _JoinAttrs_, denotes the concatenation of the attributes of the tuples, fol- lowed by projecting out repeated attributes. The merge-join algorithm associates one pointer with each relation. These pointers point initially to the first tuple of the respective relations. As the algorithm proceeds, the pointers move through the relation. A group of tuples of one relation with the same value on the join attributes is read into _Ss_ . The algorithm in Figure 12.7 _requires_ that every set of tuples _Ss_ fit in main memory; we discuss extensions of the algorithm to avoid this requirement shortly. Then, the corresponding tuples (if any) of the other relation are read in, and are processed as they are read.  

**12.5 Join Operation 555**

a 3 b 1 d 8

13d f 7

m 5 q 6

a A b G c L d N m B

_a1 a2 a1 a3 pr ps_

_r_

_s_

**Figure 12.8** Sorted relations for merge join.

Figure 12.8 shows two relations that are sorted on their join attribute _a_1\. It is instructive to go through the steps of the merge-join algorithm on the relations shown in the figure.

The merge-join algorithm of Figure 12.7 requires that each set _Ss_ of all tuples with the same value for the join attributes must fit in main memory. This require- ment can usually be met, even if the relation _s_ is large. If there are some join attribute values for which _Ss_ is larger than available memory, a block nested-loop join can be performed for such sets _Ss_ , matching them with corresponding blocks of tuples in _r_ with the same values for the join attributes.

If either of the input relations _r_ and _s_ is not sorted on the join attributes, they can be sorted first, and then the merge-join algorithm can be used. The merge-join algorithm can also be easily extended from natural joins to the more general case of equi-joins.

**12.5.4.2 Cost Analysis**

Once the relations are in sorted order, tuples with the same value on the join attributes are in consecutive order. Thereby, each tuple in the sorted order needs to be read only once, and, as a result, each block is also read only once. Since it makes only a single pass through both files (assuming all sets _Ss_ fit in memory) the merge-join method is efficient; the number of block transfers is equal to the sum of the number of blocks in both files, _br_ \+ _bs_ .

Assuming that _bb_ buffer blocks are allocated to each relation, the number of disk seeks required would be _br/bb_ + _bs/bb_ disk seeks. Since seeks are much more expensive than data transfer, it makes sense to allocate multiple buffer blocks to each relation, provided extra memory is available. For example, with _tT_ \= 0_._1 milliseconds per 4-kilobyte block, and _tS_ \= 4 milliseconds, the buffer size is 400 blocks (or 1.6 megabytes), so the seek time would be 4 milliseconds for every 40 milliseconds of transfer time, in other words, seek time would be just 10 percent of the transfer time.  

**556 Chapter 12 Query Processing**

If either of the input relations _r_ and _s_ is not sorted on the join attributes, they must be sorted first; the cost of sorting must then be added to the above costs. If some some sets _Ss_ do not fit in memory, the cost would increase slightly.

Suppose the merge-join scheme is applied to our example of _student  takes_ . The join attribute here is _ID_. Suppose that the relations are already sorted on the join attribute _ID_. In this case, the merge join takes a total of 400 + 100 = 500 block transfers. If we assume that in the worst case only one buffer block is allocated to each input relation (that is, _bb_ \= 1), a total of 400 + 100 = 500 seeks would also be required; in reality _bb_ can be set much higher since we need to buffer blocks for only two relations, and the seek cost would be significantly less.

Suppose the relations are not sorted, and the memory size is the worst case, only three blocks. The cost is as follows:

**1\.** Using the formulae that we developed in Section 12.4, we can see that sorting relation _takes_ requires log3−1(400_/_3) = 8 merge passes. Sorting of relation _takes_ then takes 400 ∗ (2log3−1(400_/_3) + 1), or 6800, block transfers, with 400 more transfers to write out the result. The number of seeks required is 2 ∗ 400_/_3 + 400 ∗ (2 ∗ 8 − 1) or 6268 seeks for sorting, and 400 seeks for writing the output, for a total of 6668 seeks, since only one buffer block is available for each run.

**2\.** Similarly, sorting relation _student_ takes log3−1(100_/_3) = 6 merge passes and 100 ∗ (2log3−1(100_/_3) + 1), or 1300, block transfers, with 100 more transfers to write it out. The number of seeks required for sorting _student_ is 2 ∗ 100_/_3 + 100 ∗ (2 ∗ 6 − 1) = 1164, and 100 seeks are required for writing the output, for a total of 1264 seeks.

**3\.** Finally, merging the two relations takes 400 + 100 = 500 block transfers and 500 seeks.

Thus, the total cost is 9100 block transfers plus 8932 seeks if the relations are not sorted, and the memory size is just 3 blocks.

With a memory size of 25 blocks, and the relations not sorted, the cost of sorting followed by merge join would be as follows:

**1\.** Sorting the relation _takes_ can be done with just one merge step, and takes a total of just 400 ∗ (2log24(400_/_25) + 1) = 1200 block transfers. Similarly, sorting _student_ takes 300 block transfers. Writing the sorted output to disk requires 400 + 100 = 500 block transfers, and the merge step requires 500 block transfers to read the data back. Adding up these costs gives a total cost of 2500 block transfers.

**2\.** If we assume that only one buffer block is allocated for each run, the number of seeks required in this case is 2∗400_/_25+400+400 = 832 seeks for sorting _takes_ and writing the sorted output to disk, and similarly 2∗100_/_25+100+ 100 = 208 for _student_, plus 400 + 100 seeks for reading the sorted data in the merge-join step. Adding up these costs gives a total cost of 1640 seeks.  

**12.5 Join Operation 557**

The number of seeks can be significantly reduced by setting aside more buffer blocks for each run. For example, if 5 buffer blocks are allocated for each run and for the output from merging the 4 runs of _student_, the cost is reduced to 2 ∗ 100_/_25 + 100_/_5 + 100_/_5 = 48 seeks, from 208 seeks. If the merge-join step sets aside 12 blocks each for buffering _takes_ and _student_, the number of seeks for the merge-join step goes down to 400_/_12 + 100_/_12 = 43, from 500. The total number of seeks is then 251.

Thus, the total cost is 2500 block transfers plus 251 seeks if the relations are not sorted, and the memory size is 25 blocks.

**12.5.4.3 Hybrid Merge Join**

It is possible to perform a variation of the merge-join operation on unsorted tuples, if secondary indices exist on both join attributes. The algorithm scans the records through the indices, resulting in their being retrieved in sorted order. This variation presents a significant drawback, however, since records may be scattered throughout the file blocks. Hence, each tuple access could involve accessing a disk block, and that is costly.

To avoid this cost, we can use a hybrid merge-join technique that combines indices with merge join. Suppose that one of the relations is sorted; the other is unsorted, but has a secondary B+-tree index on the join attributes. The **hybrid merge-join algorithm** merges the sorted relation with the leaf entries of the secondary B+-tree index. The result file contains tuples from the sorted relation and addresses for tuples of the unsorted relation. The result file is then sorted on the addresses of tuples of the unsorted relation, allowing efficient retrieval of the corresponding tuples, in physical storage order, to complete the join. Extensions of the technique to handle two unsorted relations are left as an exercise for you.

**12.5.5 Hash Join**

Like the merge-join algorithm, the hash-join algorithm can be used to implement natural joins and equi-joins. In the hash-join algorithm, a hash function _h_ is used to partition tuples of both relations. The basic idea is to partition the tuples of each of the relations into sets that have the same hash value on the join attributes.

We assume that:

• _h_ is a hash function mapping _JoinAttrs_ values to {0_,_ 1_, . . . , nh_}, where _JoinAttrs_ denotes the common attributes of _r_ and _s_ used in the natural join.

• _r_0, _r_1_, . . . , rnh_ denote partitions of _r_ tuples, each initially empty. Each tuple _tr_ ∈ _r_ is put in partition _ri_ , where _i_ \= _h_(_tr_ \[_JoinAttrs_\]).

• _s_0, _s_1_, ..., snh_ denote partitions of _s_ tuples, each initially empty. Each tuple _ts_ ∈ _s_ is put in partition _si_ , where _i_ \= _h_(_ts_\[_JoinAttrs_\]).  

**558 Chapter 12 Query Processing**

0

1

2

3

4

0

1

2

3

4_r_

_s_

.

.

.

.

.

.

.

.

partitions of _r_

partitions of _s_

**Figure 12.9** Hash partitioning of relations.

The hash function _h_ should have the “goodness” properties of randomness and uniformity that we discussed in Chapter 11. Figure 12.9 depicts the partitioning of the relations.

**12.5.5.1 Basics**

The idea behind the hash-join algorithm is this: Suppose that an _r_ tuple and an _s_ tuple satisfy the join condition; then, they have the same value for the join attributes. If that value is hashed to some value _i_ , the _r_ tuple has to be in _ri_ and the _s_ tuple in _si_ . Therefore, _r_ tuples in _ri_ need only to be compared with _s_ tuples in _si_ ; they do not need to be compared with _s_ tuples in any other partition.

For example, if _d_ is a tuple in _student_, _c_ a tuple in _takes_, and _h_ a hash function on the _ID_ attributes of the tuples, then _d_ and _c_ must be tested only if _h_(_c_) = _h_(_d_). If _h_(_c_) = _h_(_d_), then _c_ and _d_ must have different values for _ID_. However, if _h_(_c_) = _h_(_d_), we must test _c_ and _d_ to see whether the values in their join attributes are the same, since it is possible that _c_ and _d_ have different _iid_s that have the same hash value.

Figure 12.10 shows the details of the **hash-join** algorithm to compute the natural join of relations _r_ and _s_. As in the merge-join algorithm, _tr  ts_ denotes the concatenation of the attributes of tuples _tr_ and _ts_ , followed by projecting out repeated attributes. After the partitioning of the relations, the rest of the hash-join code performs a separate indexed nested-loop join on each of the partition pairs _i_ , for _i_ \= 0_, . . . , nh_ . To do so, it first **builds** a hash index on each _si_ , and then **probes** (that is, looks up _si_ ) with tuples from _ri_ . The relation _s_ is the **build input**, and _r_ is the **probe input**.

The hash index on _si_ is built in memory, so there is no need to access the disk to retrieve the tuples. The hash function used to build this hash index must be different from the hash function _h_ used earlier, but is still applied to only the join  

**12.5 Join Operation 559**

/\* Partition _s_ \*/ **for each** tuple _ts_ **in** _s_ **do begin**

_i_ := _h_(_ts_\[_JoinAttrs_\]); _Hsi_ := _Hsi_ ∪ _{ts}_;

**end** /\* Partition _r_ \*/ **for each** tuple _tr_ **in** _r_ **do begin**

_i_ := _h_(_tr_ \[_JoinAttrs_\]); _Hri_ := _Hri_ ∪ _{tr}_;

**end** /\* Perform join on each partition \*/ **for** _i_ := 0 **to** _nh_ **do begin**

read _Hsi_ and build an in-memory hash index on it; **for each** tuple _tr_ **in** _Hri_ **do begin**

probe the hash index on _Hsi_ to locate all tuples _ts_ such that _ts_\[_JoinAttrs_\] = _tr_ \[_JoinAttrs_\];

**for each** matching tuple _ts_ **in** _Hsi_ **do begin** add _tr  ts_ to the result;

**end end**

**end**

**Figure 12.10** Hash join.

attributes. In the course of the indexed nested-loop join, the system uses this hash index to retrieve records that match records in the probe input.

The build and probe phases require only a single pass through both the build and probe inputs. It is straightforward to extend the hash-join algorithm to compute general equi-joins.

The value _nh_ must be chosen to be large enough such that, for each _i_ , the tuples in the partition _si_ of the build relation, along with the hash index on the partition, fit in memory. It is not necessary for the partitions of the probe relation to fit in memory. Clearly, it is best to use the smaller input relation as the build relation. If the size of the build relation is _bs_ blocks, then, for each of the _nh_ partitions to be of size less than or equal to _M_, _nh_ must be at least _bs/M_. More precisely stated, we have to account for the extra space occupied by the hash index on the partition as well, so _nh_ should be correspondingly larger. For simplicity, we sometimes ignore the space requirement of the hash index in our analysis.

**12.5.5.2 Recursive Partitioning**

If the value of _nh_ is greater than or equal to the number of blocks of memory, the relations cannot be partitioned in one pass, since there will not be enough buffer blocks. Instead, partitioning has to be done in repeated passes. In one pass, the input can be split into at most as many partitions as there are blocks available for use as output buffers. Each bucket generated by one pass is separately read in and  

**560 Chapter 12 Query Processing**

partitioned again in the next pass, to create smaller partitions. The hash function used in a pass is, of course, different from the one used in the previous pass. The system repeats this splitting of the input until each partition of the build input fits in memory. Such partitioning is called **recursive partitioning**.

A relation does not need recursive partitioning if _M > nh_ \+ 1, or equivalently _M >_ (_bs/M_) + 1, which simplifies (approximately) to _M >_

√ _bs_ . For example,

consider a memory size of 12 megabytes, divided into 4-kilobyte blocks; it would contain a total of 3K (3072) blocks. We can use a memory of this size to partition relations of size up to 3K ∗ 3K blocks, which is 36 gigabytes. Similarly, a relation of size 1 gigabyte requires just over

√ 256K blocks, or 2 megabytes, to avoid recursive

partitioning.

**12.5.5.3 Handling of Overflows**

**Hash-table overflow** occurs in partition _i_ of the build relation _s_ if the hash index on _si_ is larger than main memory. Hash-table overflow can occur if there are many tuples in the build relation with the same values for the join attributes, or if the hash function does not have the properties of randomness and uniformity. In either case, some of the partitions will have more tuples than the average, whereas others will have fewer; partitioning is then said to be **skewed**.

We can handle a small amount of skew by increasing the number of parti- tions so that the expected size of each partition (including the hash index on the partition) is somewhat less than the size of memory. The number of partitions is therefore increased by a small value, called the **fudge factor**, that is usually about 20 percent of the number of hash partitions computed as described in Section 12.5.5.

Even if, by using a fudge factor, we are conservative on the sizes of the par- titions, overflows can still occur. Hash-table overflows can be handled by either _overflow resolution_ or _overflow avoidance_. **Overflow resolution** is performed during the build phase, if a hash-index overflow is detected. Overflow resolution pro- ceeds in this way: If _si_ , for any _i_ , is found to be too large, it is further partitioned into smaller partitions by using a different hash function. Similarly, _ri_ is also par- titioned using the new hash function, and only tuples in the matching partitions need to be joined.

In contrast, **overflow avoidance** performs the partitioning carefully, so that overflows never occur during the build phase. In overflow avoidance, the build relation _s_ is initially partitioned into many small partitions, and then some par- titions are combined in such a way that each combined partition fits in memory. The probe relation _r_ is partitioned in the same way as the combined partitions on _s_, but the sizes of _ri_ do not matter.

If a large number of tuples in _s_ have the same value for the join attributes, the resolution and avoidance techniques may fail on some partitions. In that case, instead of creating an in-memory hash index and using a nested-loop join to join the partitions, we can use other join techniques, such as block nested-loop join, on those partitions.  

**12.5 Join Operation 561**

**12.5.5.4 Cost of Hash Join**

We now consider the cost of a hash join. Our analysis assumes that there is no hash-table overflow. First, consider the case where recursive partitioning is not required.

• The partitioning of the two relations _r_ and _s_ calls for a complete reading of both relations, and a subsequent writing back of them. This operation requires 2(_br_ \+ _bs_) block transfers, where _br_ and _bs_ denote the number of blocks containing records of relations _r_ and _s_, respectively. The build and probe phases read each of the partitions once, calling for further _br_ \+ _bs_ block transfers. The number of blocks occupied by partitions could be slightly more than _br_ +_bs_ , as a result of partially filled blocks. Accessing such partially filled blocks can add an overhead of at most 2_nh_ for each of the relations, since each of the _nh_ partitions could have a partially filled block that has to be written and read back. Thus, a hash join is estimated to require:

3(_br_ \+ _bs_) + 4_nh_

block transfers. The overhead 4_nh_ is usually quite small compared to _br_ \+ _bs_ , and can be ignored.

• Assuming _bb_ blocks are allocated for the input buffer and each output buffer, partitioning requires a total of 2(_br/bb_+_bs/bb_) seeks. The build and probe phases require only one seek for each of the _nh_ partitions of each relation, since each partition can be read sequentially. The hash join thus requires 2(_br/bb_ + _bs/bb_) + 2_nh_ seeks.

Now consider the case where recursive partitioning is required. Each pass reduces the size of each of the partitions by an expected factor of _M_ − 1; and passes are repeated until each partition is of size at most _M_ blocks. The expected number of passes required for partitioning _s_ is therefore log_M_−1(_bs_) − 1.

• Since, in each pass, every block of _s_ is read in and written out, the total block transfers for partitioning of _s_ is 2_bs_log_M_−1(_bs_) − 1. The number of passes for partitioning of _r_ is the same as the number of passes for partitioning of _s_, therefore the join is estimated to require:

2(_br_ \+ _bs_)log_M_−1(_bs_) − 1 + _br_ \+ _bs_

block transfers.

• Again assuming _bb_ blocks are allocated for buffering each partition, and ignoring the relatively small number of seeks during the build and probe phase, hash join with recursive partitioning requires:

2(_br/bb_ + _bs/bb_)log_M_−1(_bs_) − 1 disk seeks.  

**562 Chapter 12 Query Processing**

Consider, for example, the natural join _takes  student_ . With a memory size of 20 blocks, the _student_ relation can be partitioned into five partitions, each of size 20 blocks, which size will fit into memory. Only one pass is required for the partitioning. The relation _takes_ is similarly partitioned into five partitions, each of size 80. Ignoring the cost of writing partially filled blocks, the cost is 3(100 + 400) = 1500 block transfers. There is enough memory to allocate 3 buffers for the input and each of the 5 outputs during partitioning, leading to 2(100_/_3+ 400_/_3) = 336 seeks.

The hash join can be improved if the main-memory size is large. When the entire build input can be kept in main memory, _nh_ can be set to 0; then, the hash- join algorithm executes quickly, without partitioning the relations into temporary files, regardless of the probe input’s size. The cost estimate goes down to _br_ \+ _bs_ block transfers and two seeks.

**12.5.5.5 Hybrid Hash Join**

The **hybrid hash-join** algorithm performs another optimization; it is useful when memory sizes are relatively large, but not all of the build relation fits in memory. The partitioning phase of the hash-join algorithm needs a minimum of one block of memory as a buffer for each partition that is created, and one block of memory as an input buffer. To reduce the impact of seeks, a larger number of blocks would be used as a buffer; let _bb_ denote the number of blocks used as a buffer for the input and for each partition. Hence, a total of (_nh_ \+ 1) ∗ _bb_ blocks of memory are needed for partitioning the two relations. If memory is larger than (_nh_ \+ 1) ∗ _bb_ , we can use the rest of memory (_M_− (_nh_ +1)∗_bb_ blocks) to buffer the first partition of the build input (that is, _s_0), so that it will not need to be written out and read back in. Further, the hash function is designed in such a way that the hash index on _s_0 fits in _M_ − (_nh_ \+ 1) ∗ _bb_ blocks, in order that, at the end of partitioning of _s_, _s_0 is completely in memory and a hash index can be built on _s_0.

When the system partitions _r_ it again does not write tuples in _r_0 to disk; instead, as it generates them, the system uses them to probe the memory-resident hash index on _s_0, and to generate output tuples of the join. After they are used for probing, the tuples can be discarded, so the partition _r_0 does not occupy any memory space. Thus, a write and a read access have been saved for each block of both _r_0 and _s_0\. The system writes out tuples in the other partitions as usual, and joins them later. The savings of hybrid hash join can be significant if the build input is only slightly bigger than memory.

If the size of the build relation is _bs_ , _nh_ is approximately equal to _bs/M_. Thus, hybrid hash join is most useful if _M >>_ (_bs/M_) ∗ _bb_ , or _M >>_

√ _bs_ ∗ _bb_ , where

the notation _\>>_ denotes _much larger than_. For example, suppose the block size is 4 kilobytes, the build relation size is 5 gigabytes, and _bb_ is 20. Then, the hybrid hash-join algorithm is useful if the size of memory is significantly more than 20 megabytes; memory sizes of gigabytes or more are common on computers today. If we devote 1 gigabyte for the join algorithm, _s_0 would be nearly 1 gigabyte, and hybrid hash join would be nearly 20 percent cheaper than hash join.  

**12.6 Other Operations 563**

**12.5.6 Complex Joins**

Nested-loop and block nested-loop joins can be used regardless of the join condi- tions. The other join techniques are more efficient than the nested-loop join and its variants, but can handle only simple join conditions, such as natural joins or equi-joins. We can implement joins with complex join conditions, such as con- junctions and disjunctions, by using the efficient join techniques, if we apply the techniques developed in Section 12.3.3 for handling complex selections.

Consider the following join with a conjunctive condition:

_r _1∧2∧···∧_n s_

One or more of the join techniques described earlier may be applicable for joins on the individual conditions _r _1 _s_, _r _2 _s_, _r _3 _s_, and so on. We can compute the overall join by first computing the result of one of these simpler joins _r __i s_; each pair of tuples in the intermediate result consists of one tuple from _r_ and one from _s_. The result of the complete join consists of those tuples in the intermediate result that satisfy the remaining conditions:

1 ∧ · · · ∧ _i_−1 ∧ _i_+1 ∧ · · · ∧ _n_

These conditions can be tested as tuples in _r __i s_ are being generated. A join whose condition is disjunctive can be computed in this way. Consider:

_r _1∨2∨···∨_n s_

The join can be computed as the union of the records in individual joins _r __i s_:

(_r _1 _s_) ∪ (_r _2 _s_) ∪ · · · ∪ (_r __n s_)

Section 12.6 describes algorithms for computing the union of relations.

**12.6 Other Operations**

Other relational operations and extended relational operations—such as dupli- cate elimination, projection, set operations, outer join, and aggregation—can be implemented as outlined in Sections 12.6.1 through 12.6.5.

**12.6.1 Duplicate Elimination**

We can implement duplicate elimination easily by sorting. Identical tuples will appear adjacent to each other as a result of sorting, and all but one copy can be removed. With external sort–merge, duplicates found while a run is being created can be removed before the run is written to disk, thereby reducing the number of block transfers. The remaining duplicates can be eliminated during merging, and  

**564 Chapter 12 Query Processing**

the final sorted run has no duplicates. The worst-case cost estimate for duplicate elimination is the same as the worst-case cost estimate for sorting of the relation.

We can also implement duplicate elimination by hashing, as in the hash-join algorithm. First, the relation is partitioned on the basis of a hash function on the whole tuple. Then, each partition is read in, and an in-memory hash index is constructed. While constructing the hash index, a tuple is inserted only if it is not already present. Otherwise, the tuple is discarded. After all tuples in the partition have been processed, the tuples in the hash index are written to the result. The cost estimate is the same as that for the cost of processing (partitioning and reading each partition) of the build relation in a hash join.

Because of the relatively high cost of duplicate elimination, SQL requires an explicit request by the user to remove duplicates; otherwise, the duplicates are retained.

**12.6.2 Projection**

We can implement projection easily by performing projection on each tuple, which gives a relation that could have duplicate records, and then removing duplicate records. Duplicates can be eliminated by the methods described in Section 12.6.1. If the attributes in the projection list include a key of the relation, no duplicates will exist; hence, duplicate elimination is not required. Generalized projection can be implemented in the same way as projection.

**12.6.3 Set Operations**

We can implement the _union_, _intersection_, and _set-difference_ operations by first sorting both relations, and then scanning once through each of the sorted relations to produce the result. In _r_ ∪ _s_, when a concurrent scan of both relations reveals the same tuple in both files, only one of the tuples is retained. The result of _r_ ∩ _s_ will contain only those tuples that appear in both relations. We implement _set difference_, _r_ − _s_, similarly, by retaining tuples in _r_ only if they are absent in _s_.

For all these operations, only one scan of the two sorted input relations is required, so the cost is _br_ \+ _bs_ block transfers if the relations are sorted in the same order. Assuming a worst case of one block buffer for each relation, a total of _br_ \+ _bs_ disk seeks would be required in addition to _br_ \+ _bs_ block transfers. The number of seeks can be reduced by allocating extra buffer blocks.

If the relations are not sorted initially, the cost of sorting has to be included. Any sort order can be used in evaluation of set operations, provided that both inputs have that same sort order.

Hashing provides another way to implement these set operations. The first step in each case is to partition the two relations by the same hash function, and thereby create the partitions _r_0_, r_1_, . . . , rnh_ and _s_0_, s_1_, . . . , snh_ . Depending on the operation, the system then takes these steps on each partition _i_ \= 0_,_ 1_, . . . , nh_ :

• _r_ ∪ _s_

**1\.** Build an in-memory hash index on _ri_ .  

**12.6 Other Operations 565**

**2\.** Add the tuples in _si_ to the hash index only if they are not already present.

**3\.** Add the tuples in the hash index to the result.

• _r_ ∩ _s_

**1\.** Build an in-memory hash index on _ri_ .

**2\.** For each tuple in _si_ , probe the hash index and output the tuple to the result only if it is already present in the hash index.

• _r_ − _s_

**1\.** Build an in-memory hash index on _ri_ .

**2\.** For each tuple in _si_ , probe the hash index, and, if the tuple is present in the hash index, delete it from the hash index.

**3\.** Add the tuples remaining in the hash index to the result.

**12.6.4 Outer Join**

Recall the _outer-join operations_ described in Section 4.1.2. For example, the natural left outer join _takes  student_ contains the join of _takes_ and _student_, and, in addition, for each _takes_ tuple _t_ that has no matching tuple in _student_ (that is, where _ID_ is not in _student_), the following tuple _t_1 is added to the result. For all attributes in the schema of _takes_, tuple _t_1 has the same values as tuple _t_. The remaining attributes (from the schema of _student_) of tuple _t_1 contain the value null.

We can implement the outer-join operations by using one of two strategies:

**1\.** Compute the corresponding join, and then add further tuples to the join result to get the outer-join result. Consider the left outer-join operation and two relations: _r_ (_R_) and _s_(_S_). To evaluate _r _ _s_, we first compute _r _ _s_, and save that result as temporary relation _q_1\. Next, we compute _r_ − _R_(_q_1) to obtain those tuples in _r_ that do not participate in the theta join. We can use any of the algorithms for computing the joins, projection, and set difference described earlier to compute the outer joins. We pad each of these tuples with null values for attributes from _s_, and add it to _q_1 to get the result of the outer join.

The right outer-join operation _r_  _s_ is equivalent to _s _ _r_ , and can therefore be implemented in a symmetric fashion to the left outer join. We can implement the full outer-join operation _r_  _s_ by computing the join _r  s_, and then adding the extra tuples of both the left and right outer-join operations, as before.

**2\.** Modify the join algorithms. It is easy to extend the nested-loop join algo- rithms to compute the left outer join: Tuples in the outer relation that do not match any tuple in the inner relation are written to the output after being padded with null values. However, it is hard to extend the nested-loop join to compute the full outer join.  

**566 Chapter 12 Query Processing**

Natural outer joins and outer joins with an equi-join condition can be computed by extensions of the merge-join and hash-join algorithms. Merge join can be extended to compute the full outer join as follows: When the merge of the two relations is being done, tuples in either relation that do not match any tuple in the other relation can be padded with nulls and written to the output. Similarly, we can extend merge join to compute the left and right outer joins by writing out nonmatching tuples (padded with nulls) from only one of the relations. Since the relations are sorted, it is easy to detect whether or not a tuple matches any tuples from the other relation. For example, when a merge join of _takes_ and _student_ is done, the tuples are read in sorted order of _ID_, and it is easy to check, for each tuple, whether there is a matching tuple in the other.

The cost estimates for implementing outer joins using the merge-join algorithm are the same as are those for the corresponding join. The only difference lies in size of the result, and therefore in the block transfers for writing it out, which we did not count in our earlier cost estimates.

The extension of the hash-join algorithm to compute outer joins is left for you to do as an exercise (Exercise 12.15).

**12.6.5 Aggregation**

Recall the aggregation function (operator), discussed in Section 3.7. For example, the function

**select** _dept name_, **avg** (_salary_) **from** _instructor_ **group by** _dept name_;

computes the average salary in each university department. The aggregation operation can be implemented in the same way as duplicate

elimination. We use either sorting or hashing, just as we did for duplicate elimina- tion, but based on the grouping attributes (_branch name_ in the preceding example). However, instead of eliminating tuples with the same value for the grouping at- tribute, we gather them into groups, and apply the aggregation operations on each group to get the result.

The cost estimate for implementing the aggregation operation is the same as the cost of duplicate elimination, for aggregate functions such as **min**, **max**, **sum**, **count**, and **avg**.

Instead of gathering all the tuples in a group and then applying the aggre- gation operations, we can implement the aggregation operations **sum**, **min**, **max**, **count**, and **avg** on the fly as the groups are being constructed. For the case of **sum**, **min**, and **max**, when two tuples in the same group are found, the system replaces them by a single tuple containing the **sum**, **min**, or **max**, respectively, of the columns being aggregated. For the **count** operation, it maintains a running count for each group for which a tuple has been found. Finally, we implement the  

**12.7 Evaluation of Expressions 567**

**avg** operation by computing the sum and the count values on the fly, and finally dividing the sum by the count to get the average.

If all tuples of the result fit in memory, both the sort-based and the hash-based implementations do not need to write any tuples to disk. As the tuples are read in, they can be inserted in a sorted tree structure or in a hash index. When we use on-the-fly aggregation techniques, only one tuple needs to be stored for each of the groups. Hence, the sorted tree structure or hash index fits in memory, and the aggregation can be processed with just _br_ block transfers (and 1 seek) instead of the 3_br_ transfers (and a worst case of up to 2_br_ seeks) that would be required otherwise.

**12.7 Evaluation of Expressions**

So far, we have studied how individual relational operations are carried out. Now we consider how to evaluate an expression containing multiple operations. The obvious way to evaluate an expression is simply to evaluate one operation at a time, in an appropriate order. The result of each evaluation is **materialized** in a temporary relation for subsequent use. A disadvantage to this approach is the need to construct the temporary relations, which (unless they are small) must be written to disk. An alternative approach is to evaluate several operations simultaneously in a **pipeline**, with the results of one operation passed on to the next, without the need to store a temporary relation.

In Sections 12.7.1 and 12.7.2, we consider both the _materialization_ approach and the _pipelining_ approach. We shall see that the costs of these approaches can differ substantially, but also that there are cases where only the materialization approach is feasible.

**12.7.1 Materialization**

It is easiest to understand intuitively how to evaluate an expression by looking at a pictorial representation of the expression in an **operator tree**. Consider the expression:

_name_(_building_ \= “Watson”(_department_) _instructor_ )

in Figure 12.11. If we apply the materialization approach, we start from the lowest-level op-

erations in the expression (at the bottom of the tree). In our example, there is only one such operation: the selection operation on _department_. The inputs to the lowest-level operations are relations in the database. We execute these operations by the algorithms that we studied earlier, and we store the results in temporary relations. We can use these temporary relations to execute the operations at the next level up in the tree, where the inputs now are either temporary relations or relations stored in the database. In our example, the inputs to the join are the _in- structor_ relation and the temporary relation created by the selection on _department_. The join can now be evaluated, creating another temporary relation.  

**568 Chapter 12 Query Processing**

Π

σ

_name_

_building_ = “Watson”

_department_

_instructor_

**Figure 12.11** Pictorial representation of an expression.

By repeating the process, we will eventually evaluate the operation at the root of the tree, giving the final result of the expression. In our example, we get the final result by executing the projection operation at the root of the tree, using as input the temporary relation created by the join.

Evaluation as just described is called **materialized evaluation**, since the re- sults of each intermediate operation are created (materialized) and then are used for evaluation of the next-level operations.

The cost of a materialized evaluation is not simply the sum of the costs of the operations involved. When we computed the cost estimates of algorithms, we ignored the cost of writing the result of the operation to disk. To compute the cost of evaluating an expression as done here, we have to add the costs of all the operations, as well as the cost of writing the intermediate results to disk. We assume that the records of the result accumulate in a buffer, and, when the buffer is full, they are written to disk. The number of blocks written out, _br_ , can be estimated as _nr/ fr_ , where _nr_ is the estimated number of tuples in the result relation _r_ , and _fr_ is the _blocking factor_ of the result relation, that is, the number of records of _r_ that will fit in a block. In addition to the transfer time, some disk seeks may be required, since the disk head may have moved between successive writes. The number of seeks can be estimated as _br/bb_ where _bb_ is the size of the output buffer (measured in blocks).

**Double buffering** (using two buffers, with one continuing execution of the algorithm while the other is being written out) allows the algorithm to execute more quickly by performing CPU activity in parallel with I/O activity. The number of seeks can be reduced by allocating extra blocks to the output buffer, and writing out multiple blocks at once.

**12.7.2 Pipelining**

We can improve query-evaluation efficiency by reducing the number of tem- porary files that are produced. We achieve this reduction by combining several relational operations into a _pipeline_ of operations, in which the results of one op-  

**12.7 Evaluation of Expressions 569**

eration are passed along to the next operation in the pipeline. Evaluation as just described is called **pipelined evaluation**.

For example, consider the expression (_a_1_,a_2(_r  s_)). If materialization were applied, evaluation would involve creating a temporary relation to hold the result of the join, and then reading back in the result to perform the projection. These operations can be combined: When the join operation generates a tuple of its result, it passes that tuple immediately to the project operation for processing. By combining the join and the projection, we avoid creating the intermediate result, and instead create the final result directly.

Creating a pipeline of operations can provide two benefits:

**1\.** It eliminates the cost of reading and writing temporary relations, reducing the cost of query evaluation.

**2\.** It can start generating query results quickly, if the root operator of a query- evaluation plan is combined in a pipeline with its inputs. This can be quite useful if the results are displayed to a user as they are generated, since otherwise there may be a long delay before the user sees any query results.

**12.7.2.1 Implementation of Pipelining**

We can implement a pipeline by constructing a single, complex operation that combines the operations that constitute the pipeline. Although this approach may be feasible for some frequently occurring situations, it is desirable in general to reuse the code for individual operations in the construction of a pipeline.

In the example of Figure 12.11, all three operations can be placed in a pipeline, which passes the results of the selection to the join as they are generated. In turn, it passes the results of the join to the projection as they are generated. The memory requirements are low, since results of an operation are not stored for long. However, as a result of pipelining, the inputs to the operations are not available all at once for processing.

Pipelines can be executed in either of two ways:

**1\.** In a **demand-driven pipeline**, the system makes repeated requests for tuples from the operation at the top of the pipeline. Each time that an operation receives a request for tuples, it computes the next tuple (or tuples) to be returned, and then returns that tuple. If the inputs of the operation are not pipelined, the next tuple(s) to be returned can be computed from the input relations, while the system keeps track of what has been returned so far. If it has some pipelined inputs, the operation also makes requests for tuples from its pipelined inputs. Using the tuples received from its pipelined inputs, the operation computes tuples for its output, and passes them up to its parent.

**2\.** In a **producer-driven pipeline**, operations do not wait for requests to pro- duce tuples, but instead generate the tuples **eagerly**. Each operation in a producer-driven pipeline is modeled as a separate process or thread within  

**570 Chapter 12 Query Processing**

the system that takes a stream of tuples from its pipelined inputs and gen- erates a stream of tuples for its output.

We describe below how demand-driven and producer-driven pipelines can be implemented.

Each operation in a demand-driven pipeline can be implemented as an **iter- ator** that provides the following functions: _open_(), _next_(), and _close_(). After a call to _open_(), each call to _next_() returns the next output tuple of the operation. The implementation of the operation in turn calls _open_() and _next_() on its inputs, to get its input tuples when required. The function _close_() tells an iterator that no more tuples are required. The iterator maintains the **state** of its execution in between calls, so that successive _next_() requests receive successive result tuples.

For example, for an iterator implementing the select operation using linear search, the _open_() operation starts a file scan, and the iterator’s state records the point to which the file has been scanned. When the _next_() function is called, the file scan continues from after the previous point; when the next tuple satisfying the selection is found by scanning the file, the tuple is returned after storing the point where it was found in the iterator state. A merge-join iterator’s _open_() operation would open its inputs, and if they are not already sorted, it would also sort the inputs. On calls to _next_(), it would return the next pair of matching tuples. The state information would consist of up to where each input had been scanned. Details of the implementation of iterators are left for you to complete in Practice Exercise 12.7.

Producer-driven pipelines, on the other hand, are implemented in a different manner. For each pair of adjacent operations in a producer-driven pipeline, the system creates a buffer to hold tuples being passed from one operation to the next. The processes or threads corresponding to different operations execute concurrently. Each operation at the bottom of a pipeline continually generates output tuples, and puts them in its output buffer, until the buffer is full. An operation at any other level of a pipeline generates output tuples when it gets input tuples from lower down in the pipeline, until its output buffer is full. Once the operation uses a tuple from a pipelined input, it removes the tuple from its input buffer. In either case, once the output buffer is full, the operation waits until its parent operation removes tuples from the buffer, so that the buffer has space for more tuples. At this point, the operation generates more tuples, until the buffer is full again. The operation repeats this process until all the output tuples have been generated.

It is necessary for the system to switch between operations only when an output buffer is full, or an input buffer is empty and more input tuples are needed to generate any more output tuples. In a parallel-processing system, operations in a pipeline may be run concurrently on distinct processors (see Chapter 18).

Using producer-driven pipelining can be thought of as **pushing** data up an operation tree from below, whereas using demand-driven pipelining can be thought of as **pulling** data up an operation tree from the top. Whereas tuples are generated _eagerly_ in producer-driven pipelining, they are generated **lazily**, on demand, in demand-driven pipelining. Demand-driven pipelining is used  

**12.7 Evaluation of Expressions 571**

more commonly than producer-driven pipelining, because it is easier to imple- ment. However, producer-driven pipelining is very useful in parallel processing systems.

**12.7.2.2 Evaluation Algorithms for Pipelining**

Some operations, such as sorting, are inherently **blocking operations**, that is, they may not be able to output any results until all tuples from their inputs have been examined.5

Other operations, such as join, are not inherently blocking, but specific eval- uation algorithms may be blocking. For example, the hash-join algorithm is a blocking operation, since it requires both its inputs to be fully retrieved and parti- tioned, before it outputs any tuples. On the other hand, the indexed nested loops join algorithm can output result tuples as it gets tuples for the outer relation. It is therefore said to be **pipelined** on its outer (left-hand side) relation, although it is blocking on its indexed (right-hand side) input, since the index must be fully constructed before the indexed nested-loop join algorithm can execute.

Hybrid hash join can be viewed as partially pipelined on the probe relation, since it can output tuples from the first partition as tuples are received for the probe relation. However, tuples that are not in the first partition will be output only after the entire pipelined input relation is received. Hybrid hash join thus provides pipelined evaluation on its probe input if the build input fits entirely in memory, or nearly pipelined evaluation if most of the build input fits in memory.

If both inputs are sorted on the join attribute, and the join condition is an equi-join, merge join can be used, with both its inputs pipelined.

However, in the more common case that the two inputs that we desire to pipeline into the join are not already sorted, another alternative is the **double- pipelined join** technique, shown in Figure 12.12. The algorithm assumes that the input tuples for both input relations, _r_ and _s_, are pipelined. Tuples made available for both relations are queued for processing in a single queue. Special queue entries, called _Endr_ and _Ends_ , which serve as end-of-file markers, are inserted in the queue after all tuples from _r_ and _s_ (respectively) have been generated. For efficient evaluation, appropriate indices should be built on the relations _r_ and _s_. As tuples are added to _r_ and _s_, the indices must be kept up to date. When hash indices are used on _r_ and _s_, the resultant algorithm is called the **double-pipelined hash-join** technique.

The double-pipelined join algorithm in Figure 12.12 assumes that both inputs fit in memory. In case the two inputs are larger than memory, it is still possible to use the double-pipelined join technique as usual until available memory is full. When available memory becomes full, _r_ and _s_ tuples that have arrived up to that point can be treated as being in partition _r_0 and _s_0, respectively. Tuples for _r_ and _s_ that arrive subsequently are assigned to partitions _r_1 and _s_1, respectively, which

5Blocking operations such as sorting may be able to output tuples early if the input is known to satisfy some special properties such as being sorted, or partially sorted, already. However, in the absence of such information, blocking operations cannot output tuples early.  

**572 Chapter 12 Query Processing**

_doner_ := _false_; _dones_ := _false_; _r_ := ∅; _s_ := ∅; _result_ := ∅; **while not** _doner_ **or not** _dones_ **do**

**begin if** queue is empty, **then** wait until queue is not empty; _t_ := top entry in queue; **if** _t_ \= _End r_ **then** _doner_ := _true_

**else if** _t_ \= _End s_ **then** _dones_ := _true_ **else if** _t_ is from input _r_

**then begin**

_r_ := _r_ ∪ _{t}_; _result_ := _result_ ∪ (_{t}  s_);

**end else** /\* _t_ is from input _s_ \*/

**begin** _s_ := _s_ ∪ _{t}_; _result_ := _result_ ∪ (_r  {t}_);

**end end**

**Figure 12.12** Double-pipelined join algorithm.

are written to disk, and are not added to the in-memory index. However, tuples assigned to _r_1 and _s_1 are used to probe _s_0 and _r_0, respectively, before they are written to disk. Thus, the join of _r_1 with _s_0, and _s_0 with _r_1, is also carried out in a pipelined fashion. After _r_ and _s_ have been fully processed, the join of _r_1 tuples with _s_1 tuples must be carried out, to complete the join; any of the join techniques we have seen earlier can be used to join _r_1 with _s_1.

**12.8 Summary**

• The first action that the system must perform on a query is to translate the query into its internal form, which (for relational database systems) is usually based on the relational algebra. In the process of generating the internal form of the query, the parser checks the syntax of the user’s query, verifies that the relation names appearing in the query are names of relations in the database, and so on. If the query was expressed in terms of a view, the parser replaces all references to the view name with the relational-algebra expression to compute the view.

• Given a query, there are generally a variety of methods for computing the answer. It is the responsibility of the query optimizer to transform the query  

**Review Terms 573**

as entered by the user into an equivalent query that can be computed more efficiently. Chapter 13 covers query optimization.

• We can process simple selection operations by performing a linear scan, or by making use of indices. We can handle complex selections by computing unions and intersections of the results of simple selections.

• We can sort relations larger than memory by the external sort–merge algo- rithm.

• Queries involving a natural join may be processed in several ways, depending on the availability of indices and the form of physical storage for the relations.

◦ If the join result is almost as large as the Cartesian product of the two relations, a _block nested-loop_ join strategy may be advantageous.

◦ If indices are available, the _indexed nested-loop_ join can be used.

◦ If the relations are sorted, a _merge join_ may be desirable. It may be advan- tageous to sort a relation prior to join computation (so as to allow use of the merge-join strategy).

◦ The _hash-join_ algorithm partitions the relations into several pieces, such that each piece of one of the relations fits in memory. The partitioning is carried out with a hash function on the join attributes, so that correspond- ing pairs of partitions can be joined independently.

• Duplicate elimination, projection, set operations (union, intersection, and difference), and aggregation can be done by sorting or by hashing.

• Outer-join operations can be implemented by simple extensions of join algo- rithms.

• Hashing and sorting are dual, in the sense that any operation such as du- plicate elimination, projection, aggregation, join, and outer join that can be implemented by hashing can also be implemented by sorting, and vice versa; that is, any operation that can be implemented by sorting can also be imple- mented by hashing.

• An expression can be evaluated by means of materialization, where the sys- tem computes the result of each subexpression and stores it on disk, and then uses it to compute the result of the parent expression.

• Pipelining helps to avoid writing the results of many subexpressions to disk, by using the results in the parent expression even as they are being generated.

**Review Terms**

• Query processing • Evaluation primitive • Query-execution plan

• Query-evaluation plan • Query-execution engine • Measures of query cost  

**574 Chapter 12 Query Processing**

• Sequential I/O

• Random I/O

• File scan • Linear search • Selections using indices • Access paths • Index scans • Conjunctive selection • Disjunctive selection • Composite index • Intersection of identifiers • External sorting • External sort–merge • Runs • _N_\-way merge • Equi-join • Nested-loop join • Block nested-loop join • Indexed nested-loop join • Merge join • Sort-merge join • Hybrid merge join • Hash join

◦ Build

◦ Probe

◦ Build input

◦ Probe input

◦ Recursive partitioning

◦ Hash-table overflow

◦ Skew

◦ Fudge factor

◦ Overflow resolution

◦ Overflow avoidance

• Hybrid hash join • Operator tree • Materialized evaluation • Double buffering • Pipelined evaluation

◦ Demand-driven pipeline (lazy, pulling)

◦ Producer-driven pipeline (eager, pushing)

◦ Iterator

• Double-pipelined join

**Practice Exercises**

**12.1** Assume (for simplicity in this exercise) that only one tuple fits in a block and memory holds at most 3 blocks. Show the runs created on each pass of the sort-merge algorithm, when applied to sort the following tuples on the first attribute: (kangaroo, 17), (wallaby, 21), (emu, 1), (wombat, 13), (platypus, 3), (lion, 8), (warthog, 4), (zebra, 11), (meerkat, 6), (hyena, 9), (hornbill, 2), (baboon, 12).

**12.2** Consider the bank database of Figure 12.13, where the primary keys are underlined, and the following SQL query:

**select** _T_._branch name_ **from** _branch T_, _branch S_ **where** _T.assets > S.assets_ **and** _S.branch city_ \= “Brooklyn”  

**Practice Exercises 575**

Write an efficient relational-algebra expression that is equivalent to this query. Justify your choice.

**12.3** Let relations _r_1(_A, B, C_) and _r_2(_C, D, E_) have the following properties: _r_1 has 20,000 tuples, _r_2 has 45,000 tuples, 25 tuples of _r_1 fit on one block, and 30 tuples of _r_2 fit on one block. Estimate the number of block transfers and seeks required, using each of the following join strategies for _r_1 _ r_2:

a. Nested-loop join.

b. Block nested-loop join.

c. Merge join.

d. Hash join.

**12.4** The indexed nested-loop join algorithm described in Section 12.5.3 can be inefficient if the index is a secondary index, and there are multiple tuples with the same value for the join attributes. Why is it inefficient? Describe a way, using sorting, to reduce the cost of retrieving tuples of the inner relation. Under what conditions would this algorithm be more efficient than hybrid merge join?

**12.5** Let _r_ and _s_ be relations with no indices, and assume that the relations are not sorted. Assuming infinite memory, what is the lowest-cost way (in terms of I/O operations) to compute _r  s_? What is the amount of memory required for this algorithm?

**12.6** Consider the bank database of Figure 12.13, where the primary keys are underlined. Suppose that a B+-tree index on _branch city_ is available on relation _branch_, and that no other index is available. List different ways to handle the following selections that involve negation:

a. ¬(_branch city<_“Brooklyn”)(_branch_)

b. ¬(_branch city_\=“Brooklyn”)(_branch_)

c. ¬(_branch city<_“Brooklyn” ∨ _assets<_5000)(_branch_)

**12.7** Write pseudocode for an iterator that implements indexed nested-loop join, where the outer relation is pipelined. Your pseudocode must define

_branch_(_branch name_, _branch city, assets_) _customer_ (_customer name_, _customer street, customer city_) _loan_ (_loan number_, _branch name, amount_) _borrower_ (_customer name_, _loan number_) _account_ (_account number_, _branch name, balance_) _depositor_ (_customer name_, _account number_)

**Figure 12.13** Banking database.  

**576 Chapter 12 Query Processing**

the standard iterator functions _open_(), _next_(), and _close_(). Show what state information the iterator must maintain between calls.

**12.8** Design sort-based and hash-based algorithms for computing the relational division operation (see Practise Exercises of Chapter 6 for a definition of the division operation).

**12.9** What is the effect on the cost of merging runs if the number of buffer blocks per run is increased, while keeping overall memory available for buffering runs fixed?

**Exercises**

**12.10** Suppose you need to sort a relation of 40 gigabytes, with 4 kilobyte blocks, using a memory size of 40 megabytes. Suppose the cost of a seek is 5 milliseconds, while the disk transfer rate is 40 megabytes per second.

a. Find the cost of sorting the relation, in seconds, with _bb_ \= 1 and with _bb_ \= 100.

b. In each case, how many merge passes are required?

c. Suppose a flash storage device is used instead of a disk, and it has a seek time of 1 microsecond, and a transfer rate of 40 megabytes per second. Recompute the cost of sorting the relation, in seconds, with _bb_ \= 1 and with _bb_ \= 100, in this setting.

**12.11** Consider the following extended relational-algebra operators. Describe how to implement each operation using sorting, and using hashing.

a. **Semijoin** (): _r_  _s_ is defined as _R_(_r _ _s_), where _R_ is the set of attributes in the schema of _r_ ; that it it selects those tuples _ri_ in _r_ for which there is a tuple _s j_ in _s_ such that _ri_ and _s j_ satisfy predicate .

b. **Anti-semijoin** (̄): _r̄s_ is defined as _r_ −_R_(_r _ _s_); that it it selects those tuples _ri_ in _r_ for which there is no tuple _s j_ in _s_ such that _ri_ and _s j_ satisfy predicate .

**12.12** Why is it not desirable to force users to make an explicit choice of a query- processing strategy? Are there cases in which it _is_ desirable for users to be aware of the costs of competing query-processing strategies? Explain your answer.

**12.13** Design a variant of the hybrid merge-join algorithm for the case where both relations are not physically sorted, but both have a sorted secondary index on the join attributes.

**12.14** Estimate the number of block transfers and seeks required by your solu- tion to Exercise 12.13 for _r_1 _ r_2, where _r_1 and _r_2 are as defined in Practice Exercise 12.3.  

**Bibliographical Notes 577**

**12.15** The hash-join algorithm as described in Section 12.5.5 computes the nat- ural join of two relations. Describe how to extend the hash-join algorithm to compute the natural left outer join, the natural right outer join and the natural full outer join. (Hint: Keep extra information with each tuple in the hash index, to detect whether any tuple in the probe relation matches the tuple in the hash index.) Try out your algorithm on the _takes_ and _student_ relations.

**12.16** Pipelining is used to avoid writing intermediate results to disk. Suppose you need to sort relation _r_ using sort–merge and merge-join the result with an already sorted relation _s_.

a. Describe how the output of the sort of _r_ can be pipelined to the merge join without being written back to disk.

b. The same idea is applicable even if both inputs to the merge join are the outputs of sort–merge operations. However, the available memory has to be shared between the two merge operations (the merge-join algorithm itself needs very little memory). What is the effect of having to share memory on the cost of each sort–merge operation?

**12.17** Write pseudocode for an iterator that implements a version of the sort –merge algorithm where the result of the final merge is pipelined to its consumers. Your pseudocode must define the standard iterator functions _open_(), _next_(), and _close_(). Show what state information the iterator must maintain between calls.

**12.18** Suppose you have to compute _AGsum_(_C_)(_r_ ) as well as _A,BGsum_(_C_)(_r_ ). Describe how to compute these together using a single sorting of _r_ .

**Bibliographical Notes**

A query processor must parse statements in the query language, and must trans- late them into an internal form. Parsing of query languages differs little from pars- ing of traditional programming languages. Most compiler texts cover the main parsing techniques, and present optimization from a programming-language point of view.

Graefe and McKenna \[1993b\] presents an excellent survey of query-evaluation techniques.

Knuth \[1973\] presents an excellent description of external sorting algorithms, including an optimization called _replacement selection_, which can create initial runs that are (on the average) twice the size of memory. Nyberg et al. \[1995\] shows that due to poor processor-cache behavior, replacement selection performs worse than in-memory quicksort for run generation, negating the benefits of generating longer runs. Nyberg et al. \[1995\] presents an efficient external sorting algorithm that takes processor cache effects into account. Query evaluation algorithms that  

**578 Chapter 12 Query Processing**

take cache effects into account have been extensively studied; see, for example, Harizopoulos and Ailamaki \[2004\].

According to performance studies conducted in the mid-1970s, database sys- tems of that period used only nested-loop join and merge join. These studies, including Blasgen and Eswaran \[1976\], which was related to the development of System R, determined that either the nested-loop join or merge join nearly always provided the optimal join method. Hence, these two were the only join algorithms implemented in System R. However, Blasgen and Eswaran \[1976\] did not include an analysis of hash-join algorithms. Today, hash joins are considered to be highly efficient and widely used.

Hash-join algorithms were initially developed for parallel database systems. Hybrid hash join is described in Shapiro \[1986\]. Zeller and Gray \[1990\] and Davison and Graefe \[1994\] describe hash-join techniques that can adapt to the available memory, which is important in systems where multiple queries may be running at the same time. Graefe et al. \[1998\] describes the use of hash joins and _hash teams_, which allow pipelining of hash joins by using the same partitioning for all hash joins in a pipeline sequence, in the Microsoft SQL Server.  

**_C H A P T E R_13 Query Optimization**

**Query optimization** is the process of selecting the most efficient query-evaluation plan from among the many strategies usually possible for processing a given query, especially if the query is complex. We do not expect users to write their queries so that they can be processed efficiently. Rather, we expect the system to construct a query-evaluation plan that minimizes the cost of query evaluation. This is where query optimization comes into play.

One aspect of optimization occurs at the relational-algebra level, where the system attempts to find an expression that is equivalent to the given expression, but more efficient to execute. Another aspect is selecting a detailed strategy for processing the query, such as choosing the algorithm to use for executing an operation, choosing the specific indices to use, and so on.

The difference in cost (in terms of evaluation time) between a good strategy and a bad strategy is often substantial, and may be several orders of magnitude. Hence, it is worthwhile for the system to spend a substantial amount of time on the selection of a good strategy for processing a query, even if the query is executed only once.

**13.1 Overview**

Consider the following relational-algebra expression, for the query “Find the names of all instructors in the Music department together with the course title of all the courses that the instructors teach.”

_name,title_ (_dept name_ \= “Music” (_instructor_ (_teaches  course id ,title_ (_course_))))

Note that the projection of _course_ on (_course id_,_title_) is required since _course_ shares an attribute _dept name_ with _instructor_; if we did not remove this attribute using the projection, the above expression using natural joins would return only courses from the Music department, even if some Music department instructors taught courses in other departments.

The above expression constructs a large intermediate relation, _instructor _

_teaches  course id ,title_(_course_). However, we are interested in only a few tuples

**579**  

**580 Chapter 13 Query Optimization**

_name, title_

_dept\_name_ \= Music

_course\_id, title_

_instructor_

_instructorteaches teaches_

_course course_

∏

∏ ∏

∏ _name, title_

_course\_id, title_

σ

σ_dept\_name_ \= Music

(a) Initial expression tree (b) Transformed expression tree

**Figure 13.1** Equivalent expressions.

of this relation (those pertaining to instructors in the Music department), and in only two of the ten attributes of this relation. Since we are concerned with only those tuples in the _instructor_ relation that pertain to the Music department, we do not need to consider those tuples that do not have _dept name_ \= “Music”. By reducing the number of tuples of the _instructor_ relation that we need to access, we reduce the size of the intermediate result. Our query is now represented by the relational-algebra expression:

_name,title_ ((_dept name_ \= “Music” (_instructor_ ))  (_teaches  course id ,title_(_course_)))

which is equivalent to our original algebra expression, but which generates smaller intermediate relations. Figure 13.1 depicts the initial and transformed expressions.

An evaluation plan defines exactly what algorithm should be used for each operation, and how the execution of the operations should be coordinated. Fig- ure 13.2 illustrates one possible evaluation plan for the expression from Fig- ure 13.1(b). As we have seen, several different algorithms can be used for each relational operation, giving rise to alternative evaluation plans. In the figure, hash join has been chosen for one of the join operations, while the other uses merge join, after sorting the relations on the join attribute, which is ID. Where edges are marked as pipelined, the output of the producer is pipelined directly to the consumer, without being written out to disk.

Given a relational-algebra expression, it is the job of the query optimizer to come up with a query-evaluation plan that computes the same result as the given expression, and is the least-costly way of generating the result (or, at least, is not much costlier than the least-costly way).

To find the least-costly query-evaluation plan, the optimizer needs to gener- ate alternative plans that produce the same result as the given expression, and to choose the least-costly one. Generation of query-evaluation plans involves three steps: (1) generating expressions that are logically equivalent to the given ex-  

**13.1 Overview 581**

Π _name, title_

_instructor_

σ_dept\_name_ \= Music

sort_ID_

_teaches_

(sort to remove duplicates)

(hash join)

(merge join)

pipeline

(use index 1)

pipeline

_course_

Π _course\_id, title_

sort_ID_ pipeline

pipeline

**Figure 13.2** An evaluation plan.

pression, (2) annotating the resultant expressions in alternative ways to generate alternative query-evaluation plans, and (3) estimating the cost of each evaluation plan, and choosing the one whose estimated cost is the least.

Steps (1), (2), and (3) are interleaved in the query optimizer—some expres- sions are generated, and annotated to generate evaluation plans, then further expressions are generated and annotated, and so on. As evaluation plans are generated, their costs are estimated by using statistical information about the relations, such as relation sizes and index depths.

To implement the first step, the query optimizer must generate expressions equivalent to a given expression. It does so by means of _equivalence rules_ that spec- ify how to transform an expression into a logically equivalent one. We describe these rules in Section 13.2.

In Section 13.3 we describe how to estimate statistics of the results of each op- eration in a query plan. Using these statistics with the cost formulae in Chapter 12 allows us to estimate the costs of individual operations. The individual costs are combined to determine the estimated cost of evaluating a given relational-algebra expression, as outlined earlier in Section 12.7.

In Section 13.4, we describe how to choose a query-evaluation plan. We can choose one based on the estimated cost of the plans. Since the cost is an estimate, the selected plan is not necessarily the least-costly plan; however, as long as the estimates are good, the plan is likely to be the least-costly one, or not much more costly than it.

Finally, materialized views help to speed up processing of certain queries. In Section 13.5, we study how to “maintain” materialized views—that is, to keep them up-to-date—and how to perform query optimization with materialized views.  

**582 Chapter 13 Query Optimization**

**VIEWING QUERY EVALUATION PLANS**

Most database systems provide a way to view the evaluation plan chosen to execute a given query. It is usually best to use the GUI provided with the database system to view evaluation plans. However, if you use a command line interface, many databases support variations of a command “**explain** _<_query_\>_”, which displays the execution plan chosen for the specified query _<_query_\>_. The exact syntax varies with different databases:

• PostgreSQL uses the syntax shown above.

• Oracle uses the syntax **explain plan for**. However, the command stores the resultant plan in a table called _plan table_, instead of displaying it. The query “**select** \* **from table**(_dbms xplan.display_);” displays the stored plan.

• DB2 follows a similar approach to Oracle, but requires the program db2exfmt to be executed to display the stored plan.

• SQL Server requires the command **set showplan text on** to be executed before submitting the query; then, when a query is submitted, instead of executing the query, the evaluation plan is displayed.

The estimated costs for the plan are also displayed along with the plan. It is worth noting that the costs are usually not in any externally meaningful unit, such as seconds or I/O operations, but rather in units of whatever cost model the optimizer uses. Some optimizers such as PostgreSQL display two cost-estimate numbers; the first indicates the estimated cost for outputting the first result, and the second indicates the estimated cost for outputting all results.

**13.2 Transformation of Relational Expressions**

A query can be expressed in several different ways, with different costs of eval- uation. In this section, rather than take the relational expression as given, we consider alternative, equivalent expressions.

Two relational-algebra expressions are said to be **equivalent** if, on every legal database instance, the two expressions generate the same set of tuples. (Recall that a legal database instance is one that satisfies all the integrity constraints specified in the database schema.) Note that the order of the tuples is irrelevant; the two expressions may generate the tuples in different orders, but would be considered equivalent as long as the set of tuples is the same.

In SQL, the inputs and outputs are multisets of tuples, and the multiset version of the relational algebra (described in the box in page 238) is used for evaluating SQL queries. Two expressions in the _multiset_ version of the relational algebra are said to be equivalent if on every legal database the two expressions generate the same multiset of tuples. The discussion in this chapter is based on the relational  

**13.2 Transformation of Relational Expressions 583**

θ

_E1 E2_

θ

_E2 E1_

Rule 5

_E_3

_E1 E2 E2 E_3

_E1_

Rule 6.a

Rule 7.a

If only has attributes from E1

_E1 E2 E1_

_E2_

σθ

σθ θ

**Figure 13.3** Pictorial representation of equivalences.

algebra. We leave extensions to the multiset version of the relational algebra to you as exercises.

**13.2.1 Equivalence Rules**

An **equivalence rule** says that expressions of two forms are equivalent. We can replace an expression of the first form by an expression of the second form, or vice versa—that is, we can replace an expression of the second form by an expression of the first form—since the two expressions generate the same result on any valid database. The optimizer uses equivalence rules to transform expressions into other logically equivalent expressions.

We now list a number of general equivalence rules on relational-algebra expressions. Some of the equivalences listed appear in Figure 13.3. We use _,_ 1_,_ 2, and so on to denote predicates, _L_1, _L_2, _L_3, and so on to denote lists of attributes, and _E, E_1_, E_2, and so on to denote relational-algebra expressions. A relation name _r_ is simply a special case of a relational-algebra expression, and can be used wherever _E_ appears.

**1\.** Conjunctive selection operations can be deconstructed into a sequence of individual selections. This transformation is referred to as a cascade of .

1∧2 (_E_) = 1 (2 (_E_))  

**584 Chapter 13 Query Optimization**

**2\.** Selection operations are **commutative**.

1 (2 (_E_)) = 2 (1 (_E_))

**3\.** Only the final operations in a sequence of projection operations are needed; the others can be omitted. This transformation can also be referred to as a cascade of .

_L_1 (_L_2 (_. . ._ (_Ln_ (_E_)) _. . ._)) = _L_1 (_E_)

**4\.** Selections can be combined with Cartesian products and theta joins.

a. (_E_1 × _E_2) = _E_1  _E_2

This expression is just the definition of the theta join.

b. 1 (_E_1 2 _E_2) = _E_1 1∧2 _E_2

**5\.** Theta-join operations are commutative.

_E_1  _E_2 = _E_2  _E_1

Actually, the order of attributes differs between the left-hand side and right- hand side, so the equivalence does not hold if the order of attributes is taken into account. A projection operation can be added to one of the sides of the equivalence to appropriately reorder attributes, but for simplicity we omit the projection and ignore the attribute order in most of our examples.

Recall that the natural-join operator is simply a special case of the theta-join operator; hence, natural joins are also commutative.

**6\.** a. Natural-join operations are **associative**.

(_E_1 _ E_2) _ E_3 = _E_1  (_E_2 _ E_3)

b. Theta joins are associative in the following manner:

(_E_1 1 _E_2) 2∧3 _E_3 = _E_1 1∧3 (_E_2 2 _E_3)

where 2 involves attributes from only _E_2 and _E_3\. Any of these condi- tions may be empty; hence, it follows that the Cartesian product (×) operation is also associative. The commutativity and associativity of join operations are important for join reordering in query optimization.

**7\.** The selection operation distributes over the theta-join operation under the following two conditions:

a. It distributes when all the attributes in selection condition 0 involve only the attributes of one of the expressions (say, _E_1) being joined.

0 (_E_1  _E_2) = (0 (_E_1))  _E_2  

**13.2 Transformation of Relational Expressions 585**

b. It distributes when selection condition 1 involves only the attributes of _E_1 and 2 involves only the attributes of _E_2.

1∧2 (_E_1  _E_2) = (1 (_E_1))  (2 (_E_2))

**8\.** The projection operation distributes over the theta-join operation under the following conditions.

a. Let _L_1 and _L_2 be attributes of _E_1 and _E_2, respectively. Suppose that the join condition  involves only attributes in _L_1 ∪ _L_2\. Then,

_L_1∪_L_2 (_E_1  _E_2) = (_L_1 (_E_1))  (_L_2 (_E_2))

b. Consider a join _E_1  _E_2\. Let _L_1 and _L_2 be sets of attributes from _E_1 and _E_2, respectively. Let _L_3 be attributes of _E_1 that are involved in join condition , but are not in _L_1 ∪ _L_2, and let _L_4 be attributes of _E_2 that are involved in join condition , but are not in _L_1 ∪ _L_2\. Then,

_L_1∪_L_2 (_E_1  _E_2) = _L_1∪_L_2 ((_L_1∪_L_3 (_E_1))  (_L_2∪_L_4 (_E_2)))

**9\.** The set operations union and intersection are commutative.

_E_1 ∪ _E_2 = _E_2 ∪ _E_1

_E_1 ∩ _E_2 = _E_2 ∩ _E_1

Set difference is not commutative.

**10\.** Set union and intersection are associative.

(_E_1 ∪ _E_2) ∪ _E_3 = _E_1 ∪ (_E_2 ∪ _E_3)

(_E_1 ∩ _E_2) ∩ _E_3 = _E_1 ∩ (_E_2 ∩ _E_3)

**11\.** The selection operation distributes over the union, intersection, and set- difference operations.

_P_ (_E_1 − _E_2) = _P_ (_E_1) − _P_ (_E_2)

Similarly, the preceding equivalence, with − replaced with either ∪ or ∩, also holds. Further:

_P_ (_E_1 − _E_2) = _P_ (_E_1) − _E_2

The preceding equivalence, with − replaced by ∩, also holds, but does not hold if − is replaced by ∪.

**12\.** The projection operation distributes over the union operation.

_L_ (_E_1 ∪ _E_2) = (_L_(_E_1)) ∪ (_L_ (_E_2))  

**586 Chapter 13 Query Optimization**

This is only a partial list of equivalences. More equivalences involving ex- tended relational operators, such as the outer join and aggregation, are discussed in the exercises.

**13.2.2 Examples of Transformations**

We now illustrate the use of the equivalence rules. We use our university example with the relation schemas:

_instructor_(_ID_, _name_, _dept name_, _salary_) _teaches_(_ID_, _course id_, _sec id_, _semester_, _year_) _course_(_course id_, _title_, _dept name_, _credits_)

In our example in Section 13.1, the expression:

_name,title_ (_dept name_ \= “Music” (_instructor_ (_teaches  course id ,title_ (_course_))))

was transformed into the following expression:

_name,title_ ((_dept name_ \= “Music” (_instructor_ ))  (_teaches  course id ,title_(_course_)))

which is equivalent to our original algebra expression, but generates smaller intermediate relations. We can carry out this transformation by using rule 7.a. Remember that the rule merely says that the two expressions are equivalent; it does not say that one is better than the other.

Multiple equivalence rules can be used, one after the other, on a query or on parts of the query. As an illustration, suppose that we modify our original query to restrict attention to instructors who have taught a course in 2009. The new relational-algebra query is:

_name,title_ (_dept name_ \= “Music” ∧ _year_ \= 2009 (_instructor_ (_teaches  course id ,title_ (_course_))))

We cannot apply the selection predicate directly to the _instructor_ relation, since the predicate involves attributes of both the _instructor_ and _teaches_ relations. However, we can first apply rule 6.a (associativity of natural join) to transform the join _instructor_ (_teaches  course id ,title_ (_course_)) into (_instructor  teaches_) 

_course id ,title_ (_course_):

_name,title_ (_dept name_ \= “Music” ∧ _year_ \= 2009 ((_instructor  teaches_) _course id ,title_ (_course_)))

Then, using rule 7.a, we can rewrite our query as:  

**13.2 Transformation of Relational Expressions 587**

_name,title_ ((_dept name_ \= “Music” ∧ _year_ \= 2009 (_instructor  teaches_)) _course id ,title_ (_course_))

Let us examine the selection subexpression within this expression. Using rule 1, we can break the selection into two selections, to get the following subex- pression:

_dept name_ \= “Music” (_year_ \= 2009 (_instructor  teaches_))

Both of the preceding expressions select tuples with _dept name_ \= “Music” and _course id_ \= 2009. However, the latter form of the expression provides a new opportunity to apply Rule 7.a (“perform selections early”), resulting in the subex- pression:

_dept name_ \= “Music” (_instructor_ )  _year_ \= 2009 (_teaches_)

Figure 13.4 depicts the initial expression and the final expression after all these transformations. We could equally well have used rule 7.b to get the final expression directly, without using rule 1 to break the selection into two selections. In fact, rule 7.b can itself be derived from rules 1 and 7.a.

A set of equivalence rules is said to be **minimal** if no rule can be derived from any combination of the others. The preceding example illustrates that the set of equivalence rules in Section 13.2.1 is not minimal. An expression equivalent to the original expression may be generated in different ways; the number of different ways of generating an expression increases when we use a nonminimal set of equivalence rules. Query optimizers therefore use minimal sets of equivalence rules.

Now consider the following form of our example query:

_name, title name, title_

_course\_id, title_

∏

∏

_dept\_name =_ Music ∧ _year =_ 2009

σ

_instructor_

_teaches_

_course_

_course\_id, title_

∏

∏

_dept\_name =_ Music _year =_ 2009σ σ

_instructor teaches course_

(a) Initial expression tree (b) Tree after multiple transformations

**Figure 13.4** Multiple transformations.  

**588 Chapter 13 Query Optimization**

_name,title_ ((_dept name_ \= “Music” (_instructor_ ) _ teaches_) _course id ,title_ (_course_))

When we compute the subexpression:

(_dept name_ \= “Music” (_instructor_ ) _ teaches_)

we obtain a relation whose schema is:

(_ID_, _name_, _dept name_, _salary_, _course id_, _sec id_, _semester_, _year_)

We can eliminate several attributes from the schema by pushing projections based on equivalence rules 8.a and 8.b. The only attributes that we must retain are those that either appear in the result of the query or are needed to process subsequent operations. By eliminating unneeded attributes, we reduce the number of columns of the intermediate result. Thus, we reduce the size of the intermediate result. In our example, the only attributes we need from the join of _instructor_ and _teaches_ are _name_ and _course id_. Therefore, we can modify the expression to:

_name,title_ ((_name,course id_ ((_dept name_ \= “Music” (_instructor_ )) _ teaches_) _ course id ,title_(_course_))

The projection _name,course id_ reduces the size of the intermediate join results.

**13.2.3 Join Ordering**

A good ordering of join operations is important for reducing the size of temporary results; hence, most query optimizers pay a lot of attention to the join order. As mentioned in Chapter 6 and in equivalence rule 6.a, the natural-join operation is associative. Thus, for all relations _r_1_, r_2, and _r_3:

(_r_1 _ r_2) _ r_3 = _r_1  (_r_2 _ r_3)

Although these expressions are equivalent, the costs of computing them may differ. Consider again the expression:

_name,title_ ((_dept name_ \= “Music” (_instructor_ )) _teaches  course id ,title_ (_course_))

We could choose to compute _teaches  course id ,title_ (_course_) first, and then to join the result with:

_dept name_ \= “Music” (_instructor_ )

However, _teaches  course id ,title_ (_course_) is likely to be a large relation, since it contains one tuple for every course taught. In contrast:

_dept name_ \= “Music” (_instructor_ ) _ teaches_  

**13.2 Transformation of Relational Expressions 589**

is probably a small relation. To see that it is, we note that, since a university has a large number of departments, it is likely that only a small fraction of the university instructors are associated with the Music department. Thus, the preceding expression results in one tuple for each course taught by an instructor in the Music department. Therefore, the temporary relation that we must store is smaller than it would have been had we computed _teaches  course id ,title_ (_course_) first.

There are other options to consider for evaluating our query. We do not care about the order in which attributes appear in a join, since it is easy to change the order before displaying the result. Thus, for all relations _r_1 and _r_2:

_r_1 _ r_2 = _r_2 _ r_1

That is, natural join is commutative (equivalence rule 5). Using the associativity and commutativity of the natural join (rules 5 and 6),

consider the following relational-algebra expression:

(_instructor  course id ,title_(_course_)) _ teaches_

Note that there are no attributes in common between _course id ,title_ (_course_) and _instructor_, so the join is just a Cartesian product. If there are _a_ tuples in _instructor_ and _b_ tuples in _course id ,title_ (_course_), this Cartesian product generates _a_ ∗ _b_ tuples, one for every possible pair of instructor tuple and course (without regard for whether the instructor taught the course). This Cartesian product would produce a very large temporary relation. However, if the user had entered the preceding expression, we could use the associativity and commutativity of the natural join to transform this expression to the more efficient expression:

(_instructor  teaches_) _course id ,title_ (_course_)

**13.2.4 Enumeration of Equivalent Expressions**

Query optimizers use equivalence rules to systematically generate expressions equivalent to the given query expression. Conceptually, this can be done as out- lined in Figure 13.5. The process proceeds as follows. Given a query expression _E_ , the set of equivalent expressions _EQ_ initially contains only _E_ . Now, each expres- sion in _EQ_ is matched with each equivalence rule. If an expression, say _Ei_ , of any subexpression _ei_ of _Ei_ (which could, as a special case, be _Ei_ itself) matches one side of an equivalence rule, the optimizer generates a new expression where _ei_ is transformed to match the other side of the rule. The resultant expression is added to _EQ_. This process continues until no more new expressions can be generated.

The preceding process is extremely costly both in space and in time, but optimizers can greatly reduce both the space and time cost, using two key ideas.

**1\.** If we generate an expression _E_ ′ from an expression _E_1 by using an equiva- lence rule on subexpression _ei_ , then _E_ ′ and _E_1 have identical subexpressions  

**590 Chapter 13 Query Optimization**

**procedure** genAllEquivalent(_E_) _EQ_ \= {_E_} **repeat**

Match each expression _Ei_ in _EQ_ with each equivalence rule _Rj_ **if** any subexpression _ei_ of _Ei_ matches one side of _Rj_

Create a new expression _E_ ′ which is identical to _Ei_ , except that _ei_ is transformed to match the other side of _Rj_

Add _E_ ′ to _EQ_ if it is not already present in _EQ_ **until** no new expression can be added to _EQ_

**Figure 13.5** Procedure to generate all equivalent expressions.

except for _ei_ and its transformation. Even _ei_ and its transformed version usu- ally share many identical subexpressions. Expression-representation tech- niques that allow both expressions to point to shared subexpressions can reduce the space requirement significantly.

**2\.** It is not always necessary to generate every expression that can be generated with the equivalence rules. If an optimizer takes cost estimates of evaluation into account, it may be able to avoid examining some of the expressions, as we shall see in Section 13.4. We can reduce the time required for optimization by using techniques such as these.

We revisit these issues in Section 13.4.2.

**13.3 Estimating Statistics of Expression Results**

The cost of an operation depends on the size and other statistics of its inputs. Given an expression such as _a_ (_b  c_) to estimate the cost of joining _a_ with (_b  c_), we need to have estimates of statistics such as the size of _b  c_.

In this section, we first list some statistics about database relations that are stored in database-system catalogs, and then show how to use the statistics to estimate statistics on the results of various relational operations.

One thing that will become clear later in this section is that the estimates are not very accurate, since they are based on assumptions that may not hold exactly. A query-evaluation plan that has the lowest estimated execution cost may therefore not actually have the lowest actual execution cost. However, real- world experience has shown that even if estimates are not precise, the plans with the lowest estimated costs usually have actual execution costs that are either the lowest actual execution costs, or are close to the lowest actual execution costs.

**13.3.1 Catalog Information**

The database-system catalog stores the following statistical information about database relations:  

**13.3 Estimating Statistics of Expression Results 591**

• _nr_ , the number of tuples in the relation _r_.

• _br_ , the number of blocks containing tuples of relation _r_ .

• _lr_ , the size of a tuple of relation _r_ in bytes.

• _fr_ , the blocking factor of relation _r_ —that is, the number of tuples of relation _r_ that fit into one block.

• _V_(_A, r_ ), the number of distinct values that appear in the relation _r_ for attribute _A_. This value is the same as the size of _A_(_r_ ). If _A_is a key for relation _r_ , _V_(_A, r_ ) is _nr_ .

The last statistic, _V_(_A, r_ ), can also be maintained for sets of attributes, if desired, instead of just for individual attributes. Thus, given a set of attributes, _A_, _V_(_A, r_ ) is the size of _A_(_r_ ).

If we assume that the tuples of relation _r_ are stored together physically in a file, the following equation holds:

_br_ \= ⌈

_nr_

_fr_

⌉

Statistics about indices, such as the heights of B+-tree indices and number of leaf pages in the indices, are also maintained in the catalog.

If we wish to maintain accurate statistics, then, every time a relation is modi- fied, we must also update the statistics. This update incurs a substantial amount of overhead. Therefore, most systems do not update the statistics on every mod- ification. Instead, they update the statistics during periods of light system load. As a result, the statistics used for choosing a query-processing strategy may not be completely accurate. However, if not too many updates occur in the intervals between the updates of the statistics, the statistics will be sufficiently accurate to provide a good estimation of the relative costs of the different plans.

The statistical information noted here is simplified. Real-world optimizers often maintain further statistical information to improve the accuracy of their cost estimates of evaluation plans. For instance, most databases store the distribution of values for each attribute as a **histogram**: in a histogram the values for the attribute are divided into a number of ranges, and with each range the histogram associates the number of tuples whose attribute value lies in that range. Figure 13.6 shows an example of a histogram for an integer-valued attribute that takes values in the range 1 to 25.

Histograms used in database systems usually record the number of distinct values in each range, in addition to the number of tuples with attribute values in that range.

As an example of a histogram, the range of values for an attribute _age_ of a re- lation _person_ could be divided into 0–9, 10–19, . . . , 90–99 (assuming a maximum age of 99). With each range we store a count of the number of _person_ tuples whose _age_ values lie in that range, and the number of distinct age values that lie in that  

**592 Chapter 13 Query Optimization**

value

fr eq

ue nc

y

50

40

30

20

10

1–5 6–10 11–15 16–20 21–25

**Figure 13.6** Example of histogram.

range. Without such histogram information, an optimizer would have to assume that the distribution of values is uniform; that is, each range has the same count.

A histogram takes up only a little space, so histograms on several different at- tributes can be stored in the system catalog. There are several types of histograms used in database systems. For example, an **equi-width histogram** divides the range of values into equal-sized ranges, whereas an **equi-depth** histogram ad- justs the boundaries of the ranges such that each range has the same number of values.

**13.3.2 Selection Size Estimation**

The size estimate of the result of a selection operation depends on the selection predicate. We first consider a single equality predicate, then a single comparison predicate, and finally combinations of predicates.

• _A_\= _a_ (_r_ ): If we assume uniform distribution of values (that is, each value ap- pears with equal probability), the selection result can be estimated to have _nr/V_(_A, r_ ) tuples, assuming that the value _a_ appears in attribute _A_ of some record of _r_ . The assumption that the value _a_ in the selection appears in some record is generally true, and cost estimates often make it implicitly. However, it is often not realistic to assume that each value appears with equal prob- ability. The _course id_ attribute in the _takes_ relation is an example where the assumption is not valid. It is reasonable to expect that a popular undergradu- ate course will have many more students than a smaller specialized graduate course. Therefore, certain _course id_ values appear with greater probability than do others. Despite the fact that the uniform-distribution assumption is often not correct, it is a reasonable approximation of reality in many cases, and it helps us to keep our presentation relatively simple.

If a histogram is available on attribute _A_, we can locate the range that contains the value _a_ , and modify the above-mentioned estimate _nr/V_(_A, r_ )  

**13.3 Estimating Statistics of Expression Results 593**

**COMPUTING AND MAINTAINING STATISTICS**

Conceptually, statistics on relations can be thought of as materialized views, which should be automatically maintained when relations are modified. Unfor- tunately, keeping statistics up-to-date on every insert, delete or update to the database can be very expensive. On the other hand, optimizers generally do not need exact statistics: an error of a few percent may result in a plan that is not quite optimal being chosen, but the alternative plan chosen is likely to have a cost which is within a few percent of the optimal cost. Thus, it is acceptable to have statistics that are approximate.

Database systems reduce the cost of generating and maintaining statistics, as outlined below, by exploiting the fact that statistics can be approximate.

• Statistics are often computed from a sample of the underlying data, instead of examining the entire collection of data. For example, a fairly accurate histogram can be computed from a sample of a few thousand tuples, even on a relation that has millions, or hundreds of millions of records. However, the sample used must be a **random sample**; a sample that is not random may have an excessive representation of one part of the relation, and can give misleading results. For example, if we used a sample of instructors to compute a histogram on salaries, if the sample has an overrepresentation of lower-paid instructors the histogram would result in wrong estimates. Database systems today routinely use random sampling to create statistics. See the bibliographic notes for references on sampling.

• Statistics are not maintained on every update to the database. In fact, some database systems never update statistics automatically. They rely on database administrators periodically running a command to update statis- tics. Oracle and PostgreSQL provide an SQL command called **analyze** that generates statistics on specified relations, or on all relations. IBM DB2 sup- ports an equivalent command called **runstats**. See the system manuals for details. You should be aware that optimizers sometimes choose very bad plans due to incorrect statistics. Many database systems, such as IBM DB2, Oracle, and SQL Server, update statistics automatically at certain points of time. For example, the system can keep approximate track of how many tuples there are in a relation and recompute statistics if this number changes significantly. Another approach is to compare estimated cardinalities of a relation scan with actual cardinalities when a query is executed, and if they differ significantly, initiate an update of statistics for that relation.

by using the frequency count for that range instead of _nr_ , and the number of distinct values that occurs in that range instead of _V_(_A, r_ ).

• _A_≤_v_(_r_ ): Consider a selection of the form _A_≤_v_(_r_ ). If the actual value used in the comparison (_v_) is available at the time of cost estimation, a more  

**594 Chapter 13 Query Optimization**

accurate estimate can be made. The lowest and highest values (min(_A, r_ ) and max(_A, r_ )) for the attribute can be stored in the catalog. Assuming that values are uniformly distributed, we can estimate the number of records that will satisfy the condition _A_ ≤ _v_ as 0 if _v <_ min(_A, r_ ), as _nr_ if _v_ ≥ max(_A, r_ ), and:

_nr_ · _v_ − min(_A, r_ ) max(_A, r_ ) − min(_A, r_ )

otherwise. If a histogram is available on attribute _A_, we can get a more accurate

estimate; we leave the details as an exercise for you. In some cases, such as when the query is part of a stored procedure, the value _v_ may not be available when the query is optimized. In such cases, we assume that approximately one-half the records will satisfy the comparison condition. That is, we assume the result has _nr/_2 tuples; the estimate may be very inaccurate, but is the best we can do without any further information.

• Complex selections:

◦ **Conjunction:** A _conjunctive selection_ is a selection of the form:

1∧2∧···∧_n_(_r_ )

We can estimate the result size of such a selection: For each _i_ , we estimate the size of the selection _i_ (_r_ ), denoted by _si_ , as described previously. Thus, the probability that a tuple in the relation satisfies selection condition _i_ is _si/nr_ .

The preceding probability is called the **selectivity** of the selection _i_ (_r_ ). Assuming that the conditions are _independent_ of each other, the probability that a tuple satisfies all the conditions is simply the product of all these probabilities. Thus, we estimate the number of tuples in the full selection as:

_nr_ ∗ _s_1 ∗ _s_2 ∗ · · · ∗ _sn_

_nn r_

◦ **Disjunction:** A _disjunctive selection_ is a selection of the form:

1∨2∨···∨_n_ (_r_ )

A disjunctive condition is satisfied by the union of all records satisfying the individual, simple conditions _i_ .

As before, let _si/nr_ denote the probability that a tuple satisfies condition _i_ . The probability that the tuple will satisfy the disjunction is then 1 minus the probability that it will satisfy _none_ of the conditions:

1 − (1 − _s_1

_nr_ ) ∗ (1 − _s_2

_nr_ ) ∗ · · · ∗ (1 − _sn_

_nr_ )

Multiplying this value by _nr_ gives us the estimated number of tuples that satisfy the selection.  

**13.3 Estimating Statistics of Expression Results 595**

◦ **Negation:** In the absence of nulls, the result of a selection ¬(_r_ ) is simply the tuples of _r_ that are not in (_r_ ). We already know how to estimate the number of tuples in (_r_ ). The number of tuples in ¬(_r_ ) is therefore estimated to be _n_(_r_ ) minus the estimated number of tuples in (_r_ ).

We can account for nulls by estimating the number of tuples for which the condition  would evaluate to _unknown_, and subtracting that number from the above estimate, ignoring nulls. Estimating that number would require extra statistics to be maintained in the catalog.

**13.3.3 Join Size Estimation**

In this section, we see how to estimate the size of the result of a join. The Cartesian product _r_ ×_s_ contains _nr_ ∗_ns_ tuples. Each tuple of _r_ ×_s_ occupies

_lr_ \+ _ls_ bytes, from which we can calculate the size of the Cartesian product. Estimating the size of a natural join is somewhat more complicated than

estimating the size of a selection or of a Cartesian product. Let _r_ (_R_) and _s_(_S_) be relations.

• If _R_ ∩ _S_ \= ∅—that is, the relations have no attribute in common—then _r  s_ is the same as _r_ × _s_, and we can use our estimation technique for Cartesian products.

• If _R_ ∩ _S_ is a key for _R_, then we know that a tuple of _s_ will join with at most one tuple from _r_ . Therefore, the number of tuples in _r  s_ is no greater than the number of tuples in _s_. The case where _R_ ∩ _S_ is a key for _S_ is symmetric to the case just described. If _R_ ∩ _S_ forms a foreign key of _S_, referencing _R_, the number of tuples in _r  s_ is exactly the same as the number of tuples in _s_.

• The most difficult case is when _R_ ∩ _S_ is a key for neither _R_ nor _S_. In this case, we assume, as we did for selections, that each value appears with equal probability. Consider a tuple _t_ of _r_ , and assume _R_ ∩ _S_ \= _{_A_}_. We estimate that tuple _t_ produces:

_ns_

_V_(_A, s_)

tuples in _r  s_, since this number is the average number of tuples in _s_ with a given value for the attributes _A_. Considering all the tuples in _r_ , we estimate that there are:

_nr_ ∗ _ns_

_V_(_A, s_)

tuples in _r  s_. Observe that, if we reverse the roles of _r_ and _s_ in the preceding estimate, we obtain an estimate of:

_nr_ ∗ _ns_

_V_(_A, r_ )  

**596 Chapter 13 Query Optimization**

tuples in _r  s_. These two estimates differ if _V_(_A, r_ ) = _V_(_A, s_). If this situation occurs, there are likely to be dangling tuples that do not participate in the join. Thus, the lower of the two estimates is probably the more accurate one.

The preceding estimate of join size may be too high if the _V_(_A, r_ ) values for attribute _A_ in _r_ have few values in common with the _V_(_A, s_) values for attribute _A_ in _s_. However, this situation is unlikely to happen in the real world, since dangling tuples either do not exist or constitute only a small fraction of the tuples, in most real-world relations.

More important, the preceding estimate depends on the assumption that each value appears with equal probability. More sophisticated techniques for size estimation have to be used if this assumption does not hold. For example, if we have histograms on the join attributes of both relations, and both histograms have the same ranges, then we can use the above estimation technique within each range, using the number of rows with values in the range instead of _nr_ or _ns_ , and the number of distinct values in that range, instead of _V_(_A, r_ ) or _V_(_A, s_). We then add up the size estimates obtained for each range to get the overall size estimate. We leave the case where both relations have histograms on the join attribute, but the histograms have different ranges, as an exercise for you.

We can estimate the size of a theta join _r _ _s_ by rewriting the join as (_r_ × _s_), and using the size estimates for Cartesian products along with the size estimates for selections, which we saw in Section 13.3.2.

To illustrate all these ways of estimating join sizes, consider the expression:

_student  takes_

Assume the following catalog information about the two relations:

• _nstudent_ \= 5000.

• _fstudent_ \= 50, which implies that _bstudent_ \= 5000_/_50 = 100.

• _ntakes_ \= 10000.

• _ftakes_ \= 25, which implies that _btakes_ \= 10000_/_25 = 400.

• _V_(_ID, takes_) = 2500, which implies that only half the students have taken any course (this is unrealistic, but we use it to show that our size estimates are correct even in this case), and on average, each student who has taken a course has taken four courses.

The attribute _ID_ in _takes_ is a foreign key on _student_, and null values do not occur in _takes_._ID_, since _ID_ is part of the primary key of _takes_; thus, the size of _student  takes_ is exactly _ntakes_ , which is 10000.

We now compute the size estimates for _student  takes_ without using infor- mation about foreign keys. Since _V_(_ID, takes_) = 2500 and _V_(_ID, student_) = 5000, the two estimates we get are 5000 ∗ 10000_/_2500 = 20000 and 5000 ∗ 10000_/_5000 = 10000, and we choose the lower one. In this case, the lower of these estimates is the same as that which we computed earlier from information about foreign keys.  

**13.3 Estimating Statistics of Expression Results 597**

**13.3.4 Size Estimation for Other Operations**

We outline below how to estimate the sizes of the results of other relational-algebra operations.

• **Projection:** The estimated size (number of records or number of tuples) of a projection of the form _A_(_r_ ) is _V_(_A, r_ ), since projection eliminates duplicates.

• **Aggregation:** The size of _AGF_ (_r_ ) is simply _V_(_A, r_ ), since there is one tuple in _AGF_ (_r_ ) for each distinct value of _A_.

• **Set operations:** If the two inputs to a set operation are selections on the same relation, we can rewrite the set operation as disjunctions, conjunctions, or negations. For example, 1 (_r_ ) ∪ 2 (_r_ ) can be rewritten as 1∨2 (_r_ ). Simi- larly, we can rewrite intersections as conjunctions, and we can rewrite set difference by using negation, so long as the two relations participating in the set operations are selections on the same relation. We can then use the estimates for selections involving conjunctions, disjunctions, and negation in Section 13.3.2.

If the inputs are not selections on the same relation, we estimate the sizes this way: The estimated size of _r_ ∪ _s_ is the sum of the sizes of _r_ and _s_. The estimated size of _r_ ∩ _s_ is the minimum of the sizes of _r_ and _s_. The estimated size of _r_ − _s_ is the same size as _r_ . All three estimates may be inaccurate, but provide upper bounds on the sizes.

• **Outer join:** The estimated size of _r  s_ is the size of _r  s_ plus the size of _r_ ; that of _r  s_ is symmetric, while that of _r  s_ is the size of _r  s_ plus the sizes of _r_ and _s_. All three estimates may be inaccurate, but provide upper bounds on the sizes.

**13.3.5 Estimation of Number of Distinct Values**

For selections, the number of distinct values of an attribute (or set of attributes) _A_ in the result of a selection, _V_(_A,_ (_r_ )), can be estimated in these ways:

• If the selection condition  forces _A_ to take on a specified value (e.g., _A_ \= 3), _V_(_A,_ (_r_ )) = 1.

• If  forces _A_ to take on one of a specified set of values (e.g., (_A_ \= 1 ∨ _A_ \= 3 ∨ _A_ \= 4)), then _V_(_A,_ (_r_ )) is set to the number of specified values.

• If the selection condition  is of the form _A op v_, where _op_ is a comparison operator, _V_(_A,_ (_r_ )) is estimated to be _V_(_A, r_ ) ∗ _s_, where _s_ is the selectivity of the selection.

• In all other cases of selections, we assume that the distribution of _A_ values is independent of the distribution of the values on which selection conditions are specified, and we use an approximate estimate of min(_V_(_A, r_ )_, n_(_r_ )). A more accurate estimate can be derived for this case using probability theory, but the above approximation works fairly well.  

**598 Chapter 13 Query Optimization**

For joins, the number of distinct values of an attribute (or set of attributes) _A_ in the result of a join, _V_(_A, r  s_), can be estimated in these ways:

• If all attributes in _A_ are from _r_ , _V_(_A, r  s_) is estimated as min(_V_(_A, r_ )_, nrs_), and similarly if all attributes in _A_ are from _s_, _V_(_A, r  s_) is estimated to be min(_V_(_A, s_)_, nrs_).

• If _A_contains attributes _A_1 from _r_ and _A_2 from _s_, then _V_(_A, r  s_) is estimated as:

min(_V_(_A_1_, r_ ) ∗ _V_(_A_2 − _A_1_, s_)_, V_(_A_1 − _A_2_, r_ ) ∗ _V_(_A_2_, s_)_, nrs_)

Note that some attributes may be in _A_1 as well as in _A_2, and _A_1 − _A_2 and _A_2− _A_1 denote, respectively, attributes in _A_that are only from _r_ and attributes in _A_ that are only from _s_. Again, more accurate estimates can be derived by using probability theory, but the above approximations work fairly well.

The estimates of distinct values are straightforward for projections: They are the same in _A_(_r_ ) as in _r_ . The same holds for grouping attributes of aggregation. For results of **sum**, **count**, and **average**, we can assume, for simplicity, that all aggregate values are distinct. For **min**(_A_) and **max**(_A_), the number of distinct values can be estimated as min(_V_(_A, r_ )_, V_(_G, r_ )), where _G_ denotes the grouping attributes. We omit details of estimating distinct values for other operations.

**13.4 Choice of Evaluation Plans**

Generation of expressions is only part of the query-optimization process, since each operation in the expression can be implemented with different algorithms. An evaluation plan defines exactly what algorithm should be used for each op- eration, and how the execution of the operations should be coordinated.

Given an evaluation plan, we can estimate its cost using statistics estimated by the techniques in Section 13.3 coupled with cost estimates for various algorithms and evaluation methods described in Chapter 12.

A **cost-based optimizer** explores the space of all query-evaluation plans that are equivalent to the given query, and chooses the one with the least estimated cost. We have seen how equivalence rules can be used to generate equivalent plans. However, cost-based optimization with arbitrary equivalence rules is fairly complicated. We first cover a simpler version of cost-based optimization, which involves only join-order and join algorithm selection, in Section 13.4.1. Later in Section 13.4.2 we briefly sketch how a general-purpose optimizer based on equivalence rules can be built, without going into details.

Exploring the space of all possible plans may be too expensive for complex queries. Most optimizers include heuristics to reduce the cost of query optimiza- tion, at the potential risk of not finding the optimal plan. We study some such heuristics in Section 13.4.3.  

**13.4 Choice of Evaluation Plans 599**

**13.4.1 Cost-Based Join Order Selection**

The most common type of query in SQL consists of a join of a few relations, with join predicates and selections specified in the **where** clause. In this section we consider the problem of choosing the optimal join order for such a query.

For a complex join query, the number of different query plans that are equiv- alent to the query can be large. As an illustration, consider the expression:

_r_1 _ r_2  · · · _ rn_

where the joins are expressed without any ordering. With _n_ \= 3, there are 12 different join orderings:

_r_1  (_r_2 _ r_3) _r_1  (_r_3 _ r_2) (_r_2 _ r_3) _ r_1 (_r_3 _ r_2) _ r_1 _r_2  (_r_1 _ r_3) _r_2  (_r_3 _ r_1) (_r_1 _ r_3) _ r_2 (_r_3 _ r_1) _ r_2 _r_3  (_r_1 _ r_2) _r_3  (_r_2 _ r_1) (_r_1 _ r_2) _ r_3 (_r_2 _ r_1) _ r_3

In general, with _n_ relations, there are (2(_n_ − 1))!_/_(_n_ − 1)! different join orders. (We leave the computation of this expression for you to do in Exercise 13.10.) For joins involving small numbers of relations, this number is acceptable; for example, with _n_ \= 5, the number is 1680. However, as _n_ increases, this number rises quickly. With _n_ \= 7, the number is 665,280; with _n_ \= 10, the number is greater than 17_._6 billion!

Luckily, it is not necessary to generate all the expressions equivalent to a given expression. For example, suppose we want to find the best join order of the form:

(_r_1 _ r_2 _ r_3) _ r_4 _ r_5

which represents all join orders where _r_1_, r_2, and _r_3 are joined first (in some order), and the result is joined (in some order) with _r_4 and _r_5\. There are 12 different join orders for computing _r_1 _ r_2 _ r_3, and 12 orders for computing the join of this result with _r_4 and _r_5\. Thus, there appear to be 144 join orders to examine. However, once we have found the best join order for the subset of relations {_r_1_, r_2_, r_3}, we can use that order for further joins with _r_4 and _r_5, and can ignore all costlier join orders of _r_1 _ r_2 _ r_3\. Thus, instead of 144 choices to examine, we need to examine only 12 + 12 choices.

Using this idea, we can develop a _dynamic-programming_ algorithm for finding optimal join orders. Dynamic-programming algorithms store results of computa- tions and reuse them, a procedure that can reduce execution time greatly.

A recursive procedure implementing the dynamic-programming algorithm appears in Figure 13.7. The procedure applies selections on individual relations at the earliest possible point, that is, when the relations are accessed. It is easiest to understand the procedure assuming that all joins are natural joins, although the procedure works unchanged with any join condition. With arbitrary join con- ditions, the join of two subexpressions is understood to include all join conditions that relate attributes from the two subexpressions.  

**600 Chapter 13 Query Optimization**

**procedure** FindBestPlan(_S_) **if** (_bestplan_\[_S_\]_.cost _ = ∞) /\* _bestplan_\[_S_\] already computed \*/

**return** _bestplan_\[_S_\] **if** (_S_ contains only 1 relation)

set _bestplan_\[_S_\]_.plan_ and _bestplan_\[_S_\]_.cost_ based on best way of accessing _S_ **else for each** non-empty subset _S_1 of _S_ such that _S_1 = _S_

P1 = FindBestPlan(_S_1) P2 = FindBestPlan(_S_ − _S_1) A = best algorithm for joining results of _P_1 and _P_2 cost = _P_1_.cost_ \+ _P_2_.cost_ \+ cost of _A_ **if** cost _< bestplan_\[_S_\]_.cost_

_bestplan_\[_S_\]_.cost_ \= cost _bestplan_\[_S_\]_.plan_ \= “execute _P_1_.plan_; execute _P_2_.plan_;

join results of _P_1 and _P_2 using _A_” **return** _bestplan_\[_S_\]

**Figure 13.7** Dynamic-programming algorithm for join order optimization.

The procedure stores the evaluation plans it computes in an associative array _bestplan_, which is indexed by sets of relations. Each element of the associative array contains two components: the cost of the best plan of _S_, and the plan itself. The value of _bestplan_\[_S_\]_.cost_ is assumed to be initialized to ∞ if _bestplan_\[_S_\] has not yet been computed.

The procedure first checks if the best plan for computing the join of the given set of relations _S_ has been computed already (and stored in the associative array _bestplan_); if so, it returns the already computed plan.

If _S_ contains only one relation, the best way of accessing _S_ (taking selections on _S_, if any, into account) is recorded in _bestplan_. This may involve using an index to identify tuples, and then fetching the tuples (often referred to as an _index scan_), or scanning the entire relation (often referred to as a _relation scan_).1 If there is any selection condition on _S_, other than those ensured by an index scan, a selection operation is added to the plan, to ensure all selections on _S_ are satisfied.

Otherwise, if _S_ contains more than one relation, the procedure tries every way of dividing _S_ into two disjoint subsets. For each division, the procedure recursively finds the best plans for each of the two subsets, and then computes the cost of the overall plan by using that division.2 The procedure picks the cheapest plan from among all the alternatives for dividing _S_ into two sets. The cheapest plan and its cost are stored in the array _bestplan_, and returned by the

1If an index contains all the attributes of a relation that are used in a query, it is possible to perform an _index-only scan_, which retrieves the required attribute values from the index, without fetching actual tuples. 2Note that an indexed nested loops join is considered for joining _P_1 and _P_2, with _P_2 as the inner relation, if _P_2 has only a single relation, say _r_ , and an index is available on the join attributes of _r_ . Plan _P_2 may contain an indexed access to _r_ , based on selection conditions on _r_ . To allow indexed nested loops join to be used, the index lookup using the selection condition on _r_ would be dropped from _P_2; instead, the selection condition would be checked on tuples returned from the index on the join attributes of _r_ .  

**13.4 Choice of Evaluation Plans 601**

procedure. The time complexity of the procedure can be shown to be _O_(3_n_) (see Practice Exercise 13.11).

Actually, the order in which tuples are generated by the join of a set of relations is also important for finding the best overall join order, since it can affect the cost of further joins (for instance, if merge join is used). A particular sort order of the tuples is said to be an **interesting sort order** if it could be useful for a later operation. For instance, generating the result of _r_1 _ r_2 _ r_3 sorted on the attributes common with _r_4 or _r_5 may be useful, but generating it sorted on the attributes common to only _r_1 and _r_2 is not useful. Using merge join for computing _r_1 _ r_2 _ r_3 may be costlier than using some other join technique, but it may provide an output sorted in an interesting sort order.

Hence, it is not sufficient to find the best join order for each subset of the set of _n_ given relations. Instead, we have to find the best join order for each subset, for each interesting sort order of the join result for that subset. The number of subsets of _n_ relations is 2_n_. The number of interesting sort orders is generally not large. Thus, about 2_n_ join expressions need to be stored. The dynamic-programming algorithm for finding the best join order can be easily extended to handle sort orders. The cost of the extended algorithm depends on the number of interesting orders for each subset of relations; since this number has been found to be small in practice, the cost remains at _O_(3_n_). With _n_ \= 10, this number is around 59,000, which is much better than the 17_._6 billion different join orders. More important, the storage required is much less than before, since we need to store only one join order for each interesting sort order of each of 1024 subsets of _r_1_, . . . , r_10\. Although both numbers still increase rapidly with _n_, commonly occurring joins usually have less than 10 relations, and can be handled easily.

**13.4.2 Cost-Based Optimization with Equivalence Rules**

The join order optimization technique we just saw handles the most common class of queries, which perform an inner join of a set of relations. However, clearly many queries use other features, such as aggregation, outer join, and nested queries, which are not addressed by join order selection.

Many optimizers follow an approach based on using heuristic transforma- tions to handle constructs other than joins, and applying the cost-based join order selection algorithm to subexpressions involving only joins and selections. Details of such heuristics are for the most part specific to individual optimizers, and we do not cover them. However, heuristic transformations to handle nested queries are widely used, and are considered in more detail in Section 13.4.4.

In this section, however, we outline how to create a general-purpose cost- based optimizer based on equivalence rules, which can handle a wide variety of query constructs.

The benefit of using equivalence rules is that it is easy to extend the optimizer with new rules to handle different query constructs. For example, nested queries can be represented using extended relational-algebra constructs, and transforma- tions of nested queries can be expressed as equivalence rules. We have already  

**602 Chapter 13 Query Optimization**

seen equivalence rules with aggregation operations, and equivalence rules can also be created for outer joins.

In Section 13.2.4, we saw how an optimizer could systematically generate all expressions equivalent to the given query. The procedure for generating equiv- alent expressions can be modified to generate all possible evaluation plans as follows: A new class of equivalence rules, called **physical equivalence rules**, is added that allows a logical operation, such as a join, to be transformed to a phys- ical operation, such as a hash join, or a nested-loops join. By adding such rules to the original set of equivalence rules, the procedure can generate all possible evaluation plans. The cost estimation techniques we have seen earlier can then be used to choose the optimal (that is, the least-cost) plan.

However, the procedure shown in Section 13.2.4 is very expensive, even if we do not consider generation of evaluation plans. To make the approach work efficiently requires the following:

**1\.** A space-efficient representation of expressions that avoids making multiple copies of the same subexpressions when equivalence rules are applied.

**2\.** Efficient techniques for detecting duplicate derivations of the same expres- sion.

**3\.** A form of dynamic programming based on **memoization**, which stores the optimal query evaluation plan for a subexpression when it is optimized for the first time; subsequent requests to optimize the same subexpression are handled by returning the already memoized plan.

**4\.** Techniques that avoid generating all possible equivalent plans, by keeping track of the cheapest plan generated for any subexpression up to any point of time, and pruning away any plan that is more expensive than the cheapest plan found so far for that subexpression.

The details are more complex than we wish to deal with here. This approach was pioneered by the Volcano research project, and the query optimizer of SQL Server is based on this approach. See the bibliographical notes for references containing further information.

**13.4.3 Heuristics in Optimization**

A drawback of cost-based optimization is the cost of optimization itself. Although the cost of query optimization can be reduced by clever algorithms, the number of different evaluation plans for a query can be very large, and finding the optimal plan from this set requires a lot of computational effort. Hence, optimizers use **heuristics** to reduce the cost of optimization.

An example of a heuristic rule is the following rule for transforming relational- algebra queries:

• Perform selection operations as early as possible.  

**13.4 Choice of Evaluation Plans 603**

A heuristic optimizer would use this rule without finding out whether the cost is reduced by this transformation. In the first transformation example in Section 13.2, the selection operation was pushed into a join.

We say that the preceding rule is a heuristic because it usually, but not always, helps to reduce the cost. For an example of where it can result in an increase in cost, consider an expression (_r  s_), where the condition  refers to only attributes in _s_. The selection can certainly be performed before the join. However, if _r_ is extremely small compared to _s_, and if there is an index on the join attributes of _s_, but no index on the attributes used by , then it is probably a bad idea to perform the selection early. Performing the selection early—that is, directly on _s_ —would require doing a scan of all tuples in _s_. It is probably cheaper, in this case, to compute the join by using the index, and then to reject tuples that fail the selection.

The projection operation, like the selection operation, reduces the size of relations. Thus, whenever we need to generate a temporary relation, it is advan- tageous to apply immediately any projections that are possible. This advantage suggests a companion to the “perform selections early” heuristic:

• Perform projections early.

It is usually better to perform selections earlier than projections, since selections have the potential to reduce the sizes of relations greatly, and selections enable the use of indices to access tuples. An example similar to the one used for the selection heuristic should convince you that this heuristic does not always reduce the cost.

Most practical query optimizers have further heuristics to reduce the cost of optimization. For example, many query optimizers, such as the System R opti- mizer,3 do not consider all join orders, but rather restrict the search to particular kinds of join orders. The System R optimizer considers only those join orders where the right operand of each join is one of the initial relations _r_1_, . . . , rn_. Such join orders are called **left-deep join orders**. Left-deep join orders are particularly convenient for pipelined evaluation, since the right operand is a stored relation, and thus only one input to each join is pipelined.

Figure 13.8 illustrates the difference between left-deep join trees and non-left- deep join trees. The time it takes to consider all left-deep join orders is _O_(_n_!), which is much less than the time to consider all join orders. With the use of dynamic- programming optimizations, the System R optimizer can find the best join order in time _O_(_n_2_n_). Contrast this cost with the _O_(3_n_) time required to find the best overall join order. The System R optimizer uses heuristics to push selections and projections down the query tree.

A heuristic approach to reduce the cost of join-order selection, which was originally used in some versions of Oracle, works roughly this way: For an _n_\-way join, it considers _n_ evaluation plans. Each plan uses a left-deep join order, starting

3System R was one of the first implementations of SQL, and its optimizer pioneered the idea of cost-based join-order optimization.  

**604 Chapter 13 Query Optimization**

_r4 r5r3_

_r1 r2_

_r5_

_r4_

_r3_

_r2r1_

(a) Left-deep join tree (b) Non-left-deep join tree

**Figure 13.8** Left-deep join trees.

with a different one of the _n_ relations. The heuristic constructs the join order for each of the _n_ evaluation plans by repeatedly selecting the “best” relation to join next, on the basis of a ranking of the available access paths. Either nested-loop or sort-merge join is chosen for each of the joins, depending on the available access paths. Finally, the heuristic chooses one of the _n_ evaluation plans in a heuristic manner, on the basis of minimizing the number of nested-loop joins that do not have an index available on the inner relation and on the number of sort-merge joins.

Query-optimization approaches that apply heuristic plan choices for some parts of the query, with cost-based choice based on generation of alternative access plans on other parts of the query, have been adopted in several systems. The approach used in System R and in its successor, the Starburst project, is a hierarchical procedure based on the nested-block concept of SQL. The cost- based optimization techniques described here are used for each block of the query separately. The optimizers in several database products, such as IBM DB2 and Oracle, are based on the above approach, with extensions to handle other operations such as aggregation. For compound SQL queries (using the ∪, ∩, or − operation), the optimizer processes each component separately, and combines the evaluation plans to form the overall evaluation plan.

Most optimizers allow a cost budget to be specified for query optimization. The search for the optimal plan is terminated when the **optimization cost budget** is exceeded, and the best plan found up to that point is returned. The budget itself may be set dynamically; for example, if a cheap plan is found for a query, the budget may be reduced, on the premise that there is no point spending a lot of time optimizing the query if the best plan found so far is already quite cheap. On the other hand, if the best plan found so far is expensive, it makes sense to invest more time in optimization, which could result in a significant reduction in execution time. To best exploit this idea, optimizers usually first apply cheap heuristics to find a plan, and then start full cost-based optimization with a budget based on the heuristically chosen plan.  

**13.4 Choice of Evaluation Plans 605**

Many applications execute the same query repeatedly, but with different values for the constants. For example, a university application may repeatedly execute a query to find the courses for which a student has registered, but each time for a different student with a different value for the student ID. As a heuristic, many optimizers optimize a query once, with whatever values were provided for the constants when the query was first submitted, and cache the query plan. Whenever the query is executed again, perhaps with new values for constants, the cached query plan is reused (using new values for the constants, of course). The optimal plan for the new constants may differ from the optimal plan for the initial values, but as a heuristic the cached plan is reused.4 Caching and reuse of query plans is referred to as **plan caching**.

Even with the use of heuristics, cost-based query optimization imposes a substantial overhead on query processing. However, the added cost of cost-based query optimization is usually more than offset by the saving at query-execution time, which is dominated by slow disk accesses. The difference in execution time between a good plan and a bad one may be huge, making query optimization essential. The achieved saving is magnified in those applications that run on a regular basis, where a query can be optimized once, and the selected query plan can be used each time the query is executed. Therefore, most commercial systems include relatively sophisticated optimizers. The bibliographical notes give references to descriptions of the query optimizers of actual database systems.

**13.4.4 Optimizing Nested Subqueries\*\***

SQL conceptually treats nested subqueries in the **where** clause as functions that take parameters and return either a single value or a set of values (possibly an empty set). The parameters are the variables from an outer level query that are used in the nested subquery (these variables are called **correlation variables**). For instance, suppose we have the following query, to find the names of all instructors who taught a course in 2007:

**select** _name_ **from** _instructor_ **where exists** (**select** \*

**from** _teaches_ **where** _instructor_._ID_ \= _teaches_._ID_

**and** _teaches_._year_ \= 2007);

Conceptually, the subquery can be viewed as a function that takes a parameter (here, _instructor_._ID_) and returns the set of all courses taught in 2007 by instructors (with the same _ID_).

4For the student registration query, the plan would almost certainly be the same for any student ID. But a query that took a range of student IDs, and returned registration information for all student IDs in that range, would probably have a different optimal plan if the range is very small than if the range is large.  

**606 Chapter 13 Query Optimization**

SQL evaluates the overall query (conceptually) by computing the Cartesian product of the relations in the outer **from** clause and then testing the predicates in the **where** clause for each tuple in the product. In the preceding example, the predicate tests if the result of the subquery evaluation is empty.

This technique for evaluating a query with a nested subquery is called **corre- lated evaluation**. Correlated evaluation is not very efficient, since the subquery is separately evaluated for each tuple in the outer level query. A large number of random disk I/O operations may result.

SQL optimizers therefore attempt to transform nested subqueries into joins, where possible. Efficient join algorithms help avoid expensive random I/O. Where the transformation is not possible, the optimizer keeps the subqueries as separate expressions, optimizes them separately, and then evaluates them by correlated evaluation.

As an example of transforming a nested subquery into a join, the query in the preceding example can be rewritten as:

**select** _name_ **from** _instructor_, _teaches_ **where** _instructor_._ID_ \= _teaches_._ID_ **and** _teaches_._year_ \= 2007;

(To properly reflect SQL semantics, the number of duplicate derivations should not change because of the rewriting; the rewritten query can be modified to ensure this property, as we shall see shortly.)

In the example, the nested subquery was very simple. In general, it may not be possible to directly move the nested subquery relations into the **from** clause of the outer query. Instead, we create a temporary relation that contains the results of the nested query _without_ the selections using correlation variables from the outer query, and join the temporary table with the outer level query. For instance, a query of the form:

**select** _. . ._

**from** _L_1 **where** _P_1 **and exists** (**select** \*

**from** _L_2 **where** _P_2);

where _P_2 is a conjunction of simpler predicates, can be rewritten as:

**create table** _t_1 **as select distinct** _V_

**from** _L_2 **where** _P_1

2 ; **select** _. . ._

**from** _L_1, _t_1 **where** _P_1 **and** _P_2

2 ;

where _P_1 2 contains predicates in _P_2 without selections involving correlation vari-

ables, and _P_2 2 reintroduces the selections involving correlation variables (with  

**13.5 Materialized Views\*\* 607**

relations referenced in the predicate appropriately renamed). Here, _V_ contains all attributes that are used in selections with correlation variables in the nested subquery.

In our example, the original query would have been transformed to:

**create table** _t_1 **as select distinct** _ID_ **from** _teaches_ **where** _year_ \= 2007;

**select** _name_ **from** _instructor_, _t_1 **where** _t_1._ID_ \= _instructor_._ID_;

The query we rewrote to illustrate creation of a temporary relation can be obtained by simplifying the above transformed query, assuming the number of duplicates of each tuple does not matter.

The process of replacing a nested query by a query with a join (possibly with a temporary relation) is called **decorrelation**.

Decorrelation is more complicated when the nested subquery uses aggrega- tion, or when the result of the nested subquery is used to test for equality, or when the condition linking the nested subquery to the outer query is **not exists**, and so on. We do not attempt to give algorithms for the general case, and instead refer you to relevant items in the bibliographical notes.

Optimization of complex nested subqueries is a difficult task, as you can infer from the above discussion, and many optimizers do only a limited amount of decorrelation. It is best to avoid using complex nested subqueries, where possible, since we cannot be sure that the query optimizer will succeed in converting them to a form that can be evaluated efficiently.

**13.5 Materialized Views\*\***

When a view is defined, normally the database stores only the query defining the view. In contrast, a **materialized view** is a view whose contents are computed and stored. Materialized views constitute redundant data, in that their contents can be inferred from the view definition and the rest of the database contents. However, it is much cheaper in many cases to read the contents of a materialized view than to compute the contents of the view by executing the query defining the view.

Materialized views are important for improving performance in some appli- cations. Consider this view, which gives the total salary in each department:

**create view** _department total salary_(_dept name_, _total salary_) **as select** _dept name_, **sum** (_salary_) **from** _instructor_ **group by** _dept name_;  

**608 Chapter 13 Query Optimization**

Suppose the total salary amount at a department is required frequently. Comput- ing the view requires reading every _instructor_ tuple pertaining to a department, and summing up the salary amounts, which can be time-consuming. In contrast, if the view definition of the total salary amount were materialized, the total salary amount could be found by looking up a single tuple in the materialized view.5

**13.5.1 View Maintenance**

A problem with materialized views is that they must be kept up-to-date when the data used in the view definition changes. For instance, if the _salary_ value of an instructor is updated, the materialized view will become inconsistent with the underlying data, and it must be updated. The task of keeping a materialized view up-to-date with the underlying data is known as **view maintenance**.

Views can be maintained by manually written code: That is, every piece of code that updates the _salary_ value can be modified to also update the total salary amount for the corresponding department. However, this approach is error prone, since it is easy to miss some places where the _salary_ is updated, and the materialized view will then no longer match the underlying data.

Another option for maintaining materialized views is to define triggers on insert, delete, and update of each relation in the view definition. The triggers must modify the contents of the materialized view, to take into account the change that caused the trigger to fire. A simplistic way of doing so is to completely recompute the materialized view on every update.

A better option is to modify only the affected parts of the materialized view, which is known as **incremental view maintenance**. We describe how to perform incremental view maintenance in Section 13.5.2.

Modern database systems provide more direct support for incremental view maintenance. Database-system programmers no longer need to define triggers for view maintenance. Instead, once a view is declared to be materialized, the database system computes the contents of the view and incrementally updates the contents when the underlying data change.

Most database systems perform **immediate view maintenance**; that is, incre- mental view maintenance is performed as soon as an update occurs, as part of the updating transaction. Some database systems also support **deferred view main- tenance**, where view maintenance is deferred to a later time; for example, updates may be collected throughout a day, and materialized views may be updated at night. This approach reduces the overhead on update transactions. However, ma- terialized views with deferred view maintenance may not be consistent with the underlying relations on which they are defined.

5The difference may not be all that large for a medium-sized university, but in other settings the difference can be very large. For example, if the materialized view computed total sales of each product, from a sales relation with tens of millions of tuples, the difference between computing the aggregate from the underlying data, and looking up the materialized view can be many orders of magnitude.  

**13.5 Materialized Views\*\* 609**

**13.5.2 Incremental View Maintenance**

To understand how to maintain materialized views incrementally, we start off by considering individual operations, and then we see how to handle a complete expression.

The changes to a relation that can cause a materialized view to become out- of-date are inserts, deletes, and updates. To simplify our description, we replace updates to a tuple by deletion of the tuple followed by insertion of the updated tuple. Thus, we need to consider only inserts and deletes. The changes (inserts and deletes) to a relation or expression are referred to as its **differential**.

**13.5.2.1 Join Operation**

Consider the materialized view _v_ \= _r  s_. Suppose we modify _r_ by inserting a set of tuples denoted by _ir_ . If the old value of _r_ is denoted by _rold_ , and the new value of _r_ by _rnew_, _rnew_ \= _rold_ ∪ _ir_ . Now, the old value of the view, _vold_ , is given by _rold  s_, and the new value _vnew_ is given by _rnew  s_. We can rewrite _rnew  s_ as (_rold_ ∪ _ir_ ) _ s_, which we can again rewrite as (_rold  s_) ∪ (_ir  s_). In other words:

_vnew_ \= _vold_ ∪ (_ir  s_)

Thus, to update the materialized view _v_, we simply need to add the tuples _ir  s_ to the old contents of the materialized view. Inserts to _s_ are handled in an exactly symmetric fashion.

Now suppose _r_ is modified by deleting a set of tuples denoted by _dr_ . Using the same reasoning as above, we get:

_vnew_ \= _vold_ − (_dr  s_)

Deletes on _s_ are handled in an exactly symmetric fashion.

**13.5.2.2 Selection and Projection Operations**

Consider a view _v_ \= (_r_ ). If we modify _r_ by inserting a set of tuples _ir_ , the new value of _v_ can be computed as:

_vnew_ \= _vold_ ∪ (_ir_ )

Similarly, if _r_ is modified by deleting a set of tuples _dr_ , the new value of _v_ can be computed as:

_vnew_ \= _vold_ − (_dr_ )

Projection is a more difficult operation with which to deal. Consider a ma- terialized view _v_ \= _A_(_r_ ). Suppose the relation _r_ is on the schema _R_ \= (_A, B_), and _r_ contains two tuples (_a ,_ 2) and (_a ,_ 3). Then, _A_(_r_ ) has a single tuple (_a_ ). If  

**610 Chapter 13 Query Optimization**

we delete the tuple (_a ,_ 2) from _r_ , we cannot delete the tuple (_a_ ) from _A_(_r_ ): If we did so, the result would be an empty relation, whereas in reality _A_(_r_ ) still has a single tuple (_a_ ). The reason is that the same tuple (_a_ ) is derived in two ways, and deleting one tuple from _r_ removes only one of the ways of deriving (_a_ ); the other is still present.

This reason also gives us the intuition for solution: For each tuple in a projec- tion such as _A_(_r_ ), we will keep a count of how many times it was derived.

When a set of tuples _dr_ is deleted from _r_ , for each tuple _t_ in _dr_ we do the following: Let _t.A_ denote the projection of _t_ on the attribute _A_. We find (_t.A_) in the materialized view, and decrease the count stored with it by 1. If the count becomes 0, (_t.A_) is deleted from the materialized view.

Handling insertions is relatively straightforward. When a set of tuples _ir_ is inserted into _r_ , for each tuple _t_ in _ir_ we do the following: If (_t.A_) is already present in the materialized view, we increase the count stored with it by 1. If not, we add (_t.A_) to the materialized view, with the count set to 1.

**13.5.2.3 Aggregation Operations**

Aggregation operations proceed somewhat like projections. The aggregate oper- ations in SQL are **count, sum, avg, min,** and **max**:

• **count**: Consider a materialized view _v_ \= _AGcount_(_B_)(_r_ ), which computes the count of the attribute _B_, after grouping _r_ by attribute _A_.

When a set of tuples _ir_ is inserted into _r_ , for each tuple _t_ in _ir_ we do the following: We look for the group _t.A_ in the materialized view. If it is not present, we add (_t.A,_ 1) to the materialized view. If the group _t.A_ is present, we add 1 to the count of the group.

When a set of tuples _dr_ is deleted from _r_ , for each tuple _t_ in _dr_ we do the following: We look for the group _t.A_ in the materialized view, and subtract 1 from the count for the group. If the count becomes 0, we delete the tuple for the group _t.A_ from the materialized view.

• **sum**: Consider a materialized view _v_ \= _AGsum_(_B_)(_r_ ). When a set of tuples _ir_ is inserted into _r_ , for each tuple _t_ in _ir_ we do

the following: We look for the group _t.A_ in the materialized view. If it is not present, we add (_t.A, t.B_) to the materialized view; in addition, we store a count of 1 associated with (_t.A, t.B_), just as we did for projection. If the group _t.A_ is present, we add the value of _t.B_ to the aggregate value for the group, and add 1 to the count of the group.

When a set of tuples _dr_ is deleted from _r_ , for each tuple _t_ in _dr_ we do the following: We look for the group _t.A_ in the materialized view, and subtract _t.B_ from the aggregate value for the group. We also subtract 1 from the count for the group, and if the count becomes 0, we delete the tuple for the group _t.A_ from the materialized view.

Without keeping the extra count value, we would not be able to distinguish a case where the sum for a group is 0 from the case where the last tuple in a group is deleted.  

**13.5 Materialized Views\*\* 611**

• **avg**: Consider a materialized view _v_ \= _AGavg_(_B_)(_r_ ). Directly updating the average on an insert or delete is not possible, since

it depends not only on the old average and the tuple being inserted/deleted, but also on the number of tuples in the group.

Instead, to handle the case of **avg**, we maintain the **sum** and **count** aggregate values as described earlier, and compute the average as the sum divided by the count.

• **min, max**: Consider a materialized view _v_ \= _AGmin_(_B_)(_r_ ). (The case of **max** is exactly equivalent.)

Handling insertions on _r_ is straightforward. Maintaining the aggregate values **min** and **max** on deletions may be more expensive. For example, if the tuple corresponding to the minimum value for a group is deleted from _r_ , we have to look at the other tuples of _r_ that are in the same group to find the new minimum value.

**13.5.2.4 Other Operations**

The set operation _intersection_ is maintained as follows: Given materialized view _v_ \= _r_ ∩ _s_, when a tuple is inserted in _r_ we check if it is present in _s_, and if so we add it to _v_. If a tuple is deleted from _r_ , we delete it from the intersection if it is present. The other set operations, _union_ and _set difference_, are handled in a similar fashion; we leave details to you.

Outer joins are handled in much the same way as joins, but with some extra work. In the case of deletion from _r_ we have to handle tuples in _s_ that no longer match any tuple in _r_ . In the case of insertion to _r_ , we have to handle tuples in _s_ that did not match any tuple in _r_ . Again we leave details to you.

**13.5.2.5 Handling Expressions**

So far we have seen how to update incrementally the result of a single operation. To handle an entire expression, we can derive expressions for computing the incremental change to the result of each subexpression, starting from the smallest subexpressions.

For example, suppose we wish to incrementally update a materialized view _E_1 _ E_2 when a set of tuples _ir_ is inserted into relation _r_ . Let us assume _r_ is used in _E_1 alone. Suppose the set of tuples to be inserted into _E_1 is given by expression _D_1\. Then the expression _D_1 _ E_2 gives the set of tuples to be inserted into _E_1 _ E_2.

See the bibliographical notes for further details on incremental view mainte- nance with expressions.

**13.5.3 Query Optimization and Materialized Views**

Query optimization can be performed by treating materialized views just like regular relations. However, materialized views offer further opportunities for optimization:

• Rewriting queries to use materialized views:  

**612 Chapter 13 Query Optimization**

Suppose a materialized view _v_ \= _r  s_ is available, and a user submits a query _r  s  t_. Rewriting the query as _v  t_ may provide a more efficient query plan than optimizing the query as submitted. Thus, it is the job of the query optimizer to recognize when a materialized view can be used to speed up a query.

• Replacing a use of a materialized view with the view definition: Suppose a materialized view _v_ \= _r  s_ is available, but without any index

on it, and a user submits a query _A_\=10(_v_). Suppose also that _s_ has an index on the common attribute _B_, and _r_ has an index on attribute _A_. The best plan for this query may be to replace _v_ with _r  s_, which can lead to the query plan _A_\=10(_r_ ) _ s_; the selection and join can be performed efficiently by using the indices on _r.A_ and _s.B_, respectively. In contrast, evaluating the selection directly on _v_ may require a full scan of _v_, which may be more expensive.

The bibliographical notes give pointers to research showing how to efficiently perform query optimization with materialized views.

**13.5.4 Materialized View and Index Selection**

Another related optimization problem is that of **materialized view selection**, namely, “What is the best set of views to materialize?” This decision must be made on the basis of the system **workload**, which is a sequence of queries and updates that reflects the typical load on the system. One simple criterion would be to select a set of materialized views that minimizes the overall execution time of the workload of queries and updates, including the time taken to maintain the materialized views. Database administrators usually modify this criterion to take into account the importance of different queries and updates: Fast response may be required for some queries and updates, but a slow response may be acceptable for others.

Indices are just like materialized views, in that they too are derived data, can speed up queries, and may slow down updates. Thus, the problem of **index selection** is closely related to that of materialized view selection, although it is simpler. We examine index and materialized view selection in more detail in Sections 24.1.6 and 24.1.7.

Most database systems provide tools to help the database administrator with index and materialized view selection. These tools examine the history of queries and updates, and suggest indices and views to be materialized. The Microsoft SQL Server Database Tuning Assistant, the IBM DB2 Design Advisor, and the Oracle SQL Tuning Wizard are examples of such tools.

**13.6 Advanced Topics in Query Optimization\*\***

There are a number of opportunities for optimizing queries, beyond those we have seen so far. We examine a few of these in this section.  

**13.6 Advanced Topics in Query Optimization\*\* 613**

**13.6.1 Top-**_K_ **Optimization**

Many queries fetch results sorted on some attributes, and require only the top _K_ results for some _K_ . Sometimes the bound _K_ is specified explicitly. For example, some databases support a **limit** _K_ clause which results in only the top _K_ results being returned by the query. Other databases support alternative ways of speci- fying similar limits. In other cases, the query may not specify such a limit, but the optimizer may allow a hint to be specified, indicating that only the top _K_ results of the query are likely to be retrieved, even if the query generates more results.

When _K_ is small, a query optimization plan that generates the entire set of results, then sorts and generates the top _K_ , is very inefficient since it discards most of the intermediate results that it computes. Several techniques have been proposed to optimize such _top-K queries_. One approach is to use pipelined plans that can generate the results in sorted order. Another approach is to estimate what is the highest value on the sorted attributes that will appear in the top-_K_ output, and introduce selection predicates that eliminate larger values. If extra tuples beyond the top-_K_ are generated they are discarded, and if too few tuples are generated then the selection condition is changed and the query is re-executed. See the bibliographical notes for references to work on top-_K_ optimization.

**13.6.2 Join Minimization**

When queries are generated through views, sometimes more relations are joined than are needed for computation of the query. For example, a view _v_ may in- clude the join of _instructor_ and _department_, but a use of the view _v_ may use only attributes from _instructor_. The join attribute _dept name_ of _instructor_ is a foreign key referencing _department_. Assuming that _instructor_._dept name_ has been declared **not null**, the join with _department_ can be dropped, with no impact on the query. For, under the above assumption, the join with _department_ does not eliminate any tuples from _instructor_, nor does it result in extra copies of any _instructor_ tuple.

Dropping a relation from a join as above is an example of join minimization. In fact, join minimization can be performed in other situations as well. See the bibliographical notes for references on join minimization.

**13.6.3 Optimization of Updates**

Update queries often involve subqueries in the **set** and **where** clauses, which must also be taken into account in optimizing the update. Updates that involve a selection on the updated column (e.g., give a 10 percent salary raise to all employees whose salary is ≥ $100,000) must be handled carefully. If the update is done while the selection is being evaluated by an index scan, an updated tuple may be reinserted in the index ahead of the scan and seen again by the scan; the same employee tuple may then get incorrectly updated multiple times (an infinite number of times, in this case). A similar problem also arises with updates involving subqueries whose result is affected by the update.

The problem of an update affecting the execution of a query associated with the update is known as the **Halloween problem** (named so because it was first  

**614 Chapter 13 Query Optimization**

recognized on a Halloween day, at IBM). The problem can be avoided by executing the queries defining the update first, creating a list of affected tuples, and updating the tuples and indices as the last step. However, breaking up the execution plan in such a fashion increases the execution cost. Update plans can be optimized by checking if the Halloween problem can occur, and if it cannot occur, updates can be performed while the query is being processed, reducing the update overheads. For example, the Halloween problem cannot occur if the update does not affect index attributes. Even if it does, if the updates decrease the value, while the index is scanned in increasing order, updated tuples will not be encountered again during the scan. In such cases, the index can be updated even while the query is being executed, reducing the overall cost.

Update queries that result in a large number of updates can also be optimized by collecting the updates as a batch, and then applying the batch of updates separately to each affected index. When applying the batch of updates to an index, the batch is first sorted in the index order for that index; such sorting can greatly reduce the amount of random I/O required for updating indices.

Such optimizations of updates are implemented in most database systems. See the bibliographical notes for references to such optimization.

**13.6.4 Multiquery Optimization and Shared Scans**

When a batch of queries are submitted together, a query optimizer can poten- tially exploit common subexpressions between the different queries, evaluating them once and reusing them where required. Complex queries may in fact have subexpressions repeated in different parts of the query, which can be similarly exploited, to reduce query evaluation cost. Such optimization is known as **multi- query optimization**.

**Common subexpression elimination** optimizes subexpressions shared by different expressions in a program, by computing and storing the result, and reusing it wherever the subexpression occurs. Common subexpression elimina- tion is a standard optimization applied on arithmetic expressions by programming- language compilers. Exploiting common subexpressions among evaluation plans chosen for each of a batch of queries is just as useful in database query evalua- tion, and is implemented by some databases. However, multiquery optimization can do even better in some cases: A query typically has more than one evalua- tion plan, and a judiciously chosen set of query evaluation plans for the queries may provide for a greater sharing and lesser cost than that afforded by choos- ing the lowest cost evaluation plan for each query. More details on multiquery optimization may be found in references cited in the bibliographical notes.

Sharing of relation scans between queries is another limited form of mul- tiquery optimization that is implemented in some databases. The **shared-scan** optimization works as follows: Instead of reading the relation repeatedly from disk, once for each query that needs to scan a relation, data are read once from disk, and pipelined to each of the queries. The shared-scan optimization is par- ticularly useful when multiple queries perform a scan on a single large relation (typically a “fact table”).  

**13.7 Summary 615**

**13.6.5 Parametric Query Optimization**

Plan caching, which we saw earlier in Section 13.4.3, is used as a heuristic in many databases. Recall that with plan caching, if a query is invoked with some constants, the plan chosen by the optimizer is cached, and reused if the query is submitted again, even if the constants in the query are different. For example, suppose a query takes a department name as a parameter, and retrieves all courses of the department. With plan caching, a plan chosen when the query is executed for the first time, say for the Music department, is reused if the query is executed for any other department.

Such reuse of plans by plan caching is reasonable if the optimal query plan is not significantly affected by the exact value of the constants in the query. However, if the plan is affected by the value of the constants, parametric query optimization is an alternative.

In **parametric query optimization**, a query is optimized without being pro- vided specific values for its parameters, for example, _dept name_ in the preceding example. The optimizer then outputs several plans, each optimal for a different parameter value. A plan would be output by the optimizer only if it is optimal for some possible value of the parameters. The set of alternative plans output by the optimizer are stored. When a query is submitted with specific values for its parameters, instead of performing a full optimization, the cheapest plan from the set of alternative plans computed earlier is used. Finding the cheapest such plan usually takes much less time than reoptimization. See the bibliographical notes for references on parametric query optimization.

**13.7 Summary**

• Given a query, there are generally a variety of methods for computing the answer. It is the responsibility of the system to transform the query as entered by the user into an equivalent query that can be computed more efficiently. The process of finding a good strategy for processing a query is called _query optimization_.

• The evaluation of complex queries involves many accesses to disk. Since the transfer of data from disk is slow relative to the speed of main memory and the CPU of the computer system, it is worthwhile to allocate a considerable amount of processing to choose a method that minimizes disk accesses.

• There are a number of equivalence rules that we can use to transform an ex- pression into an equivalent one. We use these rules to generate systematically all expressions equivalent to the given query.

• Each relational-algebra expression represents a particular sequence of op- erations. The first step in selecting a query-processing strategy is to find a relational-algebra expression that is equivalent to the given expression and is estimated to cost less to execute.  

**616 Chapter 13 Query Optimization**

• The strategy that the database system chooses for evaluating an operation depends on the size of each relation and on the distribution of values within columns. So that they can base the strategy choice on reliable information, database systems may store statistics for each relation _r_. These statistics in- clude:

◦ The number of tuples in the relation _r_.

◦ The size of a record (tuple) of relation _r_ in bytes.

◦ The number of distinct values that appear in the relation _r_ for a particular attribute.

• Most database systems use histograms to store the number of values for an attribute within each of several ranges of values. Histograms are often computed using sampling.

• These statistics allow us to estimate the sizes of the results of various opera- tions, as well as the cost of executing the operations. Statistical information about relations is particularly useful when several indices are available to assist in the processing of a query. The presence of these structures has a significant influence on the choice of a query-processing strategy.

• Alternative evaluation plans for each expression can be generated by equiv- alence rules, and the cheapest plan across all expressions can be chosen. Several optimization techniques are available to reduce the number of alter- native expressions and plans that need to be generated.

• We use heuristics to reduce the number of plans considered, and thereby to reduce the cost of optimization. Heuristic rules for transforming relational- algebra queries include “Perform selection operations as early as possible,” “Perform projections early,” and “Avoid Cartesian products.”

• Materialized views can be used to speed up query processing. Incremental view maintenance is needed to efficiently update materialized views when the underlying relations are modified. The differential of an operation can be computed by means of algebraic expressions involving differentials of the inputs of the operation. Other issues related to materialized views include how to optimize queries by making use of available materialized views, and how to select views to be materialized.

• A number of advanced optimization techniques have been proposed such as top-_K_ optimization, join minimization, optimization of updates, multiquery optimization, and parametric query optimization.

**Review Terms**

• Query optimization • Transformation of expressions

• Equivalence of expressions  

**Practice Exercises 617**

• Equivalence rules

◦ Join commutativity

◦ Join associativity

• Minimal set of equivalence rules • Enumeration of equivalent

expressions • Statistics estimation • Catalog information • Size estimation

◦ Selection

◦ Selectivity

◦ Join

• Histograms • Distinct value estimation • Random sample • Choice of evaluation plans • Interaction of evaluation

techniques • Cost-based optimization • Join-order optimization

◦ Dynamic-programming algorithm

◦ Left-deep join order

◦ Interesting sort order

• Heuristic optimization • Plan caching • Access-plan selection • Correlated evaluation • Decorrelation • Materialized views • Materialized view maintenance

◦ Recomputation

◦ Incremental maintenance

◦ Insertion

◦ Deletion

◦ Updates

• Query optimization with materialized views

• Index selection • Materialized view selection • Top-_K_ optimization • Join minimization • Halloween problem • Multiquery optimization

**Practice Exercises**

**13.1** Show that the following equivalences hold. Explain how you can apply them to improve the efficiency of certain queries:

a. _E_1  (_E_2 − _E_3) = (_E_1  _E_2 − _E_1  _E_3).

b. ( _AGF_ (_E_)) = _AGF_ ((_E_)), where  uses only attributes from _A_.

c. (_E_1 _ E_2) = (_E_1) _ E_2, where  uses only attributes from _E_1.

**13.2** For each of the following pairs of expressions, give instances of relations that show the expressions are not equivalent.

a. _A_(_R_ − _S_) and _A_(_R_) − _A_(_S_).

b. _B<_4( _AGmax_ (_B_) **as** _B_(_R_)) and _AGmax_ (_B_) **as** _B_(_B<_4(_R_)).  

**618 Chapter 13 Query Optimization**

c. In the preceding expressions, if both occurrences of _max_ were re- placed by _min_ would the expressions be equivalent?

d. (_R  S_) _T_ and _R_ (_S  T_) In other words, the natural left outer join is not associative. (Hint: Assume that the schemas of the three relations are _R_(_a , b_1)_, S_(_a , b_2), and _T_(_a , b_3), respectively.)

e. (_E_1 _ E_2) and _E_1  (_E_2), where  uses only attributes from _E_2.

**13.3** SQL allows relations with duplicates (Chapter 3).

a. Define versions of the basic relational-algebra operations , , ×, , −, ∪, and ∩ that work on relations with duplicates, in a way consistent with SQL.

b. Check which of the equivalence rules 1 through 7.b hold for the multiset version of the relational-algebra defined in part a.

**13.4** Consider the relations _r_1(_A, B, C_), _r_2(_C, D, E_), and _r_3(_E, F_ ), with primary keys _A_, _C_, and _E_, respectively. Assume that _r_1 has 1000 tuples, _r_2 has 1500 tuples, and _r_3 has 750 tuples. Estimate the size of _r_1 _ r_2 _ r_3, and give an efficient strategy for computing the join.

**13.5** Consider the relations _r_1(_A, B, C_), _r_2(_C, D, E_), and _r_3(_E, F_ ) of Practice Exercise 13.4. Assume that there are no primary keys, except the entire schema. Let _V_(_C, r_1) be 900, _V_(_C, r_2) be 1100, _V_(_E, r_2) be 50, and _V_(_E, r_3) be 100. Assume that _r_1 has 1000 tuples, _r_2 has 1500 tuples, and _r_3 has 750 tuples. Estimate the size of _r_1 _ r_2 _ r_3 and give an efficient strategy for computing the join.

**13.6** Suppose that a B+-tree index on _building_ is available on relation _department_, and that no other index is available. What would be the best way to handle the following selections that involve negation?

a. ¬(_building <_“Watson”)(_department_)

b. ¬(_building_ \=“Watson”)(_department_)

c. ¬(_building <_“Watson” ∨ _budget <_50000)(_department_)

**13.7** Consider the query:

**select** \* **from** _r_ , _s_ **where** upper(_r.A_) = upper(_s.A_);

where “upper” is a function that returns its input argument with all low- ercase letters replaced by the corresponding uppercase letters.

a. Find out what plan is generated for this query on the database system you use.  

**Practice Exercises 619**

b. Some database systems would use a (block) nested-loop join for this query, which can be very inefficient. Briefly explain how hash-join or merge-join can be used for this query.

**13.8** Give conditions under which the following expressions are equivalent

_A,BGagg_(_C_)(_E_1 _ E_2) and (_AGagg_(_C_)(_E_1)) _ E_2

where _agg_ denotes any aggregation operation. How can the above condi- tions be relaxed if _agg_ is one of **min** or **max**?

**13.9** Consider the issue of interesting orders in optimization. Suppose you are given a query that computes the natural join of a set of relations _S_. Given a subset _S_1 of _S_, what are the interesting orders of _S_1?

**13.10** Show that, with _n_ relations, there are (2(_n_ − 1))!_/_(_n_ − 1)! different join orders. _Hint:_ A **complete binary tree** is one where every internal node has exactly two children. Use the fact that the number of different complete binary trees with _n_ leaf nodes is:

1 _n_

( 2(_n_ − 1) (_n_ − 1)

)

If you wish, you can derive the formula for the number of complete binary trees with _n_ nodes from the formula for the number of binary trees with _n_ nodes. The number of binary trees with _n_ nodes is:

1 _n_ \+ 1

( 2_n n_

)

This number is known as the **Catalan number**, and its derivation can be found in any standard textbook on data structures or algorithms.

**13.11** Show that the lowest-cost join order can be computed in time _O_(3_n_). Assume that you can store and look up information about a set of relations (such as the optimal join order for the set, and the cost of that join order) in constant time. (If you find this exercise difficult, at least show the looser time bound of _O_(22_n_).)

**13.12** Show that, if only left-deep join trees are considered, as in the System R optimizer, the time taken to find the most efficient join order is around _n_2_n_. Assume that there is only one interesting sort order.

**13.13** Consider the bank database of Figure 13.9, where the primary keys are un- derlined. Construct the following SQL queries for this relational database.

a. Write a nested query on the relation _account_ to find, for each branch with name starting with B, all accounts with the maximum balance at the branch.  

**620 Chapter 13 Query Optimization**

_branch_(_branch name_, _branch city, assets_) _customer_ (_customer name_, _customer street, customer city_) _loan_ (_loan number_, _branch name, amount_) _borrower_ (_customer name_, _loan number_) _account_ (_account number_, _branch name, balance_) _depositor_ (_customer name_, _account number_)

**Figure 13.9** Banking database for Exercise 13.13.

b. Rewrite the preceding query, without using a nested subquery; in other words, decorrelate the query.

c. Give a procedure (similar to that described in Section 13.4.4) for decorrelating such queries.

**13.14** The set version of the semijoin operator  is defined as follows:

_r_  _s_ \= _R_(_r _ _s_)

where _R_ is the set of attributes in the schema of _r_ . The multiset version of the semijoin operation returns the same set of tuples, but each tuple has exactly as many copies as it had in _r_ . Consider the nested query we saw in Section 13.4.4 which finds the names of all instructors who taught a course in 2007. Write the query in relational algebra using the multiset semjoin operation, ensuring that the number of duplicates of each name is the same as in the SQL query. (The semijoin operation is widely used for decorrelation of nested queries.)

**Exercises**

**13.15** Suppose that a B+-tree index on (_dept name_, _building_) is available on re- lation _department_. What would be the best way to handle the following selection?

(_building <_ “Watson”) ∧ (_budget <_ 55000) ∧ (_dept name_ \= “Music”)(_department_)

**13.16** Show how to derive the following equivalences by a sequence of trans- formations using the equivalence rules in Section 13.2.1.

a. 1∧2∧3 (_E_) = 1 (2 (3 (_E_)))

b. 1∧2 (_E_1 3 _E_2) = 1 (_E_1 3 (2 (_E_2))), where 2 involves only attributes from _E_2  

**Exercises 621**

**13.17** Consider the two expressions (_E_1 _ E_2) and (_E_1 _ E_2).

a. Show using an example that the two expressions are not equivalent in general.

b. Give a simple condition on the predicate , which if satisfied will ensure that the two expressions are equivalent.

**13.18** A set of equivalence rules is said to be _complete_ if, whenever two expres- sions are equivalent, one can be derived from the other by a sequence of uses of the equivalence rules. Is the set of equivalence rules that we considered in Section 13.2.1 complete? Hint: Consider the equivalence 3=5(_r_ ) = { }.

**13.19** Explain how to use a histogram to estimate the size of a selection of the form _A_≤_v_(_r_ ).

**13.20** Suppose two relations _r_ and _s_ have histograms on attributes _r.A_ and _s.A_, respectively, but with different ranges. Suggest how to use the histograms to estimate the size of _r  s_. Hint: Split the ranges of each histogram further.

**13.21** Consider the query

**select** _A_, _B_ **from** _r_ **where** _r.B <_ **some** (**select** _B_

**from** _s_ **where** _s.A_ \= _r.A_)

Show how to decorrelate the above query using the multiset version of the semijoin operation, defined in Exercise 13.14.

**13.22** Describe how to incrementally maintain the results of the following oper- ations, on both insertions and deletions:

a. Union and set difference.

b. Left outer join.

**13.23** Give an example of an expression defining a materialized view and two situations (sets of statistics for the input relations and the differentials) such that incremental view maintenance is better than recomputation in one situation, and recomputation is better in the other situation.

**13.24** Suppose you want to get answers to _r  s_ sorted on an attribute of _r_ , and want only the top _K_ answers for some relatively small _K_ . Give a good way of evaluating the query:

a. When the join is on a foreign key of _r_ referencing _s_, where the foreign key attribute is declared to be not null.

b. When the join is not on a foreign key.  

**622 Chapter 13 Query Optimization**

**13.25** Consider a relation _r_ (_A, B, C_), with an index on attribute _A_. Give an example of a query that can be answered by using the index only, without looking at the tuples in the relation. (Query plans that use only the index, without accessing the actual relation, are called _index-only_ plans.)

**13.26** Suppose you have an update query _U_. Give a simple sufficient condition on _U_ that will ensure that the Halloween problem cannot occur, regardless of the execution plan chosen, or the indices that exist.

**Bibliographical Notes**

The seminal work of Selinger et al. \[1979\] describes access-path selection in the System R optimizer, which was one of the earliest relational-query optimizers. Query processing in Starburst, described in Haas et al. \[1989\], forms the basis for query optimization in IBM DB2.

Graefe and McKenna \[1993a\] describe Volcano, an equivalence-rule–based query optimizer that, along with its successor Cascades (Graefe \[1995\]), forms the basis of query optimization in Microsoft SQL Server.

Estimation of statistics of query results, such as result size, is addressed by Ioannidis and Poosala \[1995\], Poosala et al. \[1996\], and Ganguly et al. \[1996\], among others. Nonuniform distributions of values cause problems for estima- tion of query size and cost. Cost-estimation techniques that use histograms of value distributions have been proposed to tackle the problem. Ioannidis and Christodoulakis \[1993\], Ioannidis and Poosala \[1995\], and Poosala et al. \[1996\] present results in this area. The use of random sampling for constructing his- tograms is well known in statistics, but issues in histogram construction in the context of databases is discussed in Chaudhuri et al. \[1998\].

Klug \[1982\] was an early work on optimization of relational-algebra ex- pressions with aggregate functions. Optimization of queries with aggregation is addressed by Yan and Larson \[1995\] and Chaudhuri and Shim \[1994\]. Opti- mization of queries containing outer joins is described in Rosenthal and Reiner \[1984\], Galindo-Legaria and Rosenthal \[1992\], and Galindo-Legaria \[1994\]. Opti- mization of top-_K_ queries is addressed in Carey and Kossmann \[1998\] and Bruno et al. \[2002\].

Optimization of nested subqueries is discussed in Kim \[1982\], Ganski and Wong \[1987\], Dayal \[1987\], Seshadri et al. \[1996\] and Galindo-Legaria and Joshi \[2001\].

Blakeley et al. \[1986\] describe techniques for maintenance of materialized views. Optimization of materialized view maintenance plans is described by Vista \[1998\] and Mistry et al. \[2001\]. Query optimization in the presence of materialized views is addressed by Chaudhuri et al. \[1995\]. Index selection and materialized view selection are addressed by Ross et al. \[1996\], and Chaudhuri and Narasayya \[1997\].

Optimization of top-_K_ queries is addressed in Carey and Kossmann \[1998\] and Bruno et al. \[2002\]. A collection of techniques for join minimization has  

**Bibliographical Notes 623**

been grouped under the name _tableau optimization_. The notion of a tableau was introduced by Aho et al. \[1979b\] and Aho et al. \[1979a\], and was further extended by Sagiv and Yannakakis \[1981\].

Parametric query-optimization algorithms have been proposed by Ioannidis et al. \[1992\], Ganguly \[1998\] and Hulgeri and Sudarshan \[2003\]. Sellis \[1988\] was an early work on multiquery optimization, while Roy et al. \[2000\] showed how to integrate multi-query optimization into a Volcano-based query optimizer.

Galindo-Legaria et al. \[2004\] describes query processing and optimization for database updates, including optimization of index maintenance, materialized view maintenance plans and integrity constraint checking, along with techniques to handle the Halloween problem.  

_This page intentionally left blank_