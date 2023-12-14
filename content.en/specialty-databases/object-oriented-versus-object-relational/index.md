---
title: Object-Oriented versus Object-Relational
weight: 10
---

Object-Oriented versus Object-Relational## Object-Oriented versus Object-Relational

We have now studied object-relational databases, which are object-oriented data- bases built on top of the relation model, as well as object-oriented databases, which are built around persistent programming languages, and object-relational  

**974 Chapter 22 Object-Based Databases**

mapping systems, which build an object layer on top of a traditional relational database.

Each of these approaches targets a different market. The declarative nature and limited power (compared to a programming language) of the SQL language provides good protection of data from programming errors, and makes high-level optimizations, such as reducing I/O, relatively easy. (We covered optimization of relational expressions in Chapter 13.) Object-relational systems aim at making data modeling and querying easier by using complex data types. Typical ap- plications include storage and querying of complex data, including multimedia data.

A declarative language such as SQL, however, imposes a significant perfor- mance penalty for certain kinds of applications that run primarily in main mem- ory, and that perform a large number of accesses to the database. Persistent programming languages target such applications that have high performance re- quirements. They provide low-overhead access to persistent data and eliminate the need for data translation if the data are to be manipulated by a programming language. However, they are more susceptible to data corruption by program- ming errors, and they usually do not have a powerful querying capability. Typical applications include CAD databases.

Object-relational mapping systems allow programmers to build applications using an object model, while using a traditional database system to store the data. Thus, they combine the robustness of widely used relational database systems, with the power of object models for writing applications. However, they suffer from overheads of data conversion between the object model and the relational model used to store data.

We can summarize the strengths of the various kinds of database systems in this way:

- **Relational systems**: Simple data types, powerful query languages, high pro- tection.

- **Persistent programming language–based OODBs**: Complex data types, integration with programming language, high performance.

- **Object-relational systems**: Complex data types, powerful query languages, high protection.

- **Object-relational mapping systems**: Complex data types integrated with programming languages, designed as a layer on top of a relational database system.

These descriptions hold in general, but keep in mind that some database systems blur the boundaries. For example, object-oriented database systems built around a persistent programming language can be implemented on top of a relational or object-relational database system. Such systems may provide lower performance than object-oriented database systems built directly on a storage system, but provide some of the stronger protection guarantees of relational systems.  



