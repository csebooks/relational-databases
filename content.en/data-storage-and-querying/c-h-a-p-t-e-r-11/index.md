---
title: C H A P T E R 11
weight: 11
---

C H A P T E R 11## C H A P T E R 11
# Indexing and Hashing

Many queries reference only a small proportion of the records in a file. For ex- ample, a query like “Find all instructors in the Physics department” or “Find the total number of credits earned by the student with _ID_ 22201” references only a fraction of the student records. It is inefficient for the system to read every tuple in the _instructor_ relation to check if the _dept name_ value is “Physics”. Likewise, it is inefficient to read the entire _student_ relation just to find the one tuple for the _ID_ “32556,”. Ideally, the system should be able to locate these records directly. To allow these forms of access, we design additional structures that we associate with files.

