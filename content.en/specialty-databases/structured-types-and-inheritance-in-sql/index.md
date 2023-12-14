---
title: Structured Types and Inheritance in SQL
weight: 3
---

Structured Types and Inheritance in SQL## Structured Types and Inheritance in SQL

Before SQL:1999, the SQL type system consisted of a fairly simple set of predefined types. SQL:1999 added an extensive type system to SQL, allowing structured types and type inheritance.

### Structured Types

Structured types allow composite attributes of E-R designs to be represented directly. For instance, we can define the following structured type to represent a composite attribute name with component attribute firstname and lastname:
```sql

create type Name as
 (firstname varchar(20),
 lastname varchar(20))
 final;

```

Similarly, the following structured type can be used to represent a composite attribute address:
```sql
create type Address as
 (street varchar(20),
city varchar(20),
zipcode varchar(9)) 
not final;
```
Such types are called **user-defined** types in SQL2. The above definition corre- sponds to the E-R diagram in Figure 7.4. The **final** and **not final** specifications are related to subtyping, which we describe later, in Section 22.3.2.3

We can now use these types to create composite attributes in a relation, by simply declaring an attribute to be of one of these types. For example, we could create a table person as follows:

2To illustrate our earlier note about commercial implementations defining their syntax before the standards were developed, we point out that Oracle requires the keyword **object** following **as**. 3The **final** specification for Name indicates that we cannot create subtypes for name, whereas the **not final** specification for Address indicates that we can create subtypes of address.  

```sql
create table person (
     name Name,
     address Address, 
     dateOfBirth date);
```

The components of a composite attribute can be accessed using a “dot” no- tation; for instance name.firstname returns the firstname component of the name attribute. An access to attribute name would return a value of the structured type Name.

We can also create a table whose rows are of a user-defined type. For example, we could define a type PersonType and create the table person as follows:4

```sql
create type PersonType as ( 
    name Name,
    address Address,
    dateOfBirth date) 
    not final
create table person of PersonType;
```

An alternative way of defining composite attributes in SQL is to use unnamed **row types**. For instance, the relation representing person information could have been created using row types as follows:
```sql
create table person r ( 
    name row (firstname varchar(20),
    lastname varchar(20)), 
    address row (street varchar(20),
    city varchar(20),
    zipcode varchar(9)),
dateOfBirth date);
```
This definition is equivalent to the preceding table definition, except that the attributes name and address have unnamed types, and the rows of the table also have an unnamed type.

The following query illustrates how to access component attributes of a com- posite attribute. The query finds the last name and city of each person.
```sql
select name.lastname, address.city
 from person;
```

A structured type can have **methods** defined on it. We declare methods as part of the type definition of a structured type:

4Most actual systems, being case insensitive, would not permit name to be used both as an attribute name and a data type.  

```sql
create type PersonType as (
     name Name, 
     address Address,
     dateOfBirth date) 
     not final
method ageOnDate(onDate date) returns interval year;
```

We create the method body separately:
```sql
create instance method ageOnDate(onDate date)
returns interval year 
for PersonType
begin
 return onDate − self.dateOfBirth;
end
```
Note that the **for** clause indicates which type this method is for, while the keyword **instance** indicates that this method executes on an instance of the Person type. The variable **self** refers to the Person instance on which the method is invoked. The body of the method can contain procedural statements, which we saw earlier in Section 5.2. Methods can update the attributes of the instance on which they are executed.

Methods can be invoked on instances of a type. If we had created a table person of type PersonType, we could invoke the method ageOnDate() as illustrated below, to find the age of each person.
```sql
select name.lastname, ageOnDate(**current date**) **from** person;
```
In SQL:1999, **constructor functions** are used to create values of structured types. A function with the same name as a structured type is a constructor function for the structured type. For instance, we could declare a constructor for the type Name like this:
```sql
create function Name (firstname varchar(20), lastname varchar(20)) returns Name 
begin
set self.firstname = firstname;
set self.lastname = lastname;
end
```
We can then use **new** Name(’John’, ’Smith’) to create a value of the type Name. We can construct a row value by listing its attributes within parentheses. For instance, if we declare an attribute name as a row type with components firstname  

and lastname we can construct this value for it: (’Ted’, ’Codd’) without using a constructor.

By default every structured type has a constructor with no arguments, which sets the attributes to their default values. Any other constructors have to be created explicitly. There can be more than one constructor for the same structured type; although they have the same name, they must be distinguishable by the number of arguments and types of their arguments.

The following statement illustrates how we can create a new tuple in the Person relation. We assume that a constructor has been defined for Address, just like the constructor we defined for Name.

```sql
insert into Person values

(new Name(’John’, ’Smith’),
 new Address(’20 Main St’, ’New York’, ’11001’), **date** ’1960-8-22’);
```
###  Type Inheritance

Suppose that we have the following type definition for people:

**create type** Person (name **varchar**(20), address **varchar**(20));

We may want to store extra information in the database about people who are students, and about people who are teachers. Since students and teachers are also people, we can use inheritance to define the student and teacher types in SQL:

**create type** Student **under** Person (degree **varchar**(20), department **varchar**(20));

**create type** Teacher **under** Person (salary **integer**, department **varchar**(20));

Both Student and Teacher inherit the attributes of Person—namely, name and address. Student and Teacher are said to be subtypes of Person, and Person is a supertype of Student, as well as of Teacher.

Methods of a structured type are inherited by its subtypes, just as attributes are. However, a subtype can redefine the effect of a method by declaring the method again, using **overriding method** in place of **method** in the method dec- laration.  

### Structured Types and Inheritance in SQL 953

The SQL standard requires an extra field at the end of the type definition, whose value is either **final** or **not final.** The keyword **final** says that subtypes may not be created from the given type, while **not final** says that subtypes may be created.

Now suppose that we want to store information about teaching assistants, who are simultaneously students and teachers, perhaps even in different depart- ments. We can do this if the type system supports **multiple inheritance**, where a type is declared as a subtype of multiple types. Note that the SQL standard does not support multiple inheritance, although future versions of the SQL standard may support it, so we discuss the concept here.

For instance, if our type system supports multiple inheritance, we can define a type for teaching assistant as follows:

**create type** TeachingAssistant **under** Student, Teacher;

TeachingAssistant inherits all the attributes of Student and Teacher. There is a problem, however, since the attributes name, address, and department are present in Student, as well as in Teacher.

The attributes name and address are actually inherited from a common source, Person. So there is no conflict caused by inheriting them from Student as well as Teacher. However, the attribute department is defined separately in Student and Teacher. In fact, a teaching assistant may be a student of one department and a teacher in another department. To avoid a conflict between the two occurrences of department, we can rename them by using an **as** clause, as in this definition of the type TeachingAssistant:

**create type** TeachingAssistant **under** Student **with** (department **as** student dept),

Teacher **with** (department **as** teacher dept);

In SQL, as in most other languages, a value of a structured type must have ex- actly one most-specific type._ That is, each value must be associated with one specific type, called its **most-specific type**, when it is created. By means of inheritance, it is also associated with each of the supertypes of its most-specific type. For example, suppose that an entity has the type Person, as well as the type Student. Then, the most-specific type of the entity is Student, since Student is a subtype of Person. However, an entity cannot have the type Student as well as the type Teacher unless it has a type, such as TeachingAssistant, that is a subtype of Teacher, as well as of Student (which is not possible in SQL since multiple inheritance is not supported by SQL). 

