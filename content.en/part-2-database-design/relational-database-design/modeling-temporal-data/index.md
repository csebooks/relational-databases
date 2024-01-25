---
title: 'Modeling Temporal Data'
weight: 9
---

# Modeling Temporal Data

Suppose we retain data in our university organization showing not only the address of each instructor, but also all former addresses of which the university is aware. We may then ask queries such as “Find all instructors who lived in Princeton in 1981.” In this case, we may have multiple addresses for instructors. Each address has an associated start and end date, indicating when the instructor was resident at that address. A special value for the end date, e.g., null, or a value well into the future such as 9999-12-31, can be used to indicate that the instructor is still resident at that address.

In general, **temporal data** are data that have an associated time interval during which they are **valid**.8 We use the term **snapshot** of data to mean the value of the data at a particular point in time. Thus a snapshot of _course_ data gives the values of all attributes, such as title and department, of all courses at a particular point in time.

Modeling temporal data is a challenging problem for several reasons. For ex- ample, suppose we have an _instructor_ entity set with which we wish to associate a time-varying address. To add temporal information to an address, we would then have to create a multivalued attribute, each of whose values is a composite value containing an address and a time interval. In addition to time-varying attribute values, entities may themselves have an associated valid time. For example, a student entity may have a valid time from the date the student entered the uni- versity to the date the student graduated (or left the university). Relationships too may have associated valid times. For example, the _prereq_ relationship may record when a course became a prerequisite for another course. We would thus have to add valid time intervals to attribute values, entity sets, and relationship sets. Adding such detail to an E-R diagram makes it very difficult to create and to comprehend. There have been several proposals to extend the E-R notation to specify in a simple manner that an attribute value or relationship is time varying, but there are no accepted standards.

When we track data values across time, functional dependencies that we assumed to hold, such as:

_ID_ → _street_, _city_

may no longer hold. The following constraint (expressed in English) would hold instead: “An instructor _ID_ has only one _street_ and _city_ value for any given time _t_.”

Functional dependencies that hold at a particular point in time are called temporal functional dependencies. Formally, a **temporal functional dependency** _X_→ _Y_ holds on a relation schema _r_ (_R_) if, for all legal instances of _r_ (_R_), all snapshots of _r_ satisfy the functional dependency _X_ → _Y_.

We could extend the theory of relational database design to take temporal functional dependencies into account. However, reasoning with regular func- tional dependencies is difficult enough already, and few designers are prepared to deal with temporal functional dependencies.

In practice, database designers fall back to simpler approaches to design- ing temporal databases. One commonly used approach is to design the entire database (including E-R design and relational design) ignoring temporal changes (equivalently, taking only a snapshot into consideration). After this, the designer studies the various relations and decides which relations require temporal varia- tion to be tracked.

The next step is to add valid time information to each such relation, by adding start and end time as attributes. For example, consider the _course_ relation. The title of the course may change over time, which can be handled by adding a valid time range; the resultant schema would be

_course_ (_course id_, _title_,_dept name_, _start, end_)

An instance of this relation might have two records (CS-101, “Introduction to Pro- gramming”, 1985-01-01, 2000-12-31) and (CS-101, “Introduction to C”, 2001-01-01, 9999-12-31). If the relation is updated by changing the course title to “Introduction to Java,” the time “9999-12-31” would be updated to the time until which the old value (“Introduction to C”) is valid, and a new tuple would be added containing the new title (“Introduction to Java”), with an appropriate start time.

If another relation had a foreign key referencing a temporal relation, the database designer has to decide if the reference is to the current version of the data or to the data as of a particular point in time. For example, we may extend the _department_ relation to track changes in the building or budget of a department across time, but a reference from the _instructor_ or _student_ relation may not care about the history of the building or budget, but may instead implicitly refer to the temporally current record for the corresponding _dept name_. On the other hand, a record in a student’s transcript should refer to the course title at the time when the student took the course. In this latter case, the referencing relation must also record time information, to identify a particular record from the _course_ relation. In our example, the _year_ and _semester_ when the course was taken can be mapped to a representative time/date value such as midnight of the start date of the semester; the resulting time/date value is used to identify a particular record from the temporal version of the _course_ relation, from which the _title_ is retrieved.

The original primary key for a temporal relation would no longer uniquely identify a tuple. To resolve this problem, we could add the start and end time attributes to the primary key. However, some problems remain:

• It is possible to store data with overlapping intervals, which the primary-key constraint would not catch. If the system supports a native _valid time_ type, it can detect and prevent such overlapping time intervals.

• To specify a foreign key referencing such a relation, the referencing tuples would have to include the start- and end-time attributes as part of their foreign key, and the values must match that in the referenced tuple. Further, if the referenced tuple is updated (and the end time which was in the future is updated), the update must propagate to all the referencing tuples.

If the system supports temporal data in a better fashion, we can allow the referencing tuple to specify a point in time, rather than a range, and rely on the system to ensure that there is a tuple in the referenced relation whose valid time interval contains the point in time. For example, a transcript record  

