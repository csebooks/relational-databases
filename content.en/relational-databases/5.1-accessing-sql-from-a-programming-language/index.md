---
title: 5.1 Accessing SQL From a Programming Language
weight: 33
---

5.1 Accessing SQL From a Programming Language## 5.1 Accessing SQL From a Programming Language

SQL provides a powerful declarative query language. Writing queries in SQL is usually much easier than coding the same queries in a general-purpose programming language. However, a database programmer must have access to a general-purpose programming language for at least two reasons:

**1\.** Not all queries can be expressed in SQL, since SQL does not provide the full expressive power of a general-purpose language. That is, there exist queries that can be expressed in a language such as C, Java, or Cobol that cannot be expressed in SQL. To write such queries, we can embed SQL within a more powerful language.

**2\.** Nondeclarative actions—such as printing a report, interacting with a user, or sending the results of a query to a graphical user interface—cannot be done from within SQL. Applications usually have several components, and querying or updating data is only one component; other components are written in general-purpose programming languages. For an integrated application, there must be a means to combine SQL with a general-purpose programming language.

There are two approaches to accessing SQL from a general-purpose programming language:

• **Dynamic SQL**: A general-purpose program can connect to and communicate with a database server using a collection of functions (for procedural languages) or methods (for object-oriented languages). Dynamic SQL allows the program to construct an SQL query as a character string at runtime, submit the query, and then retrieve the result into program variables a tuple at a time. The _dynamic SQL_ component of SQL allows programs to construct and submit SQL queries at runtime.

In this chapter, we look at two standards for connecting to an SQL database and performing queries and updates. One, JDBC (Section 5.1.1), is an application program interface for the Java language. The other, ODBC (Section 5.1.2), is an application program interface originally developed for the C language, and subsequently extended to other languages such as C++, C#, and Visual Basic.

• **Embedded SQL**: Like dynamic SQL, embedded SQL provides a means by which a program can interact with a database server. However, under embedded SQL, the SQL statements are identified at compile time using a preprocessor. The preprocessor submits the SQL statements to the database system for precompilation and optimization; then it replaces the SQL statements in the application program with appropriate code and function calls before invoking the programming-language compiler. Section 5.1.3 covers embedded SQL.

A major challenge in mixing SQL with a general-purpose language is the mismatch in the ways these languages manipulate data. In SQL, the primary type of data is the relation. SQL statements operate on relations and return relations as a result. Programming languages normally operate on a variable at a time, and those variables correspond roughly to the value of an attribute in a tuple in a relation. Thus, integrating these two types of languages into a single application requires providing a mechanism to return the result of a query in a manner that the program can handle.

### 5.1.1 JDBC

The **JDBC** standard defines an **application program interface (API)** that Java programs can use to connect to database servers. (The word JDBC was originally  

```
public static void JDBCexample(String userid, String passwd)
{
try
{
Class.forName ("oracle.jdbc.driver.OracleDriver");
Connection conn = DriverManager.getConnection(
"jdbc:oracle:thin:@db.yale.edu:1521:univdb",
userid, passwd);
Statement stmt = conn.createStatement();
try {
stmt.executeUpdate(
"insert into instructor values(’77987’, ’Kim’, ’Physics’, 98000)");
} catch (SQLException sqle)
{
System.out.println("Could not insert tuple. " + sqle);
}
ResultSet rset = stmt.executeQuery(
"select dept name, avg (salary) "+
" from instructor "+
" group by dept name");
while (rset.next()) {
System.out.println(rset.getString("dept name") + " " +
rset.getFloat(2));
}
stmt.close();
conn.close();
}
catch (Exception sqle)
{
System.out.println("Exception : " + sqle);
}
}
```


**Figure 5.1** An example of JDBC code.

an abbreviation for **Java Database Connectivity**, but the full form is no longer used.)

Figure 5.1 shows an example Java program that uses the JDBC interface. It illustrates how connections are opened, how statements are executed and results processed, and how connections are closed. We discuss this example in detail in this section. The Java program must import java.sql.\*, which contains the interface definitions for the functionality provided by JDBC.

#### 5.1.1.1 Connecting to the Database

The first step in accessing a database from a Java program is to open a connection to the database. This step is required to select which database to use, for example ,an instance of Oracle running on your machine, or a PostgreSQL database running on another machine. Only after opening a connection can a Java program execute SQL statements.

A connection is opened using the getConnection method of the DriverManager class (within java.sql). This method takes three parameters.2

• The first parameter to the getConnection call is a string that specifies the URL, or machine name, where the server runs (in our example, db.yale.edu), along with possibly some other information such as the protocol to be used to communicate with the database (in our example, jdbc:oracle:thin:; we shall shortly see why this is required), the port number the database system uses for communication (in our example, 2000), and the specific database on the server to be used (in our example, univdb). Note that JDBC specifies only the API, not the communication protocol. A JDBC driver may support multiple protocols, and we must specify one supported by both the database and the driver. The protocol details are vendor specific.

• The second parameter to getConnection is a database user identifier, which is a string.

• The third parameter is a password, which is also a string. (Note that the need to specify a password within the JDBC code presents a security risk if an unauthorized person accesses your Java code.)

In our example in the figure, we have created a Connection object whose handle is conn.

Each database product that supports JDBC (all the major database vendors do) provides a JDBC driver that must be dynamically loaded in order to access the database from Java. In fact, loading the driver must be done first, before connecting to the database.

This is done by invoking Class.forName with one argument specifying a concrete class implementing the java.sql.Driver interface, in the first line of the program in Figure 5.1. This interface provides for the translation of productindependent JDBC calls into the product-specific calls needed by the specific database management system being used. The example in the figure shows the Oracle driver, oracle.jdbc.driver.OracleDriver.3 The driver is available in a .jar file at vendor Web sites and should be placed within the classpath so that the Java compiler can access it.

<<<<<<< HEAD
The actual protocol used to exchange information with the database depends on the driver that is used, and is not defined by the JDBC standard. Some

2There are multiple versions of the getConnection method, which differ in the parameters that they accept. We present the most commonly used version. 3The equivalent driver names for other products are as follows: IBM DB2: com.ibm.db2.jdbc.app.DB2Driver; Microsoft SQL Server: com.microsoft.sqlserver.jdbc.SQLServerDriver; PostgreSQL: org.postgresql.Driver; and MySQL: com.mysql.jdbc.Driver. Sun also offers a “bridge driver” that converts JDBC calls to ODBC. This should be used only for vendors that support ODBC but not JDBC.  

**5.1 Accessing SQL From a Programming Language 161**

drivers support more than one protocol, and a suitable protocol must be chosen depending on what protocol the database that you are connecting to supports. In our example, when opening a connection with the database, the string jdbc:oracle:thin: specifies a particular protocol supported by Oracle.

**5.1.1.2 Shipping SQL Statements to the Database System**
=======
The actual protocol used to exchange information with the database de- pends on the driver that is used, and is not defined by the JDBC standard. Some drivers support more than one protocol, and a suitable protocol must be cho- sen depending on what protocol the database that you are connecting to sup- ports. In our example, when opening a connection with the database, the string jdbc:oracle:thin: specifies a particular protocol supported by Oracle.

#### 5.1.1.2 Shipping SQL Statements to the Database System
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

Once a database connection is open, the program can use it to send SQL statements to the database system for execution. This is done via an instance of the class Statement. A Statement object is not the SQL statement itself, but rather an object that allows the Java program to invoke methods that ship an SQL statement given as an argument for execution by the database system. Our example creates a Statement handle (stmt) on the connection conn.

To execute a statement, we invoke either the executeQuery method or the executeUpdate method, depending on whether the SQL statement is a query (and, thus, returns a result set) or nonquery statement such as **update**, **insert**, **delete**, **create table**, etc. In our example, stmt.executeUpdate executes an update statement that inserts into the _instructor_ relation. It returns an integer giving the number of tuples inserted, updated, or deleted. For DDL statements, the return value is zero. The try _{ . . . }_ catch _{ . . . }_ construct permits us to catch any exceptions (error conditions) that arise when JDBC calls are made, and print an appropriate message to the user.

#### 5.1.1.3 Retrieving the Result of a Query

The example program executes a query by using stmt.executeQuery. It retrieves the set of tuples in the result into a ResultSet object rset and fetches them one tuple at a time. The next method on the result set tests whether or not there remains at least one unfetched tuple in the result set and if so, fetches it. The return value of the next method is a Boolean indicating whether it fetched a tuple. Attributes from the fetched tuple are retrieved using various methods whose names begin with get. The method getString can retrieve any of the basic SQL data types (converting the value to a Java String object), but more restrictive methods such as getFloat can be used as well. The argument to the various get methods can either be an attribute name specified as a string, or an integer indicating the position of the desired attribute within the tuple. Figure 5.1 shows two ways of retrieving the values of attributes in a tuple: using the name of the attribute (_dept name_) and using the position of the attribute (2, to denote the second attribute).

The statement and connection are both closed at the end of the Java program. Note that it is important to close the connection because there is a limit imposed on the number of connections to the database; unclosed connections may cause that limit to be exceeded. If this happens, the application cannot open any more connections to the database. 

PreparedStatement pStmt = conn.prepareStatement(
"insert into instructor values(?,?,?,?)");
pStmt.setString(1, "88877");
pStmt.setString(2, "Perry");
pStmt.setString(3, "Finance");
pStmt.setInt(4, 125000);
pStmt.executeUpdate();
pStmt.setString(1, "88878");
pStmt.executeUpdate();

**Figure 5.2** Prepared statements in JDBC code.

#### 5.1.1.4 Prepared Statements

We can create a prepared statement in which some values are replaced by “?”, thereby specifying that actual values will be provided later. The database system compiles the query when it is prepared. Each time the query is executed (with new values to replace the “?”s), the database system can reuse the previously compiled form of the query and apply the new values. The code fragment in Figure 5.2 shows how prepared statements can be used.

The prepareStatement method of the Connection class submits an SQL statement for compilation. It returns an object of class PreparedStatement. At this point, no SQL statement has been executed. The executeQuery and executeUpdate methods of PreparedStatement class do that. But before they can be invoked, we must use methods of class PreparedStatement that assign values for the “?” parameters. The setStringmethod and other similar methods such as setInt for other basic SQL types allow us to specify the values for the parameters. The first argument specifies the “?” parameter for which we are assigning a value (the first parameter is 1, unlike most other Java constructs, which start with 0). The second argument specifies the value to be assigned.

In the example in the figure, we prepare an **insert** statement, set the “?” parameters, and then invoke executeUpdate. The final two lines of our example show that parameter assignments remain unchanged until we specifically reassign them. Thus, the final statement, which invokes executeUpdate, inserts the tuple (“88878”, “Perry”, “Finance”, 125000).

Prepared statements allow for more efficient execution in cases where the same query can be compiled once and then run multiple times with different parameter values. However, there is an even more significant advantage to prepared statements that makes them the preferred method of executing SQL queries whenever a user-entered value is used, even if the query is to be run only once. Suppose that we read in a user-entered value and then use Java string manipulation to construct the SQL statement. If the user enters certain special characters, such as a single quote, the resulting SQL statement may be syntactically incorrect unless we take extraordinary care in checking the input. The setString method does this for us automatically and inserts the needed escape characters to ensure syntactic correctness.  

In our example, suppose that the values for the variables _ID_, _name_, _dept name_, and _salary_ have been entered by a user, and a corresponding row is to be inserted into the _instructor_ relation. Suppose that, instead of using a prepared statement, a query is constructed by concatenating the strings using the following Java expression:

"insert into instructor values(’ " + ID + " ’, ’ " + name + " ’, " + " ’ + dept name + " ’, " ’ balance + ")"

and the query is executed directly using the executeQuery method of a Statement object. Now, if the user typed a single quote in the ID or name fields, the query string would have a syntax error. It is quite possible that an instructor name may have a quotation mark in its name (for example, “O’Henry”).

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

which is harmless and returns the empty relation. Older systems allow multiple statements to be executed in a single call, with statements separated by a semicolon. This feature is being eliminated because the SQL injection technique was used by malicious hackers to insert whole SQL statements. Because these statements run with the privileges of the owner of the Java program, devastating SQL statements such as **drop table** could be executed. Developers of SQL applications need to be wary of such potential security holes.

#### 5.1.1.5 Callable Statements

JDBC also provides a CallableStatement interface that allows invocation of SQL stored procedures and functions (described later, in Section 5.2). These play the same role for functions and procedures as prepareStatement does for queries.

CallableStatement cStmt1 = conn.prepareCall("_{_? = call some function(?)_}_"); 
CallableStatement cStmt2 = conn.prepareCall("_{_call some procedure(?,?)_}_");

The data types of function return values and out parameters of procedures must be registered using the method registerOutParameter(), and can be retrieved using get methods similar to those for result sets. See a JDBC manual for more details.

#### 5.1.1.6 Metadata Features

As we noted earlier, a Java application program does not include declarations for data stored in the database. Those declarations are part of the SQL DDL statements. Therefore, a Java program that uses JDBC must either have assumptions about the database schema hard-coded into the program or determine that information directly from the database system at runtime. The latter approach is usually preferable, since it makes the application program more robust to changes in the database schema.

Recall that when we submit a query using the executeQuery method, the result of the query is contained in a ResultSet object. The interface ResultSet has a method, getMetaData(), that returns a ResultSetMetaData object that contains metadata about the result set. ResultSetMetaData, in turn, has methods to find metadata information, such as the number of columns in the result, the name of a specified column, or the type of a specified column. In this way, we can execute a query even if we have no idea of the schema of the result.

The Java code segment below uses JDBC to print out the names and types of all columns of a result set. The variable rs in the code below is assumed to refer to a ResultSet instance obtained by executing a query.

ResultSetMetaData rsmd = rs.getMetaData();
for(int i = 1; i <= rsmd.getColumnCount(); i++) {
System.out.println(rsmd.getColumnName(i));
System.out.println(rsmd.getColumnTypeName(i));
}

The getColumnCount method returns the arity (number of attributes) of the result relation. That allows us to iterate through each attribute (note that we start DatabaseMetaData dbmd = conn.getMetaData(); ResultSet rs = dbmd.getColumns(null, "univdb", "department", "%");

// Arguments to getColumns: Catalog, Schema-pattern, Table-pattern, // and Column-Pattern // Returns: One row for each column; row has a number of attributes // such as COLUMN NAME, TYPE NAME

while( rs.next()) _{_ System.out.println(rs.getString("COLUMN NAME"),

rs.getString("TYPE NAME"); _}_

**Figure 5.3** Finding column information in JDBC using DatabaseMetaData.

at 1, as is conventional in JDBC). For each attribute, we retrieve its name and data type using the methods getColumnName and getColumnTypeName, respectively.

The DatabaseMetaData interface provides a way to find metadata about the database. The interface Connection has a method getMetaData that returns a DatabaseMetaData object. The DatabaseMetaData interface in turn has a very large number of methods to get metadata about the database and the database system to which the application is connected.

For example, there are methods that return the product name and version number of the database system. Other methods allow the application to query the database system about its supported features.

Still other methods return information about the database itself. The code in Figure 5.3 illustrates how to find information about columns (attributes) of relations in a database. The variable conn is assumed to be a handle for an already opened database connection. The method getColumns takes four arguments: a catalog name (null signifies that the catalog name is to be ignored), a schema name pattern, a table name pattern, and a column name pattern. The schema name, table name, and column name patterns can be used to specify a name or a pattern. Patterns can use the SQL string matching special characters “%” and “ ”; for instance, the pattern “%” matches all names. Only columns of tables of schemas satisfying the specified name or pattern are retrieved. Each row in the result set contains information about one column. The rows have a number of columns such as the name of the catalog, schema, table and column, the type of the column, and so on.

Examples of other methods provided by DatabaseMetaData that provide information about the database include those for retrieval of metadata about relations (getTables()), foreign-key references (getCrossReference()), authorizations, database limits such as maximum number of connections, and so on.

The metadata interfaces can be used for a variety of tasks. For example, they can be used to write a database browser that allows a user to find the tables in a database, examine their schema, examine rows in a table, apply selections to see desired rows, and so on. The metadata information can be used to make code used for these tasks generic; for example, code to display the rows in a relation can be written in such a way that it would work on all possible relations regardless of their schema. Similarly, it is possible to write code that takes a query string, executes the query, and prints out the results as a formatted table; the code can work regardless of the actual query submitted.

#### 5.1.1.7 Other Features

JDBC provides a number of other features, such as **updatable result sets**. It can create an updatable result set from a query that performs a selection and/or a projection on a database relation. An update to a tuple in the result set then results in an update to the corresponding tuple of the database relation.

Recall from Section 4.3 that a transaction allows multiple actions to be treated as a single atomic unit which can be committed or rolled back.

By default, each SQL statement is treated as a separate transaction that is committed automatically. The method setAutoCommit() in the JDBC Connection interface allows this behavior to be turned on or off. Thus, if conn is an open connection, conn.setAutoCommit(false) turns off automatic commit. Transactions must then be committed or rolled back explicitly using either conn.commit() or conn.rollback(). conn.setAutoCommit(true) turns on automatic commit.

JDBC provides interfaces to deal with large objects without requiring an entire large object to be created in memory. To fetch large objects, the ResultSet interface provides methods getBlob() and getClob() that are similar to the getString() method, but return objects of type Blob and Clob, respectively. These objects do not store the entire large object, but instead store “locators” for the large objects, that is, logical pointers to the actual large object in the database. Fetching data from these objects is very much like fetching data from a file or an input stream, and can be performed using methods such as getBytes and getSubString.

Conversely, to store large objects in the database, the PreparedStatement class permits a database column whose type is **blob** to be linked to an input stream (such as a file that has been opened) using the method setBlob(int parameterIndex, InputStream inputStream). When the prepared statement is executed, data are read from the input stream, and written to the **blob** in the database. Similarly, a **clob** column can be set using the setClob method, which takes as arguments a parameter index and a character stream.

JDBC includes a _row set_ feature that allows result sets to be collected and shipped to other applications. Row sets can be scanned both backward and forward and can be modified. Because row sets are not part of the database itself once they are downloaded, we do not cover details of their use here.

### 5.1.2 ODBC

<<<<<<< HEAD
The **Open Database Connectivity** (ODBC) standard defines an API that applications can use to open a connection with a database, send queries and updates, and get back results. Applications such as graphical user interfaces, statistics pack 
=======
The **Open Database Connectivity** (ODBC) standard defines an API that applica- tions can use to open a connection with a database, send queries and updates, and get back results. Applications such as graphical user interfaces, statistics pack
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

```
void ODBCexample()
{
RETCODE error;
HENV env; /* environment */
HDBC conn; /* database connection */
SQLAllocEnv(&env);
SQLAllocConnect(env, &conn);
SQLConnect(conn, "db.yale.edu", SQL NTS, "avi", SQL NTS,
"avipasswd", SQL NTS);
{
char deptname[80];
float salary;
int lenOut1, lenOut2;
HSTMT stmt;
char * sqlquery = "select dept name, sum (salary)
from instructor
group by dept name";
SQLAllocStmt(conn, &stmt);
error = SQLExecDirect(stmt, sqlquery, SQL NTS);
if (error == SQL SUCCESS) {
SQLBindCol(stmt, 1, SQL C CHAR, deptname , 80, &lenOut1);
SQLBindCol(stmt, 2, SQL C FLOAT, &salary, 0 , &lenOut2);
while (SQLFetch(stmt) == SQL SUCCESS) {
printf (" %s %g\n", depthname, salary);
}
}
SQLFreeStmt(stmt, SQL DROP);
}
SQLDisconnect(conn);
SQLFreeConnect(conn);
SQLFreeEnv(env);
}
```

**Figure 5.4** ODBC code example.

ages, and spreadsheets can make use of the same ODBC API to connect to any database server that supports ODBC.

Each database system supporting ODBC provides a library that must be linked with the client program. When the client program makes an ODBC API call, the code in the library communicates with the server to carry out the requested action, and fetch results.

Figure 5.4 shows an example of C code using the ODBC API. The first step in using ODBC to communicate with a server is to set up a connection with the server. To do so, the program first allocates an SQL environment, then a database connection handle. ODBC defines the types HENV, HDBC, and RETCODE. The program then opens the database connection by using SQLConnect. This call takes several parameters, including the connection handle, the server to which to connect, the user identifier, and the password for the database. The constant SQL NTS denotes that the previous argument is a null-terminated string.

Once the connection is set up, the program can send SQL commands to the database by using SQLExecDirect. C language variables can be bound to attributes of the query result, so that when a result tuple is fetched using SQLFetch, its attribute values are stored in corresponding C variables. TheSQLBindCol function does this task; the second argument identifies the position of the attribute in the query result, and the third argument indicates the type conversion required from SQL to C. The next argument gives the address of the variable. For variable-length types like character arrays, the last two arguments give the maximum length of the variable and a location where the actual length is to be stored when a tuple is fetched. A negative value returned for the length field indicates that the value is **null**. For fixed-length types such as integer or float, the maximum length field is ignored, while a negative value returned for the length field indicates a null value.

The SQLFetch statement is in a **while** loop that is executed until SQLFetch returns a value other than SQL SUCCESS. On each fetch, the program stores the values in C variables as specified by the calls on SQLBindCol and prints out these values.

At the end of the session, the program frees the statement handle, disconnects from the database, and frees up the connection and SQL environment handles. Good programming style requires that the result of every function call must be checked to make sure there are no errors; we have omitted most of these checks for brevity.

It is possible to create an SQL statement with parameters; for example, consider the statement insert into department values(?,?,?). The question marks are placeholders for values which will be supplied later. The above statement can be “prepared,” that is, compiled at the database, and repeatedly executed by providing actual values for the placeholders—in this case, by providing an department name, building, and budget for the relation _department_.

ODBC defines functions for a variety of tasks, such as finding all the relations in the database and finding the names and types of columns of a query result or a relation in the database.

By default, each SQL statement is treated as a separate transaction that is committed automatically. The SQLSetConnectOption(conn, SQL AUTOCOMMIT, 0) turns off automatic commit on connection conn, and transactions must then be committed explicitly by SQLTransact(conn, SQL COMMIT) or rolled back by SQLTransact(conn, SQL ROLLBACK).

The ODBC standard defines _conformance levels_, which specify subsets of the functionality defined by the standard. An ODBC implementation may provide only core level features, or it may provide more advanced (level 1 or level 2) features. Level 1 requires support for fetching information about the catalog, such as information about what relations are present and the types of their attributes.  

**ADO.NET**

The ADO.NET API, designed for the Visual Basic .NET and C# languages, provides functions to access data, which at a high level are not dissimilar to the JDBC functions, although details differ. Like JDBC and ODBC, the ADO.NET API allows access to results of SQL queries, as well as to metadata, but is considerably simpler to use than ODBC. A database that supports ODBC can be accessed using the ADO.NET API, and the ADO.NET calls are translated into ODBC calls. The ADO.NET API can also be used with some kinds of nonrelational data sources such as Microsoft’s OLE-DB, XML (covered in Chapter 23), and more recently, the Entity Framework developed by Microsoft. See the bibliographic notes for more information on ADO.NET.

Level 2 requires further features, such as the ability to send and retrieve arrays of parameter values and to retrieve more detailed catalog information.

The SQL standard defines a **call level interface (CLI)** that is similar to the ODBC interface.

### 5.1.3 Embedded SQL

The SQL standard defines embeddings of SQL in a variety of programming languages, such as C, C++, Cobol, Pascal, Java, PL/I, and Fortran. A language in which SQL queries are embedded is referred to as a _host_ language, and the SQL structures permitted in the host language constitute _embedded_ SQL.

Programs written in the host language can use the embedded SQL syntax to access and update data stored in a database. An embedded SQL program must be processed by a special preprocessor prior to compilation. The preprocessor replaces embedded SQL requests with host-language declarations and procedure calls that allow runtime execution of the database accesses. Then, the resulting program is compiled by the host-language compiler. This is the main distinction between embedded SQL and JDBC or ODBC.

In JDBC, SQL statements are interpreted at runtime (even if they are prepared first using the prepared statement feature). When embedded SQL is used, some SQL-related errors (including data-type errors) may be caught at compile time.

To identify embedded SQL requests to the preprocessor, we use the EXEC SQL statement; it has the form:

EXEC SQL < embedded SQL statement _\>_;

The exact syntax for embedded SQL requests depends on the language in which SQL is embedded. In some languages, such as Cobol, the semicolon is replaced with END-EXEC. 

We place the statement SQL INCLUDE SQLCA in the program to identify the place where the preprocessor should insert the special variables used for communication between the program and the database system.

Before executing any SQL statements, the program must first connect to the database. This is done using:

EXEC SQL **connect to** _server_ **user** _user-name_ **using** _password_;

Here, _server_ identifies the server to which a connection is to be established. Variables of the host language can be used within embedded SQL statements,

but they must be preceded by a colon (:) to distinguish them from SQL variables. Variables used as above must be declared within a DECLARE section, as illustrated below. The syntax for declaring the variables, however, follows the usual host language syntax.

EXEC SQL BEGIN DECLARE SECTION;

int _credit amount_;
EXEC SQL END DECLARE SECTION;

Embedded SQL statements are similar in form to regular SQL statements. There are, however, several important differences, as we note here.

To write a relational query, we use the **declare cursor** statement. The result of the query is not yet computed. Rather, the program must use the **open** and **fetch** commands (discussed later in this section) to obtain the result tuples. As we shall see, use of a cursor is analogous to iterating through a result set in JDBC.

Consider the university schema. Assume that we have a host-language variable _credit amount_ in our program, declared as we saw earlier, and that we wish to find the names of all students who have taken more than _credit amount_ credit hours. We can write this query as follows:

EXEC SQL 
**declare** _c_ **cursor for**
**select** _ID_, _name_ 
**from** _student_ 
**where** _tot cred >_ :_credit amount_;

The variable _c_ in the preceding expression is called a _cursor_ for the query. We use this variable to identify the query. We then use the **open** statement, which causes the query to be evaluated.

The **open** statement for our sample query is as follows:

EXEC SQL **open** _c_;

This statement causes the database system to execute the query and to save the results within a temporary relation. The query uses the value of the host-language variable (_credit amount_) at the time the **open** statement is executed.  

<<<<<<< HEAD
**5.1 Accessing SQL From a Programming Language 171**

If the SQL query results in an error, the database system stores an error diagnostic in the SQL communication-area (SQLCA) variables.
=======
If the SQL query results in an error, the database system stores an error diag- nostic in the SQL communication-area (SQLCA) variables.
>>>>>>> 0ecfe74e39ab7cceb756dfe2c8441bdba5f2f6d8

We then use a series of **fetch** statements, each of which causes the values of one tuple to be placed in host-language variables. The **fetch** statement requires one host-language variable for each attribute of the result relation. For our example query, we need one variable to hold the _ID_ value and another to hold the _name_ value. Suppose that those variables are _si_ and _sn_, respectively, and have been declared within a DECLARE section. Then the statement:

EXEC SQL **fetch** _c_ **into** :_si_, :_sn_;

produces a tuple of the result relation. The program can then manipulate the variables _si_ and _sn_ by using the features of the host programming language.

A single **fetch** request returns only one tuple. To obtain all tuples of the result, the program must contain a loop to iterate over all tuples. Embedded SQL assists the programmer in managing this iteration. Although a relation is conceptually a set, the tuples of the result of a query are in some fixed physical order. When the program executes an **open** statement on a cursor, the cursor is set to point to the first tuple of the result. Each time it executes a **fetch** statement, the cursor is updated to point to the next tuple of the result. When no further tuples remain to be processed, the character array variable SQLSTATE in the SQLCA is set to ’02000’ (meaning “no more data”); the exact syntax for accessing this variable depends on the specific database system you use. Thus, we can use a **while** loop (or equivalent loop) to process each tuple of the result.

We must use the **close** statement to tell the database system to delete the temporary relation that held the result of the query. For our example, this statement takes the form

EXEC SQL **close** _c_;

Embedded SQL expressions for database modification (**update**, **insert**, and **delete**) do not return a result. Thus, they are somewhat simpler to express. A database-modification request takes the form

EXEC SQL _<_ any valid **update, insert,** or **delete**_\>_;

Host-language variables, preceded by a colon, may appear in the SQL databasemodification expression. If an error condition arises in the execution of the statement, a diagnostic is set in the SQLCA.

Database relations can also be updated through cursors. For example, if we want to add 100 to the _salary_ attribute of every instructor in the Music department, we could declare a cursor as follows.  

**SQLJ**

The Java embedding of SQL, called SQLJ, provides the same features as other embedded SQL implementations, but using a different syntax that more closely matches features already present in Java, such as iterators. For example, SQLJ uses the syntax #sql instead of EXEC SQL, and instead of cursors, uses the Java iterator interface to fetch query results. Thus the result of executing a query is a Java iterator, and the next() method of the Java iterator interface can be used to step through the result tuples, just as the preceding examples use **fetch** on the cursor. The iterator must have attributes declared, whose types match the types of the attributes in the SQL query result. The code snippet below illustrates the use of iterators.

```
#sql iterator deptInfoIter ( String dept name, int avgSal);
deptInfoIter iter = null;
#sql iter = { select dept name, avg(salary)
from instructor
group by dept name };
while (iter.next()) {
String deptName = iter.dept name();
int avgSal = iter.avgSal();
System.out.println(deptName + " " + avgSal);
}
iter.close();

```

SQLJ is supported by IBM DB2 and Oracle; both provide translators that convert SQLJ code into JDBC code. The translator can connect to the database in order to check the syntactic correctness of queries at compile time, and to ensure that the SQL types of query results are compatible with the Java types of variables they are assigned to. As of early 2009, SQLJ is not supported by other database systems.

We do not describe SQLJ in detail here; see the bibliographic notes for more information.

EXEC SQL **declare** _c_ **cursor for** 
**select** \* 
**from** _instructor_ 
**where** _dept name_\= ‘Music’ 
**for update**;

We then iterate through the tuples by performing **fetch** operations on the cursor (as illustrated earlier), and after fetching each tuple we execute the following code:  

EXEC SQL 
**update** _instructor_ 
**set** _salary_ \= _salary_ \+ 100 
**where current of** _c_;

Transactions can be committed using EXEC SQL COMMIT, or rolled back using EXEC SQL ROLLBACK.

Queries in embedded SQL are normally defined when the program is written. There are rare situations where a query needs to be defined at runtime. For example, an application interface may allow a user to specify selection conditions on one or more attributes of a relation, and may construct the **where** clause of an SQL query at runtime, with conditions on only those attributes for which the user specifies selections. In such cases, a query string can be constructed and prepared at runtime, using a statement of the form EXEC SQL PREPARE _<_query-name_\>_

FROM :_<_variable_\>_, and a cursor can be opened on the query name.

