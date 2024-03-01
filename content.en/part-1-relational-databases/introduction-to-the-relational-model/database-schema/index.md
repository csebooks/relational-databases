---
title:  Database Schema
weight: 2
references:
    videos:
        - youtube:KpbyPhJqIf0
        - youtube:0JAYah0MVno
    links:
        - https://www.tutorialspoint.com/what-is-a-query-language-in-dbms
        - https://www.i2tutorials.com/dbms-introduction/dbms-relational-query-languages/
    books:
        - b1:
            title: Understanding Relational Database Query Languages
            url: https://www.google.co.in/books/edition/Understanding_Relational_Database_Query/y3JnQgAACAAJ?hl=en
        - b2:
            title: Theory and Practice Of Relational Database
            url: https://www.google.co.in/books/edition/Theory_and_Practice_of_Relational_Databa/T42BSf-1f5cC?hl=en&gbpv=1&dq=Relational+Query+language+books&printsec=frontcover
---

#  Database Schema

When we talk about a database, we must differentiate between the **database schema**, which is the logical design of the database, and the **database instance**, which is a snapshot of the data in the database at a given instant in time.

The concept of a relation corresponds to the programming-language notion of a variable, while the concept of a **relation schema** corresponds to the programming-language notion of type definition.

In general, a relation schema consists of a list of attributes and their corresponding domains. We shall not be concerned about the precise definition of the domain of each attribute until we discuss the SQL language in Chapter 3.

The concept of a relation instance corresponds to the programming-language notion of a value of a variable. The value of a given variable may change with time;  


|_dept name_| _building_| _budget_|
| --- | ---- | --- |
|Biology |Watson |90000 |
|Comp. Sci. |Taylor |100000 |
|Elec. Eng. |Taylor |85000 |
|Finance |Painter |120000 |
|History |Painter |50000 |
|Music |Packard |80000 |
|Physics| Watson |70000|

**Figure 2.5** The _department_ relation.


similarly the contents of a relation instance may change with time as the relation is updated. In contrast, the schema of a relation does not generally change.

Although it is important to know the difference between a relation schema and a relation instance, we often use the same name, such as _instructor_, to refer to both the schema and the instance. Where required, we explicitly refer to the schema or to the instance, for example “the _instructor_ schema,” or “an instance of the _instructor_ relation.” However, where it is clear whether we mean the schema or the instance, we simply use the relation name.

Consider the _department_ relation of Figure 2.5. The schema for that relation is

_department_ (_dept name_,_building_, _budget_)

Note that the attribute _dept name_ appears in both the _instructor_ schema and the _department_ schema. This duplication is not a coincidence. Rather, using common attributes in relation schemas is one way of relating tuples of distinct relations. For example, suppose we wish to find the information about all the instructors who work in the Watson building. We look first at the _department_ relation to find the _dept name_ of all the departments housed in Watson. Then, for each such department, we look in the _instructor_ relation to find the information about the instructor associated with the corresponding _dept name_.


Let us continue with our university database example. 
Each course in a university may be offered multiple times, across different semesters, or even within a semester. We need a relation to describe each individual offering, or section, of the class. The schema is


_section_ (_course id_, _sec id_, _semester_, _year_, _building_, _room number_, _time slot id_)

Figure 2.6 shows a sample instance of the _section_ relation. We need a relation to describe the association between instructors and the

class sections that they teach. The relation schema to describe this association is

_teaches_ (_ID_, _course id_, _sec id_, _semester_, _year_) 

|_course id_| _sec-id_|_semester_| _year_|_building_ |_room number_|_time-slot-id_|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|BIO-101| 1 |Summer| 2009 |Painter |514 |B |
|BIO-301| 1 |Summer |2010 |Painter |514 |A |
|CS-101 |1 |Fall |2009 |Packard |101| H |
|CS-101 |1 |Spring |2010 |Packard |101 |F |
|CS-190 |1| Spring |2009 |Taylor |3128 |E |
|CS-190 |2 |Spring |2009 |Taylor |3128 |A |
|CS-315 |1 |Spring |2010 |Watson |120 |D |
|CS-319 |1 |Spring |2010 |Watson |100 |B |
|CS-319 |2 |Spring |2010 |Taylor |3128 |C |
|CS-347 |1 |Fall |2009 |Taylor |3128 |A |
|EE-181 |1 |Spring |2009 |Taylor |3128 |C |
|FIN-201 |1 |Spring |2010 |Packard |101 |B |
|HIS-351 |1 |Spring |2010 |Painter |514 |C |
|MU-199 |1 |Spring |2010 |Packard |101 |D |
|PHY-101 |1 |Fall |2009 |Watson |100 |A|

**Figure 2.6** The _section_ relation.

Figure 2.7 shows a sample instance of the _teaches_ relation. As you can imagine, there are many more relations maintained in a real university database. In addition to those relations we have listed already, _instructor_, _department_, _course_, _section_, _prereq_, and _teaches_, we use the following relations in this text:

|_ID_| _course id_| _sec id_| _semester_| _year_|
| ---- | ---- | ---- | ---- | ---- | 
|10101 |CS-101 |1 |Fall |2009 |
|10101 |CS-315 |1 |Spring| 2010| 
|10101 |CS-347 |1 |Fall |2009 |
|12121 |FIN-201 |1 |Spring| 2010| 
|15151 |MU-199 |1 |Spring |2010 |
|22222 |PHY-101 |1 |Fall |2009 |
|32343 |HIS-351 |1 |Spring| 2010| 
|45565 |CS-101 |1 |Spring |2010 |
|45565 |CS-319 |1 |Spring |2010 |
|76766 |BIO-101 |1| Summer |2009 |
|76766 |BIO-301 |1| Summer |2010 |
|83821 |CS-190 |1| Spring |2009 |
|83821 |CS-190 |2| Spring |2009|
|83821 |CS-319 |2| Spring |2010 |
|98345 |EE-181 |1| Spring |2009|

**Figure 2.7** The _teaches_ relation.  

-_student_ (_ID_, _name_, _dept name_, _tot cred_)

- _advisor_ (_s id_, _i id_)

- _takes_ (_ID_, _course id_, _sec id_, _semester_, _year_, _grade_)

- _classroom_ (_building_, _room number_, _capacity_)

- _time slot_ (_time slot id_, _day_, _start time_, _end time_)
