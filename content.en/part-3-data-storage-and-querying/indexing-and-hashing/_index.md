---
title: 'Indexing and Hashing'
weight: 2

---

# Indexing and Hashing

Many queries reference only a small proportion of the records in a file. For example, a query like “Find all instructors in the Physics department” or “Find the total number of credits earned by the student with ID 22201” references only a fraction of the student records. It is inefficient for the system to read every tuple in the instructor relation to check if the dept name value is “Physics”. Likewise, it is inefficient to read the entire student relation just to find the one tuple for the ID “32556,”. Ideally, the system should be able to locate these records directly. To allow these forms of access, we design additional structures that we associate with files.