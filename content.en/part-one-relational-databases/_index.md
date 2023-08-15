---
title: 'PART ONE: RELATIONAL DATABASES'
weight: 6
---

  

**PART 1**

**RELATIONAL DATABASES**

A data model is a collection of conceptual tools for describing data, data relation- ships, data semantics, and consistency constraints. In this part, we focus on the relational model.

The relational model, which is covered in Chapter 2, uses a collection of tables to represent both data and the relationships among those data. Its conceptual simplicity has led to its widespread adoption; today a vast majority of database products are based on the relational model. The relational model describes data at the logical and view levels, abstracting away low-level details of data storage. The entity-relationship model, discussed later in Chapter 7 (in Part 2), is a higher-level data model which is widely used for database design.

To make data from a relational database available to users, we have to ad- dress several issues. The most important issue is how users specify requests for retrieving and updating data; several query languages have been developed for this task. A second, but still important, issue is data integrity and protection; databases need to protect data from damage by user actions, whether uninten- tional or intentional.

Chapters 3, 4 and 5 cover the SQL language, which is the most widely used query language today. Chapters 3 and 4 provide introductory and intermediate level descriptions of SQL. Chapter 4 also covers integrity constraints which are enforced by the database, and authorization mechanisms, which control what access and update actions can be carried out by a user. Chapter 5 covers more advanced topics, including access to SQL from programming languages, and the use of SQL for data analysis.

Chapter 6 covers three formal query languages, the relational algebra, the tuple relational calculus and the domain relational calculus, which are declarative query languages based on mathematical logic. These formal languages form the basis for SQL, and for two other user-friendly languages, QBE and Datalog, which are described in Appendix B (available online at db-book.com).

**37**  

_This page intentionally left blank_  

**_C H A P T E R_2 Introduction to the Relational Model**

The relational model is today the primary data model for commercial data- processing applications. It attained its primary position because of its simplicity, which eases the job of the programmer, compared to earlier data models such as the network model or the hierarchical model.

In this chapter, we first study the fundamentals of the relational model. A substantial theory exists for relational databases. We study the part of this theory dealing with queries in Chapter 6. In Chapters 7 through 8, we shall examine aspects of database theory that help in the design of relational database schemas, while in Chapters 12 and 13 we discuss aspects of the theory dealing with efficient processing of queries.

**2.1 Structure of Relational Databases**

A relational database consists of a collection of **tables**, each of which is assigned a unique name. For example, consider the _instructor_ table of Figure 2.1, which stores information about instructors. The table has four column headers: _ID_, _name_, _dept name_, and _salary_. Each row of this table records information about an instructor,

consisting of the instructor’s _ID_, _name_, _dept name_, and _salary_. Similarly, the _course_ table of Figure 2.2 stores information about courses, consisting of a _course id_, _title_, _dept name_, and _credits_, for each course. Note that each instructor is identified by the value of the column _ID_, while each course is identified by the value of the column _course id_.

Figure 2.3 shows a third table, _prereq_, which stores the prerequisite courses for each course. The table has two columns, _course id_ and _prereq id_. Each row consists of a pair of course identifiers such that the second course is a prerequisite for the first course.

Thus, a row in the _prereq_ table indicates that two courses are _related_ in the sense that one course is a prerequisite for the other. As another example, we consider the table _instructor_, a row in the table can be thought of as representing

**39**  

**40 Chapter 2 Introduction to the Relational Model**

_ID name dept name salary_

10101 Srinivasan Comp. Sci. 65000 12121 Wu Finance 90000 15151 Mozart Music 40000 22222 Einstein Physics 95000 32343 El Said History 60000 33456 Gold Physics 87000 45565 Katz Comp. Sci. 75000 58583 Califieri History 62000 76543 Singh Finance 80000 76766 Crick Biology 72000 83821 Brandt Comp. Sci. 92000 98345 Kim Elec. Eng. 80000

**Figure 2.1** The _instructor_ relation.

the relationship between a specified _ID_ and the corresponding values for _name_, _dept name_, and _salary_ values.

In general, a row in a table represents a _relationship_ among a set of values. Since a table is a collection of such relationships, there is a close correspondence between the concept of _table_ and the mathematical concept of _relation_, from which the relational data model takes its name. In mathematical terminology, a _tuple_ is simply a sequence (or list) of values. A relationship between _n_ values is repre- sented mathematically by an _n-tuple_ of values, i.e., a tuple with _n_ values, which corresponds to a row in a table.

_course id title dept name credits_

BIO-101 Intro. to Biology Biology 4 BIO-301 Genetics Biology 4 BIO-399 Computational Biology Biology 3 CS-101 Intro. to Computer Science Comp. Sci. 4 CS-190 Game Design Comp. Sci. 4 CS-315 Robotics Comp. Sci. 3 CS-319 Image Processing Comp. Sci. 3 CS-347 Database System Concepts Comp. Sci. 3 EE-181 Intro. to Digital Systems Elec. Eng. 3 FIN-201 Investment Banking Finance 3 HIS-351 World History History 3 MU-199 Music Video Production Music 3 PHY-101 Physical Principles Physics 4

**Figure 2.2** The _course_ relation.  

**2.1 Structure of Relational Databases 41**

_course id prereq id_

BIO-301 BIO-101 BIO-399 BIO-101 CS-190 CS-101 CS-315 CS-101 CS-319 CS-101 CS-347 CS-101 EE-181 PHY-101

**Figure 2.3** The _prereq_ relation.

Thus, in the relational model the term **relation** is used to refer to a table, while the term **tuple** is used to refer to a row. Similarly, the term **attribute** refers to a column of a table.

Examining Figure 2.1, we can see that the relation _instructor_ has four attributes: _ID_, _name_, _dept name_, and _salary_.

We use the term **relation instance** to refer to a specific instance of a relation, i.e., containing a specific set of rows. The instance of _instructor_ shown in Figure 2.1 has 12 tuples, corresponding to 12 instructors.

In this chapter, we shall be using a number of different relations to illustrate the various concepts underlying the relational data model. These relations represent part of a university. They do not include all the data an actual university database would contain, in order to simplify our presentation. We shall discuss criteria for the appropriateness of relational structures in great detail in Chapters 7 and 8.

The order in which tuples appear in a relation is irrelevant, since a relation is a _set_ of tuples. Thus, whether the tuples of a relation are listed in sorted order, as in Figure 2.1, or are unsorted, as in Figure 2.4, does not matter; the relations in

_ID name dept name salary_

22222 Einstein Physics 95000 12121 Wu Finance 90000 32343 El Said History 60000 45565 Katz Comp. Sci. 75000 98345 Kim Elec. Eng. 80000 76766 Crick Biology 72000 10101 Srinivasan Comp. Sci. 65000 58583 Califieri History 62000 83821 Brandt Comp. Sci. 92000 15151 Mozart Music 40000 33456 Gold Physics 87000 76543 Singh Finance 80000

**Figure 2.4** Unsorted display of the _instructor_ relation.  

**42 Chapter 2 Introduction to the Relational Model**

the two figures are the same, since both contain the same set of tuples. For ease of exposition, we will mostly show the relations sorted by their first attribute.

For each attribute of a relation, there is a set of permitted values, called the **domain** of that attribute. Thus, the domain of the _salary_ attribute of the _instructor_ relation is the set of all possible salary values, while the domain of the _name_ attribute is the set of all possible instructor names.

We require that, for all relations _r_, the domains of all attributes of _r_ be atomic. A domain is **atomic** if elements of the domain are considered to be indivisible units. For example, suppose the table _instructor_ had an attribute _phone number_, which can store a set of phone numbers corresponding to the instructor. Then the domain of _phone number_ would not be atomic, since an element of the domain is a set of phone numbers, and it has subparts, namely the individual phone numbers in the set.

The important issue is not what the domain itself is, but rather how we use domain elements in our database. Suppose now that the _phone number_ attribute stores a single phone number. Even then, if we split the value from the phone number attribute into a country code, an area code and a local number, we would be treating it as a nonatomic value. If we treat each phone number as a single indivisible unit, then the attribute _phone number_ would have an atomic domain.

In this chapter, as well as in Chapters 3 through 6, we assume that all attributes have atomic domains. In Chapter 22, we shall discuss extensions to the relational data model to permit nonatomic domains.

The **null** value is a special value that signifies that the value is unknown or does not exist. For example, suppose as before that we include the attribute _phone number_ in the _instructor_ relation. It may be that an instructor does not have a

phone number at all, or that the telephone number is unlisted. We would then have to use the null value to signify that the value is unknown or does not exist. We shall see later that null values cause a number of difficulties when we access or update the database, and thus should be eliminated if at all possible. We shall assume null values are absent initially, and in Section 3.6 we describe the effect of nulls on different operations.

**2.2 Database Schema**

When we talk about a database, we must differentiate between the **database schema**, which is the logical design of the database, and the **database instance**, which is a snapshot of the data in the database at a given instant in time.

The concept of a relation corresponds to the programming-language no- tion of a variable, while the concept of a **relation schema** corresponds to the programming-language notion of type definition.

In general, a relation schema consists of a list of attributes and their corre- sponding domains. We shall not be concerned about the precise definition of the domain of each attribute until we discuss the SQL language in Chapter 3.

The concept of a relation instance corresponds to the programming-language notion of a value of a variable. The value of a given variable may change with time;  

**2.2 Database Schema 43**

_dept name building budget_

Biology Watson 90000 Comp. Sci. Taylor 100000 Elec. Eng. Taylor 85000 Finance Painter 120000 History Painter 50000 Music Packard 80000 Physics Watson 70000

**Figure 2.5** The _department_ relation.

similarly the contents of a relation instance may change with time as the relation is updated. In contrast, the schema of a relation does not generally change.

Although it is important to know the difference between a relation schema and a relation instance, we often use the same name, such as _instructor_, to refer to both the schema and the instance. Where required, we explicitly refer to the schema or to the instance, for example “the _instructor_ schema,” or “an instance of the _instructor_ relation.” However, where it is clear whether we mean the schema or the instance, we simply use the relation name.

Consider the _department_ relation of Figure 2.5. The schema for that relation is

_department_ (_dept name_, _building_, _budget_)

Note that the attribute _dept name_ appears in both the _instructor_ schema and the _department_ schema. This duplication is not a coincidence. Rather, using common attributes in relation schemas is one way of relating tuples of distinct relations. For example, suppose we wish to find the information about all the instructors who work in the Watson building. We look first at the _department_ relation to find the _dept name_ of all the departments housed in Watson. Then, for each such department, we look in the _instructor_ relation to find the information about the instructor associated with the corresponding _dept name_.

Let us continue with our university database example. Each course in a university may be offered multiple times, across different

semesters, or even within a semester. We need a relation to describe each individ- ual offering, or section, of the class. The schema is

_section_ (_course id_, _sec id_, _semester_, _year_, _building_, _room number_, _time slot id_)

Figure 2.6 shows a sample instance of the _section_ relation. We need a relation to describe the association between instructors and the

class sections that they teach. The relation schema to describe this association is

_teaches_ (_ID_, _course id_, _sec id_, _semester_, _year_)  

**44 Chapter 2 Introduction to the Relational Model**

_course id sec id semester year building room number time slot id_

BIO-101 1 Summer 2009 Painter 514 B BIO-301 1 Summer 2010 Painter 514 A CS-101 1 Fall 2009 Packard 101 H CS-101 1 Spring 2010 Packard 101 F CS-190 1 Spring 2009 Taylor 3128 E CS-190 2 Spring 2009 Taylor 3128 A CS-315 1 Spring 2010 Watson 120 D CS-319 1 Spring 2010 Watson 100 B CS-319 2 Spring 2010 Taylor 3128 C CS-347 1 Fall 2009 Taylor 3128 A EE-181 1 Spring 2009 Taylor 3128 C FIN-201 1 Spring 2010 Packard 101 B HIS-351 1 Spring 2010 Painter 514 C MU-199 1 Spring 2010 Packard 101 D PHY-101 1 Fall 2009 Watson 100 A

**Figure 2.6** The _section_ relation.

Figure 2.7 shows a sample instance of the _teaches_ relation. As you can imagine, there are many more relations maintained in a real uni-

versity database. In addition to those relations we have listed already, _instructor_, _department_, _course_, _section_, _prereq_, and _teaches_, we use the following relations in this text:

_ID course id sec id semester year_

10101 CS-101 1 Fall 2009 10101 CS-315 1 Spring 2010 10101 CS-347 1 Fall 2009 12121 FIN-201 1 Spring 2010 15151 MU-199 1 Spring 2010 22222 PHY-101 1 Fall 2009 32343 HIS-351 1 Spring 2010 45565 CS-101 1 Spring 2010 45565 CS-319 1 Spring 2010 76766 BIO-101 1 Summer 2009 76766 BIO-301 1 Summer 2010 83821 CS-190 1 Spring 2009 83821 CS-190 2 Spring 2009 83821 CS-319 2 Spring 2010 98345 EE-181 1 Spring 2009

**Figure 2.7** The _teaches_ relation.  

**2.3 Keys 45**

• _student_ (_ID_, _name_, _dept name_, _tot cred_)

• _advisor_ (_s id_, _i id_)

• _takes_ (_ID_, _course id_, _sec id_, _semester_, _year_, _grade_)

• _classroom_ (_building_, _room number_, _capacity_)

• _time slot_ (_time slot id_, _day_, _start time_, _end time_)

**2.3 Keys**

We must have a way to specify how tuples within a given relation are distin- guished. This is expressed in terms of their attributes. That is, the values of the attribute values of a tuple must be such that they can _uniquely identify_ the tuple. In other words, no two tuples in a relation are allowed to have exactly the same value for all attributes.

A **superkey** is a set of one or more attributes that, taken collectively, allow us to identify uniquely a tuple in the relation. For example, the _ID_ attribute of the relation _instructor_ is sufficient to distinguish one instructor tuple from another. Thus, _ID_ is a superkey. The _name_ attribute of _instructor_, on the other hand, is not a superkey, because several instructors might have the same name.

Formally, let _R_ denote the set of attributes in the schema of relation _r_ . If we say that a subset _K_ of _R_ is a _superkey_ for _r_ , we are restricting consideration to instances of relations _r_ in which no two distinct tuples have the same values on all attributes in _K_. That is, if _t_1 and _t_2 are in _r_ and _t_1 = _t_2, then _t_1_.K_ = _t_2_.K_ .

A superkey may contain extraneous attributes. For example, the combination of _ID_ and _name_ is a superkey for the relation _instructor_. If _K_ is a superkey, then so is any superset of _K_. We are often interested in superkeys for which no proper subset is a superkey. Such minimal superkeys are called **candidate keys**.

It is possible that several distinct sets of attributes could serve as a candidate key. Suppose that a combination of _name_ and _dept name_ is sufficient to distinguish among members of the _instructor_ relation. Then, both _{ID}_ and _{name_, _dept name}_ are candidate keys. Although the attributes _ID_ and _name_ together can distinguish _instructor_ tuples, their combination, _{ID_, _name}_, does not form a candidate key, since the attribute _ID_ alone is a candidate key.

We shall use the term **primary key** to denote a candidate key that is chosen by the database designer as the principal means of identifying tuples within a relation. A key (whether primary, candidate, or super) is a property of the entire relation, rather than of the individual tuples. Any two individual tuples in the relation are prohibited from having the same value on the key attributes at the same time. The designation of a key represents a constraint in the real-world enterprise being modeled.

Primary keys must be chosen with care. As we noted, the name of a person is obviously not sufficient, because there may be many people with the same name. In the United States, the social-security number attribute of a person would be a candidate key. Since non-U.S. residents usually do not have social-security  

**46 Chapter 2 Introduction to the Relational Model**

numbers, international enterprises must generate their own unique identifiers. An alternative is to use some unique combination of other attributes as a key.

The primary key should be chosen such that its attribute values are never, or very rarely, changed. For instance, the address field of a person should not be part of the primary key, since it is likely to change. Social-security numbers, on the other hand, are guaranteed never to change. Unique identifiers generated by enterprises generally do not change, except if two enterprises merge; in such a case the same identifier may have been issued by both enterprises, and a reallocation of identifiers may be required to make sure they are unique.

It is customary to list the primary key attributes of a relation schema before the other attributes; for example, the _dept name_ attribute of _department_ is listed first, since it is the primary key. Primary key attributes are also underlined.

A relation, say _r_1, may include among its attributes the primary key of an- other relation, say _r_2\. This attribute is called a **foreign key** from _r_1, referencing _r_2\. The relation _r_1 is also called the **referencing relation** of the foreign key depen- dency, and _r_2 is called the **referenced relation** of the foreign key. For example, the attribute _dept name_ in _instructor_ is a foreign key from _instructor_, referencing _depart- ment_, since _dept name_ is the primary key of _department_. In any database instance, given any tuple, say _ta_ , from the _instructor_ relation, there must be some tuple, say _tb_ , in the _department_ relation such that the value of the _dept name_ attribute of _ta_ is the same as the value of the primary key, _dept name_, of _tb_ .

Now consider the _section_ and _teaches_ relations. It would be reasonable to require that if a section exists for a course, it must be taught by at least one instructor; however, it could possibly be taught by more than one instructor. To enforce this constraint, we would require that if a particular (_course id_, _sec id_, _semester_, _year_) combination appears in _section_, then the same combination must appear in _teaches_. However, this set of values does not form a primary key for _teaches_, since more than one instructor may teach one such section. As a result, we cannot declare a foreign key constraint from _section_ to _teaches_ (although we can define a foreign key constraint in the other direction, from _teaches_ to _section_).

The constraint from _section_ to _teaches_ is an example of a **referential integrity constraint**; a referential integrity constraint requires that the values appearing in specified attributes of any tuple in the referencing relation also appear in specified attributes of at least one tuple in the referenced relation.

**2.4 Schema Diagrams**

A database schema, along with primary key and foreign key dependencies, can be depicted by **schema diagrams**. Figure 2.8 shows the schema diagram for our university organization. Each relation appears as a box, with the relation name at the top in blue, and the attributes listed inside the box. Primary key attributes are shown underlined. Foreign key dependencies appear as arrows from the foreign key attributes of the referencing relation to the primary key of the referenced relation.  

**2.5 Relational Query Languages 47**

_ID course\_id sec\_id semester year grade_

_ID name dept\_name tot\_cred_

_building room\_no capacity_

_s\_id i\_id_

_ID course\_id sec\_id semester year_

_takes_

_section_

_classroom_

_teaches_

_prereq course\_id prereq\_id_

_course\_id title dept\_name credits_

_course_

_student_

_dept\_name building budget_

_department_

_instructor ID name dept\_name salary_

_advisor_

_time\_slot time\_slot\_id day start\_time end\_time_

_course\_id sec\_id semester year building room\_no time\_slot\_id_

**Figure 2.8** Schema diagram for the university database.

Referential integrity constraints other than foreign key constraints are not shown explicitly in schema diagrams. We will study a different diagrammatic representation called the entity-relationship diagram later, in Chapter 7. Entity- relationship diagrams let us represent several kinds of constraints, including general referential integrity constraints.

Many database systems provide design tools with a graphical user interface for creating schema diagrams. We shall discuss diagrammatic representation of schemas at length in Chapter 7.

The enterprise that we use in the examples in later chapters is a university. Figure 2.9 gives the relational schema that we use in our examples, with primary- key attributes underlined. As we shall see in Chapter 3, this corresponds to the approach to defining relations in the SQL data-definition language.

**2.5 Relational Query Languages**

A **query language** is a language in which a user requests information from the database. These languages are usually on a level higher than that of a standard programming language. Query languages can be categorized as either procedural or nonprocedural. In a **procedural language**, the user instructs the system to perform a sequence of operations on the database to compute the desired result. In a **nonprocedural language**, the user describes the desired information without giving a specific procedure for obtaining that information.  

**48 Chapter 2 Introduction to the Relational Model**

_classroom_(_building_, _room number_, _capacity_) _department_(_dept name_, _building_, _budget_) _course_(_course id_, _title_, _dept name_, _credits_) _instructor_(_ID_, _name_, _dept name_, _salary_) _section_(_course id_, _sec id_, _semester_, _year_, _building_, _room number_, _time slot id_) _teaches_(_ID_, _course id_, _sec id_, _semester_, _year_) _student_(_ID_, _name_, _dept name_, _tot cred_) _takes_(_ID_, _course id_, _sec id_, _semester_, _year_, _grade_) _advisor_(_s ID_, _i ID_) _time slot_(_time slot id_, _day_, _start time_, _end time_) _prereq_(_course id_, _prereq id_)

**Figure 2.9** Schema of the university database.

Query languages used in practice include elements of both the procedural and the nonprocedural approaches. We study the very widely used query language SQL in Chapters 3 through 5.

There are a number of “pure” query languages: The relational algebra is pro- cedural, whereas the tuple relational calculus and domain relational calculus are nonprocedural. These query languages are terse and formal, lacking the “syntactic sugar” of commercial languages, but they illustrate the fundamental techniques for extracting data from the database. In Chapter 6, we examine in detail the rela- tional algebra and the two versions of the relational calculus, the tuple relational calculus and domain relational calculus. The relational algebra consists of a set of operations that take one or two relations as input and produce a new relation as their result. The relational calculus uses predicate logic to define the result desired without giving any specific algebraic procedure for obtaining that result.

**2.6 Relational Operations**

All procedural relational query languages provide a set of operations that can be applied to either a single relation or a pair of relations. These operations have the nice and desired property that their result is always a single relation. This property allows one to combine several of these operations in a modular way. Specifically, since the result of a relational query is itself a relation, relational operations can be applied to the results of queries as well as to the given set of relations.

The specific relational operations are expressed differently depending on the language, but fit the general framework we describe in this section. In Chapter 3, we show the specific way the operations are expressed in SQL.

The most frequent operation is the selection of specific tuples from a sin- gle relation (say _instructor_) that satisfies some particular predicate (say _salary >_

$85,000). The result is a new relation that is a subset of the original relation (_in-_  

**2.6 Relational Operations 49**

_ID name dept name salary_

12121 Wu Finance 90000 22222 Einstein Physics 95000 33456 Gold Physics 87000 83821 Brandt Comp. Sci. 92000

**Figure 2.10** Result of query selecting _instructor_ tuples with salary greater than $85000.

_structor_). For example, if we select tuples from the _instructor_ relation of Figure 2.1, satisfying the predicate “_salary_ is greater than $85000”, we get the result shown in Figure 2.10.

Another frequent operation is to select certain attributes (columns) from a relation. The result is a new relation having only those selected attributes. For example, suppose we want a list of instructor _ID_s and salaries without listing the _name_ and _dept name_ values from the _instructor_ relation of Figure 2.1, then the result, shown in Figure 2.11, has the two attributes _ID_ and _salary_. Each tuple in the result is derived from a tuple of the _instructor_ relation but with only selected attributes shown.

The _join_ operation allows the combining of two relations by merging pairs of tuples, one from each relation, into a single tuple. There are a number of different ways to join relations (as we shall see in Chapter 3). Figure 2.12 shows an example of joining the tuples from the _instructor_ and _department_ tables with the new tuples showing the information about each instructor and the department in which she is working. This result was formed by combining each tuple in the _instructor_ relation with the tuple in the _department_ relation for the instructor’s department.

In the form of join shown in Figure 2.12, which is called a _natural join_, a tuple from the _instructor_ relation matches a tuple in the _department_ relation if the values

_ID salary_

10101 65000 12121 90000 15151 40000 22222 95000 32343 60000 33456 87000 45565 75000 58583 62000 76543 80000 76766 72000 83821 92000 98345 80000

**Figure 2.11** Result of query selecting attributes _ID_ and _salary_ from the _instructor_ relation.  

**50 Chapter 2 Introduction to the Relational Model**

_ID name salary dept name building budget_

10101 Srinivasan 65000 Comp. Sci. Taylor 100000 12121 Wu 90000 Finance Painter 120000 15151 Mozart 40000 Music Packard 80000 22222 Einstein 95000 Physics Watson 70000 32343 El Said 60000 History Painter 50000 33456 Gold 87000 Physics Watson 70000 45565 Katz 75000 Comp. Sci. Taylor 100000 58583 Califieri 62000 History Painter 50000 76543 Singh 80000 Finance Painter 120000 76766 Crick 72000 Biology Watson 90000 83821 Brandt 92000 Comp. Sci. Taylor 100000 98345 Kim 80000 Elec. Eng. Taylor 85000

**Figure 2.12** Result of natural join of the _instructor_ and _department_ relations.

of their _dept name_ attributes are the same. All such matching pairs of tuples are present in the join result. In general, the natural join operation on two relations matches tuples whose values are the same on all attribute names that are common to both relations.

The _Cartesian product_ operation combines tuples from two relations, but unlike the join operation, its result contains _all_ pairs of tuples from the two relations, regardless of whether their attribute values match.

Because relations are sets, we can perform normal set operations on relations. The _union_ operation performs a set union of two “similarly structured” tables (say a table of all graduate students and a table of all undergraduate students). For example, one can obtain the set of all students in a department. Other set operations, such as _intersection_ and _set difference_ can be performed as well.

As we noted earlier, we can perform operations on the results of queries. For example, if we want to find the _ID_ and _salary_ for those instructors who have salary greater than $85,000, we would perform the first two operations in our example above. First we select those tuples from the _instructor_ relation where the _salary_ value is greater than $85,000 and then, from that result, select the two attributes _ID_ and _salary_, resulting in the relation shown in Figure 2.13 consisting of the _ID_

_ID salary_

12121 90000 22222 95000 33456 87000 83821 92000

**Figure 2.13** Result of selecting attributes _ID_ and _salary_ of instructors with salary greater than $85,000.  

**2.6 Relational Operations 51**

**RELATIONAL ALGEBRA**

The relational algebra defines a set of operations on relations, paralleling the usual algebraic operations such as addition, subtraction or multiplication, which operate on numbers. Just as algebraic operations on numbers take one or more numbers as input and return a number as output, the relational algebra op- erations typically take one or two relations as input and return a relation as output.

Relational algebra is covered in detail in Chapter 6, but we outline a few of the operations below.

Symbol (Name) Example of Use  salary_\>_\=85000(_instructor_ )

(Selection) Return rows of the input relation that satisfy the predicate.

_ID,salary_ (_instructor_) (Projection) Output specified attributes from all rows of

the input relation. Remove duplicate tuples from the output.

_instructor  department_ (Natural join) Output pairs of rows from the two input rela-

tions that have the same value on all attributes that have the same name.

× _instructor_ × _department_ (Cartesian product) Output all pairs of rows from the two input

relations (regardless of whether or not they have the same values on common attributes)

∪ _name_ (_instructor_ ) ∪ _name_ (_student_ ) (Union) Output the union of tuples from the two input

relations.

and _salary_. In this example, we could have performed the operations in either order, but that is not the case for all situations, as we shall see.

Sometimes, the result of a query contains duplicate tuples. For example, if we select the _dept name_ attribute from the _instructor_ relation, there are several cases of duplication, including “Comp. Sci.”, which shows up three times. Certain rela- tional languages adhere strictly to the mathematical definition of a set and remove duplicates. Others, in consideration of the relatively large amount of processing required to remove duplicates from large result relations, retain duplicates. In these latter cases, the relations are not truly relations in the pure mathematical sense of the term.

Of course, data in a database must be changed over time. A relation can be updated by inserting new tuples, deleting existing tuples, or modifying tuples by  

**52 Chapter 2 Introduction to the Relational Model**

changing the values of certain attributes. Entire relations can be deleted and new ones created.

We shall discuss relational queries and updates using the SQL language in Chapters 3 through 5.

**2.7 Summary**

• The **relational data model** is based on a collection of tables. The user of the database system may query these tables, insert new tuples, delete tuples, and update (modify) tuples. There are several languages for expressing these operations.

• The **schema** of a relation refers to its logical design, while an **instance** of the relation refers to its contents at a point in time. The schema of a database and an instance of a database are similarly defined. The schema of a relation in- cludes its attributes, and optionally the types of the attributes and constraints on the relation such as primary and foreign key constraints.

• A **superkey** of a relation is a set of one or more attributes whose values are guaranteed to identify tuples in the relation uniquely. A candidate key is a minimal superkey, that is, a set of attributes that forms a superkey, but none of whose subsets is a superkey. One of the candidate keys of a relation is chosen as its **primary key**.

• A **foreign key** is a set of attributes in a referencing relation, such that for each tuple in the referencing relation, the values of the foreign key attributes are guaranteed to occur as the primary key value of a tuple in the referenced relation.

• A **schema diagram** is a pictorial depiction of the schema of a database that shows the relations in the database, their attributes, and primary keys and foreign keys.

• The **relational query languages** define a set of operations that operate on tables, and output tables as their results. These operations can be combined to get expressions that express desired queries.

• The **relational algebra** provides a set of operations that take one or more relations as input and return a relation as an output. Practical query languages such as SQL are based on the relational algebra, but add a number of useful syntactic features.

**Review Terms**

• Table • Relation • Tuple

• Attribute • Domain • Atomic domain  

**Practice Exercises 53**

• Null value • Database schema • Database instance • Relation schema • Relation instance • Keys

◦ Superkey

◦ Candidate key

◦ Primary key

• Foreign key

◦ Referencing relation

◦ Referenced relation

• Referential integrity constraint • Schema diagram • Query language

◦ Procedural language

◦ Nonprocedural language

• Operations on relations

◦ Selection of tuples

◦ Selection of attributes

◦ Natural join

◦ Cartesian product

◦ Set operations

• Relational algebra

**Practice Exercises**

**2.1** Consider the relational database of Figure 2.14. What are the appropriate primary keys?

**2.2** Consider the foreign key constraint from the _dept name_ attribute of _instructor_ to the _department_ relation. Give examples of inserts and deletes to these relations, which can cause a violation of the foreign key constraint.

**2.3** Consider the _time slot_ relation. Given that a particular time slot can meet more than once in a week, explain why _day_ and _start time_ are part of the primary key of this relation, while _end time_ is not.

**2.4** In the instance of _instructor_ shown in Figure 2.1, no two instructors have the same name. From this, can we conclude that _name_ can be used as a superkey (or primary key) of _instructor_?

**2.5** What is the result of first performing the cross product of _student_ and _advisor_, and then performing a selection operation on the result with the predicate _s id_ \= ID? (Using the symbolic notation of relational algebra, this query can be written as _s id_\=_I D_(_student_ × _advisor_ ).)

_employee_ (_person name_, _street_, _city_) _works_ (_person name_, _company name_, _salary_) _company_ (_company name_, _city_)

**Figure 2.14** Relational database for Exercises 2.1, 2.7, and 2.12.  

**54 Chapter 2 Introduction to the Relational Model**

_branch_(_branch name_, _branch city, assets_) _customer_ (_customer name_, _customer street, customer city_) _loan_ (_loan number_, _branch name, amount_) _borrower_ (_customer name_, _loan number_) _account_ (_account number_, _branch name, balance_) _depositor_ (_customer name_, _account number_)

**Figure 2.15** Banking database for Exercises 2.8, 2.9, and 2.13.

**2.6** Consider the following expressions, which use the result of a relational algebra operation as the input to another operation. For each expression, explain in words what the expression does.

a. _year_≥2009(_takes_) _ student_

b. _year_≥2009(_takes  student_)

c. _ID,name,course id_ (_student  takes_)

**2.7** Consider the relational database of Figure 2.14. Give an expression in the relational algebra to express each of the following queries:

a. Find the names of all employees who live in city “Miami”.

b. Find the names of all employees whose salary is greater than $100,000.

c. Find the names of all employees who live in “Miami” and whose salary is greater than $100,000.

**2.8** Consider the bank database of Figure 2.15. Give an expression in the rela- tional algebra for each of the following queries.

a. Find the names of all branches located in “Chicago”.

b. Find the names of all borrowers who have a loan in branch “Down- town”.

**Exercises**

**2.9** Consider the bank database of Figure 2.15.

a. What are the appropriate primary keys?

b. Given your choice of primary keys, identify appropriate foreign keys.

**2.10** Consider the _advisor_ relation shown in Figure 2.8, with _s id_ as the primary key of _advisor_. Suppose a student can have more than one advisor. Then, would _s id_ still be a primary key of the _advisor_ relation? If not, what should the primary key of _advisor_ be?

**2.11** Describe the differences in meaning between the terms _relation_ and _relation schema_.  

**Bibliographical Notes 55**

**2.12** Consider the relational database of Figure 2.14. Give an expression in the relational algebra to express each of the following queries:

a. Find the names of all employees who work for “First Bank Corpora- tion”.

b. Find the names and cities of residence of all employees who work for “First Bank Corporation”.

c. Find the names, street address, and cities of residence of all employees who work for “First Bank Corporation” and earn more than $10,000.

**2.13** Consider the bank database of Figure 2.15. Give an expression in the rela- tional algebra for each of the following queries:

a. Find all loan numbers with a loan value greater than $10,000.

b. Find the names of all depositors who have an account with a value greater than $6,000.

c. Find the names of all depositors who have an account with a value greater than $6,000 at the “Uptown” branch.

**2.14** List two reasons why null values might be introduced into the database.

**2.15** Discuss the relative merits of procedural and nonprocedural languages.

**Bibliographical Notes**

E. F. Codd of the IBM San Jose Research Laboratory proposed the relational model in the late 1960s (Codd \[1970\]). This work led to the prestigious ACM Turing Award to Codd in 1981 (Codd \[1982\]).

After Codd published his original paper, several research projects were formed with the goal of constructing practical relational database systems, including System R at the IBM San Jose Research Laboratory, Ingres at the University of California at Berkeley, and Query-by-Example at the IBM T. J. Watson Research Center.

Many relational database products are now commercially available. These include IBM’s DB2 and Informix, Oracle, Sybase, and Microsoft SQL Server. Open source relational database systems include MySQL and PostgreSQL. Microsoft Access is a single-user database product that is part of the Microsoft Office suite.

Atzeni and Antonellis \[1993\], Maier \[1983\], and Abiteboul et al. \[1995\] are texts devoted exclusively to the theory of the relational data model.  

_This page intentionally left blank_  

**_C H A P T E R_3 Introduction to SQL**

There are a number of database query languages in use, either commercially or experimentally. In this chapter, as well as in Chapters 4 and 5, we study the most widely used query language, SQL.

Although we refer to the SQL language as a “query language,” it can do much more than just query a database. It can define the structure of the data, modify data in the database, and specify security constraints.

It is not our intention to provide a complete users’ guide for SQL. Rather, we present SQL’s fundamental constructs and concepts. Individual implementations of SQL may differ in details, or may support only a subset of the full language.

**3.1 Overview of the SQL Query Language**

IBM developed the original version of SQL, originally called Sequel, as part of the System R project in the early 1970s. The Sequel language has evolved since then, and its name has changed to SQL (Structured Query Language). Many products now support the SQL language. SQL has clearly established itself as _the_ standard relational database language.

In 1986, the American National Standards Institute (ANSI) and the Interna- tional Organization for Standardization (ISO) published an SQL standard, called SQL-86. ANSI published an extended standard for SQL, SQL-89, in 1989. The next ver- sion of the standard was SQL-92 standard, followed by SQL:1999, SQL:2003, SQL:2006, and most recently SQL:2008. The bibliographic notes provide references to these standards.

The SQL language has several parts:

• **Data-definition language** (DDL). The SQL DDL provides commands for defin- ing relation schemas, deleting relations, and modifying relation schemas.

• **Data-manipulation language** (DML). The SQL DML provides the ability to query information from the database and to insert tuples into, delete tuples from, and modify tuples in the database.

**57**  

**58 Chapter 3 Introduction to SQL**

• **Integrity**. The SQL DDL includes commands for specifying integrity con- straints that the data stored in the database must satisfy. Updates that violate integrity constraints are disallowed.

• **View definition.** The SQL DDL includes commands for defining views.

• **Transaction control**. SQL includes commands for specifying the beginning and ending of transactions.

• **Embedded SQL** and **dynamic SQL**. Embedded and dynamic SQL define how SQL statements can be embedded within general-purpose programming lan- guages, such as C, C++, and Java.

• **Authorization**. The SQL DDL includes commands for specifying access rights to relations and views.

In this chapter, we present a survey of basic DML and the DDL features of SQL. Features described here have been part of the SQL standard since SQL-92.

In Chapter 4, we provide a more detailed coverage of the SQL query language, including (a) various join expressions; (b) views; (c) transactions; (d) integrity constraints; (e) type system; and (f) authorization.

In Chapter 5, we cover more advanced features of the SQL language, including (a) mechanisms to allow accessing SQL from a programming language; (b) SQL functions and procedures; (c) triggers; (d) recursive queries; (e) advanced aggre- gation features; and (f) several features designed for data analysis, which were introduced in SQL:1999, and subsequent versions of SQL. Later, in Chapter 22, we outline object-oriented extensions to SQL, which were introduced in SQL:1999.

Although most SQL implementations support the standard features we de- scribe here, you should be aware that there are differences between implementa- tions. Most implementations support some nonstandard features, while omitting support for some of the more advanced features. In case you find that some lan- guage features described here do not work on the database system that you use, consult the user manuals for your database system to find exactly what features it supports.

**3.2 SQL Data Definition**

The set of relations in a database must be specified to the system by means of a data-definition language (DDL). The SQL DDL allows specification of not only a set of relations, but also information about each relation, including:

• The schema for each relation.

• The types of values associated with each attribute.

• The integrity constraints.

• The set of indices to be maintained for each relation.  

**3.2 SQL Data Definition 59**

• The security and authorization information for each relation.

• The physical storage structure of each relation on disk.

We discuss here basic schema definition and basic types; we defer discussion of the other SQL DDL features to Chapters 4 and 5.

**3.2.1 Basic Types**

The SQL standard supports a variety of built-in types, including:

• **char**(_n_): A fixed-length character string with user-specified length _n_. The full form, **character**, can be used instead.

• **varchar**(_n_): A variable-length character string with user-specified maximum length _n_. The full form, **character varying**, is equivalent.

• **int**: An integer (a finite subset of the integers that is machine dependent). The full form, **integer**, is equivalent.

• **smallint**: A small integer (a machine-dependent subset of the integer type).

• **numeric**(_p, d_): A fixed-point number with user-specified precision. The num- ber consists of _p_ digits (plus a sign), and _d_ of the _p_ digits are to the right of the decimal point. Thus, **numeric**(3,1) allows 44_._5 to be stored exactly, but neither 444_._5 or 0_._32 can be stored exactly in a field of this type.

• **real, double precision**: Floating-point and double-precision floating-point numbers with machine-dependent precision.

• **float(n):** A floating-point number, with precision of at least _n_ digits.

Additional types are covered in Section 4.5. Each type may include a special value called the **null** value. A null value

indicates an absent value that may exist but be unknown or that may not exist at all. In certain cases, we may wish to prohibit null values from being entered, as we shall see shortly.

The **char** data type stores fixed length strings. Consider, for example, an attribute _A_ of type **char**(10). If we store a string “Avi” in this attribute, 7 spaces are appended to the string to make it 10 characters long. In contrast, if attribute _B_ were of type **varchar**(10), and we store “Avi” in attribute _B_, no spaces would be added. When comparing two values of type **char**, if they are of different lengths extra spaces are automatically added to the shorter one to make them the same size, before comparison.

When comparing a **char** type with a **varchar** type, one may expect extra spaces to be added to the **varchar** type to make the lengths equal, before comparison; however, this may or may not be done, depending on the database system. As a result, even if the same value “Avi” is stored in the attributes _A_ and _B_ above, a comparison _A_\=_B_ may return false. We recommend you always use the **varchar** type instead of the **char** type to avoid these problems.  

**60 Chapter 3 Introduction to SQL**

SQL also provides the **nvarchar** type to store multilingual data using the Unicode representation. However, many databases allow Unicode (in the UTF-8 representation) to be stored even in **varchar** types.

**3.2.2 Basic Schema Definition**

We define an SQL relation by using the **create table** command. The following command creates a relation _department_ in the database.

**create table** _department_ (_dept name_ **varchar** (20), _building_ **varchar** (15), _budget_ **numeric** (12,2), **primary key** (_dept name_));

The relation created above has three attributes, _dept name_, which is a character string of maximum length 20, _building_, which is a character string of maximum length 15, and _budget_, which is a number with 12 digits in total, 2 of which are after the decimal point. The **create table** command also specifies that the _dept name_ attribute is the primary key of the _department_ relation.

The general form of the **create table** command is:

**create table** _r_ (_A_1 _D_1, _A_2 _D_2, . . . , _An Dn_, 〈integrity-constraint1〉, _. . . ,_

〈integrity-constraint_k_〉);

where _r_ is the name of the relation, each _Ai_ is the name of an attribute in the schema of relation _r_, and _Di_ is the domain of attribute _Ai_ ; that is, _Di_ specifies the type of attribute _Ai_ along with optional constraints that restrict the set of allowed values for _Ai_ .

The semicolon shown at the end of the **create table** statements, as well as at the end of other SQL statements later in this chapter, is optional in many SQL implementations.

SQL supports a number of different integrity constraints. In this section, we discuss only a few of them:

• **primary key** (_Aj_1 _, Aj_2_, . . . , Ajm_ ): The **primary-key** specification says that at- tributes _Aj_1 _, Aj_2 _, . . . , Ajm_ form the primary key for the relation. The primary- key attributes are required to be _nonnull_ and _unique_; that is, no tuple can have a null value for a primary-key attribute, and no two tuples in the relation can be equal on all the primary-key attributes. Although the primary-key  

**3.2 SQL Data Definition 61**

specification is optional, it is generally a good idea to specify a primary key for each relation.

• **foreign key** (_Ak_1_, Ak_2_, . . . , Akn_) **references** _s_: The **foreign key** specification says that the values of attributes (_Ak_1_, Ak_2_, . . . , Akn_) for any tuple in the relation must correspond to values of the primary key attributes of some tuple in relation _s_.

Figure 3.1 presents a partial SQL DDL definition of the university database we use in the text. The definition of the _course_ table has a declaration “**foreign key** (_dept name_) **references** _department_”. This foreign-key declaration specifies that for each course tuple, the department name specified in the tuple must exist in the primary key attribute (_dept name_) of the _department_ relation. Without this constraint, it is possible for a course to specify a nonexistent department name. Figure 3.1 also shows foreign key constraints on tables _section_, _instructor_ and _teaches_.

• **not null**: The **not null** constraint on an attribute specifies that the null value is not allowed for that attribute; in other words, the constraint excludes the null value from the domain of that attribute. For example, in Figure 3.1, the **not null** constraint on the _name_ attribute of the _instructor_ relation ensures that the name of an instructor cannot be null.

More details on the foreign-key constraint, as well as on other integrity constraints that the **create table** command may include, are provided later, in Section 4.4.

SQL prevents any update to the database that violates an integrity constraint. For example, if a newly inserted or modified tuple in a relation has null values for any primary-key attribute, or if the tuple has the same value on the primary-key attributes as does another tuple in the relation, SQL flags an error and prevents the update. Similarly, an insertion of a _course_ tuple with a _dept name_ value that does not appear in the _department_ relation would violate the foreign-key constraint on _course_, and SQL prevents such an insertion from taking place.

A newly created relation is empty initially. We can use the **insert** command to load data into the relation. For example, if we wish to insert the fact that there is an instructor named Smith in the Biology department with _instructor id_ 10211 and a salary of $66,000, we write:

**insert into** _instructor_ **values** (10211, ’Smith’, ’Biology’, 66000);

The values are specified in the _order_ in which the corresponding attributes are listed in the relation schema. The insert command has a number of useful features, and is covered in more detail later, in Section 3.9.2.

We can use the **delete** command to delete tuples from a relation. The command

**delete from** _student_;  

**62 Chapter 3 Introduction to SQL**

**create table** _department_ (_dept name_ **varchar** (20), _building_ **varchar** (15), _budget_ **numeric** (12,2), **primary key** (_dept name_));

**create table** _course_ (_course id_ **varchar** (7), _title_ **varchar** (50), _dept name_ **varchar** (20), _credits_ **numeric** (2,0), **primary key** (_course id_), **foreign key** (_dept name_) **references** _department_);

**create table** _instructor_ (_ID_ **varchar** (5), _name_ **varchar** (20) **not null**, _dept name_ **varchar** (20), _salary_ **numeric** (8,2), **primary key** (_ID_), **foreign key** (_dept name_) **references** _department_);

**create table** _section_ (_course id_ **varchar** (8), _sec id_ **varchar** (8), _semester_ **varchar** (6), _year_ **numeric** (4,0), _building_ **varchar** (15), _room number_ **varchar** (7), _time slot id_ **varchar** (4), **primary key** (_course id_, _sec id_, _semester_, _year_), **foreign key** (_course id_) **references** _course_);

**create table** _teaches_ (_ID_ **varchar** (5), _course id_ **varchar** (8), _sec id_ **varchar** (8), _semester_ **varchar** (6), _year_ **numeric** (4,0), **primary key** (_ID_, _course id_, _sec id_, _semester_, _year_), **foreign key** (_course id_, _sec id_, _semester_, _year_) **references** _section_, **foreign key** (_ID_) **references** _instructor_);

**Figure 3.1** SQL data definition for part of the university database.  

**3.3 Basic Structure of SQL Queries 63**

would delete all tuples from the _student_ relation. Other forms of the delete com- mand allow specific tuples to be deleted; the delete command is covered in more detail later, in Section 3.9.1.

To remove a relation from an SQL database, we use the **drop table** command. The **drop table** command deletes all information about the dropped relation from the database. The command

**drop table** _r_;

is a more drastic action than

**delete from** _r_;

The latter retains relation _r_, but deletes all tuples in _r_. The former deletes not only all tuples of _r_, but also the schema for _r_. After _r_ is dropped, no tuples can be inserted into _r_ unless it is re-created with the **create table** command.

We use the **alter table** command to add attributes to an existing relation. All tuples in the relation are assigned _null_ as the value for the new attribute. The form of the **alter table** command is

**alter table** _r_ **add** _A D_;

where _r_ is the name of an existing relation, _A_ is the name of the attribute to be added, and _D_ is the type of the added attribute. We can drop attributes from a relation by the command

**alter table** _r_ **drop** _A_;

where _r_ is the name of an existing relation, and _A_ is the name of an attribute of the relation. Many database systems do not support dropping of attributes, although they will allow an entire table to be dropped.

**3.3 Basic Structure of SQL Queries**

The basic structure of an SQL query consists of three clauses: **select**, **from**, and **where**. The query takes as its input the relations listed in the **from** clause, operates on them as specified in the **where** and **select** clauses, and then produces a relation as the result. We introduce the SQL syntax through examples, and describe the general structure of SQL queries later.

**3.3.1 Queries on a Single Relation**

Let us consider a simple query using our university example, “Find the names of all instructors.” Instructor names are found in the _instructor_ relation, so we  

**64 Chapter 3 Introduction to SQL**

_name_

Srinivasan Wu Mozart Einstein El Said Gold Katz Califieri Singh Crick Brandt Kim

**Figure 3.2** Result of “**select** _name_ **from** _instructor_”.

put that relation in the **from** clause. The instructor’s name appears in the _name_ attribute, so we put that in the **select** clause.

**select** _name_ **from** _instructor_;

The result is a relation consisting of a single attribute with the heading _name_. If the _instructor_ relation is as shown in Figure 2.1, then the relation that results from the preceding query is shown in Figure 3.2.

Now consider another query, “Find the department names of all instructors,” which can be written as:

**select** _dept name_ **from** _instructor_;

Since more than one instructor can belong to a department, a department name could appear more than once in the _instructor_ relation. The result of the above query is a relation containing the department names, shown in Figure 3.3.

In the formal, mathematical definition of the relational model, a relation is a set. Thus, duplicate tuples would never appear in relations. In practice, duplicate elimination is time-consuming. Therefore, SQL allows duplicates in relations as well as in the results of SQL expressions. Thus, the preceding SQL query lists each department name once for every tuple in which it appears in the _instructor_ relation.

In those cases where we want to force the elimination of duplicates, we insert the keyword **distinct** after **select**. We can rewrite the preceding query as:

**select distinct** _dept name_ **from** _instructor_;  

**3.3 Basic Structure of SQL Queries 65**

_dept name_

Comp. Sci. Finance Music Physics History Physics Comp. Sci. History Finance Biology Comp. Sci. Elec. Eng.

**Figure 3.3** Result of “**select** _dept name_ **from** _instructor_”.

if we want duplicates removed. The result of the above query would contain each department name at most once.

SQL allows us to use the keyword **all** to specify explicitly that duplicates are not removed:

**select all** _dept name_ **from** _instructor_;

Since duplicate retention is the default, we shall not use **all** in our examples. To ensure the elimination of duplicates in the results of our example queries, we shall use **distinct** whenever it is necessary.

The **select** clause may also contain arithmetic expressions involving the op- erators +, −, ∗, and / operating on constants or attributes of tuples. For example, the query:

**select** _ID_, _name_, _dept name_, _salary_ \* 1.1 **from** _instructor_;

returns a relation that is the same as the _instructor_ relation, except that the attribute _salary_ is multiplied by 1.1. This shows what would result if we gave a 10% raise to each instructor; note, however, that it does not result in any change to the _instructor_ relation.

SQL also provides special data types, such as various forms of the _date_ type, and allows several arithmetic functions to operate on these types. We discuss this further in Section 4.5.1.

The **where** clause allows us to select only those rows in the result relation of the **from** clause that satisfy a specified predicate. Consider the query “Find the names of all instructors in the Computer Science department who have salary greater than $70,000.” This query can be written in SQL as:  

**66 Chapter 3 Introduction to SQL**

_name_

Katz Brandt

**Figure 3.4** Result of “Find the names of all instructors in the Computer Science department who have salary greater than $70,000.”

**select** _name_ **from** _instructor_ **where** _dept name_ \= ’Comp. Sci.’ **and** _salary >_ 70000;

If the _instructor_ relation is as shown in Figure 2.1, then the relation that results from the preceding query is shown in Figure 3.4.

SQL allows the use of the logical connectives **and**, **or**, and **not** in the **where** clause. The operands of the logical connectives can be expressions involving the comparison operators _<_, _<_\=, _\>_, _\>_\=, =, and _<>_. SQL allows us to use the comparison operators to compare strings and arithmetic expressions, as well as special types, such as date types.

We shall explore other features of **where** clause predicates later in this chapter.

**3.3.2 Queries on Multiple Relations**

So far our example queries were on a single relation. Queries often need to access information from multiple relations. We now study how to write such queries.

An an example, suppose we want to answer the query “Retrieve the names of all instructors, along with their department names and department building name.”

Looking at the schema of the relation _instructor_, we realize that we can get the department name from the attribute _dept name_, but the department building name is present in the attribute _building_ of the relation _department_. To answer the query, each tuple in the _instructor_ relation must be matched with the tuple in the _department_ relation whose _dept name_ value matches the _dept name_ value of the _instructor_ tuple.

In SQL, to answer the above query, we list the relations that need to be accessed in the **from** clause, and specify the matching condition in the **where** clause. The above query can be written in SQL as

**select** _name_, _instructor_._dept name_, _building_ **from** _instructor_, _department_ **where** _instructor_._dept name_\= _department_._dept name_;

If the _instructor_ and _department_ relations are as shown in Figures 2.1 and 2.5 respectively, then the result of this query is shown in Figure 3.5.

Note that the attribute _dept name_ occurs in both the relations _instructor_ and _department_, and the relation name is used as a prefix (in _instructor_._dept name_, and  

**3.3 Basic Structure of SQL Queries 67**

_name dept name building_

Srinivasan Comp. Sci. Taylor Wu Finance Painter Mozart Music Packard Einstein Physics Watson El Said History Painter Gold Physics Watson Katz Comp. Sci. Taylor Califieri History Painter Singh Finance Painter Crick Biology Watson Brandt Comp. Sci. Taylor Kim Elec. Eng. Taylor

**Figure 3.5** The result of “Retrieve the names of all instructors, along with their department names and department building name.”

_department_._dept name_) to make clear to which attribute we are referring. In contrast, the attributes _name_ and _building_ appear in only one of the relations, and therefore do not need to be prefixed by the relation name.

This naming convention _requires_ that the relations that are present in the **from** clause have distinct names. This requirement causes problems in some cases, such as when information from two different tuples in the same relation needs to be combined. In Section 3.4.1, we see how to avoid these problems by using the rename operation.

We now consider the general case of SQL queries involving multiple relations. As we have seen earlier, an SQL query can contain three types of clauses, the **select** clause, the **from** clause, and the **where** clause. The role of each clause is as follows:

• The **select** clause is used to list the attributes desired in the result of a query.

• The **from** clause is a list of the relations to be accessed in the evaluation of the query.

• The **where** clause is a predicate involving attributes of the relation in the **from** clause.

A typical SQL query has the form

**select** _A_1_, A_2_, . . . , An_ **from** _r_1_, r_2_, . . . , rm_ **where** _P_;

Each _Ai_ represents an attribute, and each _ri_ a relation. _P_ is a predicate. If the **where** clause is omitted, the predicate _P_ is **true**.  

**68 Chapter 3 Introduction to SQL**

Although the clauses must be written in the order **select**, **from**, **where**, the easiest way to understand the operations specified by the query is to consider the clauses in operational order: first **from**, then **where**, and then **select**.1

The **from** clause by itself defines a Cartesian product of the relations listed in the clause. It is defined formally in terms of set theory, but is perhaps best understood as an iterative process that generates tuples for the result relation of the **from** clause.

**for each** tuple _t_1 **in** relation _r_1 **for each** tuple _t_2 **in** relation _r_2

_. . ._

**for each** tuple _tm_ **in** relation _rm_ Concatenate _t_1_, t_2_, . . . , tm_ into a single tuple _t_ Add _t_ into the result relation

The result relation has all attributes from all the relations in the **from** clause. Since the same attribute name may appear in both _ri_ and _r j_ , as we saw earlier, we prefix the the name of the relation from which the attribute originally came, before the attribute name.

For example, the relation schema for the Cartesian product of relations _in- structor_ and _teaches_ is:

(_instructor_._ID_, _instructor_._name_, _instructor_._dept name_, _instructor_._salary teaches_._ID_, _teaches_._course id_, _teaches_._sec id_, _teaches_._semester_, _teaches_._year_)

With this schema, we can distinguish _instructor_._ID_ from _teaches_._ID_. For those at- tributes that appear in only one of the two schemas, we shall usually drop the relation-name prefix. This simplification does not lead to any ambiguity. We can then write the relation schema as:

(_instructor_._ID_, _name_, _dept name_, _salary teaches_._ID_, _course id_, _sec id_, _semester_, _year_)

To illustrate, consider the _instructor_ relation in Figure 2.1 and the _teaches_ relation in Figure 2.7. Their Cartesian product is shown in Figure 3.6, which includes only a portion of the tuples that make up the Cartesian product result.2

The Cartesian product by itself combines tuples from _instructor_ and _teaches_ that are unrelated to each other. Each tuple in _instructor_ is combined with _every_ tuple in _teaches_, even those that refer to a different instructor. The result can be an extremely large relation, and it rarely makes sense to create such a Cartesian product.

1In practice, SQL may convert the expression into an equivalent form that can be processed more efficiently. However, we shall defer concerns about efficiency to Chapters 12 and 13. 2Note that we renamed _instructor_._ID_ as _inst_._ID_ to reduce the width of the table in Figure 3.6.  

**3.3 Basic Structure of SQL Queries 69**

_inst_._ID name dept name salary teaches_._ID course id sec id semester year_

10101 Srinivasan Physics 95000 10101 CS-101 1 Fall 2009 10101 Srinivasan Physics 95000 10101 CS-315 1 Spring 2010 10101 Srinivasan Physics 95000 10101 CS-347 1 Fall 2009 10101 Srinivasan Physics 95000 10101 FIN-201 1 Spring 2010 10101 Srinivasan Physics 95000 15151 MU-199 1 Spring 2010 10101 Srinivasan Physics 95000 22222 PHY-101 1 Fall 2009

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ... 12121 Wu Physics 95000 10101 CS-101 1 Fall 2009 12121 Wu Physics 95000 10101 CS-315 1 Spring 2010 12121 Wu Physics 95000 10101 CS-347 1 Fall 2009 12121 Wu Physics 95000 10101 FIN-201 1 Spring 2010 12121 Wu Physics 95000 15151 MU-199 1 Spring 2010 12121 Wu Physics 95000 22222 PHY-101 1 Fall 2009

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ... 15151 Mozart Physics 95000 10101 CS-101 1 Fall 2009 15151 Mozart Physics 95000 10101 CS-315 1 Spring 2010 15151 Mozart Physics 95000 10101 CS-347 1 Fall 2009 15151 Mozart Physics 95000 10101 FIN-201 1 Spring 2010 15151 Mozart Physics 95000 15151 MU-199 1 Spring 2010 15151 Mozart Physics 95000 22222 PHY-101 1 Fall 2009

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ... 22222 Einstein Physics 95000 10101 CS-101 1 Fall 2009 22222 Einstein Physics 95000 10101 CS-315 1 Spring 2010 22222 Einstein Physics 95000 10101 CS-347 1 Fall 2009 22222 Einstein Physics 95000 10101 FIN-201 1 Spring 2010 22222 Einstein Physics 95000 15151 MU-199 1 Spring 2010 22222 Einstein Physics 95000 22222 PHY-101 1 Fall 2009

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ...

**Figure 3.6** The Cartesian product of the _instructor_ relation with the _teaches_ relation.

Instead, the predicate in the **where** clause is used to restrict the combinations created by the Cartesian product to those that are meaningful for the desired answer. We would expect a query involving _instructor_ and _teaches_ to combine a particular tuple _t_ in _instructor_ with only those tuples in _teaches_ that refer to the same instructor to which _t_ refers. That is, we wish only to match _teaches_ tuples with _instructor_ tuples that have the same _ID_ value. The following SQL query ensures this condition, and outputs the instructor name and course identifiers from such matching tuples.  

**70 Chapter 3 Introduction to SQL**

**select** _name_, _course id_ **from** _instructor_, _teaches_ **where** _instructor_._ID_\= _teaches_._ID_;

Note that the above query outputs only instructors who have taught some course. Instructors who have not taught any course are not output; if we wish to output such tuples, we could use an operation called the _outer join_, which is described in Section 4.1.2.

If the _instructor_ relation is as shown in Figure 2.1 and the _teaches_ relation is as shown in Figure 2.7, then the relation that results from the preceding query is shown in Figure 3.7. Observe that instructors Gold, Califieri, and Singh, who have not taught any course, do not appear in the above result.

If we only wished to find instructor names and course identifiers for instruc- tors in the Computer Science department, we could add an extra predicate to the **where** clause, as shown below.

**select** _name_, _course id_ **from** _instructor_, _teaches_ **where** _instructor_._ID_\= _teaches_._ID_ **and** _instructor_._dept name_ \= ’Comp. Sci.’;

Note that since the _dept name_ attribute occurs only in the _instructor_ relation, we could have used just _dept name_, instead of _instructor_._dept name_ in the above query.

In general, the meaning of an SQL query can be understood as follows:

_name course id_

Srinivasan CS-101 Srinivasan CS-315 Srinivasan CS-347 Wu FIN-201 Mozart MU-199 Einstein PHY-101 El Said HIS-351 Katz CS-101 Katz CS-319 Crick BIO-101 Crick BIO-301 Brandt CS-190 Brandt CS-190 Brandt CS-319 Kim EE-181

**Figure 3.7** Result of “For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught.”  

**3.3 Basic Structure of SQL Queries 71**

**1\.** Generate a Cartesian product of the relations listed in the **from** clause

**2\.** Apply the predicates specified in the **where** clause on the result of Step 1.

**3\.** For each tuple in the result of Step 2, output the attributes (or results of expressions) specified in the **select** clause.

The above sequence of steps helps make clear what the result of an SQL query should be, _not_ how it should be executed. A real implementation of SQL would not execute the query in this fashion; it would instead optimize evaluation by generating (as far as possible) only elements of the Cartesian product that satisfy the **where** clause predicates. We study such implementation techniques later, in Chapters 12 and 13.

When writing queries, you should be careful to include appropriate **where** clause conditions. If you omit the **where** clause condition in the preceding SQL query, it would output the Cartesian product, which could be a huge relation. For the example _instructor_ relation in Figure 2.1 and the example _teaches_ relation in Figure 2.7, their Cartesian product has 12 ∗ 13 = 156 tuples — more than we can show in the text! To make matters worse, suppose we have a more realistic number of instructors than we show in our sample relations in the figures, say 200 instructors. Let’s assume each instructor teaches 3 courses, so we have 600 tuples in the _teaches_ relation. Then the above iterative process generates 200 ∗ 600 = 120,000 tuples in the result.

**3.3.3 The Natural Join**

In our example query that combined information from the _instructor_ and _teaches_ table, the matching condition required _instructor_._ID_ to be equal to _teaches_._ID_. These are the only attributes in the two relations that have the same name. In fact this is a common case; that is, the matching condition in the **from** clause most often requires all attributes with matching names to be equated.

To make the life of an SQL programmer easier for this common case, SQL supports an operation called the _natural join_, which we describe below. In fact SQL supports several other ways in which information from two or more relations can be **joined** together. We have already seen how a Cartesian product along with a **where** clause predicate can be used to join information from multiple relations. Other ways of joining information from multiple relations are discussed in Section 4.1.

The **natural join** operation operates on two relations and produces a relation as the result. Unlike the Cartesian product of two relations, which concatenates each tuple of the first relation with every tuple of the second, natural join considers only those pairs of tuples with the same value on those attributes that appear in the schemas of both relations. So, going back to the example of the relations _instructor_ and _teaches_, computing _instructor_ **natural join** _teaches_ considers only those pairs of tuples where both the tuple from _instructor_ and the tuple from _teaches_ have the same value on the common attribute, _ID_.  

**72 Chapter 3 Introduction to SQL**

_ID name dept name salary course id sec id semester year_

10101 Srinivasan Comp. Sci. 65000 CS-101 1 Fall 2009 10101 Srinivasan Comp. Sci. 65000 CS-315 1 Spring 2010 10101 Srinivasan Comp. Sci. 65000 CS-347 1 Fall 2009 12121 Wu Finance 90000 FIN-201 1 Spring 2010 15151 Mozart Music 40000 MU-199 1 Spring 2010 22222 Einstein Physics 95000 PHY-101 1 Fall 2009 32343 El Said History 60000 HIS-351 1 Spring 2010 45565 Katz Comp. Sci. 75000 CS-101 1 Spring 2010 45565 Katz Comp. Sci. 75000 CS-319 1 Spring 2010 76766 Crick Biology 72000 BIO-101 1 Summer 2009 76766 Crick Biology 72000 BIO-301 1 Summer 2010 83821 Brandt Comp. Sci. 92000 CS-190 1 Spring 2009 83821 Brandt Comp. Sci. 92000 CS-190 2 Spring 2009 83821 Brandt Comp. Sci. 92000 CS-319 2 Spring 2010 98345 Kim Elec. Eng. 80000 EE-181 1 Spring 2009

**Figure 3.8** The natural join of the _instructor_ relation with the _teaches_ relation.

The result relation, shown in Figure 3.8, has only 13 tuples, the ones that give information about an instructor and a course that that instructor actually teaches. Notice that we do not repeat those attributes that appear in the schemas of both relations; rather they appear only once. Notice also the order in which the attributes are listed: first the attributes common to the schemas of both relations, second those attributes unique to the schema of the first relation, and finally, those attributes unique to the schema of the second relation.

Consider the query “For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught”, which we wrote earlier as:

**select** _name_, _course id_ **from** _instructor_, _teaches_ **where** _instructor_._ID_\= _teaches_._ID_;

This query can be written more concisely using the natural-join operation in SQL as:

**select** _name_, _course id_ **from** _instructor_ **natural join** _teaches_;

Both of the above queries generate the same result. As we saw earlier, the result of the natural join operation is a relation. Concep-

tually, expression “_instructor_ **natural join** _teaches_” in the **from** clause is replaced  

**3.3 Basic Structure of SQL Queries 73**

by the relation obtained by evaluating the natural join.3 The **where** and **select** clauses are then evaluated on this relation, as we saw earlier in Section 3.3.2.

A **from** clause in an SQL query can have multiple relations combined using natural join, as shown here:

**select** _A_1_, A_2_, . . . , An_ **from** _r_1 **natural join** _r_2 **natural join** . . . **natural join** _rm_ **where** _P_;

More generally, a **from** clause can be of the form

**from** _E_1, _E_2, . . . , _En_

where each _Ei_ can be a single relation or an expression involving natural joins. For example, suppose we wish to answer the query “List the names of instructors along with the the titles of courses that they teach.” The query can be written in SQL as follows:

**select** _name_, _title_ **from** _instructor_ **natural join** _teaches_, _course_ **where** _teaches_._course id_\= _course_._course id_;

The natural join of _instructor_ and _teaches_ is first computed, as we saw earlier, and a Cartesian product of this result with _course_ is computed, from which the **where** clause extracts only those tuples where the course identifier from the join result matches the course identifier from the _course_ relation. Note that _teaches_._course id_ in the **where** clause refers to the _course id_ field of the natural join result, since this field in turn came from the _teaches_ relation.

In contrast the following SQL query does _not_ compute the same result:

**select** _name_, _title_ **from** _instructor_ **natural join** _teaches_ **natural join** _course_;

To see why, note that the natural join of _instructor_ and _teaches_ contains the attributes (_ID_, _name_, _dept name_, _salary_, _course id_, _sec id_), while the _course_ relation contains the attributes (_course id_, _title_, _dept name_, _credits_). As a result, the natural join of these two would require that the _dept name_ attribute values from the two inputs be the same, in addition to requiring that the _course id_ values be the same. This query would then omit all (instructor name, course title) pairs where the instructor teaches a course in a department other than the instructor’s own department. The previous query, on the other hand, correctly outputs such pairs.

3As a consequence, it is not possible to use attribute names containing the original relation names, for instance _instruc- tor_._name_or _teaches_._course id_, to refer to attributes in the natural join result; we can, however, use attribute names such as _name_ and _course id_, without the relation names.  

**74 Chapter 3 Introduction to SQL**

To provide the benefit of natural join while avoiding the danger of equating attributes erroneously, SQL provides a form of the natural join construct that allows you to specify exactly which columns should be equated. This feature is illustrated by the following query:

**select** _name_, _title_ **from** (_instructor_ **natural join** _teaches_) **join** _course_ **using** (_course id_);

The operation **join** _. . ._ **using** requires a list of attribute names to be specified. Both inputs must have attributes with the specified names. Consider the operation _r_1 **join** _r_2 **using**(_A_1_, A_2). The operation is similar to _r_1 **natural join** _r_2, except that a pair of tuples _t_1 from _r_1 and _t_2 from _r_2 match if _t_1_.A_1 = _t_2_.A_1 and _t_1_.A_2 = _t_2_.A_2; even if _r_1 and _r_2 both have an attribute named _A_3, it is _not_ required that _t_1_.A_3 = _t_2_.A_3.

Thus, in the preceding SQL query, the **join** construct permits _teaches_._dept name_ and _course_._dept name_ to differ, and the SQL query gives the correct answer.

**3.4 Additional Basic Operations**

There are number of additional basic operations that are supported in SQL.

**3.4.1 The Rename Operation**

Consider again the query that we used earlier:

**select** _name_, _course id_ **from** _instructor_, _teaches_ **where** _instructor_._ID_\= _teaches_._ID_;

The result of this query is a relation with the following attributes:

_name_, _course id_

The names of the attributes in the result are derived from the names of the attributes in the relations in the **from** clause.

We cannot, however, always derive names in this way, for several reasons: First, two relations in the **from** clause may have attributes with the same name, in which case an attribute name is duplicated in the result. Second, if we used an arithmetic expression in the **select** clause, the resultant attribute does not have a name. Third, even if an attribute name can be derived from the base relations as in the preceding example, we may want to change the attribute name in the result. Hence, SQL provides a way of renaming the attributes of a result relation. It uses the **as** clause, taking the form:

_old-name_ **as** _new-name_  

**3.4 Additional Basic Operations 75**

The **as** clause can appear in both the **select** and **from** clauses.4 For example, if we want the attribute name _name_ to be replaced with the name

_instructor name_, we can rewrite the preceding query as:

**select** _name_ **as** _instructor name_, _course id_ **from** _instructor_, _teaches_ **where** _instructor_._ID_\= _teaches_._ID_;

The **as** clause is particularly useful in renaming relations. One reason to rename a relation is to replace a long relation name with a shortened version that is more convenient to use elsewhere in the query. To illustrate, we rewrite the query “For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught.”

**select** _T_._name_, _S_._course id_ **from** _instructor_ **as** _T_, _teaches_ **as** _S_ **where** _T_._ID_\= _S_._ID_;

Another reason to rename a relation is a case where we wish to compare tuples in the same relation. We then need to take the Cartesian product of a relation with itself and, without renaming, it becomes impossible to distinguish one tuple from the other. Suppose that we want to write the query “Find the names of all instructors whose salary is greater than at least one instructor in the Biology department.” We can write the SQL expression:

**select distinct** _T_._name_ **from** _instructor_ **as** _T_, _instructor_ **as** _S_ **where** _T.salary > S.salary_ **and** _S.dept name_ \= ’Biology’;

Observe that we could not use the notation _instructor.salary_, since it would not be clear which reference to _instructor_ is intended.

In the above query, _T_ and _S_ can be thought of as copies of the relation _instructor_, but more precisely, they are declared as aliases, that is as alternative names, for the relation _instructor_. An identifier, such as _T_ and _S_, that is used to rename a relation is referred to as a **correlation name** in the SQL standard, but is also commonly referred to as a **table alias**, or a **correlation variable**, or a **tuple variable**.

Note that a better way to phrase the previous query in English would be “Find the names of all instructors who earn more than the lowest paid instructor in the Biology department.” Our original wording fits more closely with the SQL that we wrote, but the latter wording is more intuitive, and can in fact be expressed directly in SQL as we shall see in Section 3.8.2.

4Early versions of SQL did not include the keyword **as**. As a result, some implementations of SQL, notably Oracle, do not permit the keyword **as** in the from clause. In Oracle, “_old-name_ **as** _new-name_” is written instead as “_old-name new-name_” in the **from** clause. The keyword **as** is permitted for renaming attributes in the **select** clause, but it is optional and may be omitted in Oracle.  

**76 Chapter 3 Introduction to SQL**

**3.4.2 String Operations**

SQL specifies strings by enclosing them in single quotes, for example, ’Computer’. A single quote character that is part of a string can be specified by using two single quote characters; for example, the string “It’s right” can be specified by “It”s right”.

The SQL standard specifies that the equality operation on strings is case sen- sitive; as a result the expression “’comp. sci.’ = ’Comp. Sci.’” evaluates to false. However, some database systems, such as MySQL and SQL Server, do not distin- guish uppercase from lowercase when matching strings; as a result “’comp. sci.’ = ’Comp. Sci.’” would evaluate to true on these databases. This default behavior can, however, be changed, either at the database level or at the level of specific attributes.

SQL also permits a variety of functions on character strings, such as concate- nating (using “‖”), extracting substrings, finding the length of strings, converting strings to uppercase (using the function **upper**(_s_) where _s_ is a string) and low- ercase (using the function **lower**(_s_)), removing spaces at the end of the string (using **trim**(_s_)) and so on. There are variations on the exact set of string functions supported by different database systems. See your database system’s manual for more details on exactly what string functions it supports.

Pattern matching can be performed on strings, using the operator **like**. We describe patterns by using two special characters:

• Percent (%): The % character matches any substring.

• Underscore ( ): The character matches any character.

Patterns are case sensitive; that is, uppercase characters do not match lowercase characters, or vice versa. To illustrate pattern matching, we consider the following examples:

• ’Intro%’ matches any string beginning with “Intro”.

• ’%Comp%’ matches any string containing “Comp” as a substring, for exam- ple, ’Intro. to Computer Science’, and ’Computational Biology’.

• ’ ’ matches any string of exactly three characters.

• ’ %’ matches any string of at least three characters.

SQL expresses patterns by using the **like** comparison operator. Consider the query “Find the names of all departments whose building name includes the substring ‘Watson’.” This query can be written as:

**select** _dept name_ **from** _department_ **where** _building_ **like** ’%Watson%’;  

**3.4 Additional Basic Operations 77**

For patterns to include the special pattern characters (that is, % and ), SQL allows the specification of an escape character. The escape character is used immediately before a special pattern character to indicate that the special pattern character is to be treated like a normal character. We define the escape character for a **like** comparison using the **escape** keyword. To illustrate, consider the following patterns, which use a backslash (\\) as the escape character:

• **like** ’ab\\%cd%’ **escape** ’\\’ matches all strings beginning with “ab%cd”.

• **like** ’ab\\\\cd%’ **escape** ’\\’ matches all strings beginning with “ab\\cd”.

SQL allows us to search for mismatches instead of matches by using the **not like** comparison operator. Some databases provide variants of the **like** operation which do not distinguish lower and upper case.

SQL:1999 also offers a **similar to** operation, which provides more powerful pattern matching than the **like** operation; the syntax for specifying patterns is similar to that used in Unix regular expressions.

**3.4.3 Attribute Specification in Select Clause**

The asterisk symbol “ \* ” can be used in the **select** clause to denote “all attributes.” Thus, the use of _instructor_.\* in the **select** clause of the query:

**select** _instructor_.\* **from** _instructor_, _teaches_ **where** _instructor_._ID_\= _teaches_._ID_;

indicates that all attributes of _instructor_ are to be selected. A **select** clause of the form **select** \* indicates that all attributes of the result relation of the **from** clause are selected.

**3.4.4 Ordering the Display of Tuples**

SQL offers the user some control over the order in which tuples in a relation are displayed. The **order by** clause causes the tuples in the result of a query to appear in sorted order. To list in alphabetic order all instructors in the Physics department, we write:

**select** _name_ **from** _instructor_ **where** _dept name_ \= ’Physics’ **order by** _name_;

By default, the **order by** clause lists items in ascending order. To specify the sort order, we may specify **desc** for descending order or **asc** for ascending order. Furthermore, ordering can be performed on multiple attributes. Suppose that we wish to list the entire _instructor_ relation in descending order of _salary_. If several  

**78 Chapter 3 Introduction to SQL**

instructors have the same salary, we order them in ascending order by name. We express this query in SQL as follows:

**select** \* **from** _instructor_ **order by** _salary_ **desc**, _name_ **asc**;

**3.4.5 Where Clause Predicates**

SQL includes a **between** comparison operator to simplify **where** clauses that specify that a value be less than or equal to some value and greater than or equal to some other value. If we wish to find the names of instructors with salary amounts between $90,000 and $100,000, we can use the **between** comparison to write:

**select** _name_ **from** _instructor_ **where** _salary_ **between** 90000 **and** 100000;

instead of:

**select** _name_ **from** _instructor_ **where** _salary <_\= 100000 **and** _salary >_\= 90000;

Similarly, we can use the **not between** comparison operator. We can extend the preceding query that finds instructor names along with

course identifiers, which we saw earlier, and consider a more complicated case in which we require also that the instructors be from the Biology department: “Find the instructor names and the courses they taught for all instructors in the Biology department who have taught some course.” To write this query, we can modify either of the SQL queries we saw earlier, by adding an extra condition in the **where** clause. We show below the modified form of the SQL query that does not use natural join.

**select** _name_, _course id_ **from** _instructor_, _teaches_ **where** _instructor_._ID_\= _teaches_._ID_ **and** _dept name_ \= ’Biology’;

SQL permits us to use the notation (_v_1_, v_2_, . . . , vn_) to denote a tuple of arity _n_ containing values _v_1_, v_2_, . . . , vn_. The comparison operators can be used on tuples, and the ordering is defined lexicographically. For example, (_a_1_, a_2) _<_\= (_b_1_, b_2)  

**3.5 Set Operations 79**

_course id_

CS-101 CS-347 PHY-101

**Figure 3.9** The _c1_ relation, listing courses taught in Fall 2009.

is true if _a_1 _<_\= _b_1 **and** _a_2 _<_\= _b_2; similarly, the two tuples are equal if all their attributes are equal. Thus, the preceding SQL query can be rewritten as follows:5

**select** _name_, _course id_ **from** _instructor_, _teaches_ **where** (_instructor_._ID_, _dept name_) = (_teaches_._ID_, ’Biology’);

**3.5 Set Operations**

The SQL operations **union**, **intersect**, and **except** operate on relations and cor- respond to the mathematical set-theory operations ∪, ∩, and −. We shall now construct queries involving the **union**, **intersect**, and **except** operations over two sets.

• The set of all courses taught in the Fall 2009 semester:

**select** _course id_ **from** _section_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009;

• The set of all courses taught in the Spring 2010 semester:

**select** _course id_ **from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010;

In our discussion that follows, we shall refer to the relations obtained as the result of the preceding queries as _c1_ and _c2_, respectively, and show the results when these queries are run on the _section_ relation of Figure 2.6 in Figures 3.9 and 3.10. Observe that _c2_ contains two tuples corresponding to _course id_ CS-319, since two sections of the course have been offered in Spring 2010.

5Although it is part of the SQL-92 standard, some SQL implementations may not support this syntax.  

**80 Chapter 3 Introduction to SQL**

_course id_ CS-101 CS-315 CS-319 CS-319 FIN-201 HIS-351 MU-199

**Figure 3.10** The _c2_ relation, listing courses taught in Spring 2010.

**3.5.1 The Union Operation**

To find the set of all courses taught either in Fall 2009 or in Spring 2010, or both, we write:6

(**select** _course id_ **from** _section_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009) **union** (**select** _course id_ **from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The **union** operation automatically eliminates duplicates, unlike the **select** clause. Thus, using the _section_ relation of Figure 2.6, where two sections of CS-319 are offered in Spring 2010, and a section of CS-101 is offered in the Fall 2009 as well as in the Fall 2010 semester, CS-101 and CS-319 appear only once in the result, shown in Figure 3.11.

If we want to retain all duplicates, we must write **union all** in place of **union**:

(**select** _course id_ **from** _section_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009) **union all** (**select** _course id_ **from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The number of duplicate tuples in the result is equal to the total number of duplicates that appear in both _c1_ and _c2_. So, in the above query, each of CS-319 and CS-101 would be listed twice. As a further example, if it were the case that 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101

6The parentheses we include around each **select**\-**from**\-**where** statement are optional, but useful for ease of reading.  

**3.5 Set Operations 81**

_course id_

CS-101 CS-315 CS-319 CS-347 FIN-201 HIS-351 MU-199 PHY-101

**Figure 3.11** The result relation for _c1_ union _c2_.

were taught in the Fall 2010 semester, then there would be 6 tuples with ECE-101 in the result.

**3.5.2 The Intersect Operation**

To find the set of all courses taught in the Fall 2009 as well as in Spring 2010 we write:

(**select** _course id_ **from** _section_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009)

**intersect** (**select** _course id_ **from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The result relation, shown in Figure 3.12, contains only one tuple with CS-101. The **intersect** operation automatically eliminates duplicates. For example, if it were the case that 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101 were taught in the Spring 2010 semester, then there would be only 1 tuple with ECE-101 in the result.

If we want to retain all duplicates, we must write **intersect all** in place of **intersect**:

_course id_

CS-101

**Figure 3.12** The result relation for _c1_ intersect _c2_.  

**82 Chapter 3 Introduction to SQL**

(**select** _course id_ **from** _section_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009) **intersect all** (**select** _course id_ **from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The number of duplicate tuples that appear in the result is equal to the minimum number of duplicates in both _c1_ and _c2_. For example, if 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101 were taught in the Spring 2010 semester, then there would be 2 tuples with ECE-101 in the result.

**3.5.3 The Except Operation**

To find all courses taught in the Fall 2009 semester but not in the Spring 2010 semester, we write:

(**select** _course id_ **from** _section_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009) **except** (**select** _course id_ **from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The result of this query is shown in Figure 3.13. Note that this is exactly relation _c1_ of Figure 3.9 except that the tuple for CS-101 does not appear. The **except** operation7 outputs all tuples from its first input that do not occur in the second input; that is, it performs set difference. The operation automatically eliminates duplicates in the inputs before performing set difference. For example, if 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101 were taught in the Spring 2010 semester, the result of the **except** operation would not have any copy of ECE-101.

If we want to retain duplicates, we must write **except all** in place of **except**:

(**select** _course id_ **from** _section_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009) **except all** (**select** _course id_ **from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010);

7Some SQL implementations, notably Oracle, use the keyword **minus** in place of **except**.  

**3.6 Null Values 83**

_course id_

CS-347 PHY-101

**Figure 3.13** The result relation for _c1_ except _c2_.

The number of duplicate copies of a tuple in the result is equal to the number of duplicate copies in _c1_ minus the number of duplicate copies in _c2_, provided that the difference is positive. Thus, if 4 sections of ECE-101 were taught in the Fall 2009 semester and 2 sections of ECE-101 were taught in Spring 2010, then there are 2 tuples with ECE-101 in the result. If, however, there were two or fewer sections of ECE-101 in the the Fall 2009 semester, and two sections of ECE-101 in the Spring 2010 semester, there is no tuple with ECE-101 in the result.

**3.6 Null Values**

Null values present special problems in relational operations, including arith- metic operations, comparison operations, and set operations.

The result of an arithmetic expression (involving, for example +, −, ∗, or _/_) is null if any of the input values is null. For example, if a query has an expression _r.A_\+ 5, and _r.A_ is null for a particular tuple, then the expression result must also be null for that tuple.

Comparisons involving nulls are more of a problem. For example, consider the comparison “1 _<_ **null**”. It would be wrong to say this is true since we do not know what the null value represents. But it would likewise be wrong to claim this expression is false; if we did, “**not** (1 _<_ **null**)” would evaluate to true, which does not make sense. SQL therefore treats as **unknown** the result of any comparison involving a _null_ value (other than predicates **is null** and **is not null**, which are described later in this section). This creates a third logical value in addition to _true_ and _false_.

Since the predicate in a **where** clause can involve Boolean operations such as **and**, **or**, and **not** on the results of comparisons, the definitions of the Boolean operations are extended to deal with the value **unknown**.

• **and**: The result of _true_ **and** _unknown_ is _unknown_, _false_ **and** _unknown_ is _false_, while _unknown_ **and** _unknown_ is _unknown_.

• **or**: The result of _true_ **or** _unknown_ is _true_, _false_ **or** _unknown_ is _unknown_, while _unknown_ **or** _unknown_ is _unknown_.

• **not**: The result of **not** _unknown_ is _unknown_.

You can verify that if _r.A_ is null, then “1 _< r.A_” as well as “**not** (1 _< r.A_)” evaluate to unknown.  

**84 Chapter 3 Introduction to SQL**

If the **where** clause predicate evaluates to either **false** or **unknown** for a tuple, that tuple is not added to the result.

SQL uses the special keyword **null** in a predicate to test for a null value. Thus, to find all instructors who appear in the _instructor_ relation with null values for _salary_, we write:

**select** _name_ **from** _instructor_ **where** _salary_ **is null**;

The predicate **is not null** succeeds if the value on which it is applied is not null. Some implementations of SQL also allow us to test whether the result of a com-

parison is unknown, rather than true or false, by using the clauses **is unknown** and **is not unknown**.

When a query uses the **select distinct** clause, duplicate tuples must be elim- inated. For this purpose, when comparing values of corresponding attributes from two tuples, the values are treated as identical if either both are non-null and equal in value, or both are null. Thus two copies of a tuple, such as _{_(’A’,null), (’A’,null)_}_, are treated as being identical, even if some of the attributes have a null value. Using the **distinct** clause then retains only one copy of such identical tuples. Note that the treatment of null above is different from the way nulls are treated in predicates, where a comparison “null=null” would return unknown, rather than true.

The above approach of treating tuples as identical if they have the same values for all attributes, even if some of the values are null, is also used for the set operations union, intersection and except.

**3.7 Aggregate Functions**

_Aggregate functions_ are functions that take a collection (a set or multiset) of values as input and return a single value. SQL offers five built-in aggregate functions:

• Average: **avg**

• Minimum: **min**

• Maximum: **max**

• Total: **sum**

• Count: **count**

The input to **sum** and **avg** must be a collection of numbers, but the other operators can operate on collections of nonnumeric data types, such as strings, as well.  

**3.7 Aggregate Functions 85**

**3.7.1 Basic Aggregation**

Consider the query “Find the average salary of instructors in the Computer Sci- ence department.” We write this query as follows:

**select avg** (_salary_) **from** _instructor_ **where** _dept name_\= ’Comp. Sci.’;

The result of this query is a relation with a single attribute, containing a single tuple with a numerical value corresponding to the average salary of instructors in the Computer Science department. The database system may give an arbitrary name to the result relation attribute that is generated by aggregation; however, we can give a meaningful name to the attribute by using the **as** clause as follows:

**select avg** (_salary_) **as** _avg salary_ **from** _instructor_ **where** _dept name_\= ’Comp. Sci.’;

In the _instructor_ relation of Figure 2.1, the salaries in the Computer Science department are $75,000, $65,000, and $92,000. The average balance is $232,000_/_3 = $77,333.33.

Retaining duplicates is important in computing an average. Suppose the Computer Science department adds a fourth instructor whose salary happens to be $75,000. If duplicates were eliminated, we would obtain the wrong answer ($232,000_/_4 = $58.000) rather than the correct answer of $76,750.

There are cases where we must eliminate duplicates before computing an aggregate function. If we do want to eliminate duplicates, we use the keyword **distinct** in the aggregate expression. An example arises in the query “Find the total number of instructors who teach a course in the Spring 2010 semester.” In this case, an instructor counts only once, regardless of the number of course sections that the instructor teaches. The required information is contained in the relation _teaches_, and we write this query as follows:

**select count** (**distinct** _ID_) **from** _teaches_ **where** _semester_ \= ’Spring’ **and** _year_ \= 2010;

Because of the keyword **distinct** preceding _ID_, even if an instructor teaches more than one course, she is counted only once in the result.

We use the aggregate function **count** frequently to count the number of tuples in a relation. The notation for this function in SQL is **count** (\*). Thus, to find the number of tuples in the _course_ relation, we write

**select count** (\*) **from** _course_;  

**86 Chapter 3 Introduction to SQL**

_ID name dept name salary_

76766 Crick Biology 72000 45565 Katz Comp. Sci. 75000 10101 Srinivasan Comp. Sci. 65000 83821 Brandt Comp. Sci. 92000 98345 Kim Elec. Eng. 80000 12121 Wu Finance 90000 76543 Singh Finance 80000 32343 El Said History 60000 58583 Califieri History 62000 15151 Mozart Music 40000 33456 Gold Physics 87000 22222 Einstein Physics 95000

**Figure 3.14** Tuples of the _instructor_ relation, grouped by the _dept name_ attribute.

SQL does not allow the use of **distinct** with **count** (\*). It is legal to use **distinct** with **max** and **min**, even though the result does not change. We can use the keyword **all** in place of **distinct** to specify duplicate retention, but, since **all** is the default, there is no need to do so.

**3.7.2 Aggregation with Grouping**

There are circumstances where we would like to apply the aggregate function not only to a single set of tuples, but also to a group of sets of tuples; we specify this wish in SQL using the **group by** clause. The attribute or attributes given in the **group by** clause are used to form groups. Tuples with the same value on all attributes in the **group by** clause are placed in one group.

As an illustration, consider the query “Find the average salary in each depart- ment.” We write this query as follows:

**select** _dept name_, **avg** (_salary_) **as** _avg salary_ **from** _instructor_ **group by** _dept name_;

Figure 3.14 shows the tuples in the _instructor_ relation grouped by the _dept name_ attribute, which is the first step in computing the query result. The specified

aggregate is computed for each group, and the result of the query is shown in Figure 3.15.

In contrast, consider the query “Find the average salary of all instructors.” We write this query as follows:

**select avg** (_salary_) **from** _instructor_;  

**3.7 Aggregate Functions 87**

_dept name avg salary_

Biology 72000 Comp. Sci. 77333 Elec. Eng. 80000 Finance 85000 History 61000 Music 40000 Physics 91000

**Figure 3.15** The result relation for the query “Find the average salary in each department”.

In this case the **group by** clause has been omitted, so the entire relation is treated as a single group.

As another example of aggregation on groups of tuples, consider the query “Find the number of instructors in each department who teach a course in the Spring 2010 semester.” Information about which instructors teach which course sections in which semester is available in the _teaches_ relation. However, this in- formation has to be joined with information from the _instructor_ relation to get the department name of each instructor. Thus, we write this query as follows:

**select** _dept name_, **count** (**distinct** _ID_) **as** _instr count_ **from** _instructor_ **natural join** _teaches_ **where** _semester_ \= ’Spring’ **and** _year_ \= 2010 **group by** _dept name_;

The result is shown in Figure 3.16. When an SQL query uses grouping, it is important to ensure that the only

attributes that appear in the **select** statement without being aggregated are those that are present in the **group by** clause. In other words, any attribute that is not present in the **group by** clause must appear only inside an aggregate function if it appears in the **select** clause, otherwise the query is treated as erroneous. For example, the following query is erroneous since _ID_ does not appear in the **group by** clause, and yet it appears in the **select** clause without being aggregated:

_dept name instr count_

Comp. Sci. 3 Finance 1 History 1 Music 1

**Figure 3.16** The result relation for the query “Find the number of instructors in each department who teach a course in the Spring 2010 semester.”  

**88 Chapter 3 Introduction to SQL**

/\* erroneous query \*/ **select** _dept name_, _ID_, **avg** (_salary_) **from** _instructor_ **group by** _dept name_;

Each instructor in a particular group (defined by _dept name_) can have a different _ID_, and since only one tuple is output for each group, there is no unique way of choosing which _ID_ value to output. As a result, such cases are disallowed by SQL.

**3.7.3 The Having Clause**

At times, it is useful to state a condition that applies to groups rather than to tuples. For example, we might be interested in only those departments where the average salary of the instructors is more than $42,000. This condition does not apply to a single tuple; rather, it applies to each group constructed by the **group by** clause. To express such a query, we use the **having** clause of SQL. SQL applies predicates in the **having** clause after groups have been formed, so aggregate functions may be used. We express this query in SQL as follows:

**select** _dept name_, **avg** (_salary_) **as** _avg salary_ **from** _instructor_ **group by** _dept name_ **having avg** (_salary_) _\>_ 42000;

The result is shown in Figure 3.17. As was the case for the **select** clause, any attribute that is present in the **having**

clause without being aggregated must appear in the **group by** clause, otherwise the query is treated as erroneous.

The meaning of a query containing aggregation, **group by**, or **having** clauses is defined by the following sequence of operations:

**1\.** As was the case for queries without aggregation, the **from** clause is first evaluated to get a relation.

_dept name avg_(_avg salary_)

Physics 91000 Elec. Eng. 80000 Finance 85000 Comp. Sci. 77333 Biology 72000 History 61000

**Figure 3.17** The result relation for the query “Find the average salary of instructors in those departments where the average salary is more than $42,000.”  

**3.7 Aggregate Functions 89**

**2\.** If a **where** clause is present, the predicate in the **where** clause is applied on the result relation of the **from** clause.

**3\.** Tuples satisfying the **where** predicate are then placed into groups by the **group by** clause if it is present. If the **group by** clause is absent, the entire set of tuples satisfying the **where** predicate is treated as being in one group.

**4\.** The **having** clause, if it is present, is applied to each group; the groups that do not satisfy the **having** clause predicate are removed.

**5\.** The **select** clause uses the remaining groups to generate tuples of the result of the query, applying the aggregate functions to get a single result tuple for each group.

To illustrate the use of both a **having** clause and a **where** clause in the same query, we consider the query “For each course section offered in 2009, find the average total credits (_tot cred_) of all students enrolled in the section, if the section had at least 2 students.”

**select** _course id_, _semester_, _year_, _sec id_, **avg** (_tot cred_) **from** _takes_ **natural join** _student_ **where** _year_ \= 2009 **group by** _course id_, _semester_, _year_, _sec id_ **having count** (_ID_) _\>_\= 2;

Note that all the required information for the preceding query is available from the relations _takes_ and _student_, and that although the query pertains to sections, a join with _section_ is not needed.

**3.7.4 Aggregation with Null and Boolean Values**

Null values, when they exist, complicate the processing of aggregate operators. For example, assume that some tuples in the _instructor_ relation have a null value for _salary_. Consider the following query to total all salary amounts:

**select sum** (_salary_) **from** _instructor_;

The values to be summed in the preceding query include null values, since some tuples have a null value for _salary_. Rather than say that the overall sum is itself _null_, the SQL standard says that the **sum** operator should ignore _null_ values in its input.

In general, aggregate functions treat nulls according to the following rule: All aggregate functions except **count (\*)** ignore null values in their input collection. As a result of null values being ignored, the collection of values may be empty. The **count** of an empty collection is defined to be 0, and all other aggregate operations  

**90 Chapter 3 Introduction to SQL**

return a value of null when applied on an empty collection. The effect of null values on some of the more complicated SQL constructs can be subtle.

A **Boolean** data type that can take values **true**, **false**, and **unknown**, was introduced in SQL:1999. The aggregate functions **some** and **every**, which mean exactly what you would intuitively expect, can be applied on a collection of Boolean values.

**3.8 Nested Subqueries**

SQL provides a mechanism for nesting subqueries. A subquery is a **select**\-**from**\- **where** expression that is nested within another query. A common use of sub- queries is to perform tests for set membership, make set comparisons, and deter- mine set cardinality, by nesting subqueries in the **where** clause. We study such uses of nested subqueries in the **where** clause in Sections 3.8.1 through 3.8.4. In Section 3.8.5, we study nesting of subqueries in the **from** clause. In Section 3.8.7, we see how a class of subqueries called scalar subqueries can appear wherever an expression returning a value can occur.

**3.8.1 Set Membership**

SQL allows testing tuples for membership in a relation. The **in** connective tests for set membership, where the set is a collection of values produced by a **select** clause. The **not in** connective tests for the absence of set membership.

As an illustration, reconsider the query “Find all the courses taught in the both the Fall 2009 and Spring 2010 semesters.” Earlier, we wrote such a query by intersecting two sets: the set of courses taught in Fall 2009 and the set of courses taught in Spring 2010. We can take the alternative approach of finding all courses that were taught in Fall 2009 and that are also members of the set of courses taught in Spring 2010. Clearly, this formulation generates the same results as the previous one did, but it leads us to write our query using the **in** connective of SQL. We begin by finding all courses taught in Spring 2010, and we write the subquery

(**select** _course id_ **from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010)

We then need to find those courses that were taught in the Fall 2009 and that appear in the set of courses obtained in the subquery. We do so by nesting the subquery in the **where** clause of an outer query. The resulting query is

**select distinct** _course id_ **from** _section_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009 **and**

_course id_ **in** (**select** _course id_ **from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010);  

**3.8 Nested Subqueries 91**

This example shows that it is possible to write the same query several ways in SQL. This flexibility is beneficial, since it allows a user to think about the query in the way that seems most natural. We shall see that there is a substantial amount of redundancy in SQL.

We use the **not in** construct in a way similar to the **in** construct. For example, to find all the courses taught in the Fall 2009 semester but not in the Spring 2010 semester, we can write:

**select distinct** _course id_ **from** _section_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009 **and** _course id_ **not in** (**select** _course id_

**from** _section_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010);

The **in** and **not in** operators can also be used on enumerated sets. The follow- ing query selects the names of instructors whose names are neither “Mozart” nor “Einstein”.

**select distinct** _name_ **from** _instructor_ **where** _name_ **not in** (’Mozart’, ’Einstein’);

In the preceding examples, we tested membership in a one-attribute relation. It is also possible to test for membership in an arbitrary relation in SQL. For example, we can write the query “find the total number of (distinct) students who have taken course sections taught by the instructor with _ID_ 110011” as follows:

**select count** (**distinct** _ID_) **from** _takes_ **where** (_course id_, _sec id_, _semester_, _year_) **in** (**select** _course id_, _sec id_, _semester_, _year_

**from** _teaches_ **where** _teaches_._ID_\= 10101);

**3.8.2 Set Comparison**

As an example of the ability of a nested subquery to compare sets, consider the query “Find the names of all instructors whose salary is greater than at least one instructor in the Biology department.” In Section 3.4.1, we wrote this query as follows:

**select distinct** _T_._name_ **from** _instructor_ **as** _T_, _instructor_ **as** _S_ **where** _T.salary > S.salary_ **and** _S.dept name_ \= ’Biology’;  

**92 Chapter 3 Introduction to SQL**

SQL does, however, offer an alternative style for writing the preceding query. The phrase “greater than at least one” is represented in SQL by _\>_ **some**. This construct allows us to rewrite the query in a form that resembles closely our formulation of the query in English.

**select** _name_ **from** _instructor_ **where** _salary >_ **some** (**select** _salary_

**from** _instructor_ **where** _dept name_ \= ’Biology’);

The subquery:

(**select** _salary_ **from** _instructor_ **where** _dept name_ \= ’Biology’)

generates the set of all salary values of all instructors in the Biology department. The _\>_ **some** comparison in the **where** clause of the outer **select** is true if the _salary_ value of the tuple is greater than at least one member of the set of all salary values for instructors in Biology.

SQL also allows _<_ **some**, _<_\= **some**, _\>_\= **some**, = **some**, and _<>_ **some** com- parisons. As an exercise, verify that = **some** is identical to **in**, whereas _<>_ **some** is _not_ the same as **not in**.8

Now we modify our query slightly. Let us find the names of all instructors that have a salary value greater than that of each instructor in the Biology depart- ment. The construct _\>_ **all** corresponds to the phrase “greater than all.” Using this construct, we write the query as follows:

**select** _name_ **from** _instructor_ **where** _salary >_ **all** (**select** _salary_

**from** _instructor_ **where** _dept name_ \= ’Biology’);

As it does for **some**, SQL also allows _<_ **all**, _<_\= **all**, _\>_\= **all**, = **all**, and _<>_ **all** comparisons. As an exercise, verify that _<>_ **all** is identical to **not in**, whereas = **all** is _not_ the same as **in**.

As another example of set comparisons, consider the query “Find the depart- ments that have the highest average salary.” We begin by writing a query to find all average salaries, and then nest it as a subquery of a larger query that finds

8The keyword **any** is synonymous to **some** in SQL. Early versions of SQL allowed only **any**. Later versions added the alternative **some** to avoid the linguistic ambiguity of the word _any_ in English.  

**3.8 Nested Subqueries 93**

those departments for which the average salary is greater than or equal to all average salaries:

**select** _dept name_ **from** _instructor_ **group by** _dept name_ **having avg** (_salary_) _\>_\= **all** (**select avg** (_salary_)

**from** _instructor_ **group by** _dept name_);

**3.8.3 Test for Empty Relations**

SQL includes a feature for testing whether a subquery has any tuples in its result. The **exists** construct returns the value **true** if the argument subquery is nonempty. Using the **exists** construct, we can write the query “Find all courses taught in both the Fall 2009 semester and in the Spring 2010 semester” in still another way:

**select** _course id_ **from** _section_ **as** _S_ **where** _semester_ \= ’Fall’ **and** _year_\= 2009 **and**

**exists** (**select** \* **from** _section_ **as** _T_ **where** _semester_ \= ’Spring’ **and** _year_\= 2010 **and**

_S_._course id_\= _T_._course id_);

The above query also illustrates a feature of SQL where a correlation name from an outer query (_S_ in the above query), can be used in a subquery in the **where** clause. A subquery that uses a correlation name from an outer query is called a **correlated subquery**.

In queries that contain subqueries, a scoping rule applies for correlation names. In a subquery, according to the rule, it is legal to use only correlation names defined in the subquery itself or in any query that contains the subquery. If a correlation name is defined both locally in a subquery and globally in a containing query, the local definition applies. This rule is analogous to the usual scoping rules used for variables in programming languages.

We can test for the nonexistence of tuples in a subquery by using the **not exists** construct. We can use the **not exists** construct to simulate the set containment (that is, superset) operation: We can write “relation _A_contains relation _B_” as “**not exists** (_B_ **except** _A_).” (Although it is not part of the current SQL standards, the **contains** operator was present in some early relational systems.) To illustrate the **not exists** operator, consider the query “Find all students who have taken all courses offered in the Biology department.” Using the **except** construct, we can write the query as follows:  

**94 Chapter 3 Introduction to SQL**

**select distinct** _S_._ID_, _S_._name_ **from** _student_ **as** _S_ **where not exists** ((**select** _course id_

**from** _course_ **where** _dept name_ \= ’Biology’) **except** (**select** _T_._course id_ **from** _takes_ **as** _T_ **where** _S_._ID_ \= _T_._ID_));

Here, the subquery:

(**select** _course id_ **from** _course_ **where** _dept name_ \= ’Biology’)

finds the set of all courses offered in the Biology department. The subquery:

(**select** _T_._course id_ **from** _takes_ **as** _T_ **where** _S_._ID_ \= _T_._ID_)

finds all the courses that student _S_._ID_ has taken. Thus, the outer **select** takes each student and tests whether the set of all courses that the student has taken contains the set of all courses offered in the Biology department.

**3.8.4 Test for the Absence of Duplicate Tuples**

SQL includes a boolean function for testing whether a subquery has duplicate tuples in its result. The **unique** construct9 returns the value **true** if the argument subquery contains no duplicate tuples. Using the **unique** construct, we can write the query “Find all courses that were offered at most once in 2009” as follows:

**select** _T_._course id_ **from** _course_ **as** _T_ **where unique** (**select** _R_._course id_

**from** _section_ **as** _R_ **where** _T_._course id_\= _R_._course id_ **and**

_R_._year_ \= 2009);

Note that if a course is not offered in 2009, the subquery would return an empty result, and the **unique** predicate would evaluate to true on the empty set.

An equivalent version of the above query not using the **unique** construct is:

9This construct is not yet widely implemented.  

**3.8 Nested Subqueries 95**

**select** _T_._course id_ **from** _course_ **as** _T_ **where** 1 _<_\= (**select count**(_R_._course id_)

**from** _section_ **as** _R_ **where** _T_._course id_\= _R_._course id_ **and**

_R_._year_ \= 2009);

We can test for the existence of duplicate tuples in a subquery by using the **not unique** construct. To illustrate this construct, consider the query “Find all courses that were offered at least twice in 2009” as follows:

**select** _T_._course id_ **from** _course_ **as** _T_ **where not unique** (**select** _R_._course id_

**from** _section_ **as** _R_ **where** _T_._course id_\= _R_._course id_ **and**

_R_._year_ \= 2009);

Formally, the **unique** test on a relation is defined to fail if and only if the relation contains two tuples _t_1 and _t_2 such that _t_1 = _t_2\. Since the test _t_1 = _t_2 fails if any of the fields of _t_1 or _t_2 are null, it is possible for **unique** to be true even if there are multiple copies of a tuple, as long as at least one of the attributes of the tuple is null.

**3.8.5 Subqueries in the From Clause**

SQL allows a subquery expression to be used in the **from** clause. The key concept applied here is that any **select**\-**from**\-**where** expression returns a relation as a result and, therefore, can be inserted into another **select**\-**from**\-**where** anywhere that a relation can appear.

Consider the query “Find the average instructors’ salaries of those depart- ments where the average salary is greater than $42,000.” We wrote this query in Section 3.7 by using the **having** clause. We can now rewrite this query, without using the **having** clause, by using a subquery in the **from** clause, as follows:

**select** _dept name_, _avg salary_ **from** (**select** _dept name_, **avg** (_salary_) **as** _avg salary_

**from** _instructor_ **group by** _dept name_)

**where** _avg salary >_ 42000;

The subquery generates a relation consisting of the names of all departments and their corresponding average instructors’ salaries. The attributes of the subquery result can be used in the outer query, as can be seen in the above example.  

**96 Chapter 3 Introduction to SQL**

Note that we do not need to use the **having** clause, since the subquery in the **from** clause computes the average salary, and the predicate that was in the **having** clause earlier is now in the **where** clause of the outer query.

We can give the subquery result relation a name, and rename the attributes, using the **as** clause, as illustrated below.

**select** _dept name_, _avg salary_ **from** (**select** _dept name_, **avg** (_salary_)

**from** _instructor_ **group by** _dept name_) **as** _dept avg_ (_dept name_, _avg salary_)

**where** _avg salary >_ 42000;

The subquery result relation is named _dept avg_, with the attributes _dept name_ and _avg salary_.

Nested subqueries in the **from** clause are supported by most but not all SQL implementations. However, some SQL implementations, notably Oracle, do not support renaming of the result relation in the **from** clause.

As another example, suppose we wish to find the maximum across all de- partments of the total salary at each department. The **having** clause does not help us in this task, but we can write this query easily by using a subquery in the **from** clause, as follows:

**select max** (_tot salary_) **from** (**select** _dept name_, **sum**(_salary_)

**from** _instructor_ **group by** _dept name_) **as** _dept total_ (_dept name_, _tot salary_);

We note that nested subqueries in the **from** clause cannot use correlation variables from other relations in the **from** clause. However, SQL:2003 allows a subquery in the **from** clause that is prefixed by the **lateral** keyword to access attributes of preceding tables or subqueries in the **from** clause. For example, if we wish to print the names of each instructor, along with their salary and the average salary in their department, we could write the query as follows:

**select** _name_, _salary_, _avg salary_ **from** _instructor I1_, **lateral** (**select avg**(_salary_) as _avg salary_

**from** _instructor I2_ **where** _I2_._dept name_\= _I1_._dept name_);

Without the **lateral** clause, the subquery cannot access the correlation variable _I1_ from the outer query. Currently, only a few SQL implementations, such as IBM DB2, support the **lateral** clause.  

**3.8 Nested Subqueries 97**

**3.8.6 The with Clause**

The **with** clause provides a way of defining a temporary relation whose definition is available only to the query in which the **with** clause occurs. Consider the following query, which finds those departments with the maximum budget.

**with** _max budget_ (_value_) **as** (**select max**(_budget_) **from** _department_)

**select** _budget_ **from** _department_, _max budget_ **where** _department_._budget_ \= _max budget.value_;

The **with** clause defines the temporary relation _max budget_, which is used in the immediately following query. The **with** clause, introduced in SQL:1999, is supported by many, but not all, database systems.

We could have written the above query by using a nested subquery in either the **from** clause or the **where** clause. However, using nested subqueries would have made the query harder to read and understand. The **with** clause makes the query logic clearer; it also permits a view definition to be used in multiple places within a query.

For example, suppose we want to find all departments where the total salary is greater than the average of the total salary at all departments. We can write the query using the **with** clause as follows.

**with** _dept total_ (_dept name_, _value_) **as** (**select** _dept name_, **sum**(_salary_) **from** _instructor_ **group by** _dept name_),

_dept total avg_(_value_) **as** (**select avg**(_value_) **from** _dept total_)

**select** _dept name_ **from** _dept total_, _dept total avg_ **where** _dept total.value >_\= _dept total avg.value_;

We can, of course, create an equivalent query without the **with** clause, but it would be more complicated and harder to understand. You can write the equivalent query as an exercise.

**3.8.7 Scalar Subqueries**

SQL allows subqueries to occur wherever an expression returning a value is permitted, provided the subquery returns only one tuple containing a single attribute; such subqueries are called **scalar subqueries**. For example, a subquery  

**98 Chapter 3 Introduction to SQL**

can be used in the **select** clause as illustrated in the following example that lists all departments along with the number of instructors in each department:

**select** _dept name_, (**select count**(\*) **from** _instructor_ **where** _department_._dept name_ \= _instructor_._dept name_)

**as** _num instructors_ **from** _department_;

The subquery in the above example is guaranteed to return only a single value since it has a **count**(\*) aggregate without a **group by**. The example also illustrates the usage of correlation variables, that is, attributes of relations in the **from** clause of the outer query, such as _department_._dept name_ in the above example.

Scalar subqueries can occur in **select**, **where**, and **having** clauses. Scalar sub- queries may also be defined without aggregates. It is not always possible to figure out at compile time if a subquery can return more than one tuple in its result; if the result has more than one tuple when the subquery is executed, a run-time error occurs.

Note that technically the type of a scalar subquery result is still a relation, even if it contains a single tuple. However, when a scalar subquery is used in an expression where a value is expected, SQL implicitly extracts the value from the single attribute of the single tuple in the relation, and returns that value.

**3.9 Modification of the Database**

We have restricted our attention until now to the extraction of information from the database. Now, we show how to add, remove, or change information with SQL.

**3.9.1 Deletion**

A delete request is expressed in much the same way as a query. We can delete only whole tuples; we cannot delete values on only particular attributes. SQL expresses a deletion by

**delete from** _r_ **where** _P_;

where _P_ represents a predicate and _r_ represents a relation. The **delete** statement first finds all tuples _t_ in _r_ for which _P_(_t_) is true, and then deletes them from _r_. The **where** clause can be omitted, in which case all tuples in _r_ are deleted.

Note that a **delete** command operates on only one relation. If we want to delete tuples from several relations, we must use one **delete** command for each relation. The predicate in the **where** clause may be as complex as a **select** command’s **where** clause. At the other extreme, the **where** clause may be empty. The request  

**3.9 Modification of the Database 99**

**delete from** _instructor_;

deletes all tuples from the _instructor_ relation. The _instructor_ relation itself still exists, but it is empty.

Here are examples of SQL delete requests:

• Delete all tuples in the _instructor_ relation pertaining to instructors in the Finance department.

**delete from** _instructor_ **where** _dept name_\= ’Finance’;

• Delete all instructors with a salary between $13,000 and $15,000.

**delete from** _instructor_ **where** _salary_ **between** 13000 **and** 15000;

• Delete all tuples in the _instructor_ relation for those instructors associated with a department located in the Watson building.

**delete from** _instructor_ **where** _dept name_ **in** (**select** _dept name_

**from** _department_ **where** _building_ \= ’Watson’);

This **delete** request first finds all departments located in Watson, and then deletes all _instructor_ tuples pertaining to those departments.

Note that, although we may delete tuples from only one relation at a time, we may reference any number of relations in a **select-from-where** nested in the **where** clause of a **delete**. The **delete** request can contain a nested **select** that references the relation from which tuples are to be deleted. For example, suppose that we want to delete the records of all instructors with salary below the average at the university. We could write:

**delete from** _instructor_ **where** _salary<_ (**select avg** (_salary_)

**from** _instructor_);

The **delete** statement first tests each tuple in the relation _instructor_ to check whether the salary is less than the average salary of instructors in the univer- sity. Then, all tuples that fail the test—that is, represent an instructor with a lower-than-average salary—are deleted. Performing all the tests before perform- ing any deletion is important—if some tuples are deleted before other tuples  

**100 Chapter 3 Introduction to SQL**

have been tested, the average salary may change, and the final result of the **delete** would depend on the order in which the tuples were processed!

**3.9.2 Insertion**

To insert data into a relation, we either specify a tuple to be inserted or write a query whose result is a set of tuples to be inserted. Obviously, the attribute values for inserted tuples must be members of the corresponding attribute’s domain. Similarly, tuples inserted must have the correct number of attributes.

The simplest **insert** statement is a request to insert one tuple. Suppose that we wish to insert the fact that there is a course CS-437 in the Computer Science department with title “Database Systems”, and 4 credit hours. We write:

**insert into** _course_ **values** (’CS-437’, ’Database Systems’, ’Comp. Sci.’, 4);

In this example, the values are specified in the order in which the corresponding attributes are listed in the relation schema. For the benefit of users who may not remember the order of the attributes, SQL allows the attributes to be specified as part of the **insert** statement. For example, the following SQL **insert** statements are identical in function to the preceding one:

**insert into** _course_ (_course id_, _title_, _dept name_, _credits_) **values** (’CS-437’, ’Database Systems’, ’Comp. Sci.’, 4);

**insert into** _course_ (_title_, _course id_, _credits_, _dept name_) **values** (’Database Systems’, ’CS-437’, 4, ’Comp. Sci.’);

More generally, we might want to insert tuples on the basis of the result of a query. Suppose that we want to make each student in the Music department who has earned more than 144 credit hours, an instructor in the Music department, with a salary of $18,000. We write:

**insert into** _instructor_ **select** _ID_, _name_, _dept name_, 18000 **from** _student_ **where** _dept name_ \= ’Music’ **and** _tot cred >_ 144;

Instead of specifying a tuple as we did earlier in this section, we use a **select** to specify a set of tuples. SQL evaluates the **select** statement first, giving a set of tuples that is then inserted into the _instructor_ relation. Each tuple has an _ID_, a _name_, a _dept name_ (Music), and an salary of $18,000.

It is important that we evaluate the **select** statement fully before we carry out any insertions. If we carry out some insertions even as the **select** statement is being evaluated, a request such as:  

**3.9 Modification of the Database 101**

**insert into** _student_ **select** \* **from** _student_;

might insert an infinite number of tuples, if the primary key constraint on _student_ were absent. Without the primary key constraint, the request would insert the first tuple in _student_ again, creating a second copy of the tuple. Since this second copy is part of _student_ now, the **select** statement may find it, and a third copy would be inserted into _student_. The **select** statement may then find this third copy and insert a fourth copy, and so on, forever. Evaluating the **select** statement completely before performing insertions avoids such problems. Thus, the above **insert** statement would simply duplicate every tuple in the _student_ relation, if the relation did not have a primary key constraint.

Our discussion of the **insert** statement considered only examples in which a value is given for every attribute in inserted tuples. It is possible for inserted tuples to be given values on only some attributes of the schema. The remaining attributes are assigned a null value denoted by _null_. Consider the request:

**insert into** _student_ **values** (’3003’, ’Green’, ’Finance’, _null_);

The tuple inserted by this request specified that a student with _ID_ “3003” is in the Finance department, but the _tot cred_ value for this student is not known. Consider the query:

**select** _student_ **from** _student_ **where** _tot cred >_ 45;

Since the _tot cred_ value of student “3003” is not known, we cannot determine whether it is greater than 45.

Most relational database products have special “bulk loader” utilities to insert a large set of tuples into a relation. These utilities allow data to be read from formatted text files, and can execute much faster than an equivalent sequence of insert statements.

**3.9.3 Updates**

In certain situations, we may wish to change a value in a tuple without changing _all_ values in the tuple. For this purpose, the **update** statement can be used. As we could for **insert** and **delete**, we can choose the tuples to be updated by using a query.

Suppose that annual salary increases are being made, and salaries of all in- structors are to be increased by 5 percent. We write:  

**102 Chapter 3 Introduction to SQL**

**update** _instructor_ **set** _salary_\= _salary_ \* 1.05;

The preceding update statement is applied once to each of the tuples in _instructor_ relation.

If a salary increase is to be paid only to instructors with salary of less than $70,000, we can write:

**update** _instructor_ **set** _salary_ \= _salary_ \* 1.05 **where** _salary <_ 70000;

In general, the **where** clause of the **update** statement may contain any construct legal in the **where** clause of the **select** statement (including nested **select**s). As with **insert** and **delete**, a nested **select** within an **update** statement may reference the relation that is being updated. As before, SQL first tests all tuples in the relation to see whether they should be updated, and carries out the updates afterward. For example, we can write the request “Give a 5 percent salary raise to instructors whose salary is less than average” as follows:

**update** _instructor_ **set** _salary_ \= _salary_ \* 1.05 **where** _salary <_ (**select avg** (_salary_)

**from** _instructor_);

Let us now suppose that all instructors with salary over $100,000 receive a 3 percent raise, whereas all others receive a 5 percent raise. We could write two **update** statements:

**update** _instructor_ **set** _salary_ \= _salary_ \* 1.03 **where** _salary >_ 100000;

**update** _instructor_ **set** _salary_ \= _salary_ \* 1.05 **where** _salary <_\= 100000;

Note that the order of the two **update** statements is important. If we changed the order of the two statements, an instructor with a salary just under $100,000 would receive an over 8 percent raise.

SQL provides a **case** construct that we can use to perform both the updates with a single **update** statement, avoiding the problem with the order of updates.  

**3.9 Modification of the Database 103**

**update** _instructor_ **set** _salary_ \= **case**

**when** _salary <_\= 100000 **then** _salary_ \* 1.05 **else** _salary_ \* 1.03

**end**

The general form of the case statement is as follows.

**case when** _pred_1 **then** _result_1 **when** _pred_2 **then** _result_2 _. . ._

**when** _predn_ **then** _resultn_ **else** _result_0

**end**

The operation returns _resulti_ , where _i_ is the first of _pred_1, _pred_2, . . . , _predn_ that is satisfied; if none of the predicates is satisfied, the operation returns _result_0\. Case statements can be used in any place where a value is expected.

Scalar subqueries are also useful in SQL update statements, where they can be used in the **set** clause. Consider an update where we set the _tot cred_ attribute of each _student_ tuple to the sum of the credits of courses successfully completed by the student. We assume that a course is successfully completed if the student has a grade that is not ’F’ or null. To specify this update, we need to use a subquery in the **set** clause, as shown below:

**update** _student S_ **set** _tot cred_ \= (

**select sum**(_credits_) **from** _takes_ **natural join** _course_ **where** _S_._ID_\= _takes_._ID_ **and**

_takes_._grade <>_ ’F’ **and** _takes_._grade_ **is not null**);

Observe that the subquery uses a correlation variable _S_ from the **update** statement. In case a student has not successfully completed any course, the above update statement would set the _tot cred_ attribute value to null. To set the value to 0 instead, we could use another **update** statement to replace null values by 0; a better alternative is to replace the clause “**select sum**(_credits_)” in the preceding subquery by the following **select** clause using a **case** expression:

**select case when sum**(_credits_) **is not null then sum**(_credits_) **else** 0 **end**  

**104 Chapter 3 Introduction to SQL**

**3.10 Summary**

• SQL is the most influential commercially marketed relational query language. The SQL language has several parts:

◦ **Data-definition language** (DDL), which provides commands for defining relation schemas, deleting relations, and modifying relation schemas.

◦ **Data-manipulation language** (DML), which includes a query language and commands to insert tuples into, delete tuples from, and modify tuples in the database.

• The SQL data-definition language is used to create relations with specified schemas. In addition to specifying the names and types of relation attributes, SQL also allows the specification of integrity constraints such as primary-key constraints and foreign-key constraints.

• SQL includes a variety of language constructs for queries on the database. These include the **select**, **from**, and **where** clauses, and support for the natural join operation.

• SQL also provides mechanisms to rename both attributes and relations, and to order query results by sorting on specified attributes.

• SQL supports basic set operations on relations including **union**, **intersect**, and **except**, which correspond to the mathematical set-theory operations ∪, ∩, and −.

• SQL handles queries on relations containing null values by adding the truth value “unknown” to the usual truth values of true and false.

• SQL supports aggregation, including the ability to divide a relation into groups, applying aggregation separately on each group. SQL also supports set operations on groups.

• SQL supports nested subqueries in the **where**, and **from** clauses of an outer query. It also supports scalar subqueries, wherever an expression returning a value is permitted.

• SQL provides constructs for updating, inserting, and deleting information.

**Review Terms**

• Data-definition language • Data-manipulation language • Database schema • Database instance • Relation schema

• Relation instance • Primary key • Foreign key

◦ Referencing relation

◦ Referenced relation  

**Practice Exercises 105**

• Null value • Query language • SQL query structure

◦ **select** clause

◦ **from** clause

◦ **where** clause

• Natural join operation • **as** clause • **order** by clause • Correlation name (correlation vari-

able, tuple variable) • Set operations

◦ **union**

◦ **intersect**

◦ **except**

• Null values

◦ Truth value “unknown”

• Aggregate functions

◦ **avg, min, max, sum, count**

◦ **group by**

◦ **having**

• Nested subqueries • Set comparisons

◦ _{<, <_\=_, >, >_\=_} {_ **some, all** _}_ ◦ **exists**

◦ **unique**

• **lateral** clause • **with** clause • Scalar subquery • Database modification

◦ Deletion

◦ Insertion

◦ Updating

**Practice Exercises**

**3.1** Write the following queries in SQL, using the university schema. (We sug- gest you actually run these queries on a database, using the sample data that we provide on the Web site of the book, db-book.com. Instructions for setting up a database, and loading sample data, are provided on the above Web site.)

a. Find the titles of courses in the Comp. Sci. department that have 3 credits.

b. Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result.

c. Find the highest salary of any instructor.

d. Find all instructors earning the highest salary (there may be more than one with the same salary).

e. Find the enrollment of each section that was offered in Autumn 2009.

f. Find the maximum enrollment, across all sections, in Autumn 2009.

g. Find the sections that had the maximum enrollment in Autumn 2009.  

**106 Chapter 3 Introduction to SQL**

_person_ (_driver id_, _name_, _address_) _car_ (_license_, _model_, _year_) _accident_ (_report number_, _date_, _location_) _owns_ (_driver id_, _license_) _participated_ (_report number_, _license_, _driver id_, _damage amount_)

**Figure 3.18** Insurance database for Exercises 3.4 and 3.14.

**3.2** Suppose you are given a relation _grade points_(_grade_, _points_), which provides a conversion from letter grades in the _takes_ relation to numeric scores; for example an “A” grade could be specified to correspond to 4 points, an “A−” to 3.7 points, a “B+” to 3.3 points, a “B” to 3 points, and so on. The grade points earned by a student for a course offering (section) is defined as the number of credits for the course multiplied by the numeric points for the grade that the student received.

Given the above relation, and our university schema, write each of the following queries in SQL. You can assume for simplicity that no _takes_ tuple has the _null_ value for _grade_.

a. Find the total grade-points earned by the student with ID 12345, across all courses taken by the student.

b. Find the grade-point average (_GPA_) for the above student, that is, the total grade-points divided by the total credits for the associated courses.

c. Find the ID and the grade-point average of every student.

**3.3** Write the following inserts, deletes or updates in SQL, using the university schema.

a. Increase the salary of each instructor in the Comp. Sci. department by 10%.

b. Delete all courses that have never been offered (that is, do not occur in the _section_ relation).

c. Insert every student whose _tot cred_ attribute is greater than 100 as an instructor in the same department, with a salary of $10,000.

**3.4** Consider the insurance database of Figure 3.18, where the primary keys are underlined. Construct the following SQL queries for this relational database.

a. Find the total number of people who owned cars that were involved in accidents in 2009.

b. Add a new accident to the database; assume any values for required attributes.

c. Delete the Mazda belonging to “John Smith”.  

**Practice Exercises 107**

_branch_(_branch name_, _branch city, assets_) _customer_ (_customer name_, _customer street, customer city_) _loan_ (_loan number_, _branch name, amount_) _borrower_ (_customer name_, _loan number_) _account_ (_account number_, _branch name, balance_ ) _depositor_ (_customer name_, _account number_)

**Figure 3.19** Banking database for Exercises 3.8 and 3.15.

**3.5** Suppose that we have a relation _marks_(ID, _score_) and we wish to assign grades to students based on the score as follows: grade _F_ if _score_ < 40, grade _C_ if 40 ≤ _score_ < 60, grade _B_ if 60 ≤ _score_ < 80, and grade _A_ if 80 ≤ _score_. Write SQL queries to do the following:

a. Display the grade for each student, based on the _marks_ relation.

b. Find the number of students with each grade.

**3.6** The SQL **like** operator is case sensitive, but the lower() function on strings can be used to perform case insensitive matching. To show how, write a query that finds departments whose names contain the string “sci” as a substring, regardless of the case.

**3.7** Consider the SQL query

**select distinct** _p.a_1 **from** _p_, _r_1, _r_2 **where** _p.a_1 = _r_1._a_1 **or** _p.a_1 = _r_2._a_1

Under what conditions does the preceding query select values of _p.a_1 that are either in _r_1 or in _r_2? Examine carefully the cases where one of _r_1 or _r_2 may be empty.

**3.8** Consider the bank database of Figure 3.19, where the primary keys are un- derlined. Construct the following SQL queries for this relational database.

a. Find all customers of the bank who have an account but not a loan.

b. Find the names of all customers who live on the same street and in the same city as “Smith”.

c. Find the names of all branches with customers who have an account in the bank and who live in “Harrison”.

**3.9** Consider the employee database of Figure 3.20, where the primary keys are underlined. Give an expression in SQL for each of the following queries.

a. Find the names and cities of residence of all employees who work for “First Bank Corporation”.  

**108 Chapter 3 Introduction to SQL**

_employee_ (_employee name_, _street_, _city_) _works_ (_employee name_, _company name_, _salary_) _company_ (_company name_, _city_) _manages_ (_employee name_, _manager name_)

**Figure 3.20** Employee database for Exercises 3.9, 3.10, 3.16, 3.17, and 3.20.

b. Find the names, street addresses, and cities of residence of all em- ployees who work for “First Bank Corporation” and earn more than $10,000.

c. Find all employees in the database who do not work for “First Bank Corporation”.

d. Find all employees in the database who earn more than each employee of “Small Bank Corporation”.

e. Assume that the companies may be located in several cities. Find all companies located in every city in which “Small Bank Corporation” is located.

f. Find the company that has the most employees.

g. Find those companies whose employees earn a higher salary, on av- erage, than the average salary at “First Bank Corporation”.

**3.10** Consider the relational database of Figure 3.20. Give an expression in SQL for each of the following queries.

a. Modify the database so that “Jones” now lives in “Newtown”.

b. Give all managers of “First Bank Corporation” a 10 percent raise unless the salary becomes greater than $100,000; in such cases, give only a 3 percent raise.

**Exercises**

**3.11** Write the following queries in SQL, using the university schema.

a. Find the names of all students who have taken at least one Comp. Sci. course; make sure there are no duplicate names in the result.

b. Find the IDs and names of all students who have not taken any course offering before Spring 2009.

c. For each department, find the maximum salary of instructors in that department. You may assume that every department has at least one instructor.

d. Find the lowest, across all departments, of the per-department maxi- mum salary computed by the preceding query.  

**Exercises 109**

**3.12** Write the following queries in SQL, using the university schema.

a. Create a new course “CS-001”, titled “Weekly Seminar”, with 0 credits.

b. Create a section of this course in Autumn 2009, with _sec id_ of 1.

c. Enroll every student in the Comp. Sci. department in the above sec- tion.

d. Delete enrollments in the above section where the student’s name is Chavez.

e. Delete the course CS-001. What will happen if you run this delete statement without first deleting offerings (sections) of this course.

f. Delete all _takes_ tuples corresponding to any section of any course with the word “database” as a part of the title; ignore case when matching the word with the title.

**3.13** Write SQL DDL corresponding to the schema in Figure 3.18. Make any reasonable assumptions about data types, and be sure to declare primary and foreign keys.

**3.14** Consider the insurance database of Figure 3.18, where the primary keys are underlined. Construct the following SQL queries for this relational database.

a. Find the number of accidents in which the cars belonging to “John Smith” were involved.

b. Update the damage amount for the car with the license number “AABB2000” in the accident with report number “AR2197” to $3000.

**3.15** Consider the bank database of Figure 3.19, where the primary keys are un- derlined. Construct the following SQL queries for this relational database.

a. Find all customers who have an account at _all_ the branches located in “Brooklyn”.

b. Find out the total sum of all loan amounts in the bank.

c. Find the names of all branches that have assets greater than those of at least one branch located in “Brooklyn”.

**3.16** Consider the employee database of Figure 3.20, where the primary keys are underlined. Give an expression in SQL for each of the following queries.

a. Find the names of all employees who work for “First Bank Corpora- tion”.

b. Find all employees in the database who live in the same cities as the companies for which they work.

c. Find all employees in the database who live in the same cities and on the same streets as do their managers.  

**110 Chapter 3 Introduction to SQL**

d. Find all employees who earn more than the average salary of all employees of their company.

e. Find the company that has the smallest payroll.

**3.17** Consider the relational database of Figure 3.20. Give an expression in SQL for each of the following queries.

a. Give all employees of “First Bank Corporation” a 10 percent raise.

b. Give all managers of “First Bank Corporation” a 10 percent raise.

c. Delete all tuples in the _works_ relation for employees of “Small Bank Corporation”.

**3.18** List two reasons why null values might be introduced into the database.

**3.19** Show that, in SQL, _<>_ **all** is identical to **not in**.

**3.20** Give an SQL schema definition for the employee database of Figure 3.20. Choose an appropriate domain for each attribute and an appropriate pri- mary key for each relation schema.

**3.21** Consider the library database of Figure 3.21. Write the following queries in SQL.

a. Print the names of members who have borrowed any book published by “McGraw-Hill”.

b. Print the names of members who have borrowed all books published by “McGraw-Hill”.

c. For each publisher, print the names of members who have borrowed more than five books of that publisher.

d. Print the average number of books borrowed per member. Take into account that if an member does not borrow any books, then that member does not appear in the _borrowed_ relation at all.

**3.22** Rewrite the **where** clause

**where unique** (**select** _title_ **from** _course_)

without using the **unique** construct.

_member_(_memb no_, _name_, _age_) _book_(_isbn_, _title_, _authors_, _publisher_) _borrowed_(_memb no_, _isbn_, _date_)

**Figure 3.21** Library database for Exercise 3.21.  

**Tools 111**

**3.23** Consider the query:

**select** _course id_, _semester_, _year_, _sec id_, **avg** (_tot cred_) **from** _takes_ **natural join** _student_ **where** _year_ \= 2009 **group by** _course id_, _semester_, _year_, _sec id_ **having count** (_ID_) _\>_\= 2;

Explain why joining _section_ as well in the **from** clause would not change the result.

**3.24** Consider the query:

**with** _dept total_ (_dept name_, _value_) **as** (**select** _dept name_, **sum**(_salary_) **from** _instructor_ **group by** _dept name_),

_dept total avg_(_value_) **as** (**select avg**(_value_) **from** _dept total_)

**select** _dept name_ **from** _dept total_, _dept total avg_ **where** _dept total.value >_\= _dept total avg.value_;

Rewrite this query without using the **with** construct.

**Tools**

A number of relational database systems are available commercially, including IBM DB2, IBM Informix, Oracle, Sybase, and Microsoft SQL Server. In addition several database systems can be downloaded and used free of charge, including PostgreSQL, MySQL (free except for certain kinds of commercial use), and Oracle Express edition.

Most database systems provide a command line interface for submitting SQL commands. In addition, most databases also provide graphical user interfaces (GUIs), which simplify the task of browsing the database, creating and submitting queries, and administering the database. Commercial IDEs for SQLthat work across multiple database platforms, include Embarcadero’s RAD Studio and Aqua Data Studio.

For PostgreSQL, the pgAdmin tool provides GUI functionality, while for MySQL, phpMyAdmin provides GUI functionality. The NetBeans IDE provides a GUI front end that works with a number of different databases, but with limited functional- ity, while the Eclipse IDE supports similar functionality through several different plugins such as the Data Tools Platform (DTP) and JBuilder.

SQL schema definitions and sample data for the university schema are pro- vided on the Web site for this book, db-book.com. The Web site also contains  

**112 Chapter 3 Introduction to SQL**

instructions on how to set up and access some popular database systems. The SQL constructs discussed in this chapter are part of the SQL standard, but certain features are not supported by some databases. The Web site lists these incom- patibilities, which you will need to take into account when executing queries on those databases.

**Bibliographical Notes**

The original version of SQL, called Sequel 2, is described by Chamberlin et al. \[1976\]. Sequel 2 was derived from the language Square (Boyce et al. \[1975\] and Chamberlin and Boyce \[1974\]). The American National Standard SQL-86 is de- scribed in ANSI \[1986\]. The IBM Systems Application Architecture definition of SQL is defined by IBM \[1987\]. The official standards for SQL-89 and SQL-92 are available as ANSI \[1989\] and ANSI \[1992\], respectively.

Textbook descriptions of the SQL-92 language include Date and Darwen \[1997\], Melton and Simon \[1993\], and Cannan and Otten \[1993\]. Date and Darwen \[1997\] and Date \[1993a\] include a critique of SQL-92 from a programming-languages perspective.

Textbooks on SQL:1999 include Melton and Simon \[2001\] and Melton \[2002\]. Eisenberg and Melton \[1999\] provide an overview of SQL:1999. Donahoo and Speegle \[2005\] covers SQL from a developers’ perspective. Eisenberg et al. \[2004\] provides an overview of SQL:2003.

The SQL:1999, SQL:2003, SQL:2006 and SQL:2008 standards are published as a collection of ISO/IEC standards documents, which are described in more detail in Section 24.4. The standards documents are densely packed with information and hard to read, and of use primarily for database system implementers. The standards documents are available from the Web site http://webstore.ansi.org, but only for purchase.

Many database products support SQL features beyond those specified in the standard, and may not support some features of the standard. More information on these features may be found in the SQL user manuals of the respective products.

The processing of SQL queries, including algorithms and performance issues, is discussed in Chapters 12 and 13. Bibliographic references on these matters appear in those chapters.  

**_C H A P T E R_4 Intermediate SQL**

In this chapter, we continue our study of SQL. We consider more complex forms of SQL queries, view definition, transactions, integrity constraints, more details regarding SQL data definition, and authorization.

**4.1 Join Expressions**

In Section 3.3.3, we introduced the **natural join** operation. SQL provides other forms of the join operation, including the ability to specify an explicit **join pred- icate**, and the ability to include in the result tuples that are excluded by **natural join**. We shall discuss these forms of join in this section.

The examples in this section involve the two relations _student_ and _takes_, shown in Figures 4.1 and 4.2, respectively. Observe that the attribute _grade_ has a value null for the student with _ID_ 98988, for the course BIO-301, section 1, taken in Summer 2010. The null value indicates that the grade has not been awarded yet.

_ID name dept name tot cred_

00128 Zhang Comp. Sci. 102 12345 Shankar Comp. Sci. 32 19991 Brandt History 80 23121 Chavez Finance 110 44553 Peltier Physics 56 45678 Levy Physics 46 54321 Williams Comp. Sci. 54 55739 Sanchez Music 38 70557 Snow Physics 0 76543 Brown Comp. Sci. 58 76653 Aoi Elec. Eng. 60 98765 Bourikas Elec. Eng. 98 98988 Tanaka Biology 120

**Figure 4.1** The _student_ relation.

**113**  

**114 Chapter 4 Intermediate SQL**

_ID course id sec id semester year grade_

00128 CS-101 1 Fall 2009 A 00128 CS-347 1 Fall 2009 A- 12345 CS-101 1 Fall 2009 C 12345 CS-190 2 Spring 2009 A 12345 CS-315 1 Spring 2010 A 12345 CS-347 1 Fall 2009 A 19991 HIS-351 1 Spring 2010 B 23121 FIN-201 1 Spring 2010 C+ 44553 PHY-101 1 Fall 2009 B- 45678 CS-101 1 Fall 2009 F 45678 CS-101 1 Spring 2010 B+ 45678 CS-319 1 Spring 2010 B 54321 CS-101 1 Fall 2009 A- 54321 CS-190 2 Spring 2009 B+ 55739 MU-199 1 Spring 2010 A- 76543 CS-101 1 Fall 2009 A 76543 CS-319 2 Spring 2010 A 76653 EE-181 1 Spring 2009 C 98765 CS-101 1 Fall 2009 C- 98765 CS-315 1 Spring 2010 B 98988 BIO-101 1 Summer 2009 A 98988 BIO-301 1 Summer 2010 _null_

**Figure 4.2** The _takes_ relation.

**4.1.1 Join Conditions**

In Section 3.3.3, we saw how to express natural joins, and we saw the **join** _. . ._

**using** clause, which is a form of natural join that only requires values to match on specified attributes. SQL supports another form of join, in which an arbitrary join condition can be specified.

The **on** condition allows a general predicate over the relations being joined. This predicate is written like a **where** clause predicate except for the use of the keyword **on** rather than **where**. Like the **using** condition, the **on** condition appears at the end of the join expression.

Consider the following query, which has a join expression containing the **on** condition.

**select** \* **from** _student_ **join** _takes_ **on** _student_._ID_\= _takes_._ID_;

The **on** condition above specifies that a tuple from _student_ matches a tuple from _takes_ if their _ID_ values are equal. The join expression in this case is almost the same as the join expression _student_ **natural join** _takes_, since the natural join operation  

**4.1 Join Expressions 115**

also requires that for a _student_ tuple and a _takes_ tuple to match. The one difference is that the result has the _ID_ attribute listed twice, in the join result, once for _student_ and once for _takes_, even though their _ID_ values must be the same.

In fact, the above query is equivalent to the following query (in other words, they generate exactly the same results):

**select** \* **from** _student_, _takes_ **where** _student_._ID_\= _takes_._ID_;

As we have seen earlier, the relation name is used to disambiguate the attribute name _ID_, and thus the two occurrences can be referred to as _student_._ID_ and _takes_._ID_ respectively. A version of this query that displays the _ID_ value only once is as follows:

**select** _student_._ID_ **as** _ID_, _name_, _dept name_, _tot cred_, _course id_, _sec id_, _semester_, _year_, _grade_

**from** _student_ **join** _takes_ **on** _student_._ID_\= _takes_._ID_;

The result of the above query is shown in Figure 4.3. The **on** condition can express any SQL predicate, and thus a join expressions

using the **on** condition can express a richer class of join conditions than **natural join**. However, as illustrated by our preceding example, a query using a join expression with an **on** condition can be replaced by an equivalent expression without the **on** condition, with the predicate in the **on** clause moved to the **where** clause. Thus, it may appear that the **on** condition is a redundant feature of SQL.

However, there are two good reasons for introducing the **on** condition. First, we shall see shortly that for a kind of join called an outer join, **on** conditions do behave in a manner different from **where** conditions. Second, an SQL query is often more readable by humans if the join condition is specified in the **on** clause and the rest of the conditions appear in the **where** clause.

**4.1.2 Outer Joins**

Suppose we wish to display a list of all students, displaying their _ID_, and _name_, _dept name_, and _tot cred_, along with the courses that they have taken. The following SQL query may appear to retrieve the required information:

**select** \* **from** _student_ **natural join** _takes_;

Unfortunately, the above query does not work quite as intended. Suppose that there is some student who takes no courses. Then the tuple in the _student_ relation for that particular student would not satisfy the condition of a natural join with any tuple in the _takes_ relation, and that student’s data would not appear in the result. We would thus not see any information about students who have not taken  

**116 Chapter 4 Intermediate SQL**

_ID name dept name tot cred course id sec id semester year grade_ 00128 Zhang Comp. Sci. 102 CS-101 1 Fall 2009 A 00128 Zhang Comp. Sci. 102 CS-347 1 Fall 2009 A- 12345 Shankar Comp. Sci. 32 CS-101 1 Fall 2009 C 12345 Shankar Comp. Sci. 32 CS-190 2 Spring 2009 A 12345 Shankar Comp. Sci. 32 CS-315 1 Spring 2010 A 12345 Shankar Comp. Sci. 32 CS-347 1 Fall 2009 A 19991 Brandt History 80 HIS-351 1 Spring 2010 B 23121 Chavez Finance 110 FIN-201 1 Spring 2010 C+ 44553 Peltier Physics 56 PHY-101 1 Fall 2009 B- 45678 Levy Physics 46 CS-101 1 Fall 2009 F 45678 Levy Physics 46 CS-101 1 Spring 2010 B+ 45678 Levy Physics 46 CS-319 1 Spring 2010 B 54321 Williams Comp. Sci. 54 CS-101 1 Fall 2009 A- 54321 Williams Comp. Sci. 54 CS-190 2 Spring 2009 B+ 55739 Sanchez Music 38 MU-199 1 Spring 2010 A- 76543 Brown Comp. Sci. 58 CS-101 1 Fall 2009 A 76543 Brown Comp. Sci. 58 CS-319 2 Spring 2010 A 76653 Aoi Elec. Eng. 60 EE-181 1 Spring 2009 C 98765 Bourikas Elec. Eng. 98 CS-101 1 Fall 2009 C- 98765 Bourikas Elec. Eng. 98 CS-315 1 Spring 2010 B 98988 Tanaka Biology 120 BIO-101 1 Summer 2009 A 98988 Tanaka Biology 120 BIO-301 1 Summer 2010 _null_

**Figure 4.3** The result of _student_ **join** _takes_ **on** _student_._ID_\= _takes_._ID_ with second occurrence of _ID_ omitted.

any courses. For example, in the _student_ and _takes_ relations of Figures 4.1 and 4.2, note that student Snow, with ID 70557, has not taken any courses. Snow appears in _student_, but Snow’s ID number does not appear in the _ID_ column of _takes_. Thus, Snow does not appear in the result of the natural join.

More generally, some tuples in either or both of the relations being joined may be “lost” in this way. The **outer join** operation works in a manner similar to the join operations we have already studied, but preserve those tuples that would be lost in a join, by creating tuples in the result containing null values.

For example, to ensure that the student named Snow from our earlier example appears in the result, a tuple could be added to the join result with all attributes from the _student_ relation set to the corresponding values for the student Snow, and all the remaining attributes which come from the _takes_ relation, namely _course id_, _sec id_, _semester_, and _year_, set to _null_. Thus the tuple for the student Snow is

preserved in the result of the outer join. There are in fact three forms of outer join:

• The **left outer join** preserves tuples only in the relation named before (to the left of) the **left outer join** operation.  

**4.1 Join Expressions 117**

• The **right outer join** preserves tuples only in the relation named after (to the right of) the **right outer join** operation.

• The **full outer join** preserves tuples in both relations.

In contrast, the join operations we studied earlier that do not preserve nonmatched tuples are called **inner join** operations, to distinguish them from the outer-join operations.

We now explain exactly how each form of outer join operates. We can compute the left outer-join operation as follows. First, compute the result of the inner join as before. Then, for every tuple _t_ in the left-hand-side relation that does not match any tuple in the right-hand-side relation in the inner join, add a tuple _r_ to the result of the join constructed as follows:

• The attributes of tuple _r_ that are derived from the left-hand-side relation are filled in with the values from tuple _t_.

• The remaining attributes of _r_ are filled with null values.

Figure 4.4 shows the result of:

**select** \* **from** _student_ **natural left outer join** _takes_;

That result includes student Snow (_ID_ 70557), unlike the result of an inner join, but the tuple for Snow includes nulls for the attributes that appear only in the schema of the _takes_ relation.

As another example of the use of the outer-join operation, we can write the query “Find all students who have not taken a course” as:

**select** _ID_ **from** _student_ **natural left outer join** _takes_ **where** _course id_ **is** _null_;

The **right outer join** is symmetric to the **left outer join**. Tuples from the right- hand-side relation that do not match any tuple in the left-hand-side relation are padded with nulls and are added to the result of the right outer join. Thus, if we rewrite our above query using a right outer join and swapping the order in which we list the relations as follows:

**select** \* **from** _takes_ **natural right outer join** _student_;

we get the same result except for the order in which the attributes appear in the result (see Figure 4.5).

The **full outer join** is a combination of the left and right outer-join types. After the operation computes the result of the inner join, it extends with nulls those tuples from the left-hand-side relation that did not match with any from the  

**118 Chapter 4 Intermediate SQL**

_ID name dept name tot cred course id sec id semester year grade_ 00128 Zhang Comp. Sci. 102 CS-101 1 Fall 2009 A 00128 Zhang Comp. Sci. 102 CS-347 1 Fall 2009 A- 12345 Shankar Comp. Sci. 32 CS-101 1 Fall 2009 C 12345 Shankar Comp. Sci. 32 CS-190 2 Spring 2009 A 12345 Shankar Comp. Sci. 32 CS-315 1 Spring 2010 A 12345 Shankar Comp. Sci. 32 CS-347 1 Fall 2009 A 19991 Brandt History 80 HIS-351 1 Spring 2010 B 23121 Chavez Finance 110 FIN-201 1 Spring 2010 C+ 44553 Peltier Physics 56 PHY-101 1 Fall 2009 B- 45678 Levy Physics 46 CS-101 1 Fall 2009 F 45678 Levy Physics 46 CS-101 1 Spring 2010 B+ 45678 Levy Physics 46 CS-319 1 Spring 2010 B 54321 Williams Comp. Sci. 54 CS-101 1 Fall 2009 A- 54321 Williams Comp. Sci. 54 CS-190 2 Spring 2009 B+ 55739 Sanchez Music 38 MU-199 1 Spring 2010 A- 70557 Snow Physics 0 _null null null null null_ 76543 Brown Comp. Sci. 58 CS-101 1 Fall 2009 A 76543 Brown Comp. Sci. 58 CS-319 2 Spring 2010 A 76653 Aoi Elec. Eng. 60 EE-181 1 Spring 2009 C 98765 Bourikas Elec. Eng. 98 CS-101 1 Fall 2009 C- 98765 Bourikas Elec. Eng. 98 CS-315 1 Spring 2010 B 98988 Tanaka Biology 120 BIO-101 1 Summer 2009 A 98988 Tanaka Biology 120 BIO-301 1 Summer 2010 _null_

**Figure 4.4** Result of _student_ **natural left outer join** _takes_.

right-hand side relation, and adds them to the result. Similarly, it extends with nulls those tuples from the right-hand-side relation that did not match with any tuples from the left-hand-side relation and adds them to the result.

As an example of the use of full outer join, consider the following query: “Display a list of all students in the Comp. Sci. department, along with the course sections, if any, that they have taken in Spring 2009; all course sections from Spring 2009 must be displayed, even if no student from the Comp. Sci. department has taken the course section.” This query can be written as:

**select** \* **from** (**select** \*

**from** _student_ **where** _dept name_\= ’Comp. Sci’)

**natural full outer join** (**select** \* **from** _takes_ **where** _semester_ \= ’Spring’ **and** _year_ \= 2009);  

**4.1 Join Expressions 119**

_ID course id sec id semester year grade name dept name tot cred_ 00128 CS-101 1 Fall 2009 A Zhang Comp. Sci. 102 00128 CS-347 1 Fall 2009 A- Zhang Comp. Sci. 102 12345 CS-101 1 Fall 2009 C Shankar Comp. Sci. 32 12345 CS-190 2 Spring 2009 A Shankar Comp. Sci. 32 12345 CS-315 1 Spring 2010 A Shankar Comp. Sci. 32 12345 CS-347 1 Fall 2009 A Shankar Comp. Sci. 32 19991 HIS-351 1 Spring 2010 B Brandt History 80 23121 FIN-201 1 Spring 2010 C+ Chavez Finance 110 44553 PHY-101 1 Fall 2009 B- Peltier Physics 56 45678 CS-101 1 Fall 2009 F Levy Physics 46 45678 CS-101 1 Spring 2010 B+ Levy Physics 46 45678 CS-319 1 Spring 2010 B Levy Physics 46 54321 CS-101 1 Fall 2009 A- Williams Comp. Sci. 54 54321 CS-190 2 Spring 2009 B+ Williams Comp. Sci. 54 55739 MU-199 1 Spring 2010 A- Sanchez Music 38 70557 _null null null null null_ Snow Physics 0 76543 CS-101 1 Fall 2009 A Brown Comp. Sci. 58 76543 CS-319 2 Spring 2010 A Brown Comp. Sci. 58 76653 EE-181 1 Spring 2009 C Aoi Elec. Eng. 60 98765 CS-101 1 Fall 2009 C- Bourikas Elec. Eng. 98 98765 CS-315 1 Spring 2010 B Bourikas Elec. Eng. 98 98988 BIO-101 1 Summer 2009 A Tanaka Biology 120 98988 BIO-301 1 Summer 2010 _null_ Tanaka Biology 120

**Figure 4.5** The result of _takes_ **natural right outer join** _student_.

The **on** clause can be used with outer joins. The following query is identical to the first query we saw using “_student_ **natural left outer join** _takes_,” except that the attribute _ID_ appears twice in the result.

**select** \* **from** _student_ **left outer join** _takes_ **on** _student_._ID_\= _takes_._ID_;

As we noted earlier, **on** and **where** behave differently for outer join. The reason for this is that outer join adds null-padded tuples only for those tuples that do not contribute to the result of the corresponding inner join. The **on** condition is part of the outer join specification, but a **where** clause is not. In our example, the case of the _student_ tuple for student “Snow” with ID 70557, illustrates this distinction. Suppose we modify the preceding query by moving the **on** clause predicate to the **where** clause, and instead using an **on** condition of _true_.

**select** \* **from** _student_ **left outer join** _takes_ **on** _true_ **where** _student_._ID_\= _takes_._ID_;  

**120 Chapter 4 Intermediate SQL**

_Join types_ **inner join le outer join right outer join full outer join**

_Join conditions_ **natural on** < predicate> **using** (_A_1, _A_2, . . ., _An_)

**Figure 4.6** Join types and join conditions.

The earlier query, using the left outer join with the **on** condition, includes a tuple (70557, Snow, Physics, 0, _null_, _null_, _null_, _null_, _null_, _null_ ), because there is no tuple in _takes_ with _ID_ \= 70557. In the latter query, however, every tuple satisfies the join condition _true_, so no null-padded tuples are generated by the outer join. The outer join actually generates the Cartesian product of the two relations. Since there is no tuple in _takes_ with _ID_ \= 70557, every time a tuple appears in the outer join with _name_ \= “Snow”, the values for _student_._ID_ and _takes_._ID_ must be different, and such tuples would be eliminated by the **where** clause predicate. Thus student Snow never appears in the result of the latter query.

**4.1.3 Join Types and Conditions**

To distinguish normal joins from outer joins, normal joins are called **inner joins** in SQL. A join clause can thus specify **inner join** instead of **outer join** to specify that a normal join is to be used. The keyword **inner** is, however, optional. The default join type, when the **join** clause is used without the **outer** prefix is the **inner join**. Thus,

**select** \* **from** _student_ **join** _takes_ **using** (_ID_);

is equivalent to:

**select** \* **from** _student_ **inner join** _takes_ **using** (_ID_);

Similarly, **natural join** is equivalent to **natural inner join**. Figure 4.6 shows a full list of the various types of join that we have discussed.

As can be seen from the figure, any form of join (inner, left outer, right outer, or full outer) can be combined with any join condition (natural, using, or on).

**4.2 Views**

In our examples up to this point, we have operated at the logical-model level. That is, we have assumed that the relations in the collection we are given are the actual relations stored in the database.  

**4.2 Views 121**

It is not desirable for all users to see the entire logical model. Security con- siderations may require that certain data be hidden from users. Consider a clerk who needs to know an instructor’s ID, name and department name, but does not have authorization to see the instructor’s salary amount. This person should see a relation described in SQL, by:

**select** _ID_, _name_, _dept name_ **from** _instructor_;

Aside from security concerns, we may wish to create a personalized collection of relations that is better matched to a certain user’s intuition than is the logical model. We may want to have a list of all course sections offered by the Physics department in the Fall 2009 semester, with the building and room number of each section. The relation that we would create for obtaining such a list is:

**select** _course_._course id_, _sec id_, _building_, _room number_ **from** _course_, _section_ **where** _course_._course id_ \= _section_._course id_

**and** _course_._dept name_ \= ’Physics’ **and** _section_._semester_ \= ’Fall’ **and** _section_._year_ \= ’2009’;

It is possible to compute and store the results of the above queries and then make the stored relations available to users. However, if we did so, and the underlying data in the relations _instructor_, _course_, or _section_ changes, the stored query results would then no longer match the result of reexecuting the query on the relations. In general, it is a bad idea to compute and store query results such as those in the above examples (although there are some exceptions, which we study later).

Instead, SQL allows a “virtual relation” to be defined by a query, and the relation conceptually contains the result of the query. The virtual relation is not precomputed and stored, but instead is computed by executing the query when- ever the virtual relation is used.

Any such relation that is not part of the logical model, but is made visible to a user as a virtual relation, is called a **view**. It is possible to support a large number of views on top of any given set of actual relations.

**4.2.1 View Definition**

We define a view in SQL by using the **create view** command. To define a view, we must give the view a name and must state the query that computes the view. The form of the **create view** command is:

**create view** _v_ **as** _<_query expression_\>_;  

**122 Chapter 4 Intermediate SQL**

where _<_query expression_\>_ is any legal query expression. The view name is represented by _v_.

Consider again the clerk who needs to access all data in the _instructor_ relation, except _salary_. The clerk should not be authorized to access the _instructor_ relation (we see later, in Section 4.6, how authorizations can be specified). Instead, a view relation _faculty_ can be made available to the clerk, with the view defined as follows:

**create view** _faculty_ **as select** _ID_, _name_, _dept name_ **from** _instructor_;

As explained earlier, the view relation conceptually contains the tuples in the query result, but is not precomputed and stored. Instead, the database system stores the query expression associated with the view relation. Whenever the view relation is accessed, its tuples are created by computing the query result. Thus, the view relation is created whenever needed, on demand.

To create a view that lists all course sections offered by the Physics department in the Fall 2009 semester with the building and room number of each section, we write:

**create view** _physics fall 2009_ **as select** _course_._course id_, _sec id_, _building_, _room number_ **from** _course_, _section_ **where** _course_._course id_ \= _section_._course id_

**and** _course_._dept name_ \= ’Physics’ **and** _section_._semester_ \= ’Fall’ **and** _section_._year_ \= ’2009’;

**4.2.2 Using Views in SQL Queries**

Once we have defined a view, we can use the view name to refer to the virtual relation that the view generates. Using the view _physics fall 2009_, we can find all Physics courses offered in the Fall 2009 semester in the Watson building by writing:

**select** _course id_ **from** _physics fall 2009_ **where** _building_\= ’Watson’;

View names may appear in a query any place where a relation name may appear, The attribute names of a view can be specified explicitly as follows:

**create view** _departments total salary_(_dept name_, _total salary_) **as select** _dept name_, **sum** (_salary_) **from** _instructor_ **group by** _dept name_;  

**4.2 Views 123**

The preceding view gives for each department the sum of the salaries of all the instructors at that department. Since the expression **sum**(_salary_) does not have a name, the attribute name is specified explicitly in the view definition.

Intuitively, at any given time, the set of tuples in the view relation is the result of evaluation of the query expression that defines the view. Thus, if a view relation is computed and stored, it may become out of date if the relations used to define it are modified. To avoid this, views are usually implemented as follows. When we define a view, the database system stores the definition of the view itself, rather than the result of evaluation of the query expression that defines the view. Wherever a view relation appears in a query, it is replaced by the stored query expression. Thus, whenever we evaluate the query, the view relation is recomputed.

One view may be used in the expression defining another view. For example, we can define a view _physics fall 2009 watson_ that lists the course ID and room number of all Physics courses offered in the Fall 2009 semester in the Watson building as follows:

**create view** _physics fall 2009 watson_ **as select** _course id_, _room number_ **from** _physics fall 2009_ **where** _building_\= ’Watson’;

where _physics fall 2009 watson_ is itself a view relation. This is equivalent to:

**create view** _physics fall 2009 watson_ **as** (**select** _course id_, _room number_ **from** (**select** _course_._course id_, _building_, _room number_

**from** _course_, _section_ **where** _course_._course id_ \= _section_._course id_

**and** _course_._dept name_ \= ’Physics’ **and** _section_._semester_ \= ’Fall’ **and** _section_._year_ \= ’2009’)

**where** _building_\= ’Watson’;

**4.2.3 Materialized Views**

Certain database systems allow view relations to be stored, but they make sure that, if the actual relations used in the view definition change, the view is kept up-to-date. Such views are called **materialized views**.

For example, consider the view _departments total salary_. If the above view is materialized, its results would be stored in the database. However, if an _instructor_ tuple is added to or deleted from the _instructor_ relation, the result of the query defining the view would change, and as a result the materialized view’s contents must be updated. Similarly, if an instructor’s salary is updated, the tuple in _departments total salary_ corresponding to that instructor’s department must be updated.  

**124 Chapter 4 Intermediate SQL**

The process of keeping the materialized view up-to-date is called **material- ized view maintenance** (or often, just **view maintenance**) and is covered in Sec- tion 13.5. View maintenance can be done immediately when any of the relations on which the view is defined is updated. Some database systems, however, per- form view maintenance lazily, when the view is accessed. Some systems update materialized views only periodically; in this case, the contents of the materialized view may be stale, that is, not up-to-date, when it is used, and should not be used if the application needs up-to-date data. And some database systems permit the database administrator to control which of the above methods is used for each materialized view.

Applications that use a view frequently may benefit if the view is materi- alized. Applications that demand fast response to certain queries that compute aggregates over large relations can also benefit greatly by creating materialized views corresponding to the queries. In this case, the aggregated result is likely to be much smaller than the large relations on which the view is defined; as a result the materialized view can be used to answer the query very quickly, avoiding reading the large underlying relations. Of course, the benefits to queries from the materialization of a view must be weighed against the storage costs and the added overhead for updates.

SQL does not define a standard way of specifying that a view is material- ized, but many database systems provide their own SQL extensions for this task. Some database systems always keep materialized views up-to-date when the un- derlying relations change, while others permit them to become out of date, and periodically recompute them.

**4.2.4 Update of a View**

Although views are a useful tool for queries, they present serious problems if we express updates, insertions, or deletions with them. The difficulty is that a modification to the database expressed in terms of a view must be translated to a modification to the actual relations in the logical model of the database.

Suppose the view _faculty_, which we saw earlier, is made available to a clerk. Since we allow a view name to appear wherever a relation name is allowed, the clerk can write:

**insert into** _faculty_ **values** (’30765’, ’Green’, ’Music’);

This insertion must be represented by an insertion into the relation _instructor_, since _instructor_ is the actual relation from which the database system constructs the view _faculty_. However, to insert a tuple into _instructor_, we must have some value for _salary_. There are two reasonable approaches to dealing with this insertion:

• Reject the insertion, and return an error message to the user.

• Insert a tuple (’30765’, ’Green’, ’Music’, _null_) into the _instructor_ relation.  

**4.2 Views 125**

Another problem with modification of the database through views occurs with a view such as:

**create view** _instructor info_ **as select** _ID_, _name_, _building_ **from** _instructor_, _department_ **where** _instructor_._dept name_\= _department_._dept name_;

This view lists the _ID_, _name_, and building-name of each instructor in the university. Consider the following insertion through this view:

**insert into** _instructor info_ **values** (’69987’, ’White’, ’Taylor’);

Suppose there is no instructor with ID 69987, and no department in the Taylor building. Then the only possible method of inserting tuples into the _instructor_ and _department_ relations is to insert (’69987’, ’White’, _null_, _null_) into _instructor_ and (_null_, ’Taylor’, _null_) into _department_. Then, we obtain the relations shown in Figure 4.7. However, this update does not have the desired effect, since the view relation _instructor info_ still does _not_ include the tuple (’69987’, ’White’, ’Taylor’). Thus, there is no way to update the relations _instructor_ and _department_ by using nulls to get the desired update on _instructor info_.

Because of problems such as these, modifications are generally not permit- ted on view relations, except in limited cases. Different database systems specify different conditions under which they permit updates on view relations; see the database system manuals for details. The general problem of database modifica- tion through views has been the subject of substantial research, and the biblio- graphic notes provide pointers to some of this research.

In general, an SQL view is said to be **updatable** (that is, inserts, updates or deletes can be applied on the view) if the following conditions are all satisfied by the query defining the view:

• The **from** clause has only one database relation.

• The **select** clause contains only attribute names of the relation, and does not have any expressions, aggregates, or **distinct** specification.

• Any attribute not listed in the **select** clause can be set to _null_; that is, it does not have a **not null** constraint and is not part of a primary key.

• The query does not have a **group by** or **having** clause.

Under these constraints, the **update**, **insert**, and **delete** operations would be allowed on the following view:

**create view** _history instructors_ **as select** \* **from** _instructor_ **where** _dept name_\= ’History’;  

**126 Chapter 4 Intermediate SQL**

_ID name dept name salary_ 10101 Srinivasan Comp. Sci. 65000 12121 Wu Finance 90000 15151 Mozart Music 40000 22222 Einstein Physics 95000 32343 El Said History 60000 33456 Gold Physics 87000 45565 Katz Comp. Sci. 75000 58583 Califieri History 62000 76543 Singh Finance 80000 76766 Crick Biology 72000 83821 Brandt Comp. Sci. 92000 98345 Kim Elec. Eng. 80000 69987 White _null null_

_instructor_

_dept name building budget_ Biology Watson 90000 Comp. Sci. Taylor 100000 Electrical Eng. Taylor 85000 Finance Painter 120000 History Painter 50000 Music Packard 80000 Physics Watson 70000 _null_ Painter _null_

_department_

**Figure 4.7** Relations _instructor_ and _department_ after insertion of tuples.

Even with the conditions on updatability, the following problem still remains. Suppose that a user tries to insert the tuple (’25566’, ’Brown’, ’Biology’, 100000) into the _history instructors_ view. This tuple can be inserted into the _instructor_ relation, but it would not appear in the _history instructors_ view since it does not satisfy the selection imposed by the view.

By default, SQL would allow the above update to proceed. However, views can be defined with a **with check option** clause at the end of the view definition; then, if a tuple inserted into the view does not satisfy the view’s **where** clause condition, the insertion is rejected by the database system. Updates are similarly rejected if the new value does not satisfy the **where** clause conditions.

SQL:1999 has a more complex set of rules about when inserts, updates, and deletes can be executed on a view, that allows updates through a larger class of views; however, the rules are too complex to be discussed here.  

**4.3 Transactions 127**

**4.3 Transactions**

A **transaction** consists of a sequence of query and/or update statements. The SQL standard specifies that a transaction begins implicitly when an SQL statement is executed. One of the following SQL statements must end the transaction:

• **Commit work** commits the current transaction; that is, it makes the updates performed by the transaction become permanent in the database. After the transaction is committed, a new transaction is automatically started.

• **Rollback work** causes the current transaction to be rolled back; that is, it undoes all the updates performed by the SQL statements in the transaction. Thus, the database state is restored to what it was before the first statement of the transaction was executed.

The keyword **work** is optional in both the statements. Transaction rollback is useful if some error condition is detected during ex-

ecution of a transaction. Commit is similar, in a sense, to saving changes to a document that is being edited, while rollback is similar to quitting the edit ses- sion without saving changes. Once a transaction has executed **commit work**, its effects can no longer be undone by **rollback work**. The database system guaran- tees that in the event of some failure, such as an error in one of the SQL statements, a power outage, or a system crash, a transaction’s effects will be rolled back if it has not yet executed **commit work**. In the case of power outage or other system crash, the rollback occurs when the system restarts.

For instance, consider a banking application, where we need to transfer money from one bank account to another in the same bank. To do so, we need to update two account balances, subtracting the amount transferred from one, and adding it to the other. If the system crashes after subtracting the amount from the first account, but before adding it to the second account, the bank balances would be inconsistent. A similar problem would occur, if the second account is credited before subtracting the amount from the first account, and the system crashes just after crediting the amount.

As another example, consider our running example of a university applica- tion. We assume that the attribute _tot cred_ of each tuple in the _student_ relation is kept up-to-date by modifying it whenever the student successfully completes a course. To do so, whenever the _takes_ relation is updated to record successful completion of a course by a student (by assigning an appropriate grade) the corre- sponding _student_ tuple must also be updated. If the application performing these two updates crashes after one update is performed, but before the second one is performed, the data in the database would be inconsistent.

By either committing the actions of a transaction after all its steps are com- pleted, or rolling back all its actions in case the transaction could not complete all its actions successfully, the database provides an abstraction of a transaction as being **atomic**, that is, indivisible. Either all the effects of the transaction are reflected in the database, or none are (after rollback).  

**128 Chapter 4 Intermediate SQL**

Applying the notion of transactions to the above applications, the update statements should be executed as a single transaction. An error while a transaction executes one of its statements would result in undoing of the effects of the earlier statements of the transaction, so that the database is not left in a partially updated state.

If a program terminates without executing either of these commands, the updates are either committed or rolled back. The standard does not specify which of the two happens, and the choice is implementation dependent.

In many SQL implementations, by default each SQL statement is taken to be a transaction on its own, and gets committed as soon as it is executed. Automatic commit of individual SQL statements must be turned off if a transaction consisting of multiple SQL statements needs to be executed. How to turn off automatic commit depends on the specific SQL implementation, although there is a standard way of doing this using application program interfaces such as JDBC or ODBC, which we study later, in Sections 5.1.1 and 5.1.2, respectively.

A better alternative, which is part of the SQL:1999 standard (but supported by only some SQL implementations currently), is to allow multiple SQL statements to be enclosed between the keywords **begin atomic** _. . ._ **end**. All the statements between the keywords then form a single transaction.

We study further properties of transactions in Chapter 14; issues in imple- menting transactions in a single database are addressed in Chapters 15 and 16, while Chapter 19 addresses issues in implementing transactions across multiple databases, to deal with problems such as transfer of money across accounts in different banks, which have different databases.

**4.4 Integrity Constraints**

Integrity constraints ensure that changes made to the database by authorized users do not result in a loss of data consistency. Thus, integrity constraints guard against accidental damage to the database.

Examples of integrity constraints are:

• An instructor name cannot be _null_.

• No two instructors can have the same instructor ID.

• Every department name in the _course_ relation must have a matching depart- ment name in the _department_ relation.

• The budget of a department must be greater than $0.00.

In general, an integrity constraint can be an arbitrary predicate pertaining to the database. However, arbitrary predicates may be costly to test. Thus, most database systems allow one to specify integrity constraints that can be tested with minimal overhead.

We have already seen some forms of integrity constraints in Section 3.2.2. We study some more forms of integrity constraints in this section. In Chapter 8, we  

**4.4 Integrity Constraints 129**

study another form of integrity constraint, called **functional dependencies**, that is used primarily in the process of schema design.

Integrity constraints are usually identified as part of the database schema design process, and declared as part of the **create table** command used to create relations. However, integrity constraints can also be added to an existing relation by using the command **alter table** _table-name_ **add** _constraint_, where _constraint_ can be any constraint on the relation. When such a command is executed, the system first ensures that the relation satisfies the specified constraint. If it does, the constraint is added to the relation; if not, the command is rejected.

**4.4.1 Constraints on a Single Relation**

We described in Section 3.2 how to define tables using the **create table** command. The **create table** command may also include integrity-constraint statements. In addition to the primary-key constraint, there are a number of other ones that can be included in the **create table** command. The allowed integrity constraints include

• **not null**

• **unique**

• **check**(_<_predicate_\>_)

We cover each of these types of constraints in the following sections.

**4.4.2 Not Null Constraint**

As we discussed in Chapter 3, the null value is a member of all domains, and as a result is a legal value for every attribute in SQL by default. For certain attributes, however, null values may be inappropriate. Consider a tuple in the _student_ relation where _name_ is _null_. Such a tuple gives student information for an unknown student; thus, it does not contain useful information. Similarly, we would not want the department budget to be _null_. In cases such as this, we wish to forbid null values, and we can do so by restricting the domain of the attributes _name_ and _budget_ to exclude null values, by declaring it as follows:

_name_ **varchar**(20) **not null** _budget_ **numeric**(12,2) **not null**

The **not null** specification prohibits the insertion of a null value for the attribute. Any database modification that would cause a null to be inserted in an attribute declared to be **not null** generates an error diagnostic.

There are many situations where we want to avoid null values. In particular, SQL prohibits null values in the primary key of a relation schema. Thus, in our university example, in the _department_ relation, if the attribute _dept name_ is declared  

**130 Chapter 4 Intermediate SQL**

as the primary key for _department_, it cannot take a null value. As a result it would not need to be declared explicitly to be **not null**.

**4.4.3 Unique Constraint**

SQL also supports an integrity constraint:

**unique** (_Aj_1 _, Aj_2 _, . . . , Ajm_ )

The **unique** specification says that attributes _Aj_1 _, Aj_2_, . . . , Ajm_ form a candidate key; that is, no two tuples in the relation can be equal on all the listed attributes. However, candidate key attributes are permitted to be _null_ unless they have explicitly been declared to be **not null**. Recall that a null value does not equal any other value. (The treatment of nulls here is the same as that of the **unique** construct defined in Section 3.8.4.)

**4.4.4 The check Clause**

When applied to a relation declaration, the clause **check**(_P_) specifies a predicate _P_ that must be satisfied by every tuple in a relation.

A common use of the **check** clause is to ensure that attribute values satisfy specified conditions, in effect creating a powerful type system. For instance, a clause **check** (_budget_ \> 0) in the **create table** command for relation _department_ would ensure that the value of **budget** is nonnegative.

As another example, consider the following:

**create table** _section_ (_course id_ **varchar** (8), _sec id_ **varchar** (8), _semester_ **varchar** (6), _year_ **numeric** (4,0), _building_ **varchar** (15), _room number_ **varchar** (7), _time slot id_ **varchar** (4), **primary key** (_course id_, _sec id_, _semester_, _year_), **check** (_semester_ **in** (’Fall’, ’Winter’, ’Spring’, ’Summer’)));

Here, we use the **check** clause to simulate an enumerated type, by specifying that _semester_ must be one of ’Fall’, ’Winter’, ’Spring’, or ’Summer’. Thus, the **check** clause permits attribute domains to be restricted in powerful ways that most programming-language type systems do not permit.

The predicate in the **check** clause can, according to the SQL standard, be an arbitrary predicate that can include a subquery. However, currently none of the widely used database products allows the predicate to contain a subquery.  

**4.4 Integrity Constraints 131**

**4.4.5 Referential Integrity**

Often, we wish to ensure that a value that appears in one relation for a given set of attributes also appears for a certain set of attributes in another relation. This condition is called **referential integrity**.

Foreign keys can be specified as part of the SQL **create table** statement by using the **foreign key** clause, as we saw earlier in Section 3.2.2. We illustrate foreign-key declarations by using the SQL DDL definition of part of our university database, shown in Figure 4.8. The definition of the _course_ table has a declaration “**foreign key** (_dept name_) **references** _department_”. This foreign-key declaration specifies that for each course tuple, the department name specified in the tuple must exist in the _department_ relation. Without this constraint, it is possible for a course to specify a nonexistent department name.

More generally, let _r_1 and _r_2 be relations whose set of attributes are _R_1 and _R_2, respectively, with primary keys _K_1 and _K_2\. We say that a subset  of _R_2 is a **foreign key** referencing _K_1 in relation _r_1 if it is required that, for every tuple _t_2 in _r_2, there must be a tuple _t_1 in _r_1 such that _t_1_.K_1 = _t_2_._.

Requirements of this form are called **referential-integrity constraints**, or **subset dependencies**. The latter term arises because the preceding referential- integrity constraint can be stated as a requirement that the set of values on  in _r_2 must be a subset of the values on _K_1 in _r_1\. Note that, for a referential-integrity constraint to make sense,  and _K_1 must be compatible sets of attributes; that is, either  must be equal to _K_1, or they must contain the same number of attributes, and the types of corresponding attributes must be compatible (we assume here that  and _K_1 are ordered). Unlike foreign-key constraints, in general a referential integrity constraint does not require _K_1 to be a primary key of _r_1; as a result, more than one tuple in _r_1 can have the same value for attributes _K_1.

By default, in SQL a foreign key references the primary-key attributes of the referenced table. SQL also supports a version of the **references** clause where a list of attributes of the referenced relation can be specified explicitly. The specified list of attributes must, however, be declared as a candidate key of the referenced relation, using either a **primary key** constraint, or a **unique** constraint. A more general form of a referential-integrity constraint, where the referenced columns need not be a candidate key, cannot be directly specified in SQL. The SQL standard specifies other constructs that can be used to implement such constraints; they are described in Section 4.4.7.

We can use the following short form as part of an attribute definition to declare that the attribute forms a foreign key:

_dept name_ **varchar**(20) **references** _department_

When a referential-integrity constraint is violated, the normal procedure is to reject the action that caused the violation (that is, the transaction performing the update action is rolled back). However, a **foreign key** clause can specify that if a delete or update action on the referenced relation violates the constraint, then,  

**132 Chapter 4 Intermediate SQL**

**create table** _classroom_ (_building_ **varchar** (15), _room number_ **varchar** (7), _capacity_ **numeric** (4,0), **primary key** (_building_, _room number_))

**create table** _department_ (_dept name_ **varchar** (20), _building_ **varchar** (15), _budget_ **numeric** (12,2) **check** (_budget_ \> 0), **primary key** (_dept name_))

**create table** _course_ (_course id_ **varchar** (8), _title_ **varchar** (50), _dept name_ **varchar** (20), _credits_ **numeric** (2,0) **check** (_credits_ \> 0), **primary key** (_course id_), **foreign key** (_dept name_) **references** _department_)

**create table** _instructor_ (_ID_ **varchar** (5), _name_ **varchar** (20), **not null** _dept name_ **varchar** (20), _salary_ **numeric** (8,2), **check** (_salary_ \> 29000), **primary key** (_ID_), **foreign key** (_dept name_) **references** _department_)

**create table** _section_ (_course id_ **varchar** (8), _sec id_ **varchar** (8), _semester_ **varchar** (6), **check** (_semester_ **in**

(’Fall’, ’Winter’, ’Spring’, ’Summer’), _year_ **numeric** (4,0), **check** (_year_ \> 1759 and _year_ < 2100) _building_ **varchar** (15), _room number_ **varchar** (7), _time slot id_ **varchar** (4), **primary key** (_course id_, _sec id_, _semester_, _year_), **foreign key** (_course id_) **references** _course_, **foreign key** (_building_, _room number_) **references** _classroom_)

**Figure 4.8** SQL data definition for part of the university database.

instead of rejecting the action, the system must take steps to change the tuple in the referencing relation to restore the constraint. Consider this definition of an integrity constraint on the relation _course_:  

**4.4 Integrity Constraints 133**

**create table** _course_ ( _. . ._

**foreign key** (_dept name_) **references** _department_ **on delete cascade on update cascade**,

_. . ._ );

Because of the clause **on delete cascade** associated with the foreign-key dec- laration, if a delete of a tuple in _department_ results in this referential-integrity constraint being violated, the system does not reject the delete. Instead, the delete “cascades” to the _course_ relation, deleting the tuple that refers to the department that was deleted. Similarly, the system does not reject an update to a field refer- enced by the constraint if it violates the constraint; instead, the system updates the field _dept name_ in the referencing tuples in _course_ to the new value as well. SQL also allows the **foreign key** clause to specify actions other than **cascade**, if the constraint is violated: The referencing field (here, _dept name_) can be set to _null_ (by using **set null** in place of **cascade**), or to the default value for the domain (by using **set default**).

If there is a chain of foreign-key dependencies across multiple relations, a deletion or update at one end of the chain can propagate across the entire chain. An interesting case where the **foreign key** constraint on a relation references the same relation appears in Practice Exercises 4.9. If a cascading update or delete causes a constraint violation that cannot be handled by a further cascading operation, the system aborts the transaction. As a result, all the changes caused by the transaction and its cascading actions are undone.

Null values complicate the semantics of referential-integrity constraints in SQL. Attributes of foreign keys are allowed to be _null_, provided that they have not otherwise been declared to be **not null**. If all the columns of a foreign key are nonnull in a given tuple, the usual definition of foreign-key constraints is used for that tuple. If any of the foreign-key columns is _null_, the tuple is defined automatically to satisfy the constraint.

This definition may not always be the right choice, so SQL also provides constructs that allow you to change the behavior with null values; we do not discuss the constructs here.

**4.4.6 Integrity Constraint Violation During a Transaction**

Transactions may consist of several steps, and integrity constraints may be vio- lated temporarily after one step, but a later step may remove the violation. For instance, suppose we have a relation _person_ with primary key _name_, and an at- tribute _spouse_, and suppose that _spouse_ is a foreign key on _person_. That is, the constraint says that the _spouse_ attribute must contain a name that is present in the _person_ table. Suppose we wish to note the fact that John and Mary are married to each other by inserting two tuples, one for John and one for Mary, in the above re- lation, with the spouse attributes set to Mary and John, respectively. The insertion of the first tuple would violate the foreign-key constraint, regardless of which of  

**134 Chapter 4 Intermediate SQL**

the two tuples is inserted first. After the second tuple is inserted the foreign-key constraint would hold again.

To handle such situations, the SQL standard allows a clause **initially deferred** to be added to a constraint specification; the constraint would then be checked at the end of a transaction, and not at intermediate steps. A constraint can alter- natively be specified as **deferrable**, which means it is checked immediately by default, but can be deferred when desired. For constraints declared as deferrable, executing a statement **set constraints** _constraint-list_ **deferred** as part of a transac- tion causes the checking of the specified constraints to be deferred to the end of that transaction.

However, you should be aware that the default behavior is to check constraints immediately, and many database implementations do not support deferred con- straint checking.

We can work around the problem in the above example in another way, if the _spouse_ attribute can be set to _null_: We set the spouse attributes to _null_ when inserting the tuples for John and Mary, and we update them later. However, this technique requires more programming effort, and does not work if the attributes cannot be set to _null_.

**4.4.7 Complex Check Conditions and Assertions**

The SQL standard supports additional constructs for specifying integrity con- straints that are described in this section. However, you should be aware that these constructs are not currently supported by most database systems.

As defined by the SQL standard, the predicate in the **check** clause can be an arbitrary predicate, which can include a subquery. If a database implemen- tation supports subqueries in the **check** clause, we could specify the following referential-integrity constraint on the relation _section_:

**check** (_time slot id_ **in** (**select** _time slot id_ **from** _time slot_))

The **check** condition verifies that the _time slot id_ in each tuple in the _section_ relation is actually the identifier of a time slot in the _time slot_ relation. Thus, the condition has to be checked not only when a tuple is inserted or modified in _section_, but also when the relation _time slot_ changes (in this case, when a tuple is deleted or modified in relation _time slot_).

Another natural constraint on our university schema would be to require that every section has at least one instructor teaching the section. In an attempt to enforce this, we may try to declare that the attributes (_course id_, _sec id_, _semester_, _year_) of the _section_ relation form a foreign key referencing the corresponding attributes of the _teaches_ relation. Unfortunately, these attributes do not form a candidate key of the relation _teaches_. A check constraint similar to that for the _time slot_ attribute can be used to enforce this constraint, if check constraints with subqueries were supported by a database system.

Complex **check** conditions can be useful when we want to ensure integrity of data, but may be costly to test. For example, the predicate in the **check** clause  

**4.4 Integrity Constraints 135**

**create assertion** _credits earned constraint_ **check** (**not exists** (**select** ID

**from** _student_ **where** _tot cred <>_ (**select sum**(_credits_) **from** _takes_ **natural join** _course_ **where** _student_._ID_\= _takes_._ID_

**and** _grade_ **is not null and** _grade<>_ ’F’ )

**Figure 4.9** An assertion example.

would not only have to be evaluated when a modification is made to the _section_ relation, but may have to be checked if a modification is made to the _time slot_ relation because that relation is referenced in the subquery.

An **assertion** is a predicate expressing a condition that we wish the database always to satisfy. Domain constraints and referential-integrity constraints are special forms of assertions. We have paid substantial attention to these forms of assertions because they are easily tested and apply to a wide range of database applications. However, there are many constraints that we cannot express by using only these special forms. Two examples of such constraints are:

• For each tuple in the _student_ relation, the value of the attribute _tot cred_ must equal the sum of credits of courses that the student has completed success- fully.

• An instructor cannot teach in two different classrooms in a semester in the same time slot.1

An assertion in SQL takes the form:

**create assertion** _<_assertion-name_\>_ **check** _<_predicate_\>_;

In Figure 4.9, we show how the first example of constraints can be written in SQL. Since SQL does not provide a “for all _X_, _P_(_X_)” construct (where _P_ is a predicate), we are forced to implement the constraint by an equivalent construct, “not exists _X_ such that not _P_(_X_)”, that can be expressed in SQL.

We leave the specification of the second constraint as an exercise. When an assertion is created, the system tests it for validity. If the assertion

is valid, then any future modification to the database is allowed only if it does not cause that assertion to be violated. This testing may introduce a significant amount of overhead if complex assertions have been made. Hence, assertions should be used with great care. The high overhead of testing and maintaining assertions has led some system developers to omit support for general assertions, or to provide specialized forms of assertion that are easier to test.

1We assume that lectures are not displayed remotely in a second classroom! An alternative constraint that specifies that “an instructor cannot teach two courses in a given semester in the same time slot” may not hold since courses are sometimes cross-listed; that is, the same course is given two identifiers and titles.  

**136 Chapter 4 Intermediate SQL**

Currently, none of the widely used database systems supports either sub- queries in the **check** clause predicate, or the **create assertion** construct. However, equivalent functionality can be implemented using triggers, which are described in Section 5.3, if they are supported by the database system. Section 5.3 also de- scribes how the referential integrity constraint on _time slot id_ can be implemented using triggers.

**4.5 SQL Data Types and Schemas**

In Chapter 3, we covered a number of built-in data types supported in SQL, such as integer types, real types, and character types. There are additional built-in data types supported by SQL, which we describe below. We also describe how to create basic user-defined types in SQL.

**4.5.1 Date and Time Types in SQL**

In addition to the basic data types we introduced in Section 3.2, the SQL standard supports several data types relating to dates and times:

• **date**: A calendar date containing a (four-digit) year, month, and day of the month.

• **time**: The time of day, in hours, minutes, and seconds. A variant, **time**(_p_), can be used to specify the number of fractional digits for seconds (the default being 0). It is also possible to store time-zone information along with the time by specifying **time with timezone**.

• **timestamp**: A combination of **date** and **time**. A variant, **timestamp**(_p_), can be used to specify the number of fractional digits for seconds (the default here being 6). Time-zone information is also stored if **with timezone** is specified.

Date and time values can be specified like this:

**date** ’2001-04-25’ **time** ’09:30:00’ **timestamp** ’2001-04-25 10:29:01.45’

Dates must be specified in the format year followed by month followed by day, as shown. The seconds field of **time** or **timestamp** can have a fractional part, as in the timestamp above.

We can use an expression of the form **cast** _e_ **as** _t_ to convert a character string (or string valued expression) _e_ to the type _t_, where _t_ is one of **date, time**, or **timestamp**. The string must be in the appropriate format as illustrated at the beginning of this paragraph. When required, time-zone information is inferred from the system settings.

To extract individual fields of a **date** or **time** value _d_, we can use **extract** (field **from** _d_), where _field_ can be one of **year, month, day, hour, minute**, or **second**. Time- zone information can be extracted using **timezone hour** and **timezone minute**.  

**4.5 SQL Data Types and Schemas 137**

SQL defines several functions to get the current date and time. For example, **current date** returns the current date, **current time** returns the current time (with time zone), and **localtime** returns the current local time (without time zone). Timestamps (date plus time) are returned by **current timestamp** (with time zone) and **localtimestamp** (local date and time without time zone).

SQL allows comparison operations on all the types listed here, and it allows both arithmetic and comparison operations on the various numeric types. SQL also provides a data type called **interval**, and it allows computations based on dates and times and on intervals. For example, if _x_ and _y_ are of type **date**, then _x_ − _y_ is an interval whose value is the number of days from date _x_ to date _y_. Similarly, adding or subtracting an interval to a date or time gives back a date or time, respectively.

**4.5.2 Default Values**

SQL allows a default value to be specified for an attribute as illustrated by the following **create table** statement:

**create table** _student_ (_ID_ **varchar** (5), _name_ **varchar** (20) **not null**, _dept name_ **varchar** (20), _tot cred_ **numeric** (3,0) **default** 0, **primary key** (_ID_));

The default value of the _tot cred_ attribute is declared to be 0. As a result, when a tuple is inserted into the _student_ relation, if no value is provided for the _tot cred_ attribute, its value is set to 0. The following insert statement illustrates how an insertion can omit the value for the _tot cred_ attribute.

**insert into** _student_(_ID_, _name_, _dept name_) **values** (’12789’, ’Newman’, ’Comp. Sci.’);

**4.5.3 Index Creation**

Many queries reference only a small proportion of the records in a file. For exam- ple, a query like “Find all instructors in the Physics department” or “Find the _tot cred_ value of the student with _ID_ 22201” references only a fraction of the student

records. It is inefficient for the system to read every record and to check _ID_ field for the _ID_ “32556,” or the _building_ field for the value “Physics”.

An **index** on an attribute of a relation is a data structure that allows the database system to find those tuples in the relation that have a specified value for that attribute efficiently, without scanning through all the tuples of the relation. For example, if we create in index on attribute _ID_ of relation _student_, the database system can find the record with any specified _ID_ value, such as 22201, or 44553, directly, without reading all the tuples of the _student_ relation. An index can also  

**138 Chapter 4 Intermediate SQL**

be created on a list of attributes, for example on attributes _name_, and _dept name_ of _student_.

We study later, in Chapter 11, how indices are actually implemented, includ- ing a particularly widely used kind of index called a B+-tree index.

Although the SQL language does not formally define any syntax for creating indices, many databases support index creation using the syntax illustrated below.

**create index** _studentID index_ **on** _student_(_ID_);

The above statement creates an index named _studentID index_ on the attribute _ID_ of the relation _student_.

When a user submits an SQL query that can benefit from using an index, the SQL query processor automatically uses the index. For example, given an SQL query that selects the _student_ tuple with _ID_ 22201, the SQL query processor would use the index _studentID index_ defined above to find the required tuple without reading the whole relation.

**4.5.4 Large-Object Types**

Many current-generation database applications need to store attributes that can be large (of the order of many kilobytes), such as a photograph, or very large (of the order of many megabytes or even gigabytes), such as a high-resolution medical image or video clip. SQL therefore provides large-object data types for character data (**clob**) and binary data (**blob**). The letters “lob” in these data types stand for “Large OBject.” For example, we may declare attributes

_book review_ **clob**(10KB) _image_ **blob**(10MB) _movie_ **blob**(2GB)

For result tuples containing large objects (multiple megabytes to gigabytes), it is inefficient or impractical to retrieve an entire large object into memory. Instead, an application would usually use an SQL query to retrieve a “locator” for a large object and then use the locator to manipulate the object from the host language in which the application itself is written. For instance, the JDBC application program interface (described in Section 5.1.1) permits a locator to be fetched instead of the entire large object; the locator can then be used to fetch the large object in small pieces, rather than all at once, much like reading data from an operating system file using a read function call.

**4.5.5 User-Defined Types**

SQL supports two forms of user-defined data types. The first form, which we cover here, is called **distinct types**. The other form, called **structured data types**, allows the creation of complex data types with nested record structures, arrays,  

**4.5 SQL Data Types and Schemas 139**

and multisets. We do not cover structured data types in this chapter, but describe them later, in Chapter 22.

It is possible for several attributes to have the same data type. For example, the _name_ attributes for student name and instructor name might have the same domain: the set of all person names. However, the domains of _budget_ and _dept name_ certainly ought to be distinct. It is perhaps less clear whether _name_ and _dept name_ should have the same domain. At the implementation level, both instructor names and department names are character strings. However, we would normally not consider the query “Find all instructors who have the same name as a department” to be a meaningful query. Thus, if we view the database at the conceptual, rather than the physical, level, _name_ and _dept name_ should have distinct domains.

More importantly, at a practical level, assigning an instructor’s name to a department name is probably a programming error; similarly, comparing a mon- etary value expressed in dollars directly with a monetary value expressed in pounds is also almost surely a programming error. A good type system should be able to detect such assignments or comparisons. To support such checks, SQL provides the notion of **distinct types**.

The **create type** clause can be used to define new types. For example, the statements:

**create type** _Dollars_ **as numeric**(12,2) **final; create type** _Pounds_ **as numeric**(12,2) **final;**

define the user-defined types _Dollars_ and _Pounds_ to be decimal numbers with a total of 12 digits, two of which are placed after the decimal point. (The keyword **final** isn’t really meaningful in this context but is required by the SQL:1999 standard for reasons we won’t get into here; some implementations allow the **final** keyword to be omitted.) The newly created types can then be used, for example, as types of attributes of relations. For example, we can declare the _department_ table as:

**create table** _department_ (_dept name_ **varchar** (20), _building_ **varchar** (15), _budget Dollars_);

An attempt to assign a value of type _Dollars_ to a variable of type _Pounds_ results in a compile-time error, although both are of the same numeric type. Such an assignment is likely to be due to a programmer error, where the programmer forgot about the differences in currency. Declaring different types for different currencies helps catch such errors.

As a result of strong type checking, the expression (_department.budget_+20) would not be accepted since the attribute and the integer constant 20 have differ- ent types. Values of one type can be _cast_ (that is, converted) to another domain, as illustrated below:

**cast** (_department.budget_ **to** _numeric_(12,2))  

**140 Chapter 4 Intermediate SQL**

We could do addition on the numeric type, but to save the result back to an attribute of type _Dollars_ we would have to use another cast expression to convert the type back to _Dollars_.

SQL provides **drop type** and **alter type** clauses to drop or modify types that have been created earlier.

Even before user-defined types were added to SQL (in SQL:1999), SQL had a similar but subtly different notion of **domain** (introduced in SQL-92), which can add integrity constraints to an underlying type. For example, we could define a domain _DDollars_ as follows.

**create domain** _DDollars_ **as numeric**(12,2) **not null**;

The domain _DDollars_ can be used as an attribute type, just as we used the type _Dollars_. However, there are two significant differences between types and do- mains:

**1\.** Domains can have constraints, such as **not null**, specified on them, and can have default values defined for variables of the domain type, whereas user- defined types cannot have constraints or default values specified on them. User-defined types are designed to be used not just for specifying attribute types, but also in procedural extensions to SQL where it may not be possible to enforce constraints.

**2\.** Domains are not strongly typed. As a result, values of one domain type can be assigned to values of another domain type as long as the underlying types are compatible.

When applied to a domain, the **check** clause permits the schema designer to specify a predicate that must be satisfied by any attribute declared to be from this domain. For instance, a **check** clause can ensure that an instructor’s salary domain allows only values greater than a specified value:

**create domain** _YearlySalary_ **numeric**(8,2) **constraint** _salary value test_ **check**(**value** _\>_\= 29000.00);

The domain _YearlySalary_ has a constraint that ensures that the YearlySalary is greater than or equal to $29,000.00. The clause **constraint** _salary value test_ is op- tional, and is used to give the name _salary value test_ to the constraint. The name is used by the system to indicate the constraint that an update violated.

As another example, a domain can be restricted to contain only a specified set of values by using the **in** clause:

**create domain** _degree level_ **varchar**(10) **constraint** _degree level test_

**check** (**value in** (’Bachelors’, ’Masters’, or ’Doctorate’));  

**4.5 SQL Data Types and Schemas 141**

**SUPPORT FOR TYPES AND DOMAINS IN DATABASE IMPLEMENTATIONS**

Although the **create type** and **create domain** constructs described in this section are part of the SQL standard, the forms of these constructs described here are not fully supported by most database implementations. PostgreSQL supports the **create domain** construct, but its **create type** construct has a different syntax and interpretation.

IBM DB2 supports a version of the **create type** that uses the syntax **create distinct type**, but does not support **create domain**. Microsoft SQL Server im- plements a version of **create type** construct that supports domain constraints, similar to the SQL **create domain** construct.

Oracle does not support either construct as described here. However, SQL also defines a more complex object-oriented type system, which we study later in Chapter 22. Oracle, IBM DB2, PostgreSQL, and SQL Server all support object- oriented type systems using different forms of the **create type** construct.

**4.5.6 Create Table Extensions**

Applications often require creation of tables that have the same schema as an existing table. SQL provides a **create table like** extension to support this task:

**create table** _temp instructor_ **like** _instructor_;

The above statement creates a new table _temp instructor_ that has the same schema as _instructor_.

When writing a complex query, it is often useful to store the result of a query as a new table; the table is usually temporary. Two statements are required, one to create the table (with appropriate columns) and the second to insert the query result into the table. SQL:2003 provides a simpler technique to create a table containing the results of a query. For example the following statement creates a table _t1_ containing the results of a query.

**create table** _t1_ **as** (**select** \* **from** _instructor_ **where** _dept name_\= ’Music’)

**with data**;

By default, the names and data types of the columns are inferred from the query result. Names can be explicitly given to the columns by listing the column names after the relation name.

As defined by the SQL:2003 standard, if the **with data** clause is omitted, the table is created but not populated with data. However many implementations populate the table with data by default even if the **with data** clause is omitted.  

**142 Chapter 4 Intermediate SQL**

Note that several implementations support the functionality of **create table** _. . ._

**like** and **create table** _. . ._ **as** using different syntax; see the respective system manuals for further details.

The above **create table** _. . ._ **as** statement closely resembles the **create view** statement and both are defined by using queries. The main difference is that the contents of the table are set when the table is created, whereas the contents of a view always reflect the current query result.

**4.5.7 Schemas, Catalogs, and Environments**

To understand the motivation for schemas and catalogs, consider how files are named in a file system. Early file systems were flat; that is, all files were stored in a single directory. Current file systems, of course, have a directory (or, syn- onymously, folder) structure, with files stored within subdirectories. To name a file uniquely, we must specify the full path name of the file, for example, /users/avi/db-book/chapter3.tex.

Like early file systems, early database systems also had a single name space for all relations. Users had to coordinate to make sure they did not try to use the same name for different relations. Contemporary database systems provide a three-level hierarchy for naming relations. The top level of the hierarchy consists of **catalogs**, each of which can contain **schemas**. SQL objects such as relations and views are contained within a **schema**. (Some database implementations use the term “database” in place of the term catalog.)

In order to perform any actions on a database, a user (or a program) must first _connect_ to the database. The user must provide the user name and usually, a password for verifying the identity of the user. Each user has a default catalog and schema, and the combination is unique to the user. When a user connects to a database system, the default catalog and schema are set up for the connection; this corresponds to the current directory being set to the user’s home directory when the user logs into an operating system.

To identify a relation uniquely, a three-part name may be used, for example,

_catalog5.univ schema.course_

We may omit the catalog component, in which case the catalog part of the name is considered to be the default catalog for the connection. Thus if _catalog5_ is the default catalog, we can use _univ schema.course_ to identify the same relation uniquely.

If a user wishes to access a relation that exists in a different schema than the default schema for that user, the name of the schema must be specified. However, if a relation is in the default schema for a particular user, then even the schema name may be omitted. Thus we can use just _course_ if the default catalog is _catalog5_ and the default schema is _univ schema_.

With multiple catalogs and schemas available, different applications and different users can work independently without worrying about name clashes. Moreover, multiple versions of an application—one a production version, other test versions—can run on the same database system.  

**4.6 Authorization 143**

The default catalog and schema are part of an **SQL environment** that is set up for each connection. The environment additionally contains the user identi- fier (also referred to as the _authorization identifier)._ All the usual SQL statements, including the DDL and DML statements, operate in the context of a schema.

We can create and drop schemas by means of **create schema** and **drop schema** statements. In most database systems, schemas are also created automatically when user accounts are created, with the schema name set to the user account name. The schema is created in either a default catalog, or a catalog specified in creating the user account. The newly created schema becomes the default schema for the user account.

Creation and dropping of catalogs is implementation dependent and not part of the SQL standard.

**4.6 Authorization**

We may assign a user several forms of authorizations on parts of the database. Authorizations on data include:

• Authorization to read data.

• Authorization to insert new data.

• Authorization to update data.

• Authorization to delete data.

Each of these types of authorizations is called a **privilege**. We may authorize the user all, none, or a combination of these types of privileges on specified parts of a database, such as a relation or a view.

When a user submits a query or an update, the SQL implementation first checks if the query or update is authorized, based on the authorizations that the user has been granted. If the query or update is not authorized, it is rejected.

In addition to authorizations on data, users may also be granted authoriza- tions on the database schema, allowing them, for example, to create, modify, or drop relations. A user who has some form of authorization may be allowed to pass on (grant) this authorization to other users, or to withdraw (revoke) an au- thorization that was granted earlier. In this section, we see how each of these authorizations can be specified in SQL.

The ultimate form of authority is that given to the database administrator. The database administrator may authorize new users, restructure the database, and so on. This form of authorization is analogous to that of a **superuser**, administrator, or operator for an operating system.

**4.6.1 Granting and Revoking of Privileges**

The SQL standard includes the privileges **select**, **insert**, **update**, and **delete**. The privilege **all privileges** can be used as a short form for all the allowable privi-  

**144 Chapter 4 Intermediate SQL**

leges. A user who creates a new relation is given all privileges on that relation automatically.

The SQL data-definition language includes commands to grant and revoke privileges. The **grant** statement is used to confer authorization. The basic form of this statement is:

**grant** _<_privilege list_\>_ **on** _<_relation name or view name_\>_

**to** _<_user/role list_\>_;

The _privilege list_ allows the granting of several privileges in one command. The notion of roles is covered later, in Section 4.6.2.

The **select** authorization on a relation is required to read tuples in the relation. The following **grant** statement grants database users Amit and Satoshi **select** authorization on the _department_ relation:

**grant select on** _department_ **to** _Amit_, _Satoshi_;

This allows those users to run queries on the _department_ relation. The **update** authorization on a relation allows a user to update any tuple

in the relation. The **update** authorization may be given either on all attributes of the relation or on only some. If **update** authorization is included in a **grant** statement, the list of attributes on which update authorization is to be granted optionally appears in parentheses immediately after the **update** keyword. If the list of attributes is omitted, the update privilege will be granted on all attributes of the relation.

This **grant** statement gives users Amit and Satoshi update authorization on the _budget_ attribute of the _department_ relation:

**grant update** (_budget_) **on** _department_ **to** _Amit_, _Satoshi_;

The **insert** authorization on a relation allows a user to insert tuples into the relation. The **insert** privilege may also specify a list of attributes; any inserts to the relation must specify only these attributes, and the system either gives each of the remaining attributes default values (if a default is defined for the attribute) or sets them to _null_.

The **delete** authorization on a relation allows a user to delete tuples from a relation.

The user name **public** refers to all current and future users of the system. Thus, privileges granted to **public** are implicitly granted to all current and future users.

By default, a user/role that is granted a privilege is not authorized to grant that privilege to another user/role. SQL allows a privilege grant to specify that the recipient may further grant the privilege to another user. We describe this feature in more detail in Section 4.6.5.  

**4.6 Authorization 145**

It is worth noting that the SQL authorization mechanism grants privileges on an entire relation, or on specified attributes of a relation. However, it does not permit authorizations on specific tuples of a relation.

To revoke an authorization, we use the **revoke** statement. It takes a form almost identical to that of **grant**:

**revoke** _<_privilege list_\>_ **on** _<_relation name or view name_\>_

**from** _<_user/role list_\>_;

Thus, to revoke the privileges that we granted previously, we write

**revoke select on** _department_ **from** _Amit_, _Satoshi_; **revoke update** (budget) **on** _department_ **from** _Amit_, _Satoshi_;

Revocation of privileges is more complex if the user from whom the privilege is revoked has granted the privilege to another user. We return to this issue in Section 4.6.5.

**4.6.2 Roles**

Consider the real-world roles of various people in a university. Each instructor must have the same types of authorizations on the same set of relations. Whenever a new instructor is appointed, she will have to be given all these authorizations individually.

A better approach would be to specify the authorizations that every instructor is to be given, and to identify separately which database users are instructors. The system can use these two pieces of information to determine the authorizations of each instructor. When a new instructor is hired, a user identifier must be allocated to him, and he must be identified as an instructor. Individual permissions given to instructors need not be specified again.

The notion of **roles** captures this concept. A set of roles is created in the database. Authorizations can be granted to roles, in exactly the same fashion as they are granted to individual users. Each database user is granted a set of roles (which may be empty) that she is authorized to perform.

In our university database, examples of roles could include _instructor_, _teaching assistant_, _student_, _dean_, and _department chair_.

A less preferable alternative would be to create an _instructor_ userid, and permit each instructor to connect to the database using the _instructor_ userid. The problem with this approach is that it would not be possible to identify exactly which instructor carried out a database update, leading to security risks. The use of roles has the benefit of requiring users to connect to the database with their own userid.

Any authorization that can be granted to a user can be granted to a role. Roles are granted to users just as authorizations are.  

**146 Chapter 4 Intermediate SQL**

Roles can be created in SQL as follows:

**create role** _instructor_;

Roles can then be granted privileges just as the users can, as illustrated in this statement:

**grant select on** _takes_ **to** _instructor_;

Roles can be granted to users, as well as to other roles, as these statements show:

**grant** _dean_ **to** Amit; **create role** _dean_; **grant** _instructor_ **to** _dean_; **grant** _dean_ **to** Satoshi;

Thus the privileges of a user or a role consist of:

• All privileges directly granted to the user/role.

• All privileges granted to roles that have been granted to the user/role.

Note that there can be a chain of roles; for example, the role _teaching assistant_ may be granted to all _instructors_. In turn the role _instructor_ is granted to all _dean_s. Thus, the _dean_ role inherits all privileges granted to the roles _instructor_ and to _teaching assistant_ in addition to privileges granted directly to _dean_.

When a user logs in to the database system, the actions executed by the user during that session have all the privileges granted directly to the user, as well as all privileges granted to roles that are granted (directly or indirectly via other roles) to that user. Thus, if a user Amit has been granted the role _dean_, user Amit holds all privileges granted directly to Amit, as well as privileges granted to _dean_, plus privileges granted to _instructor,_ and _teaching assistant_ if, as above, those roles were granted (directly or indirectly) to the role _dean_.

It is worth noting that the concept of role-based authorization is not specific to SQL, and role-based authorization is used for access control in a wide variety of shared applications.

**4.6.3 Authorization on Views**

In our university example, consider a staff member who needs to know the salaries of all faculty in a particular department, say the Geology department. This staff member is not authorized to see information regarding faculty in other departments. Thus, the staff member must be denied direct access to the _instructor_ relation. But, if he is to have access to the information for the Geology department, he might be granted access to a view that we shall call _geo instructor_, consisting  

**4.6 Authorization 147**

of only those _instructor_ tuples pertaining to the Geology department. This view can be defined in SQL as follows:

**create view** _geo instructor_ **as** (**select** \* **from** _instructor_ **where** _dept name_ \= ’Geology’);

Suppose that the staff member issues the following SQL query:

**select** \* **from** _geo instructor_;

Clearly, the staff member is authorized to see the result of this query. However, when the query processor translates it into a query on the actual relations in the database, it produces a query on _instructor_. Thus, the system must check authorization on the clerk’s query before it begins query processing.

A user who creates a view does not necessarily receive all privileges on that view. She receives only those privileges that provide no additional authorization beyond those that she already had. For example, a user who creates a view cannot be given **update** authorization on a view without having **update** authorization on the relations used to define the view. If a user creates a view on which no authorization can be granted, the system will deny the view creation request. In our _geo instructor_ view example, the creator of the view must have **select** authorization on the _instructor_ relation.

As we will see later, in Section 5.2, SQL supports the creation of functions and procedures, which may in turn contain queries and updates. The **execute** privilege can be granted on a function or procedure, enabling a user to execute the function/procedure. By default, just like views, functions and procedures have all the privileges that the creator of the function or procedure had. In effect, the function or procedure runs as if it were invoked by the user who created the function.

Although this behavior is appropriate in many situations, it is not always appropriate. Starting with SQL:2003, if the function definition has an extra clause **sql security invoker**, then it is executed under the privileges of the user who invokes the function, rather than the privileges of the **definer** of the function. This allows the creation of libraries of functions that can run under the same authorization as the invoker.

**4.6.4 Authorizations on Schema**

The SQL standard specifies a primitive authorization mechanism for the database schema: Only the owner of the schema can carry out any modification to the schema, such as creating or deleting relations, adding or dropping attributes of relations, and adding or dropping indices.  

**148 Chapter 4 Intermediate SQL**

However, SQL includes a **references** privilege that permits a user to declare foreign keys when creating relations. The SQL **references** privilege is granted on specific attributes in a manner like that for the **update** privilege. The following **grant** statement allows user Mariano to create relations that reference the key _branch name_ of the _branch_ relation as a foreign key:

**grant references** (_dept name_) **on** _department_ **to** Mariano;

Initially, it may appear that there is no reason ever to prevent users from creating foreign keys referencing another relation. However, recall that foreign- key constraints restrict deletion and update operations on the referenced relation. Suppose Mariano creates a foreign key in a relation _r_ referencing the _dept name_ attribute of the _department_ relation and then inserts a tuple into _r_ pertaining to the Geology department. It is no longer possible to delete the Geology department from the _department_ relation without also modifying relation _r_ . Thus, the definition of a foreign key by Mariano restricts future activity by other users; therefore, there is a need for the **references** privilege.

Continuing to use the example of the _department_ relation, the references priv- ilege on _department_ is also required to create a **check** constraint on a relation _r_ if the constraint has a subquery referencing _department_. This is reasonable for the same reason as the one we gave for foreign-key constraints; a check constraint that references a relation limits potential updates to that relation.

**4.6.5 Transfer of Privileges**

A user who has been granted some form of authorization may be allowed to pass on this authorization to other users. By default, a user/role that is granted a privilege is not authorized to grant that privilege to another user/role. If we wish to grant a privilege and to allow the recipient to pass the privilege on to other users, we append the **with grant option** clause to the appropriate **grant** command. For example, if we wish to allow Amit the **select** privilege on _department_ and allow Amit to grant this privilege to others, we write:

**grant select on** _department_ **to** Amit **with grant option**;

The creator of an object (relation/view/role) holds all privileges on the object, including the privilege to grant privileges to others.

Consider, as an example, the granting of update authorization on the _teaches_ relation of the university database. Assume that, initially, the database adminis- trator grants update authorization on _teaches_ to users _U_1, _U_2, and _U_3, who may in turn pass on this authorization to other users. The passing of a specific authoriza- tion from one user to another can be represented by an **authorization graph**. The nodes of this graph are the users.

Consider the graph for update authorization on _teaches_. The graph includes an edge _Ui_ → _Uj_ if user _Ui_ grants update authorization on _teaches_ to _Uj_ . The root of the graph is the database administrator. In the sample graph in Figure 4.10,  

**4.6 Authorization 149**

_U_3

DBA

_U_1

_U_5_U_2

_U_4

**Figure 4.10** Authorization-grant graph (_U_1_,U_2_, . . . , U_5 are users and DBA refers to the database administrator).

observe that user _U_5 is granted authorization by both _U_1 and _U_2; _U_4 is granted authorization by only _U_1.

A user has an authorization _if and only if_ there is a path from the root of the authorization graph (the node representing the database administrator) down to the node representing the user.

**4.6.6 Revoking of Privileges**

Suppose that the database administrator decides to revoke the authorization of user _U_1\. Since _U_4 has authorization from _U_1, that authorization should be revoked as well. However, _U_5 was granted authorization by both _U_1 and _U_2\. Since the database administrator did not revoke update authorization on _teaches_ from _U_2, _U_5 retains update authorization on _teaches_. If _U_2 eventually revokes authorization from _U_5, then _U_5 loses the authorization.

A pair of devious users might attempt to defeat the rules for revocation of authorization by granting authorization to each other. For example, if _U_2 is initially granted an authorization by the database administrator, and _U_2 further grants it to _U_3\. Suppose _U_3 now grants the privilege back to _U_2\. If the database administrator revokes authorization from _U_2, it might appear that _U_2 retains authorization through _U_3\. However, note that once the administrator revokes authorization from _U_2, there is no path in the authorization graph from the root to either _U_2 or to _U_3\. Thus, SQL ensures that the authorization is revoked from both the users.

As we just saw, revocation of a privilege from a user/role may cause other users/roles also to lose that privilege. This behavior is called _cascading revocation_. In most database systems, cascading is the default behavior. However, the **revoke** statement may specify **restrict** in order to prevent cascading revocation:

**revoke select on** _department_ **from** Amit, Satoshi **restrict**;  

**150 Chapter 4 Intermediate SQL**

In this case, the system returns an error if there are any cascading revocations, and does not carry out the revoke action.

The keyword **cascade** can be used instead of **restrict** to indicate that revocation should cascade; however, it can be omitted, as we have done in the preceding examples, since it is the default behavior.

The following **revoke** statement revokes only the grant option, rather than the actual **select** privilege:

**revoke grant option for select on** _department_ **from** Amit;

Note that some database implementations do not support the above syntax; in- stead, the privilege itself can be revoked, and then granted again without the grant option.

Cascading revocation is inappropriate in many situations. Suppose Satoshi has the role of _dean_, grants _instructor_ to Amit, and later the role _dean_ is revoked from Satoshi (perhaps because Satoshi leaves the university); Amit continues to be employed on the faculty, and should retain the _instructor_ role.

To deal with the above situation, SQL permits a privilege to be granted by a role rather than by a user. SQL has a notion of the current role associated with a session. By default, the current role associated with a session is null (except in some special cases). The current role associated with a session can be set by executing **set role** _role name_. The specified role must have been granted to the user, else the **set role** statement fails.

To grant a privilege with the grantor set to the current role associated with a session, we can add the clause:

**granted by current role**

to the grant statement, provided the current role is not null. Suppose the granting of the role _instructor_ (or other privileges) to Amit is

done using the **granted by current role** clause, with the current role set to _dean_), instead of the grantor being the user Satoshi. Then, revoking of roles/privileges (including the role _dean_) from Satoshi will not result in revoking of privileges that had the grantor set to the role _dean_, even if Satoshi was the user who executed the grant; thus, Amit would retain the _instructor_ role even after Satoshi’s privileges are revoked.

**4.7 Summary**

• SQL supports several types of joins including inner and outer joins and several types of join conditions.

• View relations can be defined as relations containing the result of queries. Views are useful for hiding unneeded information, and for collecting together information from more than one relation into a single view.  

**Review Terms 151**

• Transactions are a sequence of queries and updates that together carry out a task. Transactions can be committed, or rolled back; when a transaction is rolled back, the effects of all updates performed by the transaction are undone.

• Integrity constraints ensure that changes made to the database by authorized users do not result in a loss of data consistency.

• Referential-integrity constraints ensure that a value that appears in one rela- tion for a given set of attributes also appears for a certain set of attributes in another relation.

• Domain constraints specify the set of possible values that may be associated with an attribute. Such constraints may also prohibit the use of null values for particular attributes.

• Assertions are declarative expressions that state predicates that we require always to be true.

• The SQL data-definition language provides support for defining built-in do- main types such as **date** and **time**, as well as user-defined domain types.

• SQL authorization mechanisms allow one to differentiate among the users of the database as far as the type of access they are permitted on various data values in the database.

• A user who has been granted some form of authority may be allowed to pass on this authority to other users. However, we must be careful about how authorization can be passed among users if we are to ensure that such authorization can be revoked at some future time.

• Roles help to assign a set of privileges to a user according to the role that the user plays in the organization.

**Review Terms**

• Join types

◦ Inner and outer join

◦ Left, right and full outer join

◦ Natural, using, and on

• View definition • Materialized views • View update • Transactions

◦ Commit work

◦ Rollback work

◦ Atomic transaction

• Integrity constraints • Domain constraints • Unique constraint • Check clause • Referential integrity

◦ Cascading deletes

◦ Cascading updates  

**152 Chapter 4 Intermediate SQL**

• Assertions • Date and time types • Default values • Indices • Large objects • User-defined types • Domains • Catalogs • Schemas • Authorization • Privileges

◦ **select**

◦ **insert**

◦ **update**

◦ **all privileges**

◦ Granting of privileges

◦ Revoking of privileges

◦ Privilege to grant privileges

◦ Grant option

• Roles • Authorization on views • Execute authorization • Invoker privileges • Row-level authorization

**Practice Exercises**

**4.1** Write the following queries in SQL:

a. Display a list of all instructors, showing their ID, name, and the num- ber of sections that they have taught. Make sure to show the number of sections as 0 for instructors who have not taught any section. Your query should use an outerjoin, and should not use scalar subqueries.

b. Write the same query as above, but using a scalar subquery, without outerjoin.

c. Display the list of all course sections offered in Spring 2010, along with the names of the instructors teaching the section. If a section has more than one instructor, it should appear as many times in the result as it has instructors. If it does not have any instructor, it should still appear in the result with the instructor name set to “—”.

d. Display the list of all departments, with the total number of instructors in each department, without using scalar subqueries. Make sure to correctly handle departments with no instructors.

**4.2** Outer join expressions can be computed in SQL without using the SQL **outer join** operation. To illustrate this fact, show how to rewrite each of the following SQL queries without using the **outer join** expression.

a. **select**\* **from** _student_ **natural left outer join** _takes_

b. **select**\* **from** _student_ **natural full outer join** _takes_  

**Practice Exercises 153**

**4.3** Suppose we have three relations _r_ (_A_, _B_), _s_(_B_, _C_), and _t_(_B_, _D_), with all attributes declared as **not null**. Consider the expressions

• _r_ **natural left outer join** (_s_ **natural left outer join** _t_), and

• (_r_ **natural left outer join** _s_) **natural left outer join** _t_

a. Give instances of relations _r_ , _s_ and _t_ such that in the result of the second expression, attribute _C_ has a null value but attribute _D_ has a non-null value.

b. Is the above pattern, with _C_ null and _D_ not null possible in the result of the first expression? Explain why or why not.

**4.4 Testing SQL queries**: To test if a query specified in English has been cor- rectly written in SQL, the SQL query is typically executed on multiple test databases, and a human checks if the SQL query result on each test database matches the intention of the specification in English.

a. In Section 3.3.3 we saw an example of an erroneous SQL query which was intended to find which courses had been taught by each instruc- tor; the query computed the natural join of _instructor_, _teaches_, and _course_, and as a result unintentionally equated the _dept name_ attribute of _instructor_ and _course_. Give an example of a dataset that would help catch this particular error.

b. When creating test databases, it is important to create tuples in refer- enced relations that do not have any matching tuple in the referencing relation, for each foreign key. Explain why, using an example query on the university database.

c. When creating test databases, it is important to create tuples with null values for foreign key attributes, provided the attribute is nullable (SQL allows foreign key attributes to take on null values, as long as they are not part of the primary key, and have not been declared as **not null**). Explain why, using an example query on the university database.

_Hint_: use the queries from Exercise 4.1.

**4.5** Show how to define the view _student grades_ (_ID, GPA_) giving the grade- point average of each student, based on the query in Exercise 3.2; recall that we used a relation _grade points_(_grade_, _points_) to get the numeric points associated with a letter grade. Make sure your view definition correctly handles the case of _null_ values for the _grade_ attribute of the _takes_ relation.

**4.6** Complete the SQL DDL definition of the university database of Figure 4.8 to include the relations _student_, _takes_, _advisor_, and _prereq_.  

**154 Chapter 4 Intermediate SQL**

_employee_ (_employee name_, _street_, _city_) _works_ (_employee name_, _company name_, _salary_) _company_ (_company name_, _city_) _manages_ (_employee name_, _manager name_)

**Figure 4.11** Employee database for Figure 4.7 and 4.12.

**4.7** Consider the relational database of Figure 4.11. Give an SQL DDL definition of this database. Identify referential-integrity constraints that should hold, and include them in the DDL definition.

**4.8** As discussed in Section 4.4.7, we expect the constraint “an instructor cannot teach sections in two different classrooms in a semester in the same time slot” to hold.

a. Write an SQL query that returns all (_instructor_, _section_) combinations that violate this constraint.

b. Write an SQL assertion to enforce this constraint (as discussed in Section 4.4.7, current generation database systems do not support such assertions, although they are part of the SQL standard).

**4.9** SQL allows a foreign-key dependency to refer to the same relation, as in the following example:

**create table** _manager_ (_employee name_ **varchar**(20) **not null** _manager name_ **varchar**(20) **not null**, **primary key** _employee name_, **foreign key** (_manager name_) **references** _manager_

**on delete cascade** )

Here, _employee name_ is a key to the table _manager_, meaning that each em- ployee has at most one manager. The foreign-key clause requires that every manager also be an employee. Explain exactly what happens when a tuple in the relation _manager_ is deleted.

**4.10** SQL provides an _n_\-ary operation called **coalesce**, which is defined as follows: **coalesce**(_A_1_, A_2_, . . . , An_) returns the first nonnull _Ai_ in the list _A_1_, A_2_, . . . , An_, and returns _null_ if all of _A_1_, A_2_, . . . , An_ are _null_.

Let _a_ and _b_ be relations with the schemas _A_(_name, address, title_), and _B_(_name, address, salary_), respectively. Show how to express _a_ **natural full outer join** _b_ using the **full outer-join** operation with an **on** condition and the **coalesce** operation. Make sure that the result relation does not contain two copies of the attributes _name_ and _address_, and that the solution is correct even if some tuples in _a_ and _b_ have null values for attributes _name_ or _address_.  

**Exercises 155**

_salaried worker_ (_name, office, phone, salary_) _hourly worker_ (_name, hourly wage_) _address_ (_name, street, city_)

**Figure 4.12** Employee database for Exercise 4.16.

**4.11** Some researchers have proposed the concept of _marked_ nulls. A marked null ⊥_i_ is equal to itself, but if _i_ = _j_ , then ⊥_i_ = ⊥ _j_ . One application of marked nulls is to allow certain updates through views. Consider the view _instructor info_ (Section 4.2). Show how you can use marked nulls to allow the insertion of the tuple (99999, “Johnson”, “Music”) through _instructor info_.

**Exercises**

**4.12** For the database of Figure 4.11, write a query to find those employees with no manager. Note that an employee may simply have no manager listed or may have a _null_ manager. Write your query using an outer join and then write it again using no outer join at all.

**4.13** Under what circumstances would the query

**select** \* **from** _student_ **natural full outer join** _takes_ **natural full outer join** _course_

include tuples with null values for the _title_ attribute?

**4.14** Show how to define a view _tot credits_ (_year, num credits_), giving the total number of credits taken by students in each year.

**4.15** Show how to express the **coalesce** operation from Exercise 4.10 using the **case** operation.

**4.16** Referential-integrity constraints as defined in this chapter involve exactly two relations. Consider a database that includes the relations shown in Figure 4.12. Suppose that we wish to require that every name that appears in _address_ appears in either _salaried worker_ or _hourly worker_, but not necessarily in both.

a. Propose a syntax for expressing such constraints.

b. Discuss the actions that the system must take to enforce a constraint of this form.

**4.17** Explain why, when a manager, say Satoshi, grants an authorization, the grant should be done by the manager role, rather than by the user Satoshi.  

**156 Chapter 4 Intermediate SQL**

**4.18** Suppose user _A_, who has all authorizations on a relation _r_ , grants select on relation _r_ to **public** with grant option. Suppose user _B_ then grants select on _r_ to _A_. Does this cause a cycle in the authorization graph? Explain why.

**4.19** Database systems that store each relation in a separate operating-system file may use the operating system’s authorization scheme, instead of defining a special scheme themselves. Discuss an advantage and a disadvantage of such an approach.

**Bibliographical Notes**

See the bibliographic notes of Chapter 3 for SQL reference material. The rules used by SQL to determine the updatability of a view, and how

updates are reflected on the underlying database relations, are defined by the SQL:1999 standard, and are summarized in Melton and Simon \[2001\].  

**_C H A P T E R_5 Advanced SQL**

In Chapters 3 and 4, we provided detailed coverage of the basic structure of SQL. In this chapter, we cover some of the more advanced features of SQL.1 We address the issue of how to access SQL from a general-purpose programming language, which is very important for building applications that use a database to store and retrieve data. We describe how procedural code can be executed within the database, either by extending the SQL language to support procedural actions, or by allowing functions defined in procedural languages to be executed within the database. We describe triggers, which can be used to specify actions that are to be carried out automatically on certain events such as insertion, deletion, or update of tuples in a specified relation. We discuss recursive queries and advanced aggregation features supported by SQL. Finally, we describe online analytic processing (OLAP) systems, which support interactive analysis of very large datasets.

**5.1 Accessing SQL From a Programming Language**

SQL provides a powerful declarative query language. Writing queries in SQL is usually much easier than coding the same queries in a general-purpose pro- gramming language. However, a database programmer must have access to a general-purpose programming language for at least two reasons:

**1\.** Not all queries can be expressed in SQL, since SQL does not provide the full expressive power of a general-purpose language. That is, there exist queries that can be expressed in a language such as C, Java, or Cobol that cannot be expressed in SQL. To write such queries, we can embed SQL within a more powerful language.

1**Note regarding chapter and section sequencing:** Database design—Chapters 7 and 8—can be studied independently of the material in this chapter. It is quite possible to study database design first, and study this chapter later. However, for courses with a programming emphasis, a richer variety of laboratory exercises is possible after studying Section 5.1, and we recommend that it be covered before database design for such courses.

**157**  

**158 Chapter 5 Advanced SQL**

**2\.** Nondeclarative actions—such as printing a report, interacting with a user, or sending the results of a query to a graphical user interface—cannot be done from within SQL. Applications usually have several components, and querying or updating data is only one component; other components are written in general-purpose programming languages. For an integrated application, there must be a means to combine SQL with a general-purpose programming language.

There are two approaches to accessing SQL from a general-purpose program- ming language:

• **Dynamic SQL**: A general-purpose program can connect to and communicate with a database server using a collection of functions (for procedural lan- guages) or methods (for object-oriented languages). Dynamic SQL allows the program to construct an SQL query as a character string at runtime, submit the query, and then retrieve the result into program variables a tuple at a time. The _dynamic SQL_ component of SQL allows programs to construct and submit SQL queries at runtime.

In this chapter, we look at two standards for connecting to an SQL database and performing queries and updates. One, JDBC (Section 5.1.1), is an applica- tion program interface for the Java language. The other, ODBC (Section 5.1.2), is an application program interface originally developed for the C language, and subsequently extended to other languages such as C++, C#, and Visual Basic.

• **Embedded SQL**: Like dynamic SQL, embedded SQL provides a means by which a program can interact with a database server. However, under em- bedded SQL, the SQL statements are identified at compile time using a prepro- cessor. The preprocessor submits the SQL statements to the database system for precompilation and optimization; then it replaces the SQL statements in the application program with appropriate code and function calls before in- voking the programming-language compiler. Section 5.1.3 covers embedded SQL.

A major challenge in mixing SQL with a general-purpose language is the mismatch in the ways these languages manipulate data. In SQL, the primary type of data is the relation. SQL statements operate on relations and return relations as a result. Programming languages normally operate on a variable at a time, and those variables correspond roughly to the value of an attribute in a tuple in a relation. Thus, integrating these two types of languages into a single application requires providing a mechanism to return the result of a query in a manner that the program can handle.

**5.1.1 JDBC**

The **JDBC** standard defines an **application program interface (API)** that Java programs can use to connect to database servers. (The word JDBC was originally  

**5.1 Accessing SQL From a Programming Language 159**

public static void JDBCexample(String userid, String passwd) _{_

try _{_

Class.forName ("oracle.jdbc.driver.OracleDriver"); Connection conn = DriverManager.getConnection(

"jdbc:oracle:thin:@db.yale.edu:1521:univdb", userid, passwd);

Statement stmt = conn.createStatement(); try _{_

stmt.executeUpdate( "insert into instructor values(’77987’, ’Kim’, ’Physics’, 98000)");

_}_ catch (SQLException sqle) _{_

System.out.println("Could not insert tuple. " + sqle); _}_ ResultSet rset = stmt.executeQuery(

"select dept name, avg (salary) "+ " from instructor "+ " group by dept name");

while (rset.next()) _{_ System.out.println(rset.getString("dept name") + " " +

rset.getFloat(2)); _}_ stmt.close(); conn.close();

_}_ catch (Exception sqle) _{_

System.out.println("Exception : " + sqle); _}_

_}_

**Figure 5.1** An example of JDBC code.

an abbreviation for **Java Database Connectivity**, but the full form is no longer used.)

Figure 5.1 shows an example Java program that uses the JDBC interface. It illustrates how connections are opened, how statements are executed and results processed, and how connections are closed. We discuss this example in detail in this section. The Java program must import java.sql.\*, which contains the interface definitions for the functionality provided by JDBC.

**5.1.1.1 Connecting to the Database**

The first step in accessing a database from a Java program is to open a connection to the database. This step is required to select which database to use, for example,  

**160 Chapter 5 Advanced SQL**

an instance of Oracle running on your machine, or a PostgreSQL database running on another machine. Only after opening a connection can a Java program execute SQL statements.

A connection is opened using the getConnection method of the Driver- Manager class (within java.sql). This method takes three parameters.2

• The first parameter to the getConnection call is a string that specifies the URL, or machine name, where the server runs (in our example, db.yale.edu), along with possibly some other information such as the protocol to be used to communicate with the database (in our example, jdbc:oracle:thin:; we shall shortly see why this is required), the port number the database system uses for communication (in our example, 2000), and the specific database on the server to be used (in our example, univdb). Note that JDBC specifies only the API, not the communication protocol. A JDBC driver may support multiple protocols, and we must specify one supported by both the database and the driver. The protocol details are vendor specific.

• The second parameter to getConnection is a database user identifier, which is a string.

• The third parameter is a password, which is also a string. (Note that the need to specify a password within the JDBC code presents a security risk if an unauthorized person accesses your Java code.)

In our example in the figure, we have created a Connection object whose handle is conn.

Each database product that supports JDBC (all the major database vendors do) provides a JDBC driver that must be dynamically loaded in order to access the database from Java. In fact, loading the driver must be done first, before connecting to the database.

This is done by invoking Class.forName with one argument specifying a concrete class implementing the java.sql.Driver interface, in the first line of the program in Figure 5.1. This interface provides for the translation of product- independent JDBC calls into the product-specific calls needed by the specific database management system being used. The example in the figure shows the Oracle driver, oracle.jdbc.driver.OracleDriver.3 The driver is available in a .jar file at vendor Web sites and should be placed within the classpath so that the Java compiler can access it.

The actual protocol used to exchange information with the database de- pends on the driver that is used, and is not defined by the JDBC standard. Some

2There are multiple versions of the getConnection method, which differ in the parameters that they accept. We present the most commonly used version. 3The equivalent driver names for other products are as follows: IBM DB2: com.ibm.db2.jdbc.app.DB2Driver; Mi- crosoft SQL Server: com.microsoft.sqlserver.jdbc.SQLServerDriver; PostgreSQL: org.postgresql.Driver; and MySQL: com.mysql.jdbc.Driver. Sun also offers a “bridge driver” that converts JDBC calls to ODBC. This should be used only for vendors that support ODBC but not JDBC.  

**5.1 Accessing SQL From a Programming Language 161**

drivers support more than one protocol, and a suitable protocol must be cho- sen depending on what protocol the database that you are connecting to sup- ports. In our example, when opening a connection with the database, the string jdbc:oracle:thin: specifies a particular protocol supported by Oracle.

**5.1.1.2 Shipping SQL Statements to the Database System**

Once a database connection is open, the program can use it to send SQL statements to the database system for execution. This is done via an instance of the class Statement. A Statement object is not the SQL statement itself, but rather an object that allows the Java program to invoke methods that ship an SQL statement given as an argument for execution by the database system. Our example creates a Statement handle (stmt) on the connection conn.

To execute a statement, we invoke either the executeQuery method or the executeUpdate method, depending on whether the SQL statement is a query (and, thus, returns a result set) or nonquery statement such as **update**, **insert**, **delete**, **create table**, etc. In our example, stmt.executeUpdate executes an up- date statement that inserts into the _instructor_ relation. It returns an integer giving the number of tuples inserted, updated, or deleted. For DDL statements, the return value is zero. The try _{ . . . }_ catch _{ . . . }_ construct permits us to catch any exceptions (error conditions) that arise when JDBC calls are made, and print an appropriate message to the user.

**5.1.1.3 Retrieving the Result of a Query**

The example program executes a query by using stmt.executeQuery. It retrieves the set of tuples in the result into a ResultSet object rset and fetches them one tuple at a time. The next method on the result set tests whether or not there remains at least one unfetched tuple in the result set and if so, fetches it. The return value of the next method is a Boolean indicating whether it fetched a tuple. Attributes from the fetched tuple are retrieved using various methods whose names begin with get. The method getString can retrieve any of the basic SQL data types (converting the value to a Java String object), but more restrictive methods such as getFloat can be used as well. The argument to the various get methods can either be an attribute name specified as a string, or an integer indicating the position of the desired attribute within the tuple. Figure 5.1 shows two ways of retrieving the values of attributes in a tuple: using the name of the attribute (_dept name_) and using the position of the attribute (2, to denote the second attribute).

The statement and connection are both closed at the end of the Java program. Note that it is important to close the connection because there is a limit imposed on the number of connections to the database; unclosed connections may cause that limit to be exceeded. If this happens, the application cannot open any more connections to the database.  

**162 Chapter 5 Advanced SQL**

PreparedStatement pStmt = conn.prepareStatement( "insert into instructor values(?,?,?,?)");

pStmt.setString(1, "88877"); pStmt.setString(2, "Perry"); pStmt.setString(3, "Finance"); pStmt.setInt(4, 125000); pStmt.executeUpdate(); pStmt.setString(1, "88878"); pStmt.executeUpdate();

**Figure 5.2** Prepared statements in JDBC code.

**5.1.1.4 Prepared Statements**

We can create a prepared statement in which some values are replaced by “?”, thereby specifying that actual values will be provided later. The database system compiles the query when it is prepared. Each time the query is executed (with new values to replace the “?”s), the database system can reuse the previously compiled form of the query and apply the new values. The code fragment in Figure 5.2 shows how prepared statements can be used.

The prepareStatement method of the Connection class submits an SQL statement for compilation. It returns an object of class PreparedStatement. At this point, no SQL statement has been executed. The executeQuery and execu- teUpdate methods of PreparedStatement class do that. But before they can be invoked, we must use methods of class PreparedStatement that assign values for the “?” parameters. The setStringmethod and other similar methods such as setInt for other basic SQL types allow us to specify the values for the parameters. The first argument specifies the “?” parameter for which we are assigning a value (the first parameter is 1, unlike most other Java constructs, which start with 0). The second argument specifies the value to be assigned.

In the example in the figure, we prepare an **insert** statement, set the “?” pa- rameters, and then invoke executeUpdate. The final two lines of our example show that parameter assignments remain unchanged until we specifically reas- sign them. Thus, the final statement, which invokes executeUpdate, inserts the tuple (“88878”, “Perry”, “Finance”, 125000).

Prepared statements allow for more efficient execution in cases where the same query can be compiled once and then run multiple times with different parameter values. However, there is an even more significant advantage to pre- pared statements that makes them the preferred method of executing SQL queries whenever a user-entered value is used, even if the query is to be run only once. Suppose that we read in a user-entered value and then use Java string manipu- lation to construct the SQL statement. If the user enters certain special characters, such as a single quote, the resulting SQL statement may be syntactically incorrect unless we take extraordinary care in checking the input. The setString method does this for us automatically and inserts the needed escape characters to ensure syntactic correctness.  

**5.1 Accessing SQL From a Programming Language 163**

In our example, suppose that the values for the variables _ID_, _name_, _dept name_, and _salary_ have been entered by a user, and a corresponding row is to be inserted into the _instructor_ relation. Suppose that, instead of using a prepared statement, a query is constructed by concatenating the strings using the following Java expression:

"insert into instructor values(’ " + ID + " ’, ’ " + name + " ’, " + " ’ + dept name + " ’, " ’ balance + ")"

and the query is executed directly using the executeQuery method of a State- ment object. Now, if the user typed a single quote in the ID or name fields, the query string would have a syntax error. It is quite possible that an instructor name may have a quotation mark in its name (for example, “O’Henry”).

While the above example might be considered an annoyance, the situation can be much worse. A technique called **SQL injection** can be used by malicious hackers to steal data or damage the database.

Suppose a Java program inputs a string _name_ and constructs the query:

"select \* from instructor where name = ’" + name + "’"

If the user, instead of entering a name, enters:

X’ or ’Y’ = ’Y

then the resulting statement becomes:

"select \* from instructor where name = ’" + "X’ or ’Y’ = ’Y" + "’"

which is:

select \* from instructor where name = ’X’ or ’Y’ = ’Y’

In the resulting query, the **where** clause is always true and the entire instructor relation is returned. More clever malicious users could arrange to output even more data. Use of a prepared statement would prevent this problem because the input string would have escape characters inserted, so the resulting query becomes:

"select \* from instructor where name = ’X\\’ or \\’Y\\’ = \\’Y’

which is harmless and returns the empty relation. Older systems allow multiple statements to be executed in a single call, with

statements separated by a semicolon. This feature is being eliminated because the SQL injection technique was used by malicious hackers to insert whole SQL statements. Because these statements run with the privileges of the owner of the  

**164 Chapter 5 Advanced SQL**

Java program, devastating SQL statements such as **drop table** could be executed. Developers of SQL applications need to be wary of such potential security holes.

**5.1.1.5 Callable Statements**

JDBC also provides a CallableStatement interface that allows invocation of SQL stored procedures and functions (described later, in Section 5.2). These play the same role for functions and procedures as prepareStatement does for queries.

CallableStatement cStmt1 = conn.prepareCall("_{_? = call some function(?)_}_"); CallableStatement cStmt2 = conn.prepareCall("_{_call some procedure(?,?)_}_");

The data types of function return values and out parameters of procedures must be registered using the method registerOutParameter(), and can be retrieved using get methods similar to those for result sets. See a JDBC manual for more details.

**5.1.1.6 Metadata Features**

As we noted earlier, a Java application program does not include declarations for data stored in the database. Those declarations are part of the SQL DDL statements. Therefore, a Java program that uses JDBC must either have assumptions about the database schema hard-coded into the program or determine that information directly from the database system at runtime. The latter approach is usually preferable, since it makes the application program more robust to changes in the database schema.

Recall that when we submit a query using the executeQuery method, the result of the query is contained in a ResultSet object. The interface ResultSet has a method, getMetaData(), that returns a ResultSetMetaData object that contains metadata about the result set. ResultSetMetaData, in turn, has methods to find metadata information, such as the number of columns in the result, the name of a specified column, or the type of a specified column. In this way, we can execute a query even if we have no idea of the schema of the result.

The Java code segment below uses JDBC to print out the names and types of all columns of a result set. The variable rs in the code below is assumed to refer to a ResultSet instance obtained by executing a query.

ResultSetMetaData rsmd = rs.getMetaData(); for(int i = 1; i _<_\= rsmd.getColumnCount(); i++) _{_

System.out.println(rsmd.getColumnName(i)); System.out.println(rsmd.getColumnTypeName(i));

_}_

The getColumnCount method returns the arity (number of attributes) of the result relation. That allows us to iterate through each attribute (note that we start  

**5.1 Accessing SQL From a Programming Language 165**

DatabaseMetaData dbmd = conn.getMetaData(); ResultSet rs = dbmd.getColumns(null, "univdb", "department", "%");

// Arguments to getColumns: Catalog, Schema-pattern, Table-pattern, // and Column-Pattern // Returns: One row for each column; row has a number of attributes // such as COLUMN NAME, TYPE NAME

while( rs.next()) _{_ System.out.println(rs.getString("COLUMN NAME"),

rs.getString("TYPE NAME"); _}_

**Figure 5.3** Finding column information in JDBC using DatabaseMetaData.

at 1, as is conventional in JDBC). For each attribute, we retrieve its name and data type using the methods getColumnName and getColumnTypeName, respectively.

The DatabaseMetaData interface provides a way to find metadata about the database. The interface Connection has a method getMetaData that returns a DatabaseMetaData object. The DatabaseMetaData interface in turn has a very large number of methods to get metadata about the database and the database system to which the application is connected.

For example, there are methods that return the product name and version number of the database system. Other methods allow the application to query the database system about its supported features.

Still other methods return information about the database itself. The code in Figure 5.3 illustrates how to find information about columns (attributes) of relations in a database. The variable conn is assumed to be a handle for an already opened database connection. The method getColumns takes four arguments: a catalog name (null signifies that the catalog name is to be ignored), a schema name pattern, a table name pattern, and a column name pattern. The schema name, table name, and column name patterns can be used to specify a name or a pattern. Patterns can use the SQL string matching special characters “%” and “ ”; for instance, the pattern “%” matches all names. Only columns of tables of schemas satisfying the specified name or pattern are retrieved. Each row in the result set contains information about one column. The rows have a number of columns such as the name of the catalog, schema, table and column, the type of the column, and so on.

Examples of other methods provided by DatabaseMetaData that provide information about the database include those for retrieval of metadata about relations (getTables()), foreign-key references (getCrossReference()), au- thorizations, database limits such as maximum number of connections, and so on.

The metadata interfaces can be used for a variety of tasks. For example, they can be used to write a database browser that allows a user to find the tables in a database, examine their schema, examine rows in a table, apply selections to see desired rows, and so on. The metadata information can be used to make code  

**166 Chapter 5 Advanced SQL**

used for these tasks generic; for example, code to display the rows in a relation can be written in such a way that it would work on all possible relations regardless of their schema. Similarly, it is possible to write code that takes a query string, executes the query, and prints out the results as a formatted table; the code can work regardless of the actual query submitted.

**5.1.1.7 Other Features**

JDBC provides a number of other features, such as **updatable result sets**. It can create an updatable result set from a query that performs a selection and/or a projection on a database relation. An update to a tuple in the result set then results in an update to the corresponding tuple of the database relation.

Recall from Section 4.3 that a transaction allows multiple actions to be treated as a single atomic unit which can be committed or rolled back.

By default, each SQL statement is treated as a separate transaction that is com- mitted automatically. The method setAutoCommit() in the JDBC Connection interface allows this behavior to be turned on or off. Thus, if conn is an open con- nection, conn.setAutoCommit(false) turns off automatic commit. Transactions must then be committed or rolled back explicitly using either conn.commit() or conn.rollback(). conn.setAutoCommit(true) turns on automatic commit.

JDBC provides interfaces to deal with large objects without requiring an en- tire large object to be created in memory. To fetch large objects, the ResultSet interface provides methods getBlob() and getClob() that are similar to the getString() method, but return objects of type Blob and Clob, respectively. These objects do not store the entire large object, but instead store “locators” for the large objects, that is, logical pointers to the actual large object in the database. Fetching data from these objects is very much like fetching data from a file or an input stream, and can be performed using methods such as getBytes and getSubString.

Conversely, to store large objects in the database, the PreparedStatement class permits a database column whose type is **blob** to be linked to an input stream (such as a file that has been opened) using the method setBlob(int parameterIndex, InputStream inputStream). When the prepared statement is executed, data are read from the input stream, and written to the **blob** in the database. Similarly, a **clob** column can be set using the setClob method, which takes as arguments a parameter index and a character stream.

JDBC includes a _row set_ feature that allows result sets to be collected and shipped to other applications. Row sets can be scanned both backward and for- ward and can be modified. Because row sets are not part of the database itself once they are downloaded, we do not cover details of their use here.

**5.1.2 ODBC**

The **Open Database Connectivity** (ODBC) standard defines an API that applica- tions can use to open a connection with a database, send queries and updates, and get back results. Applications such as graphical user interfaces, statistics pack-  

**5.1 Accessing SQL From a Programming Language 167**

void ODBCexample() _{_

RETCODE error; HENV env; /\* environment \*/ HDBC conn; /\* database connection \*/

SQLAllocEnv(&env); SQLAllocConnect(env, &conn); SQLConnect(conn, "db.yale.edu", SQL NTS, "avi", SQL NTS,

"avipasswd", SQL NTS); _{_

char deptname\[80\]; float salary; int lenOut1, lenOut2; HSTMT stmt;

char \* sqlquery = "select dept name, sum (salary) from instructor group by dept name";

SQLAllocStmt(conn, &stmt); error = SQLExecDirect(stmt, sqlquery, SQL NTS); if (error == SQL SUCCESS) _{_

SQLBindCol(stmt, 1, SQL C CHAR, deptname , 80, &lenOut1); SQLBindCol(stmt, 2, SQL C FLOAT, &salary, 0 , &lenOut2); while (SQLFetch(stmt) == SQL SUCCESS) _{_

printf (" %s %g\\n", depthname, salary); _}_

_}_ SQLFreeStmt(stmt, SQL DROP);

_}_ SQLDisconnect(conn); SQLFreeConnect(conn); SQLFreeEnv(env);

_}_

**Figure 5.4** ODBC code example.

ages, and spreadsheets can make use of the same ODBC API to connect to any database server that supports ODBC.

Each database system supporting ODBC provides a library that must be linked with the client program. When the client program makes an ODBC API call, the code in the library communicates with the server to carry out the requested action, and fetch results.

Figure 5.4 shows an example of C code using the ODBC API. The first step in using ODBC to communicate with a server is to set up a connection with the server. To do so, the program first allocates an SQL environment, then a  

**168 Chapter 5 Advanced SQL**

database connection handle. ODBC defines the types HENV, HDBC, and RETCODE. The program then opens the database connection by using SQLConnect. This call takes several parameters, including the connection handle, the server to which to connect, the user identifier, and the password for the database. The constant SQL NTS denotes that the previous argument is a null-terminated string.

Once the connection is set up, the program can send SQL commands to the database by using SQLExecDirect. C language variables can be bound to attributes of the query result, so that when a result tuple is fetched using SQLFetch, its attribute values are stored in corresponding C variables. TheSQLBindCol function does this task; the second argument identifies the position of the attribute in the query result, and the third argument indicates the type conversion required from SQL to C. The next argument gives the address of the variable. For variable-length types like character arrays, the last two arguments give the maximum length of the variable and a location where the actual length is to be stored when a tuple is fetched. A negative value returned for the length field indicates that the value is **null**. For fixed-length types such as integer or float, the maximum length field is ignored, while a negative value returned for the length field indicates a null value.

The SQLFetch statement is in a **while** loop that is executed until SQLFetch returns a value other than SQL SUCCESS. On each fetch, the program stores the values in C variables as specified by the calls on SQLBindCol and prints out these values.

At the end of the session, the program frees the statement handle, disconnects from the database, and frees up the connection and SQL environment handles. Good programming style requires that the result of every function call must be checked to make sure there are no errors; we have omitted most of these checks for brevity.

It is possible to create an SQL statement with parameters; for example, consider the statement insert into department values(?,?,?). The question marks are placeholders for values which will be supplied later. The above statement can be “prepared,” that is, compiled at the database, and repeatedly executed by providing actual values for the placeholders—in this case, by providing an department name, building, and budget for the relation _department_.

ODBC defines functions for a variety of tasks, such as finding all the relations in the database and finding the names and types of columns of a query result or a relation in the database.

By default, each SQL statement is treated as a separate transaction that is committed automatically. The SQLSetConnectOption(conn, SQL AUTOCOMMIT, 0) turns off automatic commit on connection conn, and transactions must then be committed explicitly by SQLTransact(conn, SQL COMMIT) or rolled back by SQLTransact(conn, SQL ROLLBACK).

The ODBC standard defines _conformance levels_, which specify subsets of the functionality defined by the standard. An ODBC implementation may provide only core level features, or it may provide more advanced (level 1 or level 2) features. Level 1 requires support for fetching information about the catalog, such as information about what relations are present and the types of their attributes.  

**5.1 Accessing SQL From a Programming Language 169**

**ADO.NET**

The ADO.NET API, designed for the Visual Basic .NET and C# languages, pro- vides functions to access data, which at a high level are not dissimilar to the JDBC functions, although details differ. Like JDBC and ODBC, the ADO.NET API allows access to results of SQL queries, as well as to metadata, but is considerably sim- pler to use than ODBC. A database that supports ODBC can be accessed using the ADO.NET API, and the ADO.NET calls are translated into ODBC calls. The ADO.NET API can also be used with some kinds of nonrelational data sources such as Microsoft’s OLE-DB, XML (covered in Chapter 23), and more recently, the Entity Framework developed by Microsoft. See the bibliographic notes for more information on ADO.NET.

Level 2 requires further features, such as the ability to send and retrieve arrays of parameter values and to retrieve more detailed catalog information.

The SQL standard defines a **call level interface (CLI)** that is similar to the ODBC interface.

**5.1.3 Embedded SQL**

The SQL standard defines embeddings of SQL in a variety of programming lan- guages, such as C, C++, Cobol, Pascal, Java, PL/I, and Fortran. A language in which SQL queries are embedded is referred to as a _host_ language, and the SQL structures permitted in the host language constitute _embedded_ SQL.

Programs written in the host language can use the embedded SQL syntax to access and update data stored in a database. An embedded SQL program must be processed by a special preprocessor prior to compilation. The preprocessor replaces embedded SQL requests with host-language declarations and procedure calls that allow runtime execution of the database accesses. Then, the resulting program is compiled by the host-language compiler. This is the main distinction between embedded SQL and JDBC or ODBC.

In JDBC, SQL statements are interpreted at runtime (even if they are prepared first using the prepared statement feature). When embedded SQL is used, some SQL-related errors (including data-type errors) may be caught at compile time.

To identify embedded SQL requests to the preprocessor, we use the EXEC SQL statement; it has the form:

EXEC SQL _<_embedded SQL statement _\>_;

The exact syntax for embedded SQL requests depends on the language in which SQL is embedded. In some languages, such as Cobol, the semicolon is replaced with END-EXEC.  

**170 Chapter 5 Advanced SQL**

We place the statement SQL INCLUDE SQLCA in the program to identify the place where the preprocessor should insert the special variables used for com- munication between the program and the database system.

Before executing any SQL statements, the program must first connect to the database. This is done using:

EXEC SQL **connect to** _server_ **user** _user-name_ **using** _password_;

Here, _server_ identifies the server to which a connection is to be established. Variables of the host language can be used within embedded SQL statements,

but they must be preceded by a colon (:) to distinguish them from SQL variables. Variables used as above must be declared within a DECLARE section, as illustrated below. The syntax for declaring the variables, however, follows the usual host language syntax.

EXEC SQL BEGIN DECLARE SECTION; int _credit amount_;

EXEC SQL END DECLARE SECTION;

Embedded SQL statements are similar in form to regular SQL statements. There are, however, several important differences, as we note here.

To write a relational query, we use the **declare cursor** statement. The result of the query is not yet computed. Rather, the program must use the **open** and **fetch** commands (discussed later in this section) to obtain the result tuples. As we shall see, use of a cursor is analogous to iterating through a result set in JDBC.

Consider the university schema. Assume that we have a host-language vari- able _credit amount_ in our program, declared as we saw earlier, and that we wish to find the names of all students who have taken more than _credit amount_ credit hours. We can write this query as follows:

EXEC SQL **declare** _c_ **cursor for select** _ID_, _name_ **from** _student_ **where** _tot cred >_ :_credit amount_;

The variable _c_ in the preceding expression is called a _cursor_ for the query. We use this variable to identify the query. We then use the **open** statement, which causes the query to be evaluated.

The **open** statement for our sample query is as follows:

EXEC SQL **open** _c_;

This statement causes the database system to execute the query and to save the results within a temporary relation. The query uses the value of the host-language variable (_credit amount_) at the time the **open** statement is executed.  

**5.1 Accessing SQL From a Programming Language 171**

If the SQL query results in an error, the database system stores an error diag- nostic in the SQL communication-area (SQLCA) variables.

We then use a series of **fetch** statements, each of which causes the values of one tuple to be placed in host-language variables. The **fetch** statement requires one host-language variable for each attribute of the result relation. For our example query, we need one variable to hold the _ID_ value and another to hold the _name_ value. Suppose that those variables are _si_ and _sn_, respectively, and have been declared within a DECLARE section. Then the statement:

EXEC SQL **fetch** _c_ **into** :_si_, :_sn_;

produces a tuple of the result relation. The program can then manipulate the variables _si_ and _sn_ by using the features of the host programming language.

A single **fetch** request returns only one tuple. To obtain all tuples of the result, the program must contain a loop to iterate over all tuples. Embedded SQL assists the programmer in managing this iteration. Although a relation is conceptually a set, the tuples of the result of a query are in some fixed physical order. When the program executes an **open** statement on a cursor, the cursor is set to point to the first tuple of the result. Each time it executes a **fetch** statement, the cursor is updated to point to the next tuple of the result. When no further tuples remain to be processed, the character array variable SQLSTATE in the SQLCA is set to ’02000’ (meaning “no more data”); the exact syntax for accessing this variable depends on the specific database system you use. Thus, we can use a **while** loop (or equivalent loop) to process each tuple of the result.

We must use the **close** statement to tell the database system to delete the tem- porary relation that held the result of the query. For our example, this statement takes the form

EXEC SQL **close** _c_;

Embedded SQL expressions for database modification (**update**, **insert**, and **delete**) do not return a result. Thus, they are somewhat simpler to express. A database-modification request takes the form

EXEC SQL _<_ any valid **update, insert,** or **delete**_\>_;

Host-language variables, preceded by a colon, may appear in the SQL database- modification expression. If an error condition arises in the execution of the state- ment, a diagnostic is set in the SQLCA.

Database relations can also be updated through cursors. For example, if we want to add 100 to the _salary_ attribute of every instructor in the Music department, we could declare a cursor as follows.  

**172 Chapter 5 Advanced SQL**

**SQLJ**

The Java embedding of SQL, called SQLJ, provides the same features as other embedded SQL implementations, but using a different syntax that more closely matches features already present in Java, such as iterators. For example, SQLJ uses the syntax #sql instead of EXEC SQL, and instead of cursors, uses the Java iterator interface to fetch query results. Thus the result of executing a query is a Java iterator, and the next() method of the Java iterator interface can be used to step through the result tuples, just as the preceding examples use **fetch** on the cursor. The iterator must have attributes declared, whose types match the types of the attributes in the SQL query result. The code snippet below illustrates the use of iterators.

#sql iterator deptInfoIter ( String dept name, int avgSal); deptInfoIter iter = null;

#sql iter = _{_ select dept name, avg(salary) from instructor group by dept name _}_;

while (iter.next()) _{_ String deptName = iter.dept name(); int avgSal = iter.avgSal(); System.out.println(deptName + " " + avgSal);

_}_ iter.close();

SQLJ is supported by IBM DB2 and Oracle; both provide translators that convert SQLJ code into JDBC code. The translator can connect to the database in order to check the syntactic correctness of queries at compile time, and to ensure that the SQL types of query results are compatible with the Java types of variables they are assigned to. As of early 2009, SQLJ is not supported by other database systems.

We do not describe SQLJ in detail here; see the bibliographic notes for more information.

EXEC SQL **declare** _c_ **cursor for select** \* **from** _instructor_ **where** _dept name_\= ‘Music’ **for update**;

We then iterate through the tuples by performing **fetch** operations on the cursor (as illustrated earlier), and after fetching each tuple we execute the following code:  

**5.2 Functions and Procedures 173**

EXEC SQL **update** _instructor_ **set** _salary_ \= _salary_ \+ 100 **where current of** _c_;

Transactions can be committed using EXEC SQL COMMIT, or rolled back using EXEC SQL ROLLBACK.

Queries in embedded SQL are normally defined when the program is written. There are rare situations where a query needs to be defined at runtime. For example, an application interface may allow a user to specify selection conditions on one or more attributes of a relation, and may construct the **where** clause of an SQL query at runtime, with conditions on only those attributes for which the user specifies selections. In such cases, a query string can be constructed and prepared at runtime, using a statement of the form EXEC SQL PREPARE _<_query-name_\>_

FROM :_<_variable_\>_, and a cursor can be opened on the query name.

**5.2 Functions and Procedures**

We have already seen several functions that are built into the SQL language. In this section, we show how developers can write their own functions and procedures, store them in the database, and then invoke them from SQL statements. Functions are particularly useful with specialized data types such as images and geometric objects. For instance, a line-segment data type used in a map database may have an associated function that checks whether two line segments overlap, and an image data type may have associated functions to compare two images for similarity.

Procedures and functions allow “business logic” to be stored in the database, and executed from SQL statements. For example, universities usually have many rules about how many courses a student can take in a given semester, the mini- mum number of courses a full-time instructor must teach in a year, the maximum number of majors a student can be enrolled in, and so on. While such business logic can be encoded as programming-language procedures stored entirely out- side the database, defining them as stored procedures in the database has several advantages. For example, it allows multiple applications to access the procedures, and it allows a single point of change in case the business rules change, without changing other parts of the application. Application code can then call the stored procedures, instead of directly updating database relations.

SQL allows the definition of functions, procedures, and methods. These can be defined either by the procedural component of SQL, or by an external program- ming language such as Java, C, or C++. We look at definitions in SQL first, and then see how to use definitions in external languages in Section 5.2.3.

Although the syntax we present here is defined by the SQL standard, most databases implement nonstandard versions of this syntax. For example, the pro- cedural languages supported by Oracle (PL/SQL), Microsoft SQL Server (Trans- actSQL), and PostgreSQL (PL/pgSQL) all differ from the standard syntax we present  

**174 Chapter 5 Advanced SQL**

**create function** _dept count_(_dept name_ **varchar(20)**) **returns integer begin declare** _d count_ **integer**;

**select count**(_\*_) **into** _d count_ **from** _instructor_ **where** _instructor_._dept name_\= _dept name_

**return** _d count_; **end**

**Figure 5.5** Function defined in SQL.

here. We illustrate some of the differences, for the case of Oracle, later (page 178). See the respective system manuals for further details. Although parts of the syn- tax we present here may not be supported on such systems, the concepts we describe are applicable across implementations, although with a different syntax.

**5.2.1 Declaring and Invoking SQL Functions and Procedures**

Suppose that we want a function that, given the name of a department, returns the count of the number of instructors in that department. We can define the function as shown in Figure 5.5.4 This function can be used in a query that returns names and budgets of all departments with more than 12 instructors:

**select** _dept name_, _budget_ **from** _instructor_ **where** _dept count_(_dept name_) _\>_ 12;

The SQL standard supports functions that can return tables as results; such functions are called **table functions**.5 Consider the function defined in Figure 5.6. The function returns a table containing all the instructors of a particular depart- ment. Note that the function’s parameter is referenced by prefixing it with the name of the function (_instructor of.dept name_).

The function can be used in a query as follows:

**select** \* **from table**(_instructor of_ (’Finance’));

This query returns all instructors of the ’Finance’ department. In the above simple case it is straightforward to write this query without using table-valued functions. In general, however, table-valued functions can be thought of as **parameterized views** that generalize the regular notion of views by allowing parameters.

4If you are entering your own functions or procedures, you should write “**create or replace**” rather than **create** so that it is easy to modify your code (by replacing the function) during debugging. 5This feature first appeared in SQL:2003.  

**5.2 Functions and Procedures 175**

**create function** _instructors of_ (_dept name_ **varchar**(20)) **returns table** (

_ID_ **varchar** (5), _name_ **varchar** (20), _dept name_ **varchar** (20), _salary_ **numeric** (8,2))

**return table** (**select** _ID_, _name_, _dept name_, _salary_ **from** _instructor_ **where** _instructor_._dept name_ \= _instructor of.dept name_);

**Figure 5.6** Table function in SQL.

SQL also supports procedures. The _dept count_ function could instead be written as a procedure:

**create procedure** _dept count proc_(**in** _dept name_ **varchar(20)**, **out** _d count_ **integer**)

**begin select count**(_\*_) **into** _d count_ **from** _instructor_ **where** _instructor_._dept name_\= _dept count proc_._dept name_

**end**

The keywords **in** and **out** indicate, respectively, parameters that are expected to have values assigned to them and parameters whose values are set in the procedure in order to return results.

Procedures can be invoked either from an SQL procedure or from embedded SQL by the **call** statement:

**declare** _d count_ **integer**; **call** _dept count proc_(’Physics’, _d count_);

Procedures and functions can be invoked from dynamic SQL, as illustrated by the JDBC syntax in Section 5.1.1.4.

SQL permits more than one procedure of the same name, so long as the num- ber of arguments of the procedures with the same name is different. The name, along with the number of arguments, is used to identify the procedure. SQL also permits more than one function with the same name, so long as the different functions with the same name either have different numbers of arguments, or for functions with the same number of arguments, they differ in the type of at least one argument.  

**176 Chapter 5 Advanced SQL**

**5.2.2 Language Constructs for Procedures and Functions**

SQL supports constructs that give it almost all the power of a general-purpose pro- gramming language. The part of the SQL standard that deals with these constructs is called the **Persistent Storage Module (PSM)**.

Variables are declared using a **declare** statement and can have any valid SQL data type. Assignments are performed using a **set** statement.

A compound statement is of the form **begin** _. . ._ **end**, and it may contain multiple SQL statements between the **begin** and the **end**. Local variables can be declared within a compound statement, as we have seen in Section 5.2.1. A compound statement of the form **begin atomic** _. . ._ **end** ensures that all the statements contained within it are executed as a single transaction.

SQL:1999 supports the **while** statements and the **repeat** statements by the following syntax:

**while** _boolean expression_ **do** _sequence of statements_;

**end while**

**repeat** _sequence of statements_;

**until** _boolean expression_ **end repeat**

There is also a **for** loop that permits iteration over all results of a query:

**declare** _n_ **integer default** 0; **for** _r_ **as**

**select** _budget_ **from** _department_ **where** _dept name_ \= ‘Music‘

**do set** _n_ \= _n_− _r.budget_

**end for**

The program fetches the query results one row at a time into the **for** loop variable (_r_, in the above example). The statement **leave** can be used to exit the loop, while **iterate** starts on the next tuple, from the beginning of the loop, skipping the remaining statements.

The conditional statements supported by SQL include if-then-else statements by using this syntax:

**if** _boolean expression_ **then** _statement or compound statement_

**elseif** _boolean expression_ **then** _statement or compound statement_

**else** _statement or compound statement_ **end if**  

**5.2 Functions and Procedures 177**

– – Registers a student after ensuring classroom capacity is not exceeded – – Returns 0 on success, and -1 if capacity is exceeded. **create function** _registerStudent_(

**in** _s id_ **varchar**(5), **in** _s courseid_ **varchar** (8), **in** _s secid_ **varchar** (8), **in** _s semester_ **varchar** (6), **in** _s year_ **numeric** (4,0), **out** _errorMsg_ **varchar**(100)

**returns integer begin**

**declare** _currEnrol_ **int**; **select count**(\*) **into** _currEnrol_

**from** _takes_ **where** _course id_ \= _s courseid_ **and** _sec id_ \= _s secid_

**and** _semester_ \= _s semester_ **and** _year_ \= _s year_; **declare** _limit_ **int**; **select** _capacity_ **into** _limit_

**from** _classroom_ **natural join** _section_ **where** _course id_ \= _s courseid_ **and** _sec id_ \= _s secid_

**and** _semester_ \= _s semester_ **and** _year_ \= _s year_; **if** (_currEnrol < limit_)

**begin insert into** _takes_ **values**

(_s id_, _s courseid_, _s secid_, _s semester_, _s year_, null); **return**(0);

**end** – – Otherwise, section capacity limit already reached **set** _errorMsg_ \= ’Enrollment limit reached for course ’ || _s courseid_

|| ’ section ’ || _s secid_; **return**(-1);

**end**;

**Figure 5.7** Procedure to register a student for a course section.

SQL also supports a case statement similar to the C/C++ language case state- ment (in addition to case expressions, which we saw in Chapter 3).

Figure 5.7 provides a larger example of the use of procedural constructs in SQL. The function _registerStudent_ defined in the figure, registers a student in a course section, after verifying that the number of students in the section does not exceed the capacity of the room allocated to the section. The function returns an error code, with a value greater than or equal to 0 signifying success, and a negative value signifying an error condition, and a message indicating the reason for the failure is returned as an **out** parameter.  

**178 Chapter 5 Advanced SQL**

**NONSTANDARD SYNTAX FOR PROCEDURES AND FUNCTIONS**

Although the SQL standard defines the syntax for procedures and functions, most databases do not follow the standard strictly, and there is considerable variation in the syntax supported. One of the reasons for this situation is that these databases typically introduced support for procedures and functions be- fore the syntax was standardized, and they continue to support their original syntax. It is not possible to list the syntax supported by each database here, but we illustrate a few of the differences in the case of Oracle’s PL/SQL, by show- ing below a version of the function from Figure 5.5, as it would be defined in PL/SQL.

**create or replace function** _dept count_(_dept name_ **in** _instructor.dept name%type_) **return integer as**

_d count_ **integer**; **begin**

**select count**(\*) **into** _d count_ **from** _instructor_ **where** _instructor.dept name_ \= _dept name_; **return** _d count_;

**end**;

While the two versions are similar in concept, there are a number of minor syn- tactic differences, some of which are evident when comparing the two versions of the function. Although not shown here, the syntax for control flow in PL/SQL also has several differences from the syntax presented here.

Observe that PL/SQL allows a type to be specified as the type of an attribute of a relation, by adding the suffix _%type_. On the other hand, PL/SQL does not directly support the ability to return a table, although there is an indirect way of implementing this functionality by creating a table type. The procedural languages supported by other databases also have a number of syntactic and semantic differences. See the respective language references for more informa- tion.

The SQL procedural language also supports the signaling of **exception condi- tions**, and declaring of **handlers** that can handle the exception, as in this code:

**declare** _out of classroom seats_ **condition declare exit handler for** _out of classroom seats_ **begin** _sequence of statements_ **end**  

**5.2 Functions and Procedures 179**

The statements between the **begin** and the **end** can raise an exception by executing **signal** _out of classroom seats_. The handler says that if the condition arises, the action to be taken is to exit the enclosing **begin end** statement. Alternative actions would be **continue**, which continues execution from the next statement following the one that raised the exception. In addition to explicitly defined conditions, there are also predefined conditions such as **sqlexception**, **sqlwarning**, and **not found**.

**5.2.3 External Language Routines**

Although the procedural extensions to SQL can be very useful, they are unfortu- nately not supported in a standard way across databases. Even the most basic features have different syntax or semantics in different database products. As a result, programmers have to essentially learn a new language for each database product. An alternative that is gaining in support is to define procedures in an im- perative programming language, but allow them to be invoked from SQL queries and trigger definitions.

SQL allows us to define functions in a programming language such as Java, C#, C or C++. Functions defined in this fashion can be more efficient than functions defined in SQL, and computations that cannot be carried out in SQL can be executed by these functions.

External procedures and functions can be specified in this way (note that the exact syntax depends on the specific database system you use):

**create procedure** dept count proc( **in** _dept name_ **varchar**(20), **out** count **integer**)

**language** C **external name** ’/usr/avi/bin/dept count proc’

**create function** dept count (_dept name_ **varchar**(20)) **returns** integer **language** C **external name** ’/usr/avi/bin/dept count’

In general, the external language procedures need to deal with null values in parameters (both **in** and **out**) and return values. They also need to communicate failure/success status, to deal with exceptions. This information can be commu- nicated by extra parameters: an **sqlstate** value to indicate failure/success status, a parameter to store the return value of the function, and indicator variables for each parameter/function result to indicate if the value is null. Other mechanisms are possible to handle null values, for example by passing pointers instead of values. The exact mechanisms depend on the database. However, if a function does not deal with these situations, an extra line **parameter style general** can be added to the declaration to indicate that the external procedures/functions take only the arguments shown and do not handle null values or exceptions.

Functions defined in a programming language and compiled outside the database system may be loaded and executed with the database-system code.  

**180 Chapter 5 Advanced SQL**

However, doing so carries the risk that a bug in the program can corrupt the database internal structures, and can bypass the access-control functionality of the database system. Database systems that are concerned more about efficient per- formance than about security may execute procedures in such a fashion. Database systems that are concerned about security may execute such code as part of a sep- arate process, communicate the parameter values to it, and fetch results back, via interprocess communication. However, the time overhead of interprocess communication is quite high; on typical CPU architectures, tens to hundreds of thousands of instructions can execute in the time taken for one interprocess com- munication.

If the code is written in a “safe” language such as Java or C#, there is another possibility: executing the code in a **sandbox** within the database query execution process itself. The sandbox allows the Java or C# code to access its own memory area, but prevents the code from reading or updating the memory of the query execution process, or accessing files in the file system. (Creating a sandbox is not possible for a language such as C, which allows unrestricted access to memory through pointers.) Avoiding interprocess communication reduces function call overhead greatly.

Several database systems today support external language routines running in a sandbox within the query execution process. For example, Oracle and IBM DB2 allow Java functions to run as part of the database process. Microsoft SQL Server allows procedures compiled into the Common Language Runtime (CLR) to execute within the database process; such procedures could have been written, for example, in C# or Visual Basic. PostgreSQL allows functions defined in several languages, such as Perl, Python, and Tcl.

**5.3 Triggers**

A **trigger** is a statement that the system executes automatically as a side effect of a modification to the database. To design a trigger mechanism, we must meet two requirements:

**1\.** Specify when a trigger is to be executed. This is broken up into an _event_ that causes the trigger to be checked and a _condition_ that must be satisfied for trigger execution to proceed.

**2\.** Specify the _actions_ to be taken when the trigger executes.

Once we enter a trigger into the database, the database system takes on the respon- sibility of executing it whenever the specified event occurs and the corresponding condition is satisfied.

**5.3.1 Need for Triggers**

Triggers can be used to implement certain integrity constraints that cannot be specified using the constraint mechanism of SQL. Triggers are also useful mecha-  

**5.3 Triggers 181**

nisms for alerting humans or for starting certain tasks automatically when certain conditions are met. As an illustration, we could design a trigger that, whenever a tuple is inserted into the _takes_ relation, updates the tuple in the _student_ relation for the student taking the course by adding the number of credits for the course to the student’s total credits. As another example, suppose a warehouse wishes to maintain a minimum inventory of each item; when the inventory level of an item falls below the minimum level, an order can be placed automatically. On an update of the inventory level of an item, the trigger compares the current inventory level with the minimum inventory level for the item, and if the level is at or below the minimum, a new order is created.

Note that trigger systems cannot usually perform updates outside the database, and hence, in the inventory replenishment example, we cannot use a trigger to place an order in the external world. Instead, we add an order to a relation hold- ing reorders. We must create a separate permanently running system process that periodically scans that relation and places orders. Some database systems provide built-in support for sending email from SQL queries and triggers, using the above approach.

**5.3.2 Triggers in SQL**

We now consider how to implement triggers in SQL. The syntax we present here is defined by the SQL standard, but most databases implement nonstandard versions of this syntax. Although the syntax we present here may not be supported on such systems, the concepts we describe are applicable across implementations. We discuss nonstandard trigger implementations later in this section (page 184).

Figure 5.8 shows how triggers can be used to ensure referential integrity on the _time slot id_ attribute of the _section_ relation. The first trigger definition in the figure specifies that the trigger is initiated _after_ any insert on the relation _section_ and it ensures that the _time slot id_ value being inserted is valid. An SQL insert statement could insert multiple tuples of the relation, and the **for each row** clause in the trigger code would then explicitly iterate over each inserted row. The **referencing new row as** clause creates a variable _nrow_ (called a **transition variable**) that stores the value of an inserted row after the insertion.

The **when** statement specifies a condition. The system executes the rest of the trigger body only for tuples that satisfy the condition. The **begin atomic** _. . ._

**end** clause can serve to collect multiple SQL statements into a single compound statement. In our example, though, there is only one statement, which rolls back the transaction that caused the trigger to get executed. Thus any transaction that violates the referential integrity constraint gets rolled back, ensuring the data in the database satisfies the constraint.

It is not sufficient to check referential integrity on inserts alone, we also need to consider updates of _section_, as well as deletes and updates to the referenced table _time slot_. The second trigger definition in Figure 5.8 considers the case of deletes to _time slot_. This trigger checks that the _time slot id_ of the tuple being deleted is either still present in _time slot_, or that no tuple in _section_ contains that particular _time slot id_ value; otherwise, referential integrity would be violated.  

**182 Chapter 5 Advanced SQL**

**create trigger** _timeslot check1_ **after insert on** _section_ **referencing new row as** _nrow_ **for each row when** (_nrow.time slot id_ **not in** (

**select** _time slot id_ **from** _time slot_)) /\* _time slot id_ not present in _time slot_ \*/

**begin rollback**

**end**;

**create trigger** _timeslot check2_ **after delete on** _timeslot_ **referencing old row as** _orow_ **for each row when** (_orow.time slot id_ **not in** (

**select** _time slot id_ **from** _time slot_) /\* last tuple for _time slot id_ deleted from _time slot_ \*/

**and** _orow.time slot id_ **in** ( **select** _time slot id_ **from** _section_)) /\* and _time slot id_ still referenced from _section_\*/

**begin rollback**

**end**;

**Figure 5.8** Using triggers to maintain referential integrity.

To ensure referential integrity, we would also have to create triggers to handle updates to _section_ and _time slot_; we describe next how triggers can be executed on updates, but leave the definition of these triggers as an exercise to the reader.

For updates, the trigger can specify attributes whose update causes the trigger to execute; updates to other attributes would not cause it to be executed. For example, to specify that a trigger executes after an update to the _grade_ attribute of the _takes_ relation, we write:

**after update of** _takes_ **on** _grade_

The **referencing old row as** clause can be used to create a variable storing the old value of an updated or deleted row. The **referencing new row as** clause can be used with updates in addition to inserts.

Figure 5.9 shows how a trigger can be used to keep the _tot cred_ attribute value of _student_ tuples up-to-date when the _grade_ attribute is updated for a tuple in the _takes_ relation. The trigger is executed only when the _grade_ attribute is updated from a value that is either null or ’F’, to a grade that indicates the course is successfully completed. The **update** statement is normal SQL syntax except for the use of the variable _nrow_.  

**5.3 Triggers 183**

**create trigger** _credits earned_ **after update of** _takes_ **on** (_grade_) **referencing new row as** _nrow_ **referencing old row as** _orow_ **for each row when** _nrow.grade <>_ ’F’ **and** _nrow.grade_ **is not null**

**and** (_orow.grade_ \= ’F’ **or** _orow.grade_ **is null**) **begin atomic**

**update** _student_ **set** _tot cred_\= _tot cred_+

(**select** _credits_ **from** _course_ **where** _course_._course id_\= _nrow.course id_)

**where** _student.id_ \= _nrow.id_; **end**;

**Figure 5.9** Using a trigger to maintain _credits earned_ values.

A more realistic implementation of this example trigger would also handle grade corrections that change a successful completion grade to a fail grade, and handle insertions into the _takes_ relation where the _grade_ indicates successful com- pletion. We leave these as an exercise for the reader.

As another example of the use of a trigger, the action on **delete** of a _student_ tuple could be to check if the student has any entries in the _takes_ relation, and if so, to delete them.

Many database systems support a variety of other triggering events, such as when a user (application) logs on to the database (that is, opens a connection), the system shuts down, or changes are made to system settings.

Triggers can be activated **before** the event (insert, delete, or update) instead of **after** the event. Triggers that execute before an event can serve as extra constraints that can prevent invalid updates, inserts, or deletes. Instead of letting the invalid action proceed and cause an error, the trigger might take action to correct the problem so that the update, insert, or delete becomes valid. For example, if we attempt to insert an instructor into a department whose name does not appear in the _department_ relation, the trigger could insert a tuple into the _department_ relation for that department name before the insertion generates a foreign-key violation. As another example, suppose the value of an inserted grade is blank, presumably to indicate the absence of a grade. We can define a trigger that replaces the value by the **null** value. The **set** statement can be used to carry out such modifications. An example of such a trigger appears in Figure 5.10.

Instead of carrying out an action for each affected row, we can carry out a single action for the entire SQL statement that caused the insert, delete, or update. To do so, we use the **for each statement** clause instead of the **for each row** clause. The clauses **referencing old table as** or **referencing new table as** can then be used to refer to temporary tables (called _transition tables_) containing all the affected rows. Transition tables cannot be used with **before** triggers, but can be  

**184 Chapter 5 Advanced SQL**

**create trigger** _setnull_ **before update on** _takes_ **referencing new row as** _nrow_ **for each row when** (_nrow.grade_ \= ’ ’) **begin atomic**

**set** _nrow.grade_ \= **null**; **end**;

**Figure 5.10** Example of using **set** to change an inserted value.

used with **after** triggers, regardless of whether they are statement triggers or row triggers. A single SQL statement can then be used to carry out multiple actions on the basis of the transition tables.

**NONSTANDARD TRIGGER SYNTAX**

Although the trigger syntax we describe here is part of the SQL standard, and is supported by IBM DB2, most other database systems have nonstandard syntax for specifying triggers, and may not implement all features in the SQL standard. We outline a few of the differences below; see the respective system manuals for further details.

For example, in the Oracle syntax, unlike the SQL standard syntax, the key- word **row** does not appear in the **referencing** statement. The keyword **atomic** does not appear after **begin**. The reference to _nrow_ in the **select** statement nested in the **update** statement must begin with a colon (:) to inform the system that the variable _nrow_ is defined externally from the SQL statement. Further, sub- queries are not allowed in the **when** and **if** clauses. It is possible to work around this problem by moving complex predicates from the **when** clause into a sep- arate query that saves the result into a local variable, and then reference that variable in an **if** clause, and the body of the trigger then moves into the cor- responding **then** clause. Further, in Oracle, triggers are not allowed to execute a transaction rollback directly; however, they can instead use a function called raise application error to not only roll back the transaction, but also return an error message to the user/application that performed the update.

As another example, in Microsoft SQL Server the keyword **on** is used instead of **after**. The **referencing** clause is omitted, and old and new rows are referenced by the tuple variables **deleted** and **inserted**. Further, the **for each row** clause is omitted, and **when** is replaced by **if**. The **before** specification is not supported, but an **instead of** specification is supported.

In PostgreSQL, triggers do not have a body, but instead invoke a procedure for each row, which can access variables **new** and **old** containing the old and new values of the row. Instead of performing a rollback, the trigger can raise an exception, with an associated error message.  

**5.3 Triggers 185**

**create trigger** _reorder_ **after update of** _amount_ **on** _inventory_ **referencing old row as** _orow_, **new row as** _nrow_ **for each row when** _nrow.level <_\= (**select** _level_

**from** _minlevel_ **where** _minlevel.item_ \= _orow.item_)

**and** _orow.level >_ (**select** _level_ **from** _minlevel_ **where** _minlevel.item_ \= _orow.item_)

**begin atomic insert into** _orders_

(**select** _item, amount_ **from** _reorder_ **where** _reorder.item_ \= _orow.item_);

**end**;

**Figure 5.11** Example of trigger for reordering an item.

Triggers can be disabled or enabled; by default they are enabled when they are created, but can be disabled by using **alter trigger** _trigger name_ **disable** (some databases use alternative syntax such as **disable trigger** _trigger name_). A trigger that has been disabled can be enabled again. A trigger can instead be dropped, which removes it permanently, by using the command **drop trigger** _trigger name_.

Returning to our warehouse inventory example, suppose we have the follow- ing relations:

• _inventory_ (_item, level_), which notes the current amount of the item in the warehouse.

• _minlevel_ (_item, level_), which notes the minimum amount of the item to be maintained.

• _reorder_ (_item, amount_), which notes the amount of the item to be ordered when its level falls below the minimum.

• _orders_ (_item, amount_), which notes the amount of the item to be ordered.

Note that we have been careful to place an order only when the amount falls from above the minimum level to below the minimum level. If we check only that the new value after an update is below the minimum level, we may place an order erroneously when the item has already been reordered. We can then use the trigger shown in Figure 5.11 for reordering the item.

SQL-based database systems use triggers widely, although before SQL:1999 they were not part of the SQL standard. Unfortunately, each database system implemented its own syntax for triggers, leading to incompatibilities. The SQL:1999 syntax for triggers that we use here is similar, but not identical, to the syntax in the IBM DB2 and Oracle database systems.  

**186 Chapter 5 Advanced SQL**

**5.3.3 When Not to Use Triggers**

There are many good uses for triggers, such as those we have just seen in Sec- tion 5.3.2, but some uses are best handled by alternative techniques. For example, we could implement the **on delete cascade** feature of a foreign-key constraint by using a trigger, instead of using the cascade feature. Not only would this be more work to implement, but also, it would be much harder for a database user to understand the set of constraints implemented in the database.

As another example, triggers can be used to maintain materialized views. For instance, if we wished to support very fast access to the total number of students registered for each course section, we could do this by creating a relation

_section registration_(_course id_, _sec id_, _semester_, _year_, _total students_)

defined by the query

**select** _course id_, _sec id_, _semester_, _year_, **count**(_ID_) **as** _total students_ **from** _takes_ **group by** _course id_, _sec id_, _semester_, _year_;

The value of _total students_ for each course must be maintained up-to-date by triggers on insert, delete, or update of the _takes_ relation. Such maintenance may require insertion, update or deletion of tuples from _section registration_, and triggers must be written accordingly.

However, many database systems now support materialized views, which are automatically maintained by the database system (see Section 4.2.3). As a result, there is no need to write trigger code for maintaining such materialized views.

Triggers have been used for maintaining copies, or replicas, of databases. A collection of triggers on insert, delete, or update can be created on each relation to record the changes in relations called **change** or **delta** relations. A separate process copies over the changes to the replica of the database. Modern database systems, however, provide built-in facilities for database replication, making trig- gers unnecessary for replication in most cases. Replicated databases are discussed in detail in Chapter 19.

Another problem with triggers lies in unintended execution of the triggered action when data are loaded from a backup copy,6 or when database updates at a site are replicated on a backup site. In such cases, the triggered action has already been executed, and typically should not be executed again. When loading data, triggers can be disabled explicitly. For backup replica systems that may have to take over from the primary system, triggers would have to be disabled initially, and enabled when the backup site takes over processing from the primary system. As an alternative, some database systems allow triggers to be specified as **not**

6We discuss database backup and recovery from failures in detail in Chapter 16.  

**5.4 Recursive Queries 187**

_course id prereq id_

BIO-301 BIO-101 BIO-399 BIO-101 CS-190 CS-101 CS-315 CS-101 CS-319 CS-101 CS-347 CS-101 EE-181 PHY-101

**Figure 5.12** The _prereq_ relation.

**for replication**, which ensures that they are not executed on the backup site during database replication. Other database systems provide a system variable that denotes that the database is a replica on which database actions are being replayed; the trigger body should check this variable and exit if it is true. Both solutions remove the need for explicit disabling and enabling of triggers.

Triggers should be written with great care, since a trigger error detected at runtime causes the failure of the action statement that set off the trigger. Further- more, the action of one trigger can set off another trigger. In the worst case, this could even lead to an infinite chain of triggering. For example, suppose an insert trigger on a relation has an action that causes another (new) insert on the same relation. The insert action then triggers yet another insert action, and so on ad infinitum. Some database systems limit the length of such chains of triggers (for example, to 16 or 32) and consider longer chains of triggering an error. Other systems flag as an error any trigger that attempts to reference the relation whose modification caused the trigger to execute in the first place.

Triggers can serve a very useful purpose, but they are best avoided when alternatives exist. Many trigger applications can be substituted by appropriate use of stored procedures, which we discussed in Section 5.2.

**5.4 Recursive Queries \*\***

Consider the instance of the relation _prereq_ shown in Figure 5.12 containing infor- mation about the various courses offered at the university and the prerequisite for each course.7

Suppose now that we want to find out which courses are a prerequisite whether directly or indirectly, for a specific course—say, CS-347. That is, we wish to find a course that is a direct prerequisite for CS-347, or is a prerequisite for a course that is a prerequisite for CS-347, and so on.

7This instance of _prereq_ differs from that used earlier for reasons that will become apparent as we use it to explain recursive queries.  

**188 Chapter 5 Advanced SQL**

Thus, if CS-301 is a prerequisite for CS-347, and CS-201 is a prerequisite for CS-301, and CS-101 is a prerequisite for CS-201, then CS-301, CS-201, and CS-101 are all prerequisites for CS-347.

The **transitive closure** of the relation _prereq_ is a relation that contains all pairs (_cid_, _pre_) such that _pre_ is a direct or indirect prerequisite of _cid_. There are numerous applications that require computation of similar transitive closures on **hierarchies**. For instance, organizations typically consist of several levels of organizational units. Machines consist of parts that in turn have subparts, and so on; for example, a bicycle may have subparts such as wheels and pedals, which in turn have subparts such as tires, rims, and spokes. Transitive closure can be used on such hierarchies to find, for example, all parts in a bicycle.

**5.4.1 Transitive Closure Using Iteration**

One way to write the above query is to use iteration: First find those courses that are a direct prerequisite of CS-347, then those courses that are a prerequisite of all the courses under the first set, and so on. This iterative process continues until we reach an iteration where no courses are added. Figure 5.13 shows a function _findAllPrereqs(cid)_ to carry out this task; the function takes the _course id_ of the course as a parameter (_cid_), computes the set of all direct and indirect

prerequisites of that course, and returns the set. The procedure uses three temporary tables:

• _c prereq_: stores the set of tuples to be returned.

• _new c prereq_: stores the courses found in the previous iteration.

• _temp_: used as temporary storage while sets of courses are manipulated.

Note that SQL allows the creation of temporary tables using the command **create temporary table**; such tables are available only within the transaction executing the query, and are dropped when the transaction finishes. Moreover, if two in- stances of _findAllPrereqs_ run concurrently, each gets its own copy of the temporary tables; if they shared a copy, their result could be incorrect.

The procedure inserts all direct prerequisites of course _cid_ into _new c prereq_ before the **repeat** loop. The **repeat** loop first adds all courses in _new c prereq_ to _c prereq_. Next, it computes prerequisites of all those courses in _new c prereq_, except those that have already been found to be prerequisites of _cid_, and stores them in the temporary table _temp_. Finally, it replaces the contents of _new c prereq_ by the contents of _temp_. The **repeat** loop terminates when it finds no new (indirect) prerequisites.

Figure 5.14 shows the prerequisites that would be found in each iteration, if the procedure were called for the course named CS-347.

We note that the use of the **except** clause in the function ensures that the function works even in the (abnormal) case where there is a cycle of prerequisites. For example, if _a_ is a prerequisite for _b_, _b_ is a prerequisite for _c_, and _c_ is a prerequisite for _a_, there is a cycle.  

**5.4 Recursive Queries 189**

**create function** _findAllPrereqs(cid_ **varchar(8)**) – – Finds all courses that are prerequisite (directly or indirectly) for _cid_

**returns table** (_course id_ **varchar**(8)) – – The relation _prereq_(_course id_, _prereq id_) specifies which course is – – directly a prerequisite for another course.

**begin create temporary table** _c prereq_ (_course id_ **varchar(8)**);

– – table _c prereq_ stores the set of courses to be returned **create temporary table** _new c prereq_ (_course id_ **varchar(8)**);

– – table _new c prereq_ contains courses found in the previous iteration **create temporary table** _temp_ (_course id_ **varchar(8)**);

– – table _temp_ is used to store intermediate results **insert into** _new c prereq_

**select** _prereq id_ **from** _prereq_ **where** _course id_ \= _cid_;

**repeat insert into** _c prereq_

**select** _course id_ **from** _new c prereq_;

**insert into** _temp_ (**select** _prereq_._course id_

**from** _new c prereq, prereq_ **where** _new c prereq_._course id_ \= _prereq_._prereq id_

) **except** (

**select** _course id_ **from** _c prereq_

); **delete from** _new c prereq_; **insert into** _new c prereq_

**select \* from** _temp_;

**delete from** _temp_;

**until not exists** (**select \* from** _new c prereq_) **end repeat**; **return table** _c prereq_;

**end**

**Figure 5.13** Finding all prerequisites of a course.

While cycles may be unrealistic in course prerequisites, cycles are possible in other applications. For instance, suppose we have a relation _flights(to, from_) that says which cities can be reached from which other cities by a direct flight. We can  

**190 Chapter 5 Advanced SQL**

Iteration Number Tuples in c1 0 1 (CS-301) 2 (CS-301), (CS-201) 3 (CS-301), (CS-201) 4 (CS-301), (CS-201), (CS-101) 5 (CS-301), (CS-201), (CS-101)

**Figure 5.14** Prerequisites of CS-347 in iterations of function _findAllPrereqs._

write code similar to that in the _findAllPrereqs_ function, to find all cities that are reachable by a sequence of one or more flights from a given city. All we have to do is to replace _prereq_ by _flight_ and replace attribute names correspondingly. In this situation, there can be cycles of reachability, but the function would work correctly since it would eliminate cities that have already been seen.

**5.4.2 Recursion in SQL**

It is rather inconvenient to specify transitive closure using iteration. There is an alternative approach, using recursive view definitions, that is easier to use.

We can use recursion to define the set of courses that are prerequisites of a particular course, say CS-347, as follows. The courses that are prerequisites (directly or indirectly) of CS-347 are:

**1\.** Courses that are prerequisites for CS-347.

**2\.** Courses that are prerequisites for those courses that are prerequisites (di- rectly or indirectly) for CS-347.

Note that case 2 is recursive, since it defines the set of courses that are prerequisites of CS-347 in terms of the set of courses that are prerequisites of CS-347. Other examples of transitive closure, such as finding all subparts (direct or indirect) of a given part can also be defined in a similar manner, recursively.

Since the SQL:1999 version, the SQL standard supports a limited form of re- cursion, using the **with recursive** clause, where a view (or temporary view) is expressed in terms of itself. Recursive queries can be used, for example, to express transitive closure concisely. Recall that the **with** clause is used to define a tempo- rary view whose definition is available only to the query in which it is defined. The additional keyword **recursive** specifies that the view is recursive.

For example, we can find every pair (_cid_,_pre_) such that _pre_ is directly or in- directly a prerequisite for course _cid_, using the recursive SQL view shown in Figure 5.15.

Any recursive view must be defined as the union of two subqueries: a **base query** that is nonrecursive and a **recursive query** that uses the recursive view. In the example in Figure 5.15, the base query is the select on _prereq_ while the recursive query computes the join of _prereq_ and _rec prereq_.  

**5.4 Recursive Queries 191**

**with recursive** _c prereq_(_course id_, _prereq id_) **as** ( **select** _course id_, _prereq id_ **from** _prereq_

**union select** _prereq_._prereq id_, _c prereq_._course id_ **from** _prereq_, _c prereq_ **where** _prereq_._course id_ \= _c prereq_._prereq id_

) **select** ∗ **from** _c prereq_;

**Figure 5.15** Recursive query in SQL.

The meaning of a recursive view is best understood as follows. First compute the base query and add all the resultant tuples to the recursively defined view relation _rec prereq_ (which is initially empty). Next compute the recursive query using the current contents of the view relation, and add all the resulting tuples back to the view relation. Keep repeating the above step until no new tuples are added to the view relation. The resultant view relation instance is called a **fixed point** of the recursive view definition. (The term “fixed” refers to the fact that there is no further change.) The view relation is thus defined to contain exactly the tuples in the fixed-point instance.

Applying the above logic to our example, we first find all direct prerequisites of each course by executing the base query. The recursive query adds one more level of courses in each iteration, until the maximum depth of the course-prereq relationship is reached. At this point no new tuples are added to the view, and a fixed point is reached.

To find the prerequisites of a specific course, such as CS-347, we can modify the outer level query by adding a where clause “**where** _rec prereq.course id_ \= ‘CS-347‘”. One way to evaluate the query with the selection is to compute the full contents of _rec prereq_ using the iterative technique, and then select from this result only those tuples whose _course id_ is CS-347. However, this would result in computing (course, prerequisite) pairs for all courses, all of which are irrelevant except for those for the course CS-347. In fact the database system is not required to use the above iterative technique to compute the full result of the recursive query and then perform the selection. It may get the same result using other techniques that may be more efficient, such as that used in the function _findAllPrereqs_ which we saw earlier. See the bibliographic notes for references to more information on this topic.

There are some restrictions on the recursive query in a recursive view; specifi- cally, the query should be **monotonic**, that is, its result on a view relation instance _V_1 should be a superset of its result on a view relation instance _V_2 if _V_1 is a super- set of _V_2\. Intuitively, if more tuples are added to the view relation, the recursive query should return at least the same set of tuples as before, and possibly return additional tuples.  

**192 Chapter 5 Advanced SQL**

In particular, recursive queries should not use any of the following constructs, since they would make the query nonmonotonic:

• Aggregation on the recursive view.

• **not exists** on a subquery that uses the recursive view.

• Set difference (**except**) whose right-hand side uses the recursive view.

For instance, if the recursive query was of the form _r_ − _v_ where _v_ is the recursive view, if we add a tuple to _v_ the result of the query can become smaller; the query is therefore not monotonic.

The meaning of recursive views can be defined by the iterative procedure as long as the recursive query is monotonic; if the recursive query is nonmonotonic, the meaning of the view is hard to define. SQL therefore requires the queries to be monotonic. Recursive queries are discussed in more detail in the context of the Datalog query language, in Section B.3.6.

SQL also allows creation of recursively defined permanent views by using **create recursive view** in place of **with recursive**. Some implementations support recursive queries using a different syntax; see the respective system manuals for further details.

**5.5 Advanced Aggregation Features\*\***

The aggregation support in SQL, which we have seen earlier, is quite powerful, and handles most common tasks with ease. However, there are some tasks that are hard to implement efficiently with the basic aggregation features. In this section, we study features that were added to SQL to handle some such tasks.

**5.5.1 Ranking**

Finding the position of a value in a larger set is a common operation. For instance, we may wish to assign students a rank in class based on their grade-point average (GPA), with the rank 1 going to the student with the highest GPA, the rank 2 to the student with the next highest GPA, and so on. A related type of query is to find the percentile in which a value in a (multi)set belongs, for example, the bottom third, middle third, or top third. While such queries can be expressed using the SQL constructs we have seen so far, they are difficult to express and inefficient to evaluate. Programmers may resort to writing the query partly in SQL and partly in a programming language. We study SQL support for direct expression of these types of queries here.

In our university example, the _takes_ relation shows the grade each student earned in each course taken. To illustrate ranking, let us assume we have a view _student grades_ (_ID, GPA_) giving the grade-point average of each student.8

8The SQL statement to create the view _student grades_ is somewhat complex since we must convert the letter grades in the _takes_ relation to numbers and weight the grades for each course by the number of credits for that course. The definition of this view is the goal of Exercise 4.5.  

**5.5 Advanced Aggregation Features 193**

Ranking is done with an **order by** specification. The following query gives the rank of each student:

**select** _ID_, **rank**() **over** (**order by** (_GPA_) **desc**) **as** _s rank_ **from** _student grades_;

Note that the order of tuples in the output is not defined, so they may not be sorted by rank. An extra **order by** clause is needed to get them in sorted order, as shown below.

**select** _ID_, **rank** () **over** (**order by** (_GPA_) **desc**) **as** _s rank_ **from** _student grades_ **order by** _s rank_;

A basic issue with ranking is how to deal with the case of multiple tuples that are the same on the ordering attribute(s). In our example, this means deciding what to do if there are two students with the same GPA. The **rank** function gives the same rank to all tuples that are equal on the **order by** attributes. For instance, if the highest GPA is shared by two students, both would get rank 1. The next rank given would be 3, not 2, so if three students get the next highest GPA, they would all get rank 3, and the next student(s) would get rank 6, and so on. There is also a **dense rank** function that does not create gaps in the ordering. In the above example, the tuples with the second highest value all get rank 2, and tuples with the third highest value get rank 3, and so on.

It is possible to express the above query with the basic SQL aggregation functions, using the following query:

**select** _ID_, (1 + (**select count**(\*) **from** _student grades B_ **where** _B_._GPA_ \> _A_._GPA_)) **as** _s rank_

**from** _student grades A_ **order by** _s rank_;

It should be clear that the rank of a student is merely 1 plus the number of students with a higher _GPA_, which is exactly what the above query specifies. However, this computation of each student’s rank takes time linear in the size of the relation, leading to an overall time quadratic in the size of the relation. On large relations, the above query could take a very long time to execute. In contrast, the system’s implementation of the **rank** clause can sort the relation and compute the rank in much less time.

Ranking can be done within partitions of the data. For instance, suppose we wish to rank students by department rather than across the entire university. Assume that a view is defined like _student grades_ but including the department name: _dept grades_(_ID, dept name, GPA_). The following query then gives the rank of students within each section:  

**194 Chapter 5 Advanced SQL**

**select** _ID_, _dept name_, **rank** () **over** (**partition by** _dept name_ **order by** _GPA_ **desc**) **as** _dept rank_

**from** _dept grades_ **order by** _dept name_, _dept rank_;

The outer **order by** clause orders the result tuples by department name, and within each department by the rank.

Multiple **rank** expressions can be used within a single **select** statement; thus we can obtain the overall rank and the rank within the department by using two **rank** expressions in the same **select** clause. When ranking (possibly with partitioning) occurs along with a **group by** clause, the **group by** clause is applied first, and partitioning and ranking are done on the results of the group by. Thus aggregate values can then be used for ranking. We could have written our ranking over the _student grades_ view without using the view, using a single **select** clause. We leave details as an exercise for you.

The ranking functions can be used to find the top _n_ tuples by embedding a ranking query within an outer-level query; we leave details as an exercise. Note that the bottom _n_ is simply the same as the top _n_ with a reverse sorting order. Several database systems provide nonstandard SQL extensions to specify directly that only the top _n_ results are required; such extensions do not require the rank function and simplify the job of the optimizer. For example, some databases allow a clause **limit** _n_ to be added at the end of an SQL query to specify that only the first _n_ tuples should be output; this clause is used in conjunction with an **order by** clause to fetch the top _n_ tuples, as illustrated by the following query, which retrieves the IDs and GPAs of the top 10 students in order of GPA:

**select** _ID_, _GPA_) **from** _student grades_ **order by** _GPA_ **limit** 10;

However, the **limit** clause does not support partitioning, so we cannot get the top _n_ within each partition without performing ranking; further, if more than one student gets the same GPA, it is possible that one is included in the top 10, while another is excluded.

Several other functions can be used in place of **rank**. For instance, **per- cent rank** of a tuple gives the rank of the tuple as a fraction. If there are _n_ tuples in the partition9 and the rank of the tuple is _r_ , then its percent rank is defined as (_r_ − 1)_/_(_n_ − 1) (and as _null_ if there is only one tuple in the partition). The function **cume dist**, short for cumulative distribution, for a tuple is defined as _p/n_ where _p_ is the number of tuples in the partition with ordering values preceding or equal to the ordering value of the tuple and _n_ is the number of tuples in the partition. The function **row number** sorts the rows and gives each row a unique number corre-

9The entire set is treated as a single partition if no explicit partition is used.  

**5.5 Advanced Aggregation Features 195**

sponding to its position in the sort order; different rows with the same ordering value would get different row numbers, in a nondeterministic fashion.

Finally, for a given constant _n_, the ranking function **ntile**(_n_) takes the tuples in each partition in the specified order and divides them into _n_ buckets with equal numbers of tuples.10 For each tuple, **ntile**(_n_) then gives the number of the bucket in which it is placed, with bucket numbers starting with 1. This function is particularly useful for constructing histograms based on percentiles. We can show the quartile into which each student falls based on GPA by the following query:

**select** _ID_, **ntile**(4) **over** (**order by** (_GPA_ **desc**)) **as** _quartile_ **from** _student grades_;

The presence of null values can complicate the definition of rank, since it is not clear where they should occur first in the sort order. SQL permits the user to specify where they should occur by using **nulls first** or **nulls last**, for instance:

**select** _ID_, **rank** () **over** (**order by** _GPA_ **desc nulls last**) **as** _s rank_ **from** _student grades_;

**5.5.2 Windowing**

Window queries compute an aggregate function over ranges of tuples. This is useful, for example, to compute an aggregate of a fixed range of time; the time range is called a _window_. Windows may overlap, in which case a tuple may contribute to more than one window. This is unlike the partitions we saw earlier, where a tuple could contribute to only one partition.

An example of the use of windowing is trend analysis. Consider our earlier sales example. Sales may fluctuate widely from day to day based on factors like weather (for example a snowstorm, flood, hurricane, or earthquake might reduce sales for a period of time). However, over a sufficiently long period of time, fluctuations might be less (continuing the example, sales may “make up” for weather-related downturns). Stock market trend analysis is another example of the use of the windowing concept. Various “moving averages” are found on business and investment Web sites.

It is relatively easy to write an SQL query using those features we have already studied to compute an aggregate over one window, for example, sales over a fixed 3-day period. However, if we want to do this for _every_ 3-day period, the query becomes cumbersome.

SQL provides a windowing feature to support such queries. Suppose we are given a view _tot credits_ (_year, num credits_) giving the total number of credits taken

10If the total number of tuples in a partition is not divisible by _n_, then the number of tuples in each bucket can differ by at most 1. Tuples with the same value for the ordering attribute may be assigned to different buckets, nondeterministically, in order to make the number of tuples in each bucket equal.  

**196 Chapter 5 Advanced SQL**

by students in each year.11 Note that this relation can contain at most one tuple for each year. Consider the following query:

**select** _year_, **avg**(_num credits_) **over** (**order by** _year_ **rows** 3 **preceding**) **as** _avg total credits_

**from** _tot credits_;

This query computes averages over the 3 _preceding_ tuples in the specified sort order. Thus, for 2009, if tuples for years 2008 and 2007 are present in the relation _tot credits_, with each year represented by only one tuple, the result of the window definition is the average of the values for years 2007, 2008, and 2009. The averages each year would be computed in a similar manner. For the earliest year in the relation _tot credits_, the average would be over only that year itself, while for the next year, the average would be over two years. Note that if the relation _tot credits_ has more than one tuple for a specific year, there may be multiple possible

orderings of tuples, that are sorted by year. In this case, the definition of preceding tuples is based on the implementation dependent sort order, and is not uniquely defined.

Suppose that instead of going back a fixed number of tuples, we want the window to consist of all prior years. That means the number of prior years considered is not fixed. To get the average total credits over all prior years we write:

**select** _year_, **avg**(_num credits_) **over** (**order by** _year_ **rows unbounded preceding**) **as** _avg total credits_

**from** _tot credits_;

It is possible to use the keyword **following** in place of **preceding**. If we did this in our example the _year_ value specifies the beginning of the window instead of the end. Similarly, we can specify a window beginning before the current tuple and ending after it:

**select** _year_, **avg**(_num credits_) **over** (**order by** _year_ **rows between** 3 **preceding and** 2 **following**) **as** _avg total credits_

**from** _tot credits_;

Instead of a specific count of tuples, we can specify a range based on the value of the **order by** attribute. To specify a range going back 4 years and including the current year, we write:

11We leave the definition of this view in terms of our university example as an exercise.  

**5.6 OLAP 197**

**select** _year_, **avg**(_num credits_) **over** (**order by** _year_ **range between** _year_ \- 4 **and** _year_) **as** _avg total credits_

**from** _tot credits_;

Be sure to note the use of the keyword **range** in the above example. For the year 2010, data for years 2006 to 2010 inclusive would be included regardless of how many tuples actually exist for that range.

In our example, all tuples pertain to the entire university. Suppose instead, we have credit data for each department in a view _tot credits dept_ (_dept name, year, num credits_) giving the total number of credits students took with the particular department in the specified year. (Again, we leave writing this view definition as an exercise.) We can write windowing queries that treat each department separately by partitioning by _dept name_:

**select** _dept name, year_, **avg**(_num credits_) **over** (**partition by** _dept name_

**order by** _year_ **rows between** 3 **preceding and current row**) **as** _avg total credits_

**from** _tot credits dept_;

**5.6 OLAP\*\***

An online analytical processing (OLAP) system is an interactive system that per- mits an analyst to view different summaries of multidimensional data. The word _online_ indicates that an analyst must be able to request new summaries and get responses online, within a few seconds, and should not be forced to wait for a long time to see the result of a query.

There are many OLAP products available, including some that ship with database products such as Microsoft SQL Server, and Oracle, and other stand- alone tools. The initial versions of many OLAP tools assumed that data is memory resident. Data analysis on small amounts of data can in fact be performed using spreadsheet applications, such as Excel. However, OLAP on very large amounts of data requires that data be resident in a database, and requires support from the database for efficient preprocessing of data as well as for online query processing. In this section, we study extensions of SQL to support such tasks.

**5.6.1 Online Analytical Processing**

Consider an application where a shop wants to find out what kinds of clothes are popular. Let us suppose that clothes are characterized by their item name, color, and size, and that we have a relation _sales_ with the schema.

_sales_ (_item name_, _color_, _clothes size_, _quantity_)  

**198 Chapter 5 Advanced SQL**

_item name color clothes size quantity_ skirt dark small 2 skirt dark medium 5 skirt dark large 1 skirt pastel small 11 skirt pastel medium 9 skirt pastel large 15 skirt white small 2 skirt white medium 5 skirt white large 3 dress dark small 2 dress dark medium 6 dress dark large 12 dress pastel small 4 dress pastel medium 3 dress pastel large 3 dress white small 2 dress white medium 3 dress white large 0 shirt dark small 2 shirt dark medium 6 shirt dark large 6 shirt pastel small 4 shirt pastel medium 1 shirt pastel large 2 shirt white small 17 shirt white medium 1 shirt white large 10 pants dark small 14 pants dark medium 6 pants dark large 0 pants pastel small 1 pants pastel medium 0 pants pastel large 1 pants white small 3 pants white medium 0 pants white large 2

**Figure 5.16** An example of _sales_ relation.

Suppose that _item name_ can take on the values (skirt, dress, shirt, pants), _color_ can take on the values (dark, pastel, white), _clothes size_ can take on values (small, medium, large), and _quantity_ is an integer value representing the total number of items of a given _{item name_, _color_, _clothes size }_. An instance of the _sales_ relation is shown in Figure 5.16.  

**5.6 OLAP 199**

Statistical analysis often requires grouping on multiple attributes. Given a relation used for data analysis, we can identify some of its attributes as **measure** attributes, since they measure some value, and can be aggregated upon. For instance, the attribute _quantity_ of the _sales_ relation is a measure attribute, since it measures the number of units sold. Some (or all) of the other attributes of the relation are identified as **dimension attributes**, since they define the dimensions on which measure attributes, and summaries of measure attributes, are viewed. In the _sales_ relation, _item name_, _color_, and _clothes size_ are dimension attributes. (A more realistic version of the _sales_ relation would have additional dimensions, such as time and sales location, and additional measures such as monetary value of the sale.)

Data that can be modeled as dimension attributes and measure attributes are called **multidimensional data**.

To analyze the multidimensional data, a manager may want to see data laid out as shown in the table in Figure 5.17. The table shows total quantities for different combinations of _item name_ and _color_. The value of _clothes size_ is specified to be **all**, indicating that the displayed values are a summary across all values of _clothes size_ (that is, we want to group the “small”, “medium”, and “large” items into one single group.

The table in Figure 5.17 is an example of a **cross-tabulation** (or **cross-tab**, for short), also referred to as a **pivot-table**. In general, a cross-tab is a table derived from a relation (say _R_), where values for one attribute of relation _R_ (say _A_) form the row headers and values for another attribute of relation _R_ (say _B_) form the column header. For example, in Figure 5.17, the attribute _item name_ corresponds to _A_ (with values “dark”, “pastel”, and “white”), and the attribute _color_ corresponds to to _B_ (with attributes “skirt”, “dress”, “shirt”, and “pants”).

Each cell in the pivot-table can be identified by (_ai , b j_ ), where _ai_ is a value for _A_ and _b j_ a value for _B_. The values of the various cells in the pivot-table are derived from the relation _R_ as follows: If there is at most one tuple in _R_ with any (_ai , b j_ ) value, the value in the cell is derived from that single tuple (if any); for instance, it could be the value of one or more other attributes of the tuple. If there can be multiple tuples with an (_ai , b j_ ) value, the value in the cell must be derived

skirt dress shirt pants

_color_

_item\_name_

_clothes\_size_ **all**

dark pastel white total

total

8 35 10 53 20 10 5 35 14 7 28 49 20 2 5 27 62 54 48 164

**Figure 5.17** Cross tabulation of _sales_ by _item name_ and _color_.  

**200 Chapter 5 Advanced SQL**

8 20 14 20 62

35 10 7 2 54

10 8 28 5 48

53 35 49 27 164

34

21

77

4

9

42

16

18

45

all large

medium small

skirt dress shirt pants all _clo the_

_s\_ siz_

_e_

2 5 3 1 11 4 7 6 12 29

2 8 5 7 22

dark

pastel

white

all

_item\_name_

_co lo_

_r_

**Figure 5.18** Three-dimensional data cube.

by aggregation on the tuples with that value. In our example, the aggregation used is the sum of the values for attribute _quantity_, across all values for _clothes size_, as indicated by “_clothes size_: **all**” above the cross-tab in Figure 5.17. Thus, the

value for cell (skirt, pastel) is 35, since there are 3 tuples in the _sales_ table that meet that criteria, with values 11, 9, and 15.

In our example, the cross-tab also has an extra column and an extra row storing the totals of the cells in the row/column. Most cross-tabs have such summary rows and columns.

The generalization of a cross-tab, which is two-dimensional, to _n_ dimensions can be visualized as an _n_\-dimensional cube, called the **data cube**. Figure 5.18 shows a data cube on the _sales_ relation. The data cube has three dimensions, _item name_, _color_, and _clothes size_, and the measure attribute is _quantity_. Each cell is identified by values for these three dimensions. Each cell in the data cube contains a value, just as in a cross-tab. In Figure 5.18, the value contained in a cell is shown on one of the faces of the cell; other faces of the cell are shown blank if they are visible. All cells contain values, even if they are not visible. The value for a dimension may be **all**, in which case the cell contains a summary over all values of that dimension, as in the case of cross-tabs.

The number of different ways in which the tuples can be grouped for aggre- gation can be large. In the example of Figure 5.18, there are 3 colors, 4 items, and 3 sizes resulting in a cube size of 3 × 4 × 3 = 36. Including the summary values, we obtain a 4 × 5 × 4 cube, whose size is 80. In fact, for a table with _n_ dimensions, aggregation can be performed with grouping on each of the 2_n_ subsets of the _n_ dimensions.12

12Grouping on the set of all _n_ dimensions is useful only if the table may have duplicates.  

**5.6 OLAP 201**

With an OLAP system, a data analyst can look at different cross-tabs on the same data by interactively selecting the attributes in the cross-tab. Each cross-tab is a two-dimensional view on a multidimensional data cube. For instance, the analyst may select a cross-tab on _item name_ and _clothes size_ or a cross-tab on _color_ and _clothes size_. The operation of changing the dimensions used in a cross-tab is called **pivoting**.

OLAP systems allow an analyst to see a cross-tab on _item name_ and _color_ for a fixed value of _clothes size_, for example, large, instead of the sum across all sizes. Such an operation is referred to as **slicing**, since it can be thought of as viewing a slice of the data cube. The operation is sometimes called **dicing**, particularly when values for multiple dimensions are fixed.

When a cross-tab is used to view a multidimensional cube, the values of dimension attributes that are not part of the cross-tab are shown above the cross- tab. The value of such an attribute can be **all**, as shown in Figure 5.17, indicating that data in the cross-tab are a summary over all values for the attribute. Slic- ing/dicing simply consists of selecting specific values for these attributes, which are then displayed on top of the cross-tab.

OLAP systems permit users to view data at any desired level of granularity. The operation of moving from finer-granularity data to a coarser granularity (by means of aggregation) is called a **rollup**. In our example, starting from the data cube on the _sales_ table, we got our example cross-tab by rolling up on the attribute _clothes size_. The opposite operation—that of moving from coarser-granularity data to finer-granularity data—is called a **drill down**. Clearly, finer-granularity data cannot be generated from coarse-granularity data; they must be generated either from the original data, or from even finer-granularity summary data.

Analysts may wish to view a dimension at different levels of detail. For instance, an attribute of type **datetime** contains a date and a time of day. Using time precise to a second (or less) may not be meaningful: An analyst who is interested in rough time of day may look at only the hour value. An analyst who is interested in sales by day of the week may map the date to a day of the week and look only at that. Another analyst may be interested in aggregates over a month, or a quarter, or for an entire year.

The different levels of detail for an attribute can be organized into a **hierarchy**. Figure 5.19a shows a hierarchy on the **datetime** attribute. As another example, Figure 5.19b shows a hierarchy on location, with the city being at the bottom of the hierarchy, state above it, country at the next level, and region being the top level. In our earlier example, clothes can be grouped by category (for instance, menswear or womenswear); _category_ would then lie above _item name_ in our hierarchy on clothes. At the level of actual values, skirts and dresses would fall under the womenswear category and pants and shirts under the menswear category.

An analyst may be interested in viewing sales of clothes divided as menswear and womenswear, and not interested in individual values. After viewing the aggregates at the level of womenswear and menswear, an analyst may _drill down the hierarchy_ to look at individual values. An analyst looking at the detailed level may _drill up the hierarchy_ and look at coarser-level aggregates. Both levels can be displayed on the same cross-tab, as in Figure 5.20.  

**202 Chapter 5 Advanced SQL**

Hour of day Date

DateTime

Day of week Month

Quarter

Year

State

Country

Region

City

(a) Time Hierarchy (b) Location Hierarchy

**Figure 5.19** Hierarchies on dimensions.

**5.6.2 Cross-Tab and Relational Tables**

A cross-tab is different from relational tables usually stored in databases, since the number of columns in the cross-tab depends on the actual data. A change in the data values may result in adding more columns, which is not desirable for data storage. However, a cross-tab view is desirable for display to users. It is straightforward to represent a cross-tab without summary values in a relational form with a fixed number of columns. A cross-tab with summary rows/columns can be represented by introducing a special value **all** to represent subtotals, as in Figure 5.21. The SQL standard actually uses the **null** value in place of **all**, but to avoid confusion with regular null values, we shall continue to use **all**.

womenswear

_category item\_name color_

_clothes\_size:_ **all**

dark pastel white total

total

skirt 8 8 10 53 dress 20 20 5 35 subtotal 28 28 15 88

menswear pants 14 14 28 49 shirt 20 20 5 27 subtotal 34 34 33 76 62 62 48 164

**Figure 5.20** Cross tabulation of _sales_ with hierarchy on _item name_.  

**5.6 OLAP 203**

_item name color clothes size quantity_ skirt dark **all** 8 skirt pastel **all** 35 skirt white **all** 10 skirt **all all** 53 dress dark **all** 20 dress pastel **all** 10 dress white **all** 5 dress **all all** 35 shirt dark **all** 14 shirt pastel **all** 7 shirt white **all** 28 shirt **all all** 49 pants dark **all** 20 pants pastel **all** 2 pants white **all** 5 pants **all all** 27 **all** dark **all** 62 **all** pastel **all** 54 **all** white **all** 48 **all all all** 164

**Figure 5.21** Relational representation of the data in Figure 5.17.

Consider the tuples (skirt, **all**, **all**, 53) and (dress, **all**, **all**, 35). We have obtained these tuples by eliminating individual tuples with different values for _color_ and _clothes size_, and by replacing the value of _quantity_ by an aggregate—namely, the sum of the quantities. The value **all** can be thought of as representing the set of all values for an attribute. Tuples with the value **all** for the _color_ and _clothes size_ dimensions can be obtained by an aggregation on the _sales_ relation with a **group by** on the column _item name_. Similarly, a **group by** on _color_, _clothes size_ can be used to get the tuples with the value **all** for _item name_, and a **group by** with no attributes (which can simply be omitted in SQL) can be used to get the tuple with value **all** for _item name_, _color_, and _clothes size_.

Hierarchies can also be represented by relations. For example, the fact that skirts and dresses fall under the womenswear category, and the pants and shirts under the menswear category can be represented by a relation _itemcategory_(_item name_, _c_ategory). This relation can be joined with the _sales_ relation, to get a relation

that includes the category for each item. Aggregation on this joined relation allows us to get a cross-tab with hierarchy. As another example, a hierarchy on city can be represented by a single relation _city hierarchy_ (ID, _city_, _state_, _country_, _region_), or by multiple relations, each mapping values in one level of the hierarchy to values at the next level. We assume here that cities have unique identifiers, stored in the attribute ID, to avoid confusing between two cities with the same name, e.g., the Springfield in Missouri and the Springfield in Illinois.  

**204 Chapter 5 Advanced SQL**

**OLAP IMPLEMENTATION**

The earliest OLAP systems used multidimensional arrays in memory to store data cubes, and are referred to as **multidimensional OLAP (MOLAP)** systems. Later, OLAP facilities were integrated into relational systems, with data stored in a relational database. Such systems are referred to as **relational OLAP (ROLAP)** systems. Hybrid systems, which store some summaries in memory and store the base data and other summaries in a relational database, are called **hybrid OLAP (HOLAP)** systems.

Many OLAP systems are implemented as client-server systems. The server contains the relational database as well as any MOLAP data cubes. Client systems obtain views of the data by communicating with the server.

A naı̈ve way of computing the entire data cube (all groupings) on a relation is to use any standard algorithm for computing aggregate operations, one group- ing at a time. The naı̈ve algorithm would require a large number of scans of the relation. A simple optimization is to compute an aggregation on, say, (_item name_, _color_) from an aggregation (_item name_, _color_, _clothes size_), instead of from

the original relation. For the standard SQL aggregate functions, we can compute an aggregate with

grouping on a set of attributes _A_ from an aggregate with grouping on a set of at- tributes _B_ if _A_ ⊆ _B_; you can do so as an exercise (see Exercise 5.24), but note that to compute **avg**, we additionally need the **count** value. (For some nonstandard aggregate functions, such as median, aggregates cannot be computed as above; the optimization described here does not apply to such _non-decomposable_ aggre- gate functions.) The amount of data read drops significantly by computing an aggregate from another aggregate, instead of from the original relation. Further improvements are possible; for instance, multiple groupings can be computed on a single scan of the data.

Early OLAP implementations precomputed and stored entire data cubes, that is, groupings on all subsets of the dimension attributes. Precomputation allows OLAP queries to be answered within a few seconds, even on datasets that may contain millions of tuples adding up to gigabytes of data. However, there are 2_n_ groupings with _n_ dimension attributes; hierarchies on attributes increase the number further. As a result, the entire data cube is often larger than the original relation that formed the data cube and in many cases it is not feasible to store the entire data cube.

Instead of precomputing and storing all possible groupings, it makes sense to precompute and store some of the groupings, and to compute others on demand. Instead of computing queries from the original relation, which may take a very long time, we can compute them from other precomputed queries. For instance, suppose that a query requires grouping by (_item name_, _color_), and this has not been precomputed. The query result can be computed from summaries by (_item name_, _color_, _clothes size_), if that has been precomputed. See the bibliographical

notes for references on how to select a good set of groupings for precomputation, given limits on the storage available for precomputed results.  

**5.6 OLAP 205**

**5.6.3 OLAP in SQL**

Several SQL implementations, such as Microsoft SQL Server, and Oracle, support a **pivot** clause in SQL, which allows creation of cross-tabs. Given the _sales_ relation from Figure 5.16, the query:

**select** \* **from** _sales_ **pivot** (

**sum**(_quantity_) **for** _color_ **in** (’dark’,’pastel’,’white’)

) **order by** _item name_;

returns the cross-tab shown in Figure 5.22. Note that the **for** clause within the **pivot** clause specifies what values from the attribute _color_ should appears as attribute names in the pivot result. The attribute _color_ itself is eliminated from the result, although all other attributes are retained, except that the values for the newly created attributes are specified to come from the attribute _quantity_. In case more than one tuple contributes values to a given cell, the aggregate operation within the **pivot** clause specifies how the values should be combined. In the above example, the _quantity_ values are summed up.

Note that the pivot clause by itself does not compute the subtotals we saw in the pivot table from Figure 5.17. However, we can first generate the relational representation shown in Figure 5.21, as outlined shortly, and then apply the pivot clause on that representation to get an equivalent result. In this case, the value **all** must also be listed in the **for** clause, and the **order by** clause needs to be modified to order **all** at the end.

_item name clothes size dark pastel white_ skirt small 2 11 2 skirt medium 5 9 5 skirt large 1 15 3 dress small 2 4 2 dress medium 6 3 3 dress large 12 3 0 shirt small 2 4 17 shirt medium 6 1 1 shirt large 6 2 10 pants small 14 1 3 pants medium 6 0 0 pants large 0 1 2

**Figure 5.22** Result of SQL pivot operation on the _sales_ relation of Figure 5.16.  

**206 Chapter 5 Advanced SQL**

_item name quantity_ skirt 53 dress 35 shirt 49 pants 27

**Figure 5.23** Query result.

The data in a data cube cannot be generated by a single SQL query, using the basic **group by** constructs, since aggregates are computed for several different groupings of the dimension attributes. For this reason, SQL includes functions to form the grouping needed for OLAP. We discuss these below.

SQL supports generalizations of the **group by** construct to perform the **cube** and **rollup** operations. The **cube** and **rollup** constructs in the **group by** clause allow multiple **group by** queries to be run in a single query with the result returned as a single relation in a style similar to that of the relation of Figure 5.21.

Consider again our retail shop example and the relation:

_sales_ (_item name_, _color_, _clothes size_, _quantity_)

We can find the number of items sold in each item name by writing a simple **group by** query:

**select** _item name_, **sum**(_quantity_) **from** _sales_ **group by** _item name_;

The result of this query is shown in Figure 5.23. Note that this represents the same data as the last column of Figure 5.17 (or equivalently, the first row in the cube of Figure 5.18).

Similarly, we can find the number of items sold in each color, etc. By using multiple attributes in the **group by** clause, we can find how many items were sold with a certain set of properties. For example, we can find a breakdown of sales by item-name and color by writing:

**select** _item name_, _color_, **sum**(_quantity_) **from** _sales_ **group by** _item name_, _color_;

The result of this query is shown in Figure 5.24. Note that this represents the same data as is shown in the the first 4 rows and first 4 columns of Figure 5.17 (or equivalently, the first 4 rows and columns in the cube of Figure 5.18).

If, however, we want to generate the entire data cube using this approach, we would have to write a separate query for each of the following sets of attributes:  

**5.6 OLAP 207**

_item name color quantity_ skirt dark 8 skirt pastel 35 skirt white 10 dress dark 20 dress pastel 10 dress white 5 shirt dark 14 shirt pastel 7 shirt white 28 pants dark 20 pants pastel 2 pants white 5

**Figure 5.24** Query result.

_{_ (_item name_, _color_, _clothes size_), (_item name_, _color_), (_item name_, _clothes size_), (_color_, _clothes size_), (_item name_), (_color_), (_clothes size_), () _}_

where () denotes an empty **group by** list. The **cube** construct allows us to accomplish this in one query:

**select** _item name_, color, _clothes size_, **sum**(_quantity_) **from** _sales_ **group by cube**(_item name_, _color_, _clothes size_);

The above query produces a relation whose schema is:

(_item name_, _color_, _clothes size_, **sum**(_quantity_))

So that the result of this query is indeed a relation, tuples in the result con- tain _null_ as the value of those attributes not present in a particular grouping. For example, tuples produced by grouping on _clothes size_ have a schema (_clothes size_, **sum**(_quantity_)). They are converted to tuples on (_item name_, _color_, _clothes size_,

**sum**(_quantity_)) by inserting _null_ for _item name_ and _color_. Data cube relations are often very large. The cube query above, with 3 possible

colors, 4 possible item names, and 3 sizes, has 80 tuples. The relation of Figure 5.21 is generated using grouping by _item name_ and _color_. It also uses **all** in place of _null_ so as to be more readable to the average user. To generate that relation in SQL, we arrange to substitute **all** for _null_. The query:

**select** _item name_, _color_, **sum**(_quantity_) **from** _sales_ **group by cube**(_item name_, _color_);  

**208 Chapter 5 Advanced SQL**

**THE DECODE FUNCTION**

The **decode** function allows substitution of values in an attribute of a tuple. The general form of **decode** is:

**decode** (_value, match-1, replacement-1, match-2, replacement-2, . . ., match-N, replacement-N, default-replacement_);

It compares _value_ against the _match_ values and if a match is found, it replaces the attribute value with the corresponding replacement value. If no match succeeds, then the attribute value is replaced with the default replacement value.

The **decode** function does not work as we might like for null values because, as we saw in Section 3.6, predicates on nulls evaluate to **unknown**, which ultimately becomes **false**. To deal with this, we apply the **grouping** function, which returns 1 if its argument is a null value generated by a **cube** or **rollup** and 0 otherwise. Then the relation in Figure 5.21, with occurrences of **all** replaced by _null_, can be computed by the query:

**select decode**(**grouping**(_item name_), 1, ’all’, _item name_) **as** _item name_ **decode**(**grouping**(_color_), 1, ’all’, _color_) **as** _color_ **sum**(_quantity_) **as** _quantity_

**from** _sales_ **group by cube**(_item name_, _color_);

generates the relation of Figure 5.21 with nulls. The substitution of **all** is achieved using the SQL **decode** and **grouping** functions. The **decode** function is conceptu- ally simple but its syntax is somewhat hard to read. See blue box for details.

The **rollup** construct is the same as the **cube** construct except that **rollup** generates fewer **group by** queries. We saw that **group by cube** (_item name_, _color_, _clothes size_) generated all 8 ways of forming a **group by** query using some (or all or none) of the attributes. In:

**select** _item name_, _color_, _clothes size_, **sum**(_quantity_) **from** _sales_ **group by rollup**(_item name_, _color_, _clothes size_);

**group by rollup**(_item name_, _color_, _clothes size_) generates only 4 groupings:

_{_ (_item name_, _color_, _clothes size_), (_item name_, _color_), (_item name_), () _}_

Notice that the order of the attributes in the **rollup** makes a difference; the final attribute (_clothes size_, in our example) appears in only one grouping, the penul- timate (second last) attribute in 2 groupings, and so on, with the first attribute appearing in all groups but one (the empty grouping).  

**5.7 Summary 209**

Why might we want the specific groupings that are used in **rollup**? These groups are of frequent practical interest for hierarchies (as in Figure 5.19, for example). For the location hierarchy (_Region, Country, State, City_), we may want to group by _Region_ to get sales by region. Then we may want to “drill down” to the level of countries within each region, which means we would group by _Region, Country_. Drilling down further, we may wish to group by _Region, Country, State_ and then by _Region, Country, State, City_. The **rollup** construct allows us to specify this sequence of drilling down for further detail.

Multiple **rollup**s and **cube**s can be used in a single **group by** clause. For instance, the following query:

**select** _item name_, _color_, _clothes size_, **sum**(_quantity_) **from** _sales_ **group by rollup**(_item name_), **rollup**(_color_, _clothes size_);

generates the groupings:

_{_ (_item name_, _color_, _clothes size_), (_item name_, _color_), (_item name_), (_color_, _clothes size_), (_color_), () _}_

To understand why, observe that **rollup**(_item name_) generates two groupings, _{_(_item name_), ()_}_, and **rollup**(_color_, _clothes size_) generates three groupings, _{_(_color_, _clothes size_), (_color_), () _}_. The Cartesian product of the two gives us the six groupings shown.

Neither the **rollup** nor the **cube** clause gives complete control on the group- ings that are generated. For instance, we cannot use them to specify that we want only groupings _{_(_color_, _clothes size_), (_clothes size_, _item name_)_}_. Such restricted groupings can be generated by using the **grouping** construct in the **having** clause; we leave the details as an exercise for you.

**5.7 Summary**

• SQL queries can be invoked from host languages, via embedded and dynamic SQL. The ODBC and JDBC standards define application program interfaces to access SQL databases from C and Java language programs. Increasingly, programmers use these APIs to access databases.

• Functions and procedures can be defined using SQLprocedural extensions that allow iteration and conditional (if-then-else) statements.

• Triggers define actions to be executed automatically when certain events occur and corresponding conditions are satisfied. Triggers have many uses, such as implementing business rules, audit logging, and even carrying out actions outside the database system. Although triggers were not added to the  

**210 Chapter 5 Advanced SQL**

SQL standard until SQL:1999, most database systems have long implemented triggers.

• Some queries, such as transitive closure, can be expressed either by using iteration or by using recursive SQL queries. Recursion can be expressed using either recursive views or recursive **with** clause definitions.

• SQL supports several advanced aggregation features, including ranking and windowing queries that simplify the expression of some aggregates and allow more efficient evaluation.

• Online analytical processing (OLAP) tools help analysts view data summa- rized in different ways, so that they can gain insight into the functioning of an organization.

◦ OLAP tools work on multidimensional data, characterized by dimension attributes and measure attributes.

◦ The data cube consists of multidimensional data summarized in different ways. Precomputing the data cube helps speed up queries on summaries of data.

◦ Cross-tab displays permit users to view two dimensions of multidimen- sional data at a time, along with summaries of the data.

◦ Drill down, rollup, slicing, and dicing are among the operations that users perform with OLAP tools.

• SQL, starting with the SQL:1999 standard, provides a variety of operators for data analysis, including **cube** and **rollup** operations. Some systems support a **pivot** clause, which allows easy creation of cross-tabs.

**Review Terms**

• JDBC

• ODBC

• Prepared statements • Accessing metadata • SQL injection • Embedded SQL

• Cursors • Updatable cursors • Dynamic SQL

• SQL functions • Stored procedures • Procedural constructs

• External language routines • Trigger • Before and after triggers • Transition variables and tables • Recursive queries • Monotonic queries • Ranking functions

◦ Rank

◦ Dense rank

◦ Partition by

• Windowing  

**Practice Exercises 211**

• Online analytical processing (OLAP)

• Multidimensional data

◦ Measure attributes

◦ Dimension attributes

◦ Pivoting

◦ Data cube

◦ Slicing and dicing

◦ Rollup and drill down

• Cross-tabulation

**Practice Exercises**

**5.1** Describe the circumstances in which you would choose to use embedded SQL rather than SQL alone or only a general-purpose programming lan- guage.

**5.2** Write a Java function using JDBC metadata features that takes a ResultSet as an input parameter, and prints out the result in tabular form, with appropriate names as column headings.

**5.3** Write a Java function using JDBC metadata features that prints a list of all relations in the database, displaying for each relation the names and types of its attributes.

**5.4** Show how to enforce the constraint “an instructor cannot teach in two different classrooms in a semester in the same time slot.” using a trigger (remember that the constraint can be violated by changes to the _teaches_ relation as well as to the _section_ relation).

**5.5** Write triggers to enforce the referential integrity constraint from _section_ to _time slot_, on updates to _section_, and _time slot_. Note that the ones we wrote in Figure 5.8 do not cover the **update** operation.

**5.6** To maintain the _tot cred_ attribute of the _student_ relation, carry out the fol- lowing:

a. Modify the trigger on updates of _takes_, to handle all updates that can affect the value of _tot cred_.

b. Write a trigger to handle inserts to the _takes_ relation.

c. Under what assumptions is it reasonable not to create triggers on the _course_ relation?

**5.7** Consider the bank database of Figure 5.25. Let us define a view _branch cust_ as follows:

**create view** _branch cust_ **as select** _branch name, customer name_ **from** _depositor, account_ **where** _depositor.account number_ \= _account.account number_  

**212 Chapter 5 Advanced SQL**

_branch_(_branch name_, _branch city, assets_) _customer_ (_customer name_, _customer street, cust omer city_) _loan_ (_loan number_, _branch name, amount_) _borrower_ (_customer name_, _loan number_) _account_ (_account number_, _branch name, balance_ ) _depositor_ (_customer name_, _account number_)

**Figure 5.25** Banking database for Exercises 5.7, 5.8, and 5.28 .

Suppose that the view is _materialized_; that is, the view is computed and stored. Write triggers to _maintain_ the view, that is, to keep it up-to-date on insertions to and deletions from _depositor_ or _account_. Do not bother about updates.

**5.8** Consider the bank database of Figure 5.25. Write an SQL trigger to carry out the following action: On **delete** of an account, for each owner of the account, check if the owner has any remaining accounts, and if she does not, delete her from the _depositor_ relation.

**5.9** Show how to express **group by cube**(_a , b, c, d_) using **rollup**; your answer should have only one **group by** clause.

**5.10** Given a relation _S_(_student, sub ject, marks_), write a query to find the top _n_ students by total marks, by using ranking.

**5.11** Consider the _sales_ relation from Section 5.6. Write an SQL query to compute the cube operation on the relation, giving the relation in Figure 5.21. Do not use the **cube** construct.

**Exercises**

**5.12** Consider the following relations for a company database:

• _emp_ (_ename_, _dname_, _salary_)

• _mgr_ (_ename_, _mname_)

and the Java code in Figure 5.26, which uses the JDBC API. Assume that the userid, password, machine name, etc. are all okay. Describe in concise English what the Java program does. (That is, produce an English sen- tence like “It finds the manager of the toy department,” not a line-by-line description of what each Java statement does.)

**5.13** Suppose you were asked to define a class MetaDisplay in Java, containing a method static void printTable(String r); the method takes a relation name _r_ as input, executes the query “**select** \* **from** _r_”, and prints the result out in nice tabular format, with the attribute names displayed in the header of the table.  

**Exercises 213**

import java.sql.\*; public class Mystery _{_

public static void main(String\[\] args) _{_ try _{_

Connection con=null; Class.forName("oracle.jdbc.driver.OracleDriver"); con=DriverManager.getConnection(

"jdbc:oracle:thin:star/X@//edgar.cse.lehigh.edu:1521/XE"); Statement s=con.createStatement(); String q; String empName = "dog"; boolean more; ResultSet result; do _{_

q = "select mname from mgr where ename = ’" + empName + "’"; result = s.executeQuery(q); more = result.next(); if (more) _{_

empName = result.getString("mname"); System.out.println (empName);

_} }_ while (more); s.close(); con.close();

_}_ catch(Exception e)_{_e.printStackTrace();_} }}_

**Figure 5.26** Java code for Exercise 5.12.

a. What do you need to know about relation _r_ to be able to print the result in the specified tabular format.

b. What JDBC methods(s) can get you the required information?

c. Write the method printTable(String r) using the JDBC API.

**5.14** Repeat Exercise 5.13 using ODBC, defining void printTable(char \*r) as a function instead of a method.

**5.15** Consider an employee database with two relations

_employee_ (_employee name_, _street_, _city_) _works_ (_employee name_, _company name_, _salary_)

where the primary keys are underlined. Write a query to find companies whose employees earn a higher salary, on average, than the average salary at “First Bank Corporation”.  

**214 Chapter 5 Advanced SQL**

a. Using SQL functions as appropriate.

b. Without using SQL functions.

**5.16** Rewrite the query in Section 5.2.1 that returns the name and budget of all departments with more than 12 instructors, using the **with** clause instead of using a function call.

**5.17** Compare the use of embedded SQL with the use in SQL of functions defined in a general-purpose programming language. Under what circumstances would you use each of these features?

**5.18** Modify the recursive query in Figure 5.15 to define a relation

_prereq depth_(_course id_, _prereq id_, _depth_)

where the attribute _depth_ indicates how many levels of intermediate pre- requisites are there between the course and the prerequisite. Direct prereq- uisites have a depth of 0.

**5.19** Consider the relational schema

_part_(_part id_, _name_, _cost_) _subpart_(_part id_, _subpart id_, _count_)

A tuple (_p_1_, p_2_,_ 3) in the _subpart_ relation denotes that the part with part-id _p_2 is a direct subpart of the part with part-id _p_1, and _p_1 has 3 copies of _p_2\. Note that _p_2 may itself have further subparts. Write a recursive SQL query that outputs the names of all subparts of the part with part-id “P-100”.

**5.20** Consider again the relational schema from Exercise 5.19. Write a JDBC function using non-recursive SQL to find the total cost of part “P-100”, including the costs of all its subparts. Be sure to take into account the fact that a part may have multiple occurrences of a subpart. You may use recursion in Java if you wish.

**5.21** Suppose there are two relations _r_ and _s_, such that the foreign key _B_ of _r_ references the primary key _A_of _s_. Describe how the trigger mechanism can be used to implement the **on delete cascade** option, when a tuple is deleted from _s_.

**5.22** The execution of a trigger can cause another action to be triggered. Most database systems place a limit on how deep the nesting can be. Explain why they might place such a limit.

**5.23** Consider the relation, _r_ , shown in Figure 5.27. Give the result of the follow- ing query:  

**Exercises 215**

_building room number time slot id course id sec id_ Garfield 359 A BIO-101 1 Garfield 359 B BIO-101 2 Saucon 651 A CS-101 2 Saucon 550 C CS-319 1 Painter 705 D MU-199 1 Painter 403 D FIN-201 1

**Figure 5.27** The relation _r_ for Exercise 5.23.

**select** _building_, _room number_, _time slot id_, **count**(\*) **from** r **group by rollup** (_building_, _room number_, _time slot id_)

**5.24** For each of the SQL aggregate functions **sum, count, min**, and **max**, show how to compute the aggregate value on a multiset _S_1 ∪ _S_2, given the aggre- gate values on multisets _S_1 and _S_2.

On the basis of the above, give expressions to compute aggregate values with grouping on a subset _S_ of the attributes of a relation _r_ (_A, B, C, D, E_), given aggregate values for grouping on attributes _T_ ⊇ _S_, for the following aggregate functions:

a. **sum, count, min,** and **max**

b. **avg**

c. Standard deviation

**5.25** In Section 5.5.1, we used the _student grades_ view of Exercise 4.5 to write a query to find the rank of each student based on grade-point average. Modify that query to show only the top 10 students (that is, those students whose rank is 1 through 10).

**5.26** Give an example of a pair of groupings that cannot be expressed by using a single **group by** clause with **cube** and **rollup**.

**5.27** Given relation _s_(_a , b, c_), show how to use the extended SQL features to generate a histogram of _c_ versus _a_ , dividing _a_ into 20 equal-sized partitions (that is, where each partition contains 5 percent of the tuples in _s_, sorted by _a_ ).

**5.28** Consider the bank database of Figure 5.25 and the _balance_ attribute of the _account_ relation. Write an SQL query to compute a histogram of _balance_ values, dividing the range 0 to the maximum account balance present, into three equal ranges.  

**216 Chapter 5 Advanced SQL**

**Tools**

Most database vendors provide OLAP tools as part of their database systems, or as add-on applications. These include OLAP tools from Microsoft Corp., Oracle Express, and Informix Metacube. Tools may be integrated with a larger “business intelligence” product such as IBM Cognos. Many companies also provide analysis tools for specific applications, such as customer relationship management (for example, Oracle Siebel CRM).

**Bibliographical Notes**

See the bibliographic notes of Chapter 3 for references to SQL standards and books on SQL.

An excellent source for more (and up-to-date) information on JDBC, and on Java in general, is java.sun.com/docs/books/tutorial. References to books on Java (in- cluding JDBC) are also available at this URL. The ODBC API is described in Microsoft \[1997\] and Sanders \[1998\]. Melton and Eisenberg \[2000\] provides a guide to SQLJ, JDBC, and related technologies. More information on ODBC, ADO, and ADO.NET can be found on msdn.microsoft.com/data.

In the context of functions and procedures in SQL, many database products support features beyond those specified in the standards, and do not support many of the features of the standard. More information on these features may be found in the SQL user manuals of the respective products.

The original SQL proposals for assertions and triggers are discussed in Astra- han et al. \[1976\], Chamberlin et al. \[1976\], and Chamberlin et al. \[1981\]. Melton and Simon \[2001\], Melton \[2002\], and Eisenberg and Melton \[1999\] provide textbook coverage of SQL:1999, the version of the SQL standard that first included triggers.

Recursive query processing was first studied in detail in the context of a query language called Datalog, which was based on mathematical logic and followed the syntax of the logic programming language Prolog. Ramakrishnan and Ullman \[1995\] provides a survey of results in this area, including techniques to optimize queries that select a subset of tuples from a recursively defined view.

Gray et al. \[1995\] and Gray et al. \[1997\] describe the data-cube operator. Efficient algorithms for computing data cubes are described by Agarwal et al. \[1996\], Harinarayan et al. \[1996\], and Ross and Srivastava \[1997\]. Descriptions of extended aggregation support in SQL:1999 can be found in the product manuals of database systems such as Oracle and IBM DB2.

There has been a substantial amount of research on the efficient processing of “top-_k_” queries that return only the top-_k_\-ranked results. A survey of that work appears in Ilyas et al. \[2008\].  

**_C H A P T E R_6 Formal Relational Query Languages**

In Chapters 2 through 5 we introduced the relational model and covered SQL in great detail. In this chapter we present the formal model upon which SQL as well as other relational query languages are based.

We cover three formal languages. We start by presenting the relational algebra, which forms the basis of the widely used SQL query language. We then cover the tuple relational calculus and the domain relational calculus, which are declarative query languages based on mathematical logic.

**6.1 The Relational Algebra**

The relational algebra is a _procedural_ query language. It consists of a set of op- erations that take one or two relations as input and produce a new relation as their result. The fundamental operations in the relational algebra are _select, project_, _union_, _set difference_, _Cartesian product,_ and _rename_. In addition to the fundamental operations, there are several other operations—namely, _set intersection_, _natural join_, and _assignment_. We shall define these operations in terms of the fundamental operations.

**6.1.1 Fundamental Operations**

The select, project, and rename operations are called _unary_ operations, because they operate on one relation. The other three operations operate on pairs of relations and are, therefore, called _binary_ operations.

**6.1.1.1 The Select Operation**

The **select** operation selects tuples that satisfy a given predicate. We use the lowercase Greek letter sigma () to denote selection. The predicate appears as a subscript to . The argument relation is in parentheses after the . Thus, to select

**217**  

**218 Chapter 6 Formal Relational Query Languages**

_ID name dept name salary_

10101 Srinivasan Comp. Sci. 65000 12121 Wu Finance 90000 15151 Mozart Music 40000 22222 Einstein Physics 95000 32343 El Said History 60000 33456 Gold Physics 87000 45565 Katz Comp. Sci. 75000 58583 Califieri History 62000 76543 Singh Finance 80000 76766 Crick Biology 72000 83821 Brandt Comp. Sci. 92000 98345 Kim Elec. Eng. 80000

**Figure 6.1** The _instructor_ relation.

those tuples of the _instructor_ relation where the instructor is in the “Physics” department, we write:

_dept name_ \= “Physics” (_instructor_ )

If the _instructor_ relation is as shown in Figure 6.1, then the relation that results from the preceding query is as shown in Figure 6.2.

We can find all instructors with salary greater than $90,000 by writing:

_salary>_90000 (_instructor_ )

In general, we allow comparisons using =, =, _<_, ≤, _\>_, and ≥ in the selection predicate. Furthermore, we can combine several predicates into a larger predicate by using the connectives _and_ (∧), _or_ (∨), and _not_ (¬). Thus, to find the instructors in Physics with a salary greater than $90,000, we write:

_dept name_ \= “Physics” ∧ _salary>_90000 (_instructor_ )

The selection predicate may include comparisons between two attributes. To illustrate, consider the relation _department_. To find all departments whose name is the same as their building name, we can write:

_dept name_ \= _building_ (_department_)

_ID name dept name salary_ 22222 Einstein Physics 95000 33456 Gold Physics 87000

**Figure 6.2** Result of _dept name_ \= “Physics” (_instructor_ ).  

**6.1 The Relational Algebra 219**

**SQL VERSUS RELATIONAL ALGEBRA**

The term _select_ in relational algebra has a different meaning than the one used in SQL, which is an unfortunate historical fact. In relational algebra, the term _select_ corresponds to what we refer to in SQL as _where_. We emphasize the different interpretations here to minimize potential confusion.

**6.1.1.2 The Project Operation**

Suppose we want to list all instructors’ _ID_, _name_, and _salary_, but do not care about the _dept name_. The **project** operation allows us to produce this relation. The project operation is a unary operation that returns its argument relation, with certain attributes left out. Since a relation is a set, any duplicate rows are eliminated. Projection is denoted by the uppercase Greek letter pi (). We list those attributes that we wish to appear in the result as a subscript to . The argument relation follows in parentheses. We write the query to produce such a list as:

_ID, name, salary_ (_instructor_ )

Figure 6.3 shows the relation that results from this query.

**6.1.1.3 Composition of Relational Operations**

The fact that the result of a relational operation is itself a relation is important. Consider the more complicated query “Find the name of all instructors in the Physics department.” We write:

_ID name salary_ 10101 Srinivasan 65000 12121 Wu 90000 15151 Mozart 40000 22222 Einstein 95000 32343 El Said 60000 33456 Gold 87000 45565 Katz 75000 58583 Califieri 62000 76543 Singh 80000 76766 Crick 72000 83821 Brandt 92000 98345 Kim 80000

**Figure 6.3** Result of _ID, name, salary_ (_instructor_ ).  

**220 Chapter 6 Formal Relational Query Languages**

_name_ (_dept name_ \= “Physics” (_instructor_ ))

Notice that, instead of giving the name of a relation as the argument of the projection operation, we give an expression that evaluates to a relation.

In general, since the result of a relational-algebra operation is of the same type (relation) as its inputs, relational-algebra operations can be composed together into a **relational-algebra expression**. Composing relational-algebra operations into relational-algebra expressions is just like composing arithmetic operations (such as +, −, ∗, and ÷) into arithmetic expressions. We study the formal definition of relational-algebra expressions in Section 6.1.2.

**6.1.1.4 The Union Operation**

Consider a query to find the set of all courses taught in the Fall 2009 semester, the Spring 2010 semester, or both. The information is contained in the _section_ relation (Figure 6.4). To find the set of all courses taught in the Fall 2009 semester, we write:

_course id_ (_semester_ \= “Fall” ∧ _year_\=2009 (_section_))

To find the set of all courses taught in the Spring 2010 semester, we write:

_course id_ (_semester_ \= “Spring” ∧ _year_\=2010 (_section_))

To answer the query, we need the **union** of these two sets; that is, we need all section IDs that appear in either or both of the two relations. We find these data

_course id sec id semester year building room number time slot id_

BIO-101 1 Summer 2009 Painter 514 B BIO-301 1 Summer 2010 Painter 514 A CS-101 1 Fall 2009 Packard 101 H CS-101 1 Spring 2010 Packard 101 F CS-190 1 Spring 2009 Taylor 3128 E CS-190 2 Spring 2009 Taylor 3128 A CS-315 1 Spring 2010 Watson 120 D CS-319 1 Spring 2010 Watson 100 B CS-319 2 Spring 2010 Taylor 3128 C CS-347 1 Fall 2009 Taylor 3128 A EE-181 1 Spring 2009 Taylor 3128 C FIN-201 1 Spring 2010 Packard 101 B HIS-351 1 Spring 2010 Painter 514 C MU-199 1 Spring 2010 Packard 101 D PHY-101 1 Fall 2009 Watson 100 A

**Figure 6.4** The _section_ relation.  

**6.1 The Relational Algebra 221**

_course id_ CS-101 CS-315 CS-319 CS-347 FIN-201 HIS-351 MU-199 PHY-101

**Figure 6.5** Courses offered in either Fall 2009, Spring 2010 or both semesters.

by the binary operation union, denoted, as in set theory, by ∪. So the expression needed is:

_course id_ (_semester_ \= “Fall” ∧ _year_\=2009 (_section_)) ∪ _course id_ (_semester_ \= “Spring” ∧ _year_\=2010 (_section_))

The result relation for this query appears in Figure 6.5. Notice that there are 8 tuples in the result, even though there are 3 distinct courses offered in the Fall 2009 semester and 6 distinct courses offered in the Spring 2010 semester. Since relations are sets, duplicate values such as CS-101, which is offered in both semesters, are replaced by a single occurrence.

Observe that, in our example, we took the union of two sets, both of which consisted of _course id_ values. In general, we must ensure that unions are taken between _compatible_ relations. For example, it would not make sense to take the union of the _instructor_ relation and the _student_ relation. Although both relations have four attributes, they differ on the _salary_ and _tot cred_ domains. The union of these two attributes would not make sense in most situations. Therefore, for a union operation _r_ ∪ _s_ to be valid, we require that two conditions hold:

**1\.** The relations _r_ and _s_ must be of the same arity. That is, they must have the same number of attributes.

**2\.** The domains of the _i_th attribute of _r_ and the _i_th attribute of _s_ must be the same, for all _i_ .

Note that _r_ and _s_ can be either database relations or temporary relations that are the result of relational-algebra expressions.

**6.1.1.5 The Set-Difference Operation**

The **set-difference** operation, denoted by −, allows us to find tuples that are in one relation but are not in another. The expression _r_ − _s_ produces a relation containing those tuples in _r_ but not in _s_.  

**222 Chapter 6 Formal Relational Query Languages**

_course id_ CS-347 PHY-101

**Figure 6.6** Courses offered in the Fall 2009 semester but not in Spring 2010 semester.

We can find all the courses taught in the Fall 2009 semester but not in Spring 2010 semester by writing:

_course id_ (_semester_ \= “Fall” ∧ _year_\=2009 (_section_)) - _course id_ (_semester_ \= “Spring” ∧ _year_\=2010 (_section_))

The result relation for this query appears in Figure 6.6. As with the union operation, we must ensure that set differences are taken

between _compatible_ relations. Therefore, for a set-difference operation _r_ − _s_ to be valid, we require that the relations _r_ and _s_ be of the same arity, and that the domains of the _i_th attribute of _r_ and the _i_th attribute of _s_ be the same, for all _i_ .

**6.1.1.6 The Cartesian-Product Operation**

The **Cartesian-product** operation, denoted by a cross (×), allows us to combine information from any two relations. We write the Cartesian product of relations _r_1 and _r_2 as _r_1 × _r_2.

Recall that a relation is by definition a subset of a Cartesian product of a set of domains. From that definition, we should already have an intuition about the definition of the Cartesian-product operation. However, since the same attribute name may appear in both _r_1 and _r_2, we need to devise a naming schema to distinguish between these attributes. We do so here by attaching to an attribute the name of the relation from which the attribute originally came. For example, the relation schema for _r_ \= _instructor_ × _teaches_ is:

(_instructor_._ID_, _instructor_._name_, _instructor_._dept name_, _instructor_._salary teaches_._ID_, _teaches_._course id_, _teaches_._sec id_, _teaches_._semester_, _teaches_._year_)

With this schema, we can distinguish _instructor_._ID_ from _teaches_._ID_. For those at- tributes that appear in only one of the two schemas, we shall usually drop the relation-name prefix. This simplification does not lead to any ambiguity. We can then write the relation schema for _r_ as:

(_instructor_._ID_, _name_, _dept name_, _salary teaches_._ID_, _course id_, _sec id_, _semester_, _year_)

This naming convention _requires_ that the relations that are the arguments of the Cartesian-product operation have distinct names. This requirement causes problems in some cases, such as when the Cartesian product of a relation with itself is desired. A similar problem arises if we use the result of a relational-algebra expression in a Cartesian product, since we shall need a name for the relation so  

**6.1 The Relational Algebra 223**

_ID course id sec id semester year_

10101 CS-101 1 Fall 2009 10101 CS-315 1 Spring 2010 10101 CS-347 1 Fall 2009 12121 FIN-201 1 Spring 2010 15151 MU-199 1 Spring 2010 22222 PHY-101 1 Fall 2009 32343 HIS-351 1 Spring 2010 45565 CS-101 1 Spring 2010 45565 CS-319 1 Spring 2010 76766 BIO-101 1 Summer 2009 76766 BIO-301 1 Summer 2010 83821 CS-190 1 Spring 2009 83821 CS-190 2 Spring 2009 83821 CS-319 2 Spring 2010 98345 EE-181 1 Spring 2009

**Figure 6.7** The _teaches_ relation.

that we can refer to the relation’s attributes. In Section 6.1.1.7, we see how to avoid these problems by using the rename operation.

Now that we know the relation schema for _r_ \= _instructor_ × _teaches_, what tuples appear in _r_? As you may suspect, we construct a tuple of _r_ out of each possible pair of tuples: one from the _instructor_ relation (Figure 6.1) and one from the _teaches_ relation (Figure 6.7). Thus, _r_ is a large relation, as you can see from Figure 6.8, which includes only a portion of the tuples that make up _r_.1

Assume that we have _n_1 tuples in _instructor_ and _n_2 tuples in _teaches_. Then, there are _n_1 ∗ _n_2 ways of choosing a pair of tuples—one tuple from each relation; so there are _n_1 ∗ _n_2 tuples in _r_. In particular, note that for some tuples _t_ in _r_, it may be that _t_\[_instructor_._ID_\] = _t_\[_teaches_._ID_\].

In general, if we have relations _r_1(_R_1) and _r_2(_R_2), then _r_1 × _r_2 is a relation whose schema is the concatenation of _R_1 and _R_2\. Relation _R_ contains all tuples _t_ for which there is a tuple _t_1 in _r_1 and a tuple _t_2 in _r_2 for which _t_\[_R_1\] = _t_1\[_R_1\] and _t_\[_R_2\] = _t_2\[_R_2\].

Suppose that we want to find the names of all instructors in the Physics department together with the _course id_ of all courses they taught. We need the information in both the _instructor_ relation and the _teaches_ relation to do so. If we write:

_dept name_ \= “Physics”(_instructor_ × _teaches_)

then the result is the relation in Figure 6.9.

1Note that we renamed _instructor_._ID_ as _inst_._ID_ to reduce the width of the tables in Figures 6.8 and 6.9.  

**224 Chapter 6 Formal Relational Query Languages**

_inst_._ID name dept name salary teaches_._ID course id sec id semester year_ 10101 Srinivasan Physics 95000 10101 CS-101 1 Fall 2009 10101 Srinivasan Physics 95000 10101 CS-315 1 Spring 2010 10101 Srinivasan Physics 95000 10101 CS-347 1 Fall 2009 10101 Srinivasan Physics 95000 10101 FIN-201 1 Spring 2010 10101 Srinivasan Physics 95000 15151 MU-199 1 Spring 2010 10101 Srinivasan Physics 95000 22222 PHY-101 1 Fall 2009

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ... 12121 Wu Physics 95000 10101 CS-101 1 Fall 2009 12121 Wu Physics 95000 10101 CS-315 1 Spring 2010 12121 Wu Physics 95000 10101 CS-347 1 Fall 2009 12121 Wu Physics 95000 10101 FIN-201 1 Spring 2010 12121 Wu Physics 95000 15151 MU-199 1 Spring 2010 12121 Wu Physics 95000 22222 PHY-101 1 Fall 2009

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ... 15151 Mozart Physics 95000 10101 CS-101 1 Fall 2009 15151 Mozart Physics 95000 10101 CS-315 1 Spring 2010 15151 Mozart Physics 95000 10101 CS-347 1 Fall 2009 15151 Mozart Physics 95000 10101 FIN-201 1 Spring 2010 15151 Mozart Physics 95000 15151 MU-199 1 Spring 2010 15151 Mozart Physics 95000 22222 PHY-101 1 Fall 2009

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ... 22222 Einstein Physics 95000 10101 CS-101 1 Fall 2009 22222 Einstein Physics 95000 10101 CS-315 1 Spring 2010 22222 Einstein Physics 95000 10101 CS-347 1 Fall 2009 22222 Einstein Physics 95000 10101 FIN-201 1 Spring 2010 22222 Einstein Physics 95000 15151 MU-199 1 Spring 2010 22222 Einstein Physics 95000 22222 PHY-101 1 Fall 2009

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ...

**Figure 6.8** Result of _instructor_ × _teaches_.

We have a relation that pertains only to instructors in the Physics department. However, the _course id_ column may contain information about courses that were not taught by the corresponding instructor. (If you do not see why that is true, recall that the Cartesian product takes all possible pairings of one tuple from _instructor_ with one tuple of _teaches_.)

Since the Cartesian-product operation associates _every_ tuple of _instructor_ with every tuple of _teaches_, we know that if a an instructor is in the Physics department, and has taught a course (as recorded in the _teaches_ relation), then there is some  

**6.1 The Relational Algebra 225**

_inst_._ID name dept name salary teaches_._ID course id sec id semester year_ 22222 Einstein Physics 95000 10101 CS-437 1 Fall 2009 22222 Einstein Physics 95000 10101 CS-315 1 Spring 2010 22222 Einstein Physics 95000 12121 FIN-201 1 Spring 2010 22222 Einstein Physics 95000 15151 MU-199 1 Spring 2010 22222 Einstein Physics 95000 22222 PHY-101 1 Fall 2009 22222 Einstein Physics 95000 32343 HIS-351 1 Spring 2010

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ... 33456 Gold Physics 87000 10101 CS-437 1 Fall 2009 33456 Gold Physics 87000 10101 CS-315 1 Spring 2010 33456 Gold Physics 87000 12121 FIN-201 1 Spring 2010 33456 Gold Physics 87000 15151 MU-199 1 Spring 2010 33456 Gold Physics 87000 22222 PHY-101 1 Fall 2009 33456 Gold Physics 87000 32343 HIS-351 1 Spring 2010

... ... ... ... ... ... ... ... ...

... ... ... ... ... ... ... ... ...

**Figure 6.9** Result of _dept name_ \= “Physics”(_instructor_ × _teaches_ ).

tuple in _dept name_ \= “Physics”(_instructor_ × _teaches_) that contains his name, and which satisfies _instructor_._ID_ \= _teaches_._ID_. So, if we write:

_instructor .ID_ \= _teaches .ID_ (_dept name_ \= “Physics”(_instructor_ × _teaches_))

we get only those tuples of _instructor_ × _teaches_ that pertain to instructors in Physics and the courses that they taught.

Finally, since we only want the names of all instructors in the Physics depart- ment together with the _course id_ of all courses they taught, we do a projection:

_name, course id_ (_instructor .ID_ \= _teaches .ID_ (_dept name_ \= “Physics”(_instructor_ × _teaches_)))

The result of this expression, shown in Figure 6.10, is the correct answer to our query. Observe that although instructor Gold is in the Physics department, he does not teach any course (as recorded in the _teaches_ relation), and therefore does not appear in the result.

_name course id_ Einstein PHY-101

**Figure 6.10** Result of _name, course id_ (_instructor .ID_ \= _teaches.ID_ (_dept name_ \= “Physics”(_instructor_ × _teaches_ ))).  

**226 Chapter 6 Formal Relational Query Languages**

Note that there is often more than one way to write a query in relational algebra. Consider the following query:

_name, course id_ (_instructor .ID_ \= _teaches .ID_ ((_dept name_ \= “Physics”(_instructor_ )) × _teaches_))

Note the subtle difference between the two queries: in the query above, the selec- tion that restricts _dept name_ to Physics is applied to _instructor_, and the Cartesian product is applied subsequently; in contrast, the Cartesian product was applied before the selection in the earlier query. However, the two queries are **equivalent**; that is, they give the same result on any database.

**6.1.1.7 The Rename Operation**

Unlike relations in the database, the results of relational-algebra expressions do not have a name that we can use to refer to them. It is useful to be able to give them names; the **rename** operator, denoted by the lowercase Greek letter rho ( ), lets us do this. Given a relational-algebra expression _E_ , the expression

_x_ (_E_)

returns the result of expression _E_ under the name _x_. A relation _r_ by itself is considered a (trivial) relational-algebra expression.

Thus, we can also apply the rename operation to a relation _r_ to get the same relation under a new name.

A second form of the rename operation is as follows: Assume that a relational- algebra expression _E_ has arity _n_. Then, the expression

_x_(_A_1_,A_2_,...,An_) (_E_)

returns the result of expression _E_ under the name _x_, and with the attributes renamed to _A_1_, A_2_, . . . , An_.

To illustrate renaming a relation, we consider the query “Find the highest salary in the university.” Our strategy is to (1) compute first a temporary relation consisting of those salaries that are _not_ the largest and (2) take the set difference between the relation _salary_ (_instructor_ ) and the temporary relation just computed, to obtain the result.

**1\.** Step 1: To compute the temporary relation, we need to compare the values of all salaries. We do this comparison by computing the Cartesian product _instructor_ × _instructor_ and forming a selection to compare the value of any two salaries appearing in one tuple. First, we need to devise a mechanism to distinguish between the two _salary_ attributes. We shall use the rename operation to rename one reference to the instructor relation; thus we can reference the relation twice without ambiguity.  

**6.1 The Relational Algebra 227**

_salary_ 65000 90000 40000 60000 87000 75000 62000 72000 80000 92000

**Figure 6.11** Result of the subexpression _instructor .salary_ (_instructor .salary < d .salary_ (_instructor_ × _d_ (_instructor_ ))).

We can now write the temporary relation that consists of the salaries that are not the largest:

_instructor .salary_ (_instructor .salary < d .salary_ (_instructor_ × _d_ (_instructor_ )))

This expression gives those salaries in the _instructor_ relation for which a larger salary appears somewhere in the _instructor_ relation (renamed as _d_). The result contains all salaries _except_ the largest one. Figure 6.11 shows this relation.

**2\.** Step 2: The query to find the largest salary in the university can be written as:

_salary_ (_instructor_ ) − _instructor .salary_ (_instructor .salary < d .salary_ (_instructor_ × _d_ (_instructor_ )))

Figure 6.12 shows the result of this query.

The rename operation is not strictly required, since it is possible to use a positional notation for attributes. We can name attributes of a relation implicitly by using a positional notation, where $1, $2, _. . ._ refer to the first attribute, the second attribute, and so on. The positional notation also applies to results of relational- algebra operations. The following relational-algebra expression illustrates the

_salary_ 95000

**Figure 6.12** Highest salary in the university.  

**228 Chapter 6 Formal Relational Query Languages**

use of positional notation to write the expression we saw earlier, which computes salaries that are not the largest:

$4 ($4 _<_ $8 (_instructor_ × _instructor_ ))

Note that the Cartesian product concatenates the attributes of the two relations. Thus, for the result of the Cartesian product (_instructor_ × _instructor_), $4 refers to the _salary_ attribute from the first occurrence of _instructor_, while $8 refers to the _salary_ attribute from the second occurrence of _instructor_. A positional notation can also be used to refer to relation names, if a binary operation needs to distinguish between its two operand relations. For example, $_R_1 could refer to the first operand relation, and $_R_2 could refer to the second operand relation of a Cartesian product. However, the positional notation is inconvenient for humans, since the position of the attribute is a number, rather than an easy-to-remember attribute name. Hence, we do not use the positional notation in this textbook.

**6.1.2 Formal Definition of the Relational Algebra**

The operations in Section 6.1.1 allow us to give a complete definition of an expres- sion in the relational algebra. A basic expression in the relational algebra consists of either one of the following:

• A relation in the database

• A constant relation

A constant relation is written by listing its tuples within { }, for example { (22222, Einstein, Physics, 95000), (76543, Singh, Finance, 80000) }.

A general expression in the relational algebra is constructed out of smaller subexpressions. Let _E_1 and _E_2 be relational-algebra expressions. Then, the fol- lowing are all relational-algebra expressions:

• _E_1 ∪ _E_2

• _E_1 − _E_2

• _E_1 × _E_2

• _P_ (_E_1), where _P_ is a predicate on attributes in _E_1

• _S_(_E_1), where _S_ is a list consisting of some of the attributes in _E_1

• _x_ (_E_1), where _x_ is the new name for the result of _E_1

**6.1.3 Additional Relational-Algebra Operations**

The fundamental operations of the relational algebra are sufficient to express any relational-algebra query. However, if we restrict ourselves to just the fundamental operations, certain common queries are lengthy to express. Therefore, we define additional operations that do not add any power to the algebra, but simplify  

**6.1 The Relational Algebra 229**

_course id_ CS-101

**Figure 6.13** Courses offered in both the Fall 2009 and Spring 2010 semesters.

common queries. For each new operation, we give an equivalent expression that uses only the fundamental operations.

**6.1.3.1 The Set-Intersection Operation**

The first additional relational-algebra operation that we shall define is **set inter- section** (∩). Suppose that we wish to find the set of all courses taught in both the Fall 2009 and the Spring 2010 semesters. Using set intersection, we can write

_course id_ (_semester_ \= “Fall” ∧ _year_\=2009 (_section_)) ∩ _course id_ (_semester_ \= “Spring” ∧ _year_\=2010 (_section_))

The result relation for this query appears in Figure 6.13. Note that we can rewrite any relational-algebra expression that uses set in-

tersection by replacing the intersection operation with a pair of set-difference operations as:

_r_ ∩ _s_ \= _r_ − (_r_ − _s_)

Thus, set intersection is not a fundamental operation and does not add any power to the relational algebra. It is simply more convenient to write _r_ ∩ _s_ than to write _r_ − (_r_ − _s_).

**6.1.3.2 The Natural-Join Operation**

It is often desirable to simplify certain queries that require a Cartesian product. Usually, a query that involves a Cartesian product includes a selection opera- tion on the result of the Cartesian product. The selection operation most often requires that all attributes that are common to the relations that are involved in the Cartesian product be equated.

In our example query from Section 6.1.1.6 that combined information from the _instructor_ and _teaches_ tables, the matching condition required _instructor_._ID_ to be equal to _teaches_._ID_. These are the only attributes in the two relations that have the same name.

The _natural join_ is a binary operation that allows us to combine certain selec- tions and a Cartesian product into one operation. It is denoted by the **join** symbol . The natural-join operation forms a Cartesian product of its two arguments, performs a selection forcing equality on those attributes that appear in both rela- tion schemas, and finally removes duplicate attributes. Returning to the example of the relations _instructor_ and _teaches_, computing _instructor_ **natural join** _teaches_ considers only those pairs of tuples where both the tuple from _instructor_ and the  

**230 Chapter 6 Formal Relational Query Languages**

_ID name dept name salary course id sec id semester year_ 10101 Srinivasan Comp. Sci. 65000 CS-101 1 Fall 2009 10101 Srinivasan Comp. Sci. 65000 CS-315 1 Spring 2010 10101 Srinivasan Comp. Sci. 65000 CS-347 1 Fall 2009 12121 Wu Finance 90000 FIN-201 1 Spring 2010 15151 Mozart Music 40000 MU-199 1 Spring 2010 22222 Einstein Physics 95000 PHY-101 1 Fall 2009 32343 El Said History 60000 HIS-351 1 Spring 2010 45565 Katz Comp. Sci. 75000 CS-101 1 Spring 2010 45565 Katz Comp. Sci. 75000 CS-319 1 Spring 2010 76766 Crick Biology 72000 BIO-101 1 Summer 2009 76766 Crick Biology 72000 BIO-301 1 Summer 2010 83821 Brandt Comp. Sci. 92000 CS-190 1 Spring 2009 83821 Brandt Comp. Sci. 92000 CS-190 2 Spring 2009 83821 Brandt Comp. Sci. 92000 CS-319 2 Spring 2010 98345 Kim Elec. Eng. 80000 EE-181 1 Spring 2009

**Figure 6.14** The natural join of the _instructor_ relation with the _teaches_ relation.

tuple from _teaches_ have the same value on the common attribute _ID_. The result relation, shown in Figure 6.14, has only 13 tuples, the ones that give information about an instructor and a course that that instructor actually teaches. Notice that we do not repeat those attributes that appear in the schemas of both relations; rather they appear only once. Notice also the order in which the attributes are listed: first the attributes common to the schemas of both relations, second those attributes unique to the schema of the first relation, and finally, those attributes unique to the schema of the second relation.

Although the definition of natural join is complicated, the operation is easy to apply. As an illustration, consider again the example “Find the names of all instructors together with the _course id_ of all courses they taught.” We express this query by using the natural join as follows:

_name, course id_ (_instructor  teaches_)

Since the schemas for _instructor_ and _teaches_ have the attribute _ID_ in common, the natural-join operation considers only pairs of tuples that have the same value on _ID_. It combines each such pair of tuples into a single tuple on the union of the two schemas; that is, (_ID_, _name_, _dept name_, _salary_, _course id_). After performing the projection, we obtain the relation in Figure 6.15.

Consider two relation schemas _R_ and _S_—which are, of course, lists of attribute names. If we consider the schemas to be _sets_, rather than lists, we can denote those attribute names that appear in both _R_ and _S_ by _R_ ∩ _S_, and denote those attribute names that appear in _R_, in _S_, or in both by _R_ ∪ _S_. Similarly, those attribute names that appear in _R_ but not _S_ are denoted by _R_ − _S_, whereas _S_ − _R_ denotes those  

**6.1 The Relational Algebra 231**

_name course id_ Srinivasan CS-101 Srinivasan CS-315 Srinivasan CS-347 Wu FIN-201 Mozart MU-199 Einstein PHY-101 El Said HIS-351 Katz CS-101 Katz CS-319 Crick BIO-101 Crick BIO-301 Brandt CS-190 Brandt CS-319 Kim EE-181

**Figure 6.15** Result of _name, course id_ (_instructor  teaches_ ).

attribute names that appear in _S_ but not in _R_. Note that the union, intersection, and difference operations here are on sets of attributes, rather than on relations.

We are now ready for a formal definition of the natural join. Consider two relations _r_ (_R_) and _s_(_S_). The **natural join** of _r_ and _s_, denoted by _r  s_, is a relation on schema _R_ ∪ _S_ formally defined as follows:

_r  s_ \= _R_ ∪ _S_ (_r.A_1 = _s.A_1 ∧ _r.A_2 = _s.A_2 ∧ _..._ ∧ _r.An_ \= _s.An_ (_r_ × _s_))

where _R_ ∩ _S_ \= _{A_1_, A_2_, . . . , An}_. Please note that if _r_ (_R_) and _s_(_S_) are relations without any attributes in com-

mon, that is, _R_ ∩ _S_ \= ∅, then _r  s_ \= _r_ × _s_. Let us consider one more example of the use of natural join, to write the query

“Find the names of all instructors in the Comp. Sci. department together with the course titles of all the courses that the instructors teach.”

_name,title_ (_dept name_ \= “Comp. Sci.” (_instructor  teaches  course_))

The result relation for this query appears in Figure 6.16. Notice that we wrote _instructor  teaches  course_ without inserting parenthe-

ses to specify the order in which the natural-join operations on the three relations should be executed. In the preceding case, there are two possibilities:

(_instructor  teaches_) _course instructor_ (_teaches  course_)

We did not specify which expression we intended, because the two are equivalent. That is, the natural join is **associative**.  

**232 Chapter 6 Formal Relational Query Languages**

_name title_ Brandt Game Design Brandt Image Processing Katz Image Processing Katz Intro. to Computer Science Srinivasan Intro. to Computer Science Srinivasan Robotics Srinivasan Database System Concepts

**Figure 6.16** Result of _name,title_ (_dept name_ \= “Comp. Sci.” (_instructor  teaches  course_ )).

The _theta join_ operation is a variant of the natural-join operation that allows us to combine a selection and a Cartesian product into a single operation. Consider relations _r_ (_R_) and _s_(_S_), and let  be a predicate on attributes in the schema _R_ ∪ _S_. The **theta join** operation _r _ _s_ is defined as follows:

_r _ _s_ \= (_r_ × _s_)

**6.1.3.3 The Assignment Operation**

It is convenient at times to write a relational-algebra expression by assigning parts of it to temporary relation variables. The **assignment** operation, denoted by ←, works like assignment in a programming language. To illustrate this operation, consider the definition of the natural-join operation. We could write _r  s_ as:

_temp_1 ← _R_ × _S temp_2 ← _r.A_1 = _s.A_1 ∧ _r.A_2 = _s.A_2 ∧ _..._ ∧ _r.An_ \= _s.An_ (_temp_1) _result_ \= _R_ ∪ _S_ (_temp_2)

The evaluation of an assignment does not result in any relation being displayed to the user. Rather, the result of the expression to the right of the ← is assigned to the relation variable on the left of the ←. This relation variable may be used in subsequent expressions.

With the assignment operation, a query can be written as a sequential program consisting of a series of assignments followed by an expression whose value is displayed as the result of the query. For relational-algebra queries, assignment must always be made to a temporary relation variable. Assignments to permanent relations constitute a database modification. Note that the assignment operation does not provide any additional power to the algebra. It is, however, a convenient way to express complex queries.

**6.1.3.4 Outer join Operations**

The **outer-join** operation is an extension of the join operation to deal with missing information. Suppose that there is some instructor who teaches no courses. Then  

**6.1 The Relational Algebra 233**

_ID name dept name salary course id sec id semester year_ 10101 Srinivasan Comp. Sci. 65000 CS-101 1 Fall 2009 10101 Srinivasan Comp. Sci. 65000 CS-315 1 Spring 2010 10101 Srinivasan Comp. Sci. 65000 CS-347 1 Fall 2009 12121 Wu Finance 90000 FIN-201 1 Spring 2010 15151 Mozart Music 40000 MU-199 1 Spring 2010 22222 Einstein Physics 95000 PHY-101 1 Fall 2009 32343 El Said History 60000 HIS-351 1 Spring 2010 33456 Gold Physics 87000 _null null null null_ 45565 Katz Comp. Sci. 75000 CS-101 1 Spring 2010 45565 Katz Comp. Sci. 75000 CS-319 1 Spring 2010 58583 Califieri History 62000 _null null null null_ 76543 Singh Finance 80000 _null null null null_ 76766 Crick Biology 72000 BIO-101 1 Summer 2009 76766 Crick Biology 72000 BIO-301 1 Summer 2010 83821 Brandt Comp. Sci. 92000 CS-190 1 Spring 2009 83821 Brandt Comp. Sci. 92000 CS-190 2 Spring 2009 83821 Brandt Comp. Sci. 92000 CS-319 2 Spring 2010 98345 Kim Elec. Eng. 80000 EE-181 1 Spring 2009

**Figure 6.17** Result of _instructor  teaches_ .

the tuple in the _instructor_ relation (Figure 6.1) for that particular instructor would not satisfy the condition of a natural join with the _teaches_ relation (Figure 6.7) and that instructor’s data would not appear in the result of the natural join, shown in Figure 6.14. For example, instructors Califieri, Gold, and Singh do not appear in the result of the natural join, since they do not teach any course.

More generally, some tuples in either or both of the relations being joined may be “lost” in this way. The **outer join** operation works in a manner similar to the natural join operation we have already studied, but preserves those tuples that would be lost in an join by creating tuples in the result containing null values.

We can use the _outer-join_ operation to avoid this loss of information. There are actually three forms of the operation: _left outer join_, denoted ; _right outer join_, denoted  ; and _full outer join_, denoted  . All three forms of outer join compute the join, and add extra tuples to the result of the join. For example, the results of the expression _instructor  teaches_ and _teaches  instructor_ appear in Figures 6.17 and 6.18, respectively.

The **left outer join** ( ) takes all tuples in the left relation that did not match with any tuple in the right relation, pads the tuples with null values for all other attributes from the right relation, and adds them to the result of the natural join. In Figure 6.17, tuple (58583, Califieri, History, 62000, _null_, _null_, _null_, _null_), is such a tuple. All information from the left relation is present in the result of the left outer join.  

**234 Chapter 6 Formal Relational Query Languages**

_ID course id sec id semester year name dept name salary_ 10101 CS-101 1 Fall 2009 Srinivasan Comp. Sci. 65000 10101 CS-315 1 Spring 2010 Srinivasan Comp. Sci. 65000 10101 CS-347 1 Fall 2009 Srinivasan Comp. Sci. 65000 12121 FIN-201 1 Spring 2010 Wu Finance 90000 15151 MU-199 1 Spring 2010 Mozart Music 40000 22222 PHY-101 1 Fall 2009 Einstein Physics 95000 32343 HIS-351 1 Spring 2010 El Said History 60000 33456 _null null null null_ Gold Physics 87000 45565 CS-101 1 Spring 2010 Katz Comp. Sci. 75000 45565 CS-319 1 Spring 2010 Katz Comp. Sci. 75000 58583 _null null null null_ Califieri History 62000 76543 _null null null null_ Singh Finance 80000 76766 BIO-101 1 Summer 2009 Crick Biology 72000 76766 BIO-301 1 Summer 2010 Crick Biology 72000 83821 CS-190 1 Spring 2009 Brandt Comp. Sci. 92000 83821 CS-190 2 Spring 2009 Brandt Comp. Sci. 92000 83821 CS-319 2 Spring 2010 Brandt Comp. Sci. 92000 98345 EE-181 1 Spring 2009 Kim Elec. Eng. 80000

**Figure 6.18** Result of _teaches  instructor_ .

The **right outer join** ( ) is symmetric with the left outer join: It pads tuples from the right relation that did not match any from the left relation with nulls and adds them to the result of the natural join. In Figure 6.18, tuple (58583, _null_, _null_, _null_, _null_, Califieri, History, 62000), is such a tuple. Thus, all information from the right relation is present in the result of the right outer join.

The **full outer join**(  ) does both the left and right outer join operations, padding tuples from the left relation that did not match any from the right relation, as well as tuples from the right relation that did not match any from the left relation, and adding them to the result of the join.

Note that in going from our left-outer-join example to our right-outer-join ex- ample, we chose to swap the order of the operands. Thus both examples preserve tuples from the _instructor_ relation, and thus contain the same information. In our example relations, _teaches_ tuples always have matching _instructor_ tuples, and thus _teaches  instructor_ would give the same result as _teaches  instructor_ . If there were tuples in _teaches_ without matching tuples in _instructor_, such tu- ples would appear padded with nulls in _teaches  instructor_ as well as in _teaches  instructor_ . Further examples of outer joins (expressed in SQL syntax) may be found in Section 4.1.2.

Since outer-join operations may generate results containing null values, we need to specify how the different relational-algebra operations deal with null values. Section 3.6 dealt with this issue in the context of SQL. The same concepts apply for the case of relational algebra, and we omit details.  

**6.1 The Relational Algebra 235**

It is interesting to note that the outer-join operations can be expressed by the basic relational-algebra operations. For instance, the left outer join operation, _r  s_, can be written as:

(_r  s_) ∪ (_r_ − _R_(_r  s_)) × {(_null, . . . , null_)}

where the constant relation {(_null, . . . , null_)} is on the schema _S_ − _R_.

**6.1.4 Extended Relational-Algebra Operations**

We now describe relational-algebra operations that provide the ability to write queries that cannot be expressed using the basic relational-algebra operations. These operations are called **extended relational-algebra** operations.

**6.1.4.1 Generalized Projection**

The first operation is the **generalized-projection** operation, which extends the projection operation by allowing operations such as arithmetic and string func- tions to be used in the projection list. The generalized-projection operation has the form:

_F_1_,F_2_,...,Fn_(_E_)

where _E_ is any relational-algebra expression, and each of _F_1_, F_2_, . . . , Fn_ is an arithmetic expression involving constants and attributes in the schema of _E_ . As a base case, the expression may be simply an attribute or a constant. In general, an expression can use arithmetic operations such as +_,_ −_,_ ∗, and ÷ on numeric valued attributes, numeric constants, and on expressions that generate a numeric result. Generalized projection also permits operations on other data types, such as concatenation of strings.

For example, the expression:

_ID,name,dept name,salary_÷12(_instructor_ )

gives the _ID_, _name_, _dept name_, and the monthly salary of each instructor.

**6.1.4.2 Aggregation**

The second extended relational-algebra operation is the aggregate operation _G_, which permits the use of aggregate functions such as min or average, on sets of values.

**Aggregate functions** take a collection of values and return a single value as a result. For example, the aggregate function **sum** takes a collection of values and returns the sum of the values. Thus, the function **sum** applied on the collection:

{1_,_ 1_,_ 3_,_ 4_,_ 4_,_ 11}  

**236 Chapter 6 Formal Relational Query Languages**

returns the value 24. The aggregate function **avg** returns the average of the values. When applied to the preceding collection, it returns the value 4. The aggregate function **count** returns the number of the elements in the collection, and returns 6 on the preceding collection. Other common aggregate functions include **min** and **max**, which return the minimum and maximum values in a collection; they return 1 and 11, respectively, on the preceding collection.

The collections on which aggregate functions operate can have multiple oc- currences of a value; the order in which the values appear is not relevant. Such collections are called **multisets**. Sets are a special case of multisets where there is only one copy of each element.

To illustrate the concept of aggregation, we shall use the _instructor_ relation. Suppose that we want to find out the sum of salaries of all instructors; the relational-algebra expression for this query is:

_G_**sum**(_salar y_)(_instructor_ )

The symbol _G_ is the letter G in calligraphic font; read it as “calligraphic G.” The relational-algebra operation _G_ signifies that aggregation is to be applied, and its subscript specifies the aggregate operation to be applied. The result of the expression above is a relation with a single attribute, containing a single row with a numerical value corresponding to the sum of the salaries of all instructors.

There are cases where we must eliminate multiple occurrences of a value before computing an aggregate function. If we do want to eliminate duplicates, we use the same function names as before, with the addition of the hyphenated string “**distinct**” appended to the end of the function name (for example, **count- distinct**). An example arises in the query “Find the total number of instructors who teach a course in the Spring 2010 semester.” In this case, an instructor counts only once, regardless of the number of course sections that the instructor teaches. The required information is contained in the relation _teaches_, and we write this query as follows:

_G_**count**−**distinct**(_ID_)(_semester_\=“Spring”∧_year_ \=2010(_teaches_))

The aggregate function **count-distinct** ensures that even if an instructor teaches more than one course, she is counted only once in the result.

There are circumstances where we would like to apply the aggregate function not to a single set of tuples, but instead to a group of sets of tuples. As an illustration, consider the query “Find the average salary in each department.” We write this query as follows:

_dept nameG_**average**(_salary_)(_instructor_ )

Figure 6.19 shows the tuples in the _instructor_ relation grouped by the _dept name_ attribute. This is the first step in computing the query result. The specified

aggregate is computed for each group, and the result of the query is shown in Figure 6.20.  

**6.1 The Relational Algebra 237**

_ID name dept name salary_ 76766 Crick Biology 72000 45565 Katz Comp. Sci. 75000 10101 Srinivasan Comp. Sci. 65000 83821 Brandt Comp. Sci. 92000 98345 Kim Elec. Eng. 80000 12121 Wu Finance 90000 76543 Singh Finance 80000 32343 El Said History 60000 58583 Califieri History 62000 15151 Mozart Music 40000 33456 Gold Physics 87000 22222 Einstein Physics 95000

**Figure 6.19** Tuples of the _instructor_ relation, grouped by the _dept name_ attribute

In contrast, consider the query “Find the average salary of all instructors.” We write this query as follows:

_G_**average**(_salary_)(_instructor_ )

In this case the attribute _dept name_ has been omitted from the left side of the _G_ operator, so the entire relation is treated as a single group.

The general form of the **aggregation operation** _G_ is as follows:

_G_1_,G_2_,...,GnGF_1(_A_1)_, F_2(_A_2)_,..., Fm_(_Am_)(_E_)

where _E_ is any relational-algebra expression; _G_1_, G_2_, . . . , Gn_ constitute a list of attributes on which to group; each _Fi_ is an aggregate function; and each _Ai_ is an attribute name. The meaning of the operation is as follows: The tuples in the result of expression _E_ are partitioned into groups in such a way that:

_dept name salary_ Biology 72000 Comp. Sci. 77333 Elec. Eng. 80000 Finance 85000 History 61000 Music 40000 Physics 91000

**Figure 6.20** The result relation for the query “Find the average salary in each department”.  

**238 Chapter 6 Formal Relational Query Languages**

**MULTISET RELATIONAL ALGEBRA**

Unlike the relational algebra, SQL allows multiple copies of a tuple in an input relation as well as in a query result. The SQL standard defines how many copies of each tuple are there in the output of a query, which depends in turn on how many copies of tuples are present in the input relations.

To model this behavior of SQL, a version of relational algebra, called the **multiset relational algebra**, is defined to work on multisets, that is, sets that may contain duplicates. The basic operations in the multiset relational algebra are defined as follows:

**1\.** If there are _c_1 copies of tuple _t_1 in _r_1, and _t_1 satisfies selection  , then there are _c_1 copies of _t_1 in (_r_1).

**2\.** For each copy of tuple _t_1 in _r_1, there is a copy of tuple _A_(_t_1) in _A_(_r_1), where _A_(_t_1) denotes the projection of the single tuple _t_1.

**3\.** If there are _c_1 copies of tuple _t_1 in _r_1 and _c_2 copies of tuple _t_2 in _r_2, there are _c_1 ∗ _c_2 copies of the tuple _t_1_.t_2 in _r_1 × _r_2.

For example, suppose that relations _r_1 with schema (_A, B_) and _r_2 with schema (_C_) are the following multisets:

_r_1 = {(1_, a_)_,_ (2_, a_)} _r_2 = {(2)_,_ (3)_,_ (3)}

Then _B_(_r_1) would be {(_a_)_,_ (_a_)}, whereas _B_(_r_1) × _r_2 would be:

{(_a,_ 2)_,_ (_a,_ 2)_,_ (_a,_ 3)_,_ (_a,_ 3)_,_ (_a,_ 3)_,_ (_a,_ 3)}

Multiset union, intersection and set difference can also be defined in a similar way, following the corresponding definitions in SQL, which we saw in Sec- tion 3.5. There is no change in the definition of the aggregation operation.

**1\.** All tuples in a group have the same values for _G_1_, G_2_, . . . , Gn_.

**2\.** Tuples in different groups have different values for _G_1_, G_2_, . . . , Gn_.

Thus, the groups can be identified by the values of attributes _G_1_, G_2_, . . . , Gn_. For each group (_g_1_, g_2_, . . . , gn_), the result has a tuple (_g_1_, g_2_, . . . , gn, a_1_, a_2_, . . . , am_) where, for each _i_ , _ai_ is the result of applying the aggregate function _Fi_ on the multiset of values for attribute _Ai_ in the group.

As a special case of the aggregate operation, the list of attributes _G_1_, G_2_, . . . , Gn_ can be empty, in which case there is a single group containing all tuples in the relation. This corresponds to aggregation without grouping.  

**6.2 The Tuple Relational Calculus 239**

**SQL AND RELATIONAL ALGEBRA**

From a comparison of the relational algebra operations and the SQL operations, it should be clear that there is a close connection between the two. A typical SQL query has the form:

**select** _A_1_, A_2_, . . . , An_

**from** _r_1_, r_2_, . . . , rm_

**where** _P_

Each _Ai_ represents an attribute, and each _ri_ a relation. _P_ is a predicate. The query is equivalent to the multiset relational-algebra expression:

_A_1_, A_2_,...,An_ (_P_ (_r_1 × _r_2 × · · · × _rm_))

If the **where** clause is omitted, the predicate _P_ is **true**. More complex SQL queries can also be rewritten in relational algebra. For

example, the query:

**select** _A_1_, A_2, **sum**(_A_3) **from** _r_1_, r_2_, . . . , rm_

**where** _P_ **group by** _A_1_, A_2

is equivalent to:

_A_1_, A_2_G_sum(_A_3)(_A_1_, A_2_,..., An_(_P_ (_r_1 × _r_2 × · · · × _rm_)))

Join expressions in the **from** clause can be written using equivalent join expres- sions in relational algebra; we leave the details as an exercise for the reader. However, subqueries in the **where** or **select** clause cannot be rewritten into relational algebra in such a straightforward manner, since there is no relational- algebra operation equivalent to the subquery construct. Extensions of relational algebra have been proposed for this task, but are beyond the scope of this book.

**6.2 The Tuple Relational Calculus**

When we write a relational-algebra expression, we provide a sequence of proce- dures that generates the answer to our query. The tuple relational calculus, by contrast, is a **nonprocedural** query language. It describes the desired information without giving a specific procedure for obtaining that information.

A query in the tuple relational calculus is expressed as:

{_t_ | _P_(_t_)}  

**240 Chapter 6 Formal Relational Query Languages**

That is, it is the set of all tuples _t_ such that predicate _P_ is true for _t_. Following our earlier notation, we use _t_\[_A_\] to denote the value of tuple _t_ on attribute _A_, and we use _t_ ∈ _r_ to denote that tuple _t_ is in relation _r_.

Before we give a formal definition of the tuple relational calculus, we re- turn to some of the queries for which we wrote relational-algebra expressions in Section 6.1.1.

**6.2.1 Example Queries**

Find the _ID_, _name_, _dept name_, _salary_ for instructors whose salary is greater than $80,000:

{_t_ | _t_ ∈ _instructor_ ∧ _t_\[_salary_\] _\>_ 80000}

Suppose that we want only the _ID_ attribute, rather than all attributes of the _instructor_ relation. To write this query in the tuple relational calculus, we need to write an expression for a relation on the schema (_ID_). We need those tuples on (_ID_) such that there is a tuple in _instructor_ with the _salary_ attribute _\>_ 80000\. To express this request, we need the construct “there exists” from mathematical logic. The notation:

∃ _t_ ∈ _r_ (_Q_(_t_))

means “there exists a tuple _t_ in relation _r_ such that predicate _Q_(_t_) is true.” Using this notation, we can write the query “Find the instructor _ID_ for each

instructor with a salary greater than $80,000” as:

{_t_ | ∃ _s_ ∈ _instructor_ (_t_\[_ID_\] = _s_\[_ID_\] ∧ _s_\[_salary_\] _\>_ 80000)}

In English, we read the preceding expression as “The set of all tuples _t_ such that there exists a tuple _s_ in relation _instructor_ for which the values of _t_ and _s_ for the _ID_ attribute are equal, and the value of _s_ for the _salary_ attribute is greater than $80,000.”

Tuple variable _t_ is defined on only the _ID_ attribute, since that is the only attribute having a condition specified for _t_. Thus, the result is a relation on (_ID_).

Consider the query “Find the names of all instructors whose department is in the Watson building.” This query is slightly more complex than the previous queries, since it involves two relations: _instructor_ and _department_. As we shall see, however, all it requires is that we have two “there exists” clauses in our tuple-relational-calculus expression, connected by _and_ (∧). We write the query as follows:

{_t_ | ∃ _s_ ∈ _instructor_ (_t_\[_name_\] = _s_\[_name_\] ∧ ∃ _u_ ∈ _department_ (_u_\[_dept name_\] = _s_\[_dept name_\]

∧ _u_\[_building_\] = “Watson”))}  

**6.2 The Tuple Relational Calculus 241**

_name_ Einstein Crick Gold

**Figure 6.21** Names of all instructors whose department is in the Watson building.

Tuple variable _u_ is restricted to departments that are located in the Watson build- ing, while tuple variable _s_ is restricted to instructors whose _dept name_ matches that of tuple variable _u_. Figure 6.21 shows the result of this query.

To find the set of all courses taught in the Fall 2009 semester, the Spring 2010 semester, or both, we used the union operation in the relational algebra. In the tuple relational calculus, we shall need two “there exists” clauses, connected by _or_ (∨):

{_t_ | ∃ _s_ ∈ _section_ (_t_\[_course id_ \] = _s_\[_course id_ \]) ∧ _s_\[_semester_ \] = “Fall” ∧ _s_\[_year_ \] = 2009)}

∨ ∃ _u_ ∈ _section_ (_u_\[_course id_ \] = _t_\[_course id_ \])} ∧ _u_\[_semester_ \] = “Spring” ∧ _u_\[_year_ \] = 2010)}

This expression gives us the set of all _course id_ tuples for which at least one of the following holds:

• The _course id_ appears in some tuple of the _section_ relation with _semester_ \= Fall and _year_ \= 2009.

• The _course id_ appears in some tuple of the _section_ relation with _semester_ \= Spring and _year_ \= 2010.

If the same course is offered in both the Fall 2009 and Spring 2010 semesters, its _course id_ appears only once in the result, because the mathematical definition of a set does not allow duplicate members. The result of this query appeared earlier in Figure 6.5.

If we now want _only_ those _course id_ values for courses that are offered in _both_ the Fall 2009 and Spring 2010 semesters, all we need to do is to change the _or_ (∨) to _and_ (∧) in the preceding expression.

{_t_ | ∃ _s_ ∈ _section_ (_t_\[_course id_ \] = _s_\[_course id_ \]) ∧ _s_\[_semester_ \] = “Fall” ∧ _s_\[_year_ \] = 2009)}

∧ ∃ _u_ ∈ _section_ (_u_\[_course id_ \] = _t_\[_course id_ \])} ∧ _u_\[_semester_ \] = “Spring” ∧ _u_\[_year_ \] = 2010)}

The result of this query appeared in Figure 6.13. Now consider the query “Find all the courses taught in the Fall 2009 semester

but not in Spring 2010 semester.” The tuple-relational-calculus expression for this  

**242 Chapter 6 Formal Relational Query Languages**

query is similar to the expressions that we have just seen, except for the use of the _not_ (¬) symbol:

{_t_ | ∃ _s_ ∈ _section_ (_t_\[_course id_ \] = _s_\[_course id_ \]) ∧ _s_\[_semester_ \] = “Fall” ∧ _s_\[_year_ \] = 2009)}

∧ ¬ ∃ _u_ ∈ _section_ (_u_\[_course id_ \] = _t_\[_course id_ \])} ∧ _u_\[_semester_ \] = “Spring” ∧ _u_\[_year_ \] = 2010)}

This tuple-relational-calculus expression uses the ∃_s_ ∈ _section_ (_. . ._) clause to require that a particular _course id_ is taught in the Fall 2009 semester, and it uses the ¬ ∃ _u_ ∈ _section_ (_. . ._) clause to eliminate those _course id_ values that appear in some tuple of the _section_ relation as having been taught in the Spring 2010 semester.

The query that we shall consider next uses implication, denoted by ⇒. The formula _P_ ⇒ _Q_ means “_P_ implies _Q_”; that is, “if _P_ is true, then _Q_ must be true.” Note that _P_ ⇒ _Q_ is logically equivalent to ¬_P_ ∨ _Q_. The use of implication rather than _not_ and _or_ often suggests a more intuitive interpretation of a query in English.

Consider the query that “Find all students who have taken all courses offered in the Biology department.” To write this query in the tuple relational calculus, we introduce the “for all” construct, denoted by ∀. The notation:

∀ _t_ ∈ _r_ (_Q_(_t_))

means “_Q_ is true for all tuples _t_ in relation _r_.” We write the expression for our query as follows:

{_t_ | ∃ _r_ ∈ _student_ (_r_ \[_ID_\] = _t_\[_ID_\]) ∧ ( ∀ _u_ ∈ _course_ (_u_\[_dept name_\] = “ Biology” ⇒

∃ _s_ ∈ _takes_ (_t_\[_ID_\] = _s_\[_ID_\] ∧ _s_\[_course id_ \] = _u_\[_course id_ \]))}

In English, we interpret this expression as “The set of all students (that is, (_ID_) tuples _t_) such that, for _all_ tuples _u_ in the _course_ relation, if the value of _u_ on attribute _dept name_ is ’Biology’, then there exists a tuple in the _takes_ relation that includes the student _ID_ and the _course id_.”

Note that there is a subtlety in the above query: If there is no course offered in the Biology department, all student _ID_s satisfy the condition. The first line of the query expression is critical in this case—without the condition

∃ _r_ ∈ _student_ (_r_ \[_ID_\] = _t_\[_ID_\])

if there is no course offered in the Biology department, any value of _t_ (including values that are not student _ID_s in the _student_ relation) would qualify.  

**6.2 The Tuple Relational Calculus 243**

**6.2.2 Formal Definition**

We are now ready for a formal definition. A tuple-relational-calculus expression is of the form:

{_t_|_P_ (_t_)}

where _P_ is a _formula_. Several tuple variables may appear in a formula. A tuple variable is said to be a _free variable_ unless it is quantified by a ∃ or ∀. Thus, in:

_t_ ∈ _instructor_ ∧ ∃ _s_ ∈ _department_(_t_\[_dept name_\] = _s_\[_dept name_\])

_t_ is a free variable. Tuple variable _s_ is said to be a _bound_ variable. A tuple-relational-calculus formula is built up out of _atoms_. An atom has one

of the following forms:

• _s_ ∈ _r_, where _s_ is a tuple variable and _r_ is a relation (we do not allow use of the _/_∈ operator).

• _s_\[_x_\] _ u_\[_y_\], where _s_ and _u_ are tuple variables, _x_ is an attribute on which _s_ is defined, _y_ is an attribute on which _u_ is defined, and  is a comparison operator (_<_, ≤, =, =, _\>_, ≥); we require that attributes _x_ and _y_ have domains whose members can be compared by .

• _s_\[_x_\] _ c_, where _s_ is a tuple variable, _x_ is an attribute on which _s_ is defined, 

is a comparison operator, and _c_ is a constant in the domain of attribute _x_.

We build up formulae from atoms by using the following rules:

• An atom is a formula.

• If _P_1 is a formula, then so are ¬_P_1 and (_P_1).

• If _P_1 and _P_2 are formulae, then so are _P_1 ∨ _P_2, _P_1 ∧ _P_2, and _P_1 ⇒ _P_2.

• If _P_1(_s_) is a formula containing a free tuple variable _s_, and _r_ is a relation, then

∃ _s_ ∈ _r_ (_P_1(_s_)) and ∀ _s_ ∈ _r_ (_P_1(_s_))

are also formulae.

As we could for the relational algebra, we can write equivalent expressions that are not identical in appearance. In the tuple relational calculus, these equiv- alences include the following three rules:

**1\.** _P_1 ∧ _P_2 is equivalent to ¬ (¬(_P_1) ∨ ¬(_P_2)).

**2\.** ∀ _t_ ∈ _r_ (_P_1(_t_)) is equivalent to ¬ ∃ _t_ ∈ _r_ (¬_P_1(_t_)).

**3\.** _P_1 ⇒ _P_2 is equivalent to ¬(_P_1) ∨ _P_2.  

**244 Chapter 6 Formal Relational Query Languages**

**6.2.3 Safety of Expressions**

There is one final issue to be addressed. A tuple-relational-calculus expression may generate an infinite relation. Suppose that we write the expression:

{_t_ |¬ (_t_ ∈ _instructor_ )}

There are infinitely many tuples that are not in _instructor_. Most of these tuples contain values that do not even appear in the database! Clearly, we do not wish to allow such expressions.

To help us define a restriction of the tuple relational calculus, we introduce the concept of the **domain** of a tuple relational formula, _P_. Intuitively, the domain of _P_, denoted _dom_(_P_), is the set of all values referenced by _P_. They include values mentioned in _P_ itself, as well as values that appear in a tuple of a relation mentioned in _P_. Thus, the domain of _P_ is the set of all values that appear explicitly in _P_ or that appear in one or more relations whose names appear in _P_. For example, _dom_(_t_ ∈ _instructor_ ∧ _t_\[_salary_\] _\>_ 80000) is the set containing 80000 as well as the set of all values appearing in any attribute of any tuple in the _instructor_ relation. Similarly, _dom_(¬ (_t_ ∈ _instructor_ )) is also the set of all values appearing in _instructor_, since the relation _instructor_ is mentioned in the expression.

We say that an expression {_t_ | _P_(_t_)} is _safe_ if all values that appear in the result are values from _dom_(_P_). The expression {_t_ |¬ (_t_ ∈ _instructor_ )} is not safe. Note that _dom_(¬ (_t_ ∈ _instructor_ )) is the set of all values appearing in _instructor_. However, it is possible to have a tuple _t_ not in _instructor_ that contains values that do not appear in _instructor_. The other examples of tuple-relational-calculus expressions that we have written in this section are safe.

The number of tuples that satisfy an unsafe expression, such as {_t_ |¬ (_t_ ∈ _instructor_ )}, could be infinite, whereas safe expressions are guaranteed to have finite results. The class of tuple-relational-calculus expressions that are allowed is therefore restricted to those that are safe.

**6.2.4 Expressive Power of Languages**

The tuple relational calculus restricted to safe expressions is equivalent in expres- sive power to the basic relational algebra (with the operators ∪_,_ −_,_ ×_,_ , and  , but without the extended relational operations such as generalized projection and ag- gregation (_G_)). Thus, for every relational-algebra expression using only the basic operations, there is an equivalent expression in the tuple relational calculus, and for every tuple-relational-calculus expression, there is an equivalent relational- algebra expression. We shall not prove this assertion here; the bibliographic notes contain references to the proof. Some parts of the proof are included in the exer- cises. We note that the tuple relational calculus does not have any equivalent of the aggregate operation, but it can be extended to support aggregation. Extending the tuple relational calculus to handle arithmetic expressions is straightforward.  

**6.3 The Domain Relational Calculus 245**

**6.3 The Domain Relational Calculus**

A second form of relational calculus, called **domain relational calculus**, uses _domain_ variables that take on values from an attributes domain, rather than values for an entire tuple. The domain relational calculus, however, is closely related to the tuple relational calculus.

Domain relational calculus serves as the theoretical basis of the widely used QBE language (see Appendix B.1), just as relational algebra serves as the basis for the SQL language.

**6.3.1 Formal Definition**

An expression in the domain relational calculus is of the form

{_< x_1_, x_2_, . . . , xn >_ | _P_(_x_1_, x_2_, . . . , xn_)}

where _x_1_, x_2_, . . . , xn_ represent domain variables. _P_ represents a formula composed of atoms, as was the case in the tuple relational calculus. An atom in the domain relational calculus has one of the following forms:

• _< x_1_, x_2_, . . . , xn >_ ∈ _r,_ where _r_ is a relation on _n_ attributes and _x_1_, x_2_, . . . , xn_ are domain variables or domain constants.

• _x  y_, where _x_ and _y_ are domain variables and  is a comparison operator (_<_, ≤, =, =, _\>_, ≥). We require that attributes _x_ and _y_ have domains that can be compared by .

• _x  c_, where _x_ is a domain variable,  is a comparison operator, and _c_ is a constant in the domain of the attribute for which _x_ is a domain variable.

We build up formulae from atoms by using the following rules:

• An atom is a formula.

• If _P_1 is a formula, then so are ¬_P_1 and (_P_1).

• If _P_1 and _P_2 are formulae, then so are _P_1 ∨ _P_2, _P_1 ∧ _P_2, and _P_1 ⇒ _P_2.

• If _P_1(_x_) is a formula in _x_, where _x_ is a free domain variable, then

∃ _x_ (_P_1(_x_)) and ∀ _x_ (_P_1(_x_))

are also formulae.

As a notational shorthand, we write ∃ _a , b, c_ (_P_(_a , b, c_)) for ∃ _a_ (∃ _b_ (∃ _c_ (_P_(_a , b, c_)))).  

**246 Chapter 6 Formal Relational Query Languages**

**6.3.2 Example Queries**

We now give domain-relational-calculus queries for the examples that we con- sidered earlier. Note the similarity of these expressions and the corresponding tuple-relational-calculus expressions.

• Find the instructor _ID_, _name_, _dept name_, and _salary_ for instructors whose salary is greater than $80,000:

{_< i, n, d, s >_ | _< i, n, d, s >_ ∈ _instructor_ ∧ _s >_ 80000}

• Find all instructor _ID_ for instructors whose salary is greater than $80,000:

{_< n >_ | ∃ _i, d, s_ (_< i, n, d, s >_ ∈ _instructor_ ∧ _s >_ 80000)}

Although the second query appears similar to the one that we wrote for the tuple relational calculus, there is an important difference. In the tuple calculus, when we write ∃ _s_ for some tuple variable _s_, we bind it immediately to a relation by writing ∃ _s_ ∈ _r_ . However, when we write ∃ _n_ in the domain calculus, _n_ refers not to a tuple, but rather to a domain value. Thus, the domain of variable _n_ is unconstrained until the subformula _< i, n, d, s >_ ∈ _instructor_ constrains _n_ to instructor names that appear in the _instructor_ relation.

We now give several examples of queries in the domain relational calculus.

• Find the names of all instructors in the Physics department together with the _course id_ of all courses they teach:

{_< n, c >_ | ∃ _i, a_ (_< i, c, a , s, y >_ ∈ _teaches_ ∧ ∃ _d, s_ (_< i, n, d, s >_ ∈ _instructor_ ∧ _d_ \= “Physics”))}

• Find the set of all courses taught in the Fall 2009 semester, the Spring 2010 semester, or both:

{_< c >_ | ∃ _s_ (_< c, a , s, y, b, r, t >_∈ _section_ ∧ _s_ \= “Fall” ∧ _y_ \= “2009”

∨∃ _u_ (_< c, a , s, y, b, r, t >_∈ _section_ ∧ _s_ \= “Spring” ∧ _y_ \= “2010”

• Find all students who have taken all courses offered in the Biology depart- ment:

{_< i >_ | ∃ _n, d, t_ (_< i, n, d, t >_ ∈ _student_) ∧ ∀ _x, y, z, w_ (_< x, y, z, w >_ ∈ _course_ ∧ _z_ \= “Biology” ⇒ ∃ _a , b_ (_< a , x, b, r, p, q >_ ∈ _takes_ ∧ _< c, a >_ ∈ _depositor_ ))}  

**6.3 The Domain Relational Calculus 247**

Note that as was the case for tuple-relational-calculus, if no courses are offered in the Biology department, all students would be in the result.

**6.3.3 Safety of Expressions**

We noted that, in the tuple relational calculus (Section 6.2), it is possible to write expressions that may generate an infinite relation. That led us to define _safety_ for tuple-relational-calculus expressions. A similar situation arises for the domain relational calculus. An expression such as

{_< i, n, d, s >_ | ¬(_< i, n, d, s >_ ∈ _instructor_ )}

is unsafe, because it allows values in the result that are not in the domain of the expression.

For the domain relational calculus, we must be concerned also about the form of formulae within “there exists” and “for all” clauses. Consider the expression

{_< x >_ | ∃ _y_ (_< x, y >_∈ _r_ ) ∧ ∃ _z_ (¬(_< x, z >_∈ _r_ ) ∧ _P_(_x, z_))}

where _P_ is some formula involving _x_ and _z_. We can test the first part of the formula, ∃ _y_ (_< x, y >_ ∈ _r_ ), by considering only the values in _r_. However, to test the second part of the formula, ∃ _z_ (¬ (_< x, z >_ ∈ _r_ ) ∧ _P_(_x, z_)), we must consider values for _z_ that do not appear in _r_. Since all relations are finite, an infinite number of values do not appear in _r_. Thus, it is not possible, in general, to test the second part of the formula without considering an infinite number of potential values for _z_. Instead, we add restrictions to prohibit expressions such as the preceding one.

In the tuple relational calculus, we restricted any existentially quantified vari- able to range over a specific relation. Since we did not do so in the domain calculus, we add rules to the definition of safety to deal with cases like our example. We say that an expression

{_< x_1_, x_2_, . . . , xn >_ | _P_ (_x_1_, x_2_, . . . , xn_)}

is safe if all of the following hold:

**1\.** All values that appear in tuples of the expression are values from _dom_(_P_).

**2\.** For every “there exists” subformula of the form ∃ _x_ (_P_1(_x_)), the subformula is true if and only if there is a value _x_ in _dom_(_P_1) such that _P_1(_x_) is true.

**3\.** For every “for all” subformula of the form ∀_x_ (_P_1(_x_)), the subformula is true if and only if _P_1(_x_) is true for all values _x_ from _dom_(_P_1).

The purpose of the additional rules is to ensure that we can test “for all” and “there exists” subformulae without having to test infinitely many possibilities. Consider the second rule in the definition of safety. For ∃ _x_ (_P_1(_x_)) to be true,  

**248 Chapter 6 Formal Relational Query Languages**

we need to find only one _x_ for which _P_1(_x_) is true. In general, there would be infinitely many values to test. However, if the expression is safe, we know that we can restrict our attention to values from _dom_(_P_1). This restriction reduces to a finite number the tuples we must consider.

The situation for subformulae of the form ∀_x_ (_P_1(_x_)) is similar. To assert that ∀_x_ (_P_1(_x_)) is true, we must, in general, test all possible values, so we must examine infinitely many values. As before, if we know that the expression is safe, it is sufficient for us to test _P_1(_x_) for those values taken from _dom_(_P_1).

All the domain-relational-calculus expressions that we have written in the example queries of this section are safe, except for the example unsafe query we saw earlier.

**6.3.4 Expressive Power of Languages**

When the domain relational calculus is restricted to safe expressions, it is equiv- alent in expressive power to the tuple relational calculus restricted to safe ex- pressions. Since we noted earlier that the restricted tuple relational calculus is equivalent to the relational algebra, all three of the following are equivalent:

• The basic relational algebra (without the extended relational-algebra opera- tions)

• The tuple relational calculus restricted to safe expressions

• The domain relational calculus restricted to safe expressions

We note that the domain relational calculus also does not have any equivalent of the aggregate operation, but it can be extended to support aggregation, and extending it to handle arithmetic expressions is straightforward.

**6.4 Summary**

• The **relational algebra** defines a set of algebraic operations that operate on tables, and output tables as their results. These operations can be combined to get expressions that express desired queries. The algebra defines the basic operations used within relational query languages.

• The operations in relational algebra can be divided into:

◦ Basic operations

◦ Additional operations that can be expressed in terms of the basic opera- tions

◦ Extended operations, some of which add further expressive power to relational algebra

• The relational algebra is a terse, formal language that is inappropriate for casual users of a database system. Commercial database systems, therefore,  

**Practice Exercises 249**

use languages with more “syntactic sugar.” In Chapters 3 through 5, we cover the most influential language—**SQL**, which is based on relational algebra.

• The **tuple relational calculus** and the **domain relational calculus** are non- procedural languages that represent the basic power required in a relational query language. The basic relational algebra is a procedural language that is equivalent in power to both forms of the relational calculus when they are restricted to safe expressions.

• The relational calculi are terse, formal languages that are inappropriate for casual users of a database system. These two formal languages form the basis for two more user-friendly languages, QBE and Datalog, that we cover in Appendix B.

**Review Terms**

• Relational algebra • Relational-algebra operations

◦ Select 

◦ Project 

◦ Union ∪ ◦ Set difference − ◦ Cartesian product × ◦ Rename 

• Additional operations

◦ Set intersection ∩ ◦ Natural join 

◦ Assignment operation

◦ Outer join

 Left outer join 

 Right outer join 

 Full outer join 

• Multisets • Grouping • Null value • Tuple relational calculus • Domain relational calculus • Safety of expressions • Expressive power of languages

**Practice Exercises**

**6.1** Write the following queries in relational algebra, using the university schema.

a. Find the titles of courses in the Comp. Sci. department that have 3 credits.

b. Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result.

c. Find the highest salary of any instructor.

d. Find all instructors earning the highest salary (there may be more than one with the same salary).  

**250 Chapter 6 Formal Relational Query Languages**

_employee_ (_person name_, _street_, _city_ ) _works_ (_person name_, _company name_, _salary_) _company_ (_company name_, _city_) _manages_ (_person name_, _manager name_)

**Figure 6.22** Relational database for Exercises 6.2, 6.8, 6.11, 6.13, and 6.15

e. Find the enrollment of each section that was offered in Autumn 2009.

f. Find the maximum enrollment, across all sections, in Autumn 2009.

g. Find the sections that had the maximum enrollment in Autumn 2009.

**6.2** Consider the relational database of Figure 6.22, where the primary keys are underlined. Give an expression in the relational algebra to express each of the following queries:

a. Find the names of all employees who live in the same city and on the same street as do their managers.

b. Find the names of all employees in this database who do not work for “First Bank Corporation”.

c. Find the names of all employees who earn more than every employee of “Small Bank Corporation”.

**6.3** The natural outer-join operations extend the natural-join operation so that tuples from the participating relations are not lost in the result of the join. Describe how the theta-join operation can be extended so that tuples from the left, right, or both relations are not lost from the result of a theta join.

**6.4** (**Division operation**): The division operator of relational algebra, “÷”, is defined as follows. Let _r_ (_R_) and _s_(_S_) be relations, and let _S_ ⊆ _R_; that is, every attribute of schema _S_ is also in schema _R_. Then _r_ ÷ _s_ is a relation on schema _R_ − _S_ (that is, on the schema containing all attributes of schema _R_ that are not in schema _S_). A tuple _t_ is in _r_ ÷ _s_ if and only if both of two conditions hold:

• _t_ is in _R_−_S_(_r_ )

• For every tuple _ts_ in _s_, there is a tuple _tr_ in _r_ satisfying both of the following: a. _tr_ \[_S_\] = _ts_\[_S_\] b. _tr_ \[_R_ − _S_\] = _t_

Given the above definition:

a. Write a relational algebra expression using the division operator to find the IDs of all students who have taken all Comp. Sci. courses. (Hint: project _takes_ to just ID and _course id_, and generate the set of  

**Practice Exercises 251**

all Comp. Sci. _course id_s using a select expression, before doing the division.)

b. Show how to write the above query in relational algebra, without using division. (By doing so, you would have shown how to define the division operation using the other relational algebra operations.)

**6.5** Let the following relation schemas be given:

_R_ \= (_A, B, C_) _S_ \= (_D, E, F_ )

Let relations _r_(_R_) and _s_(_S_) be given. Give an expression in the tuple rela- tional calculus that is equivalent to each of the following:

a. _A_(_r_ )

b. _B_ \= 17 (_r_ )

c. _r_ × _s_

d. _A,F_ (_C_ \= _D_(_r_ × _s_))

**6.6** Let _R_ \= (_A, B, C_), and let _r_1 and _r_2 both be relations on schema _R_. Give an expression in the domain relational calculus that is equivalent to each of the following:

a. _A_(_r_1)

b. _B_ \= 17 (_r_1)

c. _r_1 ∪ _r_2

d. _r_1 ∩ _r_2

e. _r_1 − _r_2

f. _A,B_(_r_1) _B,C_ (_r_2)

**6.7** Let _R_ \= (_A, B_) and _S_ \= (_A, C_), and let _r_ (_R_) and _s_(_S_) be relations. Write expressions in relational algebra for each of the following queries:

a. {_< a >_ | ∃ _b_ (_< a , b >_ ∈ _r_ ∧ _b_ \= 7)} b. {_< a , b, c >_ | _< a , b >_ ∈ _r_ ∧ _< a , c >_ ∈ _s_} c. {_< a >_ | ∃ _c_ (_< a , c >_ ∈ _s_ ∧ ∃ _b_1_, b_2 (_< a , b_1 _\>_ ∈ _r_ ∧ _< c, b_2 _\>_ ∈

_r_ ∧ _b_1 _\> b_2))}

**6.8** Consider the relational database of Figure 6.22 where the primary keys are underlined. Give an expression in tuple relational calculus for each of the following queries:  

**252 Chapter 6 Formal Relational Query Languages**

a. Find all employees who work directly for “Jones.”

b. Find all cities of residence of all employees who work directly for “Jones.”

c. Find the name of the manager of the manager of “Jones.”

d. Find those employees who earn more than all employees living in the city “Mumbai.”

**6.9** Describe how to translate join expressions in SQL to relational algebra.

**Exercises**

**6.10** Write the following queries in relational algebra, using the university schema.

a. Find the names of all students who have taken at least one Comp. Sci. course.

b. Find the IDs and names of all students who have not taken any course offering before Spring 2009.

c. For each department, find the maximum salary of instructors in that department. You may assume that every department has at least one instructor.

d. Find the lowest, across all departments, of the per-department maxi- mum salary computed by the preceding query.

**6.11** Consider the relational database of Figure 6.22, where the primary keys are underlined. Give an expression in the relational algebra to express each of the following queries:

a. Find the names of all employees who work for “First Bank Corpora- tion”.

b. Find the names and cities of residence of all employees who work for “First Bank Corporation”.

c. Find the names, street addresses, and cities of residence of all em- ployees who work for “First Bank Corporation” and earn more than $10,000.

d. Find the names of all employees in this database who live in the same city as the company for which they work.

e. Assume the companies may be located in several cities. Find all com- panies located in every city in which “Small Bank Corporation” is located.  

**Exercises 253**

**6.12** Using the university example, write relational-algebra queries to find the course sections taught by more than one instructor in the following ways:

a. Using an aggregate function.

b. Without using any aggregate functions.

**6.13** Consider the relational database of Figure 6.22. Give a relational-algebra expression for each of the following queries:

a. Find the company with the most employees.

b. Find the company with the smallest payroll.

c. Find those companies whose employees earn a higher salary, on av- erage, than the average salary at First Bank Corporation.

**6.14** Consider the following relational schema for a library:

_member_(_memb no_, _name_, _dob_) _books_(_isbn_, _title_, _authors_, _publisher_) _borrowed_(_memb no_, _isbn_, _date_)

Write the following queries in relational algebra.

a. Find the names of members who have borrowed any book published by “McGraw-Hill”.

b. Find the name of members who have borrowed all books published by “McGraw-Hill”.

c. Find the name and membership number of members who have bor- rowed more than five different books published by “McGraw-Hill”.

d. For each publisher, find the name and membership number of mem- bers who have borrowed more than five books of that publisher.

e. Find the average number of books borrowed per member. Take into account that if an member does not borrow any books, then that member does not appear in the _borrowed_ relation at all.

**6.15** Consider the employee database of Figure 6.22. Give expressions in tuple relational calculus and domain relational calculus for each of the following queries:

a. Find the names of all employees who work for “First Bank Corpora- tion”.

b. Find the names and cities of residence of all employees who work for “First Bank Corporation”.

c. Find the names, street addresses, and cities of residence of all em- ployees who work for “First Bank Corporation” and earn more than $10,000.  

**254 Chapter 6 Formal Relational Query Languages**

d. Find all employees who live in the same city as that in which the company for which they work is located.

e. Find all employees who live in the same city and on the same street as their managers.

f. Find all employees in the database who do not work for “First Bank Corporation”.

g. Find all employees who earn more than every employee of “Small Bank Corporation”.

h. Assume that the companies may be located in several cities. Find all companies located in every city in which “Small Bank Corporation” is located.

**6.16** Let _R_ \= (_A, B_) and _S_ \= (_A, C_), and let _r_ (_R_) and _s_(_S_) be relations. Write relational-algebra expressions equivalent to the following domain- relational-calculus expressions:

a. {_< a >_ | ∃ _b_ (_< a , b >_ ∈ _r_ ∧ _b_ \= 17)} b. {_< a , b, c >_ | _< a , b >_ ∈ _r_ ∧ _< a , c >_ ∈ _s_} c. {_< a >_ | ∃ _b_ (_< a , b >_ ∈ _r_ ) ∨ ∀ _c_ (∃ _d_ (_< d, c >_ ∈ _s_) ⇒ _< a , c >_ ∈ _s_)} d. {_< a >_ | ∃ _c_ (_< a , c >_ ∈ _s_ ∧ ∃ _b_1_, b_2 (_< a , b_1 _\>_ ∈ _r_ ∧ _< c, b_2 _\>_

∈ _r_ ∧ _b_1 _\> b_2))} **6.17** Repeat Exercise 6.16, writing SQL queries instead of relational-algebra ex-

pressions.

**6.18** Let _R_ \= (_A, B_) and _S_ \= (_A, C_), and let _r_ (_R_) and _s_(_S_) be relations. Using the special constant _null_, write tuple-relational-calculus expressions equivalent to each of the following:

a. _r  s_

b. _r  s_

c. _r  s_

**6.19** Give a tuple-relational-calculus expression to find the maximum value in relation _r_ (_A_).

**Bibliographical Notes**

The original definition of relational algebra is in Codd \[1970\]. Extensions to the relational model and discussions of incorporation of null values in the relational algebra (the RM/T model), as well as outer joins, are in Codd \[1979\]. Codd \[1990\] is a compendium of E. F. Codd’s papers on the relational model. Outer joins are also discussed in Date \[1993b\].  

**Bibliographical Notes 255**

The original definition of tuple relational calculus is in Codd \[1972\]. A for- mal proof of the equivalence of tuple relational calculus and relational algebra is in Codd \[1972\]. Several extensions to the relational calculus have been pro- posed. Klug \[1982\] and Escobar-Molano et al. \[1993\] describe extensions to scalar aggregate functions.  

_This page intentionally left blank_