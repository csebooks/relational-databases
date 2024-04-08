---
title:  'SQL Data Types and Schemas'
weight: 5
references:
    videos:
        - youtube:vjyRweav9zo
        - youtube:Ss-r2089R3o
    links:
        - https://www.javatpoint.com/sql-data-types
        - https://www.digitalocean.com/community/tutorials/sql-data-types
    books:
        - b1:
            title: Sql Server Advanced Data Types 
            url: https://www.google.co.in/books/edition/SQL_Server_Advanced_Data_Types/l61qDwAAQBAJ?hl=en&gbpv=1&dq=sql+data+types+and+schemas&printsec=frontcover
        - b2:
            title: My SQL in a Nutshell
            url: https://www.google.co.in/books/edition/MySQL_in_a_Nutshell/lBHE8xdSGwQC?hl=en&gbpv=1&dq=sql+data+types&printsec=frontcover
---

# SQL Data Types and Schemas

In Chapter 3, we covered a number of built-in data types supported in SQL, such as integer types, real types, and character types. There are additional built-in data types supported by SQL, which we describe below. We also describe how to create basic user-defined types in SQL.

## Date and Time Types in SQL

In addition to the basic data types we introduced in Section 3.2, the SQL standard supports several data types relating to dates and times:

• **date**: A calendar date containing a (four-digit) year, month, and day of the month.

• **time**: The time of day, in hours, minutes, and seconds. A variant, **time**(_p_), can be used to specify the number of fractional digits for seconds (the default being 0). It is also possible to store time-zone information along with the time by specifying **time with timezone**.

• **timestamp**: A combination of **date** and **time**. A variant, **timestamp**(_p_), can be used to specify the number of fractional digits for seconds (the default here being 6). Time-zone information is also stored if **with timezone** is specified.

Date and time values can be specified like this:

**date** ’2001-04-25’ 
**time** ’09:30:00’ 
**timestamp** ’2001-04-25 10:29:01.45’

Dates must be specified in the format year followed by month followed by day, as shown. The seconds field of **time** or **timestamp** can have a fractional part, as in the timestamp above.

We can use an expression of the form **cast** _e_ **as** _t_ to convert a character string (or string valued expression) _e_ to the type _t_, where _t_ is one of **date, time**, or **timestamp**. The string must be in the appropriate format as illustrated at the beginning of this paragraph. When required, time-zone information is inferred from the system settings.

To extract individual fields of a **date** or **time** value _d_, we can use **extract** (field **from** _d_), where _field_ can be one of **year, month, day, hour, minute**, or **second**. Timezone information can be extracted using **timezone hour** and **timezone minute**.  

SQL defines several functions to get the current date and time. For example, **current date** returns the current date, **current time** returns the current time (with time zone), and **localtime** returns the current local time (without time zone). Timestamps (date plus time) are returned by **current timestamp** (with time zone) and **localtimestamp** (local date and time without time zone).

SQL allows comparison operations on all the types listed here, and it allows both arithmetic and comparison operations on the various numeric types. SQL also provides a data type called **interval**, and it allows computations based on dates and times and on intervals. For example, if _x_ and _y_ are of type **date**, then _x_ − _y_ is an interval whose value is the number of days from date _x_ to date _y_. Similarly, adding or subtracting an interval to a date or time gives back a date or time, respectively.

## Default Values

SQL allows a default value to be specified for an attribute as illustrated by the following **create table** statement:

**create table** _student_ 
(_ID_ **varchar** (5), 
_name_ **varchar** (20) **not null**, 
_dept name_ **varchar** (20), 
_tot cred_ **numeric** (3,0) **default** 0, 
**primary key** (_ID_));

The default value of the _tot cred_ attribute is declared to be 0. As a result, when a tuple is inserted into the _student_ relation, if no value is provided for the _tot cred_ attribute, its value is set to 0. The following insert statement illustrates how an insertion can omit the value for the _tot cred_ attribute.

**insert into** _student_(_ID_, _name_, _dept name_) 
**values** (’12789’, ’Newman’, ’Comp. Sci.’);

## 4.5.3 Index Creation

Many queries reference only a small proportion of the records in a file. For example, a query like “Find all instructors in the Physics department” or “Find the _tot cred_ value of the student with _ID_ 22201” references only a fraction of the student

records. It is inefficient for the system to read every record and to check _ID_ field for the _ID_ “32556,” or the _building_ field for the value “Physics”.

An **index** on an attribute of a relation is a data structure that allows the database system to find those tuples in the relation that have a specified value for that attribute efficiently, without scanning through all the tuples of the relation. For example, if we create in index on attribute _ID_ of relation _student_, the database system can find the record with any specified _ID_ value, such as 22201, or 44553, directly, without reading all the tuples of the _student_ relation. An index can also be created on a list of attributes, for example on attributes _name_, and _dept name_ of _student_.

We study later, in Chapter 11, how indices are actually implemented, including a particularly widely used kind of index called a B+-tree index.

Although the SQL language does not formally define any syntax for creating indices, many databases support index creation using the syntax illustrated below.

**create index** _studentID index_ **on** _student_(_ID_);

The above statement creates an index named _studentID index_ on the attribute _ID_ of the relation _student_.

When a user submits an SQL query that can benefit from using an index, the SQL query processor automatically uses the index. For example, given an SQL query that selects the _student_ tuple with _ID_ 22201, the SQL query processor would use the index _studentID index_ defined above to find the required tuple without reading the whole relation.

## Large-Object Types

Many current-generation database applications need to store attributes that can be large (of the order of many kilobytes), such as a photograph, or very large (of the order of many megabytes or even gigabytes), such as a high-resolution medical image or video clip. SQL therefore provides large-object data types for character data (**clob**) and binary data (**blob**). The letters “lob” in these data types stand for “Large OBject.” For example, we may declare attributes

_book review_ **clob**(10KB) 
_image_ **blob**(10MB) 
_movie_ **blob**(2GB)

For result tuples containing large objects (multiple megabytes to gigabytes), it is inefficient or impractical to retrieve an entire large object into memory. Instead, an application would usually use an SQL query to retrieve a “locator” for a large object and then use the locator to manipulate the object from the host language in which the application itself is written. For instance, the JDBC application program interface (described in Section 5.1.1) permits a locator to be fetched instead of the entire large object; the locator can then be used to fetch the large object in small pieces, rather than all at once, much like reading data from an operating system file using a read function call.

## User-Defined Types

SQL supports two forms of user-defined data types. The first form, which we cover here, is called **distinct types**. The other form, called **structured data types**, allows the creation of complex data types with nested record structures, arrays, and multisets. We do not cover structured data types in this chapter, but describe them later, in Chapter 22.

It is possible for several attributes to have the same data type. For example, the _name_ attributes for student name and instructor name might have the same domain: the set of all person names. However, the domains of _budget_ and _dept name_ certainly ought to be distinct. It is perhaps less clear whether _name_ and _dept name_ should have the same domain. At the implementation level, both instructor names and department names are character strings. However, we would normally not consider the query “Find all instructors who have the same name as a department” to be a meaningful query. Thus, if we view the database at the conceptual, rather than the physical, level, _name_ and _dept name_ should have distinct domains.

More importantly, at a practical level, assigning an instructor’s name to a department name is probably a programming error; similarly, comparing a monetary value expressed in dollars directly with a monetary value expressed in pounds is also almost surely a programming error. A good type system should be able to detect such assignments or comparisons. To support such checks, SQL provides the notion of **distinct types**.

The **create type** clause can be used to define new types. For example, the statements:

**create type** _Dollars_ **as numeric**(12,2) **final;** 
**create type** _Pounds_ **as numeric**(12,2) **final;**

define the user-defined types _Dollars_ and _Pounds_ to be decimal numbers with a total of 12 digits, two of which are placed after the decimal point. (The keyword **final** isn’t really meaningful in this context but is required by the SQL:1999 standard for reasons we won’t get into here; some implementations allow the **final** keyword to be omitted.) The newly created types can then be used, for example, as types of attributes of relations. For example, we can declare the _department_ table as:

**create table** _department_ 
(_dept name_ **varchar** (20),
_building_ **varchar** (15), 
_budget Dollars_);

An attempt to assign a value of type _Dollars_ to a variable of type _Pounds_ results in a compile-time error, although both are of the same numeric type. Such an assignment is likely to be due to a programmer error, where the programmer forgot about the differences in currency. Declaring different types for different currencies helps catch such errors.

As a result of strong type checking, the expression (_department.budget_+20) would not be accepted since the attribute and the integer constant 20 have different types. Values of one type can be _cast_ (that is, converted) to another domain, as illustrated below:

**cast** (_department.budget_ **to** _numeric_(12,2))  

We could do addition on the numeric type, but to save the result back to an attribute of type _Dollars_ we would have to use another cast expression to convert the type back to _Dollars_.

SQL provides **drop type** and **alter type** clauses to drop or modify types that have been created earlier.

Even before user-defined types were added to SQL (in SQL:1999), SQL had a similar but subtly different notion of **domain** (introduced in SQL-92), which can add integrity constraints to an underlying type. For example, we could define a domain _DDollars_ as follows.

**create domain** _DDollars_ **as numeric**(12,2) **not null**;

The domain _DDollars_ can be used as an attribute type, just as we used the type _Dollars_. However, there are two significant differences between types and domains:

**1.** Domains can have constraints, such as **not null**, specified on them, and can have default values defined for variables of the domain type, whereas userdefined types cannot have constraints or default values specified on them. User-defined types are designed to be used not just for specifying attribute types, but also in procedural extensions to SQL where it may not be possible to enforce constraints.

**2.** Domains are not strongly typed. As a result, values of one domain type can be assigned to values of another domain type as long as the underlying types are compatible.

When applied to a domain, the **check** clause permits the schema designer to specify a predicate that must be satisfied by any attribute declared to be from this domain. For instance, a **check** clause can ensure that an instructor’s salary domain allows only values greater than a specified value:

**create domain** _YearlySalary_ **numeric**(8,2) 
**constraint** _salary value test_ **check**(**value** _\>_\= 29000.00);

The domain _YearlySalary_ has a constraint that ensures that the YearlySalary is greater than or equal to $29,000.00. The clause **constraint** _salary value test_ is optional, and is used to give the name _salary value test_ to the constraint. The name is used by the system to indicate the constraint that an update violated.

As another example, a domain can be restricted to contain only a specified set of values by using the **in** clause:

**create domain** _degree level_ **varchar**(10) 
**constraint** _degree level test_
**check** (**value in** (’Bachelors’, ’Masters’, or ’Doctorate’));  

**SUPPORT FOR TYPES AND DOMAINS IN DATABASE IMPLEMENTATIONS**

Although the **create type** and **create domain** constructs described in this section are part of the SQL standard, the forms of these constructs described here are not fully supported by most database implementations. PostgreSQL supports the **create domain** construct, but its **create type** construct has a different syntax and interpretation.

IBM DB2 supports a version of the **create type** that uses the syntax **create distinct type**, but does not support **create domain**. Microsoft SQL Server implements a version of **create type** construct that supports domain constraints, similar to the SQL **create domain** construct.

Oracle does not support either construct as described here. However, SQL also defines a more complex object-oriented type system, which we study later in Chapter 22. Oracle, IBM DB2, PostgreSQL, and SQL Server all support objectoriented type systems using different forms of the **create type** construct.

## Create Table Extensions

Applications often require creation of tables that have the same schema as an existing table. SQL provides a **create table like** extension to support this task:

**create table** _temp instructor_ **like** _instructor_;

The above statement creates a new table _temp instructor_ that has the same schema as _instructor_.

When writing a complex query, it is often useful to store the result of a query as a new table; the table is usually temporary. Two statements are required, one to create the table (with appropriate columns) and the second to insert the query result into the table. SQL:2003 provides a simpler technique to create a table containing the results of a query. For example the following statement creates a table _t1_ containing the results of a query.

**create table** _t1_ **as** 
(**select** \* 
**from** _instructor_ 
**where** _dept name_\= ’Music’)
**with data**;

By default, the names and data types of the columns are inferred from the query result. Names can be explicitly given to the columns by listing the column names after the relation name.

As defined by the SQL:2003 standard, if the **with data** clause is omitted, the table is created but not populated with data. However many implementations populate the table with data by default even if the **with data** clause is omitted.  

Note that several implementations support the functionality of **create table** _. . ._

**like** and **create table** _. . ._ **as** using different syntax; see the respective system manuals for further details.

The above **create table** _. . ._ **as** statement closely resembles the **create view** statement and both are defined by using queries. The main difference is that the contents of the table are set when the table is created, whereas the contents of a view always reflect the current query result.

## Schemas, Catalogs, and Environments

To understand the motivation for schemas and catalogs, consider how files are named in a file system. Early file systems were flat; that is, all files were stored in a single directory. Current file systems, of course, have a directory (or, synonymously, folder) structure, with files stored within subdirectories. To name a file uniquely, we must specify the full path name of the file, for example, /users/avi/db-book/chapter3.tex.

Like early file systems, early database systems also had a single name space for all relations. Users had to coordinate to make sure they did not try to use the same name for different relations. Contemporary database systems provide a three-level hierarchy for naming relations. The top level of the hierarchy consists of **catalogs**, each of which can contain **schemas**. SQL objects such as relations and views are contained within a **schema**. (Some database implementations use the term “database” in place of the term catalog.)

In order to perform any actions on a database, a user (or a program) must first _connect_ to the database. The user must provide the user name and usually, a password for verifying the identity of the user. Each user has a default catalog and schema, and the combination is unique to the user. When a user connects to a database system, the default catalog and schema are set up for the connection; this corresponds to the current directory being set to the user’s home directory when the user logs into an operating system.

To identify a relation uniquely, a three-part name may be used, for example,

_catalog5.univ schema.course_

We may omit the catalog component, in which case the catalog part of the name is considered to be the default catalog for the connection. Thus if _catalog5_ is the default catalog, we can use _univ schema.course_ to identify the same relation uniquely.

If a user wishes to access a relation that exists in a different schema than the default schema for that user, the name of the schema must be specified. However, if a relation is in the default schema for a particular user, then even the schema name may be omitted. Thus we can use just _course_ if the default catalog is _catalog5_ and the default schema is _univ schema_.

With multiple catalogs and schemas available, different applications and different users can work independently without worrying about name clashes. Moreover, multiple versions of an application—one a production version, other test versions—can run on the same database system.  

The default catalog and schema are part of an **SQL environment** that is set up for each connection. The environment additionally contains the user identifier (also referred to as the _authorization identifier)._ All the usual SQL statements, including the DDL and DML statements, operate in the context of a schema.

We can create and drop schemas by means of **create schema** and **drop schema** statements. In most database systems, schemas are also created automatically when user accounts are created, with the schema name set to the user account name. The schema is created in either a default catalog, or a catalog specified in creating the user account. The newly created schema becomes the default schema for the user account.

Creation and dropping of catalogs is implementation dependent and not part of the SQL standard.
