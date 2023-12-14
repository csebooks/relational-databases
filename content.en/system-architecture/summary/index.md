---
title: Summary
weight: 6
---

Summary## Summary

- Centralized database systems run entirely on a single computer. With the growth of personal computers and local-area networking, the database front-end functionality has moved increasingly to clients, with server systems providing the back-end functionality. Client–server interface protocols have helped the growth of client–server database systems.

- Servers can be either transaction servers or data servers, although the use of transaction servers greatly exceeds the use of data servers for providing database services.

>- Transaction servers have multiple processes, possibly running on multiple processors. So that these processes have access to common data, such as the database buffer, systems store such data in shared memory. In addition to processes that handle queries, there are system processes that carry out tasks such as lock and log management and checkpointing.

>- Data-server systems supply raw data to clients. Such systems strive to minimize communication between clients and servers by caching data and locks at the clients. Parallel database systems use similar optimizations.

- Parallel database systems consist of multiple processors and multiple disks connected by a fast interconnection network. Speedup measures how much we can increase processing speed by increasing parallelism for a single trans- action. Scaleup measures how well we can handle an increased number of transactions by increasing parallelism. Interference, skew, and start-up costs act as barriers to getting ideal speedup and scaleup.

- Parallel database architectures include the shared-memory, shared-disk, share- d-nothing, and hierarchical architectures. These architectures have different trade-offs of scalability versus communication speed.

- A distributed database system is a collection of partially independent database systems that (ideally) share a common schema, and coordinate processing of transactions that access nonlocal data. The systems communicate with one another through a communication network.

- Local-area networks connect nodes that are distributed over small geograph- ical areas, such as a single building or a few adjacent buildings. Wide-area networks connect nodes spread over a large geographical area. The Internet is the most extensively used wide-area network today.

- Storage-area networks are a special type of local-area network designed to provide fast interconnection between large banks of storage devices and multiple computers.  

**Review Terms**

- Centralized systems 
- Server systems 
- Coarse-granularity parallelism 
- Fine-granularity parallelism 
- Database process structure 
- Mutual exclusion 
- Thread 
- Server processes

>- Lock manager process

>- Database writer process

>- Log writer process

>- Checkpoint process

>- Process monitor process

- Client–server systems 
- Transaction server 
- Query server 
- Data server

>- Prefetching

>- De-escalation

>- Data caching

>- Cache coherency

>- Lock caching

>- Call back

- Parallel systems 
- Throughput 
- Response time 
- Speedup

>- Linear speedup

>- Sublinear speedup

- Scaleup

>- Linear scaleup

>- Sublinear scaleup

>- Batch scaleup

>- Transaction scaleup

- Start-up costs 
- Interference 
- Skew 
- Interconnection networks

>- Bus

>- Mesh

>- Hypercube

>- Parallel database architectures

>-Shared memory

>- Shared disk (clusters)

>- Shared nothing

>- Hierarchical

- Fault tolerance 
- Distributed virtual memory 
- Nonuniform memory architecture
(NUMA) 
- Distributed systems 
- Distributed database

>- Sites (nodes)

>- Local transaction

>- Global transaction

>- Local autonomy

- Multidatabase systems 
- Network types

>- Local-area networks (LAN)

>- Wide-area networks (WAN)

>- Storage-area network (SAN)  


**Practice Exercises**

**17.1** Instead of storing shared structures in shared memory, an alternative architecture would be to store them in the local memory of a special process, and access the shared data by interprocess communication with the process. What would be the drawback of such an architecture?

**17.2** In typical client–server systems the server machine is much more pow- erful than the clients; that is, its processor is faster, it may have multiple processors, and it has more memory and disk capacity. Consider instead a scenario where client and server machines have exactly the same power. Would it make sense to build a client–server system in such a scenario? Why? Which scenario would be better suited to a data-server architecture?

**17.3** Consider a database system based on a client–server architecture, with the server acting as a data server.

>1. What is the effect of the speed of the interconnection between the client and the server on the choice between tuple and page shipping?

>2. If page shipping is used, the cache of data at the client can be orga- nized either as a tuple cache or a page cache. The page cache stores data in units of a page, while the tuple cache stores data in units of tuples. Assume tuples are smaller than pages. Describe one benefit of a tuple cache over a page cache.

**17.4** Suppose a transaction is written in C with embedded SQL, and about 80 percent of the time is spent in the SQL code, with the remaining 20 percent spent in C code. How much speedup can one hope to attain if parallelism is used only for the SQL code? Explain.

**17.5** Some database operations such as joins can see a significant difference in speed when data (for example, one of the relations involved in a join) fits in memory as compared to the situation where the data does not fit in memory. Show how this fact can explain the phenomenon of **superlinear speedup**, where an application sees a speedup greater than the amount of resources allocated to it.

**17.6** Parallel systems often have a network structure where sets of _n_ processors connect to a single Ethernet switch, and the Ethernet switches themselves connect to another Ethernet switch. Does this architecture correspond to a bus, mesh or hypercube architecture? If not, how would you describe this interconnection architecture?

**Exercises**

**17.7** Why is it relatively easy to port a database from a single processor machine to a multiprocessor machine if individual queries need not be parallelized?  

**17.8** Transaction-server architectures are popular for client–server relational databases, where transactions are short. On the other hand, data-server architectures are popular for client–server object-oriented database sys- tems, where transactions are expected to be relatively long. Give two reasons why data servers may be popular for object-oriented databases but not for relational databases.

**17.9** What is lock de-escalation, and under what conditions is it required? Why is it not required if the unit of data shipping is an item?

**17.10** Suppose you were in charge of the database operations of a company whose main job is to process transactions. Suppose the company is grow- ing rapidly each year, and has outgrown its current computer system. When you are choosing a new parallel computer, what measure is most relevant—speedup, batch scaleup, or transaction scaleup? Why?

**17.11** Database systems are typically implemented as a set of processes (or threads) sharing a shared memory area.

a. How is access to the shared memory area controlled?

b. Is two-phase locking appropriate for serializing access to the data structures in shared memory? Explain your answer.

**17.12** Is it wise to allow a user process to access the shared memory area of a database system? Explain your answer.

**17.13** What are the factors that can work against linear scaleup in a transaction processing system? Which of the factors are likely to be the most important in each of the following architectures: shared memory, shared disk, and shared nothing?

**17.14** Memory systems can be divided into multiple modules, each of which can be serving a separate request at a given time. What impact would such a memory architecture have on the number of processors that can be supported in a shared-memory system?

**17.15** Consider a bank that has a collection of sites, each running a database sys- tem. Suppose the only way the databases interact is by electronic transfer of money between themselves, using persistent messaging. Would such a system qualify as a distributed database? Why?

## Summary

- Parallel databases have gained significant commercial acceptance in the past 20 years.

- In I/O parallelism, relations are partitioned among available disks so that they can be retrieved faster. Three commonly used partitioning techniques are round-robin partitioning, hash partitioning, and range partitioning.

- Skew is a major problem, especially with increasing degrees of parallelism. Balanced partitioning vectors, using histograms, and virtual processor parti- tioning are among the techniques used to reduce skew.

- In interquery parallelism, we run different queries concurrently to increase throughput.

- Intraquery parallelism attempts to reduce the cost of running a query. There are two types of intraquery parallelism: intraoperation parallelism and inter- operation parallelism.

- We use intraoperation parallelism to execute relational operations, such as sorts and joins, in parallel. Intraoperation parallelism is natural for relational operations, since they are set oriented.

- There are two basic approaches to parallelizing a binary operation such as a join.

>- In partitioned parallelism, the relations are split into several parts, and tuples in _r_~i~ are joined only with tuples from _s_~i~ . Partitioned parallelism can be used only for natural and equi-joins.

>- In fragment and replicate, both relations are partitioned and each partition is replicated. In asymmetric fragment and replicate, one of the relations is replicated while the other is partitioned. Unlike partitioned parallelism, fragment and replicate and asymmetric fragment and replicate can be used with any join condition.  


Both parallelization techniques can work in conjunction with any join technique.

- In independent parallelism, different operations that do not depend on one another are executed in parallel.

- In pipelined parallelism, processors send the results of one operation to another operation as those results are computed, without waiting for the entire operation to finish.

- Query optimization in parallel databases is significantly more complex than query optimization in sequential databases.

- Modern multicore processors are introducing new research problems in par- allel databases.

**Review Terms**

- Decision-support queries 
- I/O parallelism 
- Horizontal partitioning 
- Partitioning techniques

>- Round-robin

>- Hash partitioning

>- Range partitioning

- Partitioning attribute 
- Partitioning vector 
- Point query 
- Range query 
- Skew

>- Execution skew

>- Attribute-value skew

>- Partition skew

- Handling of skew

>- Balanced range-partitioning vector

>- Histogram

>- Virtual processors

- Interquery parallelism
- Cache coherency 
- Intraquery parallelism

>- Intraoperation parallelism

>- Interoperation parallelism

- Parallel sort

>- Range-partitioning sort

>- Parallel external sort–merge

- Data parallelism 
- Parallel join

>- Partitioned join

>- Fragment-and-replicate join

>- Asymmetric fragment-and- replicate join

>- Partitioned parallel hash join

>- Parallel nested-loop join

- Parallel selection 
- Parallel duplicate elimination 
- Parallel projection 
- Parallel aggregation 
- Cost of parallel evaluation  
- Interoperation parallelism

>- Pipelined parallelism

>- Independent parallelism

- Query optimization
- Scheduling 
- Exchange-operator model 
- Design of parallel systems 
- Online index construction 
- Multicore processors

**Practice Exercises**

**18.1** In a range selection on a range-partitioned attribute, it is possible that only one disk may need to be accessed. Describe the benefits and drawbacks of this property.

**18.2** What form of parallelism (interquery, interoperation, or intraoperation) is likely to be the most important for each of the following tasks?

>1. Increasing the throughput of a system with many small queries

>2. Increasing the throughput of a system with a few large queries, when the number of disks and processors is large

**18.3** With pipelined parallelism, it is often a good idea to perform several operations in a pipeline on a single processor, even when many processors are available.

>1. Explain why.

>2. Would the arguments you advanced in part _a_ hold if the machine has a shared-memory architecture? Explain why or why not.

>3. Would the arguments in part _a_ hold with independent parallelism? (That is, are there cases where, even if the operations are not pipelined and there are many processors available, it is still a good idea to per- form several operations on the same processor?)

**18.4** Consider join processing using symmetric fragment and replicate with range partitioning. How can you optimize the evaluation if the join con- dition is of the form | _r.A_− _s.B_ | ≤ _k_, where _k_ is a small constant? Here, | _x_ | denotes the absolute value of _x_. A join with such a join condition is called a **band join**.

**18.5** Recall that histograms are used for constructing load-balanced range partitions.

>1. Suppose you have a histogram where values are between 1 and 100, and are partitioned into 10 ranges, 1–10, 11–20, _. . . ,_ 91–100, with frequencies 15, 5, 20, 10, 10, 5, 5, 20, 5, and 5, respectively. Give a load-balanced range partitioning function to divide the values into 5 partitions.  


>1. Write an algorithm for computing a balanced range partition with _p_ partitions, given a histogram of frequency distributions containing _n_ ranges.

**18.6** Large-scale parallel database systems store an extra copy of each data item on disks attached to a different processor, to avoid loss of data if one of the processors fails.

>1. Instead of keeping the extra copy of data items from a processor at a single backup processor, it is a good idea to partition the copies of the data items of a processor across multiple processors. Explain why.

>2. Explain how virtual-processor partitioning can be used to efficiently implement the partitioning of the copies as described above.

>3. What are the benefits and drawbacks of using RAID storage instead of storing an extra copy of each data item?

**18.7** Suppose we wish to index a large relation that is partitioned. Can the idea of partitioning (including virtual processor partitioning) be applied to indices? Explain your answer, considering the following two cases (assuming for simplicity that partitioning as well as indexing are on single attributes):

>1. Where the index is on the partitioning attribute of the relation.

>2. Where the index is on an attribute other than the partitioning attribute of the relation.

**18.8** Suppose a well-balanced range-partitioning vector had been chosen for a relation, but the relation is subsequently updated, making the partitioning unbalanced. Even if virtual-processor partitioning is used, a particular virtual processor may end up with a very large number of tuples after the update, and repartitioning would then be required.

>1. Suppose a virtual processor has a significant excess of tuples (say, twice the average). Explain how repartitioning can be done by splitting the partition, thereby increasing the number of virtual processors.

>2. If, instead of round-robin allocation of virtual processors, virtual partitions can be allocated to processors in an arbitrary fashion, with a mapping table tracking the allocation. If a particular node has excess load (compared to the others), explain how load can be balanced.

>3. Assuming there are no updates, does query processing have to be stopped while repartitioning, or reallocation of virtual processors, is carried out? Explain your answer.  


**Exercises**

**18.9** For each of the three partitioning techniques, namely round-robin, hash partitioning, and range partitioning, give an example of a query for which that partitioning technique would provide the fastest response.

**18.10** What factors could result in skew when a relation is partitioned on one of its attributes by:

>1. Hash partitioning?

>2. Range partitioning?

In each case, what can be done to reduce the skew?

**18.11** Give an example of a join that is not a simple equi-join for which partitioned parallelism can be used. What attributes should be used for partitioning?

**18.12** Describe a good way to parallelize each of the following:

a. The difference operation

b. Aggregation by the **count** operation

c. Aggregation by the **count distinct** operation

d. Aggregation by the **avg** operation

e. Left outer join, if the join condition involves only equality

f. Left outer join, if the join condition involves comparisons other than equality

g. Full outer join, if the join condition involves comparisons other than equality

**18.13** Describe the benefits and drawbacks of pipelined parallelism.

**18.14** Suppose you wish to handle a workload consisting of a large number of small transactions by using shared-nothing parallelism.

a. Is intraquery parallelism required in such a situation? If not, why, and what form of parallelism is appropriate?

b. What form of skew would be of significance with such a workload?

c. Suppose most transactions accessed one _account_ record, which in- cludes an _account type_ attribute, and an associated _account type master_ record, which provides information about the account type. How would you partition and/or replicate data to speed up transactions? You may assume that the _account type master_ relation is rarely up- dated.  

**18.15** The attribute on which a relation is partitioned can have a significant impact on the cost of a query.

a. Given a workload of SQL queries on a single relation, what attributes would be candidates for partitioning?

b. How would you choose between the alternative partitioning tech- niques, based on the workload?

c. Is it possible to partition a relation on more than one attribute? Explain your answer.

**Bibliographical Notes**

In the late 1970s and early 1980s, as the relational model gained reasonably sound footing, people recognized that relational operators are highly parallelizable and have good dataflow properties. Several research projects including GAMMA (De- Witt [1990\]), XPRS (Stonebraker et al. [1989\]) and Volcano (Graefe [1990\]) were launched to investigate the practicality of parallel execution of relational operators.

Teradata was one of the first commercial parallel database systems, and con- tinues to have a large market share. The Red Brick Warehouse was another early parallel database system; Red Brick was was acquired by Informix, which was itself acquired by IBM. More recent parallel database systems include Netezza, DATAllegro (now part of Microsoft), Greenplum, and Aster Data.

Locking in parallel databases is discussed in Joshi [1991\] and Mohan and Narang [1992\]. Cache-coherency protocols for parallel database systems are dis- cussed by Dias et al. [1989\], Mohan and Narang [1992\], and Rahm [1993\]. Carey et al. [1991\] discusses caching issues in a client–server system.

Graefe and McKenna [1993b\] presents an excellent survey of query process- ing, including parallel processing of queries. The exchange-operator model was advocated by Graefe [1990\] and Graefe and McKenna [1993b\].

Parallel sorting is discussed in DeWitt et al. [1992\]. Parallel sorting on multi- core and multithreaded processors is discussed in Garcia and Korth [2005\] and Chen et al. [2007\]. Parallel join algorithms are described by Nakayama et al. [1984\], Richardson et al. [1987\], Kitsuregawa and Ogawa [1990\], and Wilschut et al. [1995\], among other works.

Skew handling in parallel joins is described by Walton et al.[1991\], Wolf [1991\], and DeWitt et al.[1992\].

Parallel query-optimization techniques are described by Lu et al. [1991\] and Ganguly et al. [1992\].

The adaptation of database-system design and query-processing algorithms to multicore and multithreaded architectures is discussed in the proceedings of the International Workshop on Data Management on Modern Hardware (DaMoN), held annually since 2005.  

# C H A P T E R 19 

# Distributed Databases

Unlike parallel systems, in which the processors are tightly coupled and constitute a single database system, a distributed database system consists of loosely coupled sites that share no physical components. Furthermore, the database sys- tems that run on each site may have a substantial degree of mutual independence. We discussed the basic structure of distributed systems in Chapter 17.

Each site may participate in the execution of transactions that access data at one site, or several sites. The main difference between centralized and distributed database systems is that, in the former, the data reside in one single location, whereas in the latter, the data reside in several locations. This distribution of data is the cause of many difficulties in transaction processing and query processing. In this chapter, we address these difficulties.

We start by classifying distributed databases as homogeneous or heterogeneous, in Section 19.1. We then address the question of how to store data in a distributed database in Section 19.2. In Section 19.3, we outline a model for transaction processing in a distributed database. In Section 19.4, we describe how to implement atomic transactions in a distributed database by using special commit protocols. In Section 19.5, we describe concurrency control in distributed databases. In Section 19.6, we outline how to provide high availability in a dis- tributed database by exploiting replication, so the system can continue processing transactions even when there is a failure. We address query processing in distributed databases in Section 19.7. In Section 19.8, we outline issues in handling heterogeneous databases. In Section 19.10, we describe directory systems, which can be viewed as a specialized form of distributed databases.

In this chapter, we illustrate all our examples using the bank database of Figure 19.1.

## Summary

- A distributed database system consists of a collection of sites, each of which maintains a local database system. Each site is able to process local transactions: those transactions that access data in only that single site. In addition, a site may participate in the execution of global transactions: those transactions that access data in several sites. The execution of global transactions requires communication among the sites.

- Distributed databases may be homogeneous, where all sites have a common schema and database system code, or heterogeneous, where the schemas and system codes may differ.

- There are several issues involved in storing a relation in the distributed data- base, including replication and fragmentation. It is essential that the system minimize the degree to which a user needs to be aware of how a relation is stored.

- A distributed system may suffer from the same types of failure that can afflict a centralized system. There are, however, additional failures with which we need to deal in a distributed environment, including the failure of a site, the failure of a link, loss of a message, and network partition. Each of these problems needs to be considered in the design of a distributed recovery scheme.

- To ensure atomicity, all the sites in which a transaction _T_ executed must agree on the final outcome of the execution. _T_ either commits at all sites or aborts at all sites. To ensure this property, the transaction coordinator of _T_ must execute a commit protocol. The most widely used commit protocol is the two-phase commit protocol.

- The two-phase commit protocol may lead to blocking, the situation in which the fate of a transaction cannot be determined until a failed site (the coordi- nator) recovers. We can use the three-phase commit protocol to reduce the probability of blocking.

- Persistent messaging provides an alternative model for handling distributed transactions. The model breaks a single transaction into parts that are exe- cuted at different databases. Persistent messages (which are guaranteed to be delivered exactly once, regardless of failures), are sent to remote sites to request actions to be taken there. While persistent messaging avoids the blocking problem, application developers have to write code to handle vari- ous types of failures.

- The various concurrency-control schemes used in a centralized system can be modified for use in a distributed environment.

>- In the case of locking protocols, the only change that needs to be incorporated is in the way that the lock manager is implemented. There are a variety of different approaches here. One or more central coordinators may be used. If, instead, a distributed-lock-manager approach is taken, replicated data must be treated specially.

>- Protocols for handling replicated data include the primary copy, majority, biased, and quorum consensus protocols. These have different trade-offs in terms of cost and ability to work in the presence of failures.

>- In the case of timestamping and validation schemes, the only needed change is to develop a mechanism for generating unique global times- tamps.

>- Many database systems support lazy replication, where updates are prop- agated to replicas outside the scope of the transaction that performed the update. Such facilities must be used with great care, since they may result in nonserializable executions.

- Deadlock detection in a distributed-lock-manager environment requires co-operation between multiple sites, since there may be global deadlocks even when there are no local deadlocks.  

- To provide high availability, a distributed database must detect failures, reconfigure itself so that computation may continue, and recover when a pro- cessor or a link is repaired. The task is greatly complicated by the fact that it is hard to distinguish between network partitions and site failures.

The majority protocol can be extended by using version numbers to permit transaction processing to proceed even in the presence of failures. While the protocol has a significant overhead, it works regardless of the type of failure. Less-expensive protocols are available to deal with site failures, but they assume network partitioning does not occur.

- Some of the distributed algorithms require the use of a coordinator. To provide high availability, the system must maintain a backup copy that is ready to assume responsibility if the coordinator fails. Another approach is to choose the new coordinator after the coordinator has failed. The algorithms that determine which site should act as a coordinator are called **election algorithms**.

- Queries on a distributed database may need to access multiple sites. Several optimization techniques are available to identify the best set of sites to access. Queries can be rewritten automatically in terms of fragments of relations and then choices can be made among the replicas of each fragment. Semijoin techniques may be employed to reduce data transfer involved in joining relations (or fragments or relicas thereof) across distinct sites.

- Heterogeneous distributed databases allow sites to have their own schemas and database system code. A multidatabase system provides an environment in which new database applications can access data from a variety of pre-existing databases located in various heterogeneous hardware and software environments. The local database systems may employ different logical mod- els and data-definition and data-manipulation languages, and may differ in their concurrency-control and transaction-management mechanisms. A multidatabase system creates the illusion of logical database integration, without requiring physical database integration.

- A large number of data-storage systems on the cloud have been built in recent years, in response to data storage needs of extremely large-scale Web applications. These data-storage systems allow scalability to thousands of nodes, with geographic distribution, and high availability. However, they do not support the usual ACID properties, and they achieve availability during partitions at the cost of consistency of replicas. Current data-storage systems also do not support SQL, and provide only a simple put()/get() interface. While cloud computing is attractive even for traditional databases, there are several challenges due to lack of control on data placement and geographic replication.

- Directory systems can be viewed as a specialized form of database, where information is organized in a hierarchical fashion similar to the way files are organized in a file system. Directories are accessed by standardized directory access protocols such as LDAP. Directories can be distributed across multiple sites to provide autonomy to individual sites. Directories can contain referrals to other directories, which help build an integrated view whereby a query is sent to a single directory, and it is transparently executed at all relevant directories.

**Review Terms**

- Homogeneous distributed database
- Heterogeneous distributed database
- Data replication 
- Primary copy 
- Data fragmentation

>- Horizontal fragmentation

>- Vertical fragmentation

>- Data transparency

>- Fragmentation transparency

>- Replication transparency

>- Location transparency

- Name server 
- Aliases 
- Distributed transactions

>- Local transactions

>- Global transactions

- Transaction manager 
- Transaction coordinator 
- System failure modes 
- Network partition 
- Commit protocols 
- Two-phase commit protocol (2PC)

>- Ready state

>- In-doubt transactions

>- Blocking problem

>- Three-phase commit protocol (3PC)

- Persistent messaging 
- Concurrency control 
- Single lock manager 
- Distributed lock manager 
- Protocols for replicas

>- Primary copy

>- Majority protocol

>- Biased protocol

>- Quorum consensus protocol

- Timestamping 
- Master–slave replication 
- Multimaster (update-anywhere)replication 
- Transaction-consistent snapshot 
- Lazy propagation 
- Deadlock handling

>- Local wait-for graph

>- Global wait-for graph

>- False cycles

>- Availability • Robustness

>- Majority-based approach

>- Read one, write all

>- Read one, write all available

>- Site reintegration

- Coordinator selection  
- Backup coordinator 
- Election algorithms 
- Bully algorithm 
- Distributed query processing 
- Semijoin strategy 
- Multidatabase system

>- Autonomy

>- Mediators

>- Local transactions

>- Global transactions

>- Ensuring global serializability

>- Ticket

- Cloud computing
- Cloud data storage 
- Tablet 
- Directory systems 
- LDAP: Lightweight Directory
- Access Protocol

 >- Distinguished name (DN)

 >-Relative distinguished names (RDNs)

 >-Directory information tree (DIT)

- Distributed directory trees

 >-DIT suffix

 >-Referral

**Practice Exercises**

**19.1** How might a distributed database designed for a local-area network differ from one designed for a wide-area network?

**19.2** To build a highly available distributed system, you must know what kinds of failures can occur.

1. List possible types of failure in a distributed system.

2. Which items in your list from part a are also applicable to a central- ized system?

**19.3** Consider a failure that occurs during 2PC for a transaction. For each pos- sible failure that you listed in Practice Exercise 19.2a, explain how 2PC ensures transaction atomicity despite the failure.

**19.4** Consider a distributed system with two sites, _A_ and _B_. Can site _A_ distin- guish among the following?

- _B_ goes down.

- The link between _A_ and _B_ goes down.

- _B_ is extremely overloaded and response time is 100 times longer than normal.

What implications does your answer have for recovery in distributed systems?  

**19.5** The persistent messaging scheme described in this chapter depends on timestamps combined with discarding of received messages if they are too old. Suggest an alternative scheme based on sequence numbers instead of timestamps.

**19.6** Give an example where the read one, write all available approach leads to an erroneous state.

**19.7** Explain the difference between data replication in a distributed system and the maintenance of a remote backup site.

**19.8** Give an example where lazy replication can lead to an inconsistent database state even when updates get an exclusive lock on the primary (master) copy.

**19.9** Consider the following deadlock-detection algorithm. When transaction _T_~i~ , at site _S_~1~, requests a resource from _T_~j~ , at site _S_~3~, a request message with timestamp _n_ is sent. The edge (_T~i~ , T~j~ , n_) is inserted in the local wait-for graph of _S_~1~\. The edge (_T~i~ , T~j~ , n_) is inserted in the local wait-for graph of _S_~3~ only if _T~j~_ has received the request message and cannot immediately grant the requested resource. A request from _T~i~_ to _T~j~_ in the same site is handled in the usual manner; no timestamps are associated with the edge (_T~i~ , T~j~_ ). A central coordinator invokes the detection algorithm by sending an initiating message to each site in the system.

On receiving this message, a site sends its local wait-for graph to the coordinator. Note that such a graph contains all the local information that the site has about the state of the real graph. The wait-for graph reflects an instantaneous state of the site, but it is not synchronized with respect to any other site.

When the controller has received a reply from each site, it constructs a graph as follows:

- The graph contains a vertex for every transaction in the system.

- The graph has an edge (_T~i~ , T~j~_ ) if and only if:

>- There is an edge (_T~i~ , T~j~_ ) in one of the wait-for graphs.

>- An edge (_T~i~ , T~j~ , n_) (for some _n_) appears in more than one wait-for graph.

Show that, if there is a cycle in the constructed graph, then the system is in a deadlock state, and that, if there is no cycle in the constructed graph, then the system was not in a deadlock state when the execution of the algorithm began.

**19.10** Consider a relation that is fragmented horizontally by _plant number_:

_employee_ (_name, address, salary, plant number_)  

Assume that each fragment has two replicas: one stored at the New York site and one stored locally at the plant site. Describe a good processing strategy for the following queries entered at the San Jose site.

1. Find all employees at the Boca plant.

2. Find the average salary of all employees.

3. Find the highest-paid employee at each of the following sites: Toronto, Edmonton, Vancouver, Montreal.

4. Find the lowest-paid employee in the company.

**19.11** Compute _r_ ⨝ _s_ for the relations of Figure 19.9.

**19.12** Give an example of an application ideally suited for the cloud and another that would be hard to implement successfully in the cloud. Explain your answer.

**19.13** Given that the LDAP functionality can be implemented on top of a database system, what is the need for the LDAP standard?

**19.14** Consider a multidatabase system in which it is guaranteed that at most one global transaction is active at any time, and every local site ensures local serializability.

1. Suggest ways in which the multidatabase system can ensure that there is at most one active global transaction at any time.

2. Show by example that it is possible for a nonserializable global schedule to result despite the assumptions.

**19.15** Consider a multidatabase system in which every local site ensures local serializability, and all global transactions are read only.

1. Show by example that nonserializable executions may result in such a system.

2. Show how you could use a ticket scheme to ensure global serializ- ability.

![Alt text](figure-19.9.png)

**Exercises**

**19.16** Discuss the relative advantages of centralized and distributed databases.

**19.17** Explain how the following differ: fragmentation transparency, replication transparency, and location transparency.

**19.18** When is it useful to have replication or fragmentation of data? Explain your answer.

**19.19** Explain the notions of transparency and autonomy. Why are these notions desirable from a human-factors standpoint?

**19.20** If we apply a distributed version of the multiple-granularity protocol of Chapter 15 to a distributed database, the site responsible for the root of the DAG may become a bottleneck. Suppose we modify that protocol as follows:

- Only intention-mode locks are allowed on the root.

- All transactions are given all possible intention-mode locks on the root automatically.

Show that these modifications alleviate this problem without allowing any nonserializable schedules.

**19.21** Study and summarize the facilities that the database system you are using provides for dealing with inconsistent states that can be reached with lazy propagation of updates.

**19.22** Discuss the advantages and disadvantages of the two methods that we presented in Section 19.5.2 for generating globally unique timestamps.

**19.23** Consider the relations:

_employee_ (_name, address, salary, plant_number_)
 _machine_ (_machine_number, type, plant_number_)

Assume that the _employee_ relation is fragmented horizontally by _plant number_, and that each fragment is stored locally at its corresponding

plant site. Assume that the _machine_ relation is stored in its entirety at the Armonk site. Describe a good strategy for processing each of the following queries.

1. Find all employees at the plant that contains machine number 1130.

2. Find all employees at plants that contain machines whose type is “milling machine.”

3. Find all machines at the Almaden plant.

4. Find employee  machine.  

**19.24** For each of the strategies of Exercise 19.23, state how your choice of a strategy depends on:

1. The site at which the query was entered.

2. The site at which the result is desired.

**19.25** Is the expression _r~i~_ ⨝ _r ~j~_ necessarily equal to _r ~j~_ ⨝ _r~i~_ ? Under what conditions does _r~i~_ ⨝ _r ~j~_ \= _r ~j~_ ⨝ _r~i~_ hold?

**19.26** If a cloud data-storage service is used to store two relations _r_ and _s_ and we need to join _r_ and _s_, why might it be useful to maintain the join as a materialized view? In your answer, be sure to distinguish among various meanings of “useful”: overall throughput, efficient use of space, and response time to user queries.

**19.27** Why do cloud-computing services support traditional database systems best by using a virtual machine instead of running directly on the service provider’s actual machine?

**19.28** Describe how LDAP can be used to provide multiple hierarchical views of data, without replicating the base-level data.

**Bibliographical Notes**

Textbook discussions of distributed databases are offered by Ozsu and Valduriez [1999\]. Breitbart et al. [1999b\] presents an overview of distributed databases.

The implementation of the transaction concept in a distributed database is presented by Gray [1981\] and Traiger et al. [1982\]. The 2PC protocol was developed by Lampson and Sturgis [1976\]. The three-phase commit protocol is from Skeen [1981\]. Mohan and Lindsay [1983\] discusses two modified versions of 2PC, called _presume commit_ and _presume abort_, that reduce the overhead of 2PC by defining default assumptions regarding the fate of transactions.

The bully algorithm in Section 19.6.5 is from Garcia-Molina [1982\]. Distributed clock synchronization is discussed in Lamport [1978\]. Distributed concurrency control is covered by Bernstein and Goodman [1981\].

The transaction manager of R* is described in Mohan et al. [1986\]. Valida- tion techniques for distributed concurrency-control schemes are described by Schlageter [1981\] and Bassiouni [1988\].

The problem of concurrent updates to replicated data was revisited in the context of data warehouses by Gray et al. [1996\]. Anderson et al. [1998\] dis- cusses issues concerning lazy replication and consistency. Breitbart et al. [1999a\] describes lazy update protocols for handling replication.

The user manuals of various database systems provide details of how they handle replication and consistency. Huang and Garcia-Molina [2001\] addresses exactly-once semantics in a replicated messaging system.

Knapp [1987\] surveys the distributed deadlock-detection literature. Practice Exercise 19.9 is from Stuart et al. [1984\].  

Distributed query processing is discussed in Epstein et al. [1978\] and Hevner and Yao [1979\]. Daniels et al. [1982\] discusses the approach to distributed query processing taken by R*.

Dynamic query optimization in multidatabases is addressed by Ozcan et al. [1997\]. Adali et al. [1996\] and Papakonstantinou et al. [1996\] describe query- optimization issues in mediator systems.

Transaction processing in multidatabase systems is discussed in Mehrotra et al. [2001\]. The ticket scheme is presented in Georgakopoulos et al. [1994\]. 2LSR is introduced in Mehrotra et al. [1991\].

A collection of papers on data management on cloud systems is in Ooi and S. Parthasarathy [2009\]. The implementation of Google’s Bigtable is described in Chang et al. [2008\], while Cooper et al. [2008\] describe Yahoo!’s PNUTS system. Experience in building a database using Amazon’s S3 cloud-based storage is described in Brantner et al. [2008\]. An approach to making transactions work correctly in cloud systems is discussed in Lomet et al. [2009\]. The CAP theorem was conjectured by Brewer [2000\], and was formalized and proved by Gilbert and Lynch [2002\].

Howes et al. [1999\] provides textbook coverage of LDAP.
