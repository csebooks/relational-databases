---
title: 'Functions and Procedures'
weight: 2
references:
    videos:
        - youtube:91mm8M_hs0s
        - youtube:WOqFqZ9LB6A
    links:
        - https://www.geeksforgeeks.org/difference-between-function-and-procedure/
        - https://www.javatpoint.com/function-vs-procedure
    books:
        - b1:
            title:  SQL Procedures, Triggers, and Functions 
            url: https://www.google.co.in/books/edition/SQL_Procedures_Triggers_and_Functions_on/nuALDAAAQBAJ?hl=en&gbpv=0
            
        - b2:
            title: MySQL Stored Procedure Programming 
            url: https://www.google.co.in/books/edition/MySQL_Stored_Procedure_Programming/YpeP0ok0cO4C?hl=en&gbpv=0
---

# Functions and Procedures

We have already seen several functions that are built into the SQL language. In this section, we show how developers can write their own functions and procedures, store them in the database, and then invoke them from SQL statements. Functions are particularly useful with specialized data types such as images and geometric objects. For instance, a line-segment data type used in a map database may have an associated function that checks whether two line segments overlap, and an image data type may have associated functions to compare two images for similarity.

Procedures and functions allow “business logic” to be stored in the database, and executed from SQL statements. For example, universities usually have many rules about how many courses a student can take in a given semester, the minimum number of courses a full-time instructor must teach in a year, the maximum number of majors a student can be enrolled in, and so on. While such business logic can be encoded as programming-language procedures stored entirely outside the database, defining them as stored procedures in the database has several advantages. For example, it allows multiple applications to access the procedures, and it allows a single point of change in case the business rules change, without changing other parts of the application. Application code can then call the stored procedures, instead of directly updating database relations.

SQL allows the definition of functions, procedures, and methods. These can be defined either by the procedural component of SQL, or by an external programming language such as Java, C, or C++. We look at definitions in SQL first, and then see how to use definitions in external languages in Section 5.2.3.

Although the syntax we present here is defined by the SQL standard, most databases implement nonstandard versions of this syntax. For example, the procedural languages supported by Oracle (PL/SQL), Microsoft SQL Server (TransactSQL), and PostgreSQL (PL/pgSQL) all differ from the standard syntax we present  

**create function** _dept count_(_dept name_ **varchar(20)**)
**returns integer** 
**begin** 
**declare** _d count_ **integer**;
**select count**(_\*_) **into** _d count_ 
**from** _instructor_ 
**where** _instructor_._dept name_\= _dept name_
**return** _d count_; 
**end**

**Figure 5.5** Function defined in SQL.

here. We illustrate some of the differences, for the case of Oracle, later (page 178). See the respective system manuals for further details. Although parts of the syntax we present here may not be supported on such systems, the concepts we describe are applicable across implementations, although with a different syntax.

## Declaring and Invoking SQL Functions and Procedures

Suppose that we want a function that, given the name of a department, returns the count of the number of instructors in that department. We can define the function as shown in Figure 5.5.4 This function can be used in a query that returns names and budgets of all departments with more than 12 instructors:

**select** _dept name_, _budget_ 
**from** _instructor_ 
**where** _dept count_(_dept name_) _\>_ 12;

The SQL standard supports functions that can return tables as results; such functions are called **table functions**.5 Consider the function defined in Figure 5.6. The function returns a table containing all the instructors of a particular department. Note that the function’s parameter is referenced by prefixing it with the name of the function (_instructor of.dept name_).

The function can be used in a query as follows:

**select** \* 
**from table**(_instructor of_ (’Finance’));

This query returns all instructors of the ’Finance’ department. In the above simple case it is straightforward to write this query without using table-valued functions. In general, however, table-valued functions can be thought of as **parameterized views** that generalize the regular notion of views by allowing parameters.

**create function** _instructors of_ (_dept name_ **varchar**(20)) 
**returns table** (
_ID_ **varchar** (5), 
_name_ **varchar** (20), 
_dept name_ **varchar** (20),
_salary_ **numeric** (8,2))

**return table** 
(**select** _ID_, _name_, _dept name_, _salary_ 
**from** _instructor_ 
**where** _instructor_._dept name_ \= _instructor of.dept name_);

**Figure 5.6** Table function in SQL.

SQL also supports procedures. The _dept count_ function could instead be written as a procedure:

**create procedure** _dept count proc_(**in** _dept name_ **varchar(20)**, **out** _d count_ **integer**)
**begin** 
**select count**(_\*_) **into** _d count_ 
**from** _instructor_ 
**where** _instructor_._dept name_\= _dept count proc_._dept name_
**end**

The keywords **in** and **out** indicate, respectively, parameters that are expected to have values assigned to them and parameters whose values are set in the procedure in order to return results.

Procedures can be invoked either from an SQL procedure or from embedded SQL by the **call** statement:

**declare** _d count_ **integer**; 
**call** _dept count proc_(’Physics’, _d count_);

Procedures and functions can be invoked from dynamic SQL, as illustrated by the JDBC syntax in Section 5.1.1.4.

SQL permits more than one procedure of the same name, so long as the number of arguments of the procedures with the same name is different. The name, along with the number of arguments, is used to identify the procedure. SQL also permits more than one function with the same name, so long as the different functions with the same name either have different numbers of arguments, or for functions with the same number of arguments, they differ in the type of at least one argument.  

## Language Constructs for Procedures and Functions

SQL supports constructs that give it almost all the power of a general-purpose programming language. The part of the SQL standard that deals with these constructs is called the **Persistent Storage Module (PSM)**.

Variables are declared using a **declare** statement and can have any valid SQL data type. Assignments are performed using a **set** statement.

A compound statement is of the form **begin** _. . ._ **end**, and it may contain multiple SQL statements between the **begin** and the **end**. Local variables can be declared within a compound statement, as we have seen in Section 5.2.1. A compound statement of the form **begin atomic** _. . ._ **end** ensures that all the statements contained within it are executed as a single transaction.

SQL:1999 supports the **while** statements and the **repeat** statements by the following syntax:

**while** _boolean expression_ **do** 
_sequence of statements_;
**end while**

**repeat** 
_sequence of statements_;
**until** _boolean expression_ 
**end repeat**

There is also a **for** loop that permits iteration over all results of a query:

**declare** _n_ **integer default** 0; 
**for** _r_ **as**
**select** _budget_ **from** _department_ 
**where** _dept name_ \= ‘Music‘
**do** 
**set** _n_ \= _n_− _r.budget_
**end for**

The program fetches the query results one row at a time into the **for** loop variable (_r_, in the above example). The statement **leave** can be used to exit the loop, while **iterate** starts on the next tuple, from the beginning of the loop, skipping the remaining statements.

The conditional statements supported by SQL include if-then-else statements by using this syntax:

**if** _boolean expression_ 
**then** _statement or compound statement_
**elseif** _boolean expression_ 
**then** _statement or compound statement_
**else** _statement or compound statement_ 
**end if**  

– – Registers a student after ensuring classroom capacity is not exceeded – – Returns 0 on success, and -1 if capacity is exceeded. **create function** _registerStudent_(

**in** _s id_ **varchar**(5), 
**in** _s courseid_ **varchar** (8), 
**in** _s secid_ **varchar** (8), 
**in** _s semester_ **varchar** (6), 
**in** _s year_ **numeric** (4,0), 
**out** _errorMsg_ **varchar**(100)
**returns integer** 
**begin**
**declare** _currEnrol_ **int**; **select count**(\*) **into** _currEnrol_
**from** _takes_ 
**where** _course id_ \= _s courseid_ 
**and** _sec id_ \= _s secid_**and** _semester_ \= _s semester_ 
**and** _year_ \= _s year_;
**declare** _limit_ **int**; 
**select** _capacity_ **into** _limit_
**from** _classroom_ **natural join** _section_ 
**where** _course id_ \= _s courseid_ **and** _sec id_ \= _s secid_
**and** _semester_ \= _s semester_ **and** _year_ \= _s year_; 
**if** (_currEnrol < limit_)
**begin** 
**insert into** _takes_ **values**
(_s id_, _s courseid_, _s secid_, _s semester_, _s year_, null); 
**return**(0);
**end** 
– – Otherwise, section capacity limit already reached 
**set** _errorMsg_ \= ’Enrollment limit reached for course ’ 
|| _s courseid_|| ’ section ’ || _s secid_; 
**return**(-1);
**end**;

**Figure 5.7** Procedure to register a student for a course section.

SQL also supports a case statement similar to the C/C++ language case statement (in addition to case expressions, which we saw in Chapter 3).

Figure 5.7 provides a larger example of the use of procedural constructs in SQL. The function _registerStudent_ defined in the figure, registers a student in a course section, after verifying that the number of students in the section does not exceed the capacity of the room allocated to the section. The function returns an error code, with a value greater than or equal to 0 signifying success, and a negative value signifying an error condition, and a message indicating the reason for the failure is returned as an **out** parameter.  

**NONSTANDARD SYNTAX FOR PROCEDURES AND FUNCTIONS**

Although the SQL standard defines the syntax for procedures and functions, most databases do not follow the standard strictly, and there is considerable variation in the syntax supported. One of the reasons for this situation is that these databases typically introduced support for procedures and functions before the syntax was standardized, and they continue to support their original syntax. It is not possible to list the syntax supported by each database here, but we illustrate a few of the differences in the case of Oracle’s PL/SQL, by showing below a version of the function from Figure 5.5, as it would be defined in PL/SQL.

**create or replace function** _dept count_(_dept name_ **in** _instructor.dept name%type_) 
**return integer as**
_d count_ **integer**; 
**begin**
**select count**(\*) **into** _d count_ 
**from** _instructor_ 
**where** _instructor.dept name_ \= _dept name_; 
**return** _d count_;
**end**;

While the two versions are similar in concept, there are a number of minor syntactic differences, some of which are evident when comparing the two versions of the function. Although not shown here, the syntax for control flow in PL/SQL also has several differences from the syntax presented here.

Observe that PL/SQL allows a type to be specified as the type of an attribute of a relation, by adding the suffix _%type_. On the other hand, PL/SQL does not directly support the ability to return a table, although there is an indirect way of implementing this functionality by creating a table type. The procedural languages supported by other databases also have a number of syntactic and semantic differences. See the respective language references for more information.

The SQL procedural language also supports the signaling of **exception conditions**, and declaring of **handlers** that can handle the exception, as in this code:

**declare** _out of classroom seats_ **condition** 
**declare exit handler for** _out of classroom seats_ 
**begin** 
_sequence of statements_ 
**end**  

The statements between the **begin** and the **end** can raise an exception by executing **signal** _out of classroom seats_. The handler says that if the condition arises, the action to be taken is to exit the enclosing **begin end** statement. Alternative actions would be **continue**, which continues execution from the next statement following the one that raised the exception. In addition to explicitly defined conditions, there are also predefined conditions such as **sqlexception**, **sqlwarning**, and **not found**.

## External Language Routines

Although the procedural extensions to SQL can be very useful, they are unfortunately not supported in a standard way across databases. Even the most basic features have different syntax or semantics in different database products. As a result, programmers have to essentially learn a new language for each database product. An alternative that is gaining in support is to define procedures in an imperative programming language, but allow them to be invoked from SQL queries and trigger definitions.

SQL allows us to define functions in a programming language such as Java, C#, C or C++. Functions defined in this fashion can be more efficient than functions defined in SQL, and computations that cannot be carried out in SQL can be executed by these functions.

External procedures and functions can be specified in this way (note that the exact syntax depends on the specific database system you use):

**create procedure** dept count proc( **in** _dept name_ **varchar**(20), **out** count **integer**)
**language** C 
**external name** ’/usr/avi/bin/dept count proc’
**create function** dept count (_dept name_ **varchar**(20)) 
**returns** integer 
**language** C 
**external name** ’/usr/avi/bin/dept count’

In general, the external language procedures need to deal with null values in parameters (both **in** and **out**) and return values. They also need to communicate failure/success status, to deal with exceptions. This information can be communicated by extra parameters: an **sqlstate** value to indicate failure/success status, a parameter to store the return value of the function, and indicator variables for each parameter/function result to indicate if the value is null. Other mechanisms are possible to handle null values, for example by passing pointers instead of values. The exact mechanisms depend on the database. However, if a function does not deal with these situations, an extra line **parameter style general** can be added to the declaration to indicate that the external procedures/functions take only the arguments shown and do not handle null values or exceptions.

Functions defined in a programming language and compiled outside the database system may be loaded and executed with the database-system code.  

However, doing so carries the risk that a bug in the program can corrupt the database internal structures, and can bypass the access-control functionality of the database system. Database systems that are concerned more about efficient per- formance than about security may execute procedures in such a fashion. Database systems that are concerned about security may execute such code as part of a sep- arate process, communicate the parameter values to it, and fetch results back, via interprocess communication. However, the time overhead of interprocess communication is quite high; on typical CPU architectures, tens to hundreds of thousands of instructions can execute in the time taken for one interprocess com- munication.

If the code is written in a “safe” language such as Java or C#, there is another possibility: executing the code in a **sandbox** within the database query execution process itself. The sandbox allows the Java or C# code to access its own memory area, but prevents the code from reading or updating the memory of the query execution process, or accessing files in the file system. (Creating a sandbox is not possible for a language such as C, which allows unrestricted access to memory through pointers.) Avoiding interprocess communication reduces function call overhead greatly.

Several database systems today support external language routines running in a sandbox within the query execution process. For example, Oracle and IBM DB2 allow Java functions to run as part of the database process. Microsoft SQL Server allows procedures compiled into the Common Language Runtime (CLR) to execute within the database process; such procedures could have been written, for example, in C# or Visual Basic. PostgreSQL allows functions defined in several languages, such as Perl, Python, and Tcl.
