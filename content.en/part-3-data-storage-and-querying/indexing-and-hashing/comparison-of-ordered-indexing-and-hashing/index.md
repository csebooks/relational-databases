---
title: 'Comparison of Ordered Indexing and Hashing'
weight: 8 \
references:
    videos:
        - youtube:_sW4aOdvQ3o&t
        - youtube:d_4SL3BRPuQ&t
    links:
        - https://www.geeksforgeeks.org/difference-between-indexing-and-hashing-in-dbms/
        - https://pediaa.com/what-is-the-difference-between-indexing-and-hashing/
    books:
        - b1:
            title: Database System Concepts
            url: https://www.google.co.in/books/edition/Database_System_Concepts/Hvn_QQAACAAJ?hl=en
        - b2:
            title: Data Structures Based on Non-linear Relations and Data Processing Methods
            url: https://www.google.co.in/books/edition/Data_structures_based_on_non_linear_rela/eXnnDwAAQBAJ?hl=en&gbpv=0
---

# Comparison of Ordered Indexing and Hashing

We have seen several ordered-indexing schemes and several hashing schemes. We can organize files of records as ordered files by using index-sequential organi- zation or B+-tree organizations. Alternatively, we can organize the files by using hashing. Finally, we can organize them as heap files, where the records are not ordered in any particular way.

Each scheme has advantages in certain situations. A database-system imple- mentor could provide many schemes, leaving the final decision of which schemes to use to the database designer. However, such an approach requires the imple- mentor to write more code, adding both to the cost of the system and to the space that the system occupies. Most database systems support B+-trees and may additionally support some form of hash file organization or hash indices.

To make a choice of file organization and indexing techniques for a relation, the implementor or the database designer must consider the following issues:

- Is the cost of periodic reorganization of the index or hash organization ac- ceptable?

- What is the relative frequency of insertion and deletion?

- Is it desirable to optimize average access time at the expense of increasing the worst-case access time?

- What types of queries are users likely to pose?

We have already examined the first three of these issues, first in our review of the relative merits of specific indexing techniques, and again in our discussion of hashing techniques. The fourth issue, the expected type of query, is critical to the choice of ordered indexing or hashing.

If most queries are of the form:

**select** _A_1_, A_2_, . . . , An_ 
**from** _r_ 
**where** _Ai_ \= _c_;  

then, to process this query, the system will perform a lookup on an ordered index or a hash structure for attribute _Ai_ , for value _c_. For queries of this form, a hashing scheme is preferable. An ordered-index lookup requires time proportional to the log of the number of values in _r_ for _Ai_ . In a hash structure, however, the average lookup time is a constant independent of the size of the database. The only advantage to an index over a hash structure for this form of query is that the worst-case lookup time is proportional to the log of the number of values in _r_ for _Ai_ . By contrast, for hashing, the worst-case lookup time is proportional to the number of values in _r_ for _Ai_ . However, the worst-case lookup time is unlikely to occur with hashing, and hashing is preferable in this case.

Ordered-index techniques are preferable to hashing in cases where the query specifies a range of values. Such a query takes the following form:

**select** _A_1_, A_2_, ..., An_ 
**from** _r_ 
**where** _Ai_ ≤ _c_2 **and** _Ai_ ≥ _c_1;

In other words, the preceding query finds all the records with _Ai_ values between _c_1 and _c_2.

Let us consider how we process this query using an ordered index. First, we perform a lookup on value c~1~\. Once we have found the bucket for value c~1~, we follow the pointer chain in the index to read the next bucket in order, and we continue in this manner until we reach c~2~.

If, instead of an ordered index, we have a hash structure, we can perform a lookup on c~1~ and can locate the corresponding bucket—but it is not easy, in general, to determine the next bucket that must be examined. The difficulty arises because a good hash function assigns values randomly to buckets. Thus, there is no simple notion of “next bucket in sorted order.” The reason we cannot chain buckets together in sorted order on _Ai_ is that each bucket is assigned many search- key values. Since values are scattered randomly by the hash function, the values in the specified range are likely to be scattered across many or all of the buckets. Therefore, we have to read all the buckets to find the required search keys.

Usually the designer will choose ordered indexing unless it is known in advance that range queries will be infrequent, in which case hashing would be chosen. Hash organizations are particularly useful for temporary files created during query processing, if lookups based on a key value are required, but no range queries will be performed.

