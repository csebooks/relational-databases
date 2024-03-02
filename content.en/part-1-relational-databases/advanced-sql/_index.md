---
title: 'Advanced Sql'
categories:
weight: 4
references:
    videos:
        - youtube:FttzCZICGpQ
        - youtube:22KOl07dMRM
    links:
        - https://www.geeksforgeeks.org/sql-trigger-student-database/
        - https://www.javatpoint.com/triggers-in-sql-server
    books:
        - b1:
            title: Triggers
            url: https://www.google.co.in/books/edition/Triggers/0qmeBgAAQBAJ?hl=en&gbpv=0
        - b2:
           title: SQL Procedures, Triggers, and Functions
            url: https://www.google.co.in/books/edition/SQL_Procedures_Triggers_and_Functions_on/nuALDAAAQBAJ?hl=en&gbpv=0
--- 

# Advanced Sql

In Chapters 3 and 4, we provided detailed coverage of the basic structure of SQL.In this chapter, we cover some of themore advanced features of SQL.1 We address the issue of how to access SQL from a general-purpose programming language, which is very important for building applications that use a database to store and retrieve data. We describe how procedural code can be executed within the database, either by extending the SQL language to support procedural actions, or by allowing functions defined in procedural languages to be executed within the database. We describe triggers, which can be used to specify actions that are to be carried out automatically on certain events such as insertion, deletion, or update of tuples in a specified relation. We discuss recursive queries and advanced aggregation features supported by SQL. Finally, we describe online analytic processing (OLAP) systems, which support interactive analysis of very large datasets.