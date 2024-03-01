---
title: 'Multiple Granularity' 
weight: 3
references:
    videos:
        - youtube:qK3qohb2XU4
        - youtube:YmfjxA52Zy8
    links:
        - https://www.geeksforgeeks.org/multiple-granularity-locking-in-dbms/
        - https://www.javatpoint.com/dbms-multiple-granularity
    books:
        - b1:
            title: Granular Computing and Intelligent Systems 
            url: https://www.google.co.in/books/edition/Granular_Computing_and_Intelligent_Syste/p-SERlncWCMC?hl=en&gbpv=0
        - b2:
            title: Novel Developments in Granular Computing 
            url: https://www.google.co.in/books/edition/Novel_Developments_in_Granular_Computing/gw_iLzj36qgC?hl=en&gbpv=0
---

# Multiple Granularity

In the concurrency-control schemes described thus far, we have used each indi- vidual data item as the unit on which synchronization is performed.

There are circumstances, however, where it would be advantageous to group several data items, and to treat them as one individual synchronization unit. For example, if a transaction _Ti_ needs to access the entire database, and a locking protocol is used, then _Ti_ must lock each item in the database. Clearly, executing these locks is time-consuming. It would be better if _Ti_ could issue a _single_ lock request to lock the entire database. On the other hand, if transaction _Tj_ needs to access only a few data items, it should not be required to lock the entire database, since otherwise concurrency is lost.

What is needed is a mechanism to allow the system to define multiple levels of **granularity**. This is done by allowing data items to be of various sizes and defining a hierarchy of data granularities, where the small granularities are nested within larger ones. Such a hierarchy can be represented graphically as a tree. Note that the tree that we describe here is significantly different from that used by the tree protocol (Section 15.1.5). A nonleaf node of the multiple-granularity tree represents the data associated with its descendants. In the tree protocol, each node is an independent data item.

As an illustration, consider the tree of Figure 15.15, which consists of four levels of nodes. The highest level represents the entire database. Below it are nodes of type _area_; the database consists of exactly these areas. Each area in turn has nodes of type _file_ as its children. Each area contains exactly those files that are its child nodes. No file is in more than one area. Finally, each file has nodes of type _record_. As before, the file consists of exactly those records that are its child nodes, and no record can be present in more than one file.

Each node in the tree can be locked individually. As we did in the two- phase locking protocol, we shall use **shared** and **exclusive** lock modes. When a transaction locks a node, in either shared or exclusive mode, the transaction also has implicitly locked all the descendants of that node in the same lock mode. For example, if transaction _Ti_ gets an **explicit lock** on file _Fc_ of Figure 15.15, in exclusive mode, then it has an **implicit lock** in exclusive mode on all the records belonging to that file. It does not need to lock the individual records of _Fc_ explicitly.  


![](1.15.png)

**Figure 15.15** Granularity hierarchy.

Suppose that transaction _Tj_ wishes to lock record _rb_6 of file _Fb_ . Since _Ti_ has locked _Fb_ explicitly, it follows that _rb_6 is also locked (implicitly). But, when _Tj_ issues a lock request for _rb_6 , _rb_6 is not explicitly locked! How does the system determine whether _Tj_ can lock _rb_6 ? _Tj_ must traverse the tree from the root to record _rb_6 . If any node in that path is locked in an incompatible mode, then _Tj_ must be delayed.

Suppose now that transaction _Tk_ wishes to lock the entire database. To do so, it simply must lock the root of the hierarchy. Note, however, that _Tk_ should not succeed in locking the root node, since _Ti_ is currently holding a lock on part of the tree (specifically, on file _Fb_). But how does the system determine if the root node can be locked? One possibility is for it to search the entire tree. This solution, however, defeats the whole purpose of the multiple-granularity locking scheme. A more efficient way to gain this knowledge is to introduce a new class of lock modes, called **intention lock modes**. If a node is locked in an intention mode, explicit locking is done at a lower level of the tree (that is, at a finer granularity). Intention locks are put on all the ancestors of a node before that node is locked explicitly. Thus, a transaction does not need to search the entire tree to determine whether it can lock a node successfully. A transaction wishing to lock a node—say, _Q_—must traverse a path in the tree from the root to _Q_. While traversing the tree, the transaction locks the various nodes in an intention mode.

There is an intention mode associated with shared mode, and there is one with exclusive mode. If a node is locked in **intention-shared (IS) mode**, explicit locking is being done at a lower level of the tree, but with only shared-mode locks. Similarly, if a node is locked in **intention-exclusive (IX) mode**, then explicit locking is being done at a lower level, with exclusive-mode or shared-mode locks. Finally, if a node is locked in **shared and intention-exclusive (SIX) mode**, the subtree rooted by that node is locked explicitly in shared mode, and that explicit locking is being done at a lower level with exclusive-mode locks. The compatibility function for these lock modes is in Figure 15.16.  

![](1.16.png)

**Figure 15.16** Compatibility matrix.

The **multiple-granularity locking protocol** uses these lock modes to ensure serializability. It requires that a transaction _Ti_ that attempts to lock a node _Q_ must follow these rules:

**1\.** Transaction _Ti_ must observe the lock-compatibility function of Figure 15.16.

**2\.** Transaction _Ti_ must lock the root of the tree first, and can lock it in any mode.

**3\.** Transaction _Ti_ can lock a node _Q_ in S or IS mode only if _Ti_ currently has the parent of _Q_ locked in either IX or IS mode.

**4\.** Transaction _Ti_ can lock a node _Q_ in X, SIX, or IX mode only if _Ti_ currently has the parent of _Q_ locked in either IX or SIX mode.

**5\.** Transaction _Ti_ can lock a node only if _Ti_ has not previously unlocked any node (that is, _Ti_ is two phase).

**6\.** Transaction _Ti_ can unlock a node _Q_ only if _Ti_ currently has none of the children of _Q_ locked.

Observe that the multiple-granularity protocol requires that locks be acquired in _top-down_ (root-to-leaf) order, whereas locks must be released in _bottom-up_ (leaf- to-root) order.

As an illustration of the protocol, consider the tree of Figure 15.15 and these transactions:

• Suppose that transaction T21 reads record _ra_2 in file _Fa_ . Then, T21 needs to lock the database, area _A_1, and _Fa_ in IS mode (and in that order), and finally to lock _ra_2 in S mode.

• Suppose that transaction T22 modifies record _ra_9 in file _Fa_ . Then, T22 needs to lock the database, area _A_1, and file _Fa_ (and in that order) in IX mode, and finally to lock _ra_9 in X mode.

• Suppose that transaction T23 reads all the records in file _Fa_ . Then, T23 needs to lock the database and area _A_1 (and in that order) in IS mode, and finally to lock _Fa_ in S mode.  


• Suppose that transaction T24 reads the entire database. It can do so after locking the database in S mode.

We note that transactions T21, T23, and T24 can access the database concurrently. Transaction T22 can execute concurrently with T21, but not with either T23 or T24.

This protocol enhances concurrency and reduces lock overhead. It is particu- larly useful in applications that include a mix of:

• Short transactions that access only a few data items.

• Long transactions that produce reports from an entire file or set of files.

There is a similar locking protocol that is applicable to database systems in which data granularities are organized in the form of a directed acyclic graph. See the bibliographical notes for additional references. Deadlock is possible in the multiple-granularity protocol, as it is in the two-phase locking protocol. There are techniques to reduce deadlock frequency in the multiple-granularity protocol, and also to eliminate deadlock entirely. These techniques are referenced in the bibliographical notes.