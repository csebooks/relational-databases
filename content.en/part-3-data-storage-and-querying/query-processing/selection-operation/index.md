---
title: 'Selection Operation'
weight: 3
---

# Selection Operation

In query processing, the **file scan** is the lowest-level operator to access data. File scans are search algorithms that locate and retrieve records that fulfill a selection condition. In relational systems, a file scan allows an entire relation to be read in those cases where the relation is stored in a single, dedicated file.

## Selections Using File Scans and Indices

Consider a selection operation on a relation whose tuples are stored together in one file. The most straightforward way of performing a selection is as follows:

- **A1** (**linear search**). In a linear search, the system scans each file block and tests all records to see whether they satisfy the selection condition. An initial seek is required to access the first block of the file. In case blocks of the file are not stored contiguously, extra seeks may be required, but we ignore this effect for simplicity.

Although it may be slower than other algorithms for implementing selec- tion, the linear-search algorithm can be applied to any file, regardless of the ordering of the file, or the availability of indices, or the nature of the selection operation. The other algorithms that we shall study are not applicable in all cases, but when applicable they are generally faster than linear search.

Cost estimates for linear scan, as well as for other selection algorithms, are shown in Figure 12.3. In the figure, we use _hi_ to represent the height of the B+- tree. Real-life optimizers usually assume that the root of the tree is present in the in-memory buffer since it is frequently accessed. Some optimizers even assume that all but the leaf level of the tree is present in memory, since they are accessed relatively frequently, and usually less than 1 percent of the nodes of a B+-tree are nonleaf nodes. The cost formulae can be modified appropriately.

Index structures are referred to as **access paths**, since they provide a path through which data can be located and accessed. In Chapter 11, we pointed out that it is efficient to read the records of a file in an order corresponding closely to physical order. Recall that a _primary index_ (also referred to as a _clustering index_) is an index that allows the records of a file to be read in an order that corresponds to the physical order in the file. An index that is not a primary index is called a _secondary index_.

Search algorithms that use an index are referred to as **index scans**. We use the selection predicate to guide us in the choice of the index to use in processing the query. Search algorithms that use an index are:

- **A2** (**primary index, equality on key**). For an equality comparison on a key attribute with a primary index, we can use the index to retrieve a single record that satisfies the corresponding equality condition. Cost estimates are shown in Figure 12.3.

- **A3** (**primary index, equality on nonkey**). We can retrieve multiple records by using a primary index when the selection condition specifies an equality comparison on a nonkey attribute, _A_. The only difference from the previous case is that multiple records may need to be fetched. However, the records must be stored consecutively in the file since the file is sorted on the search key. Cost estimates are shown in Figure 12.3.

- **A4** (**secondary index, equality**). Selections specifying an equality condition can use a secondary index. This strategy can retrieve a single record if the equality condition is on a key; multiple records may be retrieved if the index- ing field is not a key.

In the first case, only one record is retrieved. The time cost in this case is the same as that for a primary index (case A2).

In the second case, each record may be resident on a different block, which may result in one I/O operation per retrieved record, with each I/O operation requiring a seek and a block transfer. The worst-case time cost in this case is  

|   |Algorithm| Cost |Reason|
|-|---------|----------|------|
|A1| Linear Search |tS + br ∗ tT |One initial seek plus br block transfers,where br denotes the number of blocks in the file.|
|A1|Linear Search, Equality on Key Average case |tS + (br /2) ∗ tT| Since at most one record satisfies con dition, scan can be terminated as soon as the required record is found. In the worst case, br blocks transfers are still required.|
|A2| Primary B+-tree Index, Equality on Key |(hi + 1) ∗ (tT + tS) |(Where hi denotes the height of the in dex.) Index lookup traverses the height of the tree plus one I/O to fetch the record; each of these I/O operations re quires a seek and a block transfer.|
|A3|Primary B+-tree Index, Equality on Nonkey |hi ∗ (tT + tS) + b ∗ tT |One seek for each level of the tree, one seek for the first block. Here b is the number of blocks containing records with the specified search key, all of which are read. These blocks are leaf blocks assumed to be stored sequentially (since it is a primary index) and don’t require additional seeks.| 
|A4| Secondary B+-tree Index, Equality on Key |(hi + 1) ∗ (tT + tS)| This case is similar to primary index.|
|A4| Secondary B+-tree Index, Equality on Nonkey |(hi + n) ∗ (tT + tS)| (Where n is the number of records fetched.) Here, cost of index traversal is the same as for A3, but each record may be on a different block, requiring a seek per record. Cost is potentially very high if n is large. 
|A5| Primary B+-tree Index, Comparison |hi ∗ (tT + tS) + b ∗ tT| Identical to the case of A3, equality on nonkey.|
|A6| Secondary B+-tree Index, Comparison |(hi + n) ∗ (tT + tS)| Identical to the case of A4, equality on nonkey.|


**Figure 12.3** Cost estimates for selection algorithms.

(_hi_ \+ _n_) ∗ (_tS_ \+ _tT_ ), where _n_ is the number of records fetched, if each record is in a different disk block, and the block fetches are randomly ordered. The worst-case cost could become even worse than that of linear search if a large number of records are retrieved.  

If the in-memory buffer is large, the block containing the record may already be in the buffer. It is possible to construct an estimate of the _average_ or _expected_ cost of the selection by taking into account the probability of the block containing the record already being in the buffer. For large buffers, that estimate will be much less than the worst-case estimate.

In certain algorithms, including A2, the use of a B+-tree file organization can save one access since records are stored at the leaf-level of the tree.

As described in Section 11.4.2, when records are stored in a B+-tree file organi- zation or other file organizations that may require relocation of records, secondary indices usually do not store pointers to the records.3 Instead, secondary indices store the values of the attributes used as the search key in a B+-tree file organiza- tion. Accessing a record through such a secondary index is then more expensive: First the secondary index is searched to find the primary index search-key val- ues, then the primary index is looked up to find the records. The cost formulae described for secondary indices have to be modified appropriately if such indices are used.

## Selections Involving Comparisons

Consider a selection of the form ⌈_A_≤_v_(_r_ ). We can implement the selection either by using linear search or by using indices in one of the following ways:

- **A5** (**primary index, comparison**). A primary ordered index (for example, a primary B+-tree index) can be used when the selection condition is a com- parison. For comparison conditions of the form _A > v_ or _A_ ≥ _v_, a primary index on _A_ can be used to direct the retrieval of tuples, as follows: For _A_ ≥ _v_, we look up the value _v_ in the index to find the first tuple in the file that has a value of _A_ \= _v_. A file scan starting from that tuple up to the end of the file returns all tuples that satisfy the condition. For _A > v_, the file scan starts with the first tuple such that _A > v_. The cost estimate for this case is identical to that for case A3.

For comparisons of the form _A < v_ or _A_ ≤ _v_, an index lookup is not required. For _A < v_, we use a simple file scan starting from the beginning of the file, and continuing up to (but not including) the first tuple with attribute _A_ \= _v_. The case of _A_ ≤ _v_ is similar, except that the scan continues up to (but not including) the first tuple with attribute _A > v_. In either case, the index is not useful.

- **A6** (**secondary index, comparison**). We can use a secondary ordered index to guide retrieval for comparison conditions involving _<,_ ≤_,_ ≥, or _\>_. The lowest-level index blocks are scanned, either from the smallest value up to _v_

(for _<_ and ≤), or from _v_ up to the maximum value (for _\>_ and ≥).

The secondary index provides pointers to the records, but to get the actual records we have to fetch the records by using the pointers. This step may require an I/O operation for each record fetched, since consecutive records may be on different disk blocks; as before, each I/O operation requires a disk seek and a block transfer. If the number of retrieved records is large, using the secondary index may be even more expensive than using linear search. Therefore the secondary index should be used only if very few records are selected.

## Implementation of Complex Selections

So far, we have considered only simple selection conditions of the form _A op B_, where _op_ is an equality or comparison operation. We now consider more complex selection predicates.

- **Conjunction:** A _conjunctive selection_ is a selection of the form:
σ~θ1^θ2^...^θn~(r)

- **Disjunction:** A _disjunctive selection_ is a selection of the form:
σ~θ1^θ2^...^θn~(r)

A disjunctive condition is satisfied by the union of all records satisfying the individual, simple conditions θ _i_ .

- **Negation:** The result of a selection σ¬θ(_r_ ) is the set of tuples of _r_ for which the condition θ evaluates to false. In the absence of nulls, this set is simply the set of tuples in _r_ that are not in σθ(_r_ ).

We can implement a selection operation involving either a conjunction or a disjunction of simple conditions by using one of the following algorithms:

- **A7** (**conjunctive selection using one index**). We first determine whether an access path is available for an attribute in one of the simple conditions. If one is, one of the selection algorithms A2 through A6 can retrieve records satisfying that condition. We complete the operation by testing, in the memory buffer, whether or not each retrieved record satisfies the remaining simple conditions.

To reduce the cost, we choose a  θ _i_ and one of algorithms A1 through A6 for which the combination results in the least cost for σ~θ1i~+ (_r_ ). The cost of algorithm A7 is given by the cost of the chosen algorithm.

- **A8** (**conjunctive selection using composite index**). An appropriate _composite index_ (that is, an index on multiple attributes) may be available for some conjunctive selections. If the selection specifies an equality condition on two or more attributes, and a composite index exists on these combined attribute fields, then the index can be searched directly. The type of index determines which of algorithms A2, A3, or A4 will be used.

- **A9** (**conjunctive selection by intersection of identifiers).** Another alterna- tive for implementing conjunctive selection operations involves the use of record pointers or record identifiers. This algorithm requires indices with record pointers, on the fields involved in the individual conditions. The al- gorithm scans each index for pointers to tuples that satisfy an individual condition. The intersection of all the retrieved pointers is the set of pointers to tuples that satisfy the conjunctive condition. The algorithm then uses the pointers to retrieve the actual records. If indices are not available on all the individual conditions, then the algorithm tests the retrieved records against the remaining conditions.

The cost of algorithm **A9** is the sum of the costs of the individual index scans, plus the cost of retrieving the records in the intersection of the retrieved lists of pointers. This cost can be reduced by sorting the list of pointers and retrieving records in the sorted order. Thereby, (1) all pointers to records in a block come together, hence all selected records in the block can be retrieved using a single I/O operation, and (2) blocks are read in sorted order, minimizing disk-arm movement. Section 12.4 describes sorting algorithms.

- **A10** (**disjunctive selection by union of identifiers).** If access paths are avail- able on all the conditions of a disjunctive selection, each index is scanned for pointers to tuples that satisfy the individual condition. The union of all the retrieved pointers yields the set of pointers to all tuples that satisfy the disjunctive condition. We then use the pointers to retrieve the actual records.

However, if even one of the conditions does not have an access path, we have to perform a linear scan of the relation to find tuples that satisfy the condition. Therefore, if there is even one such condition in the disjunct, the most efficient access method is a linear scan, with the disjunctive condition tested on each tuple during the scan.

The implementation of selections with negation conditions is left to you as an exercise (Practice Exercise 12.6).

