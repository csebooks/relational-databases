---
title: Summary
weight: 12
---

Summary## Summary

- Time plays an important role in database systems. Databases are models of the real world. Whereas most databases model the state of the real world at a point in time (at the current time), temporal databases model the states of the real world across time.

- Facts in temporal relations have associated times when they are valid, which can be represented as a union of intervals. Temporal query languages simplify modeling of time, as well as time-related queries.

- Spatial databases are finding increasing use today to store computer-aided- design data as well as geographic data.

- Design data are stored primarily as vector data; geographic data consist of a combination of vector and raster data. Spatial-integrity constraints are important for design data.

- Vector data can be encoded as first-normal-form data, or they can be stored using non-first-normal-form structures, such as lists. Special-purpose index structures are particularly important for accessing spatial data, and for pro- cessing spatial queries.  

- R-trees are a multidimensional extension of B-trees; with variants such as R+-trees and R∗-trees, they have proved popular in spatial databases. Index structures that partition space in a regular fashion, such as quadtrees, help in processing spatial join queries.

- Multimedia databases are growing in importance. Issues such as similarity- based retrieval and delivery of data at guaranteed rates are topics of current research.

- Mobile computing systems have become common, leading to interest in data- base systems that can run on such systems. Query processing in such systems may involve lookups on server databases. The query cost model must include the cost of communication, including monetary cost and battery-power cost, which is relatively high for mobile systems.

- Broadcast is much cheaper per recipient than is point-to-point communica- tion, and broadcast of data such as stock-market data helps mobile systems to pick up data inexpensively.

- Disconnected operation, use of broadcast data, and caching of data are three important issues being addressed in mobile computing.

### Review Terms

- Temporal data 
- Valid time 
- Transaction time 
- Temporal relation 
- Bitemporal relation 
- Universal coordinated time (UTC) 
- Snapshot relation 
- Temporal query languages 
- Temporal selection 
- Temporal projection 
- Temporal join 
- Spatial and geographic data 
- Computer-aided-design (CAD)data 
- Geographic data 
- Geographic information systems 
- Triangulation 
- Design databases
- Geographic data 
- Raster data 
- Vector data 
- Global positioning system (GPS) 
- Spatial queries 
- Nearness queries 
- Nearest-neighbor queries 
- Region queries 
- Spatial join 
- Indexing of spatial data 
- k-d trees 
- k-d-B trees 
- Quadtrees

  ◦ PR quadtree
  
  ◦ Region quadtree

- R-trees

  ◦ Bounding box  

  ◦ Quadratic split

- Multimedia databases 
- Isochronous data 
- Continuous-media data 
- Similarity-based retrieval 
- Multimedia data formats 
- Video servers 
- Mobile computing

  ◦ Mobile hosts
  
  ◦ Mobile support stations
  
  ◦ Cell
  
  ◦ Handoff

- Location-dependent queries 
- Broadcast data 
- Consistency

  ◦ Invalidation reports
  
  ◦ Version-vector scheme

**25.1** What are the two types of time, and how are they different? Why does it make sense to have both types of time associated with a tuple?

**25.2** Suppose you have a relation containing the _x, y_ coordinates and names of restaurants. Suppose also that the only queries that will be asked are of the following form: The query specifies a point, and asks if there is a restaurant exactly at that point. Which type of index would be preferable, R-tree or B-tree? Why?

**25.3** Suppose you have a spatial database that supports region queries (with circular regions) but not nearest-neighbor queries. Describe an algorithm to find the nearest neighbor by making use of multiple region queries.

**25.4** Suppose you want to store line segments in an R-tree. If a line segment is not parallel to the axes, the bounding box for it can be large, containing a large empty area.

- Describe the effect on performance of having large bounding boxes on queries that ask for line segments intersecting a given region.

- Briefly describe a technique to improve performance for such queries and give an example of its benefit. Hint: You can divide segments into smaller pieces.

**25.5** Give a recursive procedure to efficiently compute the spatial join of two relations with R-tree indices. (Hint: Use bounding boxes to check if leaf entries under a pair of internal nodes may intersect.)

**25.6** Describe how the ideas behind the RAID organization (Section 10.3) can be used in a broadcast-data environment, where there may occasionally be noise that prevents reception of part of the data being transmitted.

**25.7** Define a model of repeatedly broadcast data in which the broadcast medium is modeled as a virtual disk. Describe how access time and data transfer rate for this virtual disk differ from the corresponding values for a typical hard disk.

**25.8** Consider a database of documents in which all documents are kept in a central database. Copies of some documents are kept on mobile comput- ers. Suppose that mobile computer A updates a copy of document 1 while it is disconnected, and, at the same time, mobile computer B updates a copy of document 2 while it is disconnected. Show how the version-vector scheme can ensure proper updating of the central database and mobile computers when a mobile computer reconnects.

## Summary

- Workflows are activities that involve the coordinated execution of multiple tasks performed by different processing entities. They exist not just in com- puter applications, but also in almost all organizational activities. With the growth of networks, and the existence of multiple autonomous database sys- tems, workflows provide a convenient way of carrying out tasks that involve multiple systems.

- Although the usual ACID transactional requirements are too strong or are unimplementable for such workflow applications, workflows must satisfy a limited set of transactional properties that guarantee that a process is not left in an inconsistent state.

- Transaction-processing monitors were initially developed as multithreaded servers that could service large numbers of terminals from a single process. They have since evolved, and today they provide the infrastructure for build- ing and administering complex transaction-processing systems that have a large number of clients and multiple servers. They provide services such as durable queueing of client requests and server responses, routing of client messages to servers, persistent messaging, load balancing, and coordination of two-phase commit when transactions access multiple servers.

- E-commerce systems have become a core part of commerce. There are sev- eral database issues in e-commerce systems. Catalog management, especially personalization of the catalog, is done with databases. Electronic market- places help in pricing of products through auctions, reverse auctions, or exchanges. High-performance database systems are needed to handle such trading. Orders are settled by electronic payment systems, which also need high-performance database systems to handle very high transaction rates.

- Large main memories are exploited in certain systems to achieve high sys- tem throughput. In such systems, logging is a bottleneck. Under the group- commit concept, the number of outputs to stable storage can be reduced, thus releasing this bottleneck.

- The efficient management of long-duration interactive transactions is more complex, because of the long-duration waits and because of the possibil- ity of aborts. Since the concurrency-control techniques used in Chapter 15 use waits, aborts, or both, alternative techniques must be considered. These techniques must ensure correctness without requiring serializability.

- A long-duration transaction is represented as a nested transaction with atomic database operations at the lowest level. If a transaction fails, only active short- duration transactions abort. Active long-duration transactions resume once any short-duration transactions have recovered. A compensating transaction is needed to undo updates of nested transactions that have committed, if the outer-level transaction fails.

- In systems with real-time constraints, correctness of execution involves not only database consistency but also deadline satisfaction. The wide variance of execution times for read and write operations complicates the transaction- management problem for time-constrained systems.

### Review Terms

- TP monitor 
- TP-monitor architectures

  ◦ Process per client
  
  ◦ Single server 

  ◦ Many server, single router
  
  ◦ Many server, many router

- Multitasking 
- Context switch  
- Multithreaded server 
- Queue manager 
- Application coordination

  ◦ Resource manager
  
  ◦ Remote procedure call (RPC)

- Transactional workflows
  
  ◦ Task
  
  ◦ Processing entity
  
  ◦ Workflow specification
  
  ◦ Workflow execution

- Workflow state

  ◦ Execution states
  
  ◦ Output values
  
  ◦ External variables

- Workflow failure atomicity 
- Workflow termination states
  
  ◦ Acceptable
  
  ◦ Nonacceptable
  
  ◦ Committed
  
  ◦ Aborted

- Workflow recovery 
- Workflow-management system 
- Workflow-management system architectures
  
  ◦ Centralized
  
  ◦ Partially distributed
  
  ◦ Fully distributed

- Business process management 
- Orchestration 
- E-commerce 
- E-catalogs 
- Marketplaces
  
  ◦ Auctions
  
  ◦ Reverse auctions
  
  ◦ Exchange

- Order settlement 
- Digital certificates 
- Main-memory databases 
- Group commit 
- Real-time systems 
- Deadlines

  ◦ Hard deadline
  
  ◦ Firm deadline
  
  ◦ Soft deadline

- Real-time databases 
- Long-duration transactions 
- Exposure of uncommitted data 
- Nonserializable executions 
- Nested transactions 
- Multilevel transactions 
- Saga 
- Compensating transactions 
- Logical logging

### Practice Exercises

**26.1** Like database systems, workflow systems also require concurrency and recovery management. List three reasons why we cannot simply apply a relational database system using 2PL, physical undo logging, and 2PC. 

**26.2** Consider a main-memory database system recovering from a system crash. Explain the relative merits of:

- Loading the entire database back into main memory before resuming transaction processing.

- Loading data as it is requested by transactions.

**26.3** Is a high-performance transaction system necessarily a real-time system? Why or why not?

**26.4** Explain why it may be impractical to require serializability for long- duration transactions.

**26.5** Consider a multithreaded process that delivers messages from a durable queue of persistent messages. Different threads may run concurrently, attempting to deliver different messages. In case of a delivery failure, the message must be restored in the queue. Model the actions that each thread carries out as a multilevel transaction, so that locks on the queue need not be held until a message is delivered.

**26.6** Discuss the modifications that need to be made in each of the recovery schemes covered in Chapter 16 if we allow nested transactions. Also, explain any differences that result if we allow multilevel transactions.

### Exercises

**26.7** Explain how a TP monitor manages memory and processor resources more effectively than a typical operating system.

**26.8** Compare TP-monitor features with those provided by Web servers sup- porting servlets (such servers have been nicknamed _TP-lite_).

**26.9** Consider the process of admitting new students at your university (or new employees at your organization).

a. Give a high-level picture of the workflow starting from the student application procedure.

b. Indicate acceptable termination states and which steps involve hu- man intervention.

c. Indicate possible errors (including deadline expiry) and how they are dealt with.

d. Study how much of the workflow has been automated at your uni- versity.

**26.10** Answer the following questions regarding electronic payment systems: 

a. Explain why electronic transactions carried out using credit-card numbers may be insecure.

b. An alternative is to have an electronic payment gateway maintained by the credit-card company, and the site receiving payment redirects customers to the gateway site to make the payment.
i. Explain what benefits such a system offers if the gateway does not authenticate the user. 
ii. Explain what further benefits are offered if the gateway has a

mechanism to authenticate the user.

c. Some credit-card companies offer a one-time-use credit-card number as a more secure method of electronic payment. Customers connect to the credit-card company’s Web site to get the one-time-use num- ber. Explain what benefit such a system offers, as compared to using regular credit-card numbers. Also explain its benefits and drawbacks as compared to electronic payment gateways with authentication.

d. Does either of the above systems guarantee the same privacy that is available when payments are made in cash? Explain your answer.

**26.11** If the entire database fits in main memory, do we still need a database system to manage the data? Explain your answer.

**26.12** In the group-commit technique, how many transactions should be part of a group? Explain your answer.

**26.13** In a database system using write-ahead logging, what is the worst-case number of disk accesses required to read a data item from a specified disk page. Explain why this presents a problem to designers of real-time database systems. Hint: consider the case when the disk buffer is full.

**26.14** What is the purpose of compensating transactions? Present two examples of their use.

**26.15** Explain the connections between a workflow and a long-duration trans- action.

### Bibliographical Notes

Gray and Reuter [1993] provides a detailed (and excellent) textbook description of transaction-processing systems, including chapters on TP monitors. X/Open [1991] defines the X/Open XA interface.

Fischer [2006] is a handbook on workflow systems, which is published in association with the Workflow Management Coalition. The Web site of the coalition is www.wfmc.org. Our description of workflows follows the model of Rusinkiewicz and Sheth [1995].

Loeb [1998] provides a detailed description of secure electronic transactions.

Garcia-Molina and Salem [1992] provides an overview of main-memory databases. Jagadish et al. [1993] describes a recovery algorithm designed for mainmemory databases. A storage manager for main-memory databases is described in Jagadish et al. [1994].

Real-time databases are discussed by Lam and Kuo [2001]. Concurrency control and scheduling in real-time databases are discussed by Haritsa et al. [1990],Hong et al. [1993], and Pang et al. [1995]. Ozsoyoglu and Snodgrass [1995] is a survey of research in real-time and temporal databases.

Nested and multilevel transactions are presented by Moss [1985], Lynch and Merritt [1986], Moss [1987], Haerder and Rothermel [1987], Rothermel and Mohan [1989], Weikum et al. [1990], Korth and Speegle [1990], Weikum [1991], and Korth and Speegle [1994], Theoretical aspects of multilevel transactions are presented in Lynch et al. [1988]. The concept of Saga was introduced in Garcia-Molina and Salem [1987]
