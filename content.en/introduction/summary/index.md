---
title: Summary
weight: 14
---

# Summary

- A **database-management system** (DBMS) consists of a collection of interre- lated data and a collection of programs to access that data. The data describe one particular enterprise.

- The primary goal of a DBMS is to provide an environment that is both conve- nient and efficient for people to use in retrieving and storing information.

- Database systems are ubiquitous today, and most people interact, either di- rectly or indirectly, with databases many times every day.

- Database systems are designed to store large bodies of information. The man- agement of data involves both the definition of structures for the storage of information and the provision of mechanisms for the manipulation of infor- mation. In addition, the database system must provide for the safety of the information stored, in the face of system crashes or attempts at unauthorized access. If data are to be shared among several users, the system must avoid possible anomalous results.  

- A major purpose of a database system is to provide users with an abstract view of the data. That is, the system hides certain details of how the data are stored and maintained.

- Underlying the structure of a database is the **data model**: a collection of conceptual tools for describing data, data relationships, data semantics, and data constraints.

- The relational data model is the most widely deployed model for storing data in databases. Other data models are the object-oriented model, the object- relational model, and semistructured data models.

- A **data-manipulation language (DML)** is a language that enables users to access or manipulate data. Nonprocedural DMLs, which require a user to specify only what data are needed, without specifying exactly how to get those data, are widely used today.

- A **data-definition language (DDL)** is a language for specifying the database schema and as well as other properties of the data.

- Database design mainly involves the design of the database schema. The entity-relationship (E-R) data model is a widely used data model for database design. It provides a convenient graphical representation to view data, rela- tionships, and constraints.

- A database system has several subsystems.

    - The **storage manager** subsystem provides the interface between the low- level data stored in the database and the application programs and queries submitted to the system.

    - The **query processor** subsystem compiles and executes DDL and DML statements.

- **Transaction management** ensures that the database remains in a consistent (correct) state despite system failures. The transaction manager ensures that concurrent transaction executions proceed without conflicting.

- The architecture of a database system is greatly influenced by the underlying computer system on which the database system runs. Database systems can be centralized, or client-server, where one server machine executes work on behalf of multiple client machines. Database systems can also be designed to exploit parallel computer architectures. Distributed databases span multiple geographically separated machines.

- Database applications are typically broken up into a front-end part that runs at client machines and a part that runs at the back end. In two-tier architectures, the front end directly communicates with a database running at the back end. In three-tier architectures, the back end part is itself broken up into an application server and a database server.  

- Knowledge-discovery techniques attempt to discover automatically statisti- cal rules and patterns from data. The field of **data mining** combines knowledge- discovery techniques invented by artificial intelligence researchers and sta- tistical analysts, with efficient implementation techniques that enable them to be used on extremely large databases.

- There are four different types of database-system users, differentiated by the way they expect to interact with the system. Different types of user interfaces have been designed for the different types of users.

**Review Terms**

- Database-management system (DBMS)
- Database-system applications 
- File-processing systems 
- Data inconsistency 
- Consistency constraints 
- Data abstraction 
- Instance 
- Schema

    - Physical schema

    - Logical schema

- Physical data independence - Data models

    - Entity-relationship model

    - Relational data model

    - Object-based data model

    - Semistructured data model

- Database languages

    - Data-definition language

    - Data-manipulation language

    - Query language

- Metadata 
- Application program 
- Normalization 
- Data dictionary 
- Storage manager 
- Query processor 
- Transactions
    - Atomicity

    - Failure recovery

    - Concurrency control

- Two- and three-tier database archi- tectures

- Data mining 

- Database administrator (DBA)

## Practice Exercises

**1.1** This chapter has described several major advantages of a database system. What are two disadvantages?

**1.2** List five ways in which the type declaration system of a language such as Java or C++ differs from the data definition language used in a database.  

**1.3** List six major steps that you would take in setting up a database for a particular enterprise.

**1.4** List at least 3 different types of information that a university would main- tain, beyond those listed in Section 1.6.2.

**1.5** Suppose you want to build a video site similar to YouTube. Consider each of the points listed in Section 1.2, as disadvantages of keeping data in a file-processing system. Discuss the relevance of each of these points to the storage of actual video data, and to metadata about the video, such as title, the user who uploaded it, tags, and which users viewed it.

**1.6** Keyword queries used in Web search are quite different from database queries. List key differences between the two, in terms of the way the queries are specified, and in terms of what is the result of a query.

## Exercises

**1.7** List four applications you have used that most likely employed a database system to store persistent data.

**1.8** List four significant differences between a file-processing system and a DBMS.

**1.9** Explain the concept of physical data independence, and its importance in database systems.

**1.10** List five responsibilities of a database-management system. For each re- sponsibility, explain the problems that would arise if the responsibility were not discharged.

**1.11** List at least two reasons why database systems support data manipulation using a declarative query language such as SQL, instead of just providing a a library of C or C++ functions to carry out data manipulation.

**1.12** Explain what problems are caused by the design of the table in Figure 1.4.

**1.13** What are five main functions of a database administrator?

**1.14** Explain the difference between two-tier and three-tier architectures. Which is better suited for Web applications? Why?

**1.15** Describe at least 3 tables that might be used to store information in a social-networking system such as Facebook.

## Tools

There are a large number of commercial database systems in use today. The major ones include: IBM DB2 (www.ibm.com/software/data/db2), Oracle (www.oracle.com), Microsoft SQL Server (www.microsoft.com/sql), Sybase (www.sybase.com), and IBM Informix (www.ibm.com/software/data/informix). Some of these systems are available free for personal or noncommercial use, or for development, but are not free for actual deployment.

There are also a number of free/public domain database systems; widely used ones include MySQL (www.mysql.com) and PostgreSQL (www.postgresql.org).

A more complete list of links to vendor Web sites and other information is available from the home page of this book, at www.db-book.com.

## Bibliographical Notes

We list below general-purpose books, research paper collections, and Web sites on databases. Subsequent chapters provide references to material on each topic outlined in this chapter.

Codd [1970] is the landmark paper that introduced the relational model. Textbooks covering database systems include Abiteboul et al. [1995], O’Neil

and O’Neil [2000], Ramakrishnan and Gehrke [2002], Date [2003], Kifer et al. [2005], Elmasri and Navathe [2006], and Garcia-Molina et al. [2008]. Textbook coverage of transaction processing is provided by Bernstein and Newcomer [1997] and Gray and Reuter [1993]. A book containing a collection of research papers on database management is offered by Hellerstein  and Stonebraker [2005].

A review of accomplishments in database management and an assessment of future research challenges appears in Silberschatz et al. [1990], Silberschatz et al. [1996], Bernstein et al. [1998], Abiteboul et al. [2003], and Agrawal et al. [2009]. The home page of the ACM Special Interest Group on Management of Data (www.acm.org/sigmod) provides a wealth of information about database research. Database vendor Web sites (see the Tools section above) provide details about their respective products.  
