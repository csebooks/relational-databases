---
title: SQL Data Definition
weight: 2
---

# SQL Data Definition

The set of relations in a database must be specified to the system by means of a data-definition language (DDL). The SQL DDL allows specification of not only a set of relations, but also information about each relation, including:

• The schema for each relation.

• The types of values associated with each attribute.

• The integrity constraints.

• The set of indices to be maintained for each relation.  

• The security and authorization information for each relation.

• The physical storage structure of each relation on disk.

We discuss here basic schema definition and basic types; we defer discussion of the other SQL DDL features to Chapters 4 and 5.

## Basic Types

The SQL standard supports a variety of built-in types, including:

• **char**(_n_): A fixed-length character string with user-specified length _n_. The full form, **character**, can be used instead.

• **varchar**(_n_): A variable-length character string with user-specified maximum length _n_. The full form, **character varying**, is equivalent.

• **int**: An integer (a finite subset of the integers that is machine dependent). The full form, **integer**, is equivalent.

• **smallint**: A small integer (a machine-dependent subset of the integer type).

• **numeric**(_p, d_): A fixed-point number with user-specified precision. The number consists of _p_ digits (plus a sign), and _d_ of the _p_ digits are to the right of the decimal point. Thus, **numeric**(3,1) allows 44_._5 to be stored exactly, but neither 444_._5 or 0_._32 can be stored exactly in a field of this type.

• **real, double precision**: Floating-point and double-precision floating-point numbers with machine-dependent precision.

• **float(n):** A floating-point number, with precision of at least _n_ digits.

Additional types are covered in Section 4.5. Each type may include a special value called the **null** value. A null value

indicates an absent value that may exist but be unknown or that may not exist at all. In certain cases, we may wish to prohibit null values from being entered, as we shall see shortly.

The **char** data type stores fixed length strings. Consider, for example, an attribute _A_ of type **char**(10). If we store a string “Avi” in this attribute, 7 spaces are appended to the string to make it 10 characters long. In contrast, if attribute _B_ were of type **varchar**(10), and we store “Avi” in attribute _B_, no spaces would be added. When comparing two values of type **char**, if they are of different lengths extra spaces are automatically added to the shorter one to make them the same size, before comparison.

When comparing a **char** type with a **varchar** type, one may expect extra spaces to be added to the **varchar** type to make the lengths equal, before comparison; however, this may or may not be done, depending on the database system. As a result, even if the same value “Avi” is stored in the attributes _A_ and _B_ above, a comparison _A_=_B_ may return false. We recommend you always use the **varchar** type instead of the **char** type to avoid these problems.  

SQL also provides the **nvarchar** type to store multilingual data using the Unicode representation. However, many databases allow Unicode (in the UTF-8 representation) to be stored even in **varchar** types.

## Basic Schema Definition

We define an SQL relation by using the **create table** command. The following command creates a relation _department_ in the database.
```
create table department
(dept name varchar (20),
building varchar (15),
budget numeric (12,2),
primary key (dept name));
```

The relation created above has three attributes, _dept name_, which is a character string of maximum length 20, _building_, which is a character string of maximum length 15, and _budget_, which is a number with 12 digits in total, 2 of which are after the decimal point. The **create table** command also specifies that the _dept name_ attribute is the primary key of the _department_ relation.

The general form of the **create table** command is:
```
create table r 
(A 1 D 1, 
A 2 D 2, . . . , An Dn,
 〈integrity-constraint1〉, . . . ,

〈integrity-constraint k〉);
```

where _r_ is the name of the relation, each _Ai_ is the name of an attribute in the schema of relation _r_, and _Di_ is the domain of attribute _Ai_ ; that is, _Di_ specifies the type of attribute _Ai_ along with optional constraints that restrict the set of allowed values for _Ai_ .

The semicolon shown at the end of the **create table** statements, as well as at the end of other SQL statements later in this chapter, is optional in many SQL implementations.

SQL supports a number of different integrity constraints. In this section, we discuss only a few of them:


• **primary key** (_Aj_1 _, Aj_2_, . . . , Ajm_ ): The **primary-key** specification says that attributes _Aj_1 _, Aj_2 _, . . . , Ajm_ form the primary key for the relation. The primarykey attributes are required to be _nonnull_ and _unique_; that is, no tuple can have a null value for a primary-key attribute, and no two tuples in the relation can be equal on all the primary-key attributes. Although the primary-key  

• **foreign key** (Ak1, Ak2, . . . , Akn) **references** s: The **foreign key** specification says that the values of attributes (Ak1, Ak2, . . . , Akn) for any tuple in the relation must correspond to values of the primary key attributes of some tuple in relation _s_.

Figure 3.1 presents a partial SQL DDL definition of the university database we use in the text. The definition of the _course_ table has a declaration “**foreign key** (_dept name_) **references** _department_”. This foreign-key declaration specifies that for each course tuple, the department name specified in the tuple must exist in the primary key attribute (_dept name_) of the _department_ relation. Without this constraint, it is possible for a course to specify a nonexistent department name. Figure 3.1 also shows foreign key constraints on tables _section_, _instructor_ and _teaches_.

• **not null**: The **not null** constraint on an attribute specifies that the null value is not allowed for that attribute; in other words, the constraint excludes the null value from the domain of that attribute. For example, in Figure 3.1, the **not null** constraint on the _name_ attribute of the _instructor_ relation ensures that the name of an instructor cannot be null.

More details on the foreign-key constraint, as well as on other integrity constraints that the **create table** command may include, are provided later, in Section 4.4.

SQL prevents any update to the database that violates an integrity constraint. For example, if a newly inserted or modified tuple in a relation has null values for any primary-key attribute, or if the tuple has the same value on the primary-key attributes as does another tuple in the relation, SQL flags an error and prevents the update. Similarly, an insertion of a _course_ tuple with a _dept name_ value that does not appear in the _department_ relation would violate the foreign-key constraint on _course_, and SQL prevents such an insertion from taking place.

A newly created relation is empty initially. We can use the **insert** command to load data into the relation. For example, if we wish to insert the fact that there is an instructor named Smith in the Biology department with _instructor id_ 10211 and a salary of $66,000, we write:

**insert into** _instructor_ 
**values** (10211, ’Smith’, ’Biology’, 66000);

The values are specified in the _order_ in which the corresponding attributes are listed in the relation schema. The insert command has a number of useful features, and is covered in more detail later, in Section 3.9.2.

We can use the **delete** command to delete tuples from a relation. The command

**delete from** _student_;  

**create table** department
(dept name **varchar** (20),
building **varchar** (15),
budget numeric (12,2),
**primary key** (dept name));

**create table** course
(course id **varchar** (7),
title **varchar** (50),
dept name **varchar** (20),
credits numeric (2,0),
**primary key** (course id),
**foreign key** (dept name) references department);

create table instructor
(ID **varchar** (5),
name **varchar** (20) not null,
dept name **varchar** (20),
salary numeric (8,2),
**primary key** (ID),
**foreign key** (dept name) references department);

**create table** section
(course id **varchar** (8),
sec id **varchar** (8),
semester **varchar** (6),
year numeric (4,0),
building **varchar** (15),
room number **varchar** (7),
time slot id **varchar** (4),
**primary key** (course id, sec id, semester, year),
**foreign key** (course id) references course);

create table teaches
(ID **varchar** (5),
course id **varchar** (8),
sec id **varchar** (8),
semester **varchar** (6),
year numeric (4,0),
primary key (ID, course id, sec id, semester, year),
foreign key (course id, sec id, semester, year) references section,
foreign key (ID) references instructor);

**Figure 3.1** SQL data definition for part of the university database.  

**3.3 Basic Structure of SQL Queries 63**

would delete all tuples from the _student_ relation. Other forms of the delete command allow specific tuples to be deleted; the delete command is covered in more detail later, in Section 3.9.1.

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

