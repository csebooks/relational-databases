---
title: 'PART TWO: DATABASE DESIGN'
weight: 7
---

  

**PART 2**

**DATABASE DESIGN** Database systems are designed to manage large bodies of information. These large bodies of information do not exist in isolation. They are part of the operation of some enterprise whose end product may be information from the database or may be some device or service for which the database plays only a supporting role.

The first two chapters of this part focus on the design of database schemas. The entity-relationship (E-R) model described in Chapter 7 is a high-level data model. Instead of representing all data in tables, it distinguishes between basic objects, called _entities_, and _relationships_ among these objects. It is often used as a first step in database-schema design.

Relational database design—the design of the relational schema— was cov- ered informally in earlier chapters. There are, however, principles that can be used to distinguish good database designs from bad ones. These are formal- ized by means of several “normal forms” that offer different trade-offs between the possibility of inconsistencies and the efficiency of certain queries. Chapter 8 describes the formal design of relational schemas.

The design of a complete database application environment that meets the needs of the enterprise being modeled requires attention to a broader set of issues, many of which are covered in Chapter 9. This chapter first covers the design of Web-based interfaces to applications. The chapter then describes how large applications are architected using multiple layers of abstraction. Finally, the chapter provides a detailed discussion of security at the application and database levels.

**257**  

_This page intentionally left blank_  

**_C H A P T E R_7 Database Design and the E-R Model**

Up to this point in the text, we have assumed a given database schema and studied how queries and updates are expressed. We now consider how to design a database schema in the first place. In this chapter, we focus on the entity- relationship data model (E-R), which provides a means of identifying entities to be represented in the database and how those entities are related. Ultimately, the database design will be expressed in terms of a relational database design and an associated set of constraints. We show in this chapter how an E-R design can be transformed into a set of relation schemas and how some of the constraints can be captured in that design. Then, in Chapter 8, we consider in detail whether a set of relation schemas is a good or bad database design and study the process of creating good designs using a broader set of constraints. These two chapters cover the fundamental concepts of database design.

**7.1 Overview of the Design Process**

The task of creating a database application is a complex one, involving design of the database schema, design of the programs that access and update the data, and design of a security scheme to control access to data. The needs of the users play a central role in the design process. In this chapter, we focus on the design of the database schema, although we briefly outline some of the other design tasks later in the chapter.

The design of a complete database application environment that meets the needs of the enterprise being modeled requires attention to a broad set of issues. These additional aspects of the expected use of the database influence a variety of design choices at the physical, logical, and view levels.

**7.1.1 Design Phases**

For small applications, it may be feasible for a database designer who understands the application requirements to decide directly on the relations to be created,

**259**  

**260 Chapter 7 Database Design and the E-R Model**

their attributes, and constraints on the relations. However, such a direct design process is difficult for real-world applications, since they are often highly complex. Often no one person understands the complete data needs of an application. The database designer must interact with users of the application to understand the needs of the application, represent them in a high-level fashion that can be understood by the users, and then translate the requirements into lower levels of the design. A high-level data model serves the database designer by providing a conceptual framework in which to specify, in a systematic fashion, the data requirements of the database users, and a database structure that fulfills these requirements.

• The initial phase of database design is to characterize fully the data needs of the prospective database users. The database designer needs to interact ex- tensively with domain experts and users to carry out this task. The outcome of this phase is a specification of user requirements. While there are tech- niques for diagrammatically representing user requirements, in this chapter we restrict ourselves to textual descriptions of user requirements.

• Next, the designer chooses a data model and, by applying the concepts of the chosen data model, translates these requirements into a conceptual schema of the database. The schema developed at this **conceptual-design** phase pro- vides a detailed overview of the enterprise. The entity-relationship model, which we study in the rest of this chapter, is typically used to represent the conceptual design. Stated in terms of the entity-relationship model, the con- ceptual schema specifies the entities that are represented in the database, the attributes of the entities, the relationships among the entities, and constraints on the entities and relationships. Typically, the conceptual-design phase re- sults in the creation of an entity-relationship diagram that provides a graphic representation of the schema.

The designer reviews the schema to confirm that all data requirements are indeed satisfied and are not in conflict with one another. She can also examine the design to remove any redundant features. Her focus at this point is on describing the data and their relationships, rather than on specifying physical storage details.

• A fully developed conceptual schema also indicates the functional require- ments of the enterprise. In a **specification of functional requirements**, users describe the kinds of operations (or transactions) that will be performed on the data. Example operations include modifying or updating data, searching for and retrieving specific data, and deleting data. At this stage of conceptual design, the designer can review the schema to ensure it meets functional requirements.

• The process of moving from an abstract data model to the implementation of the database proceeds in two final design phases.

◦ In the **logical-design phase**, the designer maps the high-level conceptual schema onto the implementation data model of the database system that  

**7.1 Overview of the Design Process 261**

will be used. The implementation data model is typically the relational data model, and this step typically consists of mapping the conceptual schema defined using the entity-relationship model into a relation schema.

◦ Finally, the designer uses the resulting system-specific database schema in the subsequent **physical-design phase**, in which the physical features of the database are specified. These features include the form of file or- ganization and choice of index structures, discussed in Chapters 10 and 11.

The physical schema of a database can be changed relatively easily after an application has been built. However, changes to the logical schema are usually harder to carry out, since they may affect a number of queries and updates scat- tered across application code. It is therefore important to carry out the database design phase with care, before building the rest of the database application.

**7.1.2 Design Alternatives**

A major part of the database design process is deciding how to represent in the design the various types of “things” such as people, places, products, and the like. We use the term _entity_ to refer to any such distinctly identifiable item. In a university database, examples of entities would include instructors, students, departments, courses, and course offerings.1 The various entities are related to each other in a variety of ways, all of which need to be captured in the database design. For example, a student takes a course offering, while an instructor teaches a course offering; teaches and takes are examples of relationships between entities.

In designing a database schema, we must ensure that we avoid two major pitfalls:

**1\. Redundancy:** A bad design may repeat information. For example, if we store the course identifier and title of a course with each course offering, the title would be stored redundantly (that is, multiple times, unnecessarily) with each course offering. It would suffice to store only the course identifier with each course offering, and to associate the title with the course identifier only once, in a course entity.

Redundancy can also occur in a relational schema. In the university example we have used so far, we have a relation with section information and a separate relation with course information. Suppose that instead we have a single relation where we repeat all of the course information (course id, title, dept name, credits) once for each section (offering) of the course.

Clearly, information about courses would then be stored redundantly. The biggest problem with such redundant representation of information

is that the copies of a piece of information can become inconsistent if the

1A course may have run in multiple semesters, as well as multiple times in a semester. We refer to each such offering of a course as a section.  

**262 Chapter 7 Database Design and the E-R Model**

information is updated without taking precautions to update all copies of the information. For example, different offerings of a course may have the same course identifier, but may have different titles. It would then become unclear what the correct title of the course is. Ideally, information should appear in exactly one place.

**2\. Incompleteness:** A bad design may make certain aspects of the enterprise difficult or impossible to model. For example, suppose that, as in case (1) above, we only had entities corresponding to course offering, without hav- ing an entity corresponding to courses. Equivalently, in terms of relations, suppose we have a single relation where we repeat all of the course infor- mation once for each section that the course is offered. It would then be impossible to represent information about a new course, unless that course is offered. We might try to make do with the problematic design by stor- ing null values for the section information. Such a work-around is not only unattractive, but may be prevented by primary-key constraints.

Avoiding bad designs is not enough. There may be a large number of good designs from which we must choose. As a simple example, consider a customer who buys a product. Is the sale of this product a relationship between the customer and the product? Alternatively, is the sale itself an entity that is related both to the customer and to the product? This choice, though simple, may make an important difference in what aspects of the enterprise can be modeled well. Considering the need to make choices such as this for the large number of entities and relationships in a real-world enterprise, it is not hard to see that database design can be a challenging problem. Indeed we shall see that it requires a combination of both science and “good taste.”

**7.2 The Entity-Relationship Model**

The **entity-relationship** (**E-R**) data model was developed to facilitate database design by allowing specification of an _enterprise schema_ that represents the overall logical structure of a database.

The E-R model is very useful in mapping the meanings and interactions of real-world enterprises onto a conceptual schema. Because of this usefulness, many database-design tools draw on concepts from the E-R model. The E-R data model employs three basic concepts: entity sets, relationship sets, and attributes, which we study first. The E-R model also has an associated diagrammatic representation, the E-R diagram, which we study later in this chapter.

**7.2.1 Entity Sets**

An **entity** is a “thing” or “object” in the real world that is distinguishable from all other objects. For example, each person in a university is an entity. An entity has a set of properties, and the values for some set of properties may uniquely identify an entity. For instance, a person may have a _person id_ property whose  

**7.2 The Entity-Relationship Model 263**

value uniquely identifies that person. Thus, the value 677-89-9011 for _person id_ would uniquely identify one particular person in the university. Similarly, courses can be thought of as entities, and _course id_ uniquely identifies a course entity in the university. An entity may be concrete, such as a person or a book, or it may be abstract, such as a course, a course offering, or a flight reservation.

An **entity set** is a set of entities of the same type that share the same properties, or attributes. The set of all people who are instructors at a given university, for example, can be defined as the entity set _instructor_. Similarly, the entity set _student_ might represent the set of all students in the university.

In the process of modeling, we often use the term _entity set_ in the abstract, without referring to a particular set of individual entities. We use the term **ex- tension** of the entity set to refer to the actual collection of entities belonging to the entity set. Thus, the set of actual instructors in the university forms the exten- sion of the entity set _instructor_. The above distinction is similar to the difference between a relation and a relation instance, which we saw in Chapter 2.

Entity sets do not need to be disjoint. For example, it is possible to define the entity set of all people in a university (_person_). A _person_ entity may be an _instructor_ entity, a _student_ entity, both, or neither.

An entity is represented by a set of **attributes**. Attributes are descriptive properties possessed by each member of an entity set. The designation of an attribute for an entity set expresses that the database stores similar information concerning each entity in the entity set; however, each entity may have its own value for each attribute. Possible attributes of the _instructor_ entity set are _ID_, _name_, _dept name_, and _salary_. In real life, there would be further attributes, such as street number, apartment number, state, postal code, and country, but we omit them to keep our examples simple. Possible attributes of the _course_ entity set are _course id_, _title_, _dept name_, and _credits_.

Each entity has a **value** for each of its attributes. For instance, a particular _instructor_ entity may have the value 12121 for _ID_, the value Wu for _name_, the value Finance for _dept name_, and the value 90000 for _salary_.

The _ID_ attribute is used to identify instructors uniquely, since there may be more than one instructor with the same name. In the United States, many enterprises find it convenient to use the _social-security_ number of a person2 as an attribute whose value uniquely identifies the person. In general the enterprise would have to create and assign a unique identifier for each instructor.

A database thus includes a collection of entity sets, each of which contains any number of entities of the same type. Figure 7.1 shows part of a university database that consists of two entity sets: _instructor_ and _student_. To keep the figure simple, only some of the attributes of the two entity sets are shown.

A database for a university may include a number of other entity sets. For example, in addition to keeping track of instructors and students, the university also has information about courses, which are represented by the entity set _course_

2In the United States, the government assigns to each person in the country a unique number, called a social-security number, to identify that person uniquely. Each person is supposed to have only one social-security number, and no two people are supposed to have the same social-security number.  

**264 Chapter 7 Database Design and the E-R Model**

_instructor_

_student_

22222 Einstein

Katz

Kim

Crick

Srinivasan

Singh

45565

98345

76766

10101

76543

12345 98988

76653

23121

00128 76543

Shankar

Tanaka

Aoi

Chavez

Peltier

Zhang

Brown

44553

**Figure 7.1** Entity sets _instructor_ and _student_.

with attributes _course id_, _title_, _dept name_ and _credits_. In a real setting, a university database may keep dozens of entity sets.

**7.2.2 Relationship Sets**

A **relationship** is an association among several entities. For example, we can define a relationship _advisor_ that associates instructor Katz with student Shankar. This relationship specifies that Katz is an advisor to student Shankar.

A **relationship set** is a set of relationships of the same type. Formally, it is a mathematical relation on _n_ ≥ 2 (possibly nondistinct) entity sets. If _E_1_, E_2_, . . . , En_ are entity sets, then a relationship set _R_ is a subset of

_{_(_e_1_, e_2_, . . . , en_) | _e_1 ∈ _E_1_, e_2 ∈ _E_2_, . . . , en_ ∈ _En}_

where (_e_1_, e_2_, . . . , en_) is a relationship. Consider the two entity sets _instructor_ and _student_ in Figure 7.1. We define

the relationship set _advisor_ to denote the association between instructors and students. Figure 7.2 depicts this association.

As another example, consider the two entity sets _student_ and _section_. We can define the relationship set _takes_ to denote the association between a student and the course sections in which that student is enrolled.

The association between entity sets is referred to as participation; that is, the entity sets _E_1_, E_2_, . . . , En_ **participate** in relationship set _R_. A **relationship in- stance** in an E-R schema represents an association between the named entities in the real-world enterprise that is being modeled. As an illustration, the individual _instructor_ entity Katz, who has instructor _ID_ 45565, and the _student_ entity Shankar, who has student _ID_ 12345, participate in a relationship instance of _advisor_. This re- lationship instance represents that in the university, the instructor Katz is advising student Shankar.

The function that an entity plays in a relationship is called that entity’s **role**. Since entity sets participating in a relationship set are generally distinct, roles  

**7.2 The Entity-Relationship Model 265**

_instructor_

_student_

76766 Crick

Katz Srinivasan

Kim Singh

Einstein

45565

10101

98345 76543

22222

98988

12345

00128

76543

76653

23121 44553

Tanaka

Shankar

Zhang

Brown

Aoi Chavez

Peltier

**Figure 7.2** Relationship set _advisor_.

are implicit and are not usually specified. However, they are useful when the meaning of a relationship needs clarification. Such is the case when the entity sets of a relationship set are not distinct; that is, the same entity set participates in a relationship set more than once, in different roles. In this type of relationship set, sometimes called a **recursive** relationship set, explicit role names are necessary to specify how an entity participates in a relationship instance. For example, consider the entity set _course_ that records information about all the courses offered in the university. To depict the situation where one course (C2) is a prerequisite for another course (C1) we have relationship set _prereq_ that is modeled by ordered pairs of _course_ entities. The first course of a pair takes the role of course C1, whereas the second takes the role of prerequisite course C2. In this way, all relationships of _prereq_ are characterized by (C1, C2) pairs; (C2, C1) pairs are excluded.

A relationship may also have attributes called **descriptive attributes**. Con- sider a relationship set _advisor_ with entity sets _instructor_ and _student_. We could associate the attribute _date_ with that relationship to specify the date when an instructor became the advisor of a student. The _advisor_ relationship among the entities corresponding to instructor Katz and student Shankar has the value “10 June 2007” for attribute _date_, which means that Katz became Shankar’s advisor on 10 June 2007.

Figure 7.3 shows the relationship set _advisor_ with a descriptive attribute _date_. Please note that Katz advises two students with two different advising dates.

As a more realistic example of descriptive attributes for relationships, consider the entity sets _student_ and _section_, which participate in a relationship set _takes_. We may wish to store a descriptive attribute _grade_ with the relationship to record the grade that a student got in the class. We may also store a descriptive attribute _for credit_ to record whether a student has taken the course for credit, or is auditing

(or sitting in on) the course. A relationship instance in a given relationship set must be uniquely identi-

fiable from its participating entities, without using the descriptive attributes. To understand this point, suppose we want to model all the dates when an instructor  

**266 Chapter 7 Database Design and the E-R Model**

_instructor_

_student_

76766 Crick

Katz

Srinivasan

Kim

Singh

Einstein

45565

10101

98345

76543

22222

98988

12345

00128

76543

44553

Tanaka

Shankar

Zhang

Brown

Aoi

Chavez

Peltier

3 May 2008

10 June 2007

12 June 2006

6 June 2009

30 June 2007

31 May 2007

4 May 2006

76653

23121

**Figure 7.3** _date_ as attribute of the _advisor_ relationship set.

became an advisor of a particular student. The single-valued attribute _date_ can store a single _date_ only. We cannot represent multiple dates by multiple relation- ship instances between the same instructor and a student, since the relationship instances would not be uniquely identifiable using only the participating entities. The right way to handle this case is to create a multivalued attribute _date_, which can store all the dates.

It is possible to have more than one relationship set involving the same entity sets. In our example, the _instructor_ and _student_ entity sets participate in the relationship set _advisor_. Additionally, suppose each student must have another instructor who serves as a department advisor (undergraduate or graduate). Then the _instructor_ and _student_ entity sets may participate in another relationship set, _dept advisor_.

The relationship sets _advisor_ and _dept advisor_ provide examples of a **binary** relationship set—that is, one that involves two entity sets. Most of the relationship sets in a database system are binary. Occasionally, however, relationship sets involve more than two entity sets.

As an example, suppose that we have an entity set _project_ that represents all the research projects carried out in the university. Consider the entity sets _instructor_, _student_, and _project_. Each project can have multiple associated students and multiple associated instructors. Furthermore, each student working on a project must have an associated instructor who guides the student on the project. For now, we ignore the first two relationships, between project and instructor, and between project and student. Instead, we focus on the information about which instructor is guiding which student on a particular project. To represent this information, we relate the three entity sets through the relationship set _proj guide_, which indicates that a particular student is guided by a particular instructor on a particular project.

Note that a student could have different instructors as guides for different projects, which cannot be captured by a binary relationship between students and instructors.  

**7.2 The Entity-Relationship Model 267**

The number of entity sets that participate in a relationship set is the **degree** of the relationship set. A binary relationship set is of degree 2; a ternary relationship set is of degree 3.

**7.2.3 Attributes**

For each attribute, there is a set of permitted values, called the **domain**, or **value set**, of that attribute. The domain of attribute _course id_ might be the set of all text strings of a certain length. Similarly, the domain of attribute _semester_ might be strings from the set _{_Fall, Winter, Spring, Summer_}_.

Formally, an attribute of an entity set is a function that maps from the entity set into a domain. Since an entity set may have several attributes, each entity can be described by a set of (attribute, data value) pairs, one pair for each attribute of the entity set. For example, a particular _instructor_ entity may be described by the set _{_(_ID_, 76766), (_name_, Crick), (_dept name_, Biology), (_salary_, 72000)_}_, meaning that the entity describes a person named Crick whose instructor _ID_ is 76766, who is a member of the Biology department with salary of $72,000. We can see, at this point, an integration of the abstract schema with the actual enterprise being modeled. The attribute values describing an entity constitute a significant portion of the data stored in the database.

An attribute, as used in the E-R model, can be characterized by the following attribute types.

• **Simple** and **composite** attributes. In our examples thus far, the attributes have been simple; that is, they have not been divided into subparts. **Composite** attributes, on the other hand, can be divided into subparts (that is, other attributes). For example, an attribute _name_ could be structured as a composite attribute consisting of _first name_, _middle initial_, and _last name_. Using composite attributes in a design schema is a good choice if a user will wish to refer to an entire attribute on some occasions, and to only a component of the attribute on other occasions. Suppose we were to to add an address to the _student_ entity-set. The address can be defined as the composite attribute _address_ with the attributes _street_, _city_, _state_, and _zip code_.3 Composite attributes help us to group together related attributes, making the modeling cleaner.

Note also that a composite attribute may appear as a hierarchy. In the com- posite attribute _address_, its component attribute _street_ can be further divided into _street number_, _street name_, and _apartment number_. Figure 7.4 depicts these examples of composite attributes for the _instructor_ entity set.

• **Single-valued** and **multivalued** attributes. The attributes in our examples all have a single value for a particular entity. For instance, the _student ID_ attribute for a specific student entity refers to only one student _ID_. Such attributes are said to be **single valued**. There may be instances where an attribute has a set of values for a specific entity. Suppose we add to the _instructor_ entity set

3We assume the address format used in the United States, which includes a numeric postal code called a zip code.  

**268 Chapter 7 Database Design and the E-R Model**

_name address_

_first\_name middle\_initial last\_name street city state postal\_code_

_street\_number street\_name apartment\_number_

composite attributes

component attributes

**Figure 7.4** Composite attributes instructor _name_ and _address_.

a _phone number_ attribute. An _instructor_ may have zero, one, or several phone numbers, and different instructors may have different numbers of phones. This type of attribute is said to be **multivalued**. As another example, we could add to the _instructor_ entity set an attribute _dependent name_ listing all the dependents. This attribute would be multivalued, since any particular instructor may have zero, one, or more dependents. To denote that an attribute is multivalued, we enclose it in braces, for example {_phone number_} or {_dependent name_}.

Where appropriate, upper and lower bounds may be placed on the number of values in a multivalued attribute. For example, a university may limit the number of phone numbers recorded for a single instructor to two. Placing bounds in this case expresses that the _phone number_ attribute of the _instructor_ entity set may have between zero and two values.

• **Derived** attribute. The value for this type of attribute can be derived from the values of other related attributes or entities. For instance, let us say that the _instructor_ entity set has an attribute _students advised_, which represents how many students an instructor advises. We can derive the value for this attribute by counting the number of _student_ entities associated with that instructor.

As another example, suppose that the _instructor_ entity set has an attribute _age_ that indicates the instructor’s age. If the _instructor_ entity set also has an attribute _date of birth_, we can calculate _age_ from _date of birth_ and the current date. Thus, _age_ is a derived attribute. In this case, _date of birth_ may be referred to as a _base_ attribute, or a _stored_ attribute. The value of a derived attribute is not stored but is computed when required.

An attribute takes a **null** value when an entity does not have a value for it. The _null_ value may indicate “not applicable”—that is, that the value does not exist for the entity. For example, one may have no middle name. _Null_ can also designate that an attribute value is unknown. An unknown value may be either _missing_ (the value does exist, but we do not have that information) or _not known_ (we do not know whether or not the value actually exists).

For instance, if the _name_ value for a particular instructor is _null_, we assume that the value is missing, since every instructor must have a name. A null value for the _apartment number_ attribute could mean that the address does not include  

**7.3 Constraints 269**

an apartment number (not applicable), that an apartment number exists but we do not know what it is (missing), or that we do not know whether or not an apartment number is part of the instructor’s address (unknown).

**7.3 Constraints**

An E-R enterprise schema may define certain constraints to which the contents of a database must conform. In this section, we examine mapping cardinalities and participation constraints.

**7.3.1 Mapping Cardinalities**

**Mapping cardinalities**, or cardinality ratios, express the number of entities to which another entity can be associated via a relationship set.

Mapping cardinalities are most useful in describing binary relationship sets, although they can contribute to the description of relationship sets that involve more than two entity sets. In this section, we shall concentrate on only binary relationship sets.

For a binary relationship set _R_ between entity sets _A_ and _B_, the mapping cardinality must be one of the following:

• **One-to-one**. An entity in _A_ is associated with _at most_ one entity in _B_, and an entity in _B_ is associated with _at most_ one entity in _A_. (See Figure 7.5a.)

• **One-to-many**. An entity in _A_ is associated with any number (zero or more) of entities in _B_. An entity in _B_, however, can be associated with _at most_ one entity in _A_. (See Figure 7.5b.)

(b)(a)

_a1_

_a2_

_a3_

_a4_

_b1_

_b2_

_b3_

_a2_

_a1_

_a3_

_b1_

_b2_

_b3_

_b4_

_b5_

_A B A B_

**Figure 7.5** Mapping cardinalities. (a) One-to-one. (b) One-to-many.  

**270 Chapter 7 Database Design and the E-R Model**

_a a2_

_a3_

_a5_

_a1_

_a2_

_a4_

_a2_

_a1_

_a3_

_a4_

_b1_

_b2_

_b3_

_A B BA_

_b1_

_b2_

_b3_

_b4_

(a) (b)

**Figure 7.6** Mapping cardinalities. (a) Many-to-one. (b) Many-to-many.

• **Many-to-one**. An entity in _A_ is associated with _at most_ one entity in _B_. An entity in _B_, however, can be associated with any number (zero or more) of entities in _A_. (See Figure 7.6a.)

• **Many-to-many**. An entity in _A_ is associated with any number (zero or more) of entities in _B_, and an entity in _B_ is associated with any number (zero or more) of entities in _A_. (See Figure 7.6b.)

The appropriate mapping cardinality for a particular relationship set obviously depends on the real-world situation that the relationship set is modeling.

As an illustration, consider the _advisor_ relationship set. If, in a particular university, a student can be advised by only one instructor, and an instructor can advise several students, then the relationship set from _instructor_ to _student_ is one-to-many. If a student can be advised by several instructors (as in the case of students advised jointly), the relationship set is many-to-many.

**7.3.2 Participation Constraints**

The participation of an entity set _E_ in a relationship set _R_ is said to be **total** if every entity in _E_ participates in at least one relationship in _R_. If only some entities in _E_ participate in relationships in _R_, the participation of entity set _E_ in relationship _R_ is said to be **partial**. In Figure 7.5a, the participation of _B_ in the relationship set is total while the participation of _A_ in the relationship set is partial. In Figure 7.5b, the participation of both _A_ and _B_ in the relationship set are total.

For example, we expect every _student_ entity to be related to at least one instructor through the _advisor_ relationship. Therefore the participation of _student_ in the relationship set _advisor_ is total. In contrast, an _instructor_ need not advise any students. Hence, it is possible that only some of the _instructor_ entities are related to the _student_ entity set through the _advisor_ relationship, and the participation of _instructor_ in the _advisor_ relationship set is therefore partial.  

**7.3 Constraints 271**

**7.3.3 Keys**

We must have a way to specify how entities within a given entity set are distin- guished. Conceptually, individual entities are distinct; from a database perspec- tive, however, the differences among them must be expressed in terms of their attributes.

Therefore, the values of the attribute values of an entity must be such that they can _uniquely identify_ the entity. In other words, no two entities in an entity set are allowed to have exactly the same value for all attributes.

The notion of a _key_ for a relation schema, as defined in Section 2.3, applies directly to entity sets. That is, a key for an entity is a set of attributes that suffice to distinguish entities from each other. The concepts of superkey, candidate key, and primary key are applicable to entity sets just as they are applicable to relation schemas.

Keys also help to identify relationships uniquely, and thus distinguish rela- tionships from each other. Below, we define the corresponding notions of keys for relationships.

The primary key of an entity set allows us to distinguish among the various entities of the set. We need a similar mechanism to distinguish among the various relationships of a relationship set.

Let _R_ be a relationship set involving entity sets _E_1_, E_2_, . . . , En_. Let _primary- key_(_Ei_ ) denote the set of attributes that forms the primary key for entity set _Ei_ . Assume for now that the attribute names of all primary keys are unique. The composition of the primary key for a relationship set depends on the set of attributes associated with the relationship set _R_.

If the relationship set _R_ has no attributes associated with it, then the set of attributes

_primary-key_(_E_1) ∪ _primary-key_(_E_2) ∪ · · · ∪ _primary-key_(_En_)

describes an individual relationship in set _R_. If the relationship set _R_ has attributes _a_1_, a_2_, . . . , am_ associated with it, then

the set of attributes

_primary-key_(_E_1) ∪ _primary-key_(_E_2) ∪ · · · ∪ _primary-key_(_En_) ∪ {_a_1_, a_2_, . . . , am_}

describes an individual relationship in set _R_. In both of the above cases, the set of attributes

_primary-key_(_E_1) ∪ _primary-key_(_E_2) ∪ · · · ∪ _primary-key_(_En_)

forms a superkey for the relationship set. If the attribute names of primary keys are not unique across entity sets, the

attributes are renamed to distinguish them; the name of the entity set combined with the name of the attribute would form a unique name. If an entity set par- ticipates more than once in a relationship set (as in the _prereq_ relationship in  

**272 Chapter 7 Database Design and the E-R Model**

Section 7.2.2), the role name is used instead of the name of the entity set, to form a unique attribute name.

The structure of the primary key for the relationship set depends on the mapping cardinality of the relationship set. As an illustration, consider the entity sets _instructor_ and _student_, and the relationship set _advisor_, with attribute _date_, in Section 7.2.2. Suppose that the relationship set is many-to-many. Then the primary key of _advisor_ consists of the union of the primary keys of _instructor_ and _student_. If the relationship is many-to-one from _student_ to _instructor_—that is, each student can have have at most one advisor—then the primary key of _advisor_ is simply the primary key of _student_. However, if an instructor can advise only one student— that is, if the _advisor_ relationship is many-to-one from _instructor_ to _student_—then the primary key of _advisor_ is simply the primary key of _instructor_. For one-to-one relationships either candidate key can be used as the primary key.

For nonbinary relationships, if no cardinality constraints are present then the superkey formed as described earlier in this section is the only candidate key, and it is chosen as the primary key. The choice of the primary key is more complicated if cardinality constraints are present. Since we have not discussed how to specify cardinality constraints on nonbinary relations, we do not discuss this issue further in this chapter. We consider the issue in more detail later, in Sections 7.5.5 and 8.4.

**7.4 Removing Redundant Attributes in Entity Sets**

When we design a database using the E-R model, we usually start by identifying those entity sets that should be included. For example, in the university organiza- tion we have discussed thus far, we decided to include such entity sets as _student_, _instructor_, etc. Once the entity sets are decided upon, we must choose the appro- priate attributes. These attributes are supposed to represent the various values we want to capture in the database. In the university organization, we decided that for the _instructor_ entity set, we will include the attributes _ID_, _name_, _dept name_, and _salary_. We could have added the attributes: _phone number_, _office number_, _home page_, etc. The choice of what attributes to include is up to the designer, who has

a good understanding of the structure of the enterprise. Once the entities and their corresponding attributes are chosen, the relation-

ship sets among the various entities are formed. These relationship sets may result in a situation where attributes in the various entity sets are redundant and need to be removed from the original entity sets. To illustrate, consider the entity sets _instructor_ and _department_:

• The entity set _instructor_ includes the attributes _ID_, _name_, _dept name_, and _salary_, with _ID_ forming the primary key.

• The entity set _department_ includes the attributes _dept name_, _building_, and _bud- get_, with _dept name_ forming the primary key.

We model the fact that each instructor has an associated department using a relationship set _inst dept_ relating _instructor_ and _department_.  

**7.4 Removing Redundant Attributes in Entity Sets 273**

The attribute _dept name_ appears in both entity sets. Since it is the primary key for the entity set _department_, it is redundant in the entity set _instructor_ and needs to be removed.

Removing the attribute _dept name_ from the _instructor_ entity set may appear rather unintuitive, since the relation _instructor_ that we used in the earlier chap- ters had an attribute _dept name_. As we shall see later, when we create a relational schema from the E-R diagram, the attribute _dept name_ in fact gets added to the relation _instructor_, but only if each instructor has at most one associated depart- ment. If an instructor has more than one associated department, the relationship between instructors and departments is recorded in a separate relation _inst dept_.

Treating the connection between instructors and departments uniformly as a relationship, rather than as an attribute of _instructor_, makes the logical relationship explicit, and helps avoid a premature assumption that each instructor is associated with only one department.

Similarly, the _student_ entity set is related to the _department_ entity set through the relationship set _student dept_ and thus there is no need for a _dept name_ attribute in _student_.

As another example, consider course offerings (sections) along with the time slots of the offerings. Each time slot is identified by a _time slot id_, and has associated with it a set of weekly meetings, each identified by a day of the week, start time, and end time. We decide to model the set of weekly meeting times as a multivalued composite attribute. Suppose we model entity sets _section_ and _time slot_ as follows:

• The entity set _section_ includes the attributes _course id_, _sec id_, _semester_, _year_, _building_, _room number_, and _time slot id_, with (_course id_, _sec id_, _year_, _semester_) forming the primary key.

• The entity set _time slot_ includes the attributes _time slot id_, which is the primary key,4 and a multivalued composite attribute {(_day, start time, end time_)}.5

These entities are related through the relationship set _sec time slot_. The attribute _time slot id_ appears in both entity sets. Since it is the primary

key for the entity set _time slot_, it is redundant in the entity set _section_ and needs to be removed.

As a final example, suppose we have an entity set _classroom_, with attributes _building_, _room number_, and _capacity_, with _building_ and _room number_ forming the primary key. Suppose also that we have a relationship set _sec class_ that relates _section_ to _classroom_. Then the attributes _{building_, _room number}_ are redundant in the entity set _section_.

A good entity-relationship design does not contain redundant attributes. For our university example, we list the entity sets and their attributes below, with primary keys underlined:

4We shall see later on that the primary key for the relation created from the entity set _time slot_ includes _day_ and _start time_; however, _day_ and _start time_ do not form part of the primary key of the entity set _time slot_. 5We could optionally give a name, such as _meeting_, for the composite attribute containing _day_, _start time_, and _end time_.  

**274 Chapter 7 Database Design and the E-R Model**

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

**7.5 Entity-Relationship Diagrams**

As we saw briefly in Section 1.3.3, an **E-R diagram** can express the overall logical structure of a database graphically. E-R diagrams are simple and clear—qualities that may well account in large part for the widespread use of the E-R model.

**7.5.1 Basic Structure**

An E-R diagram consists of the following major components:  

**7.5 Entity-Relationship Diagrams 275**

_instructor_

_ID name salary_

_student_

_ID name tot\_cred_

_advisor_

**Figure 7.7** E-R diagram corresponding to instructors and students.

• **Rectangles divided into two parts** represent entity sets. The first part, which in this textbook is shaded blue, contains the name of the entity set. The second part contains the names of all the attributes of the entity set.

• **Diamonds** represent relationship sets.

• **Undivided rectangles** represent the attributes of a relationship set. Attributes that are part of the primary key are underlined.

• **Lines** link entity sets to relationship sets.

• **Dashed lines** link attributes of a relationship set to the relationship set.

• **Double lines** indicate total participation of an entity in a relationship set.

• **Double diamonds** represent identifying relationship sets linked to weak entity sets (we discuss identifying relationship sets and weak entity sets later, in Section 7.5.6).

Consider the E-R diagram in Figure 7.7, which consists of two entity sets, _in- structor_ and _student_ related through a binary relationship set _advisor_. The attributes associated with _instructor_ are _ID_, _name_, and _salary_. The attributes associated with _student_ are _ID_, _name_, and _tot cred_. In Figure 7.7, attributes of an entity set that are members of the primary key are underlined.

If a relationship set has some attributes associated with it, then we enclose the attributes in a rectangle and link the rectangle with a dashed line to the diamond representing that relationship set. For example, in Figure 7.8, we have the _date_ descriptive attribute attached to the relationship set _advisor_ to specify the date on which an instructor became the advisor.

_ID name salary_

_ID name tot\_cred_

_date_

_instructor student_

_advisor_

**Figure 7.8** E-R diagram with an attribute attached to a relationship set.  

**276 Chapter 7 Database Design and the E-R Model**

_instructor student ID name salary_

_instructor ID name salary_

_instructor ID name salary_

_ID name tot\_cred_

_student ID name tot\_cred_

_student ID name tot\_cred_

(a)

(b)

(c)

_advisor_

_advisor_

_advisor_

**Figure 7.9** Relationships. (a) One-to-one. (b) One-to-many. (c) Many-to-many.

**7.5.2 Mapping Cardinality**

The relationship set _advisor_, between the _instructor_ and _student_ entity sets may be one-to-one, one-to-many, many-to-one, or many-to-many. To distinguish among these types, we draw either a directed line (→) or an undirected line (—) between the relationship set and the entity set in question, as follows:

• **One-to-one:** We draw a directed line from the relationship set _advisor_ to both entity sets _instructor_ and _student_ (see Figure 7.9a). This indicates that an instructor may advise at most one student, and a student may have at most one advisor.

• **One-to-many:** We draw a directed line from the relationship set _advisor_ to the entity set _instructor_ and an undirected line to the entity set _student_ (see Figure 7.9b). This indicates that an instructor may advise many students, but a student may have at most one advisor.

• **Many-to-one:** We draw an undirected line from the relationship set _advisor_ to the entity set _instructor_ and a directed line to the entity set _student_. This indicates that an instructor may advise at most one student, but a student may have many advisors.

• **Many-to-many:** We draw an undirected line from the relationship set _advisor_ to both entity sets _instructor_ and _student_ (see Figure 7.9c). This indicates that  

**7.5 Entity-Relationship Diagrams 277**

an instructor may advise many students, and a student may have many advisors.

E-R diagrams also provide a way to indicate more complex constraints on the number of times each entity participates in relationships in a relationship set. A line may have an associated minimum and maximum cardinality, shown in the form _l..h_, where _l_ is the minimum and _h_ the maximum cardinality. A minimum value of 1 indicates total participation of the entity set in the relationship set; that is, each entity in the entity set occurs in at least one relationship in that relationship set. A maximum value of 1 indicates that the entity participates in at most one relationship, while a maximum value ∗ indicates no limit.

For example, consider Figure 7.10. The line between _advisor_ and _student_ has a cardinality constraint of 1_.._1, meaning the minimum and the maximum cardinal- ity are both 1. That is, each student must have exactly one advisor. The limit 0_.._∗ on the line between _advisor_ and _instructor_ indicates that an instructor can have zero or more students. Thus, the relationship _advisor_ is one-to-many from _instruc- tor_ to _student_, and further the participation of _student_ in _advisor_ is total, implying that a student must have an advisor.

It is easy to misinterpret the 0_.._∗ on the left edge and think that the relationship _advisor_ is many-to-one from _instructor_ to _student_—this is exactly the reverse of the correct interpretation.

If both edges have a maximum value of 1, the relationship is one-to-one. If we had specified a cardinality limit of 1_.._∗ on the left edge, we would be saying that each instructor must advise at least one student.

The E-R diagram in Figure 7.10 could alternatively have been drawn with a double line from _student_ to _advisor_, and an arrow on the line from _advisor_ to _instructor_, in place of the cardinality constraints shown. This alternative diagram would enforce exactly the same constraints as the constraints shown in the figure.

**7.5.3 Complex Attributes**

Figure 7.11 shows how composite attributes can be represented in the E-R notation. Here, a composite attribute _name_, with component attributes _first name_, _middle initial_, and _last name_ replaces the simple attribute _name_ of _instructor_. As another

example, suppose we were to add an address to the _instructor_ entity-set. The address can be defined as the composite attribute _address_ with the attributes

_instructor_

_ID name salary_

_student_

_ID name tot\_cred_

_advisor 1..10..\*_

**Figure 7.10** Cardinality limits on relationship sets.  

**278 Chapter 7 Database Design and the E-R Model**

_instructor ID name_

_first\_name middle\_initial last\_name_

_address street_

_street\_number street\_name apt\_number_

_city state zip_

_{ phone\_number } date\_of\_birth age ( )_

**Figure 7.11** E-R diagram with composite, multivalued, and derived attributes.

_street_, _city_, _state_, and _zip code_. The attribute _street_ is itself a composite attribute whose component attributes are _street number_, _street name_, and _apartment number_.

Figure 7.11 also illustrates a multivalued attribute _phone number_, denoted by “_{phone number}_”, and a derived attribute _age_, depicted by a “_age_ ( )”.

**7.5.4 Roles**

We indicate roles in E-R diagrams by labeling the lines that connect diamonds to rectangles. Figure 7.12 shows the role indicators _course id_ and _prereq id_ between the _course_ entity set and the _prereq_ relationship set.

**7.5.5 Nonbinary Relationship Sets**

Nonbinary relationship sets can be specified easily in an E-R diagram. Figure 7.13 consists of the three entity sets _instructor_, _student_, and _project_, related through the relationship set _proj guide_.

_course course\_id title credits_

_course\_id_

_prereq\_id prereq_

**Figure 7.12** E-R diagram with role indicators.  

**7.5 Entity-Relationship Diagrams 279**

_instructor_

_ID name salary_

_student_

_ID name tot\_cred_

_. . . project_

_proj\_guide_

**Figure 7.13** E-R diagram with a ternary relationship.

We can specify some types of many-to-one relationships in the case of non- binary relationship sets. Suppose a _student_ can have at most one instructor as a guide on a project. This constraint can be specified by an arrow pointing to _instructor_ on the edge from _proj guide_.

We permit at most one arrow out of a relationship set, since an E-R diagram with two or more arrows out of a nonbinary relationship set can be interpreted in two ways. Suppose there is a relationship set _R_ between entity sets _A_1_, A_2_, . . . , An_, and the only arrows are on the edges to entity sets _Ai_+1_, Ai_+2_, . . . , An_. Then, the two possible interpretations are:

**1\.** A particular combination of entities from _A_1_, A_2_, . . . , Ai_ can be associated with at most one combination of entities from _Ai_+1_, Ai_+2_, . . . , An_. Thus, the primary key for the relationship _R_ can be constructed by the union of the primary keys of _A_1_, A_2_, . . . , Ai_ .

**2\.** For each entity set _Ak_ , _i < k_ ≤ _n_, each combination of the entities from the other entity sets can be associated with at most one entity from _Ak_ . Each set {_A_1_, A_2_, . . . , Ak_−1_, Ak_+1_, . . . , An_}, for _i < k_ ≤ _n_, then forms a candidate key.

Each of these interpretations has been used in different books and systems. To avoid confusion, we permit only one arrow out of a relationship set, in which case the two interpretations are equivalent. In Chapter 8 (Section 8.4), we study _functional dependencies_, which allow either of these interpretations to be specified in an unambiguous manner.

**7.5.6 Weak Entity Sets**

Consider a _section_ entity, which is uniquely identified by a course identifier, semester, year, and section identifier. Clearly, section entities are related to course entities. Suppose we create a relationship set _sec course_ between entity sets _section_ and _course_.

Now, observe that the information in _sec course_ is redundant, since _section_ already has an attribute _course id_, which identifies the course with which the section is related. One option to deal with this redundancy is to get rid of the  

**280 Chapter 7 Database Design and the E-R Model**

relationship _sec course_; however, by doing so the relationship between _section_ and _course_ becomes implicit in an attribute, which is not desirable.

An alternative way to deal with this redundancy is to not store the attribute _course id_ in the _section_ entity and to only store the remaining attributes _sec id_, _year_, and _semester_.6 However, the entity set _section_ then does not have enough attributes to identify a particular _section_ entity uniquely; although each _section_ entity is distinct, sections for different courses may share the same _sec id_, _year_, and _semester_. To deal with this problem, we treat the relationship _sec course_ as a special relationship that provides extra information, in this case the _course id_, required to identify _section_ entities uniquely.

The notion of _weak entity set_ formalizes the above intuition. An entity set that does not have sufficient attributes to form a primary key is termed a **weak entity set**. An entity set that has a primary key is termed a **strong entity set**.

For a weak entity set to be meaningful, it must be associated with another entity set, called the **identifying** or **owner entity set**. Every weak entity must be associated with an identifying entity; that is, the weak entity set is said to be **existence dependent** on the identifying entity set. The identifying entity set is said to **own** the weak entity set that it identifies. The relationship associating the weak entity set with the identifying entity set is called the **identifying relationship**.

The identifying relationship is many-to-one from the weak entity set to the identifying entity set, and the participation of the weak entity set in the rela- tionship is total. The identifying relationship set should not have any descriptive attributes, since any such attributes can instead be associated with the weak entity set.

In our example, the identifying entity set for _section_ is _course_, and the relation- ship _sec course_, which associates _section_ entities with their corresponding _course_ entities, is the identifying relationship.

Although a weak entity set does not have a primary key, we nevertheless need a means of distinguishing among all those entities in the weak entity set that depend on one particular strong entity. The **discriminator** of a weak entity set is a set of attributes that allows this distinction to be made. For example, the discriminator of the weak entity set _section_ consists of the attributes _sec id_, _year_, and _semester_, since, for each course, this set of attributes uniquely identifies one single section for that course. The discriminator of a weak entity set is also called the _partial key_ of the entity set.

The primary key of a weak entity set is formed by the primary key of the identifying entity set, plus the weak entity set’s discriminator. In the case of the entity set _section_, its primary key is _{course id_, _sec id_, _year_, _semester}_, where _course id_ is the primary key of the identifying entity set, namely _course_, and _{sec id_, _year_,

_semester}_ distinguishes _section_ entities for the same course. Note that we could have chosen to make _sec id_ globally unique across all

courses offered in the university, in which case the _section_ entity set would have

6Note that the relational schema we eventually create from the entity set _section_ does have the attribute _course id_, for reasons that will become clear later, even though we have dropped the attribute _course id_ from the entity set _section_.  

**7.5 Entity-Relationship Diagrams 281**

_course course\_id title credits_

_section sec\_id semester year_

_sec\_course_

**Figure 7.14** E-R diagram with a weak entity set.

had a primary key. However, conceptually, a _section_ is still dependent on a _course_ for its existence, which is made explicit by making it a weak entity set.

In E-R diagrams, a weak entity set is depicted via a rectangle, like a strong entity set, but there are two main differences:

• The discriminator of a weak entity is underlined with a dashed, rather than a solid, line.

• The relationship set connecting the weak entity set to the identifying strong entity set is depicted by a double diamond.

In Figure 7.14, the weak entity set _section_ depends on the strong entity set _course_ via the relationship set _sec course_.

The figure also illustrates the use of double lines to indicate _total participation_; the participation of the (weak) entity set _section_ in the relationship _sec course_ is total, meaning that every section must be related via _sec course_ to some course. Finally, the arrow from _sec course_ to _course_ indicates that each section is related to a single course.

A weak entity set can participate in relationships other than the identifying relationship. For instance, the _section_ entity could participate in a relationship with the _time slot_ entity set, identifying the time when a particular class section meets. A weak entity set may participate as owner in an identifying relationship with another weak entity set. It is also possible to have a weak entity set with more than one identifying entity set. A particular weak entity would then be identified by a combination of entities, one from each identifying entity set. The primary key of the weak entity set would consist of the union of the primary keys of the identifying entity sets, plus the discriminator of the weak entity set.

In some cases, the database designer may choose to express a weak entity set as a multivalued composite attribute of the owner entity set. In our example, this alternative would require that the entity set _course_ have a multivalued, composite attribute _section_. A weak entity set may be more appropriately modeled as an attribute if it participates in only the identifying relationship, and if it has few attributes. Conversely, a weak entity set representation more aptly models a situation where the set participates in relationships other than the identifying relationship, and where the weak entity set has several attributes. It is clear that _section_ violates the requirements for being modeled as a multivalued composite attribute, and is modeled more aptly as a weak entity set.  

**282 Chapter 7 Database Design and the E-R Model**

**7.5.7 E-R diagram for the University Enterprise**

In Figure 7.15, we show an E-R diagram that corresponds to the university enter- prise that we have been using thus far in the text. This E-R diagram is equivalent to the textual description of the university E-R model that we saw in Section 7.4, but with several additional constraints, and _section_ now being a weak entity.

In our university database, we have a constraint that each instructor must have exactly one associated department. As a result, there is a double line in Figure 7.15 between _instructor_ and _inst dept_, indicating total participation of _instructor_ in _inst dept_; that is, each instructor must be associated with a department. Further, there

is an arrow from _inst dept_ to _department_, indicating that each instructor can have at most one associated department.

_time\_slotcourse_

_student ID name salary_

_ID name tot\_cred_

_course\_id title credits_

_time\_slot\_id_ { _day_

_start\_time end\_time_

} _course\_id prereq\_id_

_advisor_

_teaches takes_

_sec\_course sec\_time\_slot_

_grade_

_prereq_

_inst\_dept stud\_dept_

_instructor_

_department dept\_name building budget_

_section sec\_id semester year_

_course\_dept_

_sec\_class_

_classroom building room\_number capacity_

**Figure 7.15** E-R diagram for a university enterprise.  

**7.6 Reduction to Relational Schemas 283**

Similarly, entity sets _course_ and _student_ have double lines to relationship sets _course dept_ and _stud dept_ respectively, as also entity set _section_ to relationship set _sec time slot_. The first two relationships, in turn, have an arrow pointing to the other relationship, _department_, while the third relationship has an arrow pointing to _time slot_.

Further, Figure 7.15 shows that the relationship set _takes_ has a descriptive attribute _grade_, and that each student has at most one advisor. The figure also shows that _section_ is now a weak entity set, with attributes _sec id_, _semester_, and _year_ forming the discriminator; _sec course_ is the identifying relationship set relating weak entity set _section_ to the strong entity set _course_.

In Section 7.6, we shall show how this E-R diagram can be used to derive the various relation schemas we use.

**7.6 Reduction to Relational Schemas**

We can represent a database that conforms to an E-R database schema by a col- lection of relation schemas. For each entity set and for each relationship set in the database design, there is a unique relation schema to which we assign the name of the corresponding entity set or relationship set.

Both the E-R model and the relational database model are abstract, logical representations of real-world enterprises. Because the two models employ similar design principles, we can convert an E-R design into a relational design.

In this section, we describe how an E-R schema can be represented by relation schemas and how constraints arising from the E-R design can be mapped to constraints on relation schemas.

**7.6.1 Representation of Strong Entity Sets with Simple Attributes**

Let _E_ be a strong entity set with only simple descriptive attributes _a_1_, a_2_, . . . , an_. We represent this entity by a schema called _E_ with _n_ distinct attributes. Each tuple in a relation on this schema corresponds to one entity of the entity set _E_.

For schemas derived from strong entity sets, the primary key of the entity set serves as the primary key of the resulting schema. This follows directly from the fact that each tuple corresponds to a specific entity in the entity set.

As an illustration, consider the entity set _student_ of the E-R diagram in Fig- ure 7.15. This entity set has three attributes: _ID_, _name_, _tot cred_. We represent this entity set by a schema called _student_ with three attributes:

_student_ (_ID_, _name_, _tot cred_)

Note that since student _ID_ is the primary key of the entity set, it is also the primary key of the relation schema.

Continuing with our example, for the E-R diagram in Figure 7.15, all the strong entity sets, except _time slot_, have only simple attributes. The schemas derived from these strong entity sets are:  

**284 Chapter 7 Database Design and the E-R Model**

_classroom_ (_building_, _room number_, _capacity_) _department_ (_dept name_, _building_, _budget_) _course_ (_course id_, _title_, _credits_) _instructor_ (_ID_, _name_, _salary_) _student_ (_ID_, _name_, _tot cred_)

As you can see, both the _instructor_ and _student_ schemas are different from the schemas we have used in the previous chapters (they do not contain the attribute _dept name_). We shall revisit this issue shortly.

**7.6.2 Representation of Strong Entity Sets with Complex Attributes**

When a strong entity set has nonsimple attributes, things are a bit more complex. We handle composite attributes by creating a separate attribute for each of the component attributes; we do not create a separate attribute for the composite attribute itself. To illustrate, consider the version of the _instructor_ entity set de- picted in Figure 7.11. For the composite attribute _name_, the schema generated for _instructor_ contains the attributes _first name_, _middle name_, and _last name_; there is no separate attribute or schema for _name_. Similarly, for the composite attribute _address_, the schema generated contains the attributes _street_, _city_, _state_, and _zip code_. Since _street_ is a composite attribute it is replaced by _street number_, _street name_, and _apt number_. We revisit this matter in Section 8.2.

Multivalued attributes are treated differently from other attributes. We have seen that attributes in an E-R diagram generally map directly into attributes for the appropriate relation schemas. Multivalued attributes, however, are an exception; new relation schemas are created for these attributes, as we shall see shortly.

Derived attributes are not explicitly represented in the relational data model. However, they can be represented as “methods” in other data models such as the object-relational data model, which is described later in Chapter 22.

The relational schema derived from the version of entity set _instructor_ with complex attributes, without including the multivalued attribute, is thus:

_instructor_ (_ID_, _first name_, _middle name_, _last name_, _street number_, _street name_, _apt number_, _city_, _state_, _zip code_, _date of birth_)

For a multivalued attribute _M_, we create a relation schema _R_ with an attribute _A_ that corresponds to _M_ and attributes corresponding to the primary key of the entity set or relationship set of which _M_ is an attribute.

As an illustration, consider the E-R diagram in Figure 7.11 that depicts the entity set _instructor_, which includes the multivalued attribute _phone number_. The primary key of _instructor_ is _ID_. For this multivalued attribute, we create a relation schema

_instructor phone_ (_ID_, _phone number_)  

**7.6 Reduction to Relational Schemas 285**

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

**7.6.3 Representation of Weak Entity Sets**

Let _A_ be a weak entity set with attributes _a_1_, a_2_, . . . , am_. Let _B_ be the strong entity set on which _A_ depends. Let the primary key of _B_ consist of attributes _b_1_, b_2_, . . . , bn_. We represent the entity set _A_ by a relation schema called _A_ with one attribute for each member of the set:

_{a_1_, a_2_, . . . , am}_ ∪ _{b_1_, b_2_, . . . , bn}_

For schemas derived from a weak entity set, the combination of the pri- mary key of the strong entity set and the discriminator of the weak entity set serves as the primary key of the schema. In addition to creating a primary key, we also create a foreign-key constraint on the relation _A_, specifying that the  

**286 Chapter 7 Database Design and the E-R Model**

attributes _b_1_, b_2_, . . . , bn_ reference the primary key of the relation _B_. The foreign- key constraint ensures that for each tuple representing a weak entity, there is a corresponding tuple representing the corresponding strong entity.

As an illustration, consider the weak entity set _section_ in the E-R diagram of Figure 7.15. This entity set has the attributes: _sec id_, _semester_, and _year_. The primary key of the _course_ entity set, on which _section_ depends, is _course id_. Thus, we represent _section_ by a schema with the following attributes:

_section_ (_course id_, _sec id_, _semester_, _year_)

The primary key consists of the primary key of the entity set _course_, along with the discriminator of _section_, which is _sec id_, _semester_, and _year_. We also create a foreign-key constraint on the _section_ schema, with the attribute _course id_ refer- encing the primary key of the _course_ schema, and the integrity constraint “on delete cascade”.7 Because of the “on delete cascade” specification on the foreign key constraint, if a _course_ entity is deleted, then so are all the associated _section_ entities.

**7.6.4 Representation of Relationship Sets**

Let _R_ be a relationship set, let _a_1_, a_2_, . . . , am_ be the set of attributes formed by the union of the primary keys of each of the entity sets participating in _R_, and let the descriptive attributes (if any) of _R_ be _b_1_, b_2_, . . . , bn_. We represent this relationship set by a relation schema called _R_ with one attribute for each member of the set:

_{a_1_, a_2_, . . . , am}_ ∪ _{b_1_, b_2_, . . . , bn}_

We described earlier, in Section 7.3.3, how to choose a primary key for a binary relationship set. As we saw in that section, taking all the primary-key attributes from all the related entity sets serves to identify a particular tuple, but for one-to- one, many-to-one, and one-to-many relationship sets, this turns out to be a larger set of attributes than we need in the primary key. The primary key is instead chosen as follows:

• For a binary many-to-many relationship, the union of the primary-key at- tributes from the participating entity sets becomes the primary key.

• For a binary one-to-one relationship set, the primary key of either entity set can be chosen as the primary key. The choice can be made arbitrarily.

• For a binary many-to-one or one-to-many relationship set, the primary key of the entity set on the “many” side of the relationship set serves as the primary key.

7The “on delete cascade” feature of foreign key constraints in SQL is described in Section 4.4.5.  

**7.6 Reduction to Relational Schemas 287**

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

**288 Chapter 7 Database Design and the E-R Model**

_teaches_ (_ID_, _course id_, _sec id_, _semester_, _year_) _takes_ (_ID_, _course id_, _sec id_, _semester_, _year_, _grade_) _prereq_ (_course id_, _prereq id_) _advisor_ (_s ID_, _i ID_) _sec course_ (_course id_, _sec id_, _semester_, _year_) _sec time slot_ (_course id_, _sec id_, _semester_, _year_, _time slot id_) _sec class_ (_course id_, _sec id_, _semester_, _year_, _building_, _room number_) _inst dept_ (_ID_, _dept name_) _stud dept_ (_ID_, _dept name_) _course dept_ (_course id_, _dept name_)

**Figure 7.16** Schemas derived from relationship sets in the E-R diagram in Figure 7.15.

_slot_. We retained the relation created from the multivalued attribute, and named it _time slot_, but this relation may potentially have no tuples corresponding to a _time slot id_, or may have multiple tuples corresponding to a _time slot id_; thus, _time slot id_ in _sec time slot_ cannot reference this relation.

The astute reader may wonder why we have not seen the schemas _sec course_, _sec time slot_, _sec class_, _inst dept_, _stud dept_, and _course dept_ in the previous chapters. The reason is that the algorithm we have presented thus far results in some schemas that can be either eliminated or combined with other schemas. We ex- plore this issue next.

**7.6.4.1 Redundancy of Schemas**

A relationship set linking a weak entity set to the corresponding strong entity set is treated specially. As we noted in Section 7.5.6, these relationships are many-to- one and have no descriptive attributes. Furthermore, the primary key of a weak entity set includes the primary key of the strong entity set. In the E-R diagram of Figure 7.14, the weak entity set _section_ is dependent on the strong entity set _course_ via the relationship set _sec course_. The primary key of _section_ is _{course id_, _sec id_, _semester_, _year}_ and the primary key of _course_ is _course id_. Since _sec course_ has no descriptive attributes, the _sec course_ schema has attributes _course id_, _sec id_, _semester_, and _year_. The schema for the entity set _section_ includes the attributes _course id_, _sec id_, _semester_, and _year_ (among others). Every (_course id_, _sec id_, _semester_, _year_) combination in a _sec course_ relation would also be present in the relation on schema _section_, and vice versa. Thus, the _sec course_ schema is redundant.

In general, the schema for the relationship set linking a weak entity set to its corresponding strong entity set is redundant and does not need to be present in a relational database design based upon an E-R diagram.

**7.6.4.2 Combination of Schemas**

Consider a many-to-one relationship set _AB_ from entity set _A_ to entity set _B_. Using our relational-schema construction algorithm outlined previously, we get  

**7.6 Reduction to Relational Schemas 289**

three schemas: _A_, _B_, and _AB_. Suppose further that the participation of _A_ in the relationship is total; that is, every entity _a_ in the entity set _B_ must participate in the relationship _AB_. Then we can combine the schemas _A_ and _AB_ to form a single schema consisting of the union of attributes of both schemas. The primary key of the combined schema is the primary key of the entity set into whose schema the relationship set schema was merged.

To illustrate, let’s examine the various relations in the E-R diagram of Fig- ure 7.15 that satisfy the above criteria:

• _inst dept_. The schemas _instructor_ and _department_ correspond to the entity sets _A_ and _B_, respectively. Thus, the schema _inst dept_ can be combined with the _instructor_ schema. The resulting _instructor_ schema consists of the attributes _{ID_, _name_, _dept name_, _salary}_.

• _stud dept_. The schemas _student_ and _department_ correspond to the entity sets _A_ and _B_, respectively. Thus, the schema _stud dept_ can be combined with the _student_ schema. The resulting _student_ schema consists of the attributes _{ID_, _name_, _dept name_, _tot cred}_.

• _course dept_. The schemas _course_ and _department_ correspond to the entity sets _A_ and _B_, respectively. Thus, the schema _course dept_ can be combined with the _course_ schema. The resulting _course_ schema consists of the attributes _{course id_, _title_, _dept name_, _credits}_.

• _sec class_. The schemas _section_ and _classroom_ correspond to the entity sets _A_ and _B_, respectively. Thus, the schema _sec class_ can be combined with the _section_ schema. The resulting _section_ schema consists of the attributes _{course id_, _sec id_, _semester_, _year_, _building_, _room number}_.

• _sec time slot_. The schemas _section_ and _time slot_ correspond to the entity sets _A_ and _B_ respectively, Thus, the schema _sec time slot_ can be combined with the _section_ schema obtained in the previous step. The resulting _section_ schema consists of the attributes _{course id_, _sec id_, _semester_, _year_, _building_, _room number_, _time slot id}_.

In the case of one-to-one relationships, the relation schema for the relationship set can be combined with the schemas for either of the entity sets.

We can combine schemas even if the participation is partial by using null values. In the above example, if _inst dept_ were partial, then we would store null values for the _dept name_ attribute for those instructors who have no associated department.

Finally, we consider the foreign-key constraints that would have appeared in the schema representing the relationship set. There would have been foreign-key constraints referencing each of the entity sets participating in the relationship set. We drop the constraint referencing the entity set into whose schema the relationship set schema is merged, and add the other foreign-key constraints to the combined schema. For example, _inst dept_ has a foreign key constraint of the attribute _dept name_ referencing the _department_ relation. This foreign constraint is  

**290 Chapter 7 Database Design and the E-R Model**

added to the _instructor_ relation when the schema for _inst dept_ is merged into _instructor_.

**7.7 Entity-Relationship Design Issues**

The notions of an entity set and a relationship set are not precise, and it is possible to define a set of entities and the relationships among them in a number of different ways. In this section, we examine basic issues in the design of an E-R database schema. Section 7.10 covers the design process in further detail.

**7.7.1 Use of Entity Sets versus Attributes**

Consider the entity set _instructor_ with the additional attribute _phone number_ (Fig- ure 7.17a.) It can easily be argued that a phone is an entity in its own right with attributes _phone number_ and _location_; the location may be the office or home where the phone is located, with mobile (cell) phones perhaps represented by the value “mobile.” If we take this point of view, we do not add the attribute _phone number_ to the _instructor_. Rather, we create:

• A _phone_ entity set with attributes _phone number_ and _location_.

• A relationship set _inst phone_, denoting the association between instructors and the phones that they have.

This alternative is shown in Figure 7.17b. What, then, is the main difference between these two definitions of an instruc-

tor? Treating a phone as an attribute _phone number_ implies that instructors have precisely one phone number each. Treating a phone as an entity _phone_ permits instructors to have several phone numbers (including zero) associated with them. However, we could instead easily define _phone number_ as a multivalued attribute to allow multiple phones per instructor.

The main difference then is that treating a phone as an entity better models a situation where one may want to keep extra information about a phone, such as its location, or its type (mobile, IP phone, or plain old phone), or all who share

_instructor_

_ID name salary_

_phone phone\_number location_

_instructor_

_ID name salary phone\_number_

(a) (b)

_inst\_phone_

**Figure 7.17** Alternatives for adding _phone_ to the _instructor_ entity set.  

**7.7 Entity-Relationship Design Issues 291**

the phone. Thus, treating phone as an entity is more general than treating it as an attribute and is appropriate when the generality may be useful.

In contrast, it would not be appropriate to treat the attribute _name_ (of an instructor) as an entity; it is difficult to argue that _name_ is an entity in its own right (in contrast to the phone). Thus, it is appropriate to have _name_ as an attribute of the _instructor_ entity set.

Two natural questions thus arise: What constitutes an attribute, and what constitutes an entity set? Unfortunately, there are no simple answers. The distinc- tions mainly depend on the structure of the real-world enterprise being modeled, and on the semantics associated with the attribute in question.

A common mistake is to use the primary key of an entity set as an attribute of another entity set, instead of using a relationship. For example, it is incorrect to model the _ID_ of a _student_ as an attribute of an _instructor_ even if each instructor advises only one student. The relationship _advisor_ is the correct way to represent the connection between students and instructors, since it makes their connection explicit, rather than implicit via an attribute.

Another related mistake that people sometimes make is to designate the primary-key attributes of the related entity sets as attributes of the relationship set. For example, _ID_ (the primary-key attributes of _student_) and _ID_ (the primary key of _instructor_) should not appear as attributes of the relationship _advisor_. This should not be done since the primary-key attributes are already implicit in the relationship set.8

**7.7.2 Use of Entity Sets versus Relationship Sets**

It is not always clear whether an object is best expressed by an entity set or a relationship set. In Figure 7.15, we used the _takes_ relationship set to model the situation where a student takes a (section of a) course. An alternative is to imagine that there is a course-registration record for each course that each student takes. Then, we have an entity set to represent the course-registration record. Let us call that entity set _registration_. Each _registration_ entity is related to exactly one student and to exactly one section, so we have two relationship sets, one to relate course- registration records to students and one to relate course-registration records to sections. In Figure 7.18, we show the entity sets _section_ and _student_ from Figure 7.15 with the _takes_ relationship set replaced by one entity set and two relationship sets:

• _registration_, the entity set representing course-registration records.

• _section reg_, the relationship set relating _registration_ and _course_.

• _student reg_, the relationship set relating _registration_ and _student_.

Note that we use double lines to indicate total participation by _registration_ entities.

8When we create a relation schema from the E-R schema, the attributes may appear in a schema created from the _advisor_ relationship set, as we shall see later; however, they should not appear in the _advisor_ relationship set.  

**292 Chapter 7 Database Design and the E-R Model**

_registration ... ... ..._

_section sec\_id semester year_

_student ID name tot\_cred_

_section\_reg student\_reg_

**Figure 7.18** Replacement of _takes_ by _registration_ and two relationship sets

Both the approach of Figure 7.15 and that of Figure 7.18 accurately represent the university’s information, but the use of _takes_ is more compact and probably preferable. However, if the registrar’s office associates other information with a course-registration record, it might be best to make it an entity in its own right.

One possible guideline in determining whether to use an entity set or a relationship set is to designate a relationship set to describe an action that occurs between entities. This approach can also be useful in deciding whether certain attributes may be more appropriately expressed as relationships.

**7.7.3 Binary versus** _n_**\-ary Relationship Sets**

Relationships in databases are often binary. Some relationships that appear to be nonbinary could actually be better represented by several binary relationships. For instance, one could create a ternary relationship _parent_, relating a child to his/her mother and father. However, such a relationship could also be represented by two binary relationships, _mother_ and _father_, relating a child to his/her mother and father separately. Using the two relationships _mother_ and _father_ provides us a record of a child’s mother, even if we are not aware of the father’s identity; a null value would be required if the ternary relationship _parent_ is used. Using binary relationship sets is preferable in this case.

In fact, it is always possible to replace a nonbinary (_n_\-ary, for _n >_ 2) relation- ship set by a number of distinct binary relationship sets. For simplicity, consider the abstract ternary (_n_ \= 3) relationship set _R_, relating entity sets _A_, _B_, and _C_ . We replace the relationship set _R_ by an entity set _E_ , and create three relationship sets as shown in Figure 7.19:

• _RA_, relating _E_ and _A_.

• _RB_ , relating _E_ and _B_.

• _RC_ , relating _E_ and _C_ .  

**7.7 Entity-Relationship Design Issues 293**

_B R C_

_A_

_CB E_

_A_

_RA_

_RB RC_

(a) (b)

**Figure 7.19** Ternary relationship versus three binary relationships.

If the relationship set _R_ had any attributes, these are assigned to entity set _E_ ; further, a special identifying attribute is created for _E_ (since it must be possible to distinguish different entities in an entity set on the basis of their attribute values). For each relationship (_ai , bi , ci_ ) in the relationship set _R_, we create a new entity _ei_ in the entity set _E_ . Then, in each of the three new relationship sets, we insert a relationship as follows:

• (_ei , ai_ ) in _RA_.

• (_ei , bi_ ) in _RB_ .

• (_ei , ci_ ) in _RC_ .

We can generalize this process in a straightforward manner to _n_\-ary relation- ship sets. Thus, conceptually, we can restrict the E-R model to include only binary relationship sets. However, this restriction is not always desirable.

• An identifying attribute may have to be created for the entity set created to represent the relationship set. This attribute, along with the extra relationship sets required, increases the complexity of the design and (as we shall see in Section 7.6) overall storage requirements.

• An _n_\-ary relationship set shows more clearly that several entities participate in a single relationship.

• There may not be a way to translate constraints on the ternary relationship into constraints on the binary relationships. For example, consider a con- straint that says that _R_ is many-to-one from _A, B_ to _C_ ; that is, each pair of entities from _A_ and _B_ is associated with at most one _C_ entity. This constraint cannot be expressed by using cardinality constraints on the relationship sets _RA, RB_ , and _RC_ .

Consider the relationship set _proj guide_ in Section 7.2.2, relating _instructor_, _student_, and _project_. We cannot directly split _proj guide_ into binary relationships between _instructor_ and _project_ and between _instructor_ and _student_. If we did so,  

**294 Chapter 7 Database Design and the E-R Model**

_instructor_

_student_

76766 Crick

Katz

Srinivasan

Kim

Singh

Einstein

45565

10101

98345

76543

22222

98988

12345

00128

76543

76653

23121

44553

Tanaka

Shankar

Zhang

Brown

Aoi

Chavez

Peltier

May 2009

June 2007

June 2006

June 2009

June 2007

May 2007

May 2006

**Figure 7.20** _date_ as an attribute of the _student_ entity set.

we would be able to record that instructor Katz works on projects _A_ and _B_ with students Shankar and Zhang; however, we would not be able to record that Katz works on project _A_ with student Shankar and works on project _B_ with student Zhang, but does not work on project _A_ with Zhang or on project _B_ with Shankar.

The relationship set _proj guide_ can be split into binary relationships by creating a new entity set as described above. However, doing so would not be very natural.

**7.7.4 Placement of Relationship Attributes**

The cardinality ratio of a relationship can affect the placement of relationship attributes. Thus, attributes of one-to-one or one-to-many relationship sets can be associated with one of the participating entity sets, rather than with the relation- ship set. For instance, let us specify that _advisor_ is a one-to-many relationship set such that one instructor may advise several students, but each student can be advised by only a single instructor. In this case, the attribute _date_, which specifies when the instructor became the advisor of a student, could be associated with the _student_ entity set, as Figure 7.20 depicts. (To keep the figure simple, only some of the attributes of the two entity sets are shown.) Since each _student_ entity participates in a relationship with at most one instance of _instructor_, making this attribute designation has the same meaning as would placing _date_ with the _advisor_ relationship set. Attributes of a one-to-many relationship set can be repositioned to only the entity set on the “many” side of the relationship. For one-to-one rela- tionship sets, on the other hand, the relationship attribute can be associated with either one of the participating entities.

The design decision of where to place descriptive attributes in such cases —as a relationship or entity attribute—should reflect the characteristics of the enterprise being modeled. The designer may choose to retain _date_ as an attribute of _advisor_ to express explicitly that the date refers to the advising relationship and not some other aspect of the student’s university status (for example, date of acceptance to the university).  

**7.8 Extended E-R Features 295**

The choice of attribute placement is more clear-cut for many-to-many rela- tionship sets. Returning to our example, let us specify the perhaps more realistic case that _advisor_ is a many-to-many relationship set expressing that an instructor may advise one or more students, and that a student may be advised by one or more instructors. If we are to express the date on which a specific instructor became the advisor of a specific student, _date_ must be an attribute of the _advisor_ relationship set, rather than either one of the participating entities. If _date_ were an attribute of _student_, for instance, we could not determine which instructor became the advisor on that particular date. When an attribute is determined by the com- bination of participating entity sets, rather than by either entity separately, that attribute must be associated with the many-to-many relationship set. Figure 7.3 depicts the placement of _date_ as a relationship attribute; again, to keep the figure simple, only some of the attributes of the two entity sets are shown.

**7.8 Extended E-R Features**

Although the basic E-R concepts can model most database features, some aspects of a database may be more aptly expressed by certain extensions to the basic E-R model. In this section, we discuss the extended E-R features of specializa- tion, generalization, higher- and lower-level entity sets, attribute inheritance, and aggregation.

To help with the discussions, we shall use a slightly more elaborate database schema for the university. In particular, we shall model the various people within a university by defining an entity set _person_, with attributes _ID_, _name_, and _address_.

**7.8.1 Specialization**

An entity set may include subgroupings of entities that are distinct in some way from other entities in the set. For instance, a subset of entities within an entity set may have attributes that are not shared by all the entities in the entity set. The E-R model provides a means for representing these distinctive entity groupings.

As an example, the entity set _person_ may be further classified as one of the following:

• _employee_.

• _student_.

Each of these person types is described by a set of attributes that includes all the attributes of entity set _person_ plus possibly additional attributes. For exam- ple, _employee_ entities may be described further by the attribute _salary_, whereas _student_ entities may be described further by the attribute _tot cred_. The process of designating subgroupings within an entity set is called **specialization**. The spe- cialization of _person_ allows us to distinguish among person entities according to whether they correspond to employees or students: in general, a person could be an employee, a student, both, or neither.  

**296 Chapter 7 Database Design and the E-R Model**

As another example, suppose the university divides students into two cate- gories: graduate and undergraduate. Graduate students have an office assigned to them. Undergraduate students are assigned to a residential college. Each of these student types is described by a set of attributes that includes all the attributes of the entity set _student_ plus additional attributes.

The university could create two specializations of _student_, namely _graduate_ and _undergraduate_. As we saw earlier, student entities are described by the attributes _ID_, _name_, _address_, and _tot cred_. The entity set _graduate_ would have all the attributes of _student_ and an additional attribute _office number_. The entity set _undergraduate_ would have all the attributes of _student_, and an additional attribute _residential college_.

We can apply specialization repeatedly to refine a design. For instance, uni- versity employees may be further classified as one of the following:

• _instructor_.

• _secretary_.

Each of these employee types is described by a set of attributes that includes all the attributes of entity set _employee_ plus additional attributes. For example, _instructor_ entities may be described further by the attribute _rank_ while _secretary_ entities are described by the attribute _hours per week_. Further, _secretary_ entities may participate in a relationship _secretary for_ between the _secretary_ and _employee_ entity sets, which identifies the employees who are assisted by a secretary.

An entity set may be specialized by more than one distinguishing feature. In our example, the distinguishing feature among employee entities is the job the em- ployee performs. Another, coexistent, specialization could be based on whether the person is a temporary (limited term) employee or a permanent employee, re- sulting in the entity sets _temporary employee_ and _permanent employee_. When more than one specialization is formed on an entity set, a particular entity may belong to multiple specializations. For instance, a given employee may be a temporary employee who is a secretary.

In terms of an E-R diagram, specialization is depicted by a hollow arrow-head pointing from the specialized entity to the other entity (see Figure 7.21). We refer to this relationship as the ISA relationship, which stands for “is a” and represents, for example, that an instructor “is a” employee.

The way we depict specialization in an E-R diagram depends on whether an entity may belong to multiple specialized entity sets or if it must belong to at most one specialized entity set. The former case (multiple sets permitted) is called **overlapping specialization**, while the latter case (at most one permitted) is called **disjoint specialization**. For an overlapping specialization (as is the case for _student_ and _employee_ as specializations of _person_), two separate arrows are used. For a disjoint specialization (as is the case for _instructor_ and _secretary_ as specializations of _employee_), a single arrow is used. The specialization relationship may also be referred to as a **superclass-subclass** relationship. Higher- and lower-level entity  

**7.8 Extended E-R Features 297**

_person ID name address_

_student_

_instructor rank_

_secretary hours\_per\_week_

_employee salary tot\_credits_

**Figure 7.21** Specialization and generalization.

sets are depicted as regular entity sets—that is, as rectangles containing the name of the entity set.

**7.8.2 Generalization**

The refinement from an initial entity set into successive levels of entity subgroup- ings represents a **top-down** design process in which distinctions are made explicit. The design process may also proceed in a **bottom-up** manner, in which multiple entity sets are synthesized into a higher-level entity set on the basis of common features. The database designer may have first identified:

• _instructor_ entity set with attributes _instructor id_, _instructor name_, _instructor salary_, and _rank_.

• _secretary_ entity set with attributes _secretary id_, _secretary name_, _secretary salary_, and _hours per week_.

There are similarities between the _instructor_ entity set and the _secretary_ entity set in the sense that they have several attributes that are conceptually the same across the two entity sets: namely, the identifier, name, and salary attributes. This commonality can be expressed by **generalization**, which is a containment relationship that exists between a _higher-level_ entity set and one or more _lower-level_ entity sets. In our example, _employee_ is the higher-level entity set and _instructor_ and _secretary_ are lower-level entity sets. In this case, attributes that are conceptually the same had different names in the two lower-level entity sets. To create a generalization, the attributes must be given a common name and represented with the higher-level entity _person_. We can use the attribute names _ID_, _name_, _address_, as we saw in the example in Section 7.8.1.  

**298 Chapter 7 Database Design and the E-R Model**

Higher- and lower-level entity sets also may be designated by the terms **superclass** and **subclass**, respectively. The _person_ entity set is the superclass of the _employee_ and _student_ subclasses.

For all practical purposes, generalization is a simple inversion of specializa- tion. We apply both processes, in combination, in the course of designing the E-R schema for an enterprise. In terms of the E-R diagram itself, we do not distinguish between specialization and generalization. New levels of entity representation are distinguished (specialization) or synthesized (generalization) as the design schema comes to express fully the database application and the user require- ments of the database. Differences in the two approaches may be characterized by their starting point and overall goal.

Specialization stems from a single entity set; it emphasizes differences among entities within the set by creating distinct lower-level entity sets. These lower- level entity sets may have attributes, or may participate in relationships, that do not apply to all the entities in the higher-level entity set. Indeed, the reason a designer applies specialization is to represent such distinctive features. If _student_ and _employee_ have exactly the same attributes as _person_ entities, and participate in exactly the same relationships as _person_ entities, there would be no need to specialize the _person_ entity set.

Generalization proceeds from the recognition that a number of entity sets share some common features (namely, they are described by the same attributes and participate in the same relationship sets). On the basis of their commonalities, generalization synthesizes these entity sets into a single, higher-level entity set. Generalization is used to emphasize the similarities among lower-level entity sets and to hide the differences; it also permits an economy of representation in that shared attributes are not repeated.

**7.8.3 Attribute Inheritance**

A crucial property of the higher- and lower-level entities created by specialization and generalization is **attribute inheritance**. The attributes of the higher-level entity sets are said to be **inherited** by the lower-level entity sets. For example, _student_ and _employee_ inherit the attributes of _person_. Thus, _student_ is described by its _ID_, _name_, and _address_ attributes, and additionally a _tot cred_ attribute; _employee_ is described by its _ID_, _name_, and _address_ attributes, and additionally a _salary_ attribute. Attribute inheritance applies through all tiers of lower-level entity sets; thus, _instructor_ and _secretary_, which are subclasses of _employee_, inherit the attributes _ID_, _name_, and _address_ from _person_, in addition to inheriting _salary_ from _employee_.

A lower-level entity set (or subclass) also inherits participation in the relation- ship sets in which its higher-level entity (or superclass) participates. Like attribute inheritance, participation inheritance applies through all tiers of lower-level en- tity sets. For example, suppose the _person_ entity set participates in a relationship _person dept_ with _department_. Then, the _student_, _employee_, _instructor_ and _secretary_ en- tity sets, which are subclasses of the _person_ entity set, also implicitly participate in the _person dept_ relationship with _department_. The above entity sets can participate in any relationships in which the _person_ entity set participates.  

**7.8 Extended E-R Features 299**

Whether a given portion of an E-R model was arrived at by specialization or generalization, the outcome is basically the same:

• A higher-level entity set with attributes and relationships that apply to all of its lower-level entity sets.

• Lower-level entity sets with distinctive features that apply only within a particular lower-level entity set.

In what follows, although we often refer to only generalization, the properties that we discuss belong fully to both processes.

Figure 7.21 depicts a **hierarchy** of entity sets. In the figure, _employee_ is a lower-level entity set of _person_ and a higher-level entity set of the _instructor_ and _secretary_ entity sets. In a hierarchy, a given entity set may be involved as a lower- level entity set in only one ISA relationship; that is, entity sets in this diagram have only **single inheritance**. If an entity set is a lower-level entity set in more than one ISA relationship, then the entity set has **multiple inheritance**, and the resulting structure is said to be a _lattice_.

**7.8.4 Constraints on Generalizations**

To model an enterprise more accurately, the database designer may choose to place certain constraints on a particular generalization. One type of constraint involves determining which entities can be members of a given lower-level entity set. Such membership may be one of the following:

• **Condition-defined.** In condition-defined lower-level entity sets, membership is evaluated on the basis of whether or not an entity satisfies an explicit condi- tion or predicate. For example, assume that the higher-level entity set _student_ has the attribute _student type_. All _student_ entities are evaluated on the defin- ing _student type_ attribute. Only those entities that satisfy the condition _student type_ \= “graduate” are allowed to belong to the lower-level entity set _graduate student_. All entities that satisfy the condition _student type_ \= “undergraduate”

are included in _undergraduate student_. Since all the lower-level entities are evaluated on the basis of the same attribute (in this case, on _student type_), this type of generalization is said to be **attribute-defined.**

• **User-defined.** User-defined lower-level entity sets are not constrained by a membership condition; rather, the database user assigns entities to a given entity set. For instance, let us assume that, after 3 months of employment, university employees are assigned to one of four work teams. We therefore represent the teams as four lower-level entity sets of the higher-level _employee_ entity set. A given employee is not assigned to a specific team entity auto- matically on the basis of an explicit defining condition. Instead, the user in charge of this decision makes the team assignment on an individual basis. The assignment is implemented by an operation that adds an entity to an entity set.  

**300 Chapter 7 Database Design and the E-R Model**

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

**7.8 Extended E-R Features 301**

_uate student_ into _student_ is a total, disjoint generalization. The completeness and disjointness constraints, however, do not depend on each other. Constraint pat- terns may also be partial-disjoint and total-overlapping.

We can see that certain insertion and deletion requirements follow from the constraints that apply to a given generalization or specialization. For instance, when a total completeness constraint is in place, an entity inserted into a higher- level entity set must also be inserted into at least one of the lower-level entity sets. With a condition-defined constraint, all higher-level entities that satisfy the condition must be inserted into that lower-level entity set. Finally, an entity that is deleted from a higher-level entity set also is deleted from all the associated lower-level entity sets to which it belongs.

**7.8.5 Aggregation**

One limitation of the E-R model is that it cannot express relationships among relationships. To illustrate the need for such a construct, consider the ternary relationship _proj guide_, which we saw earlier, between an _instructor_, _student_ and _project_ (see Figure 7.13).

Now suppose that each instructor guiding a student on a project is required to file a monthly evaluation report. We model the evaluation report as an entity _eval- uation_, with a primary key _evaluation id_. One alternative for recording the (_student_, _project_, _instructor_) combination to which an _evaluation_ corresponds is to create a quaternary (4-way) relationship set _eval for_ between _instructor_, _student_, _project_, and _evaluation_. (A quaternary relationship is required—a binary relationship between _student_ and _evaluation_, for example, would not permit us to represent the (_project_, _instructor_) combination to which an _evaluation_ corresponds.) Using the basic E-R modeling constructs, we obtain the E-R diagram of Figure 7.22. (We have omitted the attributes of the entity sets, for simplicity.)

It appears that the relationship sets _proj guide_ and _eval for_ can be combined into one single relationship set. Nevertheless, we should not combine them into a single relationship, since some _instructor_, _student_, _project_ combinations may not have an associated _evaluation_.

There is redundant information in the resultant figure, however, since every _instructor_, _student_, _project_ combination in _eval for_ must also be in _proj guide_. If the _evaluation_ were a value rather than a entity, we could instead make _evaluation_ a multivalued composite attribute of the relationship set _proj guide_. However, this alternative may not be be an option if an _evaluation_ may also be related to other entities; for example, each evaluation report may be associated with a _secretary_ who is responsible for further processing of the evaluation report to make scholarship payments.

The best way to model a situation such as the one just described is to use ag- gregation. **Aggregation** is an abstraction through which relationships are treated as higher-level entities. Thus, for our example, we regard the relationship set _proj guide_ (relating the entity sets _instructor_, _student_, and _project_) as a higher-level entity set called _proj guide_. Such an entity set is treated in the same manner as is any other entity set. We can then create a binary relationship _eval for_ between _proj_  

**302 Chapter 7 Database Design and the E-R Model**

_project_

_evaluation_

_instructor student_

_eval\_ for_

_proj\_ guide_

**Figure 7.22** E-R diagram with redundant relationships.

_guide_ and _evaluation_ to represent which (_student_, _project_, _instructor_) combination an _evaluation_ is for. Figure 7.23 shows a notation for aggregation commonly used to represent this situation.

**7.8.6 Reduction to Relation Schemas**

We are in a position now to describe how the extended E-R features can be translated into relation schemas.

**7.8.6.1 Representation of Generalization**

There are two different methods of designing relation schemas for an E-R diagram that includes generalization. Although we refer to the generalization in Figure 7.21 in this discussion, we simplify it by including only the first tier of lower-level entity sets—that is, _employee_ and _student_. We assume that _ID_ is the primary key of _person_.

**1\.** Create a schema for the higher-level entity set. For each lower-level entity set, create a schema that includes an attribute for each of the attributes of that entity set plus one for each attribute of the primary key of the higher-level entity set. Thus, for the E-R diagram of Figure 7.21 (ignoring the _instructor_ and _secretary_ entity sets) we have three schemas:

_person_ (_ID_, _name, street, city_) _employee_ (_ID_, _salary_) _student_ (_ID_, _tot cred_)  

**7.8 Extended E-R Features 303**

_evaluation_

_proj\_ guide instructor student_

_eval\_ for_

_project_

**Figure 7.23** E-R diagram with aggregation.

The primary-key attributes of the higher-level entity set become primary- key attributes of the higher-level entity set as well as all lower-level entity sets. These can be seen underlined in the above example.

In addition, we create foreign-key constraints on the lower-level entity sets, with their primary-key attributes referencing the primary key of the relation created from the higher-level entity set. In the above example, the _ID_ attribute of _employee_ would reference the primary key of _person_, and similarly for _student_.

**2\.** An alternative representation is possible, if the generalization is disjoint and complete—that is, if no entity is a member of two lower-level entity sets directly below a higher-level entity set, and if every entity in the higher-level entity set is also a member of one of the lower-level entity sets. Here, we do not create a schema for the higher-level entity set. Instead, for each lower- level entity set, we create a schema that includes an attribute for each of the attributes of that entity set plus one for _each_ attribute of the higher-level entity set. Then, for the E-R diagram of Figure 7.21, we have two schemas:

_employee_ (_ID_, _name_, _street_, _city_, _salary_) _student_ (_ID_, _name_, _street_, _city_, _tot cred_)

Both these schemas have _ID_, which is the primary-key attribute of the higher- level entity set _person_, as their primary key.  

**304 Chapter 7 Database Design and the E-R Model**

One drawback of the second method lies in defining foreign-key constraints. To illustrate the problem, suppose we had a relationship set _R_ involving entity set _person_. With the first method, when we create a relation schema _R_ from the relationship set, we would also define a foreign-key constraint on _R_, referencing the schema _person_. Unfortunately, with the second method, we do not have a single relation to which a foreign-key constraint on _R_ can refer. To avoid this problem, we need to create a relation schema _person_ containing at least the primary-key attributes of the _person_ entity.

If the second method were used for an overlapping generalization, some values would be stored multiple times, unnecessarily. For instance, if a person is both an employee and a student, values for _street_ and _city_ would be stored twice.

If the generalization were disjoint but not complete—that is, if some person is neither an employee nor a student—then an extra schema

_person_ (_ID_, _name, street, city_)

would be required to represent such people. However, the problem with foreign- key constraints mentioned above would remain. As an attempt to work around the problem, suppose employees and students are additionally represented in the _person_ relation. Unfortunately, name, street, and city information would then be stored redundantly in the _person_ relation and the _student_ relation for students, and similarly in the _person_ relation and the _employee_ relation for employees. That suggests storing name, street, and city information only in the _person_ relation and removing that information from _student_ and _employee_. If we do that, the result is exactly the first method we presented.

**7.8.6.2 Representation of Aggregation**

Designing schemas for an E-R diagram containing aggregation is straightforward. Consider the diagram of Figure 7.23. The schema for the relationship set _eval for_ between the aggregation of _proj guide_ and the entity set _evaluation_ includes an attribute for each attribute in the primary keys of the entity set _evaluation_, and the relationship set _proj guide_. It also includes an attribute for any descriptive attributes, if they exist, of the relationship set _eval for_. We then transform the relationship sets and entity sets within the aggregated entity set following the rules we have already defined.

The rules we saw earlier for creating primary-key and foreign-key constraints on relationship sets can be applied to relationship sets involving aggregations as well, with the aggregation treated like any other entity set. The primary key of the aggregation is the primary key of its defining relationship set. No separate relation is required to represent the aggregation; the relation created from the defining relationship is used instead.

**7.9 Alternative Notations for Modeling Data**

A diagrammatic representation of the data model of an application is a very important part of designing a database schema. Creation of a database schema  

**7.9 Alternative Notations for Modeling Data 305**

requires not only data modeling experts, but also domain experts who know the requirements of the application but may not be familiar with data modeling. An intuitive diagrammatic representation is particularly important since it eases communication of information between these groups of experts.

A number of alternative notations for modeling data have been proposed, of which E-R diagrams and UML class diagrams are the most widely used. There is no universal standard for E-R diagram notation, and different books and E-R diagram software use different notations. We have chosen a particular notation

E

R

R

R

R

R

role- name

R

E

A1 A2

A2.1 A2.2

{A3} A4

E

R l..h E

E1

E2 E3

E1

E2 E3

E1

E2 E3

entity set

relationship set

identifying relationship set for weak entity set primary key

many-to-many relationship

many-to-one relationship

one-to-one relationship

cardinality limits

discriminating aribute of weak entity set

total participation of entity set in relationship

aributes: simple (A1), composite (A2) and multivalued (A3) derived (A4)

ISA: generalization or specialization

disjoint generalization

total (disjoint) generalization

role indicator

total

A1

E

A1

E R E

()

**Figure 7.24** Symbols used in the E-R notation.  

**306 Chapter 7 Database Design and the E-R Model**

in this sixth edition of this book which actually differs from the notation we used in earlier editions, for reasons that we explain later in this section.

In the rest of this section, we study some of the alternative E-R diagram notations, as well as the UML class diagram notation. To aid in comparison of our notation with these alternatives, Figure 7.24 summarizes the set of symbols we have used in our E-R diagram notation.

**7.9.1 Alternative E-R Notations**

Figure 7.25 indicates some of the alternative E-R notations that are widely used. One alternative representation of attributes of entities is to show them in ovals connected to the box representing the entity; primary key attributes are indicated by underlining them. The above notation is shown at the top of the figure. Re- lationship attributes can be similarly represented, by connecting the ovals to the diamond representing the relationship.

participation in R: total (E1) and partial (E2)

E1 E2 E2E1R R

R

entity set E with simple aribute A1, composite aribute A2, multivalued aribute A3, derived aribute A4, and primary key A1

many-to-many relationship

one-to-one relationship

many-to-one relationship

R

R

\*

\*

\*

1

1

1

R

E1

E1

E1

E2

E2

E2 E1 E2

generalization ISA ISAtotal generalizationweak entity set

A1 A2

A3

A2.1 A2.2

A4E

RE1 E2

RE1 E2

**Figure 7.25** Alternative E-R notations.  

**7.9 Alternative Notations for Modeling Data 307**

Cardinality constraints on relationships can be indicated in several different ways, as shown in Figure 7.25. In one alternative, shown on the left side of the figure, labels ∗ and 1 on the edges out of the relationship are used for depicting many-to-many, one-to-one, and many-to-one relationships. The case of one-to- many is symmetric to many-to-one, and is not shown.

In another alternative notation shown on the right side of the figure, relation- ship sets are represented by lines between entity sets, without diamonds; only binary relationships can be modeled thus. Cardinality constraints in such a no- tation are shown by “crow’s-foot” notation, as in the figure. In a relationship _R_ between _E_1 and _E_2, crow’s feet on both sides indicates a many-to-many relation- ship, while crow’s feet on just the _E_1 side indicates a many-to-one relationship from _E_1 to _E_2\. Total participation is specified in this notation by a vertical bar. Note however, that in a relationship _R_ between entities _E_1 and _E_2, if the partici- pation of _E_1 in _R_ is total, the vertical bar is placed on the opposite side, adjacent to entity _E_2\. Similarly, partial participation is indicated by using a circle, again on the opposite side.

The bottom part of Figure 7.25 shows an alternative representation of gener- alization, using triangles instead of hollow arrow-heads.

In prior editions of this text up to the fifth edition, we used ovals to represent attributes, with triangles representing generalization, as shown in Figure 7.25. The notation using ovals for attributes and diamonds for relationships is close to the original form of E-R diagrams used by Chen in his paper that introduced the notion of E-R modeling. That notation is now referred to as Chen’s notation.

The U.S. National Institute for Standards and Technology defined a standard called IDEF1X in 1993. IDEF1X uses the crow’s-foot notation, with vertical bars on the relationship edge to denote total participation and hollow circles to denote partial participation, and includes other notations that we have not shown.

With the growth in the use of Unified Markup Language (UML), described later in Section 7.9.2, we have chosen to update our E-R notation to make it closer to the form of UML class diagrams; the connections will become clear in Section 7.9.2. In comparison with our previous notation, our new notation provides a more compact representation of attributes, and is also closer to the notation supported by many E-R modeling tools, in addition to being closer to the UML class diagram notation.

There are a variety of tools for constructing E-R diagrams, each of which has its own notational variants. Some of the tools even provide a choice between several E-R notation variants. See the references in the bibliographic notes for more information.

One key difference between entity sets in an E-R diagram and the relation schemas created from such entities is that attributes in the relational schema corresponding to E-R relationships, such as the _dept name_ attribute of _instructor_, are not shown in the entity set in the E-R diagram. Some data modeling tools allow users to choose between two views of the same entity, one an entity view without such attributes, and other a relational view with such attributes.  

**308 Chapter 7 Database Design and the E-R Model**

**7.9.2 The Unified Modeling Language UML**

Entity-relationship diagrams help model the data representation component of a software system. Data representation, however, forms only one part of an overall system design. Other components include models of user interactions with the system, specification of functional modules of the system and their interaction, etc. The **Unified Modeling Language** (UML) is a standard developed under the auspices of the Object Management Group (OMG) for creating specifications of various components of a software system. Some of the parts of UML are:

• **Class diagram**. A class diagram is similar to an E-R diagram. Later in this section we illustrate a few features of class diagrams and how they relate to E-R diagrams.

• **Use case diagram**. Use case diagrams show the interaction between users and the system, in particular the steps of tasks that users perform (such as withdrawing money or registering for a course).

• **Activity diagram**. Activity diagrams depict the flow of tasks between various components of a system.

• **Implementation diagram**. Implementation diagrams show the system com- ponents and their interconnections, both at the software component level and the hardware component level.

We do not attempt to provide detailed coverage of the different parts of UML here. See the bibliographic notes for references on UML. Instead we illustrate some features of that part of UML that relates to data modeling through examples.

Figure 7.26 shows several E-R diagram constructs and their equivalent UML class diagram constructs. We describe these constructs below. UML actually mod- els objects, whereas E-R models entities. Objects are like entities, and have at- tributes, but additionally provide a set of functions (called methods) that can be invoked to compute values on the basis of attributes of the objects, or to update the object itself. Class diagrams can depict methods in addition to attributes. We cover objects in Chapter 22. UML does not support composite or multivalued attributes, and derived attributes are equivalent to methods that take no param- eters. Since classes support encapsulation, UML allows attributes and methods to be prefixed with a “+”, “-”, or “#”, which denote respectively public, private and protected access. Private attributes can only be used in methods of the class, while protected attributes can be used only in methods of the class and its subclasses; these should be familiar to anyone who knows Java, C++ or C#.

In UML terminology, relationship sets are referred to as **associations**; we shall refer to them as relationship sets for consistency with E-R terminology. We repre- sent binary relationship sets in UML by just drawing a line connecting the entity sets. We write the relationship set name adjacent to the line. We may also specify the role played by an entity set in a relationship set by writing the role name on the line, adjacent to the entity set. Alternatively, we may write the relationship set name in a box, along with attributes of the relationship set, and connect the  

**7.9 Alternative Notations for Modeling Data 309**

–A1 +M1

E

E2 E3

E1

E2 E3

E1

E2 E3

binary relationship

class with simple aributes and methods (aribute prefixes: + = public, – = private, # = protected)

overlapping generalization

disjoint generalization

A1 M1

E entity with aributes (simple, composite, multivalued, derived)

R E2E1 role1 role2

relationship aributes E2E1 role1 role2

A1 R

R cardinality constraintsE2E1

R E2E10.. \* 0..1 0..1 0.. \*

R E3

E1 E2

R E3

E1 E2n-ary

relationships

E1

E2 E3

overlapping

disjoint

**ER Diagram Notation Equivalent in UML**

R E2E1 role1 role2

R E2E1 role1 role2

A1

() ()

E1

**Figure 7.26** Symbols used in the UML class diagram notation.

box by a dotted line to the line depicting the relationship set. This box can then be treated as an entity set, in the same way as an aggregation in E-R diagrams, and can participate in relationships with other entity sets.

Since UML version 1.3, UML supports nonbinary relationships, using the same diamond notation used in E-R diagrams. Nonbinary relationships could not be directly represented in earlier versions of UML—they had to be converted to binary relationships by the technique we have seen earlier in Section 7.7.3. UML allows the diamond notation to be used even for binary relationships, but most designers use the line notation.

Cardinality constraints are specified in UML in the same way as in E-R dia- grams, in the form _l..h_, where _l_ denotes the minimum and _h_ the maximum number of relationships an entity can participate in. However, you should be aware that the positioning of the constraints is exactly the reverse of the positioning of con- straints in E-R diagrams, as shown in Figure 7.26. The constraint 0_.._∗ on the _E_2  

**310 Chapter 7 Database Design and the E-R Model**

side and 0_.._1 on the _E_1 side means that each _E_2 entity can participate in at most one relationship, whereas each _E_1 entity can participate in many relationships; in other words, the relationship is many-to-one from _E_2 to _E_1.

Single values such as 1 or ∗ may be written on edges; the single value 1 on an edge is treated as equivalent to 1_.._1, while ∗ is equivalent to 0_.._∗. UML supports generalization; the notation is basically the same as in our E-R notation, including the representation of disjoint and overlapping generalizations.

UML class diagrams include several other notations that do not correspond to the E-R notations we have seen. For example, a line between two entity sets with a small diamond at one end specifies that the entity on the diamond side contains the other entity (containment is called “aggregation” in UML terminology; do not confuse this use of aggregation with the sense in which it is used in the E-R model). For example, a vehicle entity may contain an engine entity.

UML class diagrams also provide notations to represent object-oriented lan- guage features such as interfaces. See the references in the bibliographic notes for more information on UML class diagrams.

**7.10 Other Aspects of Database Design**

Our extensive discussion of schema design in this chapter may create the false impression that schema design is the only component of a database design. There are indeed several other considerations that we address more fully in subsequent chapters, and survey briefly here.

**7.10.1 Data Constraints and Relational Database Design**

We have seen a variety of data constraints that can be expressed using SQL, including primary-key constraints, foreign-key constraints, **check** constraints, assertions, and triggers. Constraints serve several purposes. The most obvious one is the automation of consistency preservation. By expressing constraints in the SQL data-definition language, the designer is able to ensure that the database system itself enforces the constraints. This is more reliable than relying on each application program individually to enforce constraints. It also provides a central location for the update of constraints and the addition of new ones.

A further advantage of stating constraints explicitly is that certain constraints are particularly useful in designing relational database schemas. If we know, for example, that a social-security number uniquely identifies a person, then we can use a person’s social-security number to link data related to that person even if these data appear in multiple relations. Contrast that with, for example, eye color, which is not a unique identifier. Eye color could not be used to link data pertaining to a specific person across relations because that person’s data could not be distinguished from data pertaining to other people with the same eye color.

In Section 7.6, we generated a set of relation schemas for a given E-R design using the constraints specified in the design. In Chapter 8, we formalize this idea and related ones, and show how they can assist in the design of relational  

**7.10 Other Aspects of Database Design 311**

database schemas. The formal approach to relational database design allows us to state in a precise manner when a given design is a good one and to transform poor designs into better ones. We shall see that the process of starting with an entity-relationship design and generating relation schemas algorithmically from that design provides a good start to the design process.

Data constraints are useful as well in determining the physical structure of data. It may be useful to store data that are closely related to each other in physical proximity on disk so as to gain efficiencies in disk access. Certain index structures work better when the index is on a primary key.

Constraint enforcement comes at a potentially high price in performance each time the database is updated. For each update, the system must check all of the constraints and either reject updates that fail the constraints or execute appropriate triggers. The significance of the performance penalty depends not only on the frequency of update but also on how the database is designed. Indeed efficiency of the testing of certain types of constraints is an important aspect of the discussion of relational database schema design in Chapter 8.

**7.10.2 Usage Requirements: Queries, Performance**

Database system performance is a critical aspect of most enterprise information systems. Performance pertains not only to the efficient use of the computing and storage hardware being used, but also to the efficiency of people who interact with the system and of processes that depend upon database data.

There are two main metrics for performance:

• **Throughput**—the number of queries or updates (often referred to as _trans- actions_) that can be processed on average per unit of time.

• **Response time**—the amount of time a _single_ transaction takes from start to finish in either the average case or the worst case.

Systems that process large numbers of transactions in a batch style focus on having high throughput. Systems that interact with people or time-critical systems often focus on response time. These two metrics are not equivalent. High throughput arises from obtaining high utilization of system components. Doing so may result in certain transactions being delayed until such time that they can be run more efficiently. Those delayed transactions suffer poor response time.

Most commercial database systems historically have focused on throughput; however, a variety of applications including Web-based applications and telecom- munication information systems require good response time on average and a reasonable bound on worst-case response time.

An understanding of types of queries that are expected to be the most frequent helps in the design process. Queries that involve joins require more resources to evaluate than those that do not. In cases where a join is required, the database administrator may choose to create an index that facilitates evaluation of that join. For queries—whether a join is involved or not—indices can be created to speed evaluation of selection predicates (SQL **where** clause) that are likely to appear.  

**312 Chapter 7 Database Design and the E-R Model**

Another aspect of queries that affects the choice of indices is the relative mix of update and read operations. While an index may speed queries, it also slows updates, which are forced to do extra work to maintain the accuracy of the index.

**7.10.3 Authorization Requirements**

Authorization constraints affect design of the database as well because SQL allows access to be granted to users on the basis of components of the logical design of the database. A relation schema may need to be decomposed into two or more schemas to facilitate the granting of access rights in SQL. For example, an employee record may include data relating to payroll, job functions, and medical benefits. Because different administrative units of the enterprise may manage each of these types of data, some users will need access to payroll data while being denied access to the job data, medical data, etc. If these data are all in one relation, the desired division of access, though still feasible through the use of views, is more cumbersome. Division of data in this manner becomes even more critical when the data are distributed across systems in a computer network, an issue we consider in Chapter 19.

**7.10.4 Data Flow, Workflow**

Database applications are often part of a larger enterprise application that in- teracts not only with the database system but also with various specialized ap- plications. For example, in a manufacturing company, a computer-aided design (CAD) system may assist in the design of new products. The CAD system may extract data from the database via an SQL statement, process the data internally, perhaps interacting with a product designer, and then update the database. Dur- ing this process, control of the data may pass among several product designers as well as other people. As another example, consider a travel-expense report. It is created by an employee returning from a business trip (possibly by means of a special software package) and is subsequently routed to the employee’s manager, perhaps other higher-level managers, and eventually to the accounting depart- ment for payment (at which point it interacts with the enterprise’s accounting information systems).

The term _workflow_ refers to the combination of data and tasks involved in pro- cesses like those of the preceding examples. Workflows interact with the database system as they move among users and users perform their tasks on the workflow. In addition to the data on which workflows operate, the database may store data about the workflow itself, including the tasks making up a workflow and how they are to be routed among users. Workflows thus specify a series of queries and updates to the database that may be taken into account as part of the database- design process. Put in other terms, modeling the enterprise requires us not only to understand the semantics of the data but also the business processes that use those data.  

**7.11 Summary 313**

**7.10.5 Other Issues in Database Design**

Database design is usually not a one-time activity. The needs of an organization evolve continually, and the data that it needs to store also evolve correspond- ingly. During the initial database-design phases, or during the development of an application, the database designer may realize that changes are required at the conceptual, logical, or physical schema levels. Changes in the schema can affect all aspects of the database application. A good database design anticipates future needs of an organization, and ensures that the schema requires minimal changes as the needs evolve.

It is important to distinguish between fundamental constraints that are ex- pected to be permanent and constraints that are anticipated to change. For exam- ple, the constraint that an instructor-id identify a unique instructor is fundamen- tal. On the other hand, a university may have a policy that an instructor can have only one department, which may change at a later date if joint appointments are allowed. A database design that only allows one department per instructor might require major changes if joint appointments are allowed. Such joint appointments can be represented by adding an extra relationship, without modifying the _in- structor_ relation, as long as each instructor has only one primary department affiliation; a policy change that allows more than one primary affiliation may require a larger change in the database design. A good design should account not only for current policies, but should also avoid or minimize changes due to changes that are anticipated, or have a reasonable chance of happening.

Furthermore, the enterprise that the database is serving likely interacts with other enterprises and, therefore, multiple databases may need to interact. Con- version of data between different schemas is an important problem in real-world applications. Various solutions have been proposed for this problem. The XML data model, which we study in Chapter 23, is widely used for representing data when it is exchanged between different applications.

Finally, it is worth noting that database design is a human-oriented activity in two senses: the end users of the system are people (even if an application sits between the database and the end users); and the database designer needs to interact extensively with experts in the application domain to understand the data requirements of the application. All of the people involved with the data have needs and preferences that should be taken into account in order for a database design and deployment to succeed within the enterprise.

**7.11 Summary**

• Database design mainly involves the design of the database schema. The **entity-relationship (E-R)** data model is a widely used data model for database design. It provides a convenient graphical representation to view data, rela- tionships, and constraints.

• The E-R model is intended primarily for the database-design process. It was developed to facilitate database design by allowing the specification of an  

**314 Chapter 7 Database Design and the E-R Model**

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

**Practice Exercises 315**

**Review Terms**

• Entity-relationship data model • Entity and entity set

◦ Attributes

◦ Domain

◦ Simple and composite attributes

◦ Single-valued and multivalued attributes

◦ Null value

◦ Derived attribute

◦ Superkey, candidate key, and primary key

• Relationship and relationship set

◦ Binary relationship set

◦ Degree of relationship set

◦ Descriptive attributes

◦ Superkey, candidate key, and primary key

◦ Role

◦ Recursive relationship set

• E-R diagram • Mapping cardinality:

◦ One-to-one relationship

◦ One-to-many relationship

◦ Many-to-one relationship

◦ Many-to-many relationship

• Participation

◦ Total participation

◦ Partial participation

• Weak entity sets and strong entity sets

◦ Discriminator attributes

◦ Identifying relationship

• Specialization and generalization

◦ Superclass and subclass

◦ Attribute inheritance

◦ Single and multiple inheritance

◦ Condition-defined and user- defined membership

◦ Disjoint and overlapping gener- alization

◦ Total and partial generalization

• Aggregation • UML

• UML class diagram

**Practice Exercises**

**7.1** Construct an E-R diagram for a car insurance company whose customers own one or more cars each. Each car has associated with it zero to any number of recorded accidents. Each insurance policy covers one or more cars, and has one or more premium payments associated with it. Each payment is for a particular period of time, and has an associated due date, and the date when the payment was received.

**7.2** Consider a database used to record the marks that students get in different exams of different course offerings (sections).  

**316 Chapter 7 Database Design and the E-R Model**

a. Construct an E-R diagram that models exams as entities, and uses a ternary relationship, for the database.

b. Construct an alternative E-R diagram that uses only a binary relation- ship between _student_ and _section_. Make sure that only one relationship exists between a particular _student_ and _section_ pair, yet you can rep- resent the marks that a student gets in different exams.

**7.3** Design an E-R diagram for keeping track of the exploits of your favorite sports team. You should store the matches played, the scores in each match, the players in each match, and individual player statistics for each match. Summary statistics should be modeled as derived attributes.

**7.4** Consider an E-R diagram in which the same entity set appears several times, with its attributes repeated in more than one occurrence. Why is allowing this redundancy a bad practice that one should avoid?

**7.5** An E-R diagram can be viewed as a graph. What do the following mean in terms of the structure of an enterprise schema?

a. The graph is disconnected.

b. The graph has a cycle.

**7.6** Consider the representation of a ternary relationship using binary relation- ships as described in Section 7.7.3 and illustrated in Figure 7.27b (attributes not shown).

_B C_

_A_

_CB E_

_A_

_RA_

_RB RC_

(a) (b)

(c)

_A_

_B C_

_R_

_RBC_

_RAB RAC_

**Figure 7.27** E-R diagram for Practice Exercise 7.6 and Exercise 7.24.  

**Practice Exercises 317**

a. Show a simple instance of _E, A, B, C_ , _RA, RB_ , and _RC_ that cannot correspond to any instance of _A, B, C_ , and _R_.

b. Modify the E-R diagram of Figure 7.27b to introduce constraints that will guarantee that any instance of _E, A, B, C_ , _RA, RB_ , and _RC_ that satisfies the constraints will correspond to an instance of _A, B, C_ , and _R_.

c. Modify the translation above to handle total participation constraints on the ternary relationship.

d. The above representation requires that we create a primary-key at- tribute for _E_ . Show how to treat _E_ as a weak entity set so that a primary-key attribute is not required.

**7.7** A weak entity set can always be made into a strong entity set by adding to its attributes the primary-key attributes of its identifying entity set. Outline what sort of redundancy will result if we do so.

**7.8** Consider a relation such as _sec course_, generated from a many-to-one rela- tionship _sec course_. Do the primary and foreign key constraints created on the relation enforce the many-to-one cardinality constraint? Explain why.

**7.9** Suppose the _advisor_ relationship were one-to-one. What extra constraints are required on the relation _advisor_to ensure that the one-to-one cardinality constraint is enforced?

**7.10** Consider a many-to-one relationship _R_ between entity sets _A_ and _B_. Sup- pose the relation created from _R_ is combined with the relation created from _A_. In SQL, attributes participating in a foreign key constraint can be null. Explain how a constraint on total participation of _A_ in _R_ can be enforced using **not null** constraints in SQL.

**7.11** In SQL, foreign key constraints can only reference the primary key attributes of the referenced relation, or other attributes declared to be a super key us- ing the **unique** constraint. As a result, total participation constraints on a many-to-many relationship (or on the “one” side of a one-to-many rela- tionship) cannot be enforced on the relations created from the relationship, using primary key, foreign key and not null constraints on the relations.

a. Explain why.

b. Explain how to enforce total participation constraints using com- plex check constraints or assertions (see Section 4.4.7). (Unfortunately, these features are not supported on any widely used database cur- rently.)

**7.12** Figure 7.28 shows a lattice structure of generalization and specialization (attributes not shown). For entity sets _A_, _B_, and _C_ , explain how attributes  

**318 Chapter 7 Database Design and the E-R Model**

_X Y_

_A B C_

**Figure 7.28** E-R diagram for Practice Exercise 7.12.

are inherited from the higher-level entity sets _X_ and _Y_. Discuss how to handle a case where an attribute of _X_ has the same name as some attribute of _Y_.

**7.13 Temporal changes**: An E-R diagram usually models the state of an enter- prise at a point in time. Suppose we wish to track _temporal changes_, that is, changes to data over time. For example, Zhang may have been a student between 1 September 2005 31 May 2009, while Shankar may have had in- structor Einstein as advisor from 31 May 2008 to 5 December 2008, and again from 1 June 2009 to 5 January 2010. Similarly, attribute values of an entity or relationship, such as _title_ and _credits_ of _course_, _salary_, or even _name_ of _instructor_, and _tot cred_ of _student_, can change over time.

One way to model temporal changes is as follows. We define a new data type called **valid time**, which is a time-interval, or a set of time-intervals. We then associate a _valid time_ attribute with each entity and relationship, recording the time periods during which the entity or relationship is valid. The end-time of an interval can be infinity; for example, if Shankar became a student on 2 September 2008, and is still a student, we can represent the end-time of the _valid time_ interval as infinity for the Shankar entity. Similarly, we model attributes that can change over time as a set of values, each with its own _valid time_.

a. Draw an E-R diagram with the _student_ and _instructor_ entities, and the _advisor_ relationship, with the above extensions to track temporal changes.

b. Convert the above E-R diagram into a set of relations.

It should be clear that the set of relations generated above is rather complex, leading to difficulties in tasks such as writing queries in SQL. An alterna- tive approach, which is used more widely is to ignore temporal changes when designing the E-R model (in particular, temporal changes to attribute values), and to modify the relations generated from the E-R model to track temporal changes, as discussed later in Section 8.9.  

**Exercises 319**

**Exercises**

**7.14** Explain the distinctions among the terms primary key, candidate key, and superkey.

**7.15** Construct an E-R diagram for a hospital with a set of patients and a set of medical doctors. Associate with each patient a log of the various tests and examinations conducted.

**7.16** Construct appropriate relation schemas for each of the E-R diagrams in Practice Exercises 7.1 to 7.3.

**7.17** Extend the E-R diagram of Practice Exercise 7.3 to track the same informa- tion for all teams in a league.

**7.18** Explain the difference between a weak and a strong entity set.

**7.19** We can convert any weak entity set to a strong entity set by simply adding appropriate attributes. Why, then, do we have weak entity sets?

**7.20** Consider the E-R diagram in Figure 7.29, which models an online bookstore.

a. List the entity sets and their primary keys.

b. Suppose the bookstore adds Blu-ray discs and downloadable video to its collection. The same item may be present in one or both formats, with differing prices. Extend the E-R diagram to model this addition, ignoring the effect on shopping baskets.

c. Now extend the E-R diagram, using generalization, to model the case where a shopping basket may contain any combination of books, Blu-ray discs, or downloadable video.

**7.21** Design a database for an automobile company to provide to its dealers to assist them in maintaining customer records and dealer inventory and to assist sales staff in ordering cars.

Each vehicle is identified by a vehicle identification number (VIN). Each individual vehicle is a particular model of a particular brand offered by the company (e.g., the XF is a model of the car brand Jaguar of Tata Motors). Each model can be offered with a variety of options, but an individual car may have only some (or none) of the available options. The database needs to store information about models, brands, and options, as well as information about individual dealers, customers, and cars.

Your design should include an E-R diagram, a set of relational schemas, and a list of constraints, including primary-key and foreign-key constraints.

**7.22** Design a database for a world-wide package delivery company (e.g., DHL or FedEX). The database must be able to keep track of customers (who ship items) and customers (who receive items); some customers may do both.  

**320 Chapter 7 Database Design and the E-R Model**

_author name address URL_

_written\_by published\_by_

_contains_

_number_

_number_

_stocks_

_book_

_shopping\_basket_

_basket\_id_

_warehouse_

_basket\_of ISBN title year price_

_code address phone_

_publisher name address phone URL_

_customer email name address phone_

**Figure 7.29** E-R diagram for Exercise 7.20.

Each package must be identifiable and trackable, so the database must be able to store the location of the package and its history of locations. Locations include trucks, planes, airports, and warehouses.

Your design should include an E-R diagram, a set of relational schemas, and a list of constraints, including primary-key and foreign-key constraints.

**7.23** Design a database for an airline. The database must keep track of cus- tomers and their reservations, flights and their status, seat assignments on individual flights, and the schedule and routing of future flights.

Your design should include an E-R diagram, a set of relational schemas, and a list of constraints, including primary-key and foreign-key constraints.

**7.24** In Section 7.7.3, we represented a ternary relationship (repeated in Fig- ure 7.27a) using binary relationships, as shown in Figure 7.27b. Consider the alternative shown in Figure 7.27c. Discuss the relative merits of these two alternative representations of a ternary relationship by binary relation- ships.  

**Bibliographical Notes 321**

**7.25** Consider the relation schemas shown in Section 7.6, which were generated from the E-R diagram in Figure 7.15. For each schema, specify what foreign- key constraints, if any, should be created.

**7.26** Design a generalization–specialization hierarchy for a motor vehicle sales company. The company sells motorcycles, passenger cars, vans, and buses. Justify your placement of attributes at each level of the hierarchy. Explain why they should not be placed at a higher or lower level.

**7.27** Explain the distinction between condition-defined and user-defined con- straints. Which of these constraints can the system check automatically? Explain your answer.

**7.28** Explain the distinction between disjoint and overlapping constraints.

**7.29** Explain the distinction between total and partial constraints.

**Tools**

Many database systems provide tools for database design that support E-R dia- grams. These tools help a designer create E-Rdiagrams, and they can automatically create corresponding tables in a database. See bibliographic notes of Chapter 1 for references to database-system vendors’ Web sites.

There are also several database-independent data modeling tools that sup- port E-R diagrams and UML class diagrams. The drawing tool Dia, which is available as freeware, supports E-R diagrams and UML class diagrams. Com- mercial tools include IBM Rational Rose (www.ibm.com/software/rational), Microsoft Visio (see www.microsoft.com/office/visio), CA’s ERwin (www.ca.com/us/data- modeling.aspx), Poseidon for UML (www.gentleware.com), and SmartDraw (www.smartdraw.com).

**Bibliographical Notes**

The E-R data model was introduced by Chen [1976]. A logical design methodology for relational databases using the extended E-R model is presented by Teorey et al. [1986]. The Integration Definition for Information Modeling (IDEF1X) standard NIST [1993] released by the United States National Institute of Standards and Technology (NIST) defined standards for E-R diagrams. However, a variety of E-R notations are in use today.

Thalheim [2000] provides a detailed textbook coverage of research in E-R modeling. Basic textbook discussions are offered by Batini et al. [1992] and Elmasri and Navathe [2006]. Davis et al. [1983] provides a collection of papers on the E-R model.

As of 2009, the current UML version was 2.2, with UML version 2.3 near final adoption. See www.uml.org for more information on UML standards and tools.  

_This page intentionally left blank_  

**_C H A P T E R_8 Relational Database Design**

In this chapter, we consider the problem of designing a schema for a relational database. Many of the issues in doing so are similar to design issues we considered in Chapter 7 using the E-R model.

In general, the goal of relational database design is to generate a set of relation schemas that allows us to store information without unnecessary redundancy, yet also allows us to retrieve information easily. This is accomplished by designing schemas that are in an appropriate _normal form_. To determine whether a relation schema is in one of the desirable normal forms, we need information about the real-world enterprise that we are modeling with the database. Some of this information exists in a well-designed E-R diagram, but additional information about the enterprise may be needed as well.

In this chapter, we introduce a formal approach to relational database design based on the notion of functional dependencies. We then define normal forms in terms of functional dependencies and other types of data dependencies. First, however, we view the problem of relational design from the standpoint of the schemas derived from a given entity-relationship design.

**8.1 Features of Good Relational Designs**

Our study of entity-relationship design in Chapter 7 provides an excellent starting point for creating a relational database design. We saw in Section 7.6 that it is possible to generate a set of relation schemas directly from the E-R design. Obviously, the goodness (or badness) of the resulting set of schemas depends on how good the E-R design was in the first place. Later in this chapter, we shall study precise ways of assessing the desirability of a collection of relation schemas. However, we can go a long way toward a good design using concepts we have already studied.

For ease of reference, we repeat the schemas for the university database in Figure 8.1.

**323**  

**324 Chapter 8 Relational Database Design**

_classroom_(_building_, _room number_, _capacity_) _department_(_dept name_, _building_, _budget_) _course_(_course id_, _title_, _dept name_, _credits_) _instructor_(_ID_, _name_, _dept name_, _salary_) _section_(_course id_, _sec id_, _semester_, _year_, _building_, _room number_, _time slot id_) _teaches_(_ID_, _course id_, _sec id_, _semester_, _year_) _student_(_ID_, _name_, _dept name_, _tot cred_) _takes_(_ID_, _course id_, _sec id_, _semester_, _year_, _grade_) _advisor_(_s ID_, _i ID_) _time slot_(_time slot id_, _day_, _start time_, _end time_) _prereq_(_course id_, _prereq id_)

**Figure 8.1** Schema for the university database.

**8.1.1 Design Alternative: Larger Schemas**

Now, let us explore features of this relational database design as well as some alternatives. Suppose that instead of having the schemas _instructor_ and _department_, we have the schema:

_inst dept_ (_ID_, _name_, _salary_, _dept name_, _building_, _budget_)

This represents the result of a natural join on the relations corresponding to _instructor_ and _department_. This seems like a good idea because some queries can be expressed using fewer joins, until we think carefully about the facts about the university that led to our E-R design.

Let us consider the instance of the _inst dept_ relation shown in Figure 8.2. Notice that we have to repeat the department information (“building” and “budget”) once for each instructor in the department. For example, the information about the Comp. Sci. department (Taylor, 100000) is included in the tuples of instructors Katz, Srinivasan, and Brandt.

It is important that all these tuples agree as to the budget amount since other- wise our database would be inconsistent. In our original design using _instructor_ and _department_, we stored the amount of each budget exactly once. This suggests that using _inst dept_ is a bad idea since it stores the budget amounts redundantly and runs the risk that some user might update the budget amount in one tuple but not all, and thus create inconsistency.

Even if we decided to live with the redundancy problem, there is still another problem with the _inst dept_ schema. Suppose we are creating a new department in the university. In the alternative design above, we cannot represent directly the information concerning a department (_dept name_, _building_, _budget_) unless that department has at least one instructor at the university. This is because tuples in the _inst dept_ table require values for _ID_, _name_, and _salary_. This means that we cannot record information about the newly created department until the first instructor  

**8.1 Features of Good Relational Designs 325**

_ID name salary dept name building budget_

22222 Einstein 95000 Physics Watson 70000 12121 Wu 90000 Finance Painter 120000 32343 El Said 60000 History Painter 50000 45565 Katz 75000 Comp. Sci. Taylor 100000 98345 Kim 80000 Elec. Eng. Taylor 85000 76766 Crick 72000 Biology Watson 90000 10101 Srinivasan 65000 Comp. Sci. Taylor 100000 58583 Califieri 62000 History Painter 50000 83821 Brandt 92000 Comp. Sci. Taylor 100000 15151 Mozart 40000 Music Packard 80000 33456 Gold 87000 Physics Watson 70000 76543 Singh 80000 Finance Painter 120000

**Figure 8.2** The _inst dept_ table.

is hired for the new department. In the old design, the schema _department_ can handle this, but under the revised design, we would have to create a tuple with a null value for _building_ and _budget_. In some cases null values are troublesome, as we saw in our study of SQL. However, if we decide that this is not a problem to us in this case, then we can proceed to use the revised design.

**8.1.2 Design Alternative: Smaller Schemas**

Suppose again that, somehow, we had started out with the schema _inst dept_. How would we recognize that it requires repetition of information and should be split into the two schemas _instructor_ and _department_?

By observing the contents of actual relations on schema _inst dept_, we could note the repetition of information resulting from having to list the building and budget once for each instructor associated with a department. However, this is an unreliable process. A real-world database has a large number of schemas and an even larger number of attributes. The number of tuples can be in the millions or higher. Discovering repetition would be costly. There is an even more fundamental problem with this approach. It does not allow us to determine whether the lack of repetition is just a “lucky” special case or whether it is a manifestation of a general rule. In our example, how would we _know_ that in our university organization, each department (identified by its department name) _must_ reside in a single building and must have a single budget amount? Is the fact that the budget amount for the Comp. Sci. department appears three times with the same budget amount just a coincidence? We cannot answer these questions without going back to the enterprise itself and understanding its rules. In particular, we would need to discover that the university requires that every department (identified by its department name) _must_ have only one building and one budget value.

In the case of _inst dept_, our process of creating an E-R design successfully avoided the creation of this schema. However, this fortuitous situation does not  

**326 Chapter 8 Relational Database Design**

always occur. Therefore, we need to allow the database designer to specify rules such as “each specific value for _dept name_ corresponds to at most one _budget_” even in cases where _dept name_ is not the primary key for the schema in question. In other words, we need to write a rule that says “if there were a schema (_dept name_, _budget_), then _dept name_ is able to serve as the primary key.” This rule is specified as a **functional dependency**

_dept name_ → _budget_

Given such a rule, we now have sufficient information to recognize the problem of the _inst dept_ schema. Because _dept name_ cannot be the primary key for _inst dept_ (because a department may need several tuples in the relation on schema _inst dept_), the amount of a budget may have to be repeated.

Observations such as these and the rules (functional dependencies in partic- ular) that result from them allow the database designer to recognize situations where a schema ought to be split, or _decomposed_, into two or more schemas. It is not hard to see that the right way to decompose _inst dept_ is into schemas _instruc- tor_ and _department_ as in the original design. Finding the right decomposition is much harder for schemas with a large number of attributes and several functional dependencies. To deal with this, we shall rely on a formal methodology that we develop later in this chapter.

Not all decompositions of schemas are helpful. Consider an extreme case where all we had were schemas consisting of one attribute. No interesting rela- tionships of any kind could be expressed. Now consider a less extreme case where we choose to decompose the _employee_ schema (Section 7.8):

_employee_ (_ID_, _name_, _street_, _city_, _salary_)

into the following two schemas:

_employee1_ (_ID_, _name_) _employee2_ (_name_, _street_, _city_, _salary_)

The flaw in this decomposition arises from the possibility that the enterprise has two employees with the same name. This is not unlikely in practice, as many cultures have certain highly popular names. Of course each person would have a unique employee-id, which is why _ID_ can serve as the primary key. As an example, let us assume two employees, both named Kim, work at the university and have the following tuples in the relation on schema _employee_ in the original design:

(57766, Kim, Main, Perryridge, 75000) (98776, Kim, North, Hampton, 67000)  

**8.2 Atomic Domains and First Normal Form 327**

_ID name street city salary ..._

_57766 98776..._

_Kim Kim_

_Main North_

_Perryridge Hampton_

_75000 67000_

_ID name ..._

_57766 98776..._

_Kim Kim_

_name street city salary_

_75000 67000_

_Main North_

_Perryridge Hampton_

_... Kim Kim.._.

_ID name street city salary_

_employee_

_... 57766 57766 98776 98776_

_..._

_75000 67000 75000 67000_

_Perryridge Hampton Perryridge Hampton_

_Main North Main North_

_Kim Kim Kim Kim_

_natural join_

**Figure 8.3** Loss of information via a bad decomposition.

Figure 8.3 shows these tuples, the resulting tuples using the schemas resulting from the decomposition, and the result if we attempted to regenerate the origi- nal tuples using a natural join. As we see in the figure, the two original tuples appear in the result along with two new tuples that incorrectly mix data values pertaining to the two employees named Kim. Although we have more tuples, we actually have less information in the following sense. We can indicate that a certain street, city, and salary pertain to someone named Kim, but we are unable to distinguish which of the Kims. Thus, our decomposition is unable to represent certain important facts about the university employees. Clearly, we would like to avoid such decompositions. We shall refer to such decompositions as being **lossy decompositions**, and, conversely, to those that are not as **lossless decom- positions**.

**8.2 Atomic Domains and First Normal Form**

The E-R model allows entity sets and relationship sets to have attributes that have some degree of substructure. Specifically, it allows multivalued attributes such as  

**328 Chapter 8 Relational Database Design**

_phone number_ in Figure 7.11 and composite attributes (such as an attribute _address_ with component attributes _street_, _city_, _state_, and _zip_). When we create tables from E-R designs that contain these types of attributes, we eliminate this substructure. For composite attributes, we let each component be an attribute in its own right. For multivalued attributes, we create one tuple for each item in a multivalued set.

In the relational model, we formalize this idea that attributes do not have any substructure. A domain is **atomic** if elements of the domain are considered to be indivisible units. We say that a relation schema _R_ is in **first normal form** (1NF) if the domains of all attributes of _R_ are atomic.

A set of names is an example of a nonatomic value. For example, if the schema of a relation _employee_ included an attribute _children_ whose domain elements are sets of names, the schema would not be in first normal form.

Composite attributes, such as an attribute _address_ with component attributes _street_, _city_, _state_, and _zip_ also have nonatomic domains.

Integers are assumed to be atomic, so the set of integers is an atomic domain; however, the set of all sets of integers is a nonatomic domain. The distinction is that we do not normally consider integers to have subparts, but we consider sets of integers to have subparts—namely, the integers making up the set. But the important issue is not what the domain itself is, but rather how we use domain elements in our database. The domain of all integers would be nonatomic if we considered each integer to be an ordered list of digits.

As a practical illustration of the above point, consider an organization that assigns employees identification numbers of the following form: The first two letters specify the department and the remaining four digits are a unique number within the department for the employee. Examples of such numbers would be “CS001” and “EE1127”. Such identification numbers can be divided into smaller units, and are therefore nonatomic. If a relation schema had an attribute whose domain consists of identification numbers encoded as above, the schema would not be in first normal form.

When such identification numbers are used, the department of an employee can be found by writing code that breaks up the structure of an identification number. Doing so requires extra programming, and information gets encoded in the application program rather than in the database. Further problems arise if such identification numbers are used as primary keys: When an employee changes departments, the employee’s identification number must be changed everywhere it occurs, which can be a difficult task, or the code that interprets the number would give a wrong result.

From the above discussion, it may appear that our use of course identifiers such as “CS-101”, where “CS” indicates the Computer Science department, means that the domain of course identifiers is not atomic. Such a domain is not atomic as far as humans using the system are concerned. However, the database application still treats the domain as atomic, as long as it does not attempt to split the identifier and interpret parts of the identifier as a department abbreviation. The _course_ schema stores the department name as a separate attribute, and the database application can use this attribute value to find the department of a course, instead  

**8.3 Decomposition Using Functional Dependencies 329**

of interpreting particular characters of the course identifier. Thus, our university schema can be considered to be in first normal form.

The use of set-valued attributes can lead to designs with redundant storage of data, which in turn can result in inconsistencies. For instance, instead of having the relationship between instructors and sections being represented as a separate relation _teaches_, a database designer may be tempted to store a set of course section identifiers with each instructor and a set of instructor identifiers with each section. (The primary keys of _section_ and _instructor_ are used as identifiers.) Whenever data pertaining to which instructor teaches which section is changed, the update has to be performed at two places: in the set of instructors for the section, and the set of sections for the instructor. Failure to perform both updates can leave the database in an inconsistent state. Keeping only one of these sets, that either the set of instructors of a section, or the set of sections of an instructor, would avoid repeated information; however keeping only one of these would complicate some queries, and it is unclear which of the two to retain.

Some types of nonatomic values can be useful, although they should be used with care. For example, composite-valued attributes are often useful, and set- valued attributes are also useful in many cases, which is why both are supported in the E-R model. In many domains where entities have a complex structure, forcing a first normal form representation represents an unnecessary burden on the application programmer, who has to write code to convert data into atomic form. There is also the runtime overhead of converting data back and forth from the atomic form. Support for nonatomic values can thus be very useful in such domains. In fact, modern database systems do support many types of nonatomic values, as we shall see in Chapter 22. However, in this chapter we restrict ourselves to relations in first normal form and, thus, all domains are atomic.

**8.3 Decomposition Using Functional Dependencies**

In Section 8.1, we noted that there is a formal methodology for evaluating whether a relational schema should be decomposed. This methodology is based upon the concepts of keys and functional dependencies.

In discussing algorithms for relational database design, we shall need to talk about arbitrary relations and their schema, rather than talking only about examples. Recalling our introduction to the relational model in Chapter 2, we summarize our notation here.

• In general, we use Greek letters for sets of attributes (for example, ). We use a lowercase Roman letter followed by an uppercase Roman letter in parentheses to refer to a relation schema (for example, _r_ (_R_)). We use the notation _r_ (_R_) to show that the schema is for relation _r_ , with _R_ denoting the set of attributes, but at times simplify our notation to use just _R_ when the relation name does not matter to us.

Of course, a relation schema is a set of attributes, but not all sets of attributes are schemas. When we use a lowercase Greek letter, we are referring to a set  

**330 Chapter 8 Relational Database Design**

of attributes that may or may not be a schema. A Roman letter is used when we wish to indicate that the set of attributes is definitely a schema.

• When a set of attributes is a superkey, we denote it by _K_ . A superkey pertains to a specific relation schema, so we use the terminology “_K_ is a superkey of _r_ (_R_).”

• We use a lowercase name for relations. In our examples, these names are intended to be realistic (for example, _instructor_), while in our definitions and algorithms, we use single letters, like _r_ .

• A relation, of course, has a particular value at any given time; we refer to that as an instance and use the term “instance of _r_”. When it is clear that we are talking about an instance, we may use simply the relation name (for example, _r_ ).

**8.3.1 Keys and Functional Dependencies**

A database models a set of entities and relationships in the real world. There are usually a variety of constraints (rules) on the data in the real world. For example, some of the constraints that are expected to hold in a university database are:

**1\.** Students and instructors are uniquely identified by their ID.

**2\.** Each student and instructor has only one name.

**3\.** Each instructor and student is (primarily) associated with only one depart- ment.1

**4\.** Each department has only one value for its budget, and only one associated building.

An instance of a relation that satisfies all such real-world constraints is called a **legal instance** of the relation; a legal instance of a database is one where all the relation instances are legal instances.

Some of the most commonly used types of real-world constraints can be represented formally as keys (superkeys, candidate keys and primary keys), or as functional dependencies, which we define below.

In Section 2.3, we defined the notion of a _superkey_ as a set of one or more attributes that, taken collectively, allows us to identify uniquely a tuple in the relation. We restate that definition here as follows: Let _r_ (_R_) be a relation schema. A subset _K_ of _R_ is a **superkey** of _r_ (_R_) if, in any legal instance of _r_ (_R_), for all pairs _t_1 and _t_2 of tuples in the instance of _r_ if _t_1 = _t_2, then _t_1[_K_ ] = _t_2[_K_ ]. That is, no two tuples in any legal instance of relation _r_ (_R_) may have the same value on

1An instructor or a student can be associated with more than one department, for example as an adjunct faculty, or as a minor department. Our simplified university schema models only the primary department associated with each instructor or student. A real university schema would capture secondary associations in other relations.  

**8.3 Decomposition Using Functional Dependencies 331**

attribute set _K_. Clearly, if no two tuples in _r_ have the same value on _K_ , then a _K_ \-value uniquely identifies a tuple in _r_ .

Whereas a superkey is a set of attributes that uniquely identifies an entire tuple, a functional dependency allows us to express constraints that uniquely identify the values of certain attributes. Consider a relation schema _r_ (_R_), and let  ⊆ _R_ and  ⊆ _R_.

• Given an instance of _r_ (_R_), we say that the instance **satisfies** the **functional dependency**  →  if for all pairs of tuples _t_1 and _t_2 in the instance such that _t_1[] = _t_2[], it is also the case that _t_1[] = _t_2[].

• We say that the functional dependency  →  **holds** on schema _r_ (_R_) if, in every legal instance of _r_ (_R_) it satisfies the functional dependency.

Using the functional-dependency notation, we say that _K is a superkey of r_ (_R_) if the functional dependency _K_ → _R_ holds on _r_ (_R_). In other words, _K_ is a superkey if, for every legal instance of _r_ (_R_), for every pair of tuples _t_1 and _t_2 from the instance, whenever _t_1[_K_ ] = _t_2[_K_ ]_,_ it is also the case that _t_1[_R_] = _t_2[_R_] (that is, _t_1 = _t_2).2

Functional dependencies allow us to express constraints that we cannot ex- press with superkeys. In Section 8.1.2, we considered the schema:

_inst dept_ (_ID_, _name_, _salary_, _dept name_, _building_, _budget_)

in which the functional dependency _dept name_ → _budget_ holds because for each department (identified by _dept name_) there is a unique budget amount.

We denote the fact that the pair of attributes (_ID_, _dept name_) forms a superkey for _inst dept_ by writing:

_ID_, _dept name_ → _name_, _salary_, _building_, _budget_

We shall use functional dependencies in two ways:

**1\.** To test instances of relations to see whether they satisfy a given set _F_ of functional dependencies.

**2\.** To specify constraints on the set of legal relations. We shall thus concern our- selves with _only_ those relation instances that satisfy a given set of functional dependencies. If we wish to constrain ourselves to relations on schema _r_ (_R_) that satisfy a set _F_ of functional dependencies, we say that _F_ **holds** on _r_ (_R_).

Let us consider the instance of relation _r_ of Figure 8.4, to see which functional dependencies are satisfied. Observe that _A_ → _C_ is satisfied. There are two tuples

2Note that we assume here that relations are sets. SQL deals with multisets, and a **primary key** declaration in SQL for a set of attributes _K_ requires not only that _t_1 = _t_2 if _t_1[_K_ ] = _t_2[_K_ ], but also that there be no duplicate tuples. SQL also requires that attributes in the set _K_ cannot be assigned a _null_ value.  

**332 Chapter 8 Relational Database Design**

_A B C D_

_a_1 _b_1 _c_1 _d_1 _a_1 _b_2 _c_1 _d_2 _a_2 _b_2 _c_2 _d_2 _a_2 _b_3 _c_2 _d_3 _a_3 _b_3 _c_2 _d_4

**Figure 8.4** Sample instance of relation _r_.

that have an _A_ value of _a_1\. These tuples have the same _C_ value—namely, _c_1\. Similarly, the two tuples with an _A_ value of _a_2 have the same _C_ value, _c_2\. There are no other pairs of distinct tuples that have the same _A_ value. The functional dependency _C_ → _A_ is not satisfied, however. To see that it is not, consider the tuples _t_1 = (_a_2_, b_3_, c_2_, d_3) and _t_2 = (_a_3_, b_3_, c_2_, d_4). These two tuples have the same _C_ values, _c_2, but they have different _A_ values, _a_2 and _a_3, respectively. Thus, we have found a pair of tuples _t_1 and _t_2 such that _t_1[_C_] = _t_2[_C_], but _t_1[_A_] = _t_2[_A_].

Some functional dependencies are said to be **trivial** because they are satis- fied by all relations. For example, _A_ → _A_ is satisfied by all relations involving attribute _A_. Reading the definition of functional dependency literally, we see that, for all tuples _t_1 and _t_2 such that _t_1[_A_] = _t_2[_A_], it is the case that _t_1[_A_] = _t_2[_A_]. Similarly, _AB_ → _A_ is satisfied by all relations involving attribute _A_. In general, a functional dependency of the form  →  is **trivial** if  ⊆ .

It is important to realize that an instance of a relation may satisfy some func- tional dependencies that are not required to hold on the relation’s schema. In the instance of the _classroom_ relation of Figure 8.5, we see that _room number_ → _capacity_ is satisfied. However, we believe that, in the real world, two classrooms in differ- ent buildings can have the same room number but with different room capacity. Thus, it is possible, at some time, to have an instance of the _classroom_ relation in which _room number_ → _capacity_ is not satisfied. So, we would not include _room number_ → _capacity_ in the set of functional dependencies that hold on the schema

for the _classroom_ relation. However, we would expect the functional dependency _building_, _room number_ → _capacity_ to hold on the _classroom_ schema.

Given that a set of functional dependencies _F_ holds on a relation _r_ (_R_), it may be possible to infer that certain other functional dependencies must also hold on

_building room number capacity_

Packard 101 500 Painter 514 10 Taylor 3128 70 Watson 100 30 Watson 120 50

**Figure 8.5** An instance of the _classroom_ relation.  

**8.3 Decomposition Using Functional Dependencies 333**

the relation. For example, given a schema _r_ (_A, B, C_), if functional dependencies _A_ → _B_ and _B_ → _C_ , hold on _r_ , we can infer the functional dependency _A_ → _C_ must also hold on _r_ . This is because, given any value of _A_ there can be only one corresponding value for _B_, and for that value of _B_, there can only be one corresponding value for _C_ . We study later, in Section 8.4.1, how to make such inferences.

We will use the notation _F_ \+ to denote the **closure** of the set _F_ , that is, the set of all functional dependencies that can be inferred given the set _F_ . Clearly _F_ \+ contains all of the functional dependencies in _F_ .

**8.3.2 Boyce–Codd Normal Form**

One of the more desirable normal forms that we can obtain is **Boyce–Codd normal form** (**BCNF**). It eliminates all redundancy that can be discovered based on functional dependencies, though, as we shall see in Section 8.6, there may be other types of redundancy remaining. A relation schema _R_ is in BCNF with respect to a set _F_ of functional dependencies if, for all functional dependencies in _F_ \+ of the form  → , where  ⊆ _R_ and  ⊆ _R_, at least one of the following holds:

•  →  is a trivial functional dependency (that is,  ⊆ ).

•  is a superkey for schema _R_.

A database design is in BCNF if each member of the set of relation schemas that constitutes the design is in BCNF.

We have already seen in Section 8.1 an example of a relational schema that is not in BCNF:

_inst dept_ (_ID_, _name_, _salary_, _dept name_, _building_, _budget_)

The functional dependency _dept name_ → _budget_ holds on _inst dept_, but _dept name_ is not a superkey (because, a department may have a number of different instruc- tors). In Section 8.1.2, we saw that the decomposition of _inst dept_ into _instructor_ and _department_ is a better design. The _instructor_ schema is in BCNF. All of the nontrivial functional dependencies that hold, such as:

_ID_ → _name_, _dept name_, _salary_

include _ID_ on the left side of the arrow, and _ID_ is a superkey (actually, in this case, the primary key) for _instructor_. (In other words, there is no nontrivial functional dependency with any combination of _name_, _dept name_, and _salary_, without _ID_, on the side.) Thus, _instructor_ is in BCNF.

Similarly, the _department_ schema is in BCNF because all of the nontrivial func- tional dependencies that hold, such as:

_dept name_ → _building_, _budget_  

**334 Chapter 8 Relational Database Design**

include _dept name_ on the left side of the arrow, and _dept name_ is a superkey (and the primary key) for _department_. Thus, _department_ is in BCNF.

We now state a general rule for decomposing that are not in BCNF. Let _R_ be a schema that is not in BCNF. Then there is at least one nontrivial functional dependency  →  such that  is not a superkey for _R_. We replace _R_ in our design with two schemas:

• ( ∪ )

• (_R_ − ( − ))

In the case of _inst dept_ above,  = _dept name_,  = _{building_, _budget}_, and _inst dept_ is replaced by

• ( ∪ ) = (_dept name_, _building_,_budget_)

• (_R_ − ( − )) = (_ID_, _name_, _dept name_, _salary_)

In this example, it turns out that  −  = . We need to state the rule as we did so as to deal correctly with functional dependencies that have attributes that appear on both sides of the arrow. The technical reasons for this are covered later in Section 8.5.1.

When we decompose a schema that is not in BCNF, it may be that one or more of the resulting schemas are not in BCNF. In such cases, further decomposition is required, the eventual result of which is a set of BCNF schemas.

**8.3.3 BCNF and Dependency Preservation**

We have seen several ways in which to express database consistency constraints: primary-key constraints, functional dependencies, **check** constraints, assertions, and triggers. Testing these constraints each time the database is updated can be costly and, therefore, it is useful to design the database in a way that constraints can be tested efficiently. In particular, if testing a functional dependency can be done by considering just one relation, then the cost of testing this constraint is low. We shall see that, in some cases, decomposition into BCNF can prevent efficient testing of certain functional dependencies.

To illustrate this, suppose that we make a small change to our university organization. In the design of Figure 7.15, a student may have only one advisor. This follows from the relationship set _advisor_ being many-to-one from _student_ to _advisor_. The “small” change we shall make is that an instructor can be associated with only a single department and a student may have more than one advisor, but at most one from a given department.3

One way to implement this change using the E-R design is by replacing the _advisor_ relationship set with a ternary relationship set, _dept advisor_, involving entity sets _instructor_, _student_, and _department_ that is many-to-one from the pair

3Such an arrangement makes sense for students with a double major.  

**8.3 Decomposition Using Functional Dependencies 335**

_dept\_name building budget_

_department_

_dept\_advisor_

_instructor_

_ID name salary_

_student_

_ID name tot\_cred_

**Figure 8.6** The _dept advisor_ relationship set.

_{student_, _instructor}_ to _department_ as shown in Figure 8.6. The E-R diagram specifies the constraint that “a student may have more than one advisor, but at most one corresponding to a given department”.

With this new E-R diagram, the schemas for the _instructor_, _department_, and _student_ are unchanged. However, the schema derived from _dept advisor_ is now:

_dept advisor_ (_s ID_, _i ID_, _dept name_)

Although not specified in the E-R diagram, suppose we have the additional constraint that “an instructor can act as advisor for only a single department.”

Then, the following functional dependencies hold on _dept advisor_:

_i ID_ → _dept name s ID_, _dept name_ → _i ID_

The first functional dependency follows from our requirement that “an instructor can act as an advisor for only one department.” The second functional dependency follows from our requirement that “a student may have at most one advisor for a given department.”

Notice that with this design, we are forced to repeat the department name once for each time an instructor participates in a _dept advisor_ relationship. We see that _dept advisor_ is not in BCNF because _i ID_ is not a superkey. Following our rule for BCNF decomposition, we get:

(_s ID_, _i ID_) (_i ID_, _dept name_)

Both the above schemas are BCNF. (In fact, you can verify that any schema with only two attributes is in BCNF by definition.) Note however, that in our BCNF de- sign, there is no schema that includes all the attributes appearing in the functional dependency _s ID_, _dept name_ → _i ID_.  

**336 Chapter 8 Relational Database Design**

Because our design makes it computationally hard to enforce this functional dependency, we say our design is not **dependency preserving**.4 Because depen- dency preservation is usually considered desirable, we consider another normal form, weaker than BCNF, that will allow us to preserve dependencies. That normal form is called third normal form.5

**8.3.4 Third Normal Form**

BCNF requires that all nontrivial dependencies be of the form  → , where  is a superkey. Third normal form (3NF) relaxes this constraint slightly by allowing certain nontrivial functional dependencies whose left side is not a superkey. Before we define 3NF, we recall that a candidate key is a minimal superkey—that is, a superkey no proper subset of which is also a superkey.

A relation schema _R_ is in **third normal form** with respect to a set _F_ of functional dependencies if, for all functional dependencies in _F_ \+ of the form  → , where  ⊆ _R_ and  ⊆ _R_, at least one of the following holds:

•  →  is a trivial functional dependency.

•  is a superkey for _R_.

• Each attribute _A_ in  −  is contained in a candidate key for _R_.

Note that the third condition above does not say that a single candidate key must contain all the attributes in  − ; each attribute _A_ in  −  may be contained in a _different_ candidate key.

The first two alternatives are the same as the two alternatives in the definition of BCNF. The third alternative of the 3NF definition seems rather unintuitive, and it is not obvious why it is useful. It represents, in some sense, a minimal relaxation of the BCNF conditions that helps ensure that every schema has a dependency- preserving decomposition into 3NF. Its purpose will become more clear later, when we study decomposition into 3NF.

Observe that any schema that satisfies BCNF also satisfies 3NF, since each of its functional dependencies would satisfy one of the first two alternatives. BCNF is therefore a more restrictive normal form than is 3NF.

The definition of 3NF allows certain functional dependencies that are not allowed in BCNF. A dependency  →  that satisfies only the third alternative of the 3NF definition is not allowed in BCNF, but is allowed in 3NF.6

Now, let us again consider the _dept advisor_ relationship set, which has the following functional dependencies:

4Technically, it is possible that a dependency whose attributes do not all appear in any one schema is still implicitly enforced, because of the presence of other dependencies that imply it logically. We address that case later, in Section 8.4.5. 5You may have noted that we skipped second normal form. It is of historical significance only and is not used in practice. 6These dependencies are examples of **transitive dependencies** (see Practice Exercise 8.16). The original definition of 3NF was in terms of transitive dependencies. The definition we use is equivalent but easier to understand.  

**8.3 Decomposition Using Functional Dependencies 337**

_i ID_ → _dept name s ID_, _dept name_ → _i ID_

In Section 8.3.3 we argued that the functional dependency “_i ID_ → _dept name_” caused the _dept advisor_ schema not to be in BCNF. Note that here  = _i ID_,  = _dept name_, and  −  = _dept name_. Since the functional dependency _s ID_, _dept name_ →

_i ID_ holds on _dept advisor_, the attribute _dept name_ is contained in a candidate key and, therefore, _dept advisor_ is in 3NF.

We have seen the trade-off that must be made between BCNF and 3NF when there is no dependency-preserving BCNF design. These trade-offs are described in more detail in Section 8.5.4.

**8.3.5 Higher Normal Forms**

Using functional dependencies to decompose schemas may not be sufficient to avoid unnecessary repetition of information in certain cases. Consider a slight variation in the _instructor_ entity-set definition in which we record with each instructor a set of children’s names and a set of phone numbers. The phone numbers may be shared by multiple people. Thus, _phone number_ and _child name_ would be multivalued attributes and, following our rules for generating schemas from an E-R design, we would have two schemas, one for each of the multivalued attributes, _phone number_ and _child name_:

(_ID_, _child name_) (_ID_, _phone number_)

If we were to combine these schemas to get

(_ID_, _child name_, _phone number_)

we would find the result to be in BCNF because only nontrivial functional de- pendencies hold. As a result we might think that such a combination is a good idea. However, such a combination is a bad idea, as we can see by consider- ing the example of an instructor with two children and two phone numbers. For example, let the instructor with _ID_ 99999 have two children named “David” and “William” and two phone numbers, 512-555-1234 and 512-555-4321. In the combined schema, we must repeat the phone numbers once for each dependent:

(99999, David, 512-555-1234) (99999, David, 512-555-4321) (99999, William, 512-555-1234) (99999, William, 512-555-4321)

If we did not repeat the phone numbers, and stored only the first and last tuple, we would have recorded the dependent names and the phone numbers, but  

**338 Chapter 8 Relational Database Design**

the resultant tuples would imply that David corresponded to 512-555-1234, while William corresponded to 512-555-4321. As we know, this would be incorrect.

Because normal forms based on functional dependencies are not sufficient to deal with situations like this, other dependencies and normal forms have been defined. We cover these in Sections 8.6 and 8.7.

**8.4 Functional-Dependency Theory**

We have seen in our examples that it is useful to be able to reason systematically about functional dependencies as part of a process of testing schemas for BCNF or 3NF.

**8.4.1 Closure of a Set of Functional Dependencies**

We shall see that, given a set _F_ of functional dependencies on a schema, we can prove that certain other functional dependencies also hold on the schema. We say that such functional dependencies are “logically implied” by _F_. When testing for normal forms, it is not sufficient to consider the given set of functional dependencies; rather, we need to consider _all_ functional dependencies that hold on the schema.

More formally, given a relational schema _r_ (_R_), a functional dependency _f_ on _R_ is **logically implied** by a set of functional dependencies _F_ on _r_ if every instance of _r_ (_R_) that satisfies _F_ also satisfies _f_ .

Suppose we are given a relation schema _r_ (_A_, _B_, _C_ , _G_, _H_, _I_ ) and the set of functional dependencies:

_A_ → _B A_ → _C CG_ → _H CG_ → _I B_ → _H_

The functional dependency:

_A_ → _H_

is logically implied. That is, we can show that, whenever a relation satisfies our given set of functional dependencies, _A_ → _H_ must also be satisfied by that relation. Suppose that _t_1 and _t_2 are tuples such that:

_t_1[_A_] = _t_2[_A_]

Since we are given that _A_ → _B_, it follows from the definition of functional depen- dency that:

_t_1[_B_] = _t_2[_B_]  

**8.4 Functional-Dependency Theory 339**

Then, since we are given that _B_ → _H_, it follows from the definition of functional dependency that:

_t_1[_H_] = _t_2[_H_]

Therefore, we have shown that, whenever _t_1 and _t_2 are tuples such that _t_1[_A_] = _t_2[_A_], it must be that _t_1[_H_] = _t_2[_H_]. But that is exactly the definition of _A_ → _H_.

Let _F_ be a set of functional dependencies. The **closure** of _F_, denoted by _F_ +, is the set of all functional dependencies logically implied by _F_. Given _F_, we can compute _F_ \+ directly from the formal definition of functional dependency. If _F_ were large, this process would be lengthy and difficult. Such a computation of _F_ \+ requires arguments of the type just used to show that _A_ → _H_ is in the closure of our example set of dependencies.

**Axioms**, or rules of inference, provide a simpler technique for reasoning about functional dependencies. In the rules that follow, we use Greek letters (, ,  , _. . ._ ) for sets of attributes, and uppercase Roman letters from the beginning of the alphabet for individual attributes. We use  to denote  ∪ .

We can use the following three rules to find logically implied functional dependencies. By applying these rules _repeatedly_, we can find all of _F_ +, given _F_. This collection of rules is called **Armstrong’s axioms** in honor of the person who first proposed it.

• **Reflexivity rule**. If  is a set of attributes and  ⊆ , then  →  holds.

• **Augmentation rule**. If  →  holds and  is a set of attributes, then  →  holds.

• **Transitivity rule**. If  →  holds and  →  holds, then  →  holds.

Armstrong’s axioms are **sound**, because they do not generate any incorrect functional dependencies. They are **complete**, because, for a given set _F_ of func- tional dependencies, they allow us to generate all _F_ +. The bibliographical notes provide references for proofs of soundness and completeness.

Although Armstrong’s axioms are complete, it is tiresome to use them directly for the computation of _F_ +. To simplify matters further, we list additional rules. It is possible to use Armstrong’s axioms to prove that these rules are sound (see Practice Exercises 8.4 and 8.5 and Exercise 8.26).

• **Union rule**. If  →  holds and  →  holds, then  →  holds.

• **Decomposition rule**. If  →  holds, then  →  holds and  →  holds.

• **Pseudotransitivity rule**. If  →  holds and  →  holds, then  →  holds.

Let us apply our rules to the example of schema _R_ \= (_A_, _B_, _C_ , _G_, _H_, _I_ ) and the set _F_ of functional dependencies {_A_ → _B_, _A_ → _C_ , _CG_ → _H_, _CG_ → _I_ , _B_ → _H_}. We list several members of _F_ \+ here:  

**340 Chapter 8 Relational Database Design**

• _A_ → _H_. Since _A_ → _B_ and _B_ → _H_ hold, we apply the transitivity rule. Observe that it was much easier to use Armstrong’s axioms to show that _A_ → _H_ holds than it was to argue directly from the definitions, as we did earlier in this section.

• _CG_ → _HI_ . Since _CG_ → _H_ and _CG_ → _I_ , the union rule implies that _CG_ → _HI_ .

• _AG_ → _I_ . Since _A_ → _C_ and _CG_ → _I_ , the pseudotransitivity rule implies that _AG_ → _I_ holds.

Another way of finding that _AG_ → _I_ holds is as follows: We use the augmentation rule on _A_ → _C_ to infer _AG_ → _CG_. Applying the transitivity rule to this dependency and _CG_ → _I_ , we infer _AG_ → _I_ .

Figure 8.7 shows a procedure that demonstrates formally how to use Arm- strong’s axioms to compute _F_ +. In this procedure, when a functional dependency is added to _F_ +, it may be already present, and in that case there is no change to _F_ +. We shall see an alternative way of computing _F_ \+ in Section 8.4.2.

The left-hand and right-hand sides of a functional dependency are both sub- sets of _R_. Since a set of size _n_ has 2_n_ subsets, there are a total of 2_n_ × 2_n_ \= 22_n_

possible functional dependencies, where _n_ is the number of attributes in _R_. Each iteration of the repeat loop of the procedure, except the last iteration, adds at least one functional dependency to _F_ +. Thus, the procedure is guaranteed to terminate.

**8.4.2 Closure of Attribute Sets**

We say that an attribute _B_ is **functionally determined** by  if  → _B_. To test whether a set  is a superkey, we must devise an algorithm for computing the set of attributes functionally determined by . One way of doing this is to compute _F_ +, take all functional dependencies with  as the left-hand side, and take the union of the right-hand sides of all such dependencies. However, doing so can be expensive, since _F_ \+ can be large.

An efficient algorithm for computing the set of attributes functionally deter- mined by  is useful not only for testing whether  is a superkey, but also for several other tasks, as we shall see later in this section.

_F_ \+ = _F_ **repeat**

**for each** functional dependency _f_ in _F_ \+ apply reflexivity and augmentation rules on _f_ add the resulting functional dependencies to _F_ +

**for each** pair of functional dependencies _f_1 and _f_2 in _F_ \+ **if** _f_1 and _f_2 can be combined using transitivity

add the resulting functional dependency to _F_ \+ **until** _F_ \+ does not change any further

**Figure 8.7** A procedure to compute _F_ +.  

**8.4 Functional-Dependency Theory 341**

_result_ := ; **repeat**

**for each** functional dependency  →  **in** _F_ **do begin**

**if**  ⊆ _result_ **then** _result_ := _result_ ∪  ; **end**

**until** (_result_ does not change)

**Figure 8.8** An algorithm to compute +, the closure of  under _F_.

Let  be a set of attributes. We call the set of all attributes functionally de- termined by  under a set _F_ of functional dependencies the **closure** of  under _F_; we denote it by +. Figure 8.8 shows an algorithm, written in pseudocode, to compute +. The input is a set _F_ of functional dependencies and the set  of attributes. The output is stored in the variable _result_.

To illustrate how the algorithm works, we shall use it to compute (_AG_)+ with the functional dependencies defined in Section 8.4.1. We start with _result_ \= _AG_ . The first time that we execute the **repeat** loop to test each functional dependency, we find that:

• _A_ → _B_ causes us to include _B_ in _result_. To see this fact, we observe that _A_ → _B_ is in _F_, _A_ ⊆ _result_ (which is _AG_), so _result_ := _result_ ∪_B_.

• _A_ → _C_ causes _result_ to become _ABCG_.

• _CG_ → _H_ causes _result_ to become _ABCGH_.

• _CG_ → _I_ causes _result_ to become _ABCGHI_.

The second time that we execute the **repeat** loop, no new attributes are added to _result_, and the algorithm terminates.

Let us see why the algorithm of Figure 8.8 is correct. The first step is correct, since  →  always holds (by the reflexivity rule). We claim that, for any subset  of _result_,  → . Since we start the **repeat** loop with  → _result_ being true, we can add  to _result_ only if  ⊆ _result_ and  →  . But then _result_ →  by the reflexivity rule, so  →  by transitivity. Another application of transitivity shows that  →  (using  →  and  →  ). The union rule implies that  → _result_ ∪  , so  functionally determines any new result generated in the **repeat** loop. Thus, any attribute returned by the algorithm is in +.

It is easy to see that the algorithm finds all of +. If there is an attribute in + that is not yet in _result_ at any point during the execution, then there must be a functional dependency  →  for which  ⊆ _result_, and at least one attribute in  is not in _result_. When the algorithm terminates, all such functional dependencies have been processed, and the attributes in  added to _result_; we can thus be sure that all attributes in + are in _result_.  

**342 Chapter 8 Relational Database Design**

It turns out that, in the worst case, this algorithm may take an amount of time quadratic in the size of _F_. There is a faster (although slightly more complex) algorithm that runs in time linear in the size of _F_; that algorithm is presented as part of Practice Exercise 8.8.

There are several uses of the attribute closure algorithm:

• To test if  is a superkey, we compute +, and check if + contains all attributes in _R_.

• We can check if a functional dependency  →  holds (or, in other words, is in _F_ +), by checking if  ⊆ +. That is, we compute + by using attribute closure, and then check if it contains . This test is particularly useful, as we shall see later in this chapter.

• It gives us an alternative way to compute _F_ +: For each  ⊆ _R_, we find the closure +, and for each _S_ ⊆ +, we output a functional dependency  → _S_.

**8.4.3 Canonical Cover**

Suppose that we have a set of functional dependencies _F_ on a relation schema. Whenever a user performs an update on the relation, the database system must ensure that the update does not violate any functional dependencies, that is, all the functional dependencies in _F_ are satisfied in the new database state.

The system must roll back the update if it violates any functional dependen- cies in the set _F_ .

We can reduce the effort spent in checking for violations by testing a simplified set of functional dependencies that has the same closure as the given set. Any database that satisfies the simplified set of functional dependencies also satisfies the original set, and vice versa, since the two sets have the same closure. However, the simplified set is easier to test. We shall see how the simplified set can be constructed in a moment. First, we need some definitions.

An attribute of a functional dependency is said to be **extraneous** if we can remove it without changing the closure of the set of functional dependencies. The formal definition of **extraneous attributes** is as follows: Consider a set _F_ of functional dependencies and the functional dependency  →  in _F_.

• Attribute _A_ is extraneous in  if _A_ ∈ , and _F_ logically implies (_F_ − { → }) ∪ {( − _A_) → }.

• Attribute _A_ is extraneous in  if _A_ ∈ , and the set of functional dependencies (_F_ − _{_ → _}_) ∪ { → ( − _A_)} logically implies _F_.

For example, suppose we have the functional dependencies _AB_ → _C_ and _A_ → _C_ in _F_ . Then, _B_ is extraneous in _AB_ → _C_ . As another example, suppose we have the functional dependencies _AB_ → _C D_ and _A_ → _C_ in _F_ . Then _C_ would be extraneous in the right-hand side of _AB_ → _C D_.

Beware of the direction of the implications when using the definition of ex- traneous attributes: If you exchange the left-hand side with the right-hand side,  

**8.4 Functional-Dependency Theory 343**

_Fc_ \= _F_ **repeat**

Use the union rule to replace any dependencies in _Fc_ of the form 1 → 1 and 1 → 2 with 1 → 1 2.

Find a functional dependency  →  in _Fc_ with an extraneous attribute either in  or in . /\* Note: the test for extraneous attributes is done using _Fc_ , not _F_ \*/

If an extraneous attribute is found, delete it from  →  in _Fc_ . **until** (_Fc_ does not change)

**Figure 8.9** Computing canonical cover.

the implication will _always_ hold. That is, (_F_ − { → }) ∪ {( − _A_) → } al- ways logically implies _F_, and also _F_ always logically implies (_F_ − _{_ → _}_) ∪ { → ( − _A_)}.

Here is how we can test efficiently if an attribute is extraneous. Let _R_ be the relation schema, and let _F_ be the given set of functional dependencies that hold on _R_. Consider an attribute _A_ in a dependency  → .

• If _A_ ∈ , to check if _A_ is extraneous, consider the set

_F_ ′ = (_F_ − _{_ → _}_) ∪ { → ( − _A_)} and check if  → _A_can be inferred from _F_ ′. To do so, compute + (the closure of ) under _F_ ′; if + includes _A_, then _A_ is extraneous in .

• If _A_ ∈ , to check if _A_ is extraneous, let  =  − {_A_}, and check if  →  can be inferred from _F_ . To do so, compute + (the closure of  ) under _F_ ; if + includes all attributes in , then _A_ is extraneous in .

For example, suppose _F_ contains _AB_ → _C D_, _A_ → _E_ , and _E_ → _C_ . To check if _C_ is extraneous in _AB_ → _C D_, we compute the attribute closure of _AB_ under _F_ ′ = {_AB_ → _D_, _A_ → _E_ , and _E_ → _C_}. The closure is _ABC DE_ , which includes _C D_, so we infer that _C_ is extraneous.

A **canonical cover** _Fc_ for _F_ is a set of dependencies such that _F_ logically implies all dependencies in _Fc_ , and _Fc_ logically implies all dependencies in _F_. Furthermore, _Fc_ must have the following properties:

• No functional dependency in _Fc_ contains an extraneous attribute.

• Each left side of a functional dependency in _Fc_ is unique. That is, there are no two dependencies 1 → 1 and 2 → 2 in _Fc_ such that 1 = 2.

A canonical cover for a set of functional dependencies _F_ can be computed as depicted in Figure 8.9. It is important to note that when checking if an attribute is extraneous, the check uses the dependencies in the current value of _Fc_ , and **not** the dependencies in _F_ . If a functional dependency contains only one attribute  

**344 Chapter 8 Relational Database Design**

in its right-hand side, for example _A_ → _C_ , and that attribute is found to be extraneous, we would get a functional dependency with an empty right-hand side. Such functional dependencies should be deleted.

The canonical cover of _F_ , _Fc_ , can be shown to have the same closure as _F_ ; hence, testing whether _Fc_ is satisfied is equivalent to testing whether _F_ is satis- fied. However, _Fc_ is minimal in a certain sense—it does not contain extraneous attributes, and it combines functional dependencies with the same left side. It is cheaper to test _Fc_ than it is to test _F_ itself.

Consider the following set _F_ of functional dependencies on schema (_A, B, C_):

_A_ → _BC B_ → _C A_ → _B AB_ → _C_

Let us compute the canonical cover for _F_.

• There are two functional dependencies with the same set of attributes on the left side of the arrow:

_A_ → _BC A_ → _B_

We combine these functional dependencies into _A_ → _BC_.

• _A_ is extraneous in _AB_ → _C_ because _F_ logically implies (_F_ − {_AB_ → _C_}) ∪ {_B_ → _C_}. This assertion is true because _B_ → _C_ is already in our set of functional dependencies.

• _C_ is extraneous in _A_ → _BC_, since _A_ → _BC_ is logically implied by _A_ → _B_ and _B_ → _C_.

Thus, our canonical cover is:

_A_ → _B B_ → _C_

Given a set _F_ of functional dependencies, it may be that an entire functional dependency in the set is extraneous, in the sense that dropping it does not change the closure of _F_ . We can show that a canonical cover _Fc_ of _F_ contains no such extraneous functional dependency. Suppose that, to the contrary, there were such an extraneous functional dependency in _Fc_ . The right-side attributes of the de- pendency would then be extraneous, which is not possible by the definition of canonical covers.

A canonical cover might not be unique. For instance, consider the set of functional dependencies _F_ \= {_A_ → _BC_ , _B_ → _AC_ , and _C_ → _AB_}. If we apply  

**8.4 Functional-Dependency Theory 345**

the extraneity test to _A_ → _BC_ , we find that both _B_ and _C_ are extraneous under _F_ . However, it is incorrect to delete both! The algorithm for finding the canonical cover picks one of the two, and deletes it. Then,

**1\.** If _C_ is deleted, we get the set _F_ ′ = {_A_ → _B_, _B_ → _AC_ , and _C_ → _AB_}. Now, _B_ is not extraneous in the side of _A_ → _B_ under _F_ ′. Continuing the algorithm, we find _A_ and _B_ are extraneous in the right-hand side of _C_ → _AB_, leading to two canonical covers

_Fc_ \= {_A_ → _B_, _B_ → _C_ , _C_ → _A_} _Fc_ \= {_A_ → _B_, _B_ → _AC_ , _C_ → _B_}.

**2\.** If _B_ is deleted, we get the set {_A_ → _C_ , _B_ → _AC_ , and _C_ → _AB_}. This case is symmetrical to the previous case, leading to the canonical covers

_Fc_ \= {_A_ → _C_ , _C_ → _B_, and _B_ → _A_} _Fc_ \= {_A_ → _C_ , _B_ → _C_ , and _C_ → _AB_}.

As an exercise, can you find one more canonical cover for _F_ ?

**8.4.4 Lossless Decomposition**

Let _r_ (_R_) be a relation schema, and let _F_ be a set of functional dependencies on _r_ (_R_). Let _R_1 and _R_2 form a decomposition of _R_. We say that the decomposition is a **lossless decomposition** if there is no loss of information by replacing _r_ (_R_) with two relation schemas _r_1(_R_1) and _r_2(_R_2). More precisely, we say the decomposition is lossless if, for all legal database instances (that is, database instances that satisfy the specified functional dependencies and other constraints), relation _r_ contains the same set of tuples as the result of the following SQL query:

**select** \* **from** (**select** _R_1 **from** r)

**natural join** (**select** _R_2 **from** r)

This is stated more succinctly in the relational algebra as:

_R_1 (_r_ ) _ R_2 (_r_ ) = _r_

In other words, if we project _r_ onto _R_1 and _R_2, and compute the natural join of the projection results, we get back exactly _r_ . A decomposition that is not a lossless decomposition is called a **lossy decomposition**. The terms **lossless-join decomposition** and **lossy-join decomposition** are sometimes used in place of lossless decomposition and lossy decomposition.

As an example of a lossy decomposition, recall the decomposition of the _employee_ schema into:  

**346 Chapter 8 Relational Database Design**

_employee1_ (_ID_, _name_) _employee2_ (_name_, _street_, _city_, _salary_)

that we saw earlier in Section 8.1.2. As we saw in Figure 8.3, the result of _employee1  employee2_ is a superset of the original relation _employee_, but the decomposition is lossy since the join result has lost information about which employee identi- fiers correspond to which addresses and salaries, in the case where two or more employees have the same name.

We can use functional dependencies to show when certain decompositions are lossless. Let _R_, _R_1, _R_2, and _F_ be as above. _R_1 and _R_2 form a lossless decomposition of _R_ if at least one of the following functional dependencies is in _F_ +:

• _R_1 ∩ _R_2 → _R_1

• _R_1 ∩ _R_2 → _R_2

In other words, if _R_1 ∩ _R_2 forms a superkey of either _R_1 or _R_2, the decomposition of _R_ is a lossless decomposition. We can use attribute closure to test efficiently for superkeys, as we have seen earlier.

To illustrate this, consider the schema

_inst dept_ (_ID_, _name_, _salary_, _dept name_, _building_, _budget_)

that we decomposed in Section 8.1.2 into the _instructor_ and _department_ schemas:

_instructor_ (_ID_, _name_, _dept name_, _salary_) _department_ (_dept name_, _building_, _budget_)

Consider the intersection of these two schemas, which is _dept name_. We see that because _dept name_→ _dept name_, _building_, _budget_, the lossless-decomposition rule is satisfied.

For the general case of decomposition of a schema into multiple schemas at once, the test for lossless decomposition is more complicated. See the bibliograph- ical notes for references on the topic.

While the test for binary decomposition is clearly a sufficient condition for lossless decomposition, it is a necessary condition only if all constraints are func- tional dependencies. We shall see other types of constraints later (in particular, a type of constraint called multivalued dependencies discussed in Section 8.6.1), that can ensure that a decomposition is lossless even if no functional dependencies are present.

**8.4.5 Dependency Preservation**

Using the theory of functional dependencies, it is easier to characterize depen- dency preservation than using the ad-hoc approach we took in Section 8.3.3.  

**8.4 Functional-Dependency Theory 347**

Let _F_ be a set of functional dependencies on a schema _R_, and let _R_1, _R_2_, . . . , Rn_ be a decomposition of _R_. The **restriction** of _F_ to _Ri_ is the set _Fi_ of all functional dependencies in _F_ \+ that include _only_ attributes of _Ri_ . Since all functional depen- dencies in a restriction involve attributes of only one relation schema, it is possible to test such a dependency for satisfaction by checking only one relation.

Note that the definition of restriction uses all dependencies in _F_ +, not just those in _F_ . For instance, suppose _F_ \= {_A_ → _B, B_ → _C_}, and we have a decompo- sition into _AC_ and _AB_. The restriction of _F_ to _AC_ includes _A_ → _C_ , since _A_ → _C_ is in _F_ +, even though it is not in _F_ .

The set of restrictions _F_1_, F_2_, . . . , Fn_ is the set of dependencies that can be checked efficiently. We now must ask whether testing only the restrictions is sufficient. Let _F_ ′ = _F_1 ∪ _F_2 ∪ · · · ∪ _Fn_. _F_ ′ is a set of functional dependencies on schema _R_, but, in general, _F_ ′ = _F_ . However, even if _F_ ′ = _F_ , it may be that _F_ ′+ = _F_ +. If the latter is true, then every dependency in _F_ is logically implied by _F_ ′, and, if we verify that _F_ ′ is satisfied, we have verified that _F_ is satisfied. We say that a decomposition having the property _F_ ′+ = _F_ \+ is a **dependency-preserving decomposition**.

Figure 8.10 shows an algorithm for testing dependency preservation. The input is a set _D_ \= _{R_1_, R_2_, . . . , Rn}_ of decomposed relation schemas, and a set _F_ of functional dependencies. This algorithm is expensive since it requires computation of _F_ +. Instead of applying the algorithm of Figure 8.10, we consider two alternatives.

First, note that if each member of _F_ can be tested on one of the relations of the decomposition, then the decomposition is dependency preserving. This is an easy way to show dependency preservation; however, it does not always work. There are cases where, even though the decomposition is dependency preserving, there is a dependency in _F_ that cannot be tested in any one relation in the decomposition. Thus, this alternative test can be used only as a sufficient condition that is easy

compute _F_ +; **for each** schema _Ri_ in _D_ **do**

**begin** _Fi_ : = the restriction of _F_ \+ to _Ri_ ;

**end** _F_ ′ := ∅ **for each** restriction _Fi_ **do**

**begin** _F_ ′ = _F_ ′ ∪ _Fi_

**end** compute _F_ ′+; **if** (_F_ ′+ = _F_ +) **then** return (true)

**else** return (false);

**Figure 8.10** Testing for dependency preservation.  

**348 Chapter 8 Relational Database Design**

to check; if it fails we cannot conclude that the decomposition is not dependency preserving; instead we will have to apply the general test.

We now give a second alternative test for dependency preservation that avoids computing _F_ +. We explain the intuition behind the test after presenting the test. The test applies the following procedure to each  →  in _F_ .

_result_ \=  **repeat**

**for each** _Ri_ in the decomposition _t_ \= (_result_ ∩ _Ri_ )+ ∩ _Ri result_ \= _result_ ∪ _t_

**until** (_result_ does not change)

The attribute closure here is under the set of functional dependencies _F_ . If _result_ contains all attributes in , then the functional dependency  →  is preserved. The decomposition is dependency preserving if and only if the procedure shows that all the dependencies in _F_ are preserved.

The two key ideas behind the above test are as follows:

• The first idea is to test each functional dependency  →  in _F_ to see if it is preserved in _F_ ′ (where _F_ ′ is as defined in Figure 8.10). To do so, we compute the closure of  under _F_ ′; the dependency is preserved exactly when the closure includes . The decomposition is dependency preserving if (and only if) all the dependencies in _F_ are found to be preserved.

• The second idea is to use a modified form of the attribute-closure algorithm to compute closure under _F_ ′, without actually first computing _F_ ′. We wish to avoid computing _F_ ′ since computing it is quite expensive. Note that _F_ ′ is the union of _Fi_ , where _Fi_ is the restriction of _F_ on _Ri_ . The algorithm computes the attribute closure of (_result_ ∩ _Ri_ ) with respect to _F_ , intersects the closure with _Ri_ , and adds the resultant set of attributes to _result_; this sequence of steps is equivalent to computing the closure of _result_ under _Fi_ . Repeating this step for each _i_ inside the while loop gives the closure of _result_ under _F_ ′.

To understand why this modified attribute-closure approach works cor- rectly, we note that for any  ⊆ _Ri_ ,  → + is a functional dependency in _F_ +, and  → + ∩ _Ri_ is a functional dependency that is in _Fi_ , the restriction of _F_ \+ to _Ri_ . Conversely, if  →  were in _Fi_ , then  would be a subset of + ∩ _Ri_ .

This test takes polynomial time, instead of the exponential time required to compute _F_ +.

**8.5 Algorithms for Decomposition**

Real-world database schemas are much larger than the examples that fit in the pages of a book. For this reason, we need algorithms for the generation of designs  

**8.5 Algorithms for Decomposition 349**

that are in appropriate normal form. In this section, we present algorithms for BCNF and 3NF.

**8.5.1 BCNF Decomposition**

The definition of BCNF can be used directly to test if a relation is in BCNF. However, computation of _F_ \+ can be a tedious task. We first describe below simplified tests for verifying if a relation is in BCNF. If a relation is not in BCNF, it can be decomposed to create relations that are in BCNF. Later in this section, we describe an algorithm to create a lossless decomposition of a relation, such that the decomposition is in BCNF.

**8.5.1.1 Testing for BCNF**

Testing of a relation schema _R_ to see if it satisfies BCNF can be simplified in some cases:

• To check if a nontrivial dependency  →  causes a violation of BCNF, compute + (the attribute closure of ), and verify that it includes all attributes of _R_; that is, it is a superkey of _R_.

• To check if a relation schema _R_ is in BCNF, it suffices to check only the dependencies in the given set _F_ for violation of BCNF, rather than check all dependencies in _F_ +.

We can show that if none of the dependencies in _F_ causes a violation of BCNF, then none of the dependencies in _F_ \+ will cause a violation of BCNF, either.

Unfortunately, the latter procedure does not work when a relation is decomposed. That is, it _does not_ suffice to use _F_ when we test a relation _Ri_ , in a decomposition of _R_, for violation of BCNF. For example, consider relation schema _R_ (_A, B, C, D, E_), with functional dependencies _F_ containing _A_ → _B_ and _BC_ → _D_. Suppose this were decomposed into _R_1(_A, B_) and _R_2(_A, C, D, E_). Now, neither of the dependencies in _F_ contains only attributes from (_A, C, D, E_) so we might be misled into thinking _R_2 satisfies BCNF. In fact, there is a dependency _AC_ → _D_ in _F_ \+ (which can be inferred using the pseudotransitivity rule from the two dependencies in _F_ ) that shows that _R_2 is not in BCNF. Thus, we may need a dependency that is in _F_ +, but is not in _F_ , to show that a decomposed relation is not in BCNF.

An alternative BCNF test is sometimes easier than computing every depen- dency in _F_ +. To check if a relation _Ri_ in a decomposition of _R_ is in BCNF, we apply this test:

• For every subset  of attributes in _Ri_ , check that + (the attribute closure of  under _F_ ) either includes no attribute of _Ri_ − , or includes all attributes of _Ri_ .  

**350 Chapter 8 Relational Database Design**

_result_ := _{R}_; _done_ := false; compute _F_ +; **while** (**not** _done_) **do**

**if** (there is a schema _Ri_ in _result_ that is not in BCNF) **then begin**

let  →  be a nontrivial functional dependency that holds on _Ri_ such that  → _Ri_ is not in _F_ +, and  ∩  = ∅ ; _result_ := (_result_ − _Ri_ ) ∪ (_Ri_ − ) ∪ ( _,_ );

**end else** _done_ := true;

**Figure 8.11** BCNF decomposition algorithm.

If the condition is violated by some set of attributes  in _Ri_ , consider the following functional dependency, which can be shown to be present in _F_ +:

 → (+ − ) ∩ _Ri_ .

The above dependency shows that _Ri_ violates BCNF.

**8.5.1.2 BCNF Decomposition Algorithm**

We are now able to state a general method to decompose a relation schema so as to satisfy BCNF. Figure 8.11 shows an algorithm for this task. If _R_ is not in BCNF, we can decompose _R_ into a collection of BCNF schemas _R_1_, R_2_, . . . , Rn_ by the algorithm. The algorithm uses dependencies that demonstrate violation of BCNF to perform the decomposition.

The decomposition that the algorithm generates is not only in BCNF, but is also a lossless decomposition. To see why our algorithm generates only lossless decompositions, we note that, when we replace a schema _Ri_ with (_Ri_ − ) and (_,_ ), the dependency  →  holds, and (_Ri_ − ) ∩ (_,_ ) = .

If we did not require  ∩  = ∅, then those attributes in  ∩  would not appear in the schema (_Ri_ − ) and the dependency  →  would no longer hold.

It is easy to see that our decomposition of _inst dept_ in Section 8.3.2 would result from applying the algorithm. The functional dependency _dept name_ → _building_, _budget_ satisfies the  ∩  = ∅ condition and would therefore be chosen to decompose the schema.

The BCNF decomposition algorithm takes time exponential in the size of the initial schema, since the algorithm for checking if a relation in the decomposition satisfies BCNF can take exponential time. The bibliographical notes provide ref- erences to an algorithm that can compute a BCNF decomposition in polynomial time. However, the algorithm may “overnormalize,” that is, decompose a relation unnecessarily.

As a longer example of the use of the BCNF decomposition algorithm, suppose we have a database design using the _class_ schema below:  

**8.5 Algorithms for Decomposition 351**

_class_ (_course id_, _title_, _dept name_, _credits_, _sec id_, _semester_, _year_, _building_, _room number_, _capacity_, _time slot id_)

The set of functional dependencies that we require to hold on _class_ are:

_course id_ → _title_, _dept name_, _credits building_, _room number_ → _capacity course id_, _sec id_, _semester_, _year_→ _building_, _room number_, _time slot id_

A candidate key for this schema is _{course id_, _sec id_, _semester_, _year}_. We can apply the algorithm of Figure 8.11 to the _class_ example as follows:

• The functional dependency:

_course id_ → _title_, _dept name_, _credits_

holds, but _course id_ is not a superkey. Thus, _class_ is not in BCNF. We replace _class_ by:

_course_(_course id_, _title_, _dept name_, _credits_) _class-1_ (_course id_, _sec id_, _semester_, _year_, _building_, _room number_

_capacity_, _time slot id_)

The only nontrivial functional dependencies that hold on _course_ include _course id_ on the left side of the arrow. Since _course id_ is a key for _course_, the relation

_course_ is in BCNF.

• A candidate key for _class-1_ is _{course id_, _sec id_, _semester_, _year}_. The functional dependency:

_building_, _room number_ → _capacity_

holds on _class-1_, but _{building_, _room number}_ is not a superkey for _class-1_. We replace _class-1_ by:

_classroom_ (_building_, _room number_, _capacity_) _section_ (_course id_, _sec id_, _semester_, _year_,

_building_, _room number_, _time slot id_)

_classroom_ and _section_ are in BCNF.

Thus, the decomposition of _class_ results in the three relation schemas _course_, _class- room_, and _section_, each of which is in BCNF. These correspond to the schemas that we have used in this, and previous, chapters. You can verify that the decomposi- tion is lossless and dependency preserving.  

**352 Chapter 8 Relational Database Design**

let _Fc_ be a canonical cover for _F_; _i_ := 0; **for each** functional dependency  →  in _Fc_

_i_ := _i_ \+ 1; _Ri_ :=  ;

**if** none of the schemas _Rj , j_ \= 1_,_ 2_, . . . , i_ contains a candidate key for _R_ **then**

_i_ := _i_ \+ 1; _Ri_ := any candidate key for _R_;

/\* Optionally, remove redundant relations \*/ **repeat**

**if** any schema _Rj_ is contained in another schema _Rk_ **then**

/\* Delete _Rj_ \*/ _Rj_ := _Ri_ ; _i_ := _i_ \- 1;

**until** no more _Rj_ s can be deleted **return** (_R_1_, R_2_, . . . , Ri_ )

**Figure 8.12** Dependency-preserving, lossless decomposition into 3NF.

**8.5.2 3NF Decomposition**

Figure 8.12 shows an algorithm for finding a dependency-preserving, lossless decomposition into 3NF. The set of dependencies _Fc_ used in the algorithm is a canonical cover for _F_. Note that the algorithm considers the set of schemas _Rj , j_ \= 1_,_ 2_, . . . , i_ ; initially _i_ \= 0, and in this case the set is empty.

Let us apply this algorithm to our example of Section 8.3.4, where we showed that:

_dept advisor_ (_s ID_, _i ID_, _dept name_)

is in 3NF even though it is not in BCNF. The algorithm uses the following functional dependencies in _F_ :

_f_1: _i ID_ → _dept name f_2: _s ID_, _dept name_ → _i ID_

There are no extraneous attributes in any of the functional dependencies in _F_ , so _Fc_ contains _f_1 and _f_2\. The algorithm then generates as _R_1 the schema, (_i ID dept name_), and as _R_2 the schema (_s ID_, _dept name_, _i ID_). The algorithm then finds that _R_2 contains a candidate key, so no further relation schema is created.

The resultant set of schemas can contain redundant schemas, with one schema _Rk_ containing all the attributes of another schema _Rj_ . For example, _R_2 above contains all the attributes from _R_1\. The algorithm deletes all such schemas that are contained in another schema. Any dependencies that could be tested on an  

**8.5 Algorithms for Decomposition 353**

_Rj_ that is deleted can also be tested on the corresponding relation _Rk_ , and the decomposition is lossless even if _Rj_ is deleted.

Now let us consider again the _class_ schema of Section 8.5.1.2 and apply the 3NF decomposition algorithm. The set of functional dependencies we listed there happen to be a canonical cover. As a result, the algorithm gives us the same three schemas _course, classroom_, and _section_.

The above example illustrates an interesting property of the 3NF algorithm. Sometimes, the result is not only in 3NF, but also in BCNF. This suggests an alternative method of generating a BCNF design. First use the 3NF algorithm. Then, for any schema in the 3NF design that is not in BCNF, decompose using the BCNF algorithm. If the result is not dependency-preserving, revert to the 3NF design.

**8.5.3 Correctness of the 3NF Algorithm**

The 3NF algorithm ensures the preservation of dependencies by explicitly building a schema for each dependency in a canonical cover. It ensures that the decomposi- tion is a lossless decomposition by guaranteeing that at least one schema contains a candidate key for the schema being decomposed. Practice Exercise 8.14 provides some insight into the proof that this suffices to guarantee a lossless decomposition.

This algorithm is also called the **3NF synthesis algorithm**, since it takes a set of dependencies and adds one schema at a time, instead of decomposing the initial schema repeatedly. The result is not uniquely defined, since a set of functional dependencies can have more than one canonical cover, and, further, in some cases, the result of the algorithm depends on the order in which it considers the dependencies in _Fc_ . The algorithm may decompose a relation even if it is already in 3NF; however, the decomposition is still guaranteed to be in 3NF.

If a relation _Ri_ is in the decomposition generated by the synthesis algorithm, then _Ri_ is in 3NF. Recall that when we test for 3NF it suffices to consider functional dependencies whose right-hand side is a single attribute. Therefore, to see that _Ri_ is in 3NF you must convince yourself that any functional dependency  → _B_ that holds on _Ri_ satisfies the definition of 3NF. Assume that the dependency that generated _Ri_ in the synthesis algorithm is  → . Now, _B_ must be in  or , since _B_ is in _Ri_ and  →  generated _Ri_ . Let us consider the three possible cases:

• _B_ is in both  and . In this case, the dependency  →  would not have been in _Fc_ since _B_ would be extraneous in . Thus, this case cannot hold.

• _B_ is in  but not . Consider two cases:

◦  is a superkey. The second condition of 3NF is satisfied.

◦  is not a superkey. Then  must contain some attribute not in  . Now, since  → _B_ is in _F_ +, it must be derivable from _Fc_ by using the attribute closure algorithm on  . The derivation could not have used  → — if it had been used,  must be contained in the attribute closure of  , which is not possible, since we assumed  is not a superkey. Now, using  → ( − {_B_}) and  → _B_, we can derive  → _B_ (since  ⊆ , and   

**354 Chapter 8 Relational Database Design**

cannot contain _B_ because  → _B_ is nontrivial). This would imply that _B_ is extraneous in the right-hand side of  → , which is not possible since  →  is in the canonical cover _Fc_ . Thus, if _B_ is in , then  must be a superkey, and the second condition of 3NF must be satisfied.

• _B_ is in  but not . Since  is a candidate key, the third alternative in the definition of 3NF is satisfied.

Interestingly, the algorithm we described for decomposition into 3NF can be implemented in polynomial time, even though testing a given relation to see if it satisfies 3NF is NP-hard (which means that it is very unlikely that a polynomial- time algorithm will ever be invented for this task).

**8.5.4 Comparison of BCNF and 3NF**

Of the two normal forms for relational database schemas, 3NF and BCNF there are advantages to 3NF in that we know that it is always possible to obtain a 3NF design without sacrificing losslessness or dependency preservation. Nevertheless, there are disadvantages to 3NF: We may have to use null values to represent some of the possible meaningful relationships among data items, and there is the problem of repetition of information.

Our goals of database design with functional dependencies are:

**1\.** BCNF.

**2\.** Losslessness.

**3\.** Dependency preservation.

Since it is not always possible to satisfy all three, we may be forced to choose between BCNF and dependency preservation with 3NF.

It is worth noting that SQL does not provide a way of specifying functional dependencies, except for the special case of declaring superkeys by using the **primary key** or **unique** constraints. It is possible, although a little complicated, to write assertions that enforce a functional dependency (see Practice Exercise 8.9); unfortunately, currently no database system supports the complex assertions that are required to enforce a functional dependency, and the assertions would be expensive to test. Thus even if we had a dependency-preserving decomposition, if we use standard SQL we can test efficiently only those functional dependencies whose left-hand side is a key.

Although testing functional dependencies may involve a join if the decom- position is not dependency preserving, we could in principle reduce the cost by using materialized views, which many database systems support, provided the database system supports primary key constraints on materialized views. Given a BCNF decomposition that is not dependency preserving, we consider each de- pendency in a canonical cover _Fc_ that is not preserved in the decomposition. For each such dependency  → , we define a materialized view that computes a  

**8.6 Decomposition Using Multivalued Dependencies 355**

join of all relations in the decomposition, and projects the result on . The func- tional dependency can be tested easily on the materialized view, using one of the constraints **unique** () or **primary key** ().

On the negative side, there is a space and time overhead due to the material- ized view, but on the positive side, the application programmer need not worry about writing code to keep redundant data consistent on updates; it is the job of the database system to maintain the materialized view, that is, keep it up to date when the database is updated. (Later in the book, in Section 13.5, we outline how a database system can perform materialized view maintenance efficiently.)

Unfortunately, most current database systems do not support constraints on materialized views. Although the Oracle database does support constraints on materialized views, by default it performs view maintenance when the view is accessed, not when the underlying relation is updated;7 as a result a constraint violation may get detected well after the update has been performed, which makes the detection useless.

Thus, in case we are not able to get a dependency-preserving BCNF decom- position, it is generally preferable to opt for BCNF, since checking functional dependencies other than primary key constraints is difficult in SQL.

**8.6 Decomposition Using Multivalued Dependencies**

Some relation schemas, even though they are in BCNF, do not seem to be suffi- ciently normalized, in the sense that they still suffer from the problem of repetition of information. Consider a variation of the university organization where an in- structor may be associated with multiple departments.

_inst_ (_ID_, _dept name_, _name_, _street_, _city_)

The astute reader will recognize this schema as a non-BCNF schema because of the functional dependency

_ID_ → _name_, _street_, _city_

and because _ID_ is not a key for _inst_. Further assume that an instructor may have several addresses (say, a winter

home and a summer home). Then, we no longer wish to enforce the functional dependency “_ID_→ _street_, _city_”, though, of course, we still want to enforce “_ID_ → _name_” (that is, the university is not dealing with instructors who operate under multiple aliases!). Following the BCNF decomposition algorithm, we obtain two schemas:

7At least as of Oracle version 10g.  

**356 Chapter 8 Relational Database Design**

_r_1 (_ID_, _name_) _r_2 (_ID_, _dept name_, _street_, _city_)

Both of these are in BCNF (recall that an instructor can be associated with multi- ple departments and a department may have several instructors, and therefore, neither “_ID_ → _dept name_” nor “_dept name_ → _ID_” hold).

Despite _r_2 being in BCNF, there is redundancy. We repeat the address informa- tion of each residence of an instructor once for each department with which the instructor is associated. We could solve this problem by decomposing _r_2 further into:

_r_21(_dept name_, _ID_) _r_22(_ID_, _street_, _city_)

but there is no constraint that leads us to do this. To deal with this problem, we must define a new form of constraint, called

a _multivalued dependency_. As we did for functional dependencies, we shall use multivalued dependencies to define a normal form for relation schemas. This normal form, called **fourth normal form** (4NF), is more restrictive than BCNF. We shall see that every 4NF schema is also in BCNF but there are BCNF schemas that are not in 4NF.

**8.6.1 Multivalued Dependencies**

Functional dependencies rule out certain tuples from being in a relation. If _A_ → _B_, then we cannot have two tuples with the same _A_ value but different _B_ values. Multivalued dependencies, on the other hand, do not rule out the existence of certain tuples. Instead, they _require_ that other tuples of a certain form be present in the relation. For this reason, functional dependencies sometimes are referred to as **equality-generating dependencies**, and multivalued dependencies are referred to as **tuple-generating dependencies**.

Let _r_ (_R_) be a relation schema and let  ⊆ _R_ and  ⊆ _R_. The **multivalued dependency**

 →→ 

holds on _R_ if, in any legal instance of relation _r_ (_R_), for all pairs of tuples _t_1 and _t_2 in _r_ such that _t_1[] = _t_2[], there exist tuples _t_3 and _t_4 in _r_ such that

_t_1[] = _t_2[] = _t_3[] = _t_4[] _t_3[] = _t_1[] _t_3[_R_ − ] = _t_2[_R_ − ] _t_4[] = _t_2[] _t_4[_R_ − ] = _t_1[_R_ − ]  

**8.6 Decomposition Using Multivalued Dependencies 357**

α α ββ –_R_ – _t_1 _t_2 _t_3 _t_4

_a_1 . . . _ai a_1 . . . _ai a_1 . . . _ai a_1 . . . _ai_

_ai +_ 1 . . . _aj bi +_ 1 . . . _bj ai +_ 1 . . . _aj bi +_ 1 . . . _bj_

_aj +_ 1 . . . _an bj +_ 1 . . . _bn bj +_ 1 . . . _bn aj +_ 1 . . . _an_

**Figure 8.13** Tabular representation of  →→ .

This definition is less complicated than it appears to be. Figure 8.13 gives a tabular picture of _t_1, _t_2, _t_3, and _t_4\. Intuitively, the multivalued dependency  →→  says that the relationship between  and  is independent of the relationship between  and _R_−. If the multivalued dependency  →→  is satisfied by all relations on schema _R_, then  →→  is a _trivial_ multivalued dependency on schema _R_. Thus,  →→  is trivial if  ⊆  or  ∪  = _R_.

To illustrate the difference between functional and multivalued dependencies, we consider the schema _r_2 again, and an example relation on that schema shown in Figure 8.14. We must repeat the department name once for each address that an instructor has, and we must repeat the address for each department with which an instructor is associated. This repetition is unnecessary, since the relationship between an instructor and his address is independent of the relationship between that instructor and a department. If an instructor with _ID_ 22222 is associated with the Physics department, we want that department to be associated with all of that instructor’s addresses. Thus, the relation of Figure 8.15 is illegal. To make this relation legal, we need to add the tuples (Physics, 22222, Main, Manchester) and (Math, 22222, North, Rye) to the relation of Figure 8.15.

Comparing the preceding example with our definition of multivalued depen- dency, we see that we want the multivalued dependency:

_ID_ →→ _street_, _city_

to hold. (The multivalued dependency _ID_ →→ _dept name_ will do as well. We shall soon see that they are equivalent.)

As with functional dependencies, we shall use multivalued dependencies in two ways:

**1\.** To test relations to determine whether they are legal under a given set of functional and multivalued dependencies

_ID dept name street city_

22222 Physics North Rye 22222 Physics Main Manchester 12121 Finance Lake Horseneck

**Figure 8.14** An example of redundancy in a relation on a BCNF schema.  

**358 Chapter 8 Relational Database Design**

_ID dept name street city_

22222 Physics North Rye 22222 Math Main Manchester

**Figure 8.15** An illegal _r_2 relation.

**2\.** To specify constraints on the set of legal relations; we shall thus concern ourselves with _only_ those relations that satisfy a given set of functional and multivalued dependencies

Note that, if a relation _r_ fails to satisfy a given multivalued dependency, we can construct a relation _r_ ′ that _does_ satisfy the multivalued dependency by adding tuples to _r_.

Let _D_ denote a set of functional and multivalued dependencies. The **closure** _D_\+ of _D_ is the set of all functional and multivalued dependencies logically im- plied by _D_. As we did for functional dependencies, we can compute _D_\+ from _D_, using the formal definitions of functional dependencies and multivalued de- pendencies. We can manage with such reasoning for very simple multivalued dependencies. Luckily, multivalued dependencies that occur in practice appear to be quite simple. For complex dependencies, it is better to reason about sets of dependencies by using a system of inference rules.

From the definition of multivalued dependency, we can derive the following rules for _,_  ⊆ _R_:

• If  → , then  →→ . In other words, every functional dependency is also a multivalued dependency.

• If  →→ , then  →→ _R_ −  − 

Appendix C.1.1 outlines a system of inference rules for multivalued dependen- cies.

**8.6.2 Fourth Normal Form**

Consider again our example of the BCNF schema:

_r_2 (_ID_, _dept name_, _street_, _city_)

in which the multivalued dependency “_ID_ →→ _street_, _city_” holds. We saw in the opening paragraphs of Section 8.6 that, although this schema is in BCNF, the design is not ideal, since we must repeat an instructor’s address information for each department. We shall see that we can use the given multivalued dependency to improve the database design, by decomposing this schema into a **fourth normal form** decomposition.

A relation schema _r_ (_R_) is in **fourth normal form** (4NF) with respect to a set _D_ of functional and multivalued dependencies if, for all multivalued dependencies  

**8.6 Decomposition Using Multivalued Dependencies 359**

in _D_\+ of the form  →→ , where  ⊆ _R_ and  ⊆ _R_, at least one of the following holds:

•  →→  is a trivial multivalued dependency.

•  is a superkey for _R_.

A database design is in 4NF if each member of the set of relation schemas that constitutes the design is in 4NF.

Note that the definition of 4NF differs from the definition of BCNF in only the use of multivalued dependencies. Every 4NF schema is in BCNF. To see this fact, we note that, if a schema _r_ (_R_) is not in BCNF, then there is a nontrivial functional dependency  →  holding on _R_, where  is not a superkey. Since  →  implies  →→ , _r_ (_R_) cannot be in 4NF.

Let _r_ (_R_) be a relation schema, and let _r_1(_R_1)_, r_2(_R_2)_, . . . , rn_(_Rn_) be a decompo- sition of _r_ (_R_). To check if each relation schema _ri_ in the decomposition is in 4NF, we need to find what multivalued dependencies hold on each _ri_ . Recall that, for a set _F_ of functional dependencies, the restriction _Fi_ of _F_ to _Ri_ is all functional dependencies in _F_ \+ that include _only_ attributes of _Ri_ . Now consider a set _D_ of both functional and multivalued dependencies. The **restriction** of _D_ to _Ri_ is the set _Di_ consisting of:

**1\.** All functional dependencies in _D_\+ that include only attributes of _Ri_ .

**2\.** All multivalued dependencies of the form:

 →→  ∩ _Ri_

where  ⊆ _Ri_ and  →→  is in _D_+.

**8.6.3 4NF Decomposition**

The analogy between 4NF and BCNF applies to the algorithm for decomposing a schema into 4NF. Figure 8.16 shows the 4NF decomposition algorithm. It is identical to the BCNF decomposition algorithm of Figure 8.11, except that it uses multivalued dependencies and uses the restriction of _D_\+ to _Ri_ .

If we apply the algorithm of Figure 8.16 to (_ID_, _dept name_, _street_, _city_), we find that _ID_→→ _dept name_ is a nontrivial multivalued dependency, and _ID_ is not a superkey for the schema. Following the algorithm, we replace it by two schemas:

_r_21 (_ID_, _dept name_) _r_22 (_ID_, _street_, _city_)

This pair of schemas, which is in 4NF, eliminates the redundancy we encountered earlier.

As was the case when we were dealing solely with functional dependencies, we are interested in decompositions that are lossless and that preserve dependen-  

**360 Chapter 8 Relational Database Design**

_result_ := _{R}_; _done_ := false; _compute D_+; Given schema _Ri_ , let _Di_ denote the restriction of _D_\+ to _Ri_ **while** (**not** _done_) **do**

**if** (there is a schema _Ri_ in _result_ that is not in 4NF w.r.t. _Di_ ) **then begin**

let  →→  be a nontrivial multivalued dependency that holds on _Ri_ such that  → _Ri_ is not in _Di_ , and  ∩  = ∅; _result_ := (_result_ − _Ri_ ) ∪ (_Ri_ − ) ∪ (_,_ );

**end else** _done_ := true;

**Figure 8.16** 4NF decomposition algorithm.

cies. The following fact about multivalued dependencies and losslessness shows that the algorithm of Figure 8.16 generates only lossless decompositions:

• Let _r_ (_R_) be a relation schema, and let _D_ be a set of functional and multivalued dependencies on _R_. Let _r_1(_R_1) and _r_2(_R_2) form a decomposition of _R_. This decomposition is lossless of _R_ if and only if at least one of the following multivalued dependencies is in _D_+:

_R_1 ∩ _R_2 →→ _R_1 _R_1 ∩ _R_2 →→ _R_2

Recall that we stated in Section 8.4.4 that, if _R_1 ∩ _R_2 → _R_1 or _R_1 ∩ _R_2 → _R_2, then _r_1(_R_1) and _r_2(_R_2) are a lossless decomposition _r_ (_R_) . The preceding fact about multivalued dependencies is a more general statement about losslessness. It says that, for _every_ lossless decomposition of _r_ (_R_) into two schemas _r_1(_R_1) and _r_2(_R_2), one of the two dependencies _R_1 ∩ _R_2 →→ _R_1 or _R_1 ∩ _R_2 →→ _R_2 must hold.

The issue of dependency preservation when we decompose a relation schema becomes more complicated in the presence of multivalued dependencies. Ap- pendix C.1.2 pursues this topic.

**8.7 More Normal Forms**

The fourth normal form is by no means the “ultimate” normal form. As we saw earlier, multivalued dependencies help us understand and eliminate some forms of repetition of information that cannot be understood in terms of func- tional dependencies. There are types of constraints called **join dependencies** that generalize multivalued dependencies, and lead to another normal form called **project-join normal form (PJNF)** (PJNF is called **fifth normal form** in some books). There is a class of even more general constraints that leads to a normal form called **domain-key normal form (DKNF)**.  

**8.8 Database-Design Process 361**

A practical problem with the use of these generalized constraints is that they are not only hard to reason with, but there is also no set of sound and complete inference rules for reasoning about the constraints. Hence PJNF and DKNF are used quite rarely. Appendix C provides more details about these normal forms.

Conspicuous by its absence from our discussion of normal forms is **second normal form** (2NF). We have not discussed it, because it is of historical interest only. We simply define it, and let you experiment with it in Practice Exercise 8.17.

**8.8 Database-Design Process**

So far we have looked at detailed issues about normal forms and normalization. In this section, we study how normalization fits into the overall database-design process.

Earlier in the chapter, starting in Section 8.3, we assumed that a relation schema _r_ (_R_) is given, and proceeded to normalize it. There are several ways in which we could have come up with the schema _r_ (_R_):

**1\.** _r_ (_R_) could have been generated in converting an E-R diagram to a set of relation schemas.

**2\.** _r_ (_R_) could have been a single relation schema containing _all_ attributes that are of interest. The normalization process then breaks up _r_ (_R_) into smaller schemas.

**3\.** _r_ (_R_) could have been the result of an ad-hoc design of relations that we then test to verify that it satisfies a desired normal form.

In the rest of this section, we examine the implications of these approaches. We also examine some practical issues in database design, including denormalization for performance and examples of bad design that are not detected by normalization.

**8.8.1 E-R Model and Normalization**

When we define an E-R diagram carefully, identifying all entities correctly, the relation schemas generated from the E-R diagram should not need much further normalization. However, there can be functional dependencies between attributes of an entity. For instance, suppose an _instructor_ entity set had attributes _dept name_ and _dept address_, and there is a functional dependency _dept name_ → _dept address_. We would then need to normalize the relation generated from _instructor_.

Most examples of such dependencies arise out of poor E-R diagram design. In the above example, if we had designed the E-R diagram correctly, we would have created a _department_ entity set with attribute _dept address_ and a relationship set between _instructor_ and _department_. Similarly, a relationship set involving more than two entity sets may result in a schema that may not be in a desirable normal form. Since most relationship sets are binary, such cases are relatively rare. (In  

**362 Chapter 8 Relational Database Design**

fact, some E-R-diagram variants actually make it difficult or impossible to specify nonbinary relationship sets.)

Functional dependencies can help us detect poor E-R design. If the generated relation schemas are not in desired normal form, the problem can be fixed in the E- R diagram. That is, normalization can be done formally as part of data modeling. Alternatively, normalization can be left to the designer’s intuition during E-R modeling, and can be done formally on the relation schemas generated from the E-R model.

A careful reader will have noted that in order for us to illustrate a need for multivalued dependencies and fourth normal form, we had to begin with schemas that were not derived from our E-R design. Indeed, the process of creating an E-R design tends to generate 4NF designs. If a multivalued dependency holds and is not implied by the corresponding functional dependency, it usually arises from one of the following sources:

• A many-to-many relationship set.

• A multivalued attribute of an entity set.

For a many-to-many relationship set each related entity set has its own schema and there is an additional schema for the relationship set. For a multivalued attribute, a separate schema is created consisting of that attribute and the primary key of the entity set (as in the case of the _phone number_ attribute of the entity set _instructor_).

The universal-relation approach to relational database design starts with an assumption that there is one single relation schema containing all attributes of interest. This single schema defines how users and applications interact with the database.

**8.8.2 Naming of Attributes and Relationships**

A desirable feature of a database design is the **unique-role assumption**, which means that each attribute name has a unique meaning in the database. This prevents us from using the same attribute to mean different things in different schemas. For example, we might otherwise consider using the attribute _number_ for phone number in the _instructor_ schema and for room number in the _classroom_ schema. The join of a relation on schema _instructor_ with one on _classroom_ is meaningless. While users and application developers can work carefully to ensure use of the right _number_ in each circumstance, having a different attribute name for phone number and for room number serves to reduce user errors.

While it is a good idea to keep names for incompatible attributes distinct, if attributes of different relations have the same meaning, it may be a good idea to use the same attribute name. For this reason we used the same attribute name “_name_” for both the _instructor_ and the _student_ entity sets. If this was not the case (that is, we used different naming conventions for the instructor and student names), then if we wished to generalize these entity sets by creating a _person_ entity set, we would have to rename the attribute. Thus, even if we did not currently  

**8.8 Database-Design Process 363**

have a generalization of _student_ and _instructor_, if we foresee such a possibility it is best to use the same name in both entity sets (and relations).

Although technically, the order of attribute names in a schema does not matter, it is convention to list primary-key attributes first. This makes reading default output (as from **select \***) easier.

In large database schemas, relationship sets (and schemas derived therefrom) are often named via a concatenation of the names of related entity sets, perhaps with an intervening hyphen or underscore. We have used a few such names, for example _inst sec_ and _student sec_. We used the names _teaches_ and _takes_ instead of using the longer concatenated names. This was acceptable since it is not hard for you to remember the associated entity sets for a few relationship sets. We cannot always create relationship-set names by simple concatenation; for example, a manager or works-for relationship between employees would not make much sense if it were called _employee employee_! Similarly, if there are multiple relationship sets possible between a pair of entity sets, the relationship-set names must include extra parts to identify the relationship set.

Different organizations have different conventions for naming entity sets. For example, we may call an entity set of students _student_ or _students_. We have chosen to use the singular form in our database designs. Using either singular or plural is acceptable, as long as the convention is used consistently across all entity sets.

As schemas grow larger, with increasing numbers of relationship sets, using consistent naming of attributes, relationships, and entities makes life much easier for the database designer and application programmers.

**8.8.3 Denormalization for Performance**

Occasionally database designers choose a schema that has redundant information; that is, it is not normalized. They use the redundancy to improve performance for specific applications. The penalty paid for not using a normalized schema is the extra work (in terms of coding time and execution time) to keep redundant data consistent.

For instance, suppose all course prerequisites have to be displayed along with a course information, every time a course is accessed. In our normalized schema, this requires a join of _course_ with _prereq_.

One alternative to computing the join on the fly is to store a relation containing all the attributes of _course_ and _prereq_. This makes displaying the “full” course information faster. However, the information for a course is repeated for every course prerequisite, and all copies must be updated by the application, whenever a course prerequisite is added or dropped. The process of taking a normalized schema and making it nonnormalized is called **denormalization**, and designers use it to tune performance of systems to support time-critical operations.

A better alternative, supported by many database systems today, is to use the normalized schema, and additionally store the join of _course_ and _prereq_ as a materialized view. (Recall that a materialized view is a view whose result is stored in the database and brought up to date when the relations used in the view are updated.) Like denormalization, using materialized views does have space and  

**364 Chapter 8 Relational Database Design**

time overhead; however, it has the advantage that keeping the view up to date is the job of the database system, not the application programmer.

**8.8.4 Other Design Issues**

There are some aspects of database design that are not addressed by normal- ization, and can thus lead to bad database design. Data pertaining to time or to ranges of time have several such issues. We give examples here; obviously, such designs should be avoided.

Consider a university database, where we want to store the total number of instructors in each department in different years. A relation _total inst_(_dept name_, _year_, _size_) could be used to store the desired information. The only functional dependency on this relation is _dept name_, _year_→ _size_, and the relation is in BCNF.

An alternative design is to use multiple relations, each storing the size infor- mation for a different year. Let us say the years of interest are 2007, 2008, and 2009; we would then have relations of the form _total inst 2007_, _total inst 2008_, _total inst 2009_, all of which are on the schema (_dept name_, _size_). The only functional

dependency here on each relation would be _dept name_ → _size_, so these relations are also in BCNF.

However, this alternative design is clearly a bad idea—we would have to create a new relation every year, and we would also have to write new queries every year, to take each new relation into account. Queries would also be more complicated since they may have to refer to many relations.

Yet another way of representing the same data is to have a single relation _dept year_(_dept name_, _total inst 2007_, _total inst 2008_, _total inst 2009_). Here the only functional dependencies are from _dept name_ to the other attributes, and again the relation is in BCNF. This design is also a bad idea since it has problems similar to the previous design—namely we would have to modify the relation schema and write new queries every year. Queries would also be more complicated, since they may have to refer to many attributes.

Representations such as those in the _dept year_ relation, with one column for each value of an attribute, are called **crosstabs**; they are widely used in spread- sheets and reports and in data analysis tools. While such representations are useful for display to users, for the reasons just given, they are not desirable in a database design. SQL includes features to convert data from a normal relational representation to a crosstab, for display, as we discussed in Section 5.6.1.

**8.9 Modeling Temporal Data**

Suppose we retain data in our university organization showing not only the address of each instructor, but also all former addresses of which the university is aware. We may then ask queries such as “Find all instructors who lived in Princeton in 1981.” In this case, we may have multiple addresses for instructors. Each address has an associated start and end date, indicating when the instructor was resident at that address. A special value for the end date, e.g., null, or a value  

**8.9 Modeling Temporal Data 365**

well into the future such as 9999-12-31, can be used to indicate that the instructor is still resident at that address.

In general, **temporal data** are data that have an associated time interval during which they are **valid**.8 We use the term **snapshot** of data to mean the value of the data at a particular point in time. Thus a snapshot of _course_ data gives the values of all attributes, such as title and department, of all courses at a particular point in time.

Modeling temporal data is a challenging problem for several reasons. For ex- ample, suppose we have an _instructor_ entity set with which we wish to associate a time-varying address. To add temporal information to an address, we would then have to create a multivalued attribute, each of whose values is a composite value containing an address and a time interval. In addition to time-varying attribute values, entities may themselves have an associated valid time. For example, a student entity may have a valid time from the date the student entered the uni- versity to the date the student graduated (or left the university). Relationships too may have associated valid times. For example, the _prereq_ relationship may record when a course became a prerequisite for another course. We would thus have to add valid time intervals to attribute values, entity sets, and relationship sets. Adding such detail to an E-R diagram makes it very difficult to create and to comprehend. There have been several proposals to extend the E-R notation to specify in a simple manner that an attribute value or relationship is time varying, but there are no accepted standards.

When we track data values across time, functional dependencies that we assumed to hold, such as:

_ID_ → _street_, _city_

may no longer hold. The following constraint (expressed in English) would hold instead: “An instructor _ID_ has only one _street_ and _city_ value for any given time _t_.”

Functional dependencies that hold at a particular point in time are called temporal functional dependencies. Formally, a **temporal functional dependency** _X_

→ _Y_ holds on a relation schema _r_ (_R_) if, for all legal instances of _r_ (_R_), all snapshots of _r_ satisfy the functional dependency _X_ → _Y_.

We could extend the theory of relational database design to take temporal functional dependencies into account. However, reasoning with regular func- tional dependencies is difficult enough already, and few designers are prepared to deal with temporal functional dependencies.

In practice, database designers fall back to simpler approaches to design- ing temporal databases. One commonly used approach is to design the entire database (including E-R design and relational design) ignoring temporal changes (equivalently, taking only a snapshot into consideration). After this, the designer

8There are other models of temporal data that distinguish between **valid time** and **transaction time**, the latter recording when a fact was recorded in the database. We ignore such details for simplicity.  

**366 Chapter 8 Relational Database Design**

studies the various relations and decides which relations require temporal varia- tion to be tracked.

The next step is to add valid time information to each such relation, by adding start and end time as attributes. For example, consider the _course_ relation. The title of the course may change over time, which can be handled by adding a valid time range; the resultant schema would be

_course_ (_course id_, _title_, _dept name_, _start, end_)

An instance of this relation might have two records (CS-101, “Introduction to Pro- gramming”, 1985-01-01, 2000-12-31) and (CS-101, “Introduction to C”, 2001-01-01, 9999-12-31). If the relation is updated by changing the course title to “Introduction to Java,” the time “9999-12-31” would be updated to the time until which the old value (“Introduction to C”) is valid, and a new tuple would be added containing the new title (“Introduction to Java”), with an appropriate start time.

If another relation had a foreign key referencing a temporal relation, the database designer has to decide if the reference is to the current version of the data or to the data as of a particular point in time. For example, we may extend the _department_ relation to track changes in the building or budget of a department across time, but a reference from the _instructor_ or _student_ relation may not care about the history of the building or budget, but may instead implicitly refer to the temporally current record for the corresponding _dept name_. On the other hand, a record in a student’s transcript should refer to the course title at the time when the student took the course. In this latter case, the referencing relation must also record time information, to identify a particular record from the _course_ relation. In our example, the _year_ and _semester_ when the course was taken can be mapped to a representative time/date value such as midnight of the start date of the semester; the resulting time/date value is used to identify a particular record from the temporal version of the _course_ relation, from which the _title_ is retrieved.

The original primary key for a temporal relation would no longer uniquely identify a tuple. To resolve this problem, we could add the start and end time attributes to the primary key. However, some problems remain:

• It is possible to store data with overlapping intervals, which the primary-key constraint would not catch. If the system supports a native _valid time_ type, it can detect and prevent such overlapping time intervals.

• To specify a foreign key referencing such a relation, the referencing tuples would have to include the start- and end-time attributes as part of their foreign key, and the values must match that in the referenced tuple. Further, if the referenced tuple is updated (and the end time which was in the future is updated), the update must propagate to all the referencing tuples.

If the system supports temporal data in a better fashion, we can allow the referencing tuple to specify a point in time, rather than a range, and rely on the system to ensure that there is a tuple in the referenced relation whose valid time interval contains the point in time. For example, a transcript record  

**8.10 Summary 367**

may specify a _course id_ and a time (say the start date of a semester), which is enough to identify the correct record in the _course_ relation.

As a common special case, if all references to temporal data refer to only the current data, a simpler solution is to not add time information to the relation, but instead create a corresponding _history_ relation that has temporal information, for past values. For example, in our bank database, we could use the design we have created, ignoring temporal changes, to store only the current information. All historical information is moved to historical relations. Thus, the _instructor_ relation may store only the current address, while a relation _instructor history_ may contain all the attributes of _instructor_, with additional _start time_ and _end time_ attributes.

Although we have not provided any formal way to deal with temporal data, the issues we have discussed and the examples we have provided should help you in designing a database that records temporal data. Further issues in handling temporal data, including temporal queries, are covered later, in Section 25.2.

**8.10 Summary**

• We showed pitfalls in database design, and how to systematically design a database schema that avoids the pitfalls. The pitfalls included repeated information and inability to represent some information.

• We showed the development of a relational database design from an E-R design, when schemas may be combined safely, and when a schema should be decomposed. All valid decompositions must be lossless.

• We described the assumptions of atomic domains and first normal form.

• We introduced the concept of functional dependencies, and used it to present two normal forms, Boyce–Codd normal form (BCNF) and third normal form (3NF).

• If the decomposition is dependency preserving, given a database update, all functional dependencies can be verifiable from individual relations, without computing a join of relations in the decomposition.

• We showed how to reason with functional dependencies. We placed special emphasis on what dependencies are logically implied by a set of dependen- cies. We also defined the notion of a canonical cover, which is a minimal set of functional dependencies equivalent to a given set of functional dependencies.

• We outlined an algorithm for decomposing relations into BCNF. There are relations for which there is no dependency-preserving BCNF decomposition.

• We used the canonical covers to decompose a relation into 3NF, which is a small relaxation of the BCNF condition. Relations in 3NF may have some redundancy, but there is always a dependency-preserving decomposition into 3NF.  

**368 Chapter 8 Relational Database Design**

• We presented the notion of multivalued dependencies, which specify con- straints that cannot be specified with functional dependencies alone. We de- fined fourth normal form (4NF) with multivalued dependencies. Appendix C.1.1 gives details on reasoning about multivalued dependencies.

• Other normal forms, such as PJNF and DKNF, eliminate more subtle forms of redundancy. However, these are hard to work with and are rarely used. Appendix C gives details on these normal forms.

• In reviewing the issues in this chapter, note that the reason we could define rigorous approaches to relational database design is that the relational data model rests on a firm mathematical foundation. That is one of the primary advantages of the relational model compared with the other data models that we have studied.

**Review Terms**

• E-R model and normalization • Decomposition • Functional dependencies • Lossless decomposition • Atomic domains • First normal form (1NF) • Legal relations • Superkey • _R_ satisfies _F_

• _F_ holds on _R_

• Boyce–Codd normal form (BCNF)

• Dependency preservation • Third normal form (3NF) • Trivial functional dependencies • Closure of a set of functional

dependencies

• Armstrong’s axioms • Closure of attribute sets • Restriction of _F_ to _Ri_

• Canonical cover • Extraneous attributes • BCNF decomposition algorithm • 3NF decomposition algorithm • Multivalued dependencies • Fourth normal form (4NF) • Restriction of a multivalued

dependency • Project-join normal form (PJNF) • Domain-key normal form (DKNF) • Universal relation • Unique-role assumption • Denormalization

**Practice Exercises**

**8.1** Suppose that we decompose the schema _r_ (_A_, _B_, _C_ , _D_, _E_) into

_r_1(_A_, _B_, _C_) _r_2(_A_, _D_, _E_)  

**Practice Exercises 369**

Show that this decomposition is a lossless decomposition if the following set _F_ of functional dependencies holds:

_A_ → _BC CD_ → _E B_ → _D E_ → _A_

**8.2** List all functional dependencies satisfied by the relation of Figure 8.17.

**8.3** Explain how functional dependencies can be used to indicate the following:

• A one-to-one relationship set exists between entity sets _student_ and _instructor_.

• A many-to-one relationship set exists between entity sets _student_ and _instructor_.

**8.4** Use Armstrong’s axioms to prove the soundness of the union rule. (_Hint_: Use the augmentation rule to show that, if  → , then  → . Apply the augmentation rule again, using  →  , and then apply the transitivity rule.)

**8.5** Use Armstrong’s axioms to prove the soundness of the pseudotransitivity rule.

**8.6** Compute the closure of the following set _F_ of functional dependencies for relation schema _r_ (_A, B, C, D, E_).

_A_ → _BC CD_ → _E B_ → _D E_ → _A_

List the candidate keys for _R_.

**8.7** Using the functional dependencies of Practice Exercise 8.6, compute the canonical cover _Fc_ .

_A B C_

_a_1 _b_1 _c_1 _a_1 _b_1 _c_2 _a_2 _b_1 _c_1 _a_2 _b_1 _c_3

**Figure 8.17** Relation of Practice Exercise 8.2.  

**370 Chapter 8 Relational Database Design**

**8.8** Consider the algorithm in Figure 8.18 to compute +. Show that this algo- rithm is more efficient than the one presented in Figure 8.8 (Section 8.4.2) and that it computes + correctly.

**8.9** Given the database schema _R_(_a , b, c_), and a relation _r_ on the schema _R_, write an SQL query to test whether the functional dependency _b_ → _c_ holds on relation _r_ . Also write an SQL assertion that enforces the functional de- pendency; assume that no null values are present. (Although part of the SQL standard, such assertions are not supported by any database imple- mentation currently.)

**8.10** Our discussion of lossless-join decomposition implicitly assumed that at- tributes on the left-hand side of a functional dependency cannot take on null values. What could go wrong on decomposition, if this property is violated?

**8.11** In the BCNF decomposition algorithm, suppose you use a functional de- pendency  →  to decompose a relation schema _r_ (_,_ _,_  ) into _r_1(_,_ ) and _r_2(_,_  ).

a. What primary and foreign-key constraint do you expect to hold on the decomposed relations?

b. Give an example of an inconsistency that can arise due to an erro- neous update, if the foreign-key constraint were not enforced on the decomposed relations above.

c. When a relation is decomposed into 3NF using the algorithm in Sec- tion 8.5.2, what primary and foreign key dependencies would you expect will hold on the decomposed schema?

**8.12** Let _R_1_, R_2_, . . . , Rn_ be a decomposition of schema _U_. Let _u_(_U_) be a relation, and let _ri_ \= _RI_ (_u_). Show that

_u_ ⊆ _r_1 _ r_2  · · · _ rn_

**8.13** Show that the decomposition in Practice Exercise 8.1 is not a dependency- preserving decomposition.

**8.14** Show that it is possible to ensure that a dependency-preserving decom- position into 3NF is a lossless decomposition by guaranteeing that at least one schema contains a candidate key for the schema being decomposed. (_Hint_: Show that the join of all the projections onto the schemas of the decomposition cannot have more tuples than the original relation.)

**8.15** Give an example of a relation schema _R_′ and set _F_ ′ of functional depen- dencies such that there are at least three distinct lossless decompositions of _R_′ into BCNF.  

**Practice Exercises 371**

_result_ := ∅; /\* _fdcount_ is an array whose _i_th element contains the number

of attributes on the left side of the _i_th _FD_ that are not yet known to be in + \*/

**for** _i_ := 1 to |_F_ | **do begin**

let  →  denote the _i_th _FD_; _fdcount_ [_i_] := ||;

**end** /\* _appears_ is an array with one entry for each attribute. The

entry for attribute _A_ is a list of integers. Each integer _i_ on the list indicates that _A_ appears on the left side of the _i_th _FD_ \*/

**for each** attribute _A_ **do begin**

_appears_ [_A_] := _NI L_ ; **for** _i_ := 1 to |_F_ | **do**

**begin** let  →  denote the _i_th _FD_; **if** _A_ ∈  **then** add _i_ to _appears_ [_A_];

**end end**

**addin** (); **return** (_result_);

**procedure addin** (); **for each** attribute _A_ in  **do**

**begin if** _A_ ∈ _result_ **then**

**begin** _result_ := _result_ ∪ {_A_}; **for each** element _i_ of _appears_[_A_] **do**

**begin** _fdcount_ [_i_] := _fdcount_ [_i_] − 1; **if** _fdcount_ [_i_] := 0 **then**

**begin** let  →  denote the _i_th _FD_; **addin** ( );

**end end**

**end end**

**Figure 8.18** An algorithm to compute +.  

**372 Chapter 8 Relational Database Design**

**8.16** Let a **prime** attribute be one that appears in at least one candidate key. Let  and  be sets of attributes such that  →  holds, but  →  does not hold. Let _A_ be an attribute that is not in , is not in , and for which  → _A_ holds. We say that _A_ is **transitively dependent** on . We can restate our definition of 3NF as follows: _A relation schema R is in 3NF with respect to a set F of functional dependencies if there are no nonprime attributes A in R for which A is transitively dependent on a key for R_. Show that this new definition is equivalent to the original one.

**8.17** A functional dependency  →  is called a **partial dependency** if there is a proper subset  of  such that  → . We say that  is _partially dependent_ on . A relation schema _R_ is in **second normal form** (2NF) if each attribute _A_ in _R_ meets one of the following criteria:

• It appears in a candidate key.

• It is not partially dependent on a candidate key.

Show that every 3NF schema is in 2NF. (_Hint_: Show that every partial dependency is a transitive dependency.)

**8.18** Give an example of a relation schema _R_ and a set of dependencies such that _R_ is in BCNF but is not in 4NF.

**Exercises**

**8.19** Give a lossless-join decomposition into BCNF of schema _R_ of Practice Exer- cise 8.1.

**8.20** Give a lossless-join, dependency-preserving decomposition into 3NF of schema _R_ of Practice Exercise 8.1.

**8.21** Normalize the following schema, with given constraints, to 4NF.

_books_(_accessionno_, _isbn_, _title_, _author_, _publisher_) _users_(_userid_, _name_, _deptid_, _deptname_) _accessionno_ → _isbn isbn_ → _title isbn_ → _publisher isbn_ →→ _author userid_ → _name userid_ → _deptid deptid_ → _deptname_

**8.22** Explain what is meant by _repetition of information_ and _inability to represent information_. Explain why each of these properties may indicate a bad rela- tional database design.  

**Exercises 373**

**8.23** Why are certain functional dependencies called _trivial_ functional depen- dencies?

**8.24** Use the definition of functional dependency to argue that each of Arm- strong’s axioms (reflexivity, augmentation, and transitivity) is sound.

**8.25** Consider the following proposed rule for functional dependencies: If  →  and  → , then  →  . Prove that this rule is _not_ sound by showing a relation _r_ that satisfies  →  and  → , but does not satisfy  →  .

**8.26** Use Armstrong’s axioms to prove the soundness of the decomposition rule.

**8.27** Using the functional dependencies of Practice Exercise 8.6, compute _B_+.

**8.28** Show that the following decomposition of the schema _R_ of Practice Exer- cise 8.1 is not a lossless decomposition:

(_A_, _B_, _C_) (_C_ , _D_, _E_)

_Hint_: Give an example of a relation _r_ on schema _R_ such that

_A, B, C_ (_r_ ) _C, D, E_ (_r_ ) = _r_

**8.29** Consider the following set _F_ of functional dependencies on the relation schema _r_ (_A, B, C, D, E, F_ ):

_A_ → _BCD BC_ → _DE B_ → _D D_ → _A_

a. Compute _B_+.

b. Prove (using Armstrong’s axioms) that _AF_ is a superkey.

c. Compute a canonical cover for the above set of functional dependen- cies _F_ ; give each step of your derivation with an explanation.

d. Give a 3NF decomposition of _r_ based on the canonical cover.

e. Give a BCNF decomposition of _r_ using the original set of functional dependencies.

f. Can you get the same BCNF decomposition of _r_ as above, using the canonical cover?

**8.30** List the three design goals for relational databases, and explain why each is desirable.  

**374 Chapter 8 Relational Database Design**

**8.31** In designing a relational database, why might we choose a non-BCNF de- sign?

**8.32** Given the three goals of relational database design, is there any reason to design a database schema that is in 2NF, but is in no higher-order normal form? (See Practice Exercise 8.17 for the definition of 2NF.)

**8.33** Given a relational schema _r_ (_A, B, C, D_), does _A_ →→ _BC_ logically imply _A_ →→ _B_ and _A_ →→ _C_? If yes prove it, else give a counter example.

**8.34** Explain why 4NF is a normal form more desirable than BCNF.

**Bibliographical Notes**

The first discussion of relational database design theory appeared in an early pa- per by Codd [1970]. In that paper, Codd also introduced functional dependencies and first, second, and third normal forms.

Armstrong’s axioms were introduced in Armstrong [1974]. Significant devel- opment of relational database theory occurred in the late 1970s. These results are collected in several texts on database theory including Maier [1983], Atzeni and Antonellis [1993], and Abiteboul et al. [1995].

BCNF was introduced in Codd [1972]. Biskup et al. [1979] give the algorithm we used to find a lossless dependency-preserving decomposition into 3NF. Fun- damental results on the lossless decomposition property appear in Aho et al. [1979a].

Beeri et al. [1977] gives a set of axioms for multivalued dependencies, and proves that the authors’ axioms are sound and complete. The notions of 4NF, PJNF, and DKNF are from Fagin [1977], Fagin [1979], and Fagin [1981], respectively. See the bibliographical notes of Appendix C for further references to literature on normalization.

Jensen et al. [1994] presents a glossary of temporal-database concepts. A survey of extensions to E-R modeling to handle temporal data is presented by Gregersen and Jensen [1999]. Tansel et al. [1993] covers temporal database theory, design, and implementation. Jensen et al. [1996] describes extensions of depen- dency theory to temporal data.  

**_C H A P T E R_9 Application Design and Development**

Practically all use of databases occurs from within application programs. Corre- spondingly, almost all user interaction with databases is indirect, via application programs. Not surprisingly, therefore, database systems have long supported tools such as form and GUI builders, which help in rapid development of appli- cations that interface with users. In recent years, the Web has become the most widely used user interface to databases.

In this chapter, we study tools and technologies that are used to build appli- cations, focussing on interactive applications that use databases to store data.

After an introduction to application programs and user interfaces in Sec- tion 9.1, we focus on developing applications with Web-based interfaces. We start with an overview of Web technologies in Section 9.2, and discuss the Java Servlets technology, which is widely used for building Web applications, in Section 9.3. A short overview of Web application architectures in presented Section 9.4. In Sec- tion 9.5, we discuss tools for rapid application development, while in Section 9.6 we cover performance issues in building large Web applications. In Section 9.7, we discuss issues in application security. We conclude the chapter with Section 9.8, which covers encryption and its use in applications.

**9.1 Application Programs and User Interfaces**

Although many people interact with databases, very few people use a query language to interact with a database system directly. The most common way in which users interact with databases is through an application program that provides a user interface at the front end, and interfaces with a database at the back end. Such applications take input from users, typically through a forms- based interface, and either enter data into a database or extract information from a database based on the user input, and generate output, which is displayed to the user.

As an example of an application, consider a university registration system. Like other such applications, the registration system first requires you to identify

**375**  

**376 Chapter 9 Application Design and Development**

(a) Mainframe Era (b) Personal Computer Era (c) Web era

Web Application Server

Database

Internet

Web browsersTerminals

Mainframe Computer

Propietary Network or dial up phone lines Local Area Network

Desktop PCs

Database

Application Program

Application Program

**Figure 9.1** Application architectures in different eras

and authenticate yourself, typically by a user name and password. The applica- tion then uses your identity to extract information, such as your name and the courses for which you have registered, from the database and displays the infor- mation. The application provides a number of interfaces that let you register for courses and query a variety of other information such as course and instructor information. Organizations use such applications to automate a variety of tasks, such as sales, purchases, accounting and payroll, human-resources management, and inventory management, among many others.

Application programs may be used even when it is not apparent that they are being used. For example, a news site may provide a page that is transparently customized to individual users, even if the user does not explicitly fill any forms when interacting with the site. To do so, it actually runs an application program that generates a customized page for each user; customization can, for example, be based on the history of articles browsed by the user.

A typical application program includes a front-end component, which deals with the user interface, a back-end component, which communicates with a database, and a middle layer, which contains “business logic,” that is, code that executes specific requests for information or updates, enforcing rules of business such as what actions should be carried out to execute a given task, or who can carry out what task.

Application architectures have evolved over time, as illustrated in Figure 9.1. Applications such as airline reservations have been around since the 1960s. In the early days of computer applications, applications ran on a large “mainframe” computer, and users interacted with the application through terminals, some of which even supported forms.

With the widespread use of personal computers, many organizations used a different architecture for internal applications, with applications running on the user’s computer, and accessing a central database. This architecture, often called a “client–server” architecture, allowed the creation of powerful graphical user interfaces, which earlier terminal-based applications did not support. However, software had to be installed on each user’s machine to run an application, making tasks such as upgrades harder. Even in the personal computer era, when client– server architectures became popular, mainframe architecture continued to be the  

**9.2 Web Fundamentals 377**

choice for applications such as airline reservations, which are used from a large number of geographically distributed locations.

In the past 15 years, Web browsers have become the _universal front end_ to database applications, connecting to the back end through the Internet. Browsers use a standardized syntax, the **HyperText Markup Language (HTML)** standard, which supports both formatted display of information, and creation of forms- based interfaces. The HTML standard is independent of the operating system or browser, and pretty much every computer today has a Web browser installed. Thus a Web-based application can be accessed from any computer that is con- nected to the Internet.

Unlike client–server architectures, there is no need to install any application- specific software on client machines in order to use Web-based applications. However, sophisticated user interfaces, supporting features well beyond what is possible using plain HTML, are now widely used, and are built with the script- ing language JavaScript, which is supported by most Web browsers. JavaScript programs, unlike programs written in C, can be run in a safe mode, guarantee- ing they cannot cause security problems. JavaScript programs are downloaded transparently to the browser and do not need any explicit software installation on the user’s computer.

While the Web browser provides the front end for user interaction, application programs constitute the back end. Typically, requests from a browser are sent to a Web server, which in turn executes an application program to process the request. A variety of technologies are available for creating application programs that run at the back end, including Java servlets, Java Server Pages (JSP), Active Server Page (ASP), or scripting languages such as PHP, Perl, or Python.

In the rest of this chapter, we describe how to build such applications, starting with Web technologies and tools for building Web interfaces, and technologies for building application programs, and then covering application architectures, and performance and security issues in building applications.

**9.2 Web Fundamentals**

In this section, we review some of the fundamental technology behind the World Wide Web, for readers who are not familiar with the technology underlying the Web.

**9.2.1 Uniform Resource Locators**

A **uniform resource locator** (**URL**) is a globally unique name for each document that can be accessed on the Web. An example of a URL is:

http://www.acm.org/sigmod

The first part of the URL indicates how the document is to be accessed: “http” indicates that the document is to be accessed by the **HyperText Transfer Protocol**  

**378 Chapter 9 Application Design and Development**

_<_html_\> <_body_\>_

_<_table border_\> <_tr_\> <_th_\>_ID_<_/th_\> <_th_\>_Name_<_/th_\> <_th_\>_Department_<_/th_\> <_/tr_\> <_tr_\> <_td_\>_00128_<_/td_\> <_td_\>_Zhang_<_/td_\> <_td_\>_Comp. Sci._<_/td_\> <_/tr_\> <_tr_\> <_td_\>_12345_<_/td_\> <_td_\>_Shankar_<_/td_\> <_td_\>_Comp. Sci._<_/td_\> <_/tr_\> <_tr_\> <_td_\>_19991_<_/td_\> <_td_\>_Brandt_<_/td_\> <_td_\>_History_<_/td_\> <_/tr_\> <_/table_\>_

_<_/body_\>_

_<_/html_\>_

**Figure 9.2** Tabular data in HTML format.

(HTTP), which is a protocol for transferring HTML documents. The second part gives the name of a machine that has a Web server. The rest of the URL is the path name of the file on the machine, or other unique identifier of a document within the machine.

A URL can contain the identifier of a program located on the Web server machine, as well as arguments to be given to the program. An example of such a URL is

http://www.google.com/search?q=silberschatz

which says that the program search on the server www.google.com should be executed with the argument q=silberschatz. On receiving a request for such a URL, the Web server executes the program, using the given arguments. The program returns an HTML document to the Web server, which sends it back to the front end.

**9.2.2 HyperText Markup Language**

Figure 9.2 is an example of a table represented in the HTMLformat, while Figure 9.3 shows the displayed image generated by a browser from the HTML representation of the table. The HTML source shows a few of the HTML tags. Every HTML page should be enclosed in an html tag, while the body of the page is enclosed in a body tag. A table is specified by a table tag, which contains rows specified by a tr tag. The header row of the table has table cells specified by a th tag, while regular

**ID** 00128 12345 19991

Zhang Shankar Brandt

**Name** Comp. Sci. Comp. Sci. History

**Department**

**Figure 9.3** Display of HTML source from Figure 9.2.  

**9.2 Web Fundamentals 379**

_<_html_\> <_body_\>_

_<_form action="PersonQuery" method=get_\>_ Search for: _<_select name="persontype"_\>_

_<_option value="student" selected_\>_Student _<_/option_\>_

_<_option value="instructor"_\>_ Instructor _<_/option_\>_

_<_/select_\> <_br_\>_ Name: _<_input type=text size=20 name="name"_\> <_input type=submit value="submit"_\> <_/form_\>_

_<_/body_\>_

_<_/html_\>_

**Figure 9.4** An HTML form.

rows have table cells specified by a td tag. We do not go into more details about the tags here; see the bibliographic notes for references containing more detailed descriptions of HTML.

Figure 9.4 shows how to specify an HTML form that allows users to select the person type (student or instructor) from a menu and to input a number in a text box. Figure 9.5 shows how the above form is displayed in a Web browser. Two methods of accepting input are illustrated in the form, but HTML also supports several other input methods. The action attribute of the form tag specifies that when the form is submitted (by clicking on the submit button), the form data should be sent to the URL PersonQuery (the URL is relative to that of the page). The Web server is configured such that when this URL is accessed, a corresponding application program is invoked, with the user-provided values for the arguments persontype and name (specified in the select and input fields). The application program generates an HTML document, which is then sent back and displayed to the user; we shall see how to construct such programs later in this chapter.

HTTP defines two ways in which values entered by a user at the browser can be sent to the Web server. The get method encodes the values as part of the URL. For example, if the Google search page used a form with an input parameter named q with the get method, and the user typed in the string “silberschatz” and submitted the form, the browser would request the following URL from the Web server:

http://www.google.com/search?q=silberschatz

Search for: Name:

Student

submit

**Figure 9.5** Display of HTML source from Figure 9.4.  

**380 Chapter 9 Application Design and Development**

The post method would instead send a request for the URL http://www.google.com, and send the parameter values as part of the HTTP protocol exchange between the Web server and the browser. The form in Figure 9.4 specifies that the form uses the get method.

Although HTML code can be created using a plain text editor, there are a number of editors that permit direct creation of HTML text by using a graphical interface. Such editors allow constructs such as forms, menus, and tables to be inserted into the HTML document from a menu of choices, instead of manually typing in the code to generate the constructs.

HTML supports _stylesheets_, which can alter the default definitions of how an HTML formatting construct is displayed, as well as other display attributes such as background color of the page. The _cascading stylesheet_ (CSS) standard allows the same stylesheet to be used for multiple HTML documents, giving a distinctive but uniform look to all the pages on a Web site.

**9.2.3 Web Servers and Sessions**

A **Web server** is a program running on the server machine, which accepts requests from a Web browser and sends back results in the form of HTML documents. The browser and Web server communicate via HTTP. Web servers provide powerful features, beyond the simple transfer of documents. The most important feature is the ability to execute programs, with arguments supplied by the user, and to deliver the results back as an HTML document.

As a result, a Web server can act as an intermediary to provide access to a variety of information services. A new service can be created by creating and installing an application program that provides the service. The **common gate- way interface (CGI)** standard defines how the Web server communicates with application programs. The application program typically communicates with a database server, through ODBC, JDBC, or other protocols, in order to get or store data.

HTTP

browser

server

web server

application server

database server

data

network

network

**Figure 9.6** Three-layer Web application architecture.  

**9.2 Web Fundamentals 381**

HTTP

browser

server

data

database server

web server and application server

network

**Figure 9.7** Two-layer Web application architecture.

Figure 9.6 shows a Web application built using a three-layer architecture, with a Web server, an application server, and a database server. Using multiple levels of servers increases system overhead; the CGI interface starts a new process to service each request, which results in even greater overhead.

Most Web applications today therefore use a two-layer Web application ar- chitecture, where the application program runs within the Web server, as in Figure 9.7. We study systems based on the two-layer architecture in more detail in subsequent sections.

There is no continuous connection between the client and the Web server; when a Web server receives a request, a connection is temporarily created to send the request and receive the response from the Web server. But the connection may then be closed, and the next request could come over a new connection. In contrast, when a user logs on to a computer, or connects to a database using ODBC or JDBC, a session is created, and session information is retained at the server and the client until the session is terminated—information such as the user-identifier of the user and session options that the user has set. One important reason that HTTP is **connectionless** is that most computers have limits on the number of simultaneous connections they can accommodate, and if a large number of sites on the Web open connections to a single server, this limit would be exceeded, denying service to further users. With a connectionless protocol, the connection can be broken as soon as a request is satisfied, leaving connections available for other requests.1

Most Web applications, however, need session information to allow mean- ingful user interaction. For instance, applications typically restrict access to in- formation, and therefore need to authenticate users. Authentication should be

1For performance reasons, connections may be kept open for a short while, to allow subsequent requests to reuse the connection. However, there is no guarantee that the connection will be kept open, and applications must be designed assuming the connection may be closed as soon as a request is serviced.  

**382 Chapter 9 Application Design and Development**

done once per session, and further interactions in the session should not require reauthentication.

To implement sessions in spite of connections getting closed, extra informa- tion has to be stored at the client and returned with each request in a session; the server uses this information to identify that a request is part of a user session. Extra information about the session also has to be maintained at the server.

This extra information is usually maintained in the form of a **cookie** at the client; a cookie is simply a small piece of text containing identifying information and with an associated name. For example, google.com may set a cookie with the name prefs, which encodes preferences set by the user such as the preferred language and the number of answers displayed per page. On each search request, google.com can retrieve the cookie named prefs from the user’s browser, and display results according to the specified preferences. A domain (Web site) is permitted to retrieve only cookies that it has set, not cookies set by other domains, and cookie names can be reused across domains.

For the purpose of tracking a user session, an application may generate a session identifier (usually a random number not currently in use as a session identifier), and send a cookie named (for instance) sessionid containing the session identifier. The session identifier is also stored locally at the server. When a request comes in, the application server requests the cookie named sessionid from the client. If the client does not have the cookie stored, or returns a value that is not currently recorded as a valid session identifier at the server, the application concludes that the request is not part of a current session. If the cookie value matches a stored session identifier, the request is identified as part of an ongoing session.

If an application needs to identify users securely, it can set the cookie only after authenticating the user; for example a user may be authenticated only when a valid user name and password are submitted.2

For applications that do not require high security, such as publicly available news sites, cookies can be stored permanently at the browser and at the server; they identify the user on subsequent visits to the same site, without any identifica- tion information being typed in. For applications that require higher security, the server may invalidate (drop) the session after a time-out period, or when the user logs out. (Typically a user logs out by clicking on a logout button, which submits a logout form, whose action is to invalidate the current session.) Invalidating a session merely consists of dropping the session identifier from the list of active sessions at the application server.

2The user identifier could be stored at the client end, in a cookie named, for example, userid. Such cookies can be used for low-security applications, such as free Web sites identifying their users. However, for applications that require a higher level of security, such a mechanism creates a security risk: The value of a cookie can be changed at the browser by a malicious user, who can then masquerade as a different user. Setting a cookie (named sessionid, for example) to a randomly generated session identifier (from a large space of numbers) makes it highly improbable that a user can masquerade as (that is, pretend to be) another user. A sequentially generated session identifier, on the other hand, would be susceptible to masquerading.  

**9.3 Servlets and JSP 383**

**9.3 Servlets and JSP**

In a two-layer Web architecture, an application runs as part of the Web server it- self. One way of implementing such an architecture is to load Java programs into the Web server. The Java **servlet** specification defines an application program- ming interface for communication between the Web server and the application program. The HttpServlet class in Java implements the servlet API specification; servlet classes used to implement specific functions are defined as subclasses of this class.3 Often the word _servlet_ is used to refer to a Java program (and class) that implements the servlet interface. Figure 9.8 shows a servlet example; we explain it in detail shortly.

The code for a servlet is loaded into the Web server when the server is started, or when the server receives a remote HTTP request to execute a particular servlet. The task of a servlet is to process such a request, which may involve accessing a database to retrieve necessary information, and dynamically generate an HTML page to be returned to the client browser.

**9.3.1 A Servlet Example**

Servlets are commonly used to generate dynamic responses to HTTP requests. They can access inputs provided through HTML forms, apply “business logic” to decide what response to provide, and then generate HTML output to be sent back to the browser.

Figure 9.8 shows an example of servlet code to implement the form in Fig- ure 9.4. The servlet is called PersonQueryServlet, while the form specifies that “action="PersonQuery".” The Web server must be told that this servlet is to be used to handle requests for PersonQuery. The form specifies that the HTTP get mechanism is used for transmitting parameters. So the doGet() method of the servlet, as defined in the code, is invoked.

Each request results in a new thread within which the call is executed, so multiple requests can be handled in parallel. Any values from the form menus and input fields on the Web page, as well as cookies, pass through an object of the HttpServletRequest class that is created for the request, and the reply to the request passes through an object of the class HttpServletResponse.

The doGet() method in the example extracts values of the parameter’s type and number by using request.getParameter(), and uses these values to run a query against a database. The code used to access the database and to get attribute values from the query result is not shown; refer to Section 5.1.1.4 for details of how to use JDBC to access a database. The servlet code returns the results of the query to the requester by outputting them to the HttpServletResponse object response. Outputting the results is to response is implemented by first getting a PrintWriter object out from response, and then printing the result in HTML format to out.

3The servlet interface can also support non-HTTP requests, although our example uses only HTTP.  

**384 Chapter 9 Application Design and Development**

import java.io.\*; import javax.servlet.\*; import javax.servlet.http.\*;

public class PersonQueryServlet extends HttpServlet _{_ public void doGet(HttpServletRequest request,

HttpServletResponse response) throws ServletException, IOException

_{_ response.setContentType("text/html"); PrintWriter out = response.getWriter(); out.println("_<_HEAD_\><_TITLE_\>_ Query Result_<_/TITLE_\><_/HEAD_\>_"); out.println("_<_BODY_\>_");

String persontype = request.getParameter("persontype"); String number = request.getParameter("name"); if(persontype.equals("student")) _{_

... code to find students with the specified name ...

... using JDBC to communicate with the database .. out.println("_<_table BORDER COLS=3_\>_"); out.println(" _<_tr_\> <_td_\>_ID_<_/td_\> <_td_\>_Name: _<_/td_\>_" +

" _<_td_\>_Department_<_/td_\> <_/tr_\>_"); for(... each result ...)_{_

... retrieve ID, _name_ and _dept name_

... into variables ID, name and deptname out.println("_<_tr_\> <_td_\>_" + ID + "_<_/td_\>_" +

"_<_td_\>_" + name + "_<_/td_\>_" + "_<_td_\>_" + deptname + "_<_/td_\><_/tr_\>_");

_}_; out.println("_<_/table_\>_");

_}_ else _{_

... as above, but for instructors ... _}_ out.println("_<_/BODY_\>_"); out.close();

_} }_

**Figure 9.8** Example of servlet code.

**9.3.2 Servlet Sessions**

Recall that the interaction between a browser and a Web server is stateless. That is, each time the browser makes a request to the server, the browser needs to connect to the server, request some information, then disconnect from the server. Cookies can be used to recognize that a request is from the same browser session as an  

**9.3 Servlets and JSP 385**

earlier request. However, cookies form a low-level mechanism, and programmers require a better abstraction to deal with sessions.

The servlet API provides a method of tracking a session and storing infor- mation pertaining to it. Invocation of the method getSession(false) of the class HttpServletRequest retrieves the HttpSession object corresponding to the browser that sent the request. An argument value of true would have specified that a new session object must be created if the request is a new request.

When the getSession() method is invoked, the server first asks the client to return a cookie with a specified name. If the client does not have a cookie of that name, or returns a value that does not match any ongoing session, then the request is not part of an ongoing session. In this case, getSession() would return a null value, and the servlet could direct the user to a login page.

The login page could allow the user to provide a user name and password. The servlet corresponding to the login page could verify that the password matches the user (for example, by looking up authentication information in the database). If the user is properly authenticated, the login servlet would execute getSes- sion(true), which would return a new session object. To create a new session the Web server would internally carry out the following tasks: set a cookie (called, for example, sessionId) with a session identifier as its associated value at the client browser, create a new session object, and associate the session identifier value with the session object.

The servlet code can also store and look up (attribute-name, value) pairs in the HttpSession object, to maintain state across multiple requests within a session. For example, after the user is authenticated and the session object has been created, the login servlet could store the user-id of the user as a session parameter by executing the method

session.setAttribute(“userid”, userid)

on the session object returned by getSession(); the Java variable userid is assumed to contain the user identifier.

If the request was part of an ongoing session, the browser would have re- turned the cookie value, and the corresponding session object is returned by getSession(). The servlet can then retrieve session parameters such as user-id from the session object by executing the method

session.getAttribute(“userid”)

on the session object returned above. If the attribute userid is not set, the function would return a null value, which would indicate that the client user has not been authenticated.

**9.3.3 Servlet Life Cycle**

The life cycle of a servlet is controlled by the Web server in which the servlet has been deployed. When there is a client request for a specific servlet, the server  

**386 Chapter 9 Application Design and Development**

first checks if an instance of the servlet exists or not. If not, the Web server loads the servlet class into the Java virtual machine (JVM), and creates an instance of the servlet class. In addition, the server calls the init() method to initialize the servlet instance. Notice that each servlet instance is initialized only once when it is loaded.

After making sure the servlet instance does exist, the server invokes the service method of the servlet, with a request object and a response object as parameters. By default, the server creates a new thread to execute the service method; thus, multiple requests on a servlet can execute in parallel, without having to wait for earlier requests to complete execution. The service method calls doGet or doPost as appropriate.

When no longer required, a servlet can be shut down by calling the destroy() method. The server can be set up to automatically shut down a servlet if no requests have been made on a servlet within a time-out period; the time-out period is a server parameter that can be set as appropriate for the application.

**9.3.4 Servlet Support**

Many application servers provide built-in support for servlets. One of the most popular is the Tomcat Server from the Apache Jakarta Project. Other application servers that support servlets include Glassfish, JBoss, BEA Weblogic Application Server, Oracle Application Server, and IBM’s WebSphere Application Server.

The best way to develop servlet applications is by using an IDE such as Eclipse or NetBeans, which come with Tomcat or Glassfish servers built in.

Application servers usually provide a variety of useful services, in addition to basic servlet support. They allow applications to be deployed or stopped, and provide functionality to monitor the status of the application server, including performance statistics. If a servlet file is modified, some application servers can detect this and recompile and reload the servlet transparently. Many application servers also allow the server to run on multiple machines in parallel to improve performance, and route requests to an appropriate copy. Many application servers also support the Java 2 Enterprise Edition (J2EE) platform, which provides support and APIs for a variety of tasks, such as for handling objects, parallel processing across multiple application servers, and for handling XML data (XML is described later in Chapter 23).

**9.3.5 Server-Side Scripting**

Writing even a simple Web application in a programming language such as Java or C is a time-consuming task that requires many lines of code and programmers who are familiar with the intricacies of the language. An alternative approach, that of **server-side scripting**, provides a much easier method for creating many applications. Scripting languages provide constructs that can be embedded within HTML documents. In server-side scripting, before delivering a Web page, the server executes the scripts embedded within the HTML contents of the page. Each piece of script, when executed, can generate text that is added to the page (or may even delete content from the page). The source code of the scripts is removed  

**9.3 Servlets and JSP 387**

_<_html_\> <_head_\> <_title_\>_ Hello _<_/title_\> <_/head_\>_

_<_body_\>_

_<_ % if (request.getParameter(“name”) == null) _{_ out.println(“Hello World”); _}_ else _{_ out.println(“Hello, ” + request.getParameter(“name”)); _}_

%_\>_

_<_/body_\>_

_<_/html_\>_

**Figure 9.9** A JSP page with embedded Java code.

from the page, so the client may not even be aware that the page originally had any code in it. The executed script may contain SQL code that is executed against a database.

Some of the widely used scripting languages include Java Server Pages (JSP) from Sun, Active Server Pages (ASP) and its successor ASP.NET from Microsoft, the PHP scripting language, the ColdFusion Markup Language (CFML), and Ruby on Rails. Many scripting languages also allow code written in languages such as Java, C#, VBScript, Perl, and Python to be embedded into or invoked from HTML pages. For instance, JSP allows Java code to be embedded in HTML pages, while Microsoft’s ASP.NET and ASP support embedded C# and VBScript. Many of these languages come with libraries and tools, that together constitute a framework for Web application development.

We briefly describe below **Java Server Pages** (**JSP**), a scripting language that allows HTML programmers to mix static HTML with dynamically generated HTML. The motivation is that, for many dynamic Web pages, most of their content is still static (that is, the same content is present whenever the page is generated). The dynamic content of the Web pages (which are generated, for example, on the basis of form parameters) is often a small part of the page. Creating such pages by writing servlet code results in a large amount of HTML being coded as Java strings. JSP instead allows Java code to be embedded in static HTML; the embedded Java code generates the dynamic part of the page. JSP scripts are actually translated into servlet code that is then compiled, but the application programmer is saved the trouble of writing much of the Java code to create the servlet.

Figure 9.9 shows the source text of an JSP page that includes embedded Java code. The Java code in the script is distinguished from the surrounding HTML code by being enclosed in _<_% _. . ._ %_\>_. The code uses request.getParameter() to get the value of the attribute name.

When a JSP page is requested by a browser, the application server generates HTML output from the page, which is sent back to the browser. The HTML part of the JSP page is output as is.4 Wherever Java code is embedded within _<_% _. . ._%_\>_,

4JSP allows a more complex embedding, where HTML code is within a Java if-else statement, and gets output condi- tionally depending on whether the if condition evaluates to true or not. We omit details here.  

**388 Chapter 9 Application Design and Development**

**PHP**

PHP is a scripting language that is widely used for server-side scripting. PHP code can be intermixed with HTML in a manner similar to JSP. The characters “_<_?php” indicate the start of PHP code, while the characters “?_\>_” indicate the end of PHP code. The following code performs the same actions as the JSP code in Figure 9.9.

_<_html_\> <_head_\> <_title_\>_ Hello _<_/title_\> <_/head_\>_

_<_body_\>_

_<_?php if (!isset($ REQUEST[’name’])) _{_ echo ’Hello World’; _}_ else _{_ echo ’Hello, ’ . $ REQUEST[’name’]; _}_

?_\>_

_<_/body_\>_

_<_/html_\>_

The array $ REQUEST contains the request parameters. Note that the array is indexed by the parameter name; in PHP arrays can be indexed by arbitrary strings, not just numbers. The function isset checks if the element of the array has been initialized. The echo function prints its argument to the output HTML. The operator “.” between two strings concatenates the strings.

A suitably configured Web server would interpret any file whose name ends in “.php” to be a PHP file. If the file is requested, the Web server process it in a manner similar to how JSP files are processed, and returns the generated HTML to the browser.

A number of libraries are available for the PHP language, including libraries for database access using ODBC (similar to JDBC in Java).

the code is replaced in the HTML output by the text it prints to the object out. In the JSP code in the above figure, if no value was entered for the form parameter name, the script prints “Hello World”; if a value was entered, the script prints “Hello” followed by the name.

A more realistic example may perform more complex actions, such as looking up values from a database using JDBC.

JSP also supports the concept of a _tag library_, which allows the use of tags that look much like HTML tags, but are interpreted at the server, and are replaced by appropriately generated HTML code. JSP provides a standard set of tags that de- fine variables and control flow (iterators, if-then-else), along with an expression language based on JavaScript (but interpreted at the server). The set of tags is extensible, and a number of tag libraries have been implemented. For example, there is a tag library that supports paginated display of large data sets, and a li- brary that simplifies display and parsing of dates and times. See the bibliographic notes for references to more information on JSP tag libraries.  

**9.3 Servlets and JSP 389**

**9.3.6 Client-Side Scripting**

Embedding of program code in documents allows Web pages to be **active**, car- rying out activities such as animation by executing programs at the local site, instead of just presenting passive text and graphics. The primary use of such pro- grams is flexible interaction with the user, beyond the limited interaction power provided by HTML and HTML forms. Further, executing programs at the client site speeds up interaction greatly, compared to every interaction being sent to a server site for processing.

A danger in supporting such programs is that, if the design of the system is done carelessly, program code embedded in a Web page (or equivalently, in an email message) can perform malicious actions on the user’s computer. The malicious actions could range from reading private information, to deleting or modifying information on the computer, up to taking control of the computer and propagating the code to other computers (through email, for example). A number of email viruses have spread widely in recent years in this way.

One of the reasons that the Java language became very popular is that it provides a safe mode for executing programs on users’ computers. Java code can be compiled into platform-independent “byte-code” that can be executed on any browser that supports Java. Unlike local programs, Java programs (applets) downloaded as part of a Web page have no authority to perform any actions that could be destructive. They are permitted to display data on the screen, or to make a network connection to the server from which the Web page was downloaded, in order to fetch more information. However, they are not permitted to access local files, to execute any system programs, or to make network connections to any other computers.

While Java is a full-fledged programming language, there are simpler lan- guages, called **scripting languages**, that can enrich user interaction, while pro- viding the same protection as Java. These languages provide constructs that can be embedded with an HTML document. **Client-side scripting languages** are lan- guages designed to be executed on the client’s Web browser.

Of these, the _JavaScript_ language is by far the most widely used. The current generation of Web interfaces uses the JavaScript scripting language extensively to construct sophisticated user interfaces. JavaScript is used for a variety of tasks. For example, functions written in JavaScript can be used to perform error checks (validation) on user input, such as a date string being properly formatted, or a value entered (such as age) being in an appropriate range. These checks are carried out on the browser as data is entered, even before the data are sent to the Web server.

Figure 9.10 shows an example of a JavaScript function used to validate a form input. The function is declared in the head section of the HTML document. The function checks that the credits entered for a course is a number greater than 0, and less than 16. The form tag specifies that the validation function is to be invoked when the form is submitted. If the validation fails, an alert box is shown to the user, and if it succeeds, the form is submitted to the server.

JavaScript can be used to modify dynamically the HTML code being displayed. The browser parses HTML code into an in-memory tree structure defined by  

**390 Chapter 9 Application Design and Development**

<html> <head> <script type="text/javascript">

function validate() _{_ var credits=document.getElementById("credits").value; if (isNaN(credits)|| credits<=0 || credits>=16) _{_

alert("Credits must be a number greater than 0 and less than 16"); return false

_} }_

</script> </head>

<body> <form action="createCourse" onsubmit="return validate()">

Title: <input type="text" id="title" size="20"><br /> Credits: <input type="text" id="credits" size="2"><br /> <input type="submit" value="Submit">

</form> </body> </html>

**Figure 9.10** Example of JavaScript used to validate form input

a standard called the **Document Object Model** (**DOM**). JavaScript code can modify the tree structure to carry out certain operations. For example, suppose a user needs to enter a number of rows of data, for example multiple items in a single bill. A table containing text boxes and other form input methods can be used to gather user input. The table may have a default size, but if more rows are needed, the user may click on a button labeled (for example) “Add Item.” This button can be set up to invoke a JavaScript function that modifies the DOM tree by adding an extra row in the table.

Although the JavaScript language has been standardized, there are differ- ences between browsers, particularly in the details of the DOM model. As a result, JavaScript code that works on one browser may not work on another. To avoid such problems, it is best to use a JavaScript library, such as Yahoo’s YUI library, which allows code to be written in a browser independent way. Internally, the functions in the library can find out which browser is in use, and send appropri- ately generated JavaScript to the browser. See the Tools section at the end of the chapter for more information on YUI and other libraries.

Today, JavaScript is widely used to create dynamic Web pages, using several technologies that are collectively called **Ajax**. Programs written in JavaScript communicate with the Web server asynchronously (that is, in the background, without blocking user interaction with the Web browser), and can fetch data and display it.  

**9.4 Application Architectures 391**

As an example of the use of Ajax, consider a Web site with a form that allows you to select a country, and once a country has been selected, you are allowed to select a state from a list of states in that country. Until the country is selected, the drop-down list of states is empty. The Ajax framework allows the list of states to be downloaded from the Web site in the background when the country is selected, and as soon as the list has been fetched, it is added to the drop-down list, which allows you to select the state.

There are also special-purpose scripting languages for specialized tasks such as animation (for example, Flash and Shockwave) and three-dimensional model- ing (Virtual Reality Markup Language (VRML)). Flash is very widely used today not only for animation, but also for handling streaming video content.

**9.4 Application Architectures**

To handle their complexity, large applications are often broken into several layers:

• The _presentation_ or _user interface_ layer, which deals with user interaction. A single application may have several different versions of this layer, corre- sponding to distinct kinds of interfaces such as Web browsers, and user interfaces of mobile phones, which have much smaller screens.

In many implementations, the presentation/user-interface layer is it- self conceptually broken up into layers, based on the **model-view-controller** (MVC) architecture. The **model** corresponds to the business-logic layer, de- scribed below. The **view** defines the presentation of data; a single underlying model can have different views depending on the specific software/device used to access the application. The **controller** receives events (user actions), executes actions on the model, and returns a view to the user. The MVC ar- chitecture is used in a number of Web application frameworks, which are discussed later in Section 9.5.2.

• The **business-logic** layer, which provides a high-level view of data and ac- tions on data. We discuss the business-logic layer in more detail in Sec- tion 9.4.1.

• The **data access** layer, which provides the interface between the business-logic layer and the underlying database. Many applications use an object-oriented language to code the business-logic layer, and use an object-oriented model of data, while the underlying database is a relational database. In such cases, the data-access layer also provides the mapping from the object-oriented data model used by the business logic to the relational model supported by the database. We discuss such mappings in more detail in Section 9.4.2.

Figure 9.11 shows the above layers, along with a sequence of steps taken to process a request from the Web browser. The labels on the arrows in the figure indicate the order of the steps. When the request is received by the application server, the controller sends a request to the model. The model processes the  

**392 Chapter 9 Application Design and Development**

Web browser

Internet 1

8 7

6 5 2

3

4

Database

Web/Application Server

View

Controller

Model

Data Access Layer

**Figure 9.11** Web application architecture.

request, using business logic, which may involve updating objects that are part of the model, followed by creating a result object. The model in turn uses the data- access layer to update or retrieve information from a database. The result object created by the model is sent to the view module, which creates an HTML view of the result, to be displayed on the Web browser. The view may be tailored based on the characteristics of the device used to view the result, for example whether it is a computer monitor with a large screen, or a small screen on a phone.

**9.4.1 The Business-Logic Layer**

The business-logic layer of an application for managing a university may provide abstractions of entities such as students, instructors, courses, sections, etc., and actions such as admitting a student to the university, enrolling a student in a course, and so on. The code implementing these actions ensures that **business rules** are satisfied; for example the code would ensure that a student can enroll for a course only if she has already completed course prerequisites, and has paid her tuition fees.

In addition, the business logic includes **workflows,** which describe how a particular task that involves multiple participants is handled. For example, if a candidate applies to the university, there is a workflow that defines who should see and approve the application first, and if approved in the first step, who should see the application next, and so on until either an offer is made to the student, or a rejection note is sent out. Workflow management also needs to deal with error situations; for example, if a deadline for approval/rejection is not met, a supervisor may need to be informed so she can intervene and ensure the application is processed. Workflows are discussed in more detail in Section 26.2.  

**9.4 Application Architectures 393**

**9.4.2 The Data-Access Layer and Object-Relational Mapping**

In the simplest scenario, where the business-logic layer uses the same data model as the database, the data-access layer simply hides the details of interfacing with the database. However, when the business-logic layer is written using an object- oriented programming language, it is natural to model data as objects, with methods invoked on objects.

In early implementations, programmers had to write code for creating objects by fetching data from the database, and for storing updated objects back in the database. However, such manual conversions between data models is cumber- some and error prone. One approach to handling this problem was to develop a database system that natively stores objects, and relationships between objects. Such databases, called **object-oriented databases**, are discussed in more detail in Chapter 22. However, object-oriented databases did not achieve commercial success for a variety of technical and commercial reasons.

An alternative approach is to use traditional relational databases to store data, but to automate the mapping of data in relations to in-memory objects, which are created on demand (since memory is usually not sufficient to store all data in the database), as well as the reverse mapping to store updated objects back as relations in the database.

Several systems have been developed to implement such **object-relational mappings**. The **Hibernate** system is widely used for mapping from Java objects to relations. In Hibernate, the mapping from each Java class to one or more relations is specified in a mapping file. The mapping file can specify, for example, that a Java class called _Student_ is mapped to the relation _student_, with the Java attribute ID mapped to the attribute _student_.ID, and so on. Information about the database, such as the host on which it is running, and user name and password for connecting to the database, etc., are specified in a _properties_ file. The program has to open a _session_, which sets up the connection to the database. Once the session is set up, a _Student_ object _stud_ created in Java can be stored in the database by invoking session.save(_stud_). The Hibernate code generates the SQL commands required to store corresponding data in the _student_ relation.

A list of objects can be retrieved from the database by executing a query written in the Hibernate query language; this is similar to executing a query us- ing JDBC, which returns a ResultSet containing a set of tuples. Alternatively, a single object can be retrieved by providing its primary key. The retrieved objects can be updated in memory; when the transaction on the ongoing Hibernate ses- sion is committed, Hibernate automatically saves the updated objects by making corresponding updates on relations in the database.

While entities in an E-R model naturally correspond to objects in an object- oriented language such as Java, relationships often do not. Hibernate supports the ability to map such relationships as sets associated with objects. For example, the _takes_ relationship between _student_ and _section_ can be modeled by associating a set of _section_s with each _student_, and a set of _student_s with each _section_. Once the appropriate mapping is specified, Hibernate populates these sets automatically from the database relation _takes_, and updates to the sets are reflected back to the database relation on commit.  

**394 Chapter 9 Application Design and Development**

**HIBERNATE EXAMPLE**

As an example of the use of Hibernate, we create a Java class corresponding to the _student_ relation as follows.

public class Student _{_ String ID; String name; String department; int tot cred; Student(String id, String name, String dept, int totcreds); // constructor

_}_ To be precise, the class attributes should be declared as private, and getter/setter methods should be provided to access the attributes, but we omit these details.

The mapping of the class attributes of Student to attributes of the relation _student_ is specified in a mapping file, in an XML format. Again, we omit details.

The following code snippet then creates a Student object and saves it to the database.

Session session = getSessionFactory().openSession(); Transaction txn = session.beginTransaction(); Student stud = new Student("12328", "John Smith", "Comp. Sci.", 0); session.save(stud); txn.commit(); session.close();

Hibernate automatically generates the required SQL **insert** statement to create a _student_ tuple in the database.

To retrieve students, we can use the following code snippet

Session session = getSessionFactory().openSession(); Transaction txn = session.beginTransaction(); List students =

session.find("from Student as s order by s.ID asc"); for ( Iterator iter = students.iterator(); iter.hasNext(); ) _{_

Student stud = (Student) iter.next(); .. print out the Student information ..

_}_ txn.commit(); session.close();

The above code snippet uses a query in Hibernate’s HQL query language. The HQL query is automatically translated to SQL by Hibernate and executed, and the results are converted into a list of Student objects. The for loop iterates over the objects in this list and prints them out.  

**9.4 Application Architectures 395**

The above features help to provide the programmer a high-level model of data without bothering about the details of the relational storage. However, Hibernate, like other object-relational mapping systems, also allows programmers direct SQL access to the underlying relations. Such direct access is particularly important for writing complex queries used for report generation.

Microsoft has developed a data model called the **Entity Data Model**, which can be viewed as a variant of the entity-relationship model, and an associated framework called the ADO.NET Entity Framework, which can map data between the Entity Data Model and a relational database. The framework also provides an SQL-like query language called **Entity SQL**, which operates directly on the Entity Data Model.

**9.4.3 Web Services**

In the past, most Web applications used only data available at the application server and its associated database. In recent years, a wide variety of data is available on the Web that is intended to be processed by a program, rather than displayed directly to the user; the program may be running as part of a back-end application, or may be a script running in the browser. Such data are typically accessed using what is in effect a Web application programming interface; that is, a function call request is sent using the HTTP protocol, executed at an application server, and the results sent back to the calling program. A system that supports such an interface is called a **Web service**.

Two approaches are widely used to implement Web services. In the simpler approach, called **Representation State Transfer** (or **REST**), Web service function calls are executed by a standard HTTP request to a URL at an application server, with parameters sent as standard HTTP request parameters. The application server executes the request (which may involve updating the database at the server), generates and encodes the result, and returns the result as the result of the HTTP request. The server can use any encoding for a particular requested URL; XML, and an encoding of JavaScript objects called **JavaScript Object Notation**(JSON), are widely used. The requestor parses the returned page to access the returned data.

In many applications of such RESTful Web services (that is, Web services using REST), the requestor is JavaScript code running in a Web browser; the code updates the browser screen using the result of the function call. For example, when you scroll the display on a map interface on the Web, the part of the map that needs to be newly displayed may be fetched by JavaScript code using a RESTful interface, and then displayed on the screen.

A more complex approach, sometimes referred to as “Big Web Services,” uses XML encoding of parameters as well as results, has a formal definition of the Web API using a special language, and uses a protocol layer built on top of the HTTP protocol. This approach is described in more detail in Section 23.7.3.

**9.4.4 Disconnected Operation**

Many applications wish to support some operations even when a client is dis- connected from the application server. For example, a student may wish to fill  

**396 Chapter 9 Application Design and Development**

an application form even if her laptop is disconnected from the network, but have it saved back when the laptop is reconnected. As another example, if an email client is built as a Web application, a user may wish to compose an email even if her laptop is disconnected from the network, and have it sent when it is reconnected. Building such applications requires local storage, preferably in the form of a database, in the client machine. The **Gears** software (originally de- veloped by Google) is a browser plug-in that provides a database, a local Web server, and support for parallel execution of JavaScript at the client. The software works identically on multiple operating system/browser platforms, allowing ap- plications to support rich functionality without installation of any software (other than Gears itself). Adobe’s AIR software also provides similar functionality for building Internet applications that can run outside the Web browser.

**9.5 Rapid Application Development**

If Web applications are built without using tools or libraries for constructing the user interface, the programming effort required to construct the user interface can be significantly more than that required for business logic and database access. Several approaches have been developed to reduce the effort required to build applications:

• Provide a library of functions to generate user-interface elements with mini- mal programming.

• Provide drag-and-drop features in an integrated development environment that allows user-interface elements to be dragged from a menu into a design view of a page. The integrated development environment generates code that creates the user-interface element by invoking library functions.

• Automatically generate code for the user interface from a declarative specifi- cation.

All these approaches have been used for creating user interfaces, well before the Web was created, as part of **Rapid Application Development** (**RAD**) tools, and are now used extensively for creating Web applications as well.

Examples of tools designed for rapid development of interfaces for database applications include Oracle Forms, Sybase PowerBuilder, and Oracle Application Express (APEX). In addition, tools designed for Web application development, such as Visual Studio and Netbeans VisualWeb, support several features designed for rapid development of Web interfaces for database backed applications.

We study tools for construction of user interfaces in Section 9.5.1, and study frameworks that support automatic code generation from a system model, in Section 9.5.2.

**9.5.1 Tools for Building User Interfaces**

Many HTML constructs are best generated by using appropriately defined func- tions, instead of being written as part of the code of each Web page. For example,  

**9.5 Rapid Application Development 397**

address forms typically require a menu containing country or state names. In- stead of writing lengthy HTML code to create the required menu each time it is used, it is preferable to define a function that outputs the menu, and to call the function wherever required.

Menus are often best generated from data in the database, such as a table containing country names or state names. The function generating the menu exe- cutes a database query and populates the menu, using the query result. Adding a country or state then requires only a change to the database, not to the application code. This approach has the potential drawback of requiring increased database interaction, but such overhead can be minimized by caching query results at the application server.

Forms to input dates and times, or inputs that require validation, are similarly best generated by calling appropriately defined functions. Such functions can output JavaScript code to perform validation at the browser.

Displaying a set of results from a query is a common task for many database applications. It is possible to build a generic function that takes an SQL query (or ResultSet) as argument, and display the tuples in the query result (or ResultSet) in a tabular form. JDBC metadata calls can be used to find information such as the number of columns and the names and types of the columns in the query result; this information is then used to display the query result.

To handle situations where the query result is very large, such a query result display function can provide for _pagination_ of results. The function can display a fixed number of records in a page and provide controls to step to the next or previous page or jump to a particular page of the results.

There is unfortunately no (widely used) standard Java API for functions to carry out the user-interface tasks described above. Building such a library can be an interesting programming project.

However, there are other tools, such as the JavaServer Faces (JSF) framework, that support the features listed above. The JSF framework includes a JSP tag library that implements these features. The Netbeans IDE has a component called VisualWeb that builds on JSF, providing a visual development environment where user interface components can be dragged and dropped into a page, and their properties customized. For example, JSF provides components to create drop- down menus, or display a table, which can be configured to get their data from a database query. JSF also supports validation specification on components, for example to make a selection or input mandatory, or to constrain a number or a date to be in a specified range.

Microsoft’s Active Server Pages (ASP), and its more recent version, Active Server Pages.NET(ASP.NET), is a widely used alternative to JSP/Java. ASP.NET is similar to JSP, in that code in a language such as Visual Basic or C# can be embedded within HTML code. In addition, ASP.NET provides a variety of controls (scripting commands) that are interpreted at the server, and generate HTML that is then sent to the client. These controls can significantly simplify the construction of Web interfaces. We provide a brief overview of the benefits that these controls offer.  

**398 Chapter 9 Application Design and Development**

For example, controls such as drop-down menus and list boxes can be asso- ciated with a DataSet object. The DataSet object is similar to a JDBC ResultSet object, and is typically created by executing a query on the database. The HTML menu contents are then generated from the DataSet object’s contents; for exam- ple, a query may retrieve the names of all departments in an organization into the DataSet, and the associated menu would contain these names. Thus, menus that depend on database contents can be created in a convenient manner with very little programming.

Validator controls can be added to form input fields; these declaratively spec- ify validity constraints such as value ranges, or whether the input is a required input for which a value must be provided by the user. The server creates ap- propriate HTML code combined with JavaScript to perform the validation at the user’s browser. Error messages to be displayed on invalid input can be associated with each validator control.

User actions can be specified to have an associated action at the server. For example, a menu control can specify that selecting a value from a menu has an associated server-side action (JavaScript code is generated to detect the selection event and initiate the server-side action). Visual Basic/C# code that displays data pertaining to the selected value can be associated with the action at the server. Thus, selecting a value from a menu can result in associated data on the page getting updated, without requiring the user to click on a submit button.

The DataGrid control provides a very convenient way of displaying query results. A DataGrid is associated with a DataSet object, which is typically the result of a query. The server generates HTML code that displays the query result as a table. Column headings are generated automatically from query result meta- data. In addition, DataGrids provide several features, such as pagination, and allow the user to sort the result on chosen columns. All the HTML code as well as server-side functionality to implement these features is generated automati- cally by the server. The DataGrid even allows users to edit the data and submit changes back to the server. The application developer can specify a function, to be executed when a row is edited, that can perform the update on the database.

Microsoft Visual Studio provides a graphical user interface for creating ASP pages using these features, further reducing the programming effort.

See the bibliographic notes for references to more information on ASP.NET.

**9.5.2 Web Application Frameworks**

There are a variety of Web application development frameworks that provide several commonly used features such as:

• An object-oriented model with an object-relational mapping to store data in a relational database (as we saw in Section 9.4.2).

• A (relatively) declarative way of specifying a form with validation constraints on user inputs, from which the system generates HTML and Javascript/Ajax code to implement the form.  

**9.5 Rapid Application Development 399**

• A template scripting system (similar to JSP).

• A controller that maps user interaction events such as form submits to ap- propriate functions that handle the event. The controller also manages au- thentication and sessions. Some frameworks also provide tools for managing authorizations.

Thus, these frameworks provide a variety of features that are required to build Web applications, in an integrated manner. By generating forms from declarative specifications, and managing data access transparently, the frameworks minimize the amount of coding that a Web application programmer has to carry out.

There are a large number of such frameworks, based on different languages. Some of the more widely used frameworks include Ruby on Rails, which is based on the Ruby programming language, JBoss Seam, Apache Struts, Swing, Tapestry, and WebObjects, all based on Java/JSP. Some of these, such as Ruby on Rails and JBoss Seam provide a tool that can automatically create simple **CRUD** Web interfaces; that is, interfaces that support create, read, update and delete of objects/tuples, by generating code from an object model or a database. Such tools are particularly useful to get simple applications running quickly, and the generated code can be edited to build more sophisticated Web interfaces.

**9.5.3 Report Generators**

Report generators are tools to generate human-readable summary reports from a database. They integrate querying the database with the creation of formatted text and summary charts (such as bar or pie charts). For example, a report may show the total sales in each of the past 2 months for each sales region.

The application developer can specify report formats by using the formatting facilities of the report generator. Variables can be used to store parameters such as the month and the year and to define fields in the report. Tables, graphs, bar charts, or other graphics can be defined via queries on the database. The query definitions can make use of the parameter values stored in the variables.

Once we have defined a report structure on a report-generator facility, we can store it and can execute it at any time to generate a report. Report-generator systems provide a variety of facilities for structuring tabular output, such as defining table and column headers, displaying subtotals for each group in a table, automatically splitting long tables into multiple pages, and displaying subtotals at the end of each page.

Figure 9.12 is an example of a formatted report. The data in the report are generated by aggregation on information about orders.

Report-generation tools are available from a variety of vendors, such as Crys- tal Reports and Microsoft (SQL Server Reporting Services). Several application suites, such as Microsoft Office, provide a way of embedding formatted query results from a database directly into a document. Chart-generation facilities pro- vided by Crystal Reports, or by spreadsheets such as Excel can be used to access data from databases, and generate tabular depictions of data or graphical depic- tions using charts or graphs. Such charts can be embedded within text documents.  

**400 Chapter 9 Application Design and Development**

**Region Category Sales**

North Computer Hardware 1,000,000

Computer Soware 500,000

All categories 1,500,000

South Computer Hardware 200,000

Computer Soware 400,000

All categories 600,000

2,100,000

**Acme Supply Company, Inc. Quarterly Sales Report**

Period: Jan. 1 to March 31, 2009

**Total Sales**

**Subtotal**

**Figure 9.12** A formatted report.

The charts are created initially from data generated by executing queries against the database; the queries can be re-executed and the charts regenerated when required, to generate a current version of the overall report.

In addition to generating static reports, report-generation tools support the creation of interactive reports. For example, a user can “drill down” into areas of interest, for example move from an aggregate view showing the total sales across an entire year to the monthly sales figures for a particular year. Such operations were discussed earlier, in Section 5.6.

**9.6 Application Performance**

Web sites may be accessed by millions of people from across the globe, at rates of thousands of requests per second, or even more, for the most popular sites. Ensur- ing that requests are served with low response times is a major challenge for Web site developers. To do so, application developers try to speed up the processing of individual requests by using techniques such as caching, and exploit parallel processing by using multiple application servers. We describe these techniques briefly below. Tuning of database applications is described in more detail later, in Chapter 24 (Section 24.1).

**9.6.1 Reducing Overhead by Caching**

Caching techniques of various types are used to exploit commonalities between transactions. For instance, suppose the application code for servicing each user request needs to contact a database through JDBC. Creating a new JDBC connection may take several milliseconds, so opening a new connection for each user request is not a good idea if very high transaction rates are to be supported.  

**9.6 Application Performance 401**

The **Connection pooling** method is used to reduce this overhead; it works as follows. The connection pool manager (a part of the application server) creates a pool (that is, a set) of open ODBC/JDBC connections. Instead of opening a new connection to the database, the code servicing a user request (typically a servlet) asks for (requests) a connection from the connection pool and returns the con- nection to the pool when the code (servlet) completes its processing. If the pool has no unused connections when a connection is requested, a new connection is opened to the database (taking care not to exceed the maximum number of con- nections that the database system can support concurrently). If there are many open connections that have not been used for a while, the connection pool man- ager may close some of the open database connections. Many application servers, and newer ODBC/JDBC drivers provide a built-in connection pool manager.

A common error that many programmers make when creating Web appli- cations is to forget to close an opened JDBC connection (or equivalently, when connection pooling is used, to forget to return the connection to the connection pool). Each request then opens a new connection to the database, and the database soon reaches the limit of how many open connections it can have at a time. Such problems often do not show up on small-scale testing, since databases often al- low hundreds of open connections, but show up only on intensive usage. Some programmers assume that connections, like memory allocated by Java programs, are garbage collected automatically. Unfortunately, this does not happen, and programmers are responsible for closing connections that they have opened.

Certain requests may result in exactly the same query being resubmitted to the database. The cost of communication with the database can be greatly reduced by caching the results of earlier queries and reusing them, so long as the query result has not changed at the database. Some Web servers support such query-result caching; caching can otherwise be done explicitly in application code.

Costs can be further reduced by caching the final Web page that is sent in response to a request. If a new request comes with exactly the same parameters as a previous request, the request does not perform any updates, and the resultant Web page is in the cache, then it can be reused to avoid the cost of recomputing the page. Caching can be done at the level of fragments of Web pages, which are then assembled to create complete Web pages.

Cached query results and cached Web pages are forms of materialized views. If the underlying database data change, the cached results must be discarded, or recomputed, or even incrementally updated, as in materialized-view maintenance (described later, in Section 13.5). Some database systems (such as Microsoft SQL Server) provide a way for the application server to register a query with the database, and get a **notification** from the database when the result of the query changes. Such a notification mechanism can be used to ensure that query results cached at the application server are up-to-date.

**9.6.2 Parallel Processing**

A commonly used approach to handling such very heavy loads is to use a large number of application servers running in parallel, each handling a fraction of the  

**402 Chapter 9 Application Design and Development**

requests. A Web server or a network router can be used to route each client request to one of the application servers. All requests from a particular client session must go to the same application server, since the server maintains state for a client session. This property can be ensured, for example, by routing all requests from a particular IP address to the same application server. The underlying database is, however, shared by all the application servers, so that users see a consistent view of the database.

With the above architecture, the database could easily become the bottleneck, since it is shared. Application designers pay particular attention to minimizing the number of requests to the database, by caching query results at the application server, as discussed earlier. In addition, parallel database systems, described in Chapter 18, are used when required.

**9.7 Application Security**

Application security has to deal with several security threats and issues beyond those handled by SQL authorization.

The first point where security has to be enforced is in the application. To do so, applications must authenticate users, and ensure that users are only allowed to carry out authorized tasks.

There are many ways in which an application’s security can be compromised, even if the database system is itself secure, due to badly written application code. In this section, we first describe several security loopholes that can permit hackers to carry out actions that bypass the authentication and authorization checks carried out by the application, and explain how to prevent such loopholes. Later in the section, we describe techniques for secure authentication, and for fine- grained authorization. We then describe audit trails that can help in recovering from unauthorized access and from erroneous updates. We conclude the section by describing issues in data privacy.

**9.7.1 SQL Injection**

In **SQL injection** attacks, the attacker manages to get an application to execute an SQL query created by the attacker. In Section 5.1.1.4, we saw an example of an SQL injection vulnerability if user inputs are concatenated directly with an SQL query and submitted to the database. As another example of SQL injection vulnerability, consider the form source text shown in Figure 9.4. Suppose the corresponding servlet shown in Figure 9.8 creates an SQL query string using the following Java expression:

String query = “select \* from student where name like ’%” + name + “%’”

where name is a variable containing the string input by the user, and then executes the query on the database. A malicious attacker using the Web form can then type  

**9.7 Application Security 403**

a string such as “’;_<_some SQL statement_\>_; −− ”, where _<_some SQL statement_\>_ denotes any SQL statement that the attacker desires, in place of a valid student name. The servlet would then execute the following string.

**select** _\*_ **from** _student_ **where** _name_ **like** ’ ’; _<_some SQL statement_\>_; −− ’

The quote inserted by the attacker closes the string, the following semicolon ter- minates the query, and the following text inserted by the attacker gets interpreted as a second SQL query, while the closing quote has been commented out. Thus, the malicious user has managed to insert an arbitrary SQL statement that is executed by the application. The statement can cause significant damage, since it can per- form any action on the database, bypassing all security measures implemented in the application code.

As discussed in Section 5.1.1.4, to avoid such attacks, it is best to use prepared statements to execute SQL queries. When setting a parameter of a prepared query, JDBC automatically adds escape characters so that the user-supplied quote would no longer be able to terminate the string. Equivalently, a function that adds such escape characters could be applied on input strings before they are concatenated with the SQL query, instead of using prepared statements.

Another source of SQL-injection risk comes from applications that create queries dynamically, based on selection conditions and ordering attributes spec- ified in a form. For example, an application may allow a user to specify what attribute should be used for sorting the results of a query. An appropriate SQL query is constructed, based on the attribute specified. Suppose the application takes the attribute name from a form, in the variable orderAttribute, and creates a query string such as the following:

String query = “select \* from takes order by ” + orderAttribute;

A malicious user can send an arbitrary string in place of a meaningful or- derAttribute value, even if the HTML form used to get the input tried to restrict the allowed values by providing a menu. To avoid this kind of SQL injection, the application should ensure that the orderAttribute variable value is one of the allowed values (in our example, attribute names), before appending it.

**9.7.2 Cross Site Scripting and Request Forgery**

A Web site that allows users to enter text, such as a comment or a name, and then stores it and later displays it to other users, is potentially vulnerable to a kind of attack called a **cross-site scripting** (**XSS**) attack. In such an attack, a malicious user enters code written in a client-side scripting language such as JavaScript or Flash instead of entering a valid name or comment. When a different user views the entered text, the browser would execute the script, which can carry out actions such as sending private cookie information back to the malicious user, or even executing an action on a different Web server that the user may be logged into.  

**404 Chapter 9 Application Design and Development**

For example, suppose the user happens to be logged into her bank account at the time the script executes. The script could send cookie information related to the bank account login back to the malicious user, who could use the information to connect to the bank’s Web server, fooling it into believing that the connection is from the original user. Or, the script could access appropriate pages on the bank’s Web site, with appropriately set parameters, to execute a money transfer. In fact this particular problem can occur even without scripting by simply using a line of code such as

_<_img src= "http://mybank.com/transfermoney?amount=1000&toaccount=14523"_\>_

assuming that the URL mybank.com/transfermoney accepts the specified param- eters, and carries out a money transfer. This latter kind of vulnerability is also called **cross-site request forgery** or **XSRF** (sometimes also called **CSRF**).

XSS can be done in other ways, such as luring a user into visiting a Web site that has malicious scripts embedded in its pages. There are other more complex kinds of XSS and XSRF attacks, which we shall not get into here. To protect against such attacks, two things need to be done:

• **Prevent your Web site from being used to launch XSS or XSRF attacks.** The simplest technique is to disallow any HTML tags whatsoever in text input by users. There are functions that detect, or strip all such tags. These functions can be used to prevent HTML tags, and as a result, any scripts, from being displayed to other users. In some cases HTML formatting is useful, and in that case functions that parse the text and allow limited HTML constructs, but disallow other dangerous constructs can be used instead; these must be designed carefully, since something as innocuous as an image include could potentially be dangerous in case there is a bug in the image display software that can be exploited.

• **Protect your Web site from XSS or XSRF attacks launched from other sites.** If the user has logged into your Web site, and visits a different Web site vulnerable to XSS, the malicious code executing on the user’s browser could execute actions on your Web site, or pass session information related to your Web site back to the malicious user who could try to exploit it. This cannot be prevented altogether, but you can take a few steps to minimize the risk.

◦ The HTTP protocol allows a server to check the **referer** of a page access, that is, the URL of the page that had the link that the user clicked on to initiate the page access. By checking that the referer is valid, for example, that the referer URL is a page on the same Web site, XSS attacks that originated on a different Web page accessed by the user can be prevented.

◦ Instead of using only the cookie to identify a session, the session could also be restricted to the IP address from which it was originally authenticated.  

**9.7 Application Security 405**

As a result, even if a malicious user gets a cookie, he may not be able to log in from a different computer.

◦ Never use a GET method to perform any updates. This prevents attacks using _<_img src .._\>_ such as the one we saw earlier. In fact, the HTTP stan- dard recommends that GET methods should never perform any updates, for other reasons such as a page refresh repeating an action that should have happened only once.

**9.7.3 Password Leakage**

Another problem that application developers must deal with is storing passwords in clear text in the application code. For example, programs such as JSP scripts often contain passwords in clear text. If such scripts are stored in a directory accessible by a Web server, an external user may be able to access the source code of the script, and get access to the password for the database account used by the application. To avoid such problems, many application servers provide mechanisms to store passwords in encrypted form, which the server decrypts before passing it on to the database. Such a feature removes the need for storing passwords as clear text in application programs. However, if the decryption key is also vulnerable to being exposed, this approach is not fully effective.

As another measure against compromised database passwords, many data- base systems allow access to the database to be restricted to a given set of Internet addresses, typically, the machines running the application servers. Attempts to connect to the database from other Internet addresses are rejected. Thus, unless the malicious user is able to log into the application server, she cannot do any damage even if she gains access to the database password.

**9.7.4 Application Authentication**

Authentication refers to the task of verifying the identity of a person/software connecting to an application. The simplest form of authentication consists of a secret password that must be presented when a user connects to the application. Unfortunately, passwords are easily compromised, for example, by guessing, or by sniffing of packets on the network if the passwords are not sent encrypted. More robust schemes are needed for critical applications, such as online bank accounts. Encryption is the basis for more robust authentication schemes. Au- thentication through encryption is addressed in Section 9.8.3.

Many applications use **two-factor authentication**, where two independent _factors_ (that is, pieces of information or processes) are used to identify a user. The two factors should not share a common vulnerability; for example, if a system merely required two passwords, both could be vulnerable to leakage in the same manner (by network sniffing, or by a virus on the computer used by the user, for example). While biometrics such as fingerprints or iris scanners can be used in situations where a user is physically present at the point of authentication, they are not very meaningful across a network.  

**406 Chapter 9 Application Design and Development**

Passwords are used as the first factor in most such two-factor authentication schemes. Smart cards or other encryption devices connected through the USB interface, which can be used for authentication based on encryption techniques (see Section 9.8.3), are widely used as second factors.

One-time password devices, which generate a new pseudo-random number (say) every minute are also widely used as a second factor. Each user is given one of these devices, and must enter the number displayed by the device at the time of authentication, along with the password, to authenticate himself. Each device generates a different sequence of pseudo-random numbers. The application server can generate the same sequence of pseudo-random numbers as the device given to the user, stopping at the number that would be displayed at the time of authentication, and verify that the numbers match. This scheme requires that the clock in the device and at the server are synchronized reasonably closely.

Yet another second-factor approach is to send an SMS with a (randomly gener- ated) one-time password to the user’s phone (whose number is registered earlier) whenever the user wishes to log in to the application. The user must possess a phone with that number to receive the SMS, and then enter the one-time password, along with her regular password, to be authenticated.

It is worth noting that even with two-factor authentication, users may still be vulnerable to **man-in-the-middle** attacks. In such attacks, a user attempting to connect to the application is diverted to a fake Web site, which accepts the pass- word (including second factor passwords) from the user, and uses it immediately to authenticate to the original application. The HTTPS protocol, described later in Section 9.8.3.2, is used to authenticate the Web site to the user (so the user does not connect to a fake site believing it to be the intended site). The HTTPS protocol also encrypts data, and prevents man-in-the-middle attacks.

When users access multiple Web sites, it is often annoying for a user to have to authenticate herself to each site separately, often with different passwords on each site. There are systems that allow the user to authenticate herself to one central authentication service, and other Web sites and applications can authenticate the user through the central authentication service; the same password can then be used to access multiple sites. The LDAP protocol is widely used to implement such a central point of authentication; organizations implement an LDAP server containing user names and password information, and applications use the LDAP server to authenticate users.

In addition to authenticating users, a central authentication service can pro- vide other services, for example, providing information about the user such as name, email, and address information, to the application. This obviates the need to enter this information separately in each application. LDAP can be used for this task, as described later in Section 19.10.2. Other directory systems such Mi- crosoft’s Active Directories, also provide mechanisms for authenticating users as well as for providing user information.

A **single sign-on** system further allows the user to be authenticated once, and multiple applications can then verify the user’s identity through an authentication service without requiring reauthentication. In other words, once a user is logged  

**9.7 Application Security 407**

in at one site, he does not have to enter his user name and password at other sites that use the same single sign-on service. Such single sign-on mechanisms have long been used in network authentication protocols such as Kerberos, and implementations are now available for Web applications.

The **Security Assertion Markup Language** (**SAML**) is a standard for ex- changing authentication and authorization information between different secu- rity domains, to provide cross-organization single sign-on. For example, suppose an application needs to provide access to all students from a particular university, say Yale. The university can set up a Web-based service that carries out authen- tication. Suppose a user connects to the application with a username such as “joe@yale.edu”. The application, instead of directly authenticating a user, diverts the user to Yale University’s authentication service, which authenticates the user, and then tells the application who the user is and may provide some additional information such as the category of the user (student or instructor) or other rele- vant information. The user’s password and other authentication factors are never revealed to the application, and the user need not register explicitly with the application. However, the application must trust the university’s authentication service when authenticating a user.

The **OpenID** standard is an alternative for single sign-on across organizations, and has seen increasing acceptance in recent years. A large number of popular Web sites, such as Google, Microsoft, Yahoo!, among many others, act as OpenID authentication providers. Any application that acts as an OpenID client can then use any of these providers to authenticate a user; for example, a user who has a Yahoo! account can choose Yahoo! as the authentication provider. The user is redirected to Yahoo! for authentication, and on successful authentication is transparently redirected back to the application, and can then continue using the application.

**9.7.5 Application-Level Authorization**

Although the SQL standard supports a fairly flexible system of authorization based on roles (described in Section 4.6), the SQL authorization model plays a very limited role in managing user authorizations in a typical application. For instance, suppose you want all students to be able to see their own grades, but not the grades of anyone else. Such authorization cannot be specified in SQL for at least two reasons:

**1\. Lack of end-user information**. With the growth in the Web, database ac- cesses come primarily from Web application servers. The end users typically do not have individual user identifiers on the database itself, and indeed there may only be a single user identifier in the database corresponding to all users of an application server. Thus, authorization specification in SQL cannot be used in the above scenario.

It is possible for an application server to authenticate end users, and then pass the authentication information on to the database. In this section we  

**408 Chapter 9 Application Design and Development**

will assume that the function _syscontext.user id_() returns the identifier of the application user on whose behalf a query is being executed.5

**2\. Lack of fine-grained authorization**. Authorization must be at the level of individual tuples, if we are to authorize students to see only their own grades. Such authorization is not possible in the current SQL standard, which permits authorization only on an entire relation or view, or on specified attributes of relations or views.

We could try to get around this limitation by creating for each student a view on the _takes_ relation that shows only that student’s grades. While this would work in principle, it would be extremely cumbersome since we would have to create one such view for every single student enrolled in the university, which is completely impractical.6

An alternative is to create a view of the form

**create view** _studentTakes_ **as select** \* **from** _takes_ **where** _takes_._ID_\= _syscontext.user id_()

Users are then given authorization to this view, rather than to the underlying _takes_ relation. However, queries executed on behalf of students must now be written on the view _studentTakes_, rather than on the original _takes_ relation, whereas queries executed on behalf of instructors may need to use a different view. The task of developing applications becomes more complex as a result.

The task of authorization is today typically carried out entirely in the applica- tion, bypassing the authorization facilities of SQL. At the application level, users are authorized to access specific interfaces, and may further be restricted to view or update certain data items only.

While carrying out authorization in the application gives a great deal of flexibility to application developers, there are problems, too.

• The code for checking authorization becomes intermixed with the rest of the application code.

• Implementing authorization through application code, rather than specifying it declaratively in SQL, makes it hard to ensure the absence of loopholes. Because of an oversight, one of the application programs may not check for authorization, allowing unauthorized users access to confidential data.

5In Oracle, a JDBC connection using Oracle’s JDBC drivers can set the end user identifier using the method OracleConnection.setClientIdentifier(userId), and an SQL query can use the function sys context(’USERENV’, ’CLIENT IDENTIFIER’) to retrieve the user identifier. 6Database systems are designed to manage large relations, but manage schema information such as views in a way that assumes smaller data volumes so as to enhance overall performance.  

**9.7 Application Security 409**

Verifying that all application programs make all required authorization checks involves reading through all the application-server code, a formidable task in a large system. In other words, applications have a very large “surface area,” making the task of protecting the application significantly harder. And in fact, security loopholes have been found in a variety of real-life applications.

In contrast, if a database directly supported fine-grained authorization, au- thorization policies could be specified and enforced at the SQL-level, which has a much smaller surface area. Even if some of the application interfaces inad- vertently omit required authorization checks, the SQL-level authorization could prevent unauthorized actions from being executed.

Some database systems provide mechanisms for fine-grained authorization. For example, the Oracle **Virtual Private Database** (**VPD**) allows a system admin- istrator to associate a function with a relation; the function returns a predicate that must be added to any query that uses the relation (different functions can be defined for relations that are being updated). For example, using our syntax for retrieving application user identifiers, the function for the _takes_ relation can return a predicate such as:

_ID_ \= _sys context.user id_()

This predicate is added to the **where** clause of every query that uses the _takes_ relation. As a result (assuming that the application program sets the _user id_ value to the student’s _ID_), each student can see only the tuples corresponding to courses that she took.

Thus, VPD provides authorization at the level of specific tuples, or rows, of a relation, and is therefore said to be a _row-level authorization_ mechanism. A potential pitfall with adding a predicate as described above is that it may change the meaning of a query significantly. For example, if a user wrote a query to find the average grade over all courses, she would end up getting the average of _her_ grades, not all grades. Although the system would give the “right” answer for the rewritten query, that answer would not correspond to the query the user may have thought she was submitting.

See the bibliographic notes for pointers to more information on Oracle VPD.

**9.7.6 Audit Trails**

An **audit trail** is a log of all changes (inserts, deletes, and updates) to the appli- cation data, along with information such as which user performed the change and when the change was performed. If application security is breached, or even if security was not breached, but some update was carried out erroneously, an audit trail can (a) help find out what happened, and who may have carried out the actions, and (b) aid in fixing the damage caused by the security breach or erroneous update.

For example, if a student’s grade is found to be incorrect, the audit log can be examined to locate when and how the grade was updated, as well as to find which user carried out the updates. The university could then also use the audit  

**410 Chapter 9 Application Design and Development**

trail to trace all the updates performed by this user, in order to find other incorrect or fraudulent updates, and then correct them.

Audit trails can also be used to detect security breaches where a user’s account is compromised and accessed by an intruder. For example, each time a user logs in, she may be informed about all updates in the audit trail that were done from that login in the recent past; if the user sees a update that she did not carry out, it is likely the account has been compromised.

It is possible to create a database-level audit trail by defining appropriate triggers on relation updates (using system-defined variables that identify the user name and time). However, many database systems provide built-in mechanisms to create audit trails that are much more convenient to use. Details of how to create audit trails vary across database systems, and you should refer to the database-system manuals for details.

Database-level audit trails are usually insufficient for applications, since they are usually unable to track who was the end user of the application. Further, updates are recorded at a low level, in terms of updates to tuples of a relation, rather than at a higher level, in terms of the business logic. Applications therefore usually create a higher-level audit trail, recording, for example, what action was carried out, by whom, when, and from which IP address the request originated.

A related issue is that of protecting the audit trail itself from being modified or deleted by users who breach application security. One possible solution is to copy the audit trail to a different machine, to which the intruder would not have access, with each record in the trail copied as soon as it is generated.

**9.7.7 Privacy**

In a world where an increasing amount of personal data are available online, people are increasingly worried about the privacy of their data. For example, most people would want their personal medical data to be kept private and not revealed publicly. However, the medical data must be made available to doctors and emergency medical technicians who treat the patient. Many countries have laws on privacy of such data that define when and to whom the data may be revealed. Violation of privacy law can result in criminal penalties in some countries. Applications that access such private data must be built carefully, keeping the privacy laws in mind.

On the other hand, aggregated private data can play an important role in many tasks such as detecting drug side effects, or in detecting the spread of epidemics. How to make such data available to researchers carrying out such tasks, without compromising the privacy of individuals, is an important real- world problem. As an example, suppose a hospital hides the name of the patient, but provides a researcher with the date of birth and the zip code (postal code) of the patient (both of which may be useful to the researcher). Just these two pieces of information can be used to uniquely identify the patient in many cases (using information from an external database), compromising his privacy. In this particular situation, one solution would be to give the year of birth but not the  

**9.8 Encryption and Its Applications 411**

date of birth, along with the zip code, which may suffice for the researcher. This would not provide enough information to uniquely identify most individuals.7

As another example, Web sites often collect personal data such as address, telephone, email, and credit-card information. Such information may be required to carry out a transaction such as purchasing an item from a store. However, the customer may not want the information to be made available to other organiza- tions, or may want part of the information (such as credit-card numbers) to be erased after some period of time as a way to prevent it from falling into unautho- rized hands in the event of a security breach. Many Web sites allow customers to specify their privacy preferences, and must then ensure that these preferences are respected.

**9.8 Encryption and Its Applications**

Encryption refers to the process of transforming data into a form that is unread- able, unless the reverse process of decryption is applied. Encryption algorithms use an encryption key to perform encryption, and require a decryption key (which could be the same as the encryption key depending on the encryption algorithm used) to perform decryption.

The oldest uses of encryption were for transmitting messages, encrypted using a secret key known only to the sender and the intended receiver. Even if the message is intercepted by an enemy, the enemy, not knowing the key, will not be able to decrypt and understand the message. Encryption is widely used today for protecting data in transit in a variety of applications such as data transfer on the Internet, and on cellular phone networks. Encryption is also used to carry out other tasks, such as authentication, as we will see in Section 9.8.3.

In the context of databases, encryption is used to store data in a secure way, so that even if the data is acquired by an unauthorized user (for example, a laptop computer containing the data is stolen), the data will not be accessible without a decryption key.

Many databases today store sensitive customer information, such as credit- card numbers, names, fingerprints, signatures, and identification numbers such as, in the United States, social-security numbers. A criminal who gets access to such data can use it for a variety of illegal activities such as purchasing goods using a credit-card number, or even acquiring a credit card in someone else’s name. Organizations such as credit-card companies use knowledge of personal information as a way of identifying who is requesting a service or goods. Leakage of such personal information allows a criminal to impersonate someone else and get access to service or goods; such impersonation is referred to as **identity theft**. Thus, applications that store such sensitive data must take great care to protect them from theft.

7For extremely old people, who are relatively rare, even the year of birth plus postal code may be enough to uniquely identify the individual, so a range of values, such as 80 years or older, may be provided instead of the actual age for people older than 80 years.  

**412 Chapter 9 Application Design and Development**

To reduce the chance of sensitive information being acquired by criminals, many countries and states today require by law that any database storing such sensitive information must store the information in an encrypted form. A business that does not protect its data thus could be held criminally liable in case of data theft. Thus, encryption is a critical component of any application that stores such sensitive information.

**9.8.1 Encryption Techniques**

There are a vast number of techniques for the encryption of data. Simple encryp- tion techniques may not provide adequate security, since it may be easy for an unauthorized user to break the code. As an example of a weak encryption tech- nique, consider the substitution of each character with the next character in the alphabet. Thus,

Perryridge

becomes

Qfsszsjehf

If an unauthorized user sees only “Qfsszsjehf,” she probably has insufficient information to break the code. However, if the intruder sees a large number of encrypted branch names, she could use statistical data regarding the relative frequency of characters to guess what substitution is being made (for example, _E_ is the most common letter in English text, followed by _T, A, O, N, I_, and so on).

A good encryption technique has the following properties:

• It is relatively simple for authorized users to encrypt and decrypt data.

• It depends not on the secrecy of the algorithm, but rather on a parameter of the algorithm called the _encryption key_, which is used to encrypt data. In a **symmetric-key** encryption technique, the encryption key is also used to decrypt data. In contrast, in **public-key** (also known as **asymmetric-key**) encryption techniques, there are two different keys, the public key and the private key, used to encrypt and decrypt the data.

• Its decryption key is extremely difficult for an intruder to determine, even if the intruder has access to encrypted data. In the case of asymmetric-key encryption, it is extremely difficult to infer the private key even if the public key is available.

The **Advanced Encryption Standard** (AES) is a symmetric-key encryption algorithm that was adopted as an encryption standard by the U.S. government in 2000, and is now widely used. The standard is based on the **Rijndael algorithm** (named for the inventors V. Rijmen and J. Daemen). The algorithm operates on a 128-bit block of data at a time, while the key can be 128, 192, or 256 bits in length.  

**9.8 Encryption and Its Applications 413**

The algorithm runs a series of steps to jumble up the bits in a data block in a way that can be reversed during decryption, and performs an XOR operation with a 128-bit “round key” that is derived from the encryption key. A new round key is generated from the encryption key for each block of data that is encrypted. During decryption, the round keys are generated again from the encryption key and the encryption process is reversed to recover the original data. An earlier standard called the _Data Encryption Standard_ (DES), adopted in 1977, was very widely used earlier.

For any symmetric-key encryption scheme to work, authorized users must be provided with the encryption key via a secure mechanism. This requirement is a major weakness, since the scheme is no more secure than the security of the mechanism by which the encryption key is transmitted.

**Public-key encryption** is an alternative scheme that avoids some of the prob- lems faced by symmetric-key encryption techniques. It is based on two keys: a _public key_ and a _private key_. Each user _Ui_ has a public key _Ei_ and a private key _Di_ . All public keys are published: They can be seen by anyone. Each private key is known to only the one user to whom the key belongs. If user _U_1 wants to store encrypted data, _U_1 encrypts them using public key _E_1\. Decryption requires the private key _D_1.

Because the encryption key for each user is public, it is possible to exchange information securely by this scheme. If user _U_1 wants to share data with _U_2, _U_1 encrypts the data using _E_2, the public key of _U_2\. Since only user _U_2 knows how to decrypt the data, information can be transferred securely.

For public-key encryption to work, there must be a scheme for encryption such that it is infeasible (that is, extremely hard) to deduce the private key, given the public key. Such a scheme does exist and is based on these conditions:

• There is an efficient algorithm for testing whether or not a number is prime.

• No efficient algorithm is known for finding the prime factors of a number.

For purposes of this scheme, data are treated as a collection of integers. We create a public key by computing the product of two large prime numbers: _P_1 and _P_2\. The private key consists of the pair (_P_1_, P_2). The decryption algorithm cannot be used successfully if only the product _P_1 _P_2 is known; it needs the individual values _P_1 and _P_2\. Since all that is published is the product _P_1 _P_2, an unauthorized user would need to be able to factor _P_1 _P_2 to steal data. By choosing _P_1 and _P_2 to be sufficiently large (over 100 digits), we can make the cost of factoring _P_1 _P_2 prohibitively high (on the order of years of computation time, on even the fastest computers).

The details of public-key encryption and the mathematical justification of this technique’s properties are referenced in the bibliographic notes.

Although public-key encryption by this scheme is secure, it is also computa- tionally very expensive. A hybrid scheme widely used for secure communication is as follows: a symmetric encryption key (based, for example, on AES) is ran-  

**414 Chapter 9 Application Design and Development**

domly generated and exchanged in a secure manner using a public-key encryp- tion scheme, and symmetric-key encryption using that key is used on the data transmitted subsequently.

Encryption of small values, such as identifiers or names, is made compli- cated by the possibility of **dictionary attacks**, particularly if the encryption key is publicly available. For example, if date-of-birth fields are encrypted, an attacker trying to decrypt a particular encrypted value _e_ could try encrypting every possi- ble date of birth until he finds one whose encrypted value matches _e_. Even if the encryption key is not publicly available, statistical information about data distri- butions can be used to figure out what an encrypted value represents in some cases, such as age or zip code. For example, if the age 18 is the most common age in a database, the encrypted age value that occurs most often can be inferred to represent 18.

Dictionary attacks can be deterred by adding extra random bits to the end of the value before encryption (and removing them after decryption). Such extra bits, referred to as an **initialization vector** in AES, or as _salt_ bits in other contexts, provide good protection against dictionary attack.

**9.8.2 Encryption Support in Databases**

Many file systems and database systems today support encryption of data. Such encryption protects the data from someone who is able to access the data, but is not able to access the decryption key. In the case of file-system encryption, the data to be encrypted are usually large files and directories containing information about files.

In the context of databases, encryption can be done at several different levels. At the lowest level, the disk blocks containing database data can be encrypted, using a key available to the database-system software. When a block is retrieved from disk, it is first decrypted and then used in the usual fashion. Such disk-block- level encryption protects against attackers who can access the disk contents but do not have access to the encryption key.

At the next higher level, specified (or all) attributes of a relation can be stored in encrypted form. In this case, each attribute of a relation could have a different encryption key. Many databases today support encryption at the level of specified attributes as well as at the level of an entire relation, or all relations in a database. Encryption of specified attributes minimizes the overhead of decryption, by al- lowing applications to encrypt only attributes that contain sensitive values such as credit-card numbers. However, when individual attributes or relations are en- crypted, databases typically do not allow primary and foreign key attributes to be encrypted, and do not support indexing on encrypted attributes. Encryption also then needs to use extra random bits to prevent dictionary attacks, as described earlier.

A decryption key is obviously required to get access to encrypted data. A single master encryption key may be used for all the encrypted data; with attribute level encryption, different encryption keys could be used for different attributes.  

**9.8 Encryption and Its Applications 415**

In this case, the decryption keys for different attributes can be stored in a file or relation (often referred to as “wallet”), which is itself encrypted using a master key.

A connection to the database that needs to access encrypted attributes must then provide the master key; unless this is provided, the connection will not be able to access encrypted data. The master key would be stored in the application program (typically on a different computer), or memorized by the database user, and provided when the user connects to the database.

Encryption at the database level has the advantage of requiring relatively low time and space overhead, and does not require modification of applications. For example, if data in a laptop computer database need to be protected from theft of the computer itself, such encryption can be used. Similarly, someone who gets access to backup tapes of a database would not be able to access the data contained in the backups without knowing the decryption key.

An alternative to performing encryption in the database is to perform it _before_ the data are sent to the database. The application must then encrypt the data before sending it to the database, and decrypt the data when it is retrieved. This approach to data encryption requires significant modifications to be done to the application, unlike encryption performed in a database system.

**9.8.3 Encryption and Authentication**

Password-based authentication is used widely by operating systems as well as databases. However, the use of passwords has some drawbacks, especially over a network. If an eavesdropper is able to “sniff” the data being sent over the network, she may be able to find the password as it is being sent across the network. Once the eavesdropper has a user name and password, she can connect to the database, pretending to be the legitimate user.

A more secure scheme involves a **challenge–response** system. The database system sends a challenge string to the user. The user encrypts the challenge string using a secret password as encryption key and then returns the result. The database system can verify the authenticity of the user by decrypting the string with the same secret password and checking the result with the original challenge string. This scheme ensures that no passwords travel across the network.

Public-key systems can be used for encryption in challenge–response sys- tems. The database system encrypts a challenge string using the user’s public key and sends it to the user. The user decrypts the string using her private key, and returns the result to the database system. The database system then checks the response. This scheme has the added benefit of not storing the secret password in the database, where it could potentially be seen by system administrators.

Storing the private key of a user on a computer (even a personal computer) has the risk that if the computer is compromised, the key may be revealed to an attacker who can then masquerade as the user. **Smart cards** provide a solution to this problem. In a smart card, the key can be stored on an embedded chip; the operating system of the smart card guarantees that the key can never be read, but  

**416 Chapter 9 Application Design and Development**

allows data to be sent to the card for encryption or decryption, using the private key.8

**9.8.3.1 Digital Signatures**

Another interesting application of public-key encryption is in **digital signatures** to verify authenticity of data; digital signatures play the electronic role of physical signatures on documents. The private key is used to “sign,” that is, encrypt, data, and the signed data can be made public. Anyone can verify the signature by decrypting the data using the public key, but no one could have generated the signed data without having the private key. (Note the reversal of the roles of the public and private keys in this scheme.) Thus, we can **authenticate** the data; that is, we can verify that the data were indeed created by the person who is supposed to have created them.

Furthermore, digital signatures also serve to ensure **nonrepudiation**. That is, in case the person who created the data later claims she did not create it (the electronic equivalent of claiming not to have signed the check), we can prove that that person must have created the data (unless her private key was leaked to others).

**9.8.3.2 Digital Certificates**

Authentication is, in general, a two-way process, where each of a pair of inter- acting entities authenticates itself to the other. Such pairwise authentication is needed even when a client contacts a Web site, to prevent a malicious site from masquerading as a legal Web site. Such masquerading could be done, for exam- ple, if the network routers were compromised, and data rerouted to the malicious site.

For a user to ensure that she is interacting with an authentic Web site, she must have the site’s public key. This raises the problem of how the user can get the public key–if it is stored on the Web site, the malicious site could supply a different key, and the user would have no way of verifying if the supplied public key is itself authentic. Authentication can be handled by a system of **digital certificates,** whereby public keys are signed by a certification agency, whose public key is well known. For example, the public keys of the root certification authorities are stored in standard Web browsers. A certificate issued by them can be verified by using the stored public keys.

A two-level system would place an excessive burden of creating certificates on the root certification authorities, so a multilevel system is used instead, with one or more root certification authorities and a tree of certification authorities below each root. Each authority (other than the root authorities) has a digital certificate issued by its parent.

A digital certificate issued by a certification authority _A_ consists of a public key _K A_ and an encrypted text _E_ that can be decoded by using the public key

8Smart cards provide other functionality too, such as the ability to store cash digitally and make payments, which is not relevant in our context.  

**9.9 Summary 417**

_K A_. The encrypted text contains the name of the party to whom the certificate was issued and her public key _Kc_ . In case the certification authority _A_ is not a root certification authority, the encrypted text also contains the digital certificate issued to _A_ by its parent certification authority; this certificate authenticates the key _K A_ itself. (That certificate may in turn contain a certificate from a further parent authority, and so on.)

To verify a certificate, the encrypted text _E_ is decrypted by using the public key _K A_ to retrieve the name of the party (that is, the name of the organization owning the Web site); additionally, if _A_ is not a root authority whose public key is known to the verifier, the public key _K A_ is verified recursively by using the digital certificate contained within _E_ ; recursion terminates when a certificate issued by the root authority is reached. Verifying the certificate establishes the chain through which a particular site was authenticated, and provides the name and authenticated public key for the site.

Digital certificates are widely used to authenticate Web sites to users, to prevent malicious sites from masquerading as other Web sites. In the HTTPS protocol (the secure version of the HTTP protocol), the site provides its digital certificate to the browser, which then displays it to the user. If the user accepts the certificate, the browser then uses the provided public key to encrypt data. A malicious site will have access to the certificate, but not the private key, and will thus not be able to decrypt the data sent by the browser. Only the authentic site, which has the corresponding private key, can decrypt the data sent by the browser. We note that public-/private-key encryption and decryption costs are much higher than encryption/decryption costs using symmetric private keys. To reduce encryption costs, HTTPS actually creates a one-time symmetric key after authentication, and uses it to encrypt data for the rest of the session.

Digital certificates can also be used for authenticating users. The user must submit a digital certificate containing her public key to a site, which verifies that the certificate has been signed by a trusted authority. The user’s public key can then be used in a challenge–response system to ensure that the user possesses the corresponding private key, thereby authenticating the user.

**9.9 Summary**

• Application programs that use databases as back ends and interact with users have been around since the 1960s. Application architectures have evolved over this period. Today most applications use Web browsers as their front end, and a database as their back end, with an application server in between.

• HTML provides the ability to define interfaces that combine hyperlinks with forms facilities. Web browsers communicate with Web servers by the HTTP protocol. Web servers can pass on requests to application programs, and return the results to the browser.

• Web servers execute application programs to implement desired function- ality. Servlets are a widely used mechanism to write application programs  

**418 Chapter 9 Application Design and Development**

that run as part of the Web server process, in order to reduce overhead. There are also many server-side scripting languages that are interpreted by the Web server and provide application-program functionality as part of the Web server.

• There are several client-side scripting languages—JavaScript is the most widely used—that provide richer user interaction at the browser end.

• Complex applications usually have a multilayer architecture, including a model implementing business logic, a controller, and a view mechanism to display results. They may also include a data access layer that implements an object-relational mapping. Many applications implement and use Web services, allowing functions to be invoked over HTTP.

• A number of tools have been developed for rapid application development, and in particular to reduce the effort required to build user interfaces.

• Techniques such as caching of various forms, including query result caching and connection pooling, and parallel processing are used to improve appli- cation performance.

• Application developers must pay careful attention to security, to prevent attacks such as SQL injection attacks and cross-site scripting attacks.

• SQL authorization mechanisms are coarse grained and of limited value to ap- plications that deal with large numbers of users. Today application programs implement fine-grained, tuple-level authorization, dealing with a large num- ber of application users, completely outside the database system. Database extensions to provide tuple-level access control and to deal with large num- bers of application users have been developed, but are not standard as yet.

• Protecting the privacy of data is an important task for database applications. Many countries have legal requirements on protection of certain kinds of data, such as credit-card information or medical data.

• Encryption plays a key role in protecting information and in authentication of users and Web sites. Symmetric-key encryption and public-key encryption are two contrasting but widely used approaches to encryption. Encryption of certain sensitive data stored in databases is a legal requirement in many countries and states.

• Encryption also plays a key role in authentication of users to applications, of Web sites to users, and for digital signatures.

**Review Terms**

• Application programs • Web interfaces to databases

• HyperText Markup Language (HTML)  

**Practice Exercises 419**

• Hyperlinks • Uniform resource locator (URL) • Forms • HyperText Transfer Protocol

(HTTP) • Common Gateway Interface

(CGI) • Connectionless protocols • Cookie • Session • Servlets and Servlet sessions • Server-side scripting • JSP

• PHP

• ASP.NET

• Client-side scripting • JavaScript • Document Object Model (DOM) • Applets • Application architecture • Presentation layer • Model-view-controller (MVC)

architecture • Business-logic layer • Data-access layer • Object-relational mapping • Hibernate

• Web services • RESTful services • Rapid application development • Web application frameworks • Report generators • Connection pooling • Query result caching • Application security • SQL injection • Cross-site scripting (XSS) • Cross-site request forgery (XSRF) • Authentication • Two-factor authentication • Man-in-the-middle attack • Central authentication • Single sign-on • OpenID • Virtual Private Database (VPD) • Audit trail • Encryption • Symmetric-key encryption • Public-key encryption • Dictionary attack • Challenge–response • Digital signatures • Digital certificates

**Practice Exercises**

**9.1** What is the main reason why servlets give better performance than pro- grams that use the common gateway interface (CGI), even though Java programs generally run slower than C or C++ programs?

**9.2** List some benefits and drawbacks of connectionless protocols over proto- cols that maintain connections.

**9.3** Consider a carelessly written Web application for an online-shopping site, which stores the price of each item as a hidden form variable in the Web page sent to the customer; when the customer submits the form, the in-  

**420 Chapter 9 Application Design and Development**

formation from the hidden form variable is used to compute the bill for the customer. What is the loophole in this scheme? (There was a real in- stance where the loophole was exploited by some customers of an online- shopping site, before the problem was detected and fixed.)

**9.4** Consider another carelessly written Web application, which uses a servlet that checks if there was an active session, but does not check if the user is authorized to access that page, instead depending on the fact that a link to the page is shown only to authorized users. What is the risk with this scheme? (There was a real instance where applicants to a college admissions site could, after logging into the Web site, exploit this loophole and view information they were not authorized to see; the unauthorized access was however detected, and those who accessed the information were punished by being denied admission.)

**9.5** List three ways in which caching can be used to speed up Web server performance.

**9.6** The netstat command (available on Linux and on Windows) shows the active network connections on a computer. Explain how this command can be used to find out if a particular Web page is not closing connections that it opened, or if connection pooling is used, not returning connections to the connection pool. You should account for the fact that with connection pooling, the connection may not get closed immediately.

**9.7** Testing for SQL-injection vulnerability:

a. Suggest an approach for testing an application to find if it is vulner- able to SQL injection attacks on text input.

b. Can SQL injection occur with other forms of input? If so, how would you test for vulnerability?

**9.8** A database relation may have the values of certain attributes encrypted for security. Why do database systems not support indexing on encrypted attributes? Using your answer to this question, explain why database sys- tems do not allow encryption of primary-key attributes.

**9.9** Exercise 9.8 addresses the problem of encryption of certain attributes. How- ever, some database systems support encryption of entire databases. Ex- plain how the problems raised in Exercise 9.8 are avoided when the entire database is encrypted.

**9.10** Suppose someone impersonates a company and gets a certificate from a certificate-issuing authority. What is the effect on things (such as purchase orders or programs) certified by the impersonated company, and on things certified by other companies?

**9.11** Perhaps the most important data items in any database system are the passwords that control access to the database. Suggest a scheme for the secure storage of passwords. Be sure that your scheme allows the system  

**Exercises 421**

to test passwords supplied by users who are attempting to log into the system.

**Exercises**

**9.12** Write a servlet and associated HTML code for the following very simple application: A user is allowed to submit a form containing a value, say _n_, and should get a response containing _n_ “\*” symbols.

**9.13** Write a servlet and associated HTML code for the following simple appli- cation: A user is allowed to submit a form containing a number, say _n_, and should get a response saying how many times the value _n_ has been submitted previously. The number of times each value has been submitted previously should be stored in a database.

**9.14** Write a servlet that authenticates a user (based on user names and pass- words stored in a database relation), and sets a session variable called _userid_ after authentication.

**9.15** What is an SQL injection attack? Explain how it works, and what precau- tions must be taken to prevent SQL injection attacks.

**9.16** Write pseudocode to manage a connection pool. Your pseudocode must include a function to create a pool (providing a database connection string, database user name, and password as parameters), a function to request a connection from the pool, a connection to release a connection to the pool, and a function to close the connection pool.

**9.17** Explain the terms CRUD and REST.

**9.18** Many Web sites today provide rich user-interfaces using Ajax. List two features each of which reveals if a site uses Ajax, without having to look at the source code. Using the above features, find three sites which use Ajax; you can view the HTML source of the page to check if the site is actually using Ajax.

**9.19** XSS attacks:

a. What is an XSS attack?

b. How can the referer field be used to detect some XSS attacks?

**9.20** What is multi-factor authentication? How does it help safeguard against stolen passwords?

**9.21** Consider the Oracle **Virtual Private Database** (**VPD**) feature described in Section 9.7.5, and an application based on our university schema.

a. What predicate (using a subquery) should be generated to allow each faculty member to see only _takes_ tuples corresponding to course sections that they have taught?  

**422 Chapter 9 Application Design and Development**

b. Give an SQL query such that the query with the predicate added gives a result that is a subset of the original query result without the added predicate.

c. Give an SQL query such that the query with the predicate added gives a result containing a tuple that is not in the result of the original query without the added predicate.

**9.22** What are two advantages of encrypting data stored in the database?

**9.23** Suppose you wish to create an audit trail of changes to the _takes_ relation.

a. Define triggers to create an audit trail, logging the information into a relation called, for example, _takes trail_. The logged information should include the user-id (assume a function _user id_() provides this informa- tion) and a timestamp, in addition to old and new values. You must also provide the schema of the _takes trail_ relation.

b. Can the above implementation guarantee that updates made by a malicious database administrator (or someone who manages to get the administrator’s password) will be in the audit trail? Explain your answer.

**9.24** Hackers may be able to fool you into believing that their Web site is actually a Web site (such as a bank or credit card Web site) that you trust. This may be done by misleading email, or even by breaking into the network infrastructure and rerouting network traffic destined for, say mybank.com, to the hacker’s site. If you enter your user name and password on the hacker’s site, the site can record it, and use it later to break into your account at the real site. When you use a URL such as https://mybank.com, the HTTPS protocol is used to prevent such attacks. Explain how the protocol might use digital certificates to verify authenticity of the site.

**9.25** Explain what is a challenge–response system for authentication. Why is it more secure than a traditional password-based system?

**Project Suggestions**

Each of the following is a large project, which can be a semester-long project done by a group of students. The difficulty of the project can be adjusted easily by adding or deleting features.

**Project 9.1** Pick your favorite interactive Web site, such as Bebo, Blogger, Face- book, Flickr, Last.FM, Twitter, Wikipedia; these are just a few examples, there are many more. Most of these sites manage a large amount of data, and use databases to store and process the data. Implement a subset of the functionality of the Web site you picked. Clearly, implementing even a sig- nificant subset of the features of such a site is well beyond a course project,  

**Project Suggestions 423**

but it is possible to find a set of features that is interesting to implement, yet small enough for a course project.

Most of today’s popular Web sites make extensive use of Javascript to create rich interfaces. You may wish to go easy on this for your project, at least initially, since it takes time to build such intefaces, and then add more features to your interfaces, as time permits. Make use of Web application development frameworks, or Javascript libraries available on the Web, such as the Yahoo User Interface library, to speed up your development.

**Project 9.2** Create a “mashup” which uses Web services such as Google or Ya- hoo maps APIs to create an interactive Web sites. For example, the map APIs provide a way to display a map on the Web page, with other information overlayed on the maps. You could implement a restaurant recommenda- tion system, with users contributing information about restaurants such as location, cuisine, price range, and ratings. Results of user searches could be displayed on the map. You could allow Wikipedia-like features, such as allowing users to add information and edit information added by other users, along with moderators who can weed out malicious updates. You could also implement social features, such as giving more importance to ratings provided by your friends.

**Project 9.3** Your university probably uses a course-management systems such as Moodle, Blackboard, or WebCT. Implement a subset of the functionality of such a course-management system. For example, you can provide as- signment submission and grading functionality, including mechanisms for students and teachers/teaching-assistants to discuss grading of a particular assignment. You could also provide polls and other mechanisms for getting feedback.

**Project 9.4** Consider the E-R schema of Practice Exercise 7.3 (Chapter 7), which represents information about teams in a league. Design and implement a Web-based system to enter, update, and view the data.

**Project 9.5** Design and implement a shopping cart system that lets shoppers collect items into a shopping cart (you can decide what information is to be supplied for each item) and purchased together. You can extend and use the E-R schema of Exercise 7.20 of Chapter 7. You should check for availability of the item and deal with nonavailable items as you feel appropriate.

**Project 9.6** Design and implement a Web-based system to record student regis- tration and grade information for courses at a university.

**Project 9.7** Design and implement a system that permits recording of course performance information—specifically, the marks given to each student in each assignment or exam of a course, and computation of a (weighted) sum of marks to get the total course marks. The number of assignments/exams should not be predefined; that is, more assignments/exams can be added at any time. The system should also support grading, permitting cutoffs to be specified for various grades.  

**424 Chapter 9 Application Design and Development**

You may also wish to integrate it with the student registration system of Project 9.6 (perhaps being implemented by another project team).

**Project 9.8** Design and implement a Web-based system for booking classrooms at your university. Periodic booking (fixed days/times each week for a whole semester) must be supported. Cancellation of specific lectures in a periodic booking should also be supported.

You may also wish to integrate it with the student registration system of Project 9.6 (perhaps being implemented by another project team) so that classrooms can be booked for courses, and cancellations of a lecture or addition of extra lectures can be noted at a single interface, and will be reflected in the classroom booking and communicated to students via email.

**Project 9.9** Design and implement a system for managing online multiple-choice tests. You should support distributed contribution of questions (by teaching assistants, for example), editing of questions by whoever is in charge of the course, and creation of tests from the available set of questions. You should also be able to administer tests online, either at a fixed time for all students, or at any time but with a time limit from start to finish (support one or both), and give students feedback on their scores at the end of the allotted time.

**Project 9.10** Design and implement a system for managing email customer ser- vice. Incoming mail goes to a common pool. There is a set of customer service agents who reply to email. If the email is part of an ongoing se- ries of replies (tracked using the in-reply-to field of email) the mail should preferably be replied to by the same agent who replied earlier. The system should track all incoming mail and replies, so an agent can see the history of questions from a customer before replying to an email.

**Project 9.11** Design and implement a simple electronic marketplace where items can be listed for sale or for purchase under various categories (which should form a hierarchy). You may also wish to support alerting services, whereby a user can register interest in items in a particular category, perhaps with other constraints as well, without publicly advertising her interest, and is notified when such an item is listed for sale.

**Project 9.12** Design and implement a Web-based newsgroup system. Users should be able to subscribe to newsgroups, and browse articles in newsgroups. The system tracks which articles were read by a user, so they are not displayed again. Also provide search for old articles. You may also wish to provide a rating service for articles, so that articles with high rating are highlighted, permitting the busy reader to skip low-rated articles.

**Project 9.13** Design and implement a Web-based system for managing a sports “ladder.” Many people register, and may be given some initial rankings (perhaps based on past performance). Anyone can challenge anyone else to a match, and the rankings are adjusted according to the result. One simple system for adjusting rankings just moves the winner ahead of the loser in  

**Project Suggestions 425**

the rank order, in case the winner was behind earlier. You can try to invent more complicated rank-adjustment systems.

**Project 9.14** Design and implement a publication-listing service. The service should permit entering of information about publications, such as title, authors, year, where the publication appeared, and pages. Authors should be a separate entity with attributes such as name, institution, department, email, address, and home page.

Your application should support multiple views on the same data. For instance, you should provide all publications by a given author (sorted by year, for example), or all publications by authors from a given institution or department. You should also support search by keywords, on the overall database as well as within each of the views.

**Project 9.15** A common task in any organization is to collect structured infor- mation from a group of people. For example, a manager may need to ask employees to enter their vacation plans, a professor may wish to collect feedback on a particular topic from students, or a student organizing an event may wish to allow other students to register for the event, or some- one may wish to conduct an online vote on some topic.

Create a system that will allow users to easily create information collection events. When creating an event, the event creator must define who is eligible to participate; to do so, your system must maintain user information, and allow predicates defining a subset of users. The event creator should be able to specify a set of inputs (with types, default values, and validation checks) that the users will have to provide. The event should have an associated deadline, and the system should have the ability to send reminders to users who have not yet submitted their information. The event creator may be given the option of automatic enforcement of the deadline based on a specified date/time, or choosing to login and declare the deadline is over. Statistics about the submissions should be generated—to do so, the event creator may be allowed to create simple summaries on the entered information. The event creator may choose to make some of the summaries public, viewable by all users, either continually (e.g., how many people have responded) or after the deadline (e.g., what was the average feedback score).

**Project 9.16** Create a library of functions to simplify creation of Web interfaces. You must implement at least the following functions: a function to display a JDBC result set (with tabular formatting), functions to create different types of text and numeric inputs (with validation criteria such as input type and optional range, enforced at the client by appropriate JavaScript code), functions to input date and time values (with default values), and functions to create menu items based on a result set. For extra credit, allow the user to set style parameters such as colors and fonts, and provide pagination support in the tables (hidden form parameters can be used to specify which page is to be displayed). Build a sample database application to illustrate the use of these functions.  

**426 Chapter 9 Application Design and Development**

**Project 9.17** Design and implement a Web-based multiuser calendar system. The system must track appointments for each person, including multioccurrence events, such as weekly meetings, and shared events (where an update made by the event creator gets reflected to all those who share the event). Provide interfaces to schedule multiuser events, where an event creator can add a number of users who are invited to the event. Provide email notification of events. For extra credits implement a Web service that can be used by a reminder program running on the client machine.

**Tools**

Development of a Web application requires several software tools such as an application server, a compiler, and an editor for a programming language such as Java or C#, and other optional tools such as a Web server. There are several integrated development environments that provide support for Web application development. The two most popular open-source IDEs are Eclipse, developed by IBM, and Netbeans, developed by Sun Microsystems. Microsoft’s Visual Studio is the most widely used IDE in the Windows world.

The Apache Tomcat (jakarta.apache.org), Glassfish (glassfish.dev.java.net), JBoss (jboss.org), and Caucho’s Resin (www.caucho.com), are application servers that sup- port servlets and JSP. The Apache Web server (apache.org) is the most widely used Web server today. Microsoft’s IIS (Internet Information Services) is a Web and ap- plication server that is widely used on Microsoft Windows platforms, supporting Microsoft’s ASP.NET (msdn.microsoft.com/asp.net/).

IBM’s WebSphere (www.software.ibm.com) software provides a variety of soft- ware tools for Web application development and deployment, including an ap- plication server, an IDE, application integration middleware, business process management software and system management tools.

Some of the above tools are open-source software that can be used free of cost, some are free for noncommercial use or for personal use, while others need to be paid for. See the respective Web sites for more information.

The Yahoo! User Interface (YUI) JavaScript library (developer.yahoo.com/yui) is widely used for creating JavaScript programs that work across multiple browsers.

**Bibliographical Notes**

Information about servlets, including tutorials, standard specifications, and soft- ware, is available on java.sun.com/products/servlet. Information about JSP is available at java.sun.com/products/jsp. Information on JSP tag libraries can also be found at this URL. Information about the .NET framework and about Web application de- velopment using ASP.NET can be found at msdn.microsoft.com.

Atreya et al. [2002] provide textbook coverage of digital signatures, including X.509 digital certificates and public-key infrastructure.