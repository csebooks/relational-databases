---
title: Structure of XML Data
weight: 14
---

Structure of XML Data## Structure of XML Data

The fundamental construct in an XML document is the **element**. An element is simply a pair of matching start- and end-tags and all the text that appears between them.

XML documents must have a single **root** element that encompasses all other elements in the document. In the example in Figure 23.1, the <university> element forms the root element. Further, elements in an XML document must **nest** properly. For instance:
```sql
<course> . . . <title> . . . </title> . . . </course>

is properly nested, whereas:

<course> . . . <title> . . . </course> . . . </title>
```
is not properly nested. While proper nesting is an intuitive property, we may define it more formally.

Text is said to appear **in the context of** an element if it appears between the start- tag and end-tag of that element. Tags are properly nested if every start-tag has a unique matching end-tag that is in the context of the same parent element.

Note that text may be mixed with the subelements of an element, as in Fig- ure 23.4. As with several other features of XML, this freedom makes more sense in a document-processing context than in a data-processing context, and is not par- ticularly useful for representing more-structured data such as database content in XML.

The ability to nest elements within other elements provides an alternative way to represent information. Figure 23.5 shows a representation of part of the university information from Figure 23.1, but with course elements nested within department elements. The nested representation makes it easy to find all courses offered by a department. Similarly, identifiers of courses taught by an instruc- tor are nested within the instructor elements. If an instructor teaches more than one course, there would be multiple course id elements within the correspond-
```json
_. . ._

<course>

This course is being offered for the first time in 2009. <course id> BIO-399 </course id>

<title> Computational Biology </title>

<dept name> Biology </dept name>

<credits> 3 </credits>

</course>

_. . ._

**Figure 23.4** Mixture of text with subelements.  

**23.2 Structure of XML Data 987**

<university-1>

<department> <dept name> Comp. Sci. </dept name>

<building> Taylor </building>

<budget> 100000 </budget> <course>

<course id> CS-101 </course id>

<title> Intro. to Computer Science </title>

<credits> 4 </credits>

</course>

<course>

<course id> CS-347 </course id>

<title> Database System Concepts </title>

<credits> 3 </credits>

</course>

</department> <department>

<dept name> Biology </dept name>

<building> Watson </building>

<budget> 90000 </budget> <course>

<course id> BIO-301 </course id>

<title> Genetics </title>

<credits> 4 </credits>

</course>

</department> <instructor>

<IID> 10101 </IID>

<name> Srinivasan </name>

<dept name> Comp. Sci. </dept name>

<salary> 65000\. </salary>

<course id> CS-101 </coursr id>

</instructor> </university-1>
```


ing instructor element. Details of instructors Brandt and Crick are omitted from Figure 23.5 for lack of space, but are similar in structure to that for Srinivasan.

Although nested representations are natural in XML, they may lead to re- dundant storage of data. For example, suppose details of courses taught by an instructor are stored nested within the instructor element as shown in Figure 23.6. If a course is taught by more than one instructor, course information such as ti- tle, department, and credits would be stored redundantly with every instructor associated with the course.  
```json
<university-2>

<instructor> <ID> 10101 </ID>

<name> Srinivasan </name>

<dept name> Comp. Sci.</dept name>

<salary> 65000 </salary>

<teaches>

<course>

<course id> CS-101 </course id>

<title> Intro. to Computer Science </title>

<dept name> Comp. Sci. </dept name>

<credits> 4 </credits>

</course>

</teaches>

</instructor>

<instructor> <ID> 83821 </ID>

<name> Brandt </name>

<dept name> Comp. Sci.</dept name>

<salary> 92000 </salary>

<teaches>

<course>

<course id> CS-101 </course id>

<title> Intro. to Computer Science </title>

<dept name> Comp. Sci. </dept name>

<credits> 4 </credits>

</course>

</teaches>

</instructor> </university-2>
```

Nested representations are widely used in XML data interchange applications to avoid joins. For instance, a purchase order would store the full address of sender and receiver redundantly on multiple purchase orders, whereas a normalized representation may require a join of purchase order records with a company address relation to get address information.

In addition to elements, XML specifies the notion of an **attribute**. For instance, the course identifier of a course can be represented as an attribute, as shown in Figure 23.7. The attributes of an element appear as name=value pairs before the closing “>” of a tag. Attributes are strings and do not contain markup. Further- more, attributes can appear only once in a given tag, unlike subelements, which may be repeated. 

_. . ._

<course course id= “CS-101”> <title> Intro. to Computer Science</title>

<dept name> Comp. Sci. </dept name>

<credits> 4 </credits>

</course>

_. . ._



Note that in a document construction context, the distinction between subele- ment and attribute is important—an attribute is implicitly text that does not appear in the printed or displayed document. However, in database and data exchange applications of XML, this distinction is less relevant, and the choice of representing data as an attribute or a subelement is frequently arbitrary. In general, it is advisable to use attributes only to represent identifiers, and to store all other data as subelements.

One final syntactic note is that an element of the form <element></element> that contains no subelements or text can be abbreviated as <element/>; abbrevi- ated elements may, however, contain attributes.

Since XML documents are designed to be exchanged between applications, a **namespace** mechanism has been introduced to allow organizations to specify globally unique names to be used as element tags in documents. The idea of a namespace is to prepend each tag or attribute with a universal resource identifier (for example, a Web address). Thus, for example, if Yale University wanted to ensure that XML documents it created would not duplicate tags used by any business partner’s XML documents, it could prepend a unique identifier with a colon to each tag name. The university may use a Web URL such as:

**http://www.yale.edu**

as a unique identifier. Using long unique identifiers in every tag would be rather inconvenient, so the namespace standard provides a way to define an abbreviation for identifiers.

In Figure 23.8, the root element (university) has an attribute xmlns:yale, which declares that yale is defined as an abbreviation for the URL given above. The abbreviation can then be used in various element tags, as illustrated in the figure.

A document can have more than one namespace, declared as part of the root element. Different elements can then be associated with different namespaces. A default namespace can be defined by using the attribute xmlns instead of xmlns:yale in the root element. Elements without an explicit namespace prefix would then belong to the default namespace.

Sometimes we need to store values containing tags without having the tags interpreted as XML tags. So that we can do so, XML allows this construct:

<_![CDATA[<course> · · ·</course>]]>  

**990 Chapter 23 XML**

<university xmlns:yale=“http://www.yale.edu”> . . ._

<yale:course>

<yale:course id> CS-101 </yale:course id>

<yale:title> Intro. to Computer Science</yale:title>

<yale:dept name> Comp. Sci. </yale:dept name>

<yale:credits> 4 </yale:credits>

</yale:course>

_. . ._

</university>



Because it is enclosed within CDATA, the text <course> is treated as normal text data, not as a tag. The term CDATA stands for character data.

