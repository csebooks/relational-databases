---
title: 'Dynamic Hashing'
weight: 7
---

# Dynamic Hashing

As we have seen, the need to fix the set _B_ of bucket addresses presents a serious problem with the static hashing technique of the previous section. Most databases grow larger over time. If we are to use static hashing for such a database, we have three classes of options:

**1\.** Choose a hash function based on the current file size. This option will result in performance degradation as the database grows.

**2\.** Choose a hash function based on the anticipated size of the file at some point in the future. Although performance degradation is avoided, a significant amount of space may be wasted initially.

**3\.** Periodically reorganize the hash structure in response to file growth. Such a reorganization involves choosing a new hash function, recomputing the hash function on every record in the file, and generating new bucket as- signments. This reorganization is a massive, time-consuming operation. Furthermore, it is necessary to forbid access to the file during reorganiza- tion.

Several **dynamic hashing** techniques allow the hash function to be modified dynamically to accommodate the growth or shrinkage of the database. In this section we describe one form of dynamic hashing, called **extendable hashing**. The bibliographical notes provide references to other forms of dynamic hashing.

## Data Structure

Extendable hashing copes with changes in database size by splitting and coalesc- ing buckets as the database grows and shrinks. As a result, space efficiency is retained. Moreover, since the reorganization is performed on only one bucket at a time, the resulting performance overhead is acceptably low.

With extendable hashing, we choose a hash function _h_ with the desirable properties of uniformity and randomness. However, this hash function generates values over a relatively large range—namely, _b_\-bit binary integers. A typical value for _b_ is 32.  

![Alt text](image-37.png)

We do not create a bucket for each hash value. Indeed, 232 is over 4 billion, and that many buckets is unreasonable for all but the largest databases. Instead, we create buckets on demand, as records are inserted into the file. We do not use the entire _b_ bits of the hash value initially. At any point, we use _i_ bits, where 0 ≤ _i_ ≤ _b_. These _i_ bits are used as an offset into an additional table of bucket addresses. The value of _i_ grows and shrinks with the size of the database.

Figure 11.26 shows a general extendable hash structure. The _i_ appearing above the bucket address table in the figure indicates that _i_ bits of the hash value _h_(_K_ ) are required to determine the correct bucket for _K_. This number will, of course, change as the file grows. Although _i_ bits are required to find the correct entry in the bucket address table, several consecutive table entries may point to the same bucket. All such entries will have a common hash prefix, but the length of this prefix may be less than _i_. Therefore, we associate with each bucket an integer giving the length of the common hash prefix. In Figure 11.26 the integer associated with bucket _j_ is shown as _i j_ . The number of bucket-address-table entries that point to bucket _j_ is

2(_i_ − _i j_ )

## Queries and Updates

We now see how to perform lookup, insertion, and deletion on an extendable hash structure. 

To locate the bucket containing search-key value _Kl_ , the system takes the first _i_ high-order bits of _h_(_Kl_), looks at the corresponding table entry for this bit string, and follows the bucket pointer in the table entry.

To insert a record with search-key value _Kl_ , the system follows the same procedure for lookup as before, ending up in some bucket—say, _j_. If there is room in the bucket, the system inserts the record in the bucket. If, on the other hand, the bucket is full, it must split the bucket and redistribute the current records, plus the new one. To split the bucket, the system must first determine from the hash value whether it needs to increase the number of bits that it uses.

- If _i_ \= _i j_ , only one entry in the bucket address table points to bucket _j_. Therefore, the system needs to increase the size of the bucket address table so that it can include pointers to the two buckets that result from splitting bucket _j_. It does so by considering an additional bit of the hash value. It increments the value of _i_ by 1, thus doubling the size of the bucket address table. It replaces each entry by two entries, both of which contain the same pointer as the original entry. Now two entries in the bucket address table point to bucket _j_. The system allocates a new bucket (bucket _z_), and sets the second entry to point to the new bucket. It sets _i j_ and _iz_ to _i_. Next, it rehashes each record in bucket _j_ and, depending on the first _i_ bits (remember the system has added 1 to _i_), either keeps it in bucket _j_ or allocates it to the newly created bucket.

The system now reattempts the insertion of the new record. Usually, the attempt will succeed. However, if all the records in bucket _j_, as well as the new record, have the same hash-value prefix, it will be necessary to split a bucket again, since all the records in bucket _j_ and the new record are assigned to the same bucket. If the hash function has been chosen carefully, it is unlikely that a single insertion will require that a bucket be split more than once, unless there are a large number of records with the same search key. If all the records in bucket _j_ have the same search-key value, no amount of splitting will help. In such cases, overflow buckets are used to store the records, as in static hashing.

- If _i > i j_ , then more than one entry in the bucket address table points to bucket _j_. Thus, the system can split bucket _j_ without increasing the size of the bucket address table. Observe that all the entries that point to bucket _j_ correspond to hash prefixes that have the same value on the leftmost _i j_ bits. The system allocates a new bucket (bucket _z_), and sets _i j_ and _iz_ to the value that results from adding 1 to the original _i j_ value. Next, the system needs to adjust the entries in the bucket address table that previously pointed to bucket _j_. (Note that with the new value for _i j_ , not all the entries correspond to hash prefixes that have the same value on the leftmost _i j_ bits.) The system leaves the first half of the entries as they were (pointing to bucket _j_), and sets all the remaining entries to point to the newly created bucket (bucket _z_). Next, as in the previous case, the system rehashes each record in bucket _j_, and allocates it either to bucket _j_ or to the newly created bucket _z_.  

_dept\_name_ h(_dept\_name_)

Biology     0010 1101 1111 1011 0010 1100 0011 0000 
Comp. Sci.  1111 0001 0010 0100 1001 0011 0110 1101 
Elec. Eng.  0100 0011 1010 1100 1100 0110 1101 1111 
Finance     1010 0011 1010 0000 1100 0110 1001 1111 
History     1100 0111 1110 1101 1011 1111 0011 1010 
Music       0011 0101 1010 0110 1100 1001 1110 1011 
Physics     1001 1000 0011 1111 1001 1100 0000 0001

**Figure 11.27** Hash function for _dept name_.

The system then reattempts the insert. In the unlikely case that it again fails, it applies one of the two cases, _i_ \= _i j_ or _i > i j_ , as appropriate.

Note that, in both cases, the system needs to recompute the hash function on only the records in bucket _j_.

To delete a record with search-key value _Kl_ , the system follows the same procedure for lookup as before, ending up in some bucket—say, _j_. It removes both the search key from the bucket and the record from the file. The bucket, too, is removed if it becomes empty. Note that, at this point, several buckets can be coalesced, and the size of the bucket address table can be cut in half. The procedure for deciding on which buckets can be coalesced and how to coalesce buckets is left to you to do as an exercise. The conditions under which the bucket address table can be reduced in size are also left to you as an exercise. Unlike coalescing of buckets, changing the size of the bucket address table is a rather expensive operation if the table is large. Therefore it may be worthwhile to reduce the bucket-address-table size only if the number of buckets reduces greatly.

To illustrate the operation of insertion, we use the _instructor_ file in Figure 11.1 and assume that the search key is _dept name_ with the 32-bit hash values as appear in Figure 11.27. Assume that, initially, the file is empty, as in Figure 11.28. We insert the records one by one. To illustrate all the features of extendable hashing in a small structure, we shall make the unrealistic assumption that a bucket can hold only two records.

![Alt text](image-38.png)

**Figure 11.28** Initial extendable hash structure.  

![Alt text](image-39.png)

**Figure 11.29** Hash structure after three insertions.

We insert the record (10101, Srinivasan, Comp. Sci., 65000). The bucket address table contains a pointer to the one bucket, and the system inserts the record. Next, we insert the record (12121, Wu, Finance, 90000). The system also places this record in the one bucket of our structure.

When we attempt to insert the next record (15151, Mozart, Music, 40000), we find that the bucket is full. Since _i_ \= _i_0, we need to increase the number of bits that we use from the hash value. We now use 1 bit, allowing us 21 = 2 buckets. This increase in the number of bits necessitates doubling the size of the bucket address table to two entries. The system splits the bucket, placing in the new bucket those records whose search key has a hash value beginning with 1, and leaving in the original bucket the other records. Figure 11.29 shows the state of our structure after the split.

Next, we insert (22222, Einstein, Physics, 95000). Since the first bit of _h_(Physics) is 1, we must insert this record into the bucket pointed to by the “1” entry in the

![Alt text](image-40.png)

![Alt text](image-41.png)

![Alt text](image-42.png)

bucket address table. Once again, we find the bucket full and _i_ \= _i_1\. We increase the number of bits that we use from the hash to 2. This increase in the number of bits necessitates doubling the size of the bucket address table to four entries, as in Figure 11.30. Since the bucket of Figure 11.29 for hash prefix 0 was not split, the two entries of the bucket address table of 00 and 01 both point to this bucket.

For each record in the bucket of Figure 11.29 for hash prefix 1 (the bucket being split), the system examines the first 2 bits of the hash value to determine which bucket of the new structure should hold it.

Next, we insert (32343, El Said, History, 60000), which goes in the same bucket as Comp. Sci. The following insertion of (33456, Gold, Physics, 87000) results in a bucket overflow, leading to an increase in the number of bits, and a doubling of the size of the bucket address table (see Figure 11.31).

The insertion of (45565, Katz, Comp. Sci., 75000) leads to another bucket over- flow; this overflow, however, can be handled without increasing the number of bits, since the bucket in question has two pointers pointing to it (see Figure 11.32).

Next, we insert the records of “Califieri”, “Singh”, and “Crick” without any bucket overflow. The insertion of the third Comp. Sci. record (83821, Brandt, Comp. Sci., 92000), however, leads to another overflow. This overflow cannot be handled by increasing the number of bits, since there are three records with exactly the same hash value. Hence the system uses an overflow bucket, as in

![Alt text](image-43.png)

![Alt text](image-44.png)

Figure 11.33. We continue in this manner until we have inserted all the _instructor_ records of Figure 11.1. The resulting structure appears in Figure 11.34.

## Static Hashing versus Dynamic Hashing

We now examine the advantages and disadvantages of extendable hashing, com- pared with static hashing. The main advantage of extendable hashing is that performance does not degrade as the file grows. Furthermore, there is minimal space overhead. Although the bucket address table incurs additional overhead, it contains one pointer for each hash value for the current prefix length. This table is thus small. The main space saving of extendable hashing over other forms of hashing is that no buckets need to be reserved for future growth; rather, buckets can be allocated dynamically.

A disadvantage of extendable hashing is that lookup involves an additional level of indirection, since the system must access the bucket address table before accessing the bucket itself. This extra reference has only a minor effect on per- formance. Although the hash structures that we discussed in Section 11.6 do not have this extra level of indirection, they lose their minor performance advantage as they become full.

Thus, extendable hashing appears to be a highly attractive technique, pro- vided that we are willing to accept the added complexity involved in its im- plementation. The bibliographical notes reference more detailed descriptions of extendable hashing implementation.

The bibliographical notes also provide references to another form of dynamic hashing called **linear hashing**, which avoids the extra level of indirection associ- ated with extendable hashing, at the possible cost of more overflow buckets.

