---
title: XML Document Schema
weight: 15
---

XML Document Schema## XML Document Schema

Databases have schemas, which are used to constrain what information can be stored in the database and to constrain the data types of the stored information. In contrast, by default, XML documents can be created without any associated schema: an element may then have any subelement or attribute. While such freedom may occasionally be acceptable given the self-describing nature of the data format, it is not generally useful when XML documents must be processed automatically as part of an application, or even when large amounts of related data are to be formatted in XML.

Here, we describe the first schema-definition language included as part of the XML standard, the Document Type Definition,_ as well as its more recently defined replacement, XML Schema. Another XML schema-definition language called Relax NG is also in use, but we do not cover it here; for more information on Relax NG see the references in the bibliographical notes section.

### Document Type Definition

The **document type definition** (**DTD**) is an optional part of an XML document. The main purpose of a DTD is much like that of a schema: to constrain and type the information present in the document. However, the DTD does not in fact constrain types in the sense of basic types like integer or string. Instead, it constrains only the appearance of subelements and attributes within an element. The DTD is primarily a list of rules for what pattern of subelements may appear within an element. Figure 23.9 shows a part of an example DTD for a university information document; the XML document in Figure 23.1 conforms to this DTD.

Each declaration is in the form of a regular expression for the subelements of an element. Thus, in the DTD in Figure 23.9, a university element consists of one or more course, department, or instructor elements; the | operator specifies “or”  


```sql
<_!DOCTYPE university [ <_!ELEMENT university ( (department|course|instructor|teaches)+)> <_!ELEMENT department ( dept name, building, budget)> <_!ELEMENT course ( course id, title, dept name, credits)> <_!ELEMENT instructor (IID, name, dept name, salary)> <_!ELEMENT teaches (IID, course id)> <_!ELEMENT dept name( #PCDATA )> <_!ELEMENT building( #PCDATA )> <_!ELEMENT budget( #PCDATA )> <_!ELEMENT course id ( #PCDATA )> <_!ELEMENT title ( #PCDATA )> <_!ELEMENT credits( #PCDATA )> <_!ELEMENT IID( #PCDATA )> <_!ELEMENT name( #PCDATA )> <_!ELEMENT salary( #PCDATA )>

] >
```
while the + operator specifies “one or more.” Although not shown here, the ∗ operator is used to specify “zero or more,” while the ? operator is used to specify an optional element (that is, “zero or one”).

The course element contains subelements course id, title, dept name, and credits (in that order). Similarly, department and instructor have the attributes of their relational schema defined as subelements in the DTD.

Finally, the elements course id, title, dept name, credits, building, budget, IID, name, and salary are all declared to be of type #PCDATA. The keyword #PCDATA indicates text data; it derives its name, historically, from “parsed character data.” Two other special type declarations are empty, which says that the element has no contents, and any, which says that there is no constraint on the subelements of the element; that is, any elements, even those not mentioned in the DTD, can occur as subelements of the element. The absence of a declaration for an element is equivalent to explicitly declaring the type as any.

The allowable attributes for each element are also declared in the DTD. Unlike subelements, no order is imposed on attributes. Attributes may be specified to be of type CDATA, ID, IDREF, or IDREFS; the type CDATA simply says that the attribute contains character data, while the other three are not so simple; they are explained in more detail shortly. For instance, the following line from a DTD specifies that element course has an attribute of type course id, and a value must be present for this attribute:

<_!ATTLIST course course id CDATA #REQUIRED>

Attributes must have a type declaration and a default declaration. The default declaration can consist of a default value for the attribute or #REQUIRED, meaning  



<_!DOCTYPE university-3 [ <_!ELEMENT university ( (department|course|instructor)+)> <_!ELEMENT department ( building, budget )> <_!ATTLIST department

dept name ID #REQUIRED >

<_!ELEMENT course (title, credits )> <_!ATTLIST course

course id ID #REQUIRED dept name IDREF #REQUIRED instructors IDREFS #IMPLIED >

<_!ELEMENT instructor ( name, salary )> <_!ATTLIST instructor

IID ID #REQUIRED >

dept name IDREF #REQUIRED >

· · · declarations for title, credits, building, budget, name and salary · · ·

] >



that a value must be specified for the attribute in each element, or #IMPLIED, meaning that no default value has been provided, and the document may omit this attribute. If an attribute has a default value, for every element that does not specify a value for the attribute, the default value is filled in automatically when the XML document is read.

An attribute of type ID provides a unique identifier for the element; a value that occurs in an ID attribute of an element must not occur in any other element in the same document. At most one attribute of an element is permitted to be of type ID. (We renamed the attribute ID of the instructor relation to IID in the XML representation, in order to avoid confusion with the type ID.)

An attribute of type IDREF is a reference to an element; the attribute must contain a value that appears in the ID attribute of some element in the document. The type IDREFS allows a list of references, separated by spaces.

Figure 23.10 shows an example DTD in which identifiers of course, department and instructor are represented by ID attributes, and relationships between them are represented by IDREF and IDREFS attributes. The course elements use course id as their identifier attribute; to do so, course id has been made an attribute of course instead of a subelement. Additionally, each course element also contains an IDREF of the department corresponding to the course, and an IDREFS attribute instructors identifying the instructors who teach the course. The department elements have an identifier attribute called dept name. The instructor elements have an identifier attribute called IID, and an IDREF attribute dept name identifying the department to which the instructor belongs.  



<university-3>

<department dept name=“Comp. Sci.”> <building> Taylor </building>

<budget> 100000 </budget> </department> <department dept name=“Biology”>

<building> Watson </building>

<budget> 90000 </budget> </department> <course course id=“CS-101” dept name=“Comp. Sci”

instructors=“10101 83821”> <title> Intro. to Computer Science </title>

<credits> 4 </credits>

</course>

<course course id=“BIO-301” dept name=“Biology” instructors=“76766”>

<title> Genetics </title>

<credits> 4 </credits>

</course>

<instructor IID=“10101” dept name=“Comp. Sci.”> <name> Srinivasan </name>

<salary> 65000 </salary>

</instructor> <instructor IID=“83821” dept name=“Comp. Sci.”>

<name> Brandt </name>

<salary> 72000 </salary>

</instructor> <instructor IID=“76766” dept name=“Biology”>

<name> Crick </name>

<salary> 72000 </salary>

</instructor> </university-3>



The ID and IDREF attributes serve the same role as reference mechanisms in object-oriented and object-relational databases, permitting the construction of complex data relationships.

Document type definitions are strongly connected to the document formatting heritage of XML. Because of this, they are unsuitable in many ways for serving as the type structure of XML for data-processing applications. Nevertheless, a number of data exchange formats have been defined in terms of DTDs, since they were part of the original standard. Here are some of the limitations of DTDs as a schema mechanism:  


- Individual text elements and attributes cannot be typed further. For instance, the element balance cannot be constrained to be a positive number. The lack of such constraints is problematic for data processing and exchange applications, which must then contain code to verify the types of elements and attributes.

- It is difficult to use the DTD mechanism to specify unordered sets of subele- ments. Order is seldom important for data exchange (unlike document lay- out, where it is crucial). While the combination of alternation (the | operation) and the ∗ or the + operation as in Figure 23.9 permits the specification of unordered collections of tags, it is much more difficult to specify that each tag may only appear once.

- There is a lack of typing in IDs and IDREFSs. Thus, there is no way to specify the type of element to which an IDREF or IDREFS attribute should refer. As a result, the DTD in Figure 23.10 does not prevent the “dept name” attribute of a course element from referring to other courses, even though this makes no sense.

### XML Schema

An effort to redress the deficiencies of the DTD mechanism resulted in the devel- opment of a more sophisticated schema language, **XML Schema**. We provide a brief overview of XML Schema, and then we list some areas in which it improves DTDs.

XML Schema defines a number of built-in types such as string, integer, decimal date, and boolean. In addition, it allows user-defined types; these may be simple types with added restrictions, or complex types constructed using constructors such as complexType and sequence.

Figures 23.12 and 23.13 show how the DTD in Figure 23.9 can be represented by XML Schema; we describe below XML Schema features illustrated by the figures.

The first thing to note is that schema definitions in XML Schema are themselves specified in XML syntax, using a variety of tags defined by XML Schema. To avoid conflicts with user-defined tags, we prefix the XML Schema tag with the namespace prefix “xs:”; this prefix is associated with the XML Schema namespace by the xmlns:xs specification in the root element:

**<xs:schema xmlns:xs=“http://www.w3.org/2001/XMLSchema”>**

Note that any namespace prefix could be used in place of xs; thus we could replace all occurrences of “xs:” in the schema definition with “xsd:” without changing the meaning of the schema definition. All types defined by XML Schema must be prefixed by this namespace prefix.

The first element is the root element university, whose type is specified to be UniversityType, which is declared later. The example then defines the types of elements department, course, instructor, and teaches. Note that each of these  


<xs:schema xmlns:xs=“http://www.w3.org/2001/XMLSchema”> <xs:element name=“university” type=“universityType” /> <xs:element name=“department”>

<xs:complexType>

<xs:sequence>

<xs:element name=“dept name” type=“xs:string”/> <xs:element name=“building” type=“xs:string”/> <xs:element name=“budget” type=“xs:decimal”/>

</xs:sequence>

</xs:complexType>

</xs:element> <xs:element name=“course”>

<xs:element name=“course id” type=“xs:string”/> <xs:element name=“title” type=“xs:string”/> <xs:element name=“dept name” type=“xs:string”/> <xs:element name=“credits” type=“xs:decimal”/>

</xs:element> <xs:element name=“instructor”>

<xs:complexType>

<xs:sequence>

<xs:element name=“IID” type=“xs:string”/> <xs:element name=“name” type=“xs:string”/> <xs:element name=“dept name” type=“xs:string”/> <xs:element name=“salary” type=“xs:decimal”/>

</xs:sequence>

</xs:complexType>

</xs:element>

continued in Figure 23.13.



is specified by an element with tag xs:element, whose body contains the type definition.

The type of department is defined to be a complex type, which is further specified to consist of a sequence of elements dept name, building, and budget. Any type that has either attributes or nested subelements must be specified to be a complex type.

Alternatively, the type of an element can be specified to be a predefined type by the attribute type; observe how the XML Schema types xs:string and xs:decimal are used to constrain the types of data elements such as dept name and credits.

Finally the example defines the type UniversityType as containing zero or more occurrences of each of department, course, instructor, and teaches. Note the use of ref to specify the occurrence of an element defined earlier. XML Schema can define the minimum and maximum number of occurrences of subelements by  



<xs:element name=“teaches”> <xs:complexType>

<xs:sequence>

<xs:element name=“IID” type=“xs:string”/> <xs:element name=“course id” type=“xs:string”/>

</xs:sequence>

</xs:complexType>

</xs:element> <xs:complexType name=“UniversityType”>

<xs:sequence>

<xs:element ref=“department” minOccurs=“0” maxOccurs=“unbounded”/>

<xs:element ref=“course” minOccurs=“0” maxOccurs=“unbounded”/>

<xs:element ref=“instructor” minOccurs=“0” maxOccurs=“unbounded”/>

<xs:element ref=“teaches” minOccurs=“0” maxOccurs=“unbounded”/>

</xs:sequence>

</xs:complexType>

</xs:schema>

**Figure 23.13** Continuation of Figure 23.12.

using minOccurs and maxOccurs. The default for both minimum and maximum occurrences is 1, so these have to be specified explicitly to allow zero or more department, course, instructor, and teaches elements.

Attributes are specified using the xs:attribute tag. For example, we could have defined dept name as an attribute by adding:

<xs:attribute name = “dept name”/>

within the declaration of the department element. Adding the attribute use = “required” to the above attribute specification declares that the attribute must be specified, whereas the default value of use is optional. Attribute specifications would appear directly under the enclosing complexType specification, even if elements are nested within a sequence specification.

We can use the xs:complexType element to create named complex types; the syntax is the same as that used for the xs:complexType element in Figure 23.12, except that we add an attribute name = typeName to the xs:complexType element, where typeName is the name we wish to give to the type. We can then use the named type to specify the type of an element using the type attribute, just as we used xs:decimal and xs:string in our example.

In addition to defining types, a relational schema also allows the specification of constraints. XML Schema allows the specification of keys and key references,  

### 23.3 XML Document Schema 

corresponding to the primary-key and foreign-key definition in SQL. In SQL, a primary-key constraint or unique constraint ensures that the attribute values do not recur within the relation. In the context of XML, we need to specify a scope within which values are unique and form a key. The selector is a path expression that defines the scope for the constraint, and field declarations specify the elements or attributes that form the key.1 To specify that dept name forms a key for department elements under the root university element, we add the following constraint specification to the schema definition:
```xml
<xs:key name = “deptKey”> <xs:selector xpath = “/university/department”/> <xs:field xpath = “dept name”/>

</xs:key>

Correspondingly a foreign-key constraint from course to department may be defined as follows:

<xs: name = “courseDeptFKey” refer=“deptKey”> <xs:selector xpath = “/university/course”/> <xs:field xpath = “dept name”/>

</xs:keyref>
```
Note that the refer attribute specifies the name of the key declaration that is being referenced, while the field specification identifies the referring attributes.

XML Schema offers several benefits over DTDs, and is widely used today. Among the benefits that we have seen in the examples above are these:

- It allows the text that appears in elements to be constrained to specific types, such as numeric types in specific formats or complex types such as sequences of elements of other types.

- It allows user-defined types to be created.

- It allows uniqueness and foreign-key constraints.

- It is integrated with namespaces to allow different parts of a document to conform to different schemas.

In addition to the features we have seen, XML Schema supports several other features that DTDs do not, such as these:

- It allows types to be restricted to create specialized types, for instance by specifying minimum and maximum values.

- It allows complex types to be extended by using a form of inheritance.

1We use simple path expressions here that are in a familiar syntax. XML has a rich syntax for path expressions, called XPath, which we explore in Section 23.4.2.  



Our description of XML Schema is just an overview; to learn more about XML Schema, see the references in the bibliographical notes.

