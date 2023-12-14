---
title: Object-Identity and Reference Types in SQL
weight: 6
---

Object-Identity and Reference Types in SQL## Object-Identity and Reference Types in SQL

The SQL standard does not provide any way to update multiset attributes except by assigning a new value. For example, to delete a value v from a multiset attribute A, we would have to set it to (A **except all multiset**[v]).

Object-oriented languages provide the ability to refer to objects. An attribute of a type can be a reference to an object of a specified type. For example, in SQL we can define a type Department with a field name and a field head that is a reference to the type Person, and a table departments of type Department, as follows:

**create type** Department ( name **varchar(20)**, head **ref**(Person) **scope** people);

**create table** departments **of** Department;

Here, the reference is restricted to tuples of the table people. The restriction of the **scope** of a reference to tuples of a table is mandatory in SQL, and it makes references behave like foreign keys.

We can omit the declaration **scope** people from the type declaration and instead make an addition to the **create table** statement:

**create table** departments **of** Department (head **with options scope** people);

The referenced table must have an attribute that stores the identifier of the tuple. We declare this attribute, called the **self-referential attribute**, by adding a **ref is** clause to the **create table** statement:

**create table** people **of** Person **ref is** person id **system generated**;

Here, person id is an attribute name, not a keyword, and the **create table** statement specifies that the identifier is generated automatically by the database.

In order to initialize a reference attribute, we need to get the identifier of the tuple that is to be referenced. We can get the identifier value of a tuple by means of a query. Thus, to create a tuple with the reference value, we may first create the tuple with a null reference and then set the reference separately:  

**insert into** departments **values** (’CS’, null);

**update** departments **set** head \= (**select** p.person id

**from** people **as** p **where** name \= ’John’)

**where** name \= ’CS’;

An alternative to system-generated identifiers is to allow users to generate identifiers. The type of the self-referential attribute must be specified as part of the type definition of the referenced table, and the table definition must specify that the reference is **user generated**:

**create type** Person (name **varchar**(20), address **varchar**(20))

**ref using varchar**(20);

**create table** people **of** Person **ref is** person id **user generated**;

When inserting a tuple in people, we must then provide a value for the identifier:

**insert into** people (person id, name, address) **values** (’01284567’, ’John’, ’23 Coyote Run’);

No other tuple for people or its supertables or subtables can have the same identifier. We can then use the identifier value when inserting a tuple into depart- ments, without the need for a separate query to retrieve the identifier:

**insert into** departments **values** (’CS’, ’01284567’);

It is even possible to use an existing primary-key value as the identifier, by including the **ref from** clause in the type definition:

**create type** Person (name **varchar**(20) **primary key**, address **varchar**(20))

**ref from**(name);

**create table** people **of** Person **ref is** person id **derived**;  

Note that the table definition must specify that the reference is derived, and must still specify a self-referential attribute name. When inserting a tuple for departments, we can then use:

**insert into** departments **values** (’CS’, ’John’);

References are dereferenced in SQL:1999 by the −> symbol. Consider the departments table defined earlier. We can use this query to find the names and addresses of the heads of all departments:

**select** head−>name, head−>address **from** departments;

An expression such as “head−>name” is called a **path expression**. Since head is a reference to a tuple in the people table, the attribute name

in the preceding query is the name attribute of the tuple from the people table. References can be used to hide join operations; in the preceding example, without the references, the head field of department would be declared a foreign key of the table people. To find the name and address of the head of a department, we would require an explicit join of the relations departments and people. The use of references simplifies the query considerably.

We can use the operation **deref** to return the tuple pointed to by a reference, and then access its attributes, as shown below:

**select deref**(head).name **from** departments;

