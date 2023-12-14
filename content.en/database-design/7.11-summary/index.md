---
title: 7.11 Summary
weight: 13
---

7.11 Summary## 7.11 Summary

• Database design mainly involves the design of the database schema. The **entity-relationship (E-R)** data model is a widely used data model for database design. It provides a convenient graphical representation to view data, rela- tionships, and constraints.

• The E-R model is intended primarily for the database-design process. It was developed to facilitate database design by allowing the specification of an  

**enterprise schema**. Such a schema represents the overall logical structure of the database. This overall structure can be expressed graphically by an **E-R diagram**.

• An **entity** is an object that exists in the real world and is distinguishable from other objects. We express the distinction by associating with each entity a set of attributes that describes the object.

• A **relationship** is an association among several entities. A **relationship set** is a collection of relationships of the same type, and an **entity set** is a collection of entities of the same type.

• The terms **superkey**, **candidate key**, and **primary key** apply to entity and relationship sets as they do for relation schemas. Identifying the primary key of a relationship set requires some care, since it is composed of attributes from one or more of the related entity sets.

• **Mapping cardinalities** express the number of entities to which another entity can be associated via a relationship set.

• An entity set that does not have sufficient attributes to form a primary key is termed a **weak entity set**. An entity set that has a primary key is termed a **strong entity set**.

• The various features of the E-R model offer the database designer numer- ous choices in how to best represent the enterprise being modeled. Concepts and objects may, in certain cases, be represented by entities, relationships, or attributes. Aspects of the overall structure of the enterprise may be best described by using weak entity sets, generalization, specialization, or aggre- gation. Often, the designer must weigh the merits of a simple, compact model versus those of a more precise, but more complex, one.

• A database design specified by an E-R diagram can be represented by a collection of relation schemas. For each entity set and for each relationship set in the database, there is a unique relation schema that is assigned the name of the corresponding entity set or relationship set. This forms the basis for deriving a relational database design from an E-R diagram.

• **Specialization** and **generalization** define a containment relationship be- tween a higher-level entity set and one or more lower-level entity sets. Spe- cialization is the result of taking a subset of a higher-level entity set to form a lower-level entity set. Generalization is the result of taking the union of two or more disjoint (lower-level) entity sets to produce a higher-level entity set. The attributes of higher-level entity sets are inherited by lower-level entity sets.

• **Aggregation** is an abstraction in which relationship sets (along with their as- sociated entity sets) are treated as higher-level entity sets, and can participate in relationships.

• UML is a popular modeling language. UML class diagrams are widely used for modeling classes, as well as for general purpose data modeling.  

