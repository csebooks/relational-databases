---
title: 11.11 Summary
weight: 22
---

11.11 Summary## 11.11 Summary

- Many queries reference only a small proportion of the records in a file. To reduce the overhead in searching for these records, we can construct _indices_ for the files that store the database.

- Index-sequential files are one of the oldest index schemes used in database systems. To permit fast retrieval of records in search-key order, records are stored sequentially, and out-of-order records are chained together. To allow fast random access, we use an index structure.

- There are two types of indices that we can use: dense indices and sparse indices. Dense indices contain entries for every search-key value, whereas sparse indices contain entries only for some search-key values. 

- If the sort order of a search key matches the sort order of a relation, an index on the search key is called a _clustering index_. The other indices are called _nonclustering_ or _secondary indices_. Secondary indices improve the performance of queries that use search keys other than the search key of the clustering index. However, they impose an overhead on modification of the database.

- The primary disadvantage of the index-sequential file organization is that performance degrades as the file grows. To overcome this deficiency, we can use a B+-_tree index_.

- A B+-tree index takes the form of a _balanced_ tree, in which every path from the root of the tree to a leaf of the tree is of the same length. The height of a B+-tree is proportional to the logarithm to the base _N_ of the number of records in the relation, where each nonleaf node stores _N_ pointers; the value of _N_ is often around 50 or 100. B+-trees are much shorter than other balanced binary-tree structures such as AVL trees, and therefore require fewer disk accesses to locate records.

- Lookup on B+-trees is straightforward and efficient. Insertion and deletion, however, are somewhat more complicated, but still efficient. The number of operations required for lookup, insertion, and deletion on B+-trees is propor- tional to the logarithm to the base _N_ of the number of records in the relation, where each nonleaf node stores _N_ pointers.

- We can use B+-trees for indexing a file containing records, as well as to organize records into a file.

- B-tree indices are similar to B+-tree indices. The primary advantage of a B-tree is that the B-tree eliminates the redundant storage of search-key values. The major disadvantages are overall complexity and reduced fanout for a given node size. System designers almost universally prefer B+-tree indices over B-tree indices in practice.

- Sequential file organizations require an index structure to locate data. File organizations based on hashing, by contrast, allow us to find the address of a data item directly by computing a function on the search-key value of the desired record. Since we do not know at design time precisely which search- key values will be stored in the file, a good hash function to choose is one that assigns search-key values to buckets such that the distribution is both uniform and random.

- _Static hashing_ uses hash functions in which the set of bucket addresses is fixed. Such hash functions cannot easily accommodate databases that grow significantly larger over time. There are several _dynamic hashing techniques_ that allow the hash function to be modified. One example is _extendable hashing_, which copes with changes in database size by splitting and coalescing buckets as the database grows and shrinks.  

- We can also use hashing to create secondary indices; such indices are called _hash indices_. For notational convenience, we assume hash file organizations have an implicit hash index on the search key used for hashing.

- Ordered indices such as B+-trees and hash indices can be used for selec- tions based on equality conditions involving single attributes. When multi- ple attributes are involved in a selection condition, we can intersect record identifiers retrieved from multiple indices.

- Bitmap indices provide a very compact representation for indexing attributes with very few distinct values. Intersection operations are extremely fast on bitmaps, making them ideal for supporting queries on multiple attributes.

**Review Terms**

- Access types 
- Access time 
- Insertion time 
- Deletion time 
- Space overhead 
- Ordered index 
- Clustering index 
- Primary index 
- Nonclustering index 
- Secondary index 
- Index-sequential file 
- Index entry/record 
- Dense index 
- Sparse index 
- Multilevel index 
- Composite key 
- Sequential scan 
- B+-tree index 
- Leaf node 
- Nonleaf node 
- Balanced tree 
- Range query 
- Node split 
- Node coalesce

- Nonunique search key 
- B+-tree file organization 
- Bulk load 
- Bottom-up B+-tree construction 
- B-tree index 
- Static hashing 
- Hash file organization 
- Hash index 
- Bucket 
- Hash function 
- Bucket overflow 
- Skew 
- Closed hashing 
- Dynamic hashing 
- Extendable hashing 
- Multiple-key access 
- Indices on multiple keys 
- Bitmap index 
- Bitmap operations
    ◦ Intersection
    ◦ Union
    ◦ Complement
    ◦ Existence bitmap  

