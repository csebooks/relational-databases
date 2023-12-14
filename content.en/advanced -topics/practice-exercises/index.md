---
title: Practice Exercises
weight: 5
---

Practice Exercises## Practice Exercises

**24.1** Many applications need to generate sequence numbers for each transac- tion.

a. If a sequence counter is locked in two-phase manner, it can become a concurrency bottleneck. Explain why this may be the case.
b. Many database systems support built-in sequence counters that are not locked in two-phase manner; when a transaction requests a se- quence number, the counter is locked, incremented and unlocked. 

i. Explain how such counters can improve concurrency.

ii. Explain why there may be gaps in the sequence numbers belong- ing to the final set of committed transactions.

**24.2** Suppose you are given a relation _r_ (_a , b, c_).

a. Give an example of a situation under which the performance of equality selection queries on attribute _a_ can be greatly affected by how _r_ is clustered.

b. Suppose you also had range selection queries on attribute _b_. Can you cluster _r_ in such a way that the equality selection queries on _r.a_ and the range selection queries on _r.b_ can both be answered efficiently? Explain your answer.

c. If clustering as above is not possible, suggest how both types of queries can be executed efficiently by choosing appropriate indices, assuming your database supports index-only plans (that is, if all in- formation required for a query is available in an index, the database can generate a plan that uses the index but does not access the rela- tion).

**24.3** Suppose that a database application does not appear to have a single bottleneck; that is, CPU and disk utilization are both high, and all database queues are roughly balanced. Does that mean the application cannot be tuned further? Explain your answer.

**24.4** Suppose a system runs three types of transactions. Transactions of type A run at the rate of 50 per second, transactions of type B run at 100 per second, and transactions of type C run at 200 per second. Suppose the mix of transactions has 25 percent of type A, 25 percent of type B, and 50 percent of type C.

a. What is the average transaction throughput of the system, assuming there is no interference between the transactions?

b. What factors may result in interference between the transactions of different types, leading to the calculated throughput being incorrect?

**24.5** List some benefits and drawbacks of an anticipatory standard compared to a reactionary standard.

**24.6** Find out all performance information your favorite database system pro- vides. Look for at least the following: what queries are currently executing or executed recently, what resources each of them consumed (CPU and I/O), what fraction of page requests resulted in buffer misses (for each query, if available), and what locks have a high degree of contention. You may also be able to get information about CPU and I/O utilization from the operating system.

**24.7** a. What are the three broad levels at which a database system can be tuned to improve performance?

b. Give two examples of how tuning can be done for each of the levels.

**24.8** When carrying out performance tuning, should you try to tune your hardware (by adding disks or memory) first, or should you try to tune your transactions (by adding indices or materialized views) first? Explain your answer.

**24.9** Suppose that your application has transactions that each access and up- date a single tuple in a very large relation stored in a B+-tree file organiza- tion. Assume that all internal nodes of the B+-tree are in memory, but only a very small fraction of the leaf pages can fit in memory. Explain how to calculate the minimum number of disks required to support a workload of 1000 transactions per second. Also calculate the required number of disks, using values for disk parameters given in Section 10.2.

**24.10** What is the motivation for splitting a long transaction into a series of small ones? What problems could arise as a result, and how can these problems be averted?

**24.11** Suppose the price of memory falls by half, and the speed of disk access (number of accesses per second) doubles, while all other factors remain the same. What would be the effect of this change on the 5-minute and 1-minute rules?

**24.12** List at least four features of the TPC benchmarks that help make them realistic and dependable measures.

**24.13** Why was the TPC-D benchmark replaced by the TPC-H and TPC-R bench- marks?

**24.14** Explain what application characteristics would help you decide which of TPC-C, TPC-H, or TPC-R best models the application.

