---
title: Time in Databases
weight: 8
---

Time in Databases## Time in Databases

A database models the state of some aspect of the real world outside itself. Typi- cally, databases model only one state—the current state—of the real world, and do not store information about past states, except perhaps as audit trails. When the state of the real world changes, the database gets updated, and information about the old state gets lost. However, in many applications, it is important to store and retrieve information about past states. For example, a patient database must store information about the medical history of a patient. A factory monitor- ing system may store information about current and past readings of sensors in the factory, for analysis. Databases that store information about states of the real world across time are called **temporal databases**.

When considering the issue of time in database systems, we must distinguish between time as measured by the system and time as observed in the real world. The **valid time** for a fact is the set of time intervals during which the fact is true in the real world. The **transaction time** for a fact is the time interval during which the fact is current within the database system. This latter time is based on the transaction serialization order and is generated automatically by the system. Note that valid-time intervals, being a real-world concept, cannot be generated automatically and must be provided to the system.

A **temporal relation** is one where each tuple has an associated time when it is true; the time may be either valid time or transaction time. Of course, both valid time and transaction time can be stored, in which case the relation is said  
| ID | name | dept name | salary | from | to | 
| ---- |---- | ---- |---- | ---- |---- |
| 10101 | Srinivasan | Comp. Sci. | 61000 | 2007/1/1 |2007/12/31 |
 | 10101 | Srinivasan | Comp. Sci. | 65000 |2008/1/1 | 2008/12/31 |
 | 12121 | Wu | Finance | 82000 | 2005/1/1 |  2006/12/31 |
 | 12121 | Wu | Finance | 87000 | 2007/1/1 | 2007/12/31 |
 | 12121 | Wu | Finance | 90000 | 2008/1/1 | 2008/12/31 |
 | 98345 | Kim  | Elec. Eng. | 80000 | 2005/1/1 | 2008/12/31 |

**Figure 25.1** A temporal _instructor_ relation.

to be a **bitemporal relation**. Figure 25.1 shows an example of a temporal relation. To simplify the representation, each tuple has only one time interval associated with it; thus, a tuple is represented once for every disjoint time interval in which it is true. Intervals are shown here as a pair of attributes _from_ and _to_; an actual implementation would have a structured type, perhaps called _Interval_, that con- tains both fields. Note that some of the tuples have a “\*” in the _to_ time column; these asterisks indicate that the tuple is true until the value in the _to_ time column is changed; thus, the tuple is true at the current time. Although times are shown in textual form, they are stored internally in a more compact form, such as the number of seconds since some fixed time on a fixed date (such as 12:00 A.M., January 1, 1900) that can be translated back to the normal textual form.

### Time Specification in SQL

The SQL standard defines the types **date**, **time**, and **timestamp** as we saw in Chapter 4. The type **date** contains four digits for the year (1–9999), two digits for the month (1–12), and two digits for the date (1–31). The type **time** contains two digits for the hour, two digits for the minute, and two digits for the second, plus optional fractional digits. The seconds field can go beyond 60, to allow for leap seconds that are added during some years to correct for small variations in the speed of rotation of Earth. The type **timestamp** contains the fields of **date** and **time**, with six fractional digits for the seconds field.

Since different places in the world have different local times, there is often a need for specifying the time zone along with the time. The **Universal Coordinated Time** (**UTC**) is a standard reference point for specifying time, with local times defined as offsets from UTC. (The standard abbreviation is UTC, rather than UCT, since it is an abbreviation of “Universal Coordinated Time” written in French as _universel temps coordonné.)_ SQL also supports two types, **time with time zone**, and **timestamp with time zone**, which specify the time as a local time plus the offset of the local time from UTC. For instance, the time could be expressed in terms of U.S. Eastern Standard Time, with an offset of −6:00, since U.S. Eastern Standard time is 6 hours behind UTC.

SQL supports a type called **interval**, which allows us to refer to a period of time such as “1 day” or “2 days and 5 hours,” without specifying a particular time when this period starts. This notion differs from the notion of interval we used previously, which refers to an interval of time with specific starting and ending times.^1^

### Temporal Query Languages

A database relation without temporal information is sometimes called a **snapshot relation**, since it reflects the state in a snapshot of the real world. Thus, a snapshot of a temporal relation at a point in time _t_ is the set of tuples in the relation that are true at time _t_, with the time-interval attributes projected out. The snapshot operation on a temporal relation gives the snapshot of the relation at a specified time (or the current time, if the time is not specified).

A **temporal selection** is a selection that involves the time attributes; a **tempo- ral projection** is a projection where the tuples in the projection inherit their times from the tuples in the original relation. A **temporal join** is a join, with the time of a tuple in the result being the intersection of the times of the tuples from which it is derived. If the times do not intersect, the tuple is removed from the result.

The predicates _precedes_, _overlaps_, and _contains_ can be applied on intervals; their meanings should be clear. The _intersect_ operation can be applied on two intervals, to give a single (possibly empty) interval. However, the union of two intervals may or may not be a single interval.

Functional dependencies must be used with care in a temporal relation, as we saw in Section 8.9. Although the instructor _ID_ may functionally determine the salary at any given point in time, obviously the salary can change over time. A **temporal functional dependency** _X_ → _Y_ holds on a relation schema _R_ if, for all legal instances _r_ of _R_, all snapshots of _r_ satisfy the functional dependency _X_ → _Y_.

Several proposals have been made for extending SQL to improve its support of temporal data, but at least until SQL:2008, SQL has not provided any special support for temporal data beyond the time-related data types and operations.

