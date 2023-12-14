---
title: Bibliographical Notes
weight: 15
---

Bibliographical Notes## Bibliographical Notes

E. F. Codd of the IBM San Jose Research Laboratory proposed the relational model in the late 1960s (Codd [1970]). This work led to the prestigious ACM Turing Award to Codd in 1981 (Codd [1982]).

After Codd published his original paper, several research projects were formed with the goal of constructing practical relational database systems, including System R at the IBM San Jose Research Laboratory, Ingres at the University of California at Berkeley, and Query-by-Example at the IBM T. J. Watson Research Center.

Many relational database products are now commercially available. These include IBM’s DB2 and Informix, Oracle, Sybase, and Microsoft SQL Server. Open source relational database systems include MySQL and PostgreSQL. Microsoft Access is a single-user database product that is part of the Microsoft Office suite.

Atzeni and Antonellis [1993], Maier [1983], and Abiteboul et al. [1995] are texts devoted exclusively to the theory of the relational data model.  


C H A P T E R 3 Introduction to SQL

There are a number of database query languages in use, either commercially or experimentally. In this chapter, as well as in Chapters 4 and 5, we study the most widely used query language, SQL.

Although we refer to the SQL language as a “query language,” it can do much more than just query a database. It can define the structure of the data, modify data in the database, and specify security constraints.

It is not our intention to provide a complete users’ guide for SQL. Rather, we present SQL’s fundamental constructs and concepts. Individual implementations of SQL may differ in details, or may support only a subset of the full language.

**3.1 Overview of the SQL Query Language**

IBM developed the original version of SQL, originally called Sequel, as part of the System R project in the early 1970s. The Sequel language has evolved since then, and its name has changed to SQL (Structured Query Language). Many products now support the SQL language. SQL has clearly established itself as _the_ standard relational database language.

In 1986, the American National Standards Institute (ANSI) and the International Organization for Standardization (ISO) published an SQL standard, called SQL-86. ANSI published an extended standard for SQL, SQL-89, in 1989. The next version of the standard was SQL-92 standard, followed by SQL:1999, SQL:2003, SQL:2006, and most recently SQL:2008. The bibliographic notes provide references to these standards.

The SQL language has several parts:

• **Data-definition language** (DDL). The SQL DDL provides commands for defining relation schemas, deleting relations, and modifying relation schemas.

• **Data-manipulation language** (DML). The SQL DML provides the ability to query information from the database and to insert tuples into, delete tuples from, and modify tuples in the database.

<<<<<<< HEAD
**57**  

**58 Chapter 3 Introduction to SQL**

• **Integrity**. The SQL DDL includes commands for specifying integrity constraints that the data stored in the database must satisfy. Updates that violate integrity constraints are disallowed.
=======
• **Integrity**. The SQL DDL includes commands for specifying integrity con- straints that the data stored in the database must satisfy. Updates that violate integrity constraints are disallowed.
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

• **View definition.** The SQL DDL includes commands for defining views.

• **Transaction control**. SQL includes commands for specifying the beginning and ending of transactions.

• **Embedded SQL** and **dynamic SQL**. Embedded and dynamic SQL define how SQL statements can be embedded within general-purpose programming languages, such as C, C++, and Java.

• **Authorization**. The SQL DDL includes commands for specifying access rights to relations and views.

In this chapter, we present a survey of basic DML and the DDL features of SQL. Features described here have been part of the SQL standard since SQL-92.

In Chapter 4, we provide a more detailed coverage of the SQL query language, including (a) various join expressions; (b) views; (c) transactions; (d) integrity constraints; (e) type system; and (f) authorization.

In Chapter 5, we cover more advanced features of the SQL language, including (a) mechanisms to allow accessing SQL from a programming language; (b) SQL functions and procedures; (c) triggers; (d) recursive queries; (e) advanced aggregation features; and (f) several features designed for data analysis, which were introduced in SQL:1999, and subsequent versions of SQL. Later, in Chapter 22, we outline object-oriented extensions to SQL, which were introduced in SQL:1999.

Although most SQL implementations support the standard features we describe here, you should be aware that there are differences between implementations. Most implementations support some nonstandard features, while omitting support for some of the more advanced features. In case you find that some language features described here do not work on the database system that you use, consult the user manuals for your database system to find exactly what features it supports.

## Bibliographical Notes

<<<<<<< HEAD
instructions on how to set up and access some popular database systems. The SQL constructs discussed in this chapter are part of the SQL standard, but certain features are not supported by some databases. The Web site lists these incompatibilities, which you will need to take into account when executing queries on those databases.
=======
The original version of SQL, called Sequel 2, is described by Chamberlin et al. [1976]. Sequel 2 was derived from the language Square (Boyce et al. [1975] and Chamberlin and Boyce [1974]). The American National Standard SQL-86 is de- scribed in ANSI [1986]. The IBM Systems Application Architecture definition of SQL is defined by IBM [1987]. The official standards for SQL-89 and SQL-92 are available as ANSI [1989] and ANSI [1992], respectively.
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

Textbook descriptions of the SQL-92 language include Date and Darwen [1997], Melton and Simon [1993], and Cannan and Otten [1993]. Date and Darwen [1997] and Date [1993a] include a critique of SQL-92 from a programming-languages perspective.

<<<<<<< HEAD
The original version of SQL, called Sequel 2, is described by Chamberlin et al. \[1976\]. Sequel 2 was derived from the language Square (Boyce et al. \[1975\] and Chamberlin and Boyce \[1974\]). The American National Standard SQL-86 is described in ANSI \[1986\]. The IBM Systems Application Architecture definition of SQL is defined by IBM \[1987\]. The official standards for SQL-89 and SQL-92 are available as ANSI \[1989\] and ANSI \[1992\], respectively.

Textbook descriptions of the SQL-92 language include Date and Darwen \[1997\], Melton and Simon \[1993\], and Cannan and Otten \[1993\]. Date and Darwen \[1997\] and Date \[1993a\] include a critique of SQL-92 from a programming-languages perspective.

Textbooks on SQL:1999 include Melton and Simon \[2001\] and Melton \[2002\]. Eisenberg and Melton \[1999\] provide an overview of SQL:1999. Donahoo and Speegle \[2005\] covers SQL from a developers’ perspective. Eisenberg et al. \[2004\] provides an overview of SQL:2003.
=======
Textbooks on SQL:1999 include Melton and Simon [2001] and Melton [2002]. Eisenberg and Melton [1999] provide an overview of SQL:1999. Donahoo and Speegle [2005] covers SQL from a developers’ perspective. Eisenberg et al. [2004\] provides an overview of SQL:2003.
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

The SQL:1999, SQL:2003, SQL:2006 and SQL:2008 standards are published as a collection of ISO/IEC standards documents, which are described in more detail in Section 24.4. The standards documents are densely packed with information and hard to read, and of use primarily for database system implementers. The standards documents are available from the Web site http://webstore.ansi.org, but only for purchase.

Many database products support SQL features beyond those specified in the standard, and may not support some features of the standard. More information on these features may be found in the SQL user manuals of the respective products.

The processing of SQL queries, including algorithms and performance issues, is discussed in Chapters 12 and 13. Bibliographic references on these matters appear in those chapters.  

# C H A P T E R4 Intermediate SQL

In this chapter, we continue our study of SQL. We consider more complex forms of SQL queries, view definition, transactions, integrity constraints, more details regarding SQL data definition, and authorization.

## Bibliographical Notes

See the bibliographic notes of Chapter 3 for references to SQL standards and books on SQL.

<<<<<<< HEAD
An excellent source for more (and up-to-date) information on JDBC, and on Java in general, is java.sun.com/docs/books/tutorial. References to books on Java (including JDBC) are also available at this URL. The ODBC API is described in Microsoft \[1997\] and Sanders \[1998\]. Melton and Eisenberg \[2000\] provides a guide to SQLJ, JDBC, and related technologies. More information on ODBC, ADO, and ADO.NET can be found on msdn.microsoft.com/data.

In the context of functions and procedures in SQL, many database products support features beyond those specified in the standards, and do not support many of the features of the standard. More information on these features may be found in the SQL user manuals of the respective products.

The original SQL proposals for assertions and triggers are discussed in Astrahan et al. \[1976\], Chamberlin et al. \[1976\], and Chamberlin et al. \[1981\]. Melton and Simon \[2001\], Melton \[2002\], and Eisenberg and Melton \[1999\] provide textbook coverage of SQL:1999, the version of the SQL standard that first included triggers.
=======
An excellent source for more (and up-to-date) information on JDBC, and on Java in general, is java.sun.com/docs/books/tutorial. References to books on Java (in- cluding JDBC) are also available at this URL. The ODBC API is described in Microsoft [1997] and Sanders [1998]. Melton and Eisenberg [2000] provides a guide to SQLJ, JDBC, and related technologies. More information on ODBC, ADO, and ADO.NET can be found on msdn.microsoft.com/data.

In the context of functions and procedures in SQL, many database products support features beyond those specified in the standards, and do not support many of the features of the standard. More information on these features may be found in the SQL user manuals of the respective products.

The original SQL proposals for assertions and triggers are discussed in Astra- han et al. [1976], Chamberlin et al. [1976], and Chamberlin et al. [1981]. Melton and Simon [2001], Melton [2002], and Eisenberg and Melton [1999] provide textbook coverage of SQL:1999, the version of the SQL standard that first included triggers.
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

Recursive query processing was first studied in detail in the context of a query language called Datalog, which was based on mathematical logic and followed the syntax of the logic programming language Prolog. Ramakrishnan and Ullman [1995] provides a survey of results in this area, including techniques to optimize queries that select a subset of tuples from a recursively defined view.

Gray et al. [1995] and Gray et al. [1997] describe the data-cube operator. Efficient algorithms for computing data cubes are described by Agarwal et al. [1996], Harinarayan et al. [1996], and Ross and Srivastava [1997]. Descriptions of extended aggregation support in SQL:1999 can be found in the product manuals of database systems such as Oracle and IBM DB2.

There has been a substantial amount of research on the efficient processing of “top-_k_” queries that return only the top-_k_-ranked results. A survey of that work appears in Ilyas et al. [2008].  

# _C H A P T E R_6 Formal Relational Query Languages

In Chapters 2 through 5 we introduced the relational model and covered SQL in great detail. In this chapter we present the formal model upon which SQL as well as other relational query languages are based.

We cover three formal languages. We start by presenting the relational algebra, which forms the basis of the widely used SQL query language. We then cover the tuple relational calculus and the domain relational calculus, which are declarative query languages based on mathematical logic.

## Bibliographical Notes

<<<<<<< HEAD
The original definition of relational algebra is in Codd \[1970\]. Extensions to the relational model and discussions of incorporation of null values in the relational algebra (the RM/T model), as well as outer joins, are in Codd \[1979\]. Codd \[1990\] is a compendium of E. F. Codd’s papers on the relational model. Outer joins are also discussed in Date \[1993b\].  

**Bibliographical Notes 255**

The original definition of tuple relational calculus is in Codd \[1972\]. A formal proof of the equivalence of tuple relational calculus and relational algebra is in Codd \[1972\]. Several extensions to the relational calculus have been proposed. Klug \[1982\] and Escobar-Molano et al. \[1993\] describe extensions to scalar aggregate functions.  

_This page intentionally left blank_
=======
The original definition of relational algebra is in Codd [1970]. Extensions to the relational model and discussions of incorporation of null values in the relational algebra (the RM/T model), as well as outer joins, are in Codd [1979]. Codd [1990] is a compendium of E. F. Codd’s papers on the relational model. Outer joins are also discussed in Date [1993b].  
The original definition of tuple relational calculus is in Codd [1972]. A for- mal proof of the equivalence of tuple relational calculus and relational algebra is in Codd [1972]. Several extensions to the relational calculus have been pro- posed. Klug [1982] and Escobar-Molano et al. [1993] describe extensions to scalar aggregate functions.  
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8
