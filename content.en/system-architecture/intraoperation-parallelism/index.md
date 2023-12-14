---
title: Intraoperation Parallelism
weight: 13
---

Intraoperation Parallelism## Intraoperation Parallelism

Since relational operations work on relations containing large sets of tuples, we can parallelize the operations by executing them in parallel on different subsets of the relations. Since the number of tuples in a relation can be large, the degree of parallelism is potentially enormous. Thus, intraoperation parallelism is natural in a database system. We shall study parallel versions of some common relational operations in Sections 18.5.1 through 18.5.3.  


### Parallel Sort

Suppose that we wish to sort a relation that resides on _n_ disks _D_~0~, _D_~1~, . . . , _D_~n−1~. If the relation has been range-partitioned on the attributes on which it is to be sorted, then, as noted in Section 18.2.2, we can sort each partition separately, and can concatenate the results to get the full sorted relation. Since the tuples are partitioned on **n** disks, the time required for reading the entire relation is reduced by the parallel access.

If the relation has been partitioned in any other way, we can sort it in one of two ways:

**1\.** We can range-partition it on the sort attributes, and then sort each partition separately.

**2\.** We can use a parallel version of the external sort–merge algorithm.

#### 18.5.1.1 Range-Partitioning Sort

**Range-partitioning sort** works in two steps: first range partitioning the relation, then sorting each partition separately. When we sort by range partitioning the relation, it is not necessary to range-partition the relation on the same set of processors or disks as those on which that relation is stored. Suppose that we choose processors _P_~0~, _P_~1~, . . . , _P~m~, where _m < n_, to sort the relation. There are two steps involved in this operation:

**1\.** Redistribute the tuples in the relation, using a range-partition strategy, so that all tuples that lie within the _i_th range are sent to processor _P_~i~ , which stores the relation temporarily on disk _D_~i~ .

To implement range partitioning, in parallel every processor reads the tuples from its disk and sends the tuples to their destination processors. Each processor P~0~, P~1~, . . . , P~m~ also receives tuples belonging to its partition, and stores them locally. This step requires disk I/O and communication overhead.

**2\.** Each of the processors sorts its partition of the relation locally, without interaction with the other processors. Each processor executes the same operation—namely, sorting—on a different data set. (Execution of the same operation in parallel on different sets of data is called **data parallelism**.)

The final merge operation is trivial, because the range partitioning in the first phase ensures that, for 1 ≤ i < j ≤ m, the key values in processor _P_~i~ are all less than the key values in _P_~j~ .

We must do range partitioning with a good range-partition vector, so that each partition will have approximately the same number of tuples. Virtual processor partitioning can also be used to reduce skew.  


#### 18.5.1.2 Parallel External Sort–Merge

**Parallel external sort–merge** is an alternative to range partitioning. Suppose that a relation has already been partitioned among disks D~0~, D~1~, . . . , D~n−1~ (it does not matter how the relation has been partitioned). Parallel external sort–merge then works this way:

**1\.** Each processor _P_~i~ locally sorts the data on disk _D_~i~ .

**2\.** The system then merges the sorted runs on each processor to get the final sorted output.

The merging of the sorted runs in step 2 can be parallelized by this sequence of actions:

**1\.** The system range-partitions the sorted partitions at each processor _P_~i~ (all by the same partition vector) across the processors P~0~, P~1~, . . . , P~m−1~. It sends the tuples in sorted order, so that each processor receives the tuples in sorted streams.

**2\.** Each processor **Pi** performs a merge on the streams as they are received, to get a single sorted run.

**3\.** The system concatenates the sorted runs on processors P~0~, P~1~, . . . , P~m−1~ to get the final result.

As described, this sequence of actions results in an interesting form of **execution skew**, since at first every processor sends all blocks of partition 0 to P~0~, then every processor sends all blocks of partition 1 to P~1~, and so on. Thus, while sending happens in parallel, receiving tuples becomes sequential: First only P~0~ receives tuples, then only P~1~ receives tuples, and so on. To avoid this problem, each processor repeatedly sends a block of data to each partition. In other words, each processor sends the first block of every partition, then sends the second block of every partition, and so on. As a result, all processors receive data in parallel.

Some machines, such as the Teradata Purpose-Built Platform Family machines, use specialized hardware to perform merging. The BYNET interconnection network in the Teradata machines can merge output from multiple processors to give a single sorted output.

### Parallel Join

The join operation requires that the system test pairs of tuples to see whether they satisfy the join condition; if they do, the system adds the pair to the join output. Parallel join algorithms attempt to split the pairs to be tested over several processors. Each processor then computes part of the join locally. Then, the system collects the results from each processor to produce the final result.  

#### 18.5.2.1 Partitioned Join

For certain kinds of joins, such as equi-joins and natural joins, it is possible to _partition_ the two input relations across the processors and to compute the join locally at each processor. Suppose that we are using _n_ processors and that the relations to be joined are _r_ and _s_. **Partitioned join** then works this way: The system partitions the relations r and s each into n partitions, denoted r~0~, r~1~, . . . , r~n−1~ and s~0~, s~1~, . . . , s~n−1~. The system sends partitions _r_~i~ and _s_~i~ to processor _P_~i~ , where their join is computed locally.

The partitioned join technique works correctly only if the join is an equi-join (for example, _r ⨝r.~A_\=_s.B~ s_) and if we partition _r_ and _s_ by the same partitioning function on their join attributes. The idea of partitioning is exactly the same as that behind the partitioning step of hash join. In a partitioned join, however, there are two different ways of partitioning _r_ and _s_:

- Range partitioning on the join attributes.

- Hash partitioning on the join attributes.

In either case, the same partitioning function must be used for both relations. For range partitioning, the same partition vector must be used for both relations. For hash partitioning, the same hash function must be used on both relations. Figure 18.2 depicts the partitioning in a partitioned parallel join.

Once the relations are partitioned, we can use any join technique locally at each processor _P_~i~ to compute the join of _r_~i~ and _s_~i~ . For example, hash join, merge join, or nested-loop join could be used. Thus, we can use partitioning to parallelize any join technique.

![Alt text](figure-18.2.png)

If one or both of the relations _r_ and _s_ are already partitioned on the join attributes (by either hash partitioning or range partitioning), the work needed for partitioning is reduced greatly. If the relations are not partitioned, or are partitioned on attributes other than the join attributes, then the tuples need to be repartitioned. Each processor _P_~i~ reads in the tuples on disk _D_~i~ , computes for each tuple _t_ the partition _j_ to which _t_ belongs, and sends tuple _t_ to processor _P_~j~ . Processor _P_~j~ stores the tuples on disk _D_~j~ .

We can optimize the join algorithm used locally at each processor to reduce I/O by buffering some of the tuples to memory, instead of writing them to disk. We describe such optimizations in Section 18.5.2.3.

Skew presents a special problem when range partitioning is used, since a partition vector that splits one relation of the join into equal-sized partitions may split the other relations into partitions of widely varying size. The partition vector should be such that |_r_~i~ | + |_s~i~_ | (that is, the sum of the sizes of _r~i~_ and _s~i~_ ) is roughly equal over all the _i_ \= 0_,_ 1_, . . . , n − 1. With a good hash function, hash partitioning is likely to have a smaller skew, except when there are many tuples with the same values for the join attributes.

#### 18.5.2.2 Fragment-and-Replicate Join

Partitioning is not applicable to all types of joins. For instance, if the join condition is an inequality, such as _r ⨝~r.a<s.b ~s_, it is possible that all tuples in _r_ join with some tuple in _s_ (and vice versa). Thus, there may be no easy way of partitioning _r_ and _s_ so that tuples in partition _r_~i~ join with only tuples in partition _s_~i~ .

We can parallelize such joins by using a technique called _fragment and replicate_. We first consider a special case of fragment and replicate—**asymmetric fragment- and-replicate join**—which works as follows:

**1\.** The system partitions one of the relations—say, _r_ . Any partitioning tech- nique can be used on _r_ , including round-robin partitioning.

**2\.** The system replicates the other relation, _s_, across all the processors.

**3\.** Processor _P_~i~ then locally computes the join of _r_~i~ with all of _s_, using any join technique.

The asymmetric fragment-and-replicate scheme appears in Figure 18.3a. If _r_ is already stored by partitioning, there is no need to partition it further in step 1. All that is required is to replicate _s_ across all processors.

The general case of **fragment-and-replicate join** appears in Figure 18.3b; it works this way: The system partitions relation _r_ into _n_ partitions, r~0~, r~1~, . . . , r~n−1~, and partitions _s_ into _m_ partitions, s~0~, s~1~, . . . , s~m−1~. As before, any partitioning technique may be used on _r_ and on _s_. The values of _m_ and _n_ do not need to be equal, but they must be chosen so that there are at least _m_ ∗ _n_ processors. Asymmetric fragment and replicate is simply a special case of general fragment and replicate, where _m_ \= 1. Fragment and replicate reduces the sizes of the relations at each processor, compared to asymmetric fragment and replicate.  

![Alt text](figure-18.3.png)


Let the processors be P~0~,~0~, P~0~,~1~, . . . , P~0~,~m−1~, P~1~,~0~, . . . , P~n−1~,~m−1~. Processor _P_~i~, ~j~_ computes the join of _ri_ with _s j_ . Each processor must get those tuples in the partitions on which it works. To accomplish this, the system replicates _ri_ to processors ~P~i~,~0~, P~i~,~1~, . . . , P~i~,~m−1~ (which form a row in Figure 18.3b), and replicates _si_ to processors P~0~,~i~ , P~1~,~i~ , . . . , P~n−1~,~i~ (which form a column in Figure 18.3b). Any join technique can be used at each processor _P_~i~, ~j~_ .

Fragment and replicate works with any join condition, since every tuple in _r_ can be tested with every tuple in _s_. Thus, it can be used where partitioning cannot be.

Fragment and replicate usually has a higher cost than partitioning when both relations are of roughly the same size, since at least one of the relations has to be replicated. However, if one of the relations—say, _s_ —is small, it may be cheaper to replicate _s_ across all processors, rather than to repartition _r_ and _s_ on the join attributes. In such a case, asymmetric fragment and replicate is preferable, even though partitioning could be used.

#### 18.5.2.3 Partitioned Parallel Hash Join**

The partitioned hash join of Section 12.5.5 can be parallelized. Suppose that we have _n_ processors, P~0~, ~P~1, . . . , P~n−1~, and two relations _r_ and _s_, such that the relations _r_ and _s_ are partitioned across multiple disks. Recall from Section 12.5.5 that the smaller relation is chosen as the build relation. If the size of _s_ is less than that of _r_ , the parallel hash-join algorithm proceeds this way:

**1\.** Choose a hash function—say, h~1~ that takes the join attribute value of each tuple in _r_ and _s_ and maps the tuple to one of the _n_ processors. Let _r_~i~ denote the tuples of relation _r_ that are mapped to processor P~i~ ; similarly, let s~i~ denote the tuples of relation _s_ that are mapped to processor P~i~ . Each processor P~i~ reads the tuples of _s_ that are on its disk D~i~ and sends each tuple to the appropriate processor on the basis of hash function h~1~.

**2\.** As the destination processor P~i~ receives the tuples of s~i~ , it further partitions them by another hash function, h~2~, which the processor uses to compute the hash join locally. The partitioning at this stage is exactly the same as in the partitioning phase of the sequential hash-join algorithm. Each processor P~i~ executes this step independently from the other processors.

**3\.** Once the tuples of _s_ have been distributed, the system redistributes the larger relation _r_ across the _n_ processors by the hash function h~1~, in the same way as before. As it receives each tuple, the destination processor repartitions it by the function h~2~, just as the probe relation is partitioned in the sequential hash-join algorithm.

**4\.** Each processor P~i~ executes the build and probe phases of the hash-join algorithm on the local partitions r~i~ and s~i~ of _r_ and _s_ to produce a partition of the final result of the hash join.

The hash join at each processor is independent of that at other processors, and receiving the tuples of r~i~ and s~i~ is similar to reading them from disk. Therefore, any of the optimizations of the hash join described in Chapter 12 can be applied as well to the parallel case. In particular, we can use the hybrid hash-join algorithm to cache some of the incoming tuples in memory, and thus avoid the costs of writing them and of reading them back in.

#### 18.5.2.4 Parallel Nested-Loop Join

To illustrate the use of fragment-and-replicate–based parallelization, consider the case where the relation _s_ is much smaller than relation _r_ . Suppose that relation _r_ is stored by partitioning; the attribute on which it is partitioned does not matter. Suppose too that there is an index on a join attribute of relation _r_ at each of the partitions of relation _r_ .

We use asymmetric fragment and replicate, with relation _s_ being replicated and with the existing partitioning of relation _r_ . Each processor _P~j~_ where a partition of relation _s_ is stored reads the tuples of relation _s_ stored in _D~j~_ , and replicates the tuples to every other processor _P~i~_ . At the end of this phase, relation _s_ is replicated at all sites that store tuples of relation _r_ .

Now, each processor _P_~i~ performs an indexed nested-loop join of relation _s_ with the _i_th partition of relation _r_ . We can overlap the indexed nested-loop join with the distribution of tuples of relation _s_, to reduce the costs of writing the tuples of relation _s_ to disk, and of reading them back. However, the replication of relation _s_ must be synchronized with the join so that there is enough space in the in-memory buffers at each processor _P~i~_ to hold the tuples of relation _s_ that have been received but that have not yet been used in the join.

### Other Relational Operations

The evaluation of other relational operations also can be parallelized:

- **Selection**. Let the selection be σ~θ~(_r_ ). Consider first the case where θ is of the form _a~i~_ \= _v_, where _ai_ is an attribute and _v_ is a value. If the relation _r_ is partitioned on _a~i~_ , the selection proceeds at a single processor. If θ is of the form _l_ ≤ _a~i~_ ≤ _u_—that is, θ is a range selection—and the relation has been range-partitioned on _a~i~_ , then the selection proceeds at each processor whose partition overlaps with the specified range of values. In all other cases, the selection proceeds in parallel at all the processors.

- **Duplicate elimination**. Duplicates can be eliminated by sorting; either of the parallel sort techniques can be used, optimized to eliminate duplicates as soon as they appear during sorting. We can also parallelize duplicate elimination by partitioning the tuples (by either range or hash partitioning) and eliminating duplicates locally at each processor.

- **Projection**. Projection without duplicate elimination can be performed as tuples are read in from disk in parallel. If duplicates are to be eliminated, either of the techniques just described can be used.

- **Aggregation**. Consider an aggregation operation. We can parallelize the operation by partitioning the relation on the grouping attributes, and then computing the aggregate values locally at each processor. Either hash partitioning or range partitioning can be used. If the relation is already partitioned on the grouping attributes, the first step can be skipped.

We can reduce the cost of transferring tuples during partitioning by partly computing aggregate values before partitioning, at least for the commonly used aggregate functions. Consider an aggregation operation on a relation _r_ , using the **sum** aggregate function on attribute _B_, with grouping on attribute _A_. The system can perform the operation at each processor _P~i~_ on those _r_ tuples stored on disk _D~i~_ . This computation results in tuples with partial sums at each processor; there is one tuple at _P~i~_ for each value for attribute _A_ present in _r_ tuples stored on _D~i~_ . The system partitions the result of the local aggregation on the grouping attribute _A_, and performs the aggregation again (on tuples with the partial sums) at each processor _P~i~_ to get the final result.

As a result of this optimization, fewer tuples need to be sent to other processors during partitioning. This idea can be extended easily to the **min** and **max** aggregate functions. Extensions to the **count** and **avg** aggregate functions are left for you to do in Exercise 18.12.  


The parallelization of other operations is covered in several of the exercises.

### Cost of Parallel Evaluation of Operations

We achieve parallelism by partitioning the I/O among multiple disks, and partitioning the CPU work among multiple processors. If such a split is achieved without any overhead, and if there is no skew in the splitting of work, a parallel operation using _n_ processors will take 1/n times as long as the same operation on a single processor. We already know how to estimate the cost of an operation such as a join or a selection. The time cost of parallel processing would then be 1/n of the time cost of sequential processing of the operation.

We must also account for the following costs:

- **Start-up costs** for initiating the operation at multiple processors.

- **Skew** in the distribution of work among the processors, with some processors getting a larger number of tuples than others.

- **Contention for resources**—such as memory, disk, and the communication network—resulting in delays.

- **Cost of assembling** the final result by transmitting partial results from each processor.

The time taken by a parallel operation can be estimated as:

_T_~part~ + _T_~asm~ + max(_T_~0~_, T_~1~, . . . , _T~n−1~)


where _T_part is the time for partitioning the relations, _T_asm is the time for assembling the results, and _Ti_ is the time taken for the operation at processor _Pi_ . Assuming that the tuples are distributed without any skew, the number of tuples sent to each processor can be estimated as 1_/n_ of the total number of tuples. Ignoring contention, the cost _Ti_ of the operations at each processor _Pi_ can then be estimated by the techniques in Chapter 12.

The preceding estimate will be an optimistic estimate, since skew is common. Even though breaking down a single query into a number of parallel steps reduces the size of the average step, it is the time for processing the single slowest step that determines the time taken for processing the query as a whole. A partitioned parallel evaluation, for instance, is only as fast as the slowest of the parallel executions. Thus, any skew in the distribution of the work across processors greatly affects performance.

The problem of skew in partitioning is closely related to the problem of partition overflow in sequential hash joins (Chapter 12). We can use overflow resolution and avoidance techniques developed for hash joins to handle skew when hash partitioning is used. We can use balanced range partitioning and virtual processor partitioning to minimize skew due to range partitioning, as in Section 18.2.3.  


