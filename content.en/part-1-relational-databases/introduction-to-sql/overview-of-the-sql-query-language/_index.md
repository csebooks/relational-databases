---
title: Overview of the SQL Query Language
weight: 1
references:
    videos:
        - youtube:k5WZ9MJTA_Y
        - youtube:HqUWoQMxYfE
    links:
        - https://www.tutorialspoint.com/sql/sql-overview.htm
        - https://aws.amazon.com/what-is/sql/
    books:
        - b1:
            title: The Structured Query Language
            url: https://www.google.co.in/books/edition/Structured_Query_Language_SQL/FKRQAAAAMAAJ?hl=en&gbpv=0&bsq=overview%20of%20the%20sql%20query%20language
        - b2:
            title: SQL Clearly Explained  
            url: https://www.google.co.in/books/edition/SQL_Clearly_Explained/KMZasMUMU5UC?hl=en&gbpv=0
---

# Overview of the SQL Query Language

IBM developed the original version of SQL, originally called Sequel, as part of the System R project in the early 1970s. The Sequel language has evolved since then, and its name has changed to SQL (Structured Query Language). Many products now support the SQL language. SQL has clearly established itself as the standard relational database language.

In 1986, the American National Standards Institute (ANSI) and the International Organization for Standardization (ISO) published an SQL standard, called SQL-86. ANSIpublishedanextendedstandard for SQL, SQL-89, in 1989. The next version of the standardwas SQL-92 standard, followed by SQL:1999, SQL:2003, SQL:2006,and most recently SQL:2008.The bibliographic notes provide references to these standards.
The SQL language has several parts:

• Data-definition language (DDL). The SQL DDL provides commands for defining relation schemas, deleting relations, and modifying relation schemas.

• Data-manipulation language (DML). The SQL DML provides the ability to query information from the database and to insert tuples into, delete tuples from, and modify tuples in the database.

• Integrity. The SQL DDL includes commands for specifying integrity constraints that the data stored in the database must satisfy. Updates that violate integrity constraints are disallowed.

• View definition. The SQL DDL includes commands for defining views.

• Transaction control. SQL includes commands for specifying the beginning and ending of transactions.

• Embedded SQL and dynamic SQL. Embedded and dynamic SQL define how SQL statements can be embedded within general-purpose programming languages, such as C, C++, and Java.

• Authorization. The SQL DDL includes commands for specifying access rights to relations and views.


In this chapter, we present a survey of basic DML and the DDL features of SQL. Features described here have been part of the SQL standard since SQL-92.

In Chapter 4,we provide amore detailed coverage of the SQL query language, including (a) various join expressions; (b) views; (c) transactions; (d) integrity constraints; (e) type system; and (f) authorization.

In Chapter 5,we cover more advanced features of the SQL language, including (a) mechanisms to allow accessing SQL from a programming language; (b) SQL functions and procedures; (c) triggers; (d) recursive queries; (e) advanced aggregation features; and (f) several features designed for data analysis, which were introduced in SQL:1999, and subsequent versions of SQL. Later, in Chapter 22, we outline object-oriented extensions to SQL, which were introduced in SQL:1999.

Although most SQL implementations support the standard features we describe here, you should be aware that there are differences between implementations. Most implementations support some nonstandard features, while omitting support for some of the more advanced features. In case you find that some language features described here do not work on the database system that you use, consult the user manuals for your database system to find exactly what features it supports.