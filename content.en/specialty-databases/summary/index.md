---
title: Summary
weight: 11
---

Summary## Summary

- The object-relational data model extends the relational data model by pro- viding a richer type system including collection types and object orientation.

- Collection types include nested relations, sets, multisets, and arrays, and the object-relational model permits attributes of a table to be collections.

- Object orientation provides inheritance with subtypes and subtables, as well as object (tuple) references.

- The SQL standard includes extensions of the SQL data-definition and query language to deal with new data types and with object orientation. These include support for collection-valued attributes, inheritance, and tuple ref- erences. Such extensions attempt to preserve the relational foundations— in particular, the declarative access to data—while extending the modeling power.

- Object-relational database systems (that is, database systems based on the object-relation model) provide a convenient migration path for users of rela- tional databases who wish to use object-oriented features.

- Persistent extensions to C++ and Java integrate persistence seamlessly and orthogonally with existing programming language constructs and so are easy to use.

- The ODMG standard defines classes and other constructs for creating and ac- cessing persistent objects from C++, while the JDO standard provides equiv- alent functionality for Java.

- Object-relational mapping systems provide an object view of data that is stored in a relational database. Objects are transient, and there is no notion of persistent object identity. Objects are created on-demand from relational data, and updates to objects are implemented by updating the relational data. Object-relational mapping systems have been widely adopted, unlike the more limited adoption of persistent programming languages.

- We discussed differences between persistent programming languages and object-relational systems, and we mention criteria for choosing between them.

**Review Terms**

- Nested relations 
- Nested relational model 
- Complex types 
- Collection types 
- Large object types

- Sets 
- Arrays 
- Multisets 
- Structured types 
- Methods  
- Row types 
- Constructors 
- Inheritance

    ◦ Single inheritance

    ◦ Multiple inheritance

- Type inheritance 
- Most-specific type 
- Table inheritance 
- Subtable 
- Overlapping subtables 
- Reference types 
- Scope of a reference 
- Self-referential attribute 
- Path expressions 
- Nesting and unnesting 
- SQL functions and procedures

- Persistent programming languages

- Persistence by

    ◦ Class

    ◦ Creation

    ◦ Marking

    ◦ Reachability

- ODMG C++ binding - ObjectStore - JDO

    ◦ Persistence by reachability

    ◦ Roots

    ◦ Hollow objects

- Object-relational mapping

# Practice Exercises

**22.1** A car-rental company maintains a database for all vehicles in its current fleet. For all vehicles, it includes the vehicle identification number, license number, manufacturer, model, date of purchase, and color. Special data are included for certain types of vehicles:

- Trucks: cargo capacity.

- Sports cars: horsepower, renter age requirement.

- Vans: number of passengers.

- Off-road vehicles: ground clearance, drivetrain (four- or two-wheel drive).

Construct an SQL schema definition for this database. Use inheritance where appropriate.

**22.2** Consider a database schema with a relation Emp whose attributes are as shown below, with types specified for multivalued attributes.

Emp = (ename, ChildrenSet **multiset**(Children), SkillSet **multiset**(Skills)) Children = (name, birthday) Skills = (type, ExamSet **setof**(Exams)) Exams = (year, city)  

a. Define the above schema in SQL, with appropriate types for each attribute.

b. Using the above schema, write the following queries in SQL. i. Find the names of all employees who have a child born on or

after January 1, 2000. ii. Find those employees who took an examination for the skill type

“typing” in the city “Dayton”. iii. List all skill types in the relation Emp.

**22.3** Consider the E-R diagram in Figure 22.5, which contains composite, mul- tivalued, and derived attributes.

a. Give an SQL schema definition corresponding to the E-R diagram.

b. Give constructors for each of the structured types defined above.

**22.4** Consider the relational schema shown in Figure 22.6.

a. Give a schema definition in SQLcorresponding to the relational schema, but using references to express foreign-key relationships.

b. Write each of the queries given in Exercise 6.13 on the above schema, using SQL.

![E-R diagram with composite, multivalued, and derived attributes.](figure-22_5.png)
 
```sql
employee (person name, street, city) 
works (person name, company name, salary) 
company (company name, city) 
manages (person name, manager name)
```

**Figure 22.6** Relational database for Practice Exercise 22.4.

**22.5** Suppose that you have been hired as a consultant to choose a database system for your client’s application. For each of the following applications, state what type of database system (relational, persistent programming language–based OODB, object relational; do not specify a commercial product) you would recommend. Justify your recommendation.

a. A computer-aided design system for a manufacturer of airplanes.

b. A system to track contributions made to candidates for public office.

c. An information system to support the making of movies.

**22.6** How does the concept of an object in the object-oriented model differ from the concept of an entity in the entity-relationship model?

**Exercises**

**22.7** Redesign the database of Practice Exercise 22.2 into first normal form and fourth normal form. List any functional or multivalued dependencies that you assume. Also list all referential-integrity constraints that should be present in the first and fourth normal form schemas.

**22.8** Consider the schema from Practice Exercise 22.2.

a. Give SQL DDL statements to create a relation EmpA which has the same information as Emp, but where multiset-valued attributes Chil- drenSet, SkillsSet and ExamsSet are replaced by array-valued at- tributes ChildrenArray, SkillsArray and ExamsArray.

b. Write a query to convert data from the schema of Emp to that of E€, with the array of children sorted by birthday, the array of skills by the skill type and the array of exams by the year.

c. Write an SQL statement to update the Emp relation by adding a child Jeb, with a birthdate of February 5, 2001, to the employee named George.

d. Write an SQL statement to perform the same update as above but on the EmpA relation. Make sure that the array of children remains sorted by year.  

**Exercises**

![Specialization and generalization.](Figure-22_7.png)

**22.9** Consider the schemas for the table people, and the tables students and teachers, which were created under people, in Section 22.4. Give a relational schema in third normal form that represents the same information. Re- call the constraints on subtables, and give all constraints that must be imposed on the relational schema so that every database instance of the relational schema can also be represented by an instance of the schema with inheritance.

**22.10** Explain the distinction between a type x and a reference type **ref**(x). Under what circumstances would you choose to use a reference type?

**22.11** Consider the E-R diagram in Figure 22.7, which contains specializations, using subtypes and subtables.

a. Give an SQL schema definition of the E-R diagram.

b. Give an SQL query to find the names of all people who are not secretaries.

c. Give an SQL query to print the names of people who are neither employees nor students.

d. Can you create a person who is an employee and a student with the schema you created? Explain how, or explain why it is not possible.

**22.12** Suppose a JDO database had an object A, which references object B, which in turn references object C . Assume all objects are on disk initially. Suppose a program first dereferences A, then dereferences B by following the reference from A, and then finally dereferences C . Show the objects that are represented in memory after each dereference, along with their state (hollow or filled, and values in their reference fields).

## Summary 

for all accounts of a customer is a major challenge. XML-based mediation ad- dresses the problem by extracting an XML representation of account information from the respective Web sites of the financial institutions where the individual holds accounts. This information may be extracted easily if the institution exports it in a standard XML format, for example, as a Web service. For those that do not, wrapper software is used to generate XML data from HTML Web pages returned by the Web site. Wrapper applications need constant maintenance, since they depend on formatting details of Web pages, which change often. Nevertheless, the value provided by mediation often justifies the effort required to develop and maintain wrappers.

Once the basic tools are available to extract information from each source, a mediator application is used to combine the extracted information under a single schema. This may require further transformation of the XML data from each site, since different sites may structure the same information differently. They may also use different names for the same information (for instance, acct number and account id), or may even use the same name for different information. The mediator must decide on a single schema that represents all required information, and must provide code to transform data between different representations. Such issues are discussed in more detail in Section 19.8, in the context of distributed databases. XML query languages such as XSLT and XQuery play an important role in the task of transformation between different XML representations.



- Like the Hyper-Text Markup Language (HTML) on which the Web is based, the Extensible Markup Language (XML) is derived from the Standard Gener- alized Markup Language (SGML). XML was originally intended for providing functional markup for Web documents, but has now become the de facto standard data format for data exchange between applications.

- XML documents contain elements with matching starting and ending tags indicating the beginning and end of an element. Elements may have subele- ments nested within them, to any level of nesting. Elements may also have attributes. The choice between representing information as attributes and subelements is often arbitrary in the context of data representation.

- Elements may have an attribute of type ID that stores a unique identifier for the element. Elements may also store references to other elements by using attributes of type IDREF. Attributes of type IDREFS can store a list of references.

- Documents optionally may have their schema specified by a Document Type Declaration (DTD). The DTD of a document specifies what elements may occur, how they may be nested, and what attributes each element may have. Although DTDs are widely used, they have several limitations. For instance, they do not provide a type system.

- XML Schema is now the standard mechanism for specifying the schema of an XML document. It provides a large set of basic types, as well as constructs for  



creating complex types and specifying integrity constraints, including key constraints and foreign-key (keyref) constraints.

- XML data can be represented as tree structures, with nodes corresponding to elements and attributes. Nesting of elements is reflected by the parent-child structure of the tree representation.

- Path expressions can be used to traverse the XML tree structure and locate data. XPath is a standard language for path expressions, and allows required elements to be specified by a file-system-like path, and additionally allows selections and other features. XPath also forms part of other XML query languages.

- The XQuery language is the standard language for querying XML data. It has a structure not unlike SQL, with **for**, **let**, **where**, **order by**, and **return** clauses. However, it supports many extensions to deal with the tree nature of XML and to allow for the transformation of XML documents into other documents with a significantly different structure. XPath path expressions form a part of XQuery. XQuery supports nested queries and user-defined functions.

- The DOM and SAX APIs are widely used for programmatic access to XML data. These APIs are available from a variety of programming languages.

- XML data can be stored in any of several different ways. XML data may also be stored in file systems, or in XML databases, which use XML as their internal representation.

- XML data can be stored as strings in a relational database. Alternatively, relations can represent XML data as trees. As another alternative, XML data can be mapped to relations in the same way that E-R schemas are mapped to relational schemas. Native storage of XML in relational databases is facilitated by adding an **xml** data type to SQL.

- XML is used in a variety of applications, such as storing complex data, ex- change of data between organizations in a standardized form, data mediation, and Web services. Web services provide a remote-procedure call interface, with XML as the mechanism for encoding parameters as well as results.

# Review Terms
• Extensible Markup Language
(XML)
• Hyper-Text Markup Language
(HTML)
• Standard Generalized Markup
Language
• Markup language
• Tags
• Self-documenting
• Element
• Root element
• Nested elements
• Attribute
• Namespace
• Default namespace
• Schema definition
• Document Type Definition
(DTD)
    ◦ ID
    ◦ IDREF and IDREFS
• XML Schema
    ◦ Simple and complex types
    ◦ Sequence type
    ◦ Key and keyref
    ◦ Occurrence constraints
• Tree model of XML data
• Nodes
• Querying and transformation
• Path expressions
• XPath
• XQuery
    ◦ FLWOR expressions
        -for
        -let
        -where
        -order by
        -return
    ◦ Joins
    ◦ Nested FLWOR expression
    ◦ Sorting
• XML API
• Document Object Model (DOM)
• Simple API for XML (SAX)
• Storage of XML data
    ◦ In nonrelational data stores
    ◦ In relational databases
        - Store as string
        - Tree representation
        - Map to relations
        - Publish and shred
        - XML-enabled database
        - Native storage
        - SQL/XML
• XML applications
    ◦ Storing complex data
    ◦ Exchange of data
    ◦ Data mediation
    ◦ SOAP
    ◦ Web services

**Practice Exercises**

**23.1** Give an alternative representation of university information containing the same data as in Figure 23.1, but using attributes instead of subelements. Also give the DTD or XML Schema for this representation.

**23.2** Give the DTD or XML Schema for an XML representation of the following nested-relational schema:

Emp = (ename, ChildrenSet **setof**(Children), SkillsSet **setof**(Skills)) Children = (name, Birthday) Birthday = (day, month, year) Skills = (type, ExamsSet **setof**(Exams)) Exams = (year, city)  

<_!DOCTYPE bibliography [ 
<_!ELEMENT book (title, author+, year, publisher, place?)> <_!ELEMENT article (title, author+, journal, year, number, volume, pages?)> <_!ELEMENT author ( last name, first name) >

<_!ELEMENT title ( #PCDATA )> · · · similar PCDATA declarations for year, publisher, place, journal, year,

number, volume, pages, last name and first name ] >

**Figure 23.16** DTD for bibliographical data.

**23.3** Write a query in XPath on the schema of Practice Exercise 23.2 to list all skill types in Emp.

**23.4** Write a query in XQuery on the XML representation in Figure 23.11 to find the total salary of all instructors in each department.

**23.5** Write a query in XQuery on the XML representation in Figure 23.1 to compute the left outer join of department elements with course elements. (Hint: Use universal quantification.)

**23.6** Write queries in XQuery to output department elements with associated course elements nested within the department elements, given the uni- versity information representation using ID and IDREFS in Figure 23.11.

**23.7** Give a relational schema to represent bibliographical information speci- fied according to the DTD fragment in Figure 23.16. The relational schema must keep track of the order of author elements. You can assume that only books and articles appear as top-level elements in XML documents.

**23.8** Show the tree representation of the XML data in Figure 23.1, and the representation of the tree using nodes and child relations described in Section 23.6.2.

**23.9** Consider the following recursive DTD:

<_!DOCTYPE parts [ 
    <_!ELEMENT part (name, subpartinfo\*)> 
    <_!ELEMENT subpartinfo (part, quantity)> 
    <_!ELEMENT name ( #PCDATA )> 
    <_!ELEMENT quantity ( #PCDATA )>

] >

a. Give a small example of data corresponding to this DTD.

b. Show how to map this DTD to a relational schema. You can assume that part names are unique; that is, wherever a part appears, its subpart structure will be the same.

c. Create a schema in XML Schema corresponding to this DTD.  



