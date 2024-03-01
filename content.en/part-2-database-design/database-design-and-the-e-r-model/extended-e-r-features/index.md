---
title: 'Extended E-R Features'
weight: 8
references:
    videos:
        - youtube:20WU801_KXw
        - youtube:mNW_BDkU9SM
    links:
        - https://www.geeksforgeeks.org/enhanced-er-model/
        - https://www.javatpoint.com/enhanced-er-model
    books:
        - b1:
            title: Entity-Relationship Modeling
            url: https://www.google.co.in/books/edition/Entity_Relationship_Modeling/ztioCAAAQBAJ?hl=en&gbpv=0
        - b2:
            title: An Extended Entity-Relationship Model
            url: https://www.google.co.in/books/edition/An_Extended_Entity_Relationship_Model/iP8NJ9UbZasC?hl=en
---

# Extended E-R Features

Although the basic E-R concepts can model most database features, some aspects of a database may be more aptly expressed by certain extensions to the basic E-R model. In this section, we discuss the extended E-R features of specializa- tion, generalization, higher- and lower-level entity sets, attribute inheritance, and aggregation.

To help with the discussions, we shall use a slightly more elaborate database schema for the university. In particular, we shall model the various people within a university by defining an entity set _person_, with attributes _ID_, _name_, and _address_.

## Specialization

An entity set may include subgroupings of entities that are distinct in some way from other entities in the set. For instance, a subset of entities within an entity set may have attributes that are not shared by all the entities in the entity set. The E-R model provides a means for representing these distinctive entity groupings.

As an example, the entity set _person_ may be further classified as one of the following:

• _employee_.

• _student_.

Each of these person types is described by a set of attributes that includes all the attributes of entity set _person_ plus possibly additional attributes. For exam- ple, _employee_ entities may be described further by the attribute _salary_, whereas _student_ entities may be described further by the attribute _tot cred_. The process of designating subgroupings within an entity set is called **specialization**. The spe- cialization of _person_ allows us to distinguish among person entities according to whether they correspond to employees or students: in general, a person could be an employee, a student, both, or neither.  

As another example, suppose the university divides students into two cate- gories: graduate and undergraduate. Graduate students have an office assigned to them. Undergraduate students are assigned to a residential college. Each of these student types is described by a set of attributes that includes all the attributes of the entity set _student_ plus additional attributes.

The university could create two specializations of _student_, namely _graduate_ and _undergraduate_. As we saw earlier, student entities are described by the attributes _ID_, _name_, _address_, and _tot cred_. The entity set _graduate_ would have all the attributes of _student_ and an additional attribute _office number_. The entity set _undergraduate_ would have all the attributes of _student_, and an additional attribute _residential college_.

We can apply specialization repeatedly to refine a design. For instance, uni- versity employees may be further classified as one of the following:

• _instructor_.

• _secretary_.

Each of these employee types is described by a set of attributes that includes all the attributes of entity set _employee_ plus additional attributes. For example, _instructor_ entities may be described further by the attribute _rank_ while _secretary_ entities are described by the attribute _hours per week_. Further, _secretary_ entities may participate in a relationship _secretary for_ between the _secretary_ and _employee_ entity sets, which identifies the employees who are assisted by a secretary.

An entity set may be specialized by more than one distinguishing feature. In our example, the distinguishing feature among employee entities is the job the em- ployee performs. Another, coexistent, specialization could be based on whether the person is a temporary (limited term) employee or a permanent employee, re- sulting in the entity sets _temporary employee_ and _permanent employee_. When more than one specialization is formed on an entity set, a particular entity may belong to multiple specializations. For instance, a given employee may be a temporary employee who is a secretary.

In terms of an E-R diagram, specialization is depicted by a hollow arrow-head pointing from the specialized entity to the other entity (see Figure 7.21). We refer to this relationship as the ISA relationship, which stands for “is a” and represents, for example, that an instructor “is a” employee.

The way we depict specialization in an E-R diagram depends on whether an entity may belong to multiple specialized entity sets or if it must belong to at most one specialized entity set. The former case (multiple sets permitted) is called **overlapping specialization**, while the latter case (at most one permitted) is called **disjoint specialization**. For an overlapping specialization (as is the case for _student_ and _employee_ as specializations of _person_), two separate arrows are used. For a disjoint specialization (as is the case for _instructor_ and _secretary_ as specializations of _employee_), a single arrow is used. The specialization relationship may also be referred to as a **superclass-subclass** relationship. Higher- and lower-level entity  

![Alt text](image-19.png)

sets are depicted as regular entity sets—that is, as rectangles containing the name of the entity set.

## Generalization

The refinement from an initial entity set into successive levels of entity subgroup- ings represents a **top-down** design process in which distinctions are made explicit. The design process may also proceed in a **bottom-up** manner, in which multiple entity sets are synthesized into a higher-level entity set on the basis of common features. The database designer may have first identified:

• _instructor_ entity set with attributes _instructor id_, _instructor name_, _instructor salary_, and _rank_.

• _secretary_ entity set with attributes _secretary id_, _secretary name_, _secretary salary_, and _hours per week_.

There are similarities between the _instructor_ entity set and the _secretary_ entity set in the sense that they have several attributes that are conceptually the same across the two entity sets: namely, the identifier, name, and salary attributes. This commonality can be expressed by **generalization**, which is a containment relationship that exists between a _higher-level_ entity set and one or more _lower-level_ entity sets. In our example, _employee_ is the higher-level entity set and _instructor_ and _secretary_ are lower-level entity sets. In this case, attributes that are conceptually the same had different names in the two lower-level entity sets. To create a generalization, the attributes must be given a common name and represented with the higher-level entity _person_. We can use the attribute names _ID_, _name_, _address_, as we saw in the example in Section 7.8.1.  

**298 Chapter 7 Database Design and the E-R Model**

Higher- and lower-level entity sets also may be designated by the terms **superclass** and **subclass**, respectively. The _person_ entity set is the superclass of the _employee_ and _student_ subclasses.

For all practical purposes, generalization is a simple inversion of specializa- tion. We apply both processes, in combination, in the course of designing the E-R schema for an enterprise. In terms of the E-R diagram itself, we do not distinguish between specialization and generalization. New levels of entity representation are distinguished (specialization) or synthesized (generalization) as the design schema comes to express fully the database application and the user require- ments of the database. Differences in the two approaches may be characterized by their starting point and overall goal.

Specialization stems from a single entity set; it emphasizes differences among entities within the set by creating distinct lower-level entity sets. These lower- level entity sets may have attributes, or may participate in relationships, that do not apply to all the entities in the higher-level entity set. Indeed, the reason a designer applies specialization is to represent such distinctive features. If _student_ and _employee_ have exactly the same attributes as _person_ entities, and participate in exactly the same relationships as _person_ entities, there would be no need to specialize the _person_ entity set.

Generalization proceeds from the recognition that a number of entity sets share some common features (namely, they are described by the same attributes and participate in the same relationship sets). On the basis of their commonalities, generalization synthesizes these entity sets into a single, higher-level entity set. Generalization is used to emphasize the similarities among lower-level entity sets and to hide the differences; it also permits an economy of representation in that shared attributes are not repeated.

## Attribute Inheritance

A crucial property of the higher- and lower-level entities created by specialization and generalization is **attribute inheritance**. The attributes of the higher-level entity sets are said to be **inherited** by the lower-level entity sets. For example, _student_ and _employee_ inherit the attributes of _person_. Thus, _student_ is described by its _ID_, _name_, and _address_ attributes, and additionally a _tot cred_ attribute; _employee_ is described by its _ID_, _name_, and _address_ attributes, and additionally a _salary_ attribute. Attribute inheritance applies through all tiers of lower-level entity sets; thus, _instructor_ and _secretary_, which are subclasses of _employee_, inherit the attributes _ID_, _name_, and _address_ from _person_, in addition to inheriting _salary_ from _employee_.

A lower-level entity set (or subclass) also inherits participation in the relation- ship sets in which its higher-level entity (or superclass) participates. Like attribute inheritance, participation inheritance applies through all tiers of lower-level en- tity sets. For example, suppose the _person_ entity set participates in a relationship _person dept_ with _department_. Then, the _student_, _employee_, _instructor_ and _secretary_ en- tity sets, which are subclasses of the _person_ entity set, also implicitly participate in the _person dept_ relationship with _department_. The above entity sets can participate in any relationships in which the _person_ entity set participates.  

Whether a given portion of an E-R model was arrived at by specialization or generalization, the outcome is basically the same:

• A higher-level entity set with attributes and relationships that apply to all of its lower-level entity sets.

• Lower-level entity sets with distinctive features that apply only within a particular lower-level entity set.

In what follows, although we often refer to only generalization, the properties that we discuss belong fully to both processes.

Figure 7.21 depicts a **hierarchy** of entity sets. In the figure, _employee_ is a lower-level entity set of _person_ and a higher-level entity set of the _instructor_ and _secretary_ entity sets. In a hierarchy, a given entity set may be involved as a lower- level entity set in only one ISA relationship; that is, entity sets in this diagram have only **single inheritance**. If an entity set is a lower-level entity set in more than one ISA relationship, then the entity set has **multiple inheritance**, and the resulting structure is said to be a _lattice_.

## Constraints on Generalizations

To model an enterprise more accurately, the database designer may choose to place certain constraints on a particular generalization. One type of constraint involves determining which entities can be members of a given lower-level entity set. Such membership may be one of the following:

• **Condition-defined.** In condition-defined lower-level entity sets, membership is evaluated on the basis of whether or not an entity satisfies an explicit condi- tion or predicate. For example, assume that the higher-level entity set _student_ has the attribute _student type_. All _student_ entities are evaluated on the defin- ing _student type_ attribute. Only those entities that satisfy the condition _student type_ \= “graduate” are allowed to belong to the lower-level entity set _graduate student_. All entities that satisfy the condition _student type_ \= “undergraduate”

are included in _undergraduate student_. Since all the lower-level entities are evaluated on the basis of the same attribute (in this case, on _student type_), this type of generalization is said to be **attribute-defined.**

• **User-defined.** User-defined lower-level entity sets are not constrained by a membership condition; rather, the database user assigns entities to a given entity set. For instance, let us assume that, after 3 months of employment, university employees are assigned to one of four work teams. We therefore represent the teams as four lower-level entity sets of the higher-level _employee_ entity set. A given employee is not assigned to a specific team entity auto- matically on the basis of an explicit defining condition. Instead, the user in charge of this decision makes the team assignment on an individual basis. The assignment is implemented by an operation that adds an entity to an entity set.  

A second type of constraint relates to whether or not entities may belong to more than one lower-level entity set within a single generalization. The lower- level entity sets may be one of the following:

• **Disjoint**. A _disjointness constraint_ requires that an entity belong to no more than one lower-level entity set. In our example, _student_ entity can satisfy only one condition for the _student type_ attribute; an entity can be either a graduate student or an undergraduate student, but cannot be both.

• **Overlapping**. In _overlapping generalizations_, the same entity may belong to more than one lower-level entity set within a single generalization. For an illustration, consider the employee work-team example, and assume that certain employees participate in more than one work team. A given employee may therefore appear in more than one of the team entity sets that are lower- level entity sets of _employee_. Thus, the generalization is overlapping.

In Figure 7.21, we assume a person may be both an employee and a student. We show this overlapping generalization via separate arrows: one from employee to person and another from student to person. However, the generalization of instructor and secretaries is disjoint. We show this using a single arrow.

A final constraint, the **completeness constraint** on a generalization or special- ization, specifies whether or not an entity in the higher-level entity set must belong to at least one of the lower-level entity sets within the generalization/specialization. This constraint may be one of the following:

• **Total generalization** or **specialization**. Each higher-level entity must belong to a lower-level entity set.

• **Partial generalization** or **specialization**. Some higher-level entities may not belong to any lower-level entity set.

Partial generalization is the default. We can specify total generalization in an E-R diagram by adding the keyword “total” in the diagram and drawing a dashed line from the keyword to the corresponding hollow arrow-head to which it applies (for a total generalization), or to the set of hollow arrow-heads to which it applies (for an overlapping generalization).

The _student_ generalization is total: All student entities must be either graduate or undergraduate. Because the higher-level entity set arrived at through general- ization is generally composed of only those entities in the lower-level entity sets, the completeness constraint for a generalized higher-level entity set is usually total. When the generalization is partial, a higher-level entity is not constrained to appear in a lower-level entity set. The work team entity sets illustrate a partial specialization. Since employees are assigned to a team only after 3 months on the job, some _employee_ entities may not be members of any of the lower-level team entity sets.

We may characterize the team entity sets more fully as a partial, overlapping specialization of _employee_. The generalization of _graduate student_ and _undergrad-_  

_uate student_ into _student_ is a total, disjoint generalization. The completeness and disjointness constraints, however, do not depend on each other. Constraint pat- terns may also be partial-disjoint and total-overlapping.

We can see that certain insertion and deletion requirements follow from the constraints that apply to a given generalization or specialization. For instance, when a total completeness constraint is in place, an entity inserted into a higher- level entity set must also be inserted into at least one of the lower-level entity sets. With a condition-defined constraint, all higher-level entities that satisfy the condition must be inserted into that lower-level entity set. Finally, an entity that is deleted from a higher-level entity set also is deleted from all the associated lower-level entity sets to which it belongs.

## Aggregation

One limitation of the E-R model is that it cannot express relationships among relationships. To illustrate the need for such a construct, consider the ternary relationship _proj guide_, which we saw earlier, between an _instructor_, _student_ and _project_ (see Figure 7.13).

Now suppose that each instructor guiding a student on a project is required to file a monthly evaluation report. We model the evaluation report as an entity _eval- uation_, with a primary key _evaluation id_. One alternative for recording the (_student_, _project_, _instructor_) combination to which an _evaluation_ corresponds is to create a quaternary (4-way) relationship set _eval for_ between _instructor_, _student_, _project_, and _evaluation_. (A quaternary relationship is required—a binary relationship between _student_ and _evaluation_, for example, would not permit us to represent the (_project_, _instructor_) combination to which an _evaluation_ corresponds.) Using the basic E-R modeling constructs, we obtain the E-R diagram of Figure 7.22. (We have omitted the attributes of the entity sets, for simplicity.)

It appears that the relationship sets _proj guide_ and _eval for_ can be combined into one single relationship set. Nevertheless, we should not combine them into a single relationship, since some _instructor_, _student_, _project_ combinations may not have an associated _evaluation_.

There is redundant information in the resultant figure, however, since every _instructor_, _student_, _project_ combination in _eval for_ must also be in _proj guide_. If the _evaluation_ were a value rather than a entity, we could instead make _evaluation_ a multivalued composite attribute of the relationship set _proj guide_. However, this alternative may not be be an option if an _evaluation_ may also be related to other entities; for example, each evaluation report may be associated with a _secretary_ who is responsible for further processing of the evaluation report to make scholarship payments.

The best way to model a situation such as the one just described is to use ag- gregation. **Aggregation** is an abstraction through which relationships are treated as higher-level entities. Thus, for our example, we regard the relationship set _proj guide_ (relating the entity sets _instructor_, _student_, and _project_) as a higher-level entity set called _proj guide_. Such an entity set is treated in the same manner as is any other entity set. We can then create a binary relationship _eval for_ between _proj_  

![Alt text](image-20.png)

_guide_ and _evaluation_ to represent which (_student_, _project_, _instructor_) combination an _evaluation_ is for. Figure 7.23 shows a notation for aggregation commonly used to represent this situation.

## Reduction to Relation Schemas

We are in a position now to describe how the extended E-R features can be translated into relation schemas.

### Representation of Generalization

There are two different methods of designing relation schemas for an E-R diagram that includes generalization. Although we refer to the generalization in Figure 7.21 in this discussion, we simplify it by including only the first tier of lower-level entity sets—that is, _employee_ and _student_. We assume that _ID_ is the primary key of _person_.

**1\.** Create a schema for the higher-level entity set. For each lower-level entity set, create a schema that includes an attribute for each of the attributes of that entity set plus one for each attribute of the primary key of the higher-level entity set. Thus, for the E-R diagram of Figure 7.21 (ignoring the _instructor_ and _secretary_ entity sets) we have three schemas:

_person_ (_ID_, _name, street, city_) 
_employee_ (_ID_, _salary_) 
_student_ (_ID_, _tot cred_)  

![Alt text](image-21.png)

The primary-key attributes of the higher-level entity set become primary- key attributes of the higher-level entity set as well as all lower-level entity sets. These can be seen underlined in the above example.

In addition, we create foreign-key constraints on the lower-level entity sets, with their primary-key attributes referencing the primary key of the relation created from the higher-level entity set. In the above example, the _ID_ attribute of _employee_ would reference the primary key of _person_, and similarly for _student_.

**2\.** An alternative representation is possible, if the generalization is disjoint and complete—that is, if no entity is a member of two lower-level entity sets directly below a higher-level entity set, and if every entity in the higher-level entity set is also a member of one of the lower-level entity sets. Here, we do not create a schema for the higher-level entity set. Instead, for each lower- level entity set, we create a schema that includes an attribute for each of the attributes of that entity set plus one for _each_ attribute of the higher-level entity set. Then, for the E-R diagram of Figure 7.21, we have two schemas:

_employee_ (_ID_, _name_, street_,_city_, _salary_) 
_student_ (_ID_, _name_, _street_,_city_, _tot cred_)

Both these schemas have _ID_, which is the primary-key attribute of the higher- level entity set _person_, as their primary key.  

One drawback of the second method lies in defining foreign-key constraints. To illustrate the problem, suppose we had a relationship set _R_ involving entity set _person_. With the first method, when we create a relation schema _R_ from the relationship set, we would also define a foreign-key constraint on _R_, referencing the schema _person_. Unfortunately, with the second method, we do not have a single relation to which a foreign-key constraint on _R_ can refer. To avoid this problem, we need to create a relation schema _person_ containing at least the primary-key attributes of the _person_ entity.

If the second method were used for an overlapping generalization, some values would be stored multiple times, unnecessarily. For instance, if a person is both an employee and a student, values for _street_ and _city_ would be stored twice.

If the generalization were disjoint but not complete—that is, if some person is neither an employee nor a student—then an extra schema

_person_ (_ID_, _name, street, city_)

would be required to represent such people. However, the problem with foreign- key constraints mentioned above would remain. As an attempt to work around the problem, suppose employees and students are additionally represented in the _person_ relation. Unfortunately, name, street, and city information would then be stored redundantly in the _person_ relation and the _student_ relation for students, and similarly in the _person_ relation and the _employee_ relation for employees. That suggests storing name, street, and city information only in the _person_ relation and removing that information from _student_ and _employee_. If we do that, the result is exactly the first method we presented.

### Representation of Aggregation

Designing schemas for an E-R diagram containing aggregation is straightforward. Consider the diagram of Figure 7.23. The schema for the relationship set _eval for_ between the aggregation of _proj guide_ and the entity set _evaluation_ includes an attribute for each attribute in the primary keys of the entity set _evaluation_, and the relationship set _proj guide_. It also includes an attribute for any descriptive attributes, if they exist, of the relationship set _eval for_. We then transform the relationship sets and entity sets within the aggregated entity set following the rules we have already defined.

The rules we saw earlier for creating primary-key and foreign-key constraints on relationship sets can be applied to relationship sets involving aggregations as well, with the aggregation treated like any other entity set. The primary key of the aggregation is the primary key of its defining relationship set. No separate relation is required to represent the aggregation; the relation created from the defining relationship is used instead.

