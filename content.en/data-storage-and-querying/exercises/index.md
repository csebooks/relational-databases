---
title: Exercises
weight: 24
---

Exercises## Exercises

**11.15** When is it preferable to use a dense index rather than a sparse index? Explain your answer.

**11.16** What is the difference between a clustering index and a secondary index?

**11.17** For each B+-tree of Practice Exercise 11.3, show the steps involved in the following queries:

a. Find records with a search-key value of 11.

b. Find records with a search-key value between 7 and 17, inclusive.

**11.18** The solution presented in Section 11.3.4 to deal with nonunique search keys added an extra attribute to the search key. What effect could this change have on the height of the B+-tree?

**11.19** Explain the distinction between closed and open hashing. Discuss the relative merits of each technique in database applications.  

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

## Exercises

**12.10** Suppose you need to sort a relation of 40 gigabytes, with 4 kilobyte blocks, using a memory size of 40 megabytes. Suppose the cost of a seek is 5 milliseconds, while the disk transfer rate is 40 megabytes per second.

a. Find the cost of sorting the relation, in seconds, with _bb_ \= 1 and with _bb_ \= 100.

b. In each case, how many merge passes are required?

c. Suppose a flash storage device is used instead of a disk, and it has a seek time of 1 microsecond, and a transfer rate of 40 megabytes per second. Recompute the cost of sorting the relation, in seconds, with _bb_ \= 1 and with _bb_ \= 100, in this setting.

**12.11** Consider the following extended relational-algebra operators. Describe how to implement each operation using sorting, and using hashing.

![Alt text](image-61.png)

**12.12** Why is it not desirable to force users to make an explicit choice of a query- processing strategy? Are there cases in which it _is_ desirable for users to be aware of the costs of competing query-processing strategies? Explain your answer.

**12.13** Design a variant of the hybrid merge-join algorithm for the case where both relations are not physically sorted, but both have a sorted secondary index on the join attributes.

**12.14** Estimate the number of block transfers and seeks required by your solu- tion to Exercise 12.13 for _r_1 _ r_2, where _r_1 and _r_2 are as defined in Practice Exercise 12.3.  

**12.15** The hash-join algorithm as described in Section 12.5.5 computes the nat- ural join of two relations. Describe how to extend the hash-join algorithm to compute the natural left outer join, the natural right outer join and the natural full outer join. (Hint: Keep extra information with each tuple in the hash index, to detect whether any tuple in the probe relation matches the tuple in the hash index.) Try out your algorithm on the _takes_ and _student_ relations.

**12.16** Pipelining is used to avoid writing intermediate results to disk. Suppose you need to sort relation _r_ using sort–merge and merge-join the result with an already sorted relation _s_.

a. Describe how the output of the sort of _r_ can be pipelined to the merge join without being written back to disk.

b. The same idea is applicable even if both inputs to the merge join are the outputs of sort–merge operations. However, the available memory has to be shared between the two merge operations (the merge-join algorithm itself needs very little memory). What is the effect of having to share memory on the cost of each sort–merge operation?

**12.17** Write pseudocode for an iterator that implements a version of the sort –merge algorithm where the result of the final merge is pipelined to its consumers. Your pseudocode must define the standard iterator functions _open_(), _next_(), and _close_(). Show what state information the iterator must maintain between calls.

**12.18** Suppose you have to compute _AGsum_(_C_)(_r_ ) as well as _A,BGsum_(_C_)(_r_ ). Describe how to compute these together using a single sorting of _r_ .

