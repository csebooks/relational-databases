---
title: 'Relational Databases'
weight: 5
references:
    videos:
        - youtube:WI9dE8-TFAc
        - youtube:OqjJjpjDRLc
    links:
        - https://www.oracle.com/in/database/what-is-a-relational-database/#:~:text=A%20relational%20database%20is%20a,of%20representing%20data%20in%20tables.
        - https://www.ibm.com/topics/relational-databases
    books:
        - b1:
            title: Relational Database Design and Implementation
            url: https://www.google.co.in/books/edition/Relational_Database_Design_and_Implement/yQgfCgAAQBAJ?hl=en&gbpv=0
        - b2:
            title: Fundamentals of relational database management systems 
            url: https://www.google.co.in/books/edition/Fundamentals_of_Relational_Database_Mana/MQNtCQAAQBAJ?hl=en&gbpv=0
---

# Relational Databases

A relational database is based on the relational model and uses a collection of tables to represent both data and the relationships among those data. It also in- cludes a DML and DDL. In Chapter 2 we present a gentle introduction to the fundamentals of the relational model. Most commercial relational database sys- tems employ the SQL language, which we cover in great detail in Chapters 3, 4, and 5. In Chapter 6 we discuss other influential languages.

## Tables

Each table has multiple columns and each column has a unique name. Figure 1.2 presents a sample relational database comprising two tables: one shows details of university instructors and the other shows details of the various university departments.

The first table, the instructor table, shows, for example, that an instructor named Einstein with ID 22222 is a member of the Physics department and has an annual salary of $95,000. The second table, department, shows, for example, that the Biology department is located in the Watson building and has a budget of $90,000. Of course, a real-world university would have many more departments and instructors. We use small tables in the text to illustrate concepts. A larger example for the same schema is available online.

The relational model is an example of a record-based model. Record-based models are so named because the database is structured in fixed-format records of several types. Each table contains records of a particular type. Each record type defines a fixed number of fields, or attributes. The columns of the table correspond to the attributes of the record type.

It is not hard to see how tables may be stored in files. For instance, a special character (such as a comma) may be used to delimit the different attributes of a record, and another special character (such as a new-line character) may be used to delimit records. The relational model hides such low-level implementation details from database developers and users.

We also note that it is possible to create schemas in the relational model that have problems such as unnecessarily duplicated information. For example, sup- pose we store the department budget as an attribute of the instructor record. Then, whenever the value of a particular budget (say that one for the Physics depart- ment) changes, that change must to be reflected in the records of all instructors associated with the Physics department. In Chapter 8, we shall study how to distinguish good schema designs from bad schema designs.

![alt A sample relational database](sample.png)
**Figure 1.2** A sample relational database.

## Data-Manipulation Language

The SQL query language is nonprocedural. A query takes as input several tables (possibly only one) and always returns a single table. Here is an example of an SQL query that finds the names of all instructors in the History department:

**select** instructor.name **from** instructor **where** instructor._dept name_ = ’History’;

The query specifies that those rows from the table instructor where the _dept name_ is History must be retrieved, and the name attribute of these rows must be displayed. More specifically, the result of executing this query is a table with a single column  

labeled name, and a set of rows, each of which contains the name of an instructor whose _dept name_, is History. If the query is run on the table in Figure 1.2, the result will consist of two rows, one with the name El Said and the other with the name Califieri.

Queries may involve information from more than one table. For instance, the following query finds the instructor ID and department name of all instructors associated with a department with budget of greater than $95,000.

**select** instructor.ID, department._dept name_ **from** instructor, department **where** instructor._dept name_\= department._dept name_ **and**

department._budget >_ 95000;

If the above query were run on the tables in Figure 1.2, the system would find that there are two departments with budget of greater than $95,000—Computer Science and Finance; there are five instructors in these departments. Thus, the result will consist of a table with two columns (ID, _dept name_) and five rows: (12121, Finance), (45565, Computer Science), (10101, Computer Science), (83821, Computer Science), and (76543, Finance).

## Data-Definition Language

SQL provides a rich DDL that allows one to define tables, integrity constraints, assertions, etc.

For instance, the following SQL DDL statement defines the department table:

**create table** department (_dept name_ **char** (20), building **char** (15), budget **numeric** (12,2));

Execution of the above DDL statement creates the department table with three columns: _dept name_, building, and budget, each of which has a specific data type associated with it. We discuss data types in more detail in Chapter 3. In addition, the DDL statement updates the data dictionary, which contains metadata (see Section 1.4.2). The schema of a table is an example of metadata.

## Database Access from Application Programs

SQL is not as powerful as a universal Turing machine; that is, there are some computations that are possible using a general-purpose programming language but are not possible using SQL. SQL also does not support actions such as input from users, output to displays, or communication over the network. Such com- putations and actions must be written in a host language, such as C, C++, or Java, with embedded SQL queries that access the data in the database. **Application programs** are programs that are used to interact with the database in this fashion.  

Examples in a university system are programs that allow students to register for courses, generate class rosters, calculate student GPA, generate payroll checks, etc.

To access the database, DML statements need to be executed from the host language. There are two ways to do this:

- By providing an application program interface (set of procedures) that can be used to send DML and DDL statements to the database and retrieve the results.

The Open Database Connectivity (ODBC) standard for use with the C language is a commonly used application program interface standard. The Java Database Connectivity (JDBC) standard provides corresponding features to the Java language.

- By extending the host language syntax to embed DML calls within the host language program. Usually, a special character prefaces DML calls, and a preprocessor, called the **DML precompiler**, converts the DML statements to normal procedure calls in the host language.

