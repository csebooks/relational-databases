---
title: 'Reduction to Relational Schemas'
weight: 6\
references:
    videos:
        - youtube:K2uQFfW5TXA&t=10s
        - youtube:8P435Ftkd54
    links:
        - https://www.exploredatabase.com/2016/04/convert-entity-relationship-diagram-to-relation-schema-exercise5.html
        - https://www.javatpoint.com/dbms-reduction-of-er-diagram-into-table
    books:
        - b1:
            title: SQL and Relational Theory
            url: https://www.google.co.in/books/edition/SQL_and_Relational_Theory/Ew06OZtjuJEC?hl=en&gbpv=0
        - b2:
            title: Information Modeling and Relational Databases
            url: https://www.google.co.in/books/edition/Information_Modeling_and_Relational_Data/UyCUjuuJNGgC?hl=en&gbpv=0
---

# Reduction to Relational Schemas

We can represent a database that conforms to an E-R database schema by a col- lection of relation schemas. For each entity set and for each relationship set in the database design, there is a unique relation schema to which we assign the name of the corresponding entity set or relationship set.

Both the E-R model and the relational database model are abstract, logical representations of real-world enterprises. Because the two models employ similar design principles, we can convert an E-R design into a relational design.

In this section, we describe how an E-R schema can be represented by relation schemas and how constraints arising from the E-R design can be mapped to constraints on relation schemas.

### Representation of Strong Entity Sets with Simple Attributes

Let _E_ be a strong entity set with only simple descriptive attributes a1, a2, . . . , an. We represent this entity by a schema called _E_ with _n_ distinct attributes. Each tuple in a relation on this schema corresponds to one entity of the entity set _E_.

For schemas derived from strong entity sets, the primary key of the entity set serves as the primary key of the resulting schema. This follows directly from the fact that each tuple corresponds to a specific entity in the entity set.

As an illustration, consider the entity set _student_ of the E-R diagram in Fig- ure 7.15. This entity set has three attributes: _ID_, _name_, _tot cred_. We represent this entity set by a schema called _student_ with three attributes:

_student_ (_ID_, _name_, _tot_cred_)

Note that since student _ID_ is the primary key of the entity set, it is also the primary key of the relation schema.

Continuing with our example, for the E-R diagram in Figure 7.15, all the strong entity sets, except _time slot_, have only simple attributes. The schemas derived from these strong entity sets are:  

classroom (building, room number, capacity)
department (dept name, building, budget)
course (course id, title, credits)
instructor (ID, name, salary)
student (ID, name, tot cred)

As you can see, both the _instructor_ and _student_ schemas are different from the schemas we have used in the previous chapters (they do not contain the attribute _dept name_). We shall revisit this issue shortly.

### 7.6.2 Representation of Strong Entity Sets with Complex Attributes

When a strong entity set has nonsimple attributes, things are a bit more complex. We handle composite attributes by creating a separate attribute for each of the component attributes; we do not create a separate attribute for the composite attribute itself. To illustrate, consider the version of the _instructor_ entity set de- picted in Figure 7.11. For the composite attribute _name_, the schema generated for _instructor_ contains the attributes _first name_, _middle name_, and _last name_; there is no separate attribute or schema for _name_. Similarly, for the composite attribute _address_, the schema generated contains the attributes _street_, _city_, _state_, and _zip code_. Since _street_ is a composite attribute it is replaced by _street number_, _street name_, and _apt number_. We revisit this matter in Section 8.2.

Multivalued attributes are treated differently from other attributes. We have seen that attributes in an E-R diagram generally map directly into attributes for the appropriate relation schemas. Multivalued attributes, however, are an exception; new relation schemas are created for these attributes, as we shall see shortly.

Derived attributes are not explicitly represented in the relational data model. However, they can be represented as “methods” in other data models such as the object-relational data model, which is described later in Chapter 22.

The relational schema derived from the version of entity set _instructor_ with complex attributes, without including the multivalued attribute, is thus:

instructor (ID, first_name, middle_name, last_name,street_number,street_name, apt_number,city,state, zip_code, date_of_birth)

For a multivalued attribute _M_, we create a relation schema _R_ with an attribute _A_ that corresponds to _M_ and attributes corresponding to the primary key of the entity set or relationship set of which _M_ is an attribute.

As an illustration, consider the E-R diagram in Figure 7.11 that depicts the entity set _instructor_, which includes the multivalued attribute _phone number_. The primary key of _instructor_ is _ID_. For this multivalued attribute, we create a relation schema _instructor phone_ (_ID_, _phone number_)  

Each phone number of an instructor is represented as a unique tuple in the relation on this schema. Thus, if we had an instructor with _ID_ 22222, and phone numbers 555-1234 and 555-4321, the relation _instructor phone_ would have two tuples (22222, 555-1234) and (22222, 555-4321).

We create a primary key of the relation schema consisting of all attributes of the schema. In the above example, the primary key consists of both attributes of the relation _instructor phone_.

In addition, we create a foreign-key constraint on the relation schema created from the multivalued attribute, with the attribute generated from the primary key of the entity set referencing the relation generated from the entity set. In the above example, the foreign-key constraint on the _instructor phone_ relation would be that attribute _ID_ references the _instructor_ relation.

In the case that an entity set consists of only two attributes — a single primary- key attribute _B_ and a single multivalued attribute _M_ — the relation schema for the entity set would contain only one attribute, namely the primary-key attribute _B_. We can drop this relation, while retaining the relation schema with the attribute _B_ and attribute _A_ that corresponds to _M_.

To illustrate, consider the entity set _time slot_ depicted in Figure 7.15. Here, _time slot id_ is the primary key of the _time slot_ entity set and there is a single multivalued

attribute that happens also to be composite. The entity set can be represented by just the following schema created from the multivalued composite attribute:

_time slot_ (_time slot id_, _day_, _start time_, _end time_)

Although not represented as a constraint on the E-R diagram, we know that there cannot be two meetings of a class that start at the same time of the same day-of- the-week but end at different times; based on this constraint, _end time_ has been omitted from the primary key of the _time slot_ schema.

The relation created from the entity set would have only a single attribute _time slot id_; the optimization of dropping this relation has the benefit of simplifying

the resultant database schema, although it has a drawback related to foreign keys, which we briefly discuss in Section 7.6.4.

### 7.6.3 Representation of Weak Entity Sets

Let _A_ be a weak entity set with attributes _a_1_, a_2_, . . . , am_. Let _B_ be the strong entity set on which _A_ depends. Let the primary key of _B_ consist of attributes _b_1_, b_2_, . . . , bn_. We represent the entity set _A_ by a relation schema called _A_ with one attribute for each member of the set:

_{a _1_, a _2_, . . . , am}_ ∪ _{b _1_, b _2_, . . . , bn}_

For schemas derived from a weak entity set, the combination of the pri- mary key of the strong entity set and the discriminator of the weak entity set serves as the primary key of the schema. In addition to creating a primary key, we also create a foreign-key constraint on the relation _A_, specifying that the attributes _b _1_, b _2_, . . . , bn_ reference the primary key of the relation _B_. The foreign- key constraint ensures that for each tuple representing a weak entity, there is a corresponding tuple representing the corresponding strong entity.

As an illustration, consider the weak entity set _section_ in the E-R diagram of Figure 7.15. This entity set has the attributes: _sec id_, _semester_, and _year_. The primary key of the _course_ entity set, on which _section_ depends, is _course id_. Thus, we represent _section_ by a schema with the following attributes:

_section_ (_course id_, _sec id_, _semester_, _year_)

The primary key consists of the primary key of the entity set _course_, along with the discriminator of _section_, which is _sec id_, _semester_, and _year_. We also create a foreign-key constraint on the _section_ schema, with the attribute _course id_ refer- encing the primary key of the _course_ schema, and the integrity constraint “on delete cascade”.7 Because of the “on delete cascade” specification on the foreign key constraint, if a _course_ entity is deleted, then so are all the associated _section_ entities.

### 7.6.4 Representation of Relationship Sets

Let _R_ be a relationship set, let _a_1_, a_2_, . . . , am_ be the set of attributes formed by the union of the primary keys of each of the entity sets participating in _R_, and let the descriptive attributes (if any) of _R_ be _b_1_, b_2_, . . . , bn_. We represent this relationship set by a relation schema called _R_ with one attribute for each member of the set:

_{a _1_, a _2_, . . . , am}_ ∪ _{b _1_, b _2_, . . . , bn}_

We described earlier, in Section 7.3.3, how to choose a primary key for a binary relationship set. As we saw in that section, taking all the primary-key attributes from all the related entity sets serves to identify a particular tuple, but for one-to- one, many-to-one, and one-to-many relationship sets, this turns out to be a larger set of attributes than we need in the primary key. The primary key is instead chosen as follows:

• For a binary many-to-many relationship, the union of the primary-key at- tributes from the participating entity sets becomes the primary key.

• For a binary one-to-one relationship set, the primary key of either entity set can be chosen as the primary key. The choice can be made arbitrarily.

• For a binary many-to-one or one-to-many relationship set, the primary key of the entity set on the “many” side of the relationship set serves as the primary key.

• For an _n_\-ary relationship set without any arrows on its edges, the union of the primary key-attributes from the participating entity sets becomes the primary key.

• For an _n_\-ary relationship set with an arrow on one of its edges, the primary keys of the entity sets not on the “arrow” side of the relationship set serve as the primary key for the schema. Recall that we allowed only one arrow out of a relationship set.

We also create foreign-key constraints on the relation schema _R_ as follows: For each entity set _Ei_ related to relationship set _R_, we create a foreign-key con- straint from relation schema _R_, with the attributes of _R_ that were derived from primary-key attributes of _Ei_ referencing the primary key of the relation schema representing _Ei_ .

As an illustration, consider the relationship set _advisor_ in the E-R diagram of Figure 7.15. This relationship set involves the following two entity sets:

• _instructor_ with the primary key _ID_.

• _student_ with the primary key _ID_.

Since the relationship set has no attributes, the _advisor_ schema has two attributes, the primary keys of _instructor_ and _student_. Since both attributes have the same name, we rename them _i ID_ and s _ID_. Since the _advisor_ relationship set is many- to-one from _student_ to _instructor_ the primary key for the _advisor_ relation schema is _s ID_.

We also create two foreign-key constraints on the _advisor_ relation, with at- tribute _i ID_ referencing the primary key of _instructor_ and attribute _s ID_ referencing the primary key of _student_.

Continuing with our example, for the E-R diagram in Figure 7.15, the schemas derived from a relationship set are depicted in Figure 7.16.

Observe that for the case of the relationship set _prereq_, the role indicators associated with the relationship are used as attribute names, since both roles refer to the same relation _course_.

Similar to the case of _advisor_, the primary key for each of the relations _sec course_, _sec time slot_, _sec class_, _inst dept_, _stud dept_ and _course dept_ consists of the primary key of only one of the two related entity sets, since each of the corresponding relationships is many-to-one.

Foreign keys are not shown in Figure 7.16, but for each of the relations in the figure there are two foreign-key constraints, referencing the two relations created from the two related entity sets. Thus, for example, _sec course_ has foreign keys referencing _section_ and _classroom_, _teaches_ has foreign keys referencing _instructor_ and _section_, and _takes_ has foreign keys referencing _student_ and _section_.

The optimization that allowed us to create only a single relation schema from the entity set _time slot_, which had a multivalued attribute, prevents the creation of a foreign key from the relation schema _sec time slot_ to the relation created from entity set _time slot_, since we dropped the relation created from the entity set _time_ 

teaches (ID, course id, sec id, semester, year)
takes (ID, course id, sec id, semester, year, grade)
prereq (course id, prereq id)
advisor (s ID, i ID)
sec course (course id, sec id, semester, year)
sec time slot (course id, sec id, semester, year, time slot id)
sec class (course id, sec id, semester, year, building, room number)
inst dept (ID, dept name)
stud dept (ID, dept name)
course dept (course id, dept name)

**Figure 7.16** Schemas derived from relationship sets in the E-R diagram in Figure 7.15.

_slot_. We retained the relation created from the multivalued attribute, and named it _time slot_, but this relation may potentially have no tuples corresponding to a _time slot id_, or may have multiple tuples corresponding to a _time slot id_; thus, _time slot id_ in _sec time slot_ cannot reference this relation.

The astute reader may wonder why we have not seen the schemas _sec course_, _sec time slot_, _sec class_, _inst dept_, _stud dept_, and _course dept_ in the previous chapters. The reason is that the algorithm we have presented thus far results in some schemas that can be either eliminated or combined with other schemas. We ex- plore this issue next.

#### 7.6.4.1 Redundancy of Schemas

A relationship set linking a weak entity set to the corresponding strong entity set is treated specially. As we noted in Section 7.5.6, these relationships are many-to- one and have no descriptive attributes. Furthermore, the primary key of a weak entity set includes the primary key of the strong entity set. In the E-R diagram of Figure 7.14, the weak entity set _section_ is dependent on the strong entity set _course_ via the relationship set _sec course_. The primary key of _section_ is _{course id_, _sec id_, _semester_, _year}_ and the primary key of _course_ is _course id_. Since _sec course_ has no descriptive attributes, the _sec course_ schema has attributes _course id_, _sec id_, _semester_, and _year_. The schema for the entity set _section_ includes the attributes _course id_, _sec id_, _semester_, and _year_ (among others). Every (_course id_, _sec id_, _semester_, _year_) combination in a _sec course_ relation would also be present in the relation on schema _section_, and vice versa. Thus, the _sec course_ schema is redundant.

In general, the schema for the relationship set linking a weak entity set to its corresponding strong entity set is redundant and does not need to be present in a relational database design based upon an E-R diagram.

#### 7.6.4.2 Combination of Schemas

Consider a many-to-one relationship set _AB_ from entity set _A_ to entity set _B_. Using our relational-schema construction algorithm outlined previously, we get three schemas: _A_, _B_, and _AB_. Suppose further that the participation of _A_ in the relationship is total; that is, every entity _a_ in the entity set _B_ must participate in the relationship _AB_. Then we can combine the schemas _A_ and _AB_ to form a single schema consisting of the union of attributes of both schemas. The primary key of the combined schema is the primary key of the entity set into whose schema the relationship set schema was merged.

To illustrate, let’s examine the various relations in the E-R diagram of Fig- ure 7.15 that satisfy the above criteria:

• _inst dept_. The schemas _instructor_ and _department_ correspond to the entity sets _A_ and _B_, respectively. Thus, the schema _inst dept_ can be combined with the _instructor_ schema. The resulting _instructor_ schema consists of the attributes _{ID_, _name_, _dept name_, _salary}_.

• _stud dept_. The schemas _student_ and _department_ correspond to the entity sets _A_ and _B_, respectively. Thus, the schema _stud dept_ can be combined with the _student_ schema. The resulting _student_ schema consists of the attributes _{ID_, _name_, _dept name_, _tot cred}_.

• _course dept_. The schemas _course_ and _department_ correspond to the entity sets _A_ and _B_, respectively. Thus, the schema _course dept_ can be combined with the _course_ schema. The resulting _course_ schema consists of the attributes _{course id_, _title_, _dept name_, _credits}_.

• _sec class_. The schemas _section_ and _classroom_ correspond to the entity sets _A_ and _B_, respectively. Thus, the schema _sec class_ can be combined with the _section_ schema. The resulting _section_ schema consists of the attributes _{course id_, _sec id_, _semester_, _year_, _building_, _room number}_.

• _sec time slot_. The schemas _section_ and _time slot_ correspond to the entity sets _A_ and _B_ respectively, Thus, the schema _sec time slot_ can be combined with the _section_ schema obtained in the previous step. The resulting _section_ schema consists of the attributes _{course id_, _sec id_, _semester_, _year_, _building_, _room number_, _time slot id}_.

In the case of one-to-one relationships, the relation schema for the relationship set can be combined with the schemas for either of the entity sets.

We can combine schemas even if the participation is partial by using null values. In the above example, if _inst dept_ were partial, then we would store null values for the _dept name_ attribute for those instructors who have no associated department.

Finally, we consider the foreign-key constraints that would have appeared in the schema representing the relationship set. There would have been foreign-key constraints referencing each of the entity sets participating in the relationship set. We drop the constraint referencing the entity set into whose schema the relationship set schema is merged, and add the other foreign-key constraints to the combined schema. For example, _inst dept_ has a foreign key constraint of the attribute _dept name_ referencing the _department_ relation. This foreign constraint is added to the _instructor_ relation when the schema for _inst dept_ is merged into _instructor_.

