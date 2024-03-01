---
title: 'Failure Classification'
weight: 1
references:
    videos:
        - youtube:U__Io4IgWIo
        - youtube:zEcLqV-Zd28
    links:
        - https://www.geeksforgeeks.org/failure-classification-in-dbms/
        - https://www.javatpoint.com/dbms-failure-classification
    books:
        - b1:
            title: Database Management Systems
            url: https://www.google.co.in/books/edition/UGC_NET_unit_4_COMPUTER_SCIENCE_Database/9iWGEAAAQBAJ?hl=en&gbpv=0
        - b2:
            title: Fault-Tolerant Systems 
            url:  https://www.google.co.in/books/edition/Fault_Tolerant_Systems/o_Pjbo4Wvp8C?hl=en&gbpv=0
---

#  Failure Classification

There are various types of failure that may occur in a system, each of which needs to be dealt with in a different manner. In this chapter, we shall consider only the following types of failure:

• **Transaction failure**. There are two types of errors that may cause a transaction to fail:

◦ **Logical error**. The transaction can no longer continue with its normal execution because of some internal condition, such as bad input, data not found, overflow, or resource limit exceeded.

◦ **System error**. The system has entered an undesirable state (for example, deadlock), as a result of which a transaction cannot continue with its normal execution. The transaction, however, can be reexecuted at a later time.

• **System crash**. There is a hardware malfunction, or a bug in the database software or the operating system, that causes the loss of the content of volatile storage, and brings transaction processing to a halt. The content of nonvolatile storage remains intact, and is not corrupted.

The assumption that hardware errors and bugs in the software bring the system to a halt, but do not corrupt the nonvolatile storage contents, is known as the **fail-stop assumption**. Well-designed systems have numerous internal checks, at the hardware and the software level, that bring the system to a halt when there is an error. Hence, the fail-stop assumption is a reasonable one.

• **Disk failure**. A disk block loses its content as a result of either a head crash or failure during a data-transfer operation. Copies of the data on other disks, or archival backups on tertiary media, such as DVD or tapes, are used to recover from the failure.

To determine how the system should recover from failures, we need to iden- tify the failure modes of those devices used for storing data. Next, we must consider how these failure modes affect the contents of the database. We can then propose algorithms to ensure database consistency and transaction atomic- ity despite failures. These algorithms, known as recovery algorithms, have two parts:

**1\.** Actions taken during normal transaction processing to ensure that enough information exists to allow recovery from failures.

**2\.** Actions taken after a failure to recover the database contents to a state that ensures database consistency, transaction atomicity, and durability.