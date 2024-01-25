---
title: 'Other Operations'
weight: 6
---

# Other Operations

Other relational operations and extended relational operations—such as dupli- cate elimination, projection, set operations, outer join, and aggregation—can be implemented as outlined in Sections 12.6.1 through 12.6.5.

## Duplicate Elimination

We can implement duplicate elimination easily by sorting. Identical tuples will appear adjacent to each other as a result of sorting, and all but one copy can be removed. With external sort–merge, duplicates found while a run is being created can be removed before the run is written to disk, thereby reducing the number of block transfers. The remaining duplicates can be eliminated during merging, and the final sorted run has no duplicates. The worst-case cost estimate for duplicate elimination is the same as the worst-case cost estimate for sorting of the relation.

We can also implement duplicate elimination by hashing, as in the hash-join algorithm. First, the relation is partitioned on the basis of a hash function on the whole tuple. Then, each partition is read in, and an in-memory hash index is constructed. While constructing the hash index, a tuple is inserted only if it is not already present. Otherwise, the tuple is discarded. After all tuples in the partition have been processed, the tuples in the hash index are written to the result. The cost estimate is the same as that for the cost of processing (partitioning and reading each partition) of the build relation in a hash join.

Because of the relatively high cost of duplicate elimination, SQL requires an explicit request by the user to remove duplicates; otherwise, the duplicates are retained.

## Projection

We can implement projection easily by performing projection on each tuple, which gives a relation that could have duplicate records, and then removing duplicate records. Duplicates can be eliminated by the methods described in Section 12.6.1. If the attributes in the projection list include a key of the relation, no duplicates will exist; hence, duplicate elimination is not required. Generalized projection can be implemented in the same way as projection.

## Set Operation

We can implement the _union_, _intersection_, and _set-difference_ operations by first sorting both relations, and then scanning once through each of the sorted relations to produce the result. In _r_ ∪ _s_, when a concurrent scan of both relations reveals the same tuple in both files, only one of the tuples is retained. The result of _r_ ∩ _s_ will contain only those tuples that appear in both relations. We implement _set difference_, _r_ − _s_, similarly, by retaining tuples in _r_ only if they are absent in _s_.

For all these operations, only one scan of the two sorted input relations is required, so the cost is _br_ \+ _bs_ block transfers if the relations are sorted in the same order. Assuming a worst case of one block buffer for each relation, a total of _br_ \+ _bs_ disk seeks would be required in addition to _br_ \+ _bs_ block transfers. The number of seeks can be reduced by allocating extra buffer blocks.

If the relations are not sorted initially, the cost of sorting has to be included. Any sort order can be used in evaluation of set operations, provided that both inputs have that same sort order.

Hashing provides another way to implement these set operations. The first step in each case is to partition the two relations by the same hash function, and thereby create the partitions r~0~,r~1~,...,r~nh~ and s~0~,s~1~,...,s~nh~ . Depending on the operation, the system then takes these steps on each partition _i_ \= 0,1, . . . , nh:

- _r_ ∪ _s_

    **1\.** Build an in-memory hash index on _ri_ .  

    **2\.** Add the tuples in _si_ to the hash index only if they are not already present.

    **3\.** Add the tuples in the hash index to the result.

- _r_ ∩ _s_

    **1\.** Build an in-memory hash index on _ri_ .

    **2\.** For each tuple in _si_ , probe the hash index and output the tuple to the result only if it is already present in the hash index.

- _r_ − _s_

    **1\.** Build an in-memory hash index on _ri_ .

    **2\.** For each tuple in _si_ , probe the hash index, and, if the tuple is present in the hash index, delete it from the hash index.

    **3\.** Add the tuples remaining in the hash index to the result.

## Outer Join

Recall the _outer-join operations_ described in Section 4.1.2. For example, the natural left outer join _takes  student_ contains the join of _takes_ and _student_, and, in addition, for each _takes_ tuple _t_ that has no matching tuple in _student_ (that is, where _ID_ is not in _student_), the following tuple _t_ 1 is added to the result. For all attributes in the schema of _takes_, tuple _t_ 1 has the same values as tuple _t_ . The remaining attributes (from the schema of _student_) of tuple _t_ 1 contain the value null.

We can implement the outer-join operations by using one of two strategies:

**1\.** Compute the corresponding join, and then add further tuples to the join result to get the outer-join result. Consider the left outer-join operation and two relations: _r_ (_R_) and _s_(_S_). To evaluate _r _ _s_ , we first compute _r _ _s_, and save that result as temporary relation _q_ 1\. Next, we compute _r_ − _R_(_q_ 1) to obtain those tuples in _r_ that do not participate in the theta join. We can use any of the algorithms for computing the joins, projection, and set difference described earlier to compute the outer joins. We pad each of these tuples with null values for attributes from _s_, and add it to _q_ 1 to get the result of the outer join.

The right outer-join operation _r_  _s_ is equivalent to _s _ _r_ , and can therefore be implemented in a symmetric fashion to the left outer join. We can implement the full outer-join operation _r_  _s_ by computing the join _r  s_, and then adding the extra tuples of both the left and right outer-join operations, as before.

**2\.** Modify the join algorithms. It is easy to extend the nested-loop join algo- rithms to compute the left outer join: Tuples in the outer relation that do not match any tuple in the inner relation are written to the output after being padded with null values. However, it is hard to extend the nested-loop join to compute the full outer join.  

Natural outer joins and outer joins with an equi-join condition can be computed by extensions of the merge-join and hash-join algorithms. Merge join can be extended to compute the full outer join as follows: When the merge of the two relations is being done, tuples in either relation that do not match any tuple in the other relation can be padded with nulls and written to the output. Similarly, we can extend merge join to compute the left and right outer joins by writing out nonmatching tuples (padded with nulls) from only one of the relations. Since the relations are sorted, it is easy to detect whether or not a tuple matches any tuples from the other relation. For example, when a merge join of _takes_ and _student_ is done, the tuples are read in sorted order of _ID_, and it is easy to check, for each tuple, whether there is a matching tuple in the other.

The cost estimates for implementing outer joins using the merge-join algorithm are the same as are those for the corresponding join. The only difference lies in size of the result, and therefore in the block transfers for writing it out, which we did not count in our earlier cost estimates.

The extension of the hash-join algorithm to compute outer joins is left for you to do as an exercise (Exercise 12.15).

## Aggregation

Recall the aggregation function (operator), discussed in Section 3.7. For example, the function

**select** _dept name_, 
**avg** (_salary_) **from** _instructor_ 
**group by** _dept name_;

computes the average salary in each university department. The aggregation operation can be implemented in the same way as duplicate

elimination. We use either sorting or hashing, just as we did for duplicate elimina- tion, but based on the grouping attributes (_branch name_ in the preceding example). However, instead of eliminating tuples with the same value for the grouping at- tribute, we gather them into groups, and apply the aggregation operations on each group to get the result.

The cost estimate for implementing the aggregation operation is the same as the cost of duplicate elimination, for aggregate functions such as **min**, **max**, **sum**, **count**, and **avg**.

Instead of gathering all the tuples in a group and then applying the aggre- gation operations, we can implement the aggregation operations **sum**, **min**, **max**, **count**, and **avg** on the fly as the groups are being constructed. For the case of **sum**, **min**, and **max**, when two tuples in the same group are found, the system replaces them by a single tuple containing the **sum**, **min**, or **max**, respectively, of the columns being aggregated. For the **count** operation, it maintains a running count for each group for which a tuple has been found. Finally, we implement the **avg** operation by computing the sum and the count values on the fly, and finally dividing the sum by the count to get the average.

If all tuples of the result fit in memory, both the sort-based and the hash-based implementations do not need to write any tuples to disk. As the tuples are read in, they can be inserted in a sorted tree structure or in a hash index. When we use on-the-fly aggregation techniques, only one tuple needs to be stored for each of the groups. Hence, the sorted tree structure or hash index fits in memory, and the aggregation can be processed with just _br_ block transfers (and 1 seek) instead of the 3_br_ transfers (and a worst case of up to 2_br_ seeks) that would be required otherwise.

