---
title: 'Static Hashing'
weight: 6
references:
    videos:
        - youtube:d_4SL3BRPuQ
        - youtube:-XviyIActiM
    links:
        - https://www.javatpoint.com/dbms-static-hashing
        - https://en.wikipedia.org/wiki/Static_hashing
    books:
        - b1:
            title: Design of hashing algorithms
            url: https://www.google.co.in/books/edition/Design_of_Hashing_Algorithms/MKlzAAAAIAAJ?hl=en&gbpv=0&bsq=Static%20Hashing%20books
        - b2:
           title: The Joys of Hashing
            url: https://www.google.co.in/books/edition/The_Joys_of_Hashing/r0mHDwAAQBAJ?hl=en&gbpv=0
---

# Static Hashing

One disadvantage of sequential file organization is that we must access an index structure to locate data, or must use binary search, and that results in more I/O operations. File organizations based on the technique of **hashing** allow us to avoid accessing an index structure. Hashing also provides a way of constructing indices. We study file organizations and indices based on hashing in the following sections.

In our description of hashing, we shall use the term **bucket** to denote a unit of storage that can store one or more records. A bucket is typically a disk block, but could be chosen to be smaller or larger than a disk block.

Formally, let _K_ denote the set of all search-key values, and let _B_ denote the set of all bucket addresses. A **hash function** _h_ is a function from _K_ to _B_. Let _h_ denote a hash function.

To insert a record with search key _Ki_ , we compute _h_(_Ki_ ), which gives the address of the bucket for that record. Assume for now that there is space in the bucket to store the record. Then, the record is stored in that bucket.

To perform a lookup on a search-key value _Ki_ , we simply compute _h_(_Ki_ ), then search the bucket with that address. Suppose that two search keys, _K_5 and _K_7, have the same hash value; that is, _h_(_K_5) = _h_(_K_7). If we perform a lookup on _K_5, the bucket _h_(_K_5) contains records with search-key values _K_5 and records with search-key values _K_7\. Thus, we have to check the search-key value of every record in the bucket to verify that the record is one that we want.

Deletion is equally straightforward. If the search-key value of the record to be deleted is _Ki_ , we compute _h_(_Ki_ ), then search the corresponding bucket for that record, and delete the record from the bucket.

Hashing can be used for two different purposes. In a **hash file organization**, we obtain the address of the disk block containing a desired record directly by computing a function on the search-key value of the record. In a **hash index organization** we organize the search keys, with their associated pointers, into a hash file structure.

## Hash Functions

The worst possible hash function maps all search-key values to the same bucket. Such a function is undesirable because all the records have to be kept in the same bucket. A lookup has to examine every such record to find the one desired. An ideal hash function distributes the stored keys uniformly across all the buckets, so that every bucket has the same number of records.

Since we do not know at design time precisely which search-key values will be stored in the file, we want to choose a hash function that assigns search-key values to buckets in such a way that the distribution has these qualities:

- The distribution is _uniform_. That is, the hash function assigns each bucket the same number of search-key values from the set of _all_ possible search-key values.

- The distribution is _random_. That is, in the average case, each bucket will have nearly the same number of values assigned to it, regardless of the actual distribution of search-key values. More precisely, the hash value will not be correlated to any externally visible ordering on the search-key values, such as alphabetic ordering or ordering by the length of the search keys; the hash function will appear to be random.

As an illustration of these principles, let us choose a hash function for the _instructor_ file using the search key _dept name_. The hash function that we choose must have the desirable properties not only on the example _instructor_ file that we have been using, but also on an _instructor_ file of realistic size for a large university with many departments.

Assume that we decide to have 26 buckets, and we define a hash function that maps names beginning with the _i_th letter of the alphabet to the _i_th bucket. This hash function has the virtue of simplicity, but it fails to provide a uniform distribution, since we expect more names to begin with such letters as B and R than Q and X, for example.

Now suppose that we want a hash function on the search key _salary_. Suppose that the minimum salary is $30,000 and the maximum salary is $130,000, and we use a hash function that divides the values into 10 ranges, $30,000–$40,000, $40,001–$50,000 and so on. The distribution of search-key values is uniform (since 

![Alt text](image-34.png)

each bucket has the same number of different _salary_ values), but is not random. Records with salaries between $60,001 and $70,000 are far more common than are records with salaries between $30,001 and $40,000. As a result, the distribution of records is not uniform—some buckets receive more records than others do. If the function has a random distribution, even if there are such correlations in the search keys, the randomness of the distribution will make it very likely that all buckets will have roughly the same number of records, as long as each search key occurs in only a small fraction of the records. (If a single search key occurs in a large fraction of the records, the bucket containing it is likely to have more records than other buckets, regardless of the hash function used.)

Typical hash functions perform computation on the internal binary machine representation of characters in the search key. A simple hash function of this type first computes the sum of the binary representations of the characters of a key, then returns the sum modulo the number of buckets.

Figure 11.23 shows the application of such a scheme, with eight buckets, to the _instructor_ file, under the assumption that the _i_th letter in the alphabet is represented by the integer _i_.

The following hash function is a better alternative for hashing strings. Let _s_ be a string of length _n_, and let s[i] denote the ith byte of the string. The hash function is defined as:

s[0] ∗ 31^(n−1)^ + s[1] ∗ 31^(n−2)^ +···+ s[n − 1]


The function can be implemented efficiently by setting the hash result initially to 0, and iterating from the first to the last character of the string, at each step multiplying the hash value by 31 and then adding the next character (treated as an integer). The above expression would appear to result in a very large number, but it is actually computed with fixed-size positive integers; the result of each multiplication and addition is thus automatically computed modulo the largest possible integer value plus 1. The result of the above function modulo the number of buckets can then be used for indexing.

Hash functions require careful design. A bad hash function may result in lookup taking time proportional to the number of search keys in the file. A well- designed function gives an average-case lookup time that is a (small) constant, independent of the number of search keys in the file.

## Handling of Bucket Overflows

So far, we have assumed that, when a record is inserted, the bucket to which it is mapped has space to store the record. If the bucket does not have enough space, a **bucket overflow** is said to occur. Bucket overflow can occur for several reasons:

- **Insufficient buckets**. The number of buckets, which we denote _nB_ , must be chosen such that _nB > nr/ fr_ , where _nr_ denotes the total number of records that will be stored and _fr_ denotes the number of records that will fit in a bucket. This designation, of course, assumes that the total number of records is known when the hash function is chosen.

- **Skew**. Some buckets are assigned more records than are others, so a bucket may overflow even when other buckets still have space. This situation is called bucket **skew**. Skew can occur for two reasons:

**1\.** Multiple records may have the same search key.

**2\.** The chosen hash function may result in nonuniform distribution of search keys.

So that the probability of bucket overflow is reduced, the number of buckets is chosen to be (_nr/ fr_ ) ∗ (1 + _d_), where _d_ is a fudge factor, typically around 0_._2\. Some space is wasted: About 20 percent of the space in the buckets will be empty. But the benefit is that the probability of overflow is reduced.

Despite allocation of a few more buckets than required, bucket overflow can still occur. We handle bucket overflow by using **overflow buckets**. If a record must be inserted into a bucket _b_, and _b_ is already full, the system provides an overflow bucket for _b_, and inserts the record into the overflow bucket. If the overflow bucket is also full, the system provides another overflow bucket, and so on. All the overflow buckets of a given bucket are chained together in a linked list, as in Figure 11.24. Overflow handling using such a linked list is called **overflow chaining**.

We must change the lookup algorithm slightly to handle overflow chaining. As before, the system uses the hash function on the search key to identify a bucket  

![Alt text](image-35.png)

_b_. The system must examine all the records in bucket _b_ to see whether they match the search key, as before. In addition, if bucket _b_ has overflow buckets, the system must examine the records in all the overflow buckets also.

The form of hash structure that we have just described is sometimes referred to as **closed hashing**. Under an alternative approach, called **open hashing**, the set of buckets is fixed, and there are no overflow chains. Instead, if a bucket is full, the system inserts records in some other bucket in the initial set of buckets _B_. One policy is to use the next bucket (in cyclic order) that has space; this policy is called _linear probing_. Other policies, such as computing further hash functions, are also used. Open hashing has been used to construct symbol tables for compilers and assemblers, but closed hashing is preferable for database systems. The rea- son is that deletion under open hashing is troublesome. Usually, compilers and assemblers perform only lookup and insertion operations on their symbol tables. However, in a database system, it is important to be able to handle deletion as well as insertion. Thus, open hashing is of only minor importance in database implementation.

An important drawback to the form of hashing that we have described is that we must choose the hash function when we implement the system, and it cannot be changed easily thereafter if the file being indexed grows or shrinks. Since the function _h_ maps search-key values to a fixed set _B_ of bucket addresses, we waste space if _B_ is made large to handle future growth of the file. If _B_ is too small, the buckets contain records of many different search-key values, and bucket overflows can occur. As the file grows, performance suffers. We study later, in Section 11.7, how the number of buckets and the hash function can be changed dynamically.  

![Alt text](image-36.png)

## Hash Indices

Hashing can be used not only for file organization, but also for index-structure creation. A **hash index** organizes the search keys, with their associated pointers, into a hash file structure. We construct a hash index as follows. We apply a hash function on a search key to identify a bucket, and store the key and its associated pointers in the bucket (or in overflow buckets). Figure 11.25 shows a secondary hash index on the _instructor_ file, for the search key _ID_. The hash function in the figure computes the sum of the digits of the _ID_ modulo 8. The hash index has eight buckets, each of size 2 (realistic indices would, of course, have much larger bucket sizes). One of the buckets has three keys mapped to it, so it has an overflow bucket. In this example, _ID_ is a primary key for _instructor_, so each search key has only one associated pointer. In general, multiple pointers can be associated with each key.

We use the term _hash index_ to denote hash file structures as well as secondary hash indices. Strictly speaking, hash indices are only secondary index structures. A hash index is never needed as a clustering index structure, since, if a file itself is organized by hashing, there is no need for a separate hash index structure on it. However, since hash file organization provides the same direct access to records that indexing provides, we pretend that a file organized by hashing also has a clustering hash index on it.

