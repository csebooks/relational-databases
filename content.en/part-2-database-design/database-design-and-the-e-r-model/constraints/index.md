---
title: 'Constraints'
weight: 3
references:
    videos:
        - youtube:uPOGPL2C0_8
        - youtube:xEqvxU3LuS0
    links:
        - https://www.w3schools.com/sql/sql_constraints.asp
        - https://en.wikipedia.org/wiki/Constraint
    books:
        - b1:
            title: Theory of Constraints Handbook
            url: https://www.google.co.in/books/edition/Theory_of_Constraints_Handbook/18biX-wLjE8C?hl=en&gbpv=0&bsq=Constraints%20books
        - b2:
           title: The Triple Constraints in Project Management
            url: https://www.google.co.in/books/edition/The_Triple_Constraints_in_Project_Manage/ihhFDwAAQBAJ?hl=en&gbpv=0
---

# Constraints

An E-R enterprise schema may define certain constraints to which the contents of a database must conform. In this section, we examine mapping cardinalities and participation constraints.

## Mapping Cardinalities

**Mapping cardinalities**, or cardinality ratios, express the number of entities to which another entity can be associated via a relationship set.

Mapping cardinalities are most useful in describing binary relationship sets, although they can contribute to the description of relationship sets that involve more than two entity sets. In this section, we shall concentrate on only binary relationship sets.

For a binary relationship set _R_ between entity sets _A_ and _B_, the mapping cardinality must be one of the following:

• **One-to-one**. An entity in _A_ is associated with _at most_ one entity in _B_, and an entity in _B_ is associated with _at most_ one entity in _A_. (See Figure 7.5a.)

• **One-to-many**. An entity in _A_ is associated with any number (zero or more) of entities in _B_. An entity in _B_, however, can be associated with _at most_ one entity in _A_. (See Figure 7.5b.)

![Alt text](image-4.png)

![Alt text](image-5.png)

• **Many-to-one**. An entity in _A_ is associated with _at most_ one entity in _B_. An entity in _B_, however, can be associated with any number (zero or more) of entities in _A_. (See Figure 7.6a.)

• **Many-to-many**. An entity in _A_ is associated with any number (zero or more) of entities in _B_, and an entity in _B_ is associated with any number (zero or more) of entities in _A_. (See Figure 7.6b.)

The appropriate mapping cardinality for a particular relationship set obviously depends on the real-world situation that the relationship set is modeling.

As an illustration, consider the _advisor_ relationship set. If, in a particular university, a student can be advised by only one instructor, and an instructor can advise several students, then the relationship set from _instructor_ to _student_ is one-to-many. If a student can be advised by several instructors (as in the case of students advised jointly), the relationship set is many-to-many.

## Participation Constraints

The participation of an entity set _E_ in a relationship set _R_ is said to be **total** if every entity in _E_ participates in at least one relationship in _R_. If only some entities in _E_ participate in relationships in _R_, the participation of entity set _E_ in relationship _R_ is said to be **partial**. In Figure 7.5a, the participation of _B_ in the relationship set is total while the participation of _A_ in the relationship set is partial. In Figure 7.5b, the participation of both _A_ and _B_ in the relationship set are total.

For example, we expect every _student_ entity to be related to at least one instructor through the _advisor_ relationship. Therefore the participation of _student_ in the relationship set _advisor_ is total. In contrast, an _instructor_ need not advise any students. Hence, it is possible that only some of the _instructor_ entities are related to the _student_ entity set through the _advisor_ relationship, and the participation of _instructor_ in the _advisor_ relationship set is therefore partial.  

## Keys

We must have a way to specify how entities within a given entity set are distin- guished. Conceptually, individual entities are distinct; from a database perspec- tive, however, the differences among them must be expressed in terms of their attributes.

Therefore, the values of the attribute values of an entity must be such that they can _uniquely identify_ the entity. In other words, no two entities in an entity set are allowed to have exactly the same value for all attributes.

The notion of a _key_ for a relation schema, as defined in Section 2.3, applies directly to entity sets. That is, a key for an entity is a set of attributes that suffice to distinguish entities from each other. The concepts of superkey, candidate key, and primary key are applicable to entity sets just as they are applicable to relation schemas.

Keys also help to identify relationships uniquely, and thus distinguish rela- tionships from each other. Below, we define the corresponding notions of keys for relationships.

The primary key of an entity set allows us to distinguish among the various entities of the set. We need a similar mechanism to distinguish among the various relationships of a relationship set.

Let _R_ be a relationship set involving entity sets _E1_, E2_, . . . , En_. Let _primary- key_(_Ei_ ) denote the set of attributes that forms the primary key for entity set _Ei_ . Assume for now that the attribute names of all primary keys are unique. The composition of the primary key for a relationship set depends on the set of attributes associated with the relationship set _R_.

If the relationship set _R_ has no attributes associated with it, then the set of attributes

primary-key(E~1~) ∪ primary-key(E~2~) ∪···∪ primary-key(E~n~) ∪ {a~1~, a~2~,..., a~m~}

describes an individual relationship in set _R_. If the relationship set _R_ has attributes _a1_,_a2_, . . . , am_ associated with it, then
the set of attributes

_primary-key_(_E_1) ∪ _primary-key_(_E_2) ∪ · · · ∪ _primary-key_(_En_) ∪ {_a_1_, a_2_, . . . , am_}

describes an individual relationship in set _R_. In both of the above cases, the set of attributes

primary-key(E~1~) ∪ primary-key(E~2~) ∪···∪ primary-key(E~n~)

forms a superkey for the relationship set. If the attribute names of primary keys are not unique across entity sets, the

attributes are renamed to distinguish them; the name of the entity set combined with the name of the attribute would form a unique name. If an entity set par- ticipates more than once in a relationship set (as in the _prereq_ relationship in Section 7.2.2), the role name is used instead of the name of the entity set, to form a unique attribute name.

The structure of the primary key for the relationship set depends on the mapping cardinality of the relationship set. As an illustration, consider the entity sets _instructor_ and _student_, and the relationship set _advisor_, with attribute _date_, in Section 7.2.2. Suppose that the relationship set is many-to-many. Then the primary key of _advisor_ consists of the union of the primary keys of _instructor_ and _student_. If the relationship is many-to-one from _student_ to _instructor_—that is, each student can have have at most one advisor—then the primary key of _advisor_ is simply the primary key of _student_. However, if an instructor can advise only one student— that is, if the _advisor_ relationship is many-to-one from _instructor_ to _student_—then the primary key of _advisor_ is simply the primary key of _instructor_. For one-to-one relationships either candidate key can be used as the primary key.

For nonbinary relationships, if no cardinality constraints are present then the superkey formed as described earlier in this section is the only candidate key, and it is chosen as the primary key. The choice of the primary key is more complicated if cardinality constraints are present. Since we have not discussed how to specify cardinality constraints on nonbinary relations, we do not discuss this issue further in this chapter. We consider the issue in more detail later, in Sections 7.5.5 and 8.4.

