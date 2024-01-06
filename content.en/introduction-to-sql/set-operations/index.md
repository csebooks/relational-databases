---
title: Set Operations
weight: 5
---

# Set Operations

The SQL operations **union**, **intersect**, and **except** operate on relations and correspond to the mathematical set-theory operations ∪, ∩, and −. We shall now construct queries involving the **union**, **intersect**, and **except** operations over two sets.

• The set of all courses taught in the Fall 2009 semester:

**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Fall’ **and** _year_\= 2009;

• The set of all courses taught in the Spring 2010 semester:

**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Spring’ **and** _year_\= 2010;

In our discussion that follows, we shall refer to the relations obtained as the result of the preceding queries as _c1_ and _c2_, respectively, and show the results when these queries are run on the _section_ relation of Figure 2.6 in Figures 3.9 and 3.10. Observe that _c2_ contains two tuples corresponding to _course id_ CS-319, since two sections of the course have been offered in Spring 2010.

![Alt text](image-21.png)

**Figure 3.10** The _c2_ relation, listing courses taught in Spring 2010.

## The Union Operation

To find the set of all courses taught either in Fall 2009 or in Spring 2010, or both, we write:6

(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Fall’ **and** _year_\= 2009) 
**union** 
(**select** _course id_ 
**from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The **union** operation automatically eliminates duplicates, unlike the **select** clause. Thus, using the _section_ relation of Figure 2.6, where two sections of CS-319 are offered in Spring 2010, and a section of CS-101 is offered in the Fall 2009 as well as in the Fall 2010 semester, CS-101 and CS-319 appear only once in the result, shown in Figure 3.11.

If we want to retain all duplicates, we must write **union all** in place of **union**:

(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Fall’ **and** _year_\= 2009) 
**union all** (**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The number of duplicate tuples in the result is equal to the total number of duplicates that appear in both _c1_ and _c2_. So, in the above query, each of CS-319 and CS-101 would be listed twice. As a further example, if it were the case that 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101

![Alt text](image-22.png)

**Figure 3.11** The result relation for _c1_ union _c2_.

were taught in the Fall 2010 semester, then there would be 6 tuples with ECE-101 in the result.

## The Intersect Operation

To find the set of all courses taught in the Fall 2009 as well as in Spring 2010 we write:

(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Fall’ **and** _year_\= 2009)
**intersect** 
(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The result relation, shown in Figure 3.12, contains only one tuple with CS-101. The **intersect** operation automatically eliminates duplicates. For example, if it were the case that 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101 were taught in the Spring 2010 semester, then there would be only 1 tuple with ECE-101 in the result.

If we want to retain all duplicates, we must write **intersect all** in place of **intersect**:

![Alt text](image-23.png)

**Figure 3.12** The result relation for _c1_ intersect _c2_.  

(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Fall’ **and** _year_\= 2009) 
**intersect all** 
(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The number of duplicate tuples that appear in the result is equal to the minimum number of duplicates in both _c1_ and _c2_. For example, if 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101 were taught in the Spring 2010 semester, then there would be 2 tuples with ECE-101 in the result.

## The Except Operation

To find all courses taught in the Fall 2009 semester but not in the Spring 2010 semester, we write:

(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Fall’ **and** _year_\= 2009) 
**except** 
(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The result of this query is shown in Figure 3.13. Note that this is exactly relation _c1_ of Figure 3.9 except that the tuple for CS-101 does not appear. The **except** operation7 outputs all tuples from its first input that do not occur in the second input; that is, it performs set difference. The operation automatically eliminates duplicates in the inputs before performing set difference. For example, if 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101 were taught in the Spring 2010 semester, the result of the **except** operation would not have any copy of ECE-101.

If we want to retain duplicates, we must write **except all** in place of **except**:

(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Fall’ **and** _year_\= 2009) 
**except all** 
(**select** _course id_ 
**from** _section_ 
**where** _semester_ \= ’Spring’ **and** _year_\= 2010);

![Alt text](image-24.png)

**Figure 3.13** The result relation for _c1_ except _c2_.

The number of duplicate copies of a tuple in the result is equal to the number of duplicate copies in _c1_ minus the number of duplicate copies in _c2_, provided that the difference is positive. Thus, if 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101 were taught in Spring 2010, then there are 2 tuples with ECE-101 in the result. If, however, there were two or fewer sections of ECE-101 in the the Fall 2009 semester, and two sections of ECE-101 in the Spring 2010 semester, there is no tuple with ECE-101 in the result.

