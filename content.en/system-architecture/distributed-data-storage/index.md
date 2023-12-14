---
title: Distributed Data Storage
weight: 19
---

Distributed Data Storage## Distributed Data Storage

Consider a relation _r_ that is to be stored in the database. There are two approaches to storing this relation in the distributed database:

- **Replication**. The system maintains several identical replicas (copies) of the relation, and stores each replica at a different site. The alternative to replica- tion is to store only one copy of relation _r_.

- **Fragmentation**. The system partitions the relation into several fragments, and stores each fragment at a different site.

Fragmentation and replication can be combined: A relation can be partitioned into several fragments and there may be several replicas of each fragment. In the following subsections, we elaborate on each of these techniques.

### Data Replication

If relation _r_ is replicated, a copy of relation _r_ is stored in two or more sites. In the most extreme case, we have **full replication**, in which a copy is stored in every site in the system.

There are a number of advantages and disadvantages to replication.

- **Availability**. If one of the sites containing relation _r_ fails, then the relation _r_ can be found in another site. Thus, the system can continue to process queries involving _r_, despite the failure of one site.  

- **Increased parallelism**. In the case where the majority of accesses to the rela- tion _r_ result in only the reading of the relation, then several sites can process queries involving _r_ in parallel. The more replicas of _r_ there are, the greater the chance that the needed data will be found in the site where the transaction is executing. Hence, data replication minimizes movement of data between sites.

- **Increased overhead on update**. The system must ensure that all replicas of a relation _r_ are consistent; otherwise, erroneous computations may result. Thus, whenever _r_ is updated, the update must be propagated to all sites containing replicas. The result is increased overhead. For example, in a banking system, where account information is replicated in various sites, it is necessary to ensure that the balance in a particular account agrees in all sites.

In general, replication enhances the performance of read operations and increases the availability of data to read-only transactions. However, update transactions incur greater overhead. Controlling concurrent updates by several transactions to replicated data is more complex than in centralized systems, which we studied in Chapter 15. We can simplify the management of replicas of relation _r_ by choosing one of them as the **primary copy** of _r_. For example, in a banking system, an account can be associated with the site in which the account has been opened. Similarly, in an airline-reservation system, a flight can be associated with the site at which the flight originates. We shall examine the primary copy scheme and other options for distributed concurrency control in Section 19.5.

### Data Fragmentation

If relation _r_ is fragmented, _r_ is divided into a number of _fragments r_~1~, _r_~2~, . . . , _r_~n~. These fragments contain sufficient information to allow reconstruction of the original relation _r_. There are two different schemes for fragmenting a relation: _horizontal_ fragmentation and _vertical_ fragmentation. Horizontal fragmentation splits the relation by assigning each tuple of _r_ to one or more fragments. Vertical fragmentation splits the relation by decomposing the scheme _R_ of relation _r_.

In **horizontal fragmentation**, a relation _r_ is partitioned into a number of subsets, _r_~1~, _r_~2~, . . . , _r_~n~. Each tuple of relation _r_ must belong to at least one of the fragments, so that the original relation can be reconstructed, if needed.

As an illustration, the _account_ relation can be divided into several different fragments, each of which consists of tuples of accounts belonging to a particular branch. If the banking system has only two branches—Hillside and Valleyview —then there are two different fragments:

![Alt text](account.png)

Horizontal fragmentation is usually used to keep tuples at the sites where they are used the most, to minimize data transfer.  

In general, a horizontal fragment can be defined as a _selection_ on the global relation _r_. That is, we use a predicate _Pi_ to construct fragment _r_~i~:

![Alt text](ri.png)

We reconstruct the relation _r_ by taking the union of all fragments; that is:

_r_ \= _r_~1~ ∪ _r_~~ ∪ · · · ∪ _R_~n~

In our example, the fragments are disjoint. By changing the selection predi- cates used to construct the fragments, we can have a particular tuple of _r_ appear in more than one of the _r~i~_ .

In its simplest form, vertical fragmentation is the same as decomposition (see Chapter 8). **Vertical fragmentation** of _r_(_R_) involves the definition of several subsets of attributes _R_~1~, _R_~2~, . . . , _R_~n~ of the schema _R_ so that:

_R_ \= _R_~1~ ∪ _R_~2~ ∪ · · · ∪ _R_~n~

Each fragment _r_~i~ of _r_ is defined by:

![Alt text](f2.png)

The fragmentation should be done in such a way that we can reconstruct relation _r_ from the fragments by taking the natural join:

![Alt text](f3.png)

One way of ensuring that the relation _r_ can be reconstructed is to include the primary-key attributes of _R_ in each _Ri_ . More generally, any superkey can be used. It is often convenient to add a special attribute, called a _tuple-id_, to the schema _R_. The tuple-id value of a tuple is a unique value that distinguishes the tuple from all other tuples. The tuple-id attribute thus serves as a candidate key for the augmented schema, and is included in each _Ri_ . The physical or logical address for a tuple can be used as a tuple-id, since each tuple has a unique address.

To illustrate vertical fragmentation, consider a university database with a re- lation _employee info_ that stores, for each employee, _employee id_, _name_, _designation_, and _salary_. For privacy reasons, this relation may be fragmented into a relation _em- ployee private info_ containing _employee id_ and _salary_, and another relation _employee public info_ containing attributes _employee id_, _name_, and _designation_. These may be

stored at different sites, again, possibly for security reasons. The two types of fragmentation can be applied to a single schema; for instance,

the fragments obtained by horizontally fragmenting a relation can be further partitioned vertically. Fragments can also be replicated. In general, a fragment can be replicated, replicas of fragments can be fragmented further, and so on.  


### Transparency

The user of a distributed database system should not be required to know where the data are physically located nor how the data can be accessed at the specific local site. This characteristic, called **data transparency**, can take several forms:

- **Fragmentation transparency**. Users are not required to know how a relation has been fragmented.

- **Replication transparency**. Users view each data object as logically unique. The distributed system may replicate an object to increase either system performance or data availability. Users do not have to be concerned with what data objects have been replicated, or where replicas have been placed.

- **Location transparency**. Users are not required to know the physical location of the data. The distributed database system should be able to find any data as long as the data identifier is supplied by the user transaction.

Data items—such as relations, fragments, and replicas—must have unique names. This property is easy to ensure in a centralized database. In a distributed database, however, we must take care to ensure that two sites do not use the same name for distinct data items.

One solution to this problem is to require all names to be registered in a central **name server**. The name server helps to ensure that the same name does not get used for different data items. We can also use the name server to locate a data item, given the name of the item. This approach, however, suffers from two major disadvantages. First, the name server may become a performance bottle neck when data items are located by their names, resulting in poor performance. Second, if the name server crashes, it may not be possible for any site in the distributed system to continue to run.

A more widely used alternative approach requires that each site prefix its own site identifier to any name that it generates. This approach ensures that no two sites generate the same name (since each site has a unique identifier). Furthermore, no central control is required. This solution, however, fails to achieve location transparency, since site identifiers are attached to names. Thus, the _account_ relation might be referred to as _site_17\. _account_, or _account@site_17, rather than as simply _account_. Many database systems use the Internet address (IP address) of a site to identify it.

To overcome this problem, the database system can create a set of alternative names, or **aliases**, for data items. A user may thus refer to data items by simple names that are translated by the system to complete names. The mapping of aliases to the real names can be stored at each site. With aliases, the user can be unaware of the physical location of a data item. Furthermore, the user will be unaffected if the database administrator decides to move a data item from one site to another.

Users should not have to refer to a specific replica of a data item. Instead, the system should determine which replica to reference on a read request, and should update all replicas on a write request. We can ensure that it does so by maintaining a catalog table, which the system uses to determine all replicas for the data item.

