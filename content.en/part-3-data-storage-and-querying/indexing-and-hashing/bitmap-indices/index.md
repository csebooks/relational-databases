---
title: 'Bitmap Indices'
weight: 9
references:
    videos:
        - youtube:JYRyEo88Qd0 
        - youtube:JYRyEo88Qd0
    links:
        - https://www.geeksforgeeks.org/bitmap-indexing-in-dbms/
        - https://www.javatpoint.com/bitmap-indexing
    books:
        - b1:
            title: Multidimensional Databases and Data Warehousing
            url: https://www.google.co.in/books/edition/Multidimensional_Databases_and_Data_Ware/GYdyEAAAQBAJ?hl=en&gbpv=0
        - b2:
           title: Database Management Systems book
            url:https://www.google.co.in/books/edition/UGC_NET_unit_4_COMPUTER_SCIENCE_Database/9iWGEAAAQBAJ?hl=en&gbpv=0
---

# Bitmap Indices

Bitmap indices are a specialized type of index designed for easy querying on multiple keys, although each bitmap index is built on a single key.

For bitmap indices to be used, records in a relation must be numbered se- quentially, starting from, say, 0. Given a number _n_, it must be easy to retrieve the record numbered _n_. This is particularly easy to achieve if records are fixed in size, and allocated on consecutive blocks of a file. The record number can then be translated easily into a block number and a number that identifies the record within the block.

Consider a relation _r_ , with an attribute _A_ that can take on only one of a small number (for example, 2 to 20) values. For instance, a relation _instructor info_ may have an attribute _gender_, which can take only values m (male) or f (female). Another example would be an attribute _income level_, where income has been broken up into 5 levels: L1: _$_0 9999, L2: $10,000−19,999, L3: 20,000−39,999, L4:40,000−74,999, and L5: 75,000−∞. Here, the raw data can take on many values, but a data analyst has split the values into a small number of ranges to simplify analysis of the data.

## Bitmap Index Structure

A **bitmap** is simply an array of bits. In its simplest form, a **bitmap index** on the attribute _A_ of relation _r_ consists of one bitmap for each value that _A_ can take. Each bitmap has as many bits as the number of records in the relation. The _i_th bit of the bitmap for value _v j_ is set to 1 if the record numbered _i_ has the value _v j_ for attribute _A_. All other bits of the bitmap are set to 0.

In our example, there is one bitmap for the value m and one for f. The _i_th bit of the bitmap for m is set to 1 if the _gender_ value of the record numbered _i_ is m. All other bits of the bitmap for m are set to 0. Similarly, the bitmap for f has the value 1 for bits corresponding to records with the value f for the _gender_ attribute; all other bits have the value 0. Figure 11.35 shows an example of bitmap indices on a relation _instructor info_.

We now consider when bitmaps are useful. The simplest way of retrieving all records with value m (or value f) would be to simply read all records of the relation and select those records with value m (or f, respectively). The bitmap index doesn’t really help to speed up such a selection. While it would allow us to!


read only those records for a specific gender, it is likely that every disk block for the file would have to be read anyway.

In fact, bitmap indices are useful for selections mainly when there are selec- tions on multiple keys. Suppose we create a bitmap index on attribute _income level_, which we described earlier, in addition to the bitmap index on _gender_.

Consider now a query that selects women with income in the range $10,000 to
$19, 999. This query can be expressed as

**select** \* 
**from** _r_ 
**where** _gender_ \= ’f’ **and** _income level_ \= ’L2’;

To evaluate this selection, we fetch the bitmaps for _gender_ value f and the bitmap for _income level_ value _L_ 2, and perform an **intersection** (logical-and) of the two bitmaps. In other words, we compute a new bitmap where bit _i_ has value 1 if the _i_th bit of the two bitmaps are both 1, and has a value 0 otherwise. In the example in Figure 11.35, the intersection of the bitmap for _gender_ \= f (01101) and the bitmap for _income level_ \= _L_ 2 (01000) gives the bitmap 01000.

Since the first attribute can take two values, and the second can take five values, we would expect only about 1 in 10 records, on an average, to satisfy a combined condition on the two attributes. If there are further conditions, the fraction of records satisfying all the conditions is likely to be quite small. The system can then compute the query result by finding all bits with value 1 in the intersection bitmap and retrieving the corresponding records. If the fraction is large, scanning the entire relation would remain the cheaper alternative.

Another important use of bitmaps is to count the number of tuples satisfying a given selection. Such queries are important for data analysis. For instance, if we wish to find out how many women have an income level _L_ 2, we compute the intersection of the two bitmaps and then count the number of bits that are 1 in the intersection bitmap. We can thus get the desired result from the bitmap index, without even accessing the relation.

Bitmap indices are generally quite small compared to the actual relation size. Records are typically at least tens of bytes to hundreds of bytes long, whereas a single bit represents the record in a bitmap. Thus the space occupied by a single bitmap is usually less than 1 percent of the space occupied by the relation. For instance, if the record size for a given relation is 100 bytes, then the space occupied by a single bitmap would be 1

8 of 1 percent of the space occupied by the relation. If an attribute _A_ of the relation can take on only one of eight values, a bitmap index on attribute _A_ would consist of eight bitmaps, which together occupy only 1 percent of the size of the relation.

Deletion of records creates gaps in the sequence of records, since shifting records (or record numbers) to fill gaps would be extremely expensive. To recog- nize deleted records, we can store an **existence bitmap**, in which bit _i_ is 0 if record _i_ does not exist and 1 otherwise. We shall see the need for existence bitmaps in Section 11.9.2. Insertion of records should not affect the sequence numbering of other records. Therefore, we can do insertion either by appending records to the end of the file or by replacing deleted records.

## Efficient Implementation of Bitmap Operations

We can compute the intersection of two bitmaps easily by using a **for** loop: the _i_ th iteration of the loop computes the **and** of the _i_ th bits of the two bitmaps. We can speed up computation of the intersection greatly by using bit-wise **and** instruc- tions supported by most computer instruction sets. A _word_ usually consists of 32 or 64 bits, depending on the architecture of the computer. A bit-wise **and** instruc- tion takes two words as input and outputs a word where each bit is the logical **and** of the bits in corresponding positions of the input words. What is important to note is that a single bit-wise **and** instruction can compute the intersection of 32 or 64 bits _at once_.

If a relation had 1 million records, each bitmap would contain 1 million bits, or equivalently 128 kilobytes. Only 31,250 instructions are needed to compute the intersection of two bitmaps for our relation, assuming a 32-bit word length. Thus, computing bitmap intersections is an extremely fast operation.

Just as bitmap intersection is useful for computing the **and** of two conditions, bitmap union is useful for computing the **or** of two conditions. The procedure for bitmap union is exactly the same as for intersection, except we use bit-wise **or** instructions instead of bit-wise **and** instructions.

The complement operation can be used to compute a predicate involving the negation of a condition, such as **not** (_income-level_ \= _L_ 1). The complement of a bitmap is generated by complementing every bit of the bitmap (the complement of 1 is 0 and the complement of 0 is 1). It may appear that **not** (_income level_ \= _L_ 1) can be implemented by just computing the complement of the bitmap for income level _L_ 1\. If some records have been deleted, however, just computing the complement of a bitmap is not sufficient. Bits corresponding to such records would be 0 in the original bitmap, but would become 1 in the complement, although the records don’t exist. A similar problem also arises when the value of an attribute is _null_. For instance, if the value of _income level_ is null, the bit would be 0 in the original bitmap for value _L_ 1, and 1 in the complement bitmap.

To make sure that the bits corresponding to deleted records are set to 0 in the result, the complement bitmap must be intersected with the existence bitmap to turn off the bits for deleted records. Similarly, to handle null values, the complement bitmap must also be intersected with the complement of the bitmap for the value _null_.2

Counting the number of bits that are 1 in a bitmap can be done quickly by a clever technique. We can maintain an array with 256 entries, where the _i_th entry stores the number of bits that are 1 in the binary representation of _i_ . Set the total count initially to 0. We take each byte of the bitmap, use it to index into this array, and add the stored count to the total count. The number of addition operations is1/8 of the number of tuples, and thus the counting process is very efficient. A large array (using 2^16^ = 65,536 entries), indexed by pairs of bytes, would give even higher speedup, but at a higher storage cost.

## Bitmaps and B+-Trees

Bitmaps can be combined with regular B+-tree indices for relations where a few attribute values are extremely common, and other values also occur, but much less frequently. In a B+-tree index leaf, for each value we would normally maintain a list of all records with that value for the indexed attribute. Each element of the list would be a record identifier, consisting of at least 32 bits, and usually more. For a value that occurs in many records, we store a bitmap instead of a list of records.

Suppose a particular value _vi_ occurs in 1/16 of the records of a relation. Let _N_ be the number of records in the relation, and assume that a record has a 64-bit number identifying it. The bitmap needs only 1 bit per record, or _N_ bits in total. In contrast, the list representation requires 64 bits per record where the value occurs, or 64 ∗_N/_16 = 4 _N_ bits. Thus, a bitmap is preferable for representing the list of records for value _vi_ . In our example (with a 64-bit record identifier), if fewer than 1 in 64 records have a particular value, the list representation is preferable for identifying records with that value, since it uses fewer bits than the bitmap representation. If more than 1 in 64 records have that value, the bitmap representation is preferable.

Thus, bitmaps can be used as a compressed storage mechanism at the leaf nodes of B+-trees for those values that occur very frequently.

