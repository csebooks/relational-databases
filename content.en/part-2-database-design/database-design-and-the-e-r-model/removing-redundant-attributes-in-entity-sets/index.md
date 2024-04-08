---
title: 'Removing Redundant Attributes in Entity Sets'
weight: 4
references:
    videos:
        - youtube:uWJToJndkKQ
        - youtube:uWJToJndkKQ
    links:
        - https://www.javatpoint.com/redundancy-in-dbms
        - https://www.studocu.com/en-us/document/metropolitan-state-university-of-denver/database-management-systems/removing-redundant-attributes-in-entity-sets-in-database-management-systems/52529493
    books:
        - b1:
            title: Database Systems
            url: https://www.google.co.in/books/edition/Database_Systems_The_Complete_Book/-5mqs4hQIBcC?hl=en&gbpv=0
        - b2:
            title: Sql Server 2005 Black Book
            url:https://www.google.co.in/books/edition/Sql_Server_2005_Black_Book_New_Ed_With_C/Klj20GNs5egC?hl=en&gbpv=0
---

# Removing Redundant Attributes in Entity Sets

When we design a database using the E-R model, we usually start by identifying those entity sets that should be included. For example, in the university organiza- tion we have discussed thus far, we decided to include such entity sets as _student_, _instructor_, etc. Once the entity sets are decided upon, we must choose the appro- priate attributes. These attributes are supposed to represent the various values we want to capture in the database. In the university organization, we decided that for the _instructor_ entity set, we will include the attributes _ID_, _name_, _dept name_, and _salary_. We could have added the attributes: _phone number_, _office number_, _home page_, etc. The choice of what attributes to include is up to the designer, who has a good understanding of the structure of the enterprise. Once the entities and their corresponding attributes are chosen, the relationship sets among the various entities are formed. These relationship sets may result in a situation where attributes in the various entity sets are redundant and need to be removed from the original entity sets. To illustrate, consider the entity sets _instructor_ and _department_:

• The entity set _instructor_ includes the attributes _ID_, _name_, _dept name_, and _salary_, with _ID_ forming the primary key.

• The entity set _department_ includes the attributes _dept name_, _building_, and _bud- get_, with _dept name_ forming the primary key.

We model the fact that each instructor has an associated department using a relationship set _inst dept_ relating _instructor_ and _department_.  

The attribute _dept name_ appears in both entity sets. Since it is the primary key for the entity set _department_, it is redundant in the entity set _instructor_ and needs to be removed.

Removing the attribute _dept name_ from the _instructor_ entity set may appear rather unintuitive, since the relation _instructor_ that we used in the earlier chap- ters had an attribute _dept name_. As we shall see later, when we create a relational schema from the E-R diagram, the attribute _dept name_ in fact gets added to the relation _instructor_, but only if each instructor has at most one associated depart- ment. If an instructor has more than one associated department, the relationship between instructors and departments is recorded in a separate relation _inst dept_.

Treating the connection between instructors and departments uniformly as a relationship, rather than as an attribute of _instructor_, makes the logical relationship explicit, and helps avoid a premature assumption that each instructor is associated with only one department.

Similarly, the _student_ entity set is related to the _department_ entity set through the relationship set _student dept_ and thus there is no need for a _dept name_ attribute in _student_.

As another example, consider course offerings (sections) along with the time slots of the offerings. Each time slot is identified by a _time slot id_, and has associated with it a set of weekly meetings, each identified by a day of the week, start time, and end time. We decide to model the set of weekly meeting times as a multivalued composite attribute. Suppose we model entity sets _section_ and _time slot_ as follows:

• The entity set _section_ includes the attributes _course id_, _sec id_, _semester_, _year_, _building_, _room number_, and _time slot id_, with (_course id_, _sec id_, _year_, _semester_) forming the primary key.

• The entity set _time slot_ includes the attributes _time slot id_, which is the primary key,4 and a multivalued composite attribute {(_day, start time, end time_)}.5

These entities are related through the relationship set _sec time slot_. The attribute _time slot id_ appears in both entity sets. Since it is the primarykey for the entity set _time slot_, it is redundant in the entity set _section_ and needs to be removed.

As a final example, suppose we have an entity set _classroom_, with attributes _building_, _room number_, and _capacity_, with _building_ and _room number_ forming the primary key. Suppose also that we have a relationship set _sec class_ that relates _section_ to _classroom_. Then the attributes _{building_, _room number}_ are redundant in the entity set _section_.

A good entity-relationship design does not contain redundant attributes. For our university example, we list the entity sets and their attributes below, with primary keys underlined:

• **classroom**: with attributes (_building_, _room number_, _capacity_).

• **department**: with attributes (_dept name_, _building_, _budget_).

• **course**: with attributes (_course id_, _title_, _credits_).

• **instructor**: with attributes (_ID_, _name_, _salary_).

• **section:** with attributes (_course id_, _sec id_, _semester_, _year_).

• **student**: with attributes (_ID_, _name_, _tot cred_).

• **time slot**: with attributes (_time slot id_, {(_day_, _start time_, _end time_) }).

The relationship sets in our design are listed below:

• **inst dept**: relating instructors with departments.

• **stud dept**: relating students with departments.

• **teaches**: relating instructors with sections.

• **takes**: relating students with sections, with a descriptive attribute _grade_.

• **course dept**: relating courses with departments.

• **sec course**: relating sections with courses.

• **sec class**: relating sections with classrooms.

• **sec time slot**: relating sections with time slots.

• **advisor**: relating students with instructors.

• **prereq**: relating courses with prerequisite courses.

You can verify that none of the entity sets has any attribute that is made redundant by one of the relationship sets. Further, you can verify that all the information (other than constraints) in the relational schema for our university database, which we saw earlier in Figure 2.8 in Chapter 2, has been captured by the above design, but with several attributes in the relational design replaced by relationships in the E-R design.

