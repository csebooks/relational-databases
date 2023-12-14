---
title: 5.7 Summary
weight: 40
---

5.7 Summary## 5.7 Summary

• SQL queries can be invoked from host languages, via embedded and dynamic SQL. The ODBC and JDBC standards define application program interfaces to access SQL databases from C and Java language programs. Increasingly, programmers use these APIs to access databases.

• Functions and procedures can be defined using SQLprocedural extensions that allow iteration and conditional (if-then-else) statements.

• Triggers define actions to be executed automatically when certain events occur and corresponding conditions are satisfied. Triggers have many uses, such as implementing business rules, audit logging, and even carrying out actions outside the database system. Although triggers were not added to the SQL standard until SQL:1999, most database systems have long implemented triggers.

• Some queries, such as transitive closure, can be expressed either by using iteration or by using recursive SQL queries. Recursion can be expressed using either recursive views or recursive **with** clause definitions.

• SQL supports several advanced aggregation features, including ranking and windowing queries that simplify the expression of some aggregates and allow more efficient evaluation.

• Online analytical processing (OLAP) tools help analysts view data summarized in different ways, so that they can gain insight into the functioning of an organization.

◦ OLAP tools work on multidimensional data, characterized by dimension attributes and measure attributes.

◦ The data cube consists of multidimensional data summarized in different ways. Precomputing the data cube helps speed up queries on summaries of data.

◦ Cross-tab displays permit users to view two dimensions of multidimensional data at a time, along with summaries of the data.

◦ Drill down, rollup, slicing, and dicing are among the operations that users perform with OLAP tools.

• SQL, starting with the SQL:1999 standard, provides a variety of operators for data analysis, including **cube** and **rollup** operations. Some systems support a **pivot** clause, which allows easy creation of cross-tabs.

**Review Terms**
• JDBC
• ODBC
• Prepared statements
• Accessing metadata
• SQL injection
• Embedded SQL
• Cursors
• Updatable cursors
• Dynamic SQL
• SQL functions
• Stored procedures
• Procedural constructs
• External language routines
• Trigger
• Before and after triggers
• Transition variables and tables
• Recursive queries
• Monotonic queries
• Ranking functions
◦ Rank
◦ Dense rank
◦ Partition by
• Windowing
• Online analytical processing
(OLAP)
• Multidimensional data
◦ Measure attributes
◦ Dimension attributes
◦ Pivoting
◦ Data cube
◦ Slicing and dicing
◦ Rollup and drill down
• Cross-tabulation

