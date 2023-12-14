---
title: Querying and Transformation
weight: 16
---

Querying and Transformation## Querying and Transformation

Given the increasing number of applications that use XML to exchange, mediate, and store data, tools for effective management of XML data are becoming increas- ingly important. In particular, tools for querying and transformation of XML data are essential to extract information from large bodies of XML data, and to convert data between different representations (schemas) in XML. Just as the output of a relational query is a relation, the output of an XML query can be an XML document. As a result, querying and transformation can be combined into a single tool.

In this section, we describe the XPath and XQuery languages:

- XPath is a language for path expressions and is actually a building block for XQuery.

- XQuery is the standard language for querying XML data. It is modeled after SQL but is significantly different, since it has to deal with nested XML data. XQuery also incorporates XPath expressions.

The XSLT language is another language designed for transforming XML. How- ever, it is used primarily in document-formatting applications, rather in data- management applications, so we do not discuss it in this book.

The tools section at the end of this chapter provides references to software that can be used to execute queries written in XPath and XQuery.

### Tree Model of XML

A **tree model** of XML data is used in all these languages. An XML document is modeled as a **tree**, with **nodes** corresponding to elements and attributes. Element nodes can have child nodes, which can be subelements or attributes of the element. Correspondingly, each node (whether attribute or element), other than the root element, has a parent node, which is an element. The order of elements and attributes in the XML document is modeled by the ordering of children of nodes of the tree. The terms parent, child, ancestor, descendant, and siblings are used in the tree model of XML data.

The text content of an element can be modeled as a text-node child of the element. Elements containing text broken up by intervening subelements can have multiple text-node children. For instance, an element containing “this is a <bold> wonderful </bold> book” would have a subelement child corresponding to the element bold and two text node children corresponding to “this is a” and “book.” Since such structures are not commonly used in data representation, we shall assume that elements do not contain both text and subelements.  



### XPath

XPath addresses parts of an XML document by means of path expressions. The language can be viewed as an extension of the simple path expressions in object- oriented and object-relational databases (see Section 22.6). The current version of the XPath standard is XPath 2.0, and our description is based on this version.

A **path expression** in XPath is a sequence of location steps separated by “/” (instead of the “.” operator that separates location steps in SQL). The result of a path expression is a set of nodes. For instance, on the document in Figure 23.11, the XPath expression:
```xml
/university-3/instructor/name

returns these elements:

<name>Srinivasan</name>

<name>Brandt</name>

The expression:

/university-3/instructor/name/text()
```
returns the same names, but without the enclosing tags. Path expressions are evaluated from left to right. Like a directory hierarchy,

the initial ’/’ indicates the root of the document. Note that this is an abstract root “above” <university-3> that is the document tag.

As a path expression is evaluated, the result of the path at any point consists of an ordered set of nodes from the document. Initially, the “current” set of elements contains only one node, the abstract root. When the next step in a path expression is an element name, such as instructor, the result of the step consists of the nodes corresponding to elements of the specified name that are children of elements in the current element set. These nodes then become the current element set for the next step of the path expression evaluation. Thus, the expression:
```xml
/university-3

returns a single node corresponding to the:

<university-3>

tag, while:

/university-3/instructor

returns the two nodes corresponding to the:  

**1000 Chapter 23 XML**

instructor

elements that are children of the:

university-3

node.
``` 
The result of a path expression is then the set of nodes after the last step of

path expression evaluation. The nodes returned by each step appear in the same order as their appearance in the document.

Since multiple children can have the same name, the number of nodes in the node set can increase or decrease with each step. Attribute values may also be accessed, using the “@” symbol. For instance, /university-3/course/@course id returns a set of all values of course id attributes of course elements. By default, IDREF links are not followed; we shall see how to deal with IDREFs later.

XPath supports a number of other features:

- Selection predicates may follow any step in a path, and are contained in square brackets. For example,

/university-3/course[credits >\= 4]

returns course elements with a credits value greater than or equal to 4, while:

/university-3/course[credits >\= 4]/@course id

returns the course identifiers of those courses. We can test the existence of a subelement by listing it without any compar-

ison operation; for instance, if we removed just “>\= 4” from the above, the expression would return course identifiers of all courses that have a credits subelement, regardless of its value.

- XPath provides several functions that can be used as part of predicates, including testing the position of the current node in the sibling order and the aggregate function count(), which counts the number of nodes matched by the expression to which it is applied. For example, on the XML representation in Figure 23.6, the path expression:

/university-2/instructor[count(/teaches/course)> 2]

returns instructors who teach more than two courses. Boolean connectives and and or can be used in predicates, while the function not(. . .) can be used for negation.

- The function id(“foo”) returns the node (if any) with an attribute of type ID and value “foo”. The function id can even be applied on sets of references,  



or even strings containing multiple references separated by blanks, such as IDREFS. For instance, the path:

/university-3/course/id(@dept name)

returns all department elements referred to from the dept name attribute of course elements, while:

/university-3/course/id(@instructors)

returns the instructor elements referred to in the instuctors attribute of course elements.

- The | operator allows expression results to be unioned. For example, given data using the schema from Figure 23.11, we could find the union of Computer Science and Biology courses using the expression:

/university-3/course[@dept name=“Comp. Sci”] | /university-3/course[@dept name=“Biology”]

However, the | operator cannot be nested inside other operators. It is also worth noting that the nodes in the union are returned in the order in which they appear in the document.

- An XPath expression can skip multiple levels of nodes by using “//”. For instance, the expression /university-3//name finds all name elements anywhere under the /university-3 element, regardless of the elements in which they are contained, and regardless of how many levels of enclosing elements are present between the university-3 and name elements. This example illustrates the ability to find required data without full knowledge of the schema.

- A step in the path need not just select from the children of the nodes in the current node set. In fact, this is just one of several directions along which a step in the path may proceed, such as parents, siblings, ancestors, and descendants. We omit details, but note that “//”, described above, is a short form for specifying “all descendants,” while “..” specifies the parent.

- The built-in function doc(name) returns the root of a named document; the name could be a file name or a URL. The root returned by the function can then be used in a path expression to access the contents of the document. Thus, a path expression can be applied on a specified document, instead of being applied on the current default document.

For example, if the university data in our university example is contained in a file “university.xml”, the following path expression would return all departments at the university:

doc(“university.xml”)/university/department  



The function collection(name) is similar to doc, but returns a collection of doc- uments identified by name. The function collection can be used, for example, to open an XML database, which can be viewed as a collection of documents; the following element in the XPath expression would select the appropriate document(s) from the collection.

In most of our examples, we assume that the expressions are evaluated in the context of a database, which implicitly provides a collection of “documents” on which XPath expressions are evaluated. In such cases, we do not need to use the functions doc and collection.

### XQuery

The World Wide Web Consortium (W3C) has developed XQuery as the standard query language for XML. Our discussion is based on XQuery 1.0, which was released as a W3C recommendation on 23 January 2007.

#### 23.4.3.1 FLWOR Expressions

XQuery queries are modeled after SQL queries, but differ significantly from SQL. They are organized into five sections: **for**, **let**, **where**, **order by**, and **return**. They are referred to as “FLWOR” (pronounced “flower”) expressions, with the letters in FLWOR denoting the five sections.

A simple FLWOR expression that returns course identifiers of courses with greater than 3 credits, shown below, is based on the XML document of Figure 23.11, which uses ID and IDREFS:
```xml
**for** $x **in** /university-3/course **let** $courseId := $x/@course id **where** $x/credits > 3 **return** <course id> { $courseId } </course id>
```

The **for** clause is like the **from** clause of SQL, and specifies variables that range over the results of XPath expressions. When more than one variable is specified, the results include the Cartesian product of the possible values the variables can take, just as the SQL **from** clause does.

The **let** clause simply allows the results of XPath expressions to be assigned to variable names for simplicity of representation. The **where** clause, like the SQL **where** clause, performs additional tests on the joined tuples from the **for** clause. The **order by** clause, like the SQL **order by** clause, allows sorting of the output. Finally, the **return** clause allows the construction of results in XML.

A FLWOR query need not contain all the clauses; for example a query may contain just the **for** and **return** clauses, and omit the **let**, **where**, and **order by** clauses. The preceding XQuery query did not contain an **order by** clause. In fact, since this query is simple, we can easily do away with the **let** clause, and the variable $courseId in the **return** clause could be replaced with $x/@course id. Note further that, since the **for** clause uses XPath expressions, selections may  



occur within the XPath expression. Thus, an equivalent query may have only **for** and **return** clauses:
```xml
**for** $x **in** /university-3/course[credits > 3] **return** <course id> { $x/@course id } </course id>
```
However, the **let** clause helps simplify complex queries. Note also that variables assigned by **let** clauses may contain sequences with multiple elements or values, if the path expression on the right-hand side returns a sequence of multiple elements or values.

Observe the use of curly brackets (“_{}_”) in the **return** clause. When XQuery finds an element such as <course id> starting an expression, it treats its contents as regular XML text, except for portions enclosed within curly brackets, which are evaluated as expressions. Thus, if we omitted the curly brackets in the above **re- turn** clause, the result would contain several copies of the string “$x/@course id” each enclosed in a course id tag. The contents within the curly brackets are, however, treated as expressions to be evaluated. Note that this convention ap- plies even if the curly brackets appear within quotes. Thus, we could modify the above query to return an element with tag course, with the course identifier as an attribute, by replacing the **return** clause with the following:
```xml
**return** <course course id=“{$x/@course id}” />
```
XQuery provides another way of constructing elements using the **element** and **attribute** constructors. For example, if the **return** clause in the previous query is replaced by the following **return** clause, the query would return course elements with course id and dept name as attributes and title and credits as subelements.
```xml
**return element** course { **attribute** course id {$x/@course id}, **attribute** dept name {$x/dept name}, **element** title {$x/title}, **element** credits {$x/credits}

}
```
Note that, as before, the curly brackets are required to treat a string as an expres- sion to be evaluated.

#### 23.4.3.2 Joins

Joins are specified in XQuery much as they are in SQL. The join of course, instructor, and teaches elements in Figure 23.1 can be written in XQuery this way:  

```xml

**for** $c **in** /university/course, $i **in** /university/instructor, $t **in** /university/teaches

**where** $c/course id= $t/course id **and** $t/IID = $i/IID

**return** <course instructor> { $c $i } </course instructor>

The same query can be expressed with the selections specified as XPath selec- tions:

**for** $c **in** /university/course, $i **in** /university/instructor, $t **in** /university/teaches[ $c/course id= $t/course id

**and** $t/IID = $i/IID] **return** <course instructor> { $c $i } </course instructor>
```
Path expressions in XQuery are the same as path expressions in XPath2.0. Path expressions may return a single value or element, or a sequence of values or elements. In the absence of schema information, it may not be possible to infer whether a path expression returns a single value or a sequence of values. Such path expressions may participate in comparison operations such as =, <, and >\=.

XQuery has an interesting definition of comparison operations on sequences. For example, the expression $x/credits > 3 would have the usual interpretation if the result of $x/credits is a single value, but if the result is a sequence containing multiple values, the expression evaluates to true if at least one of the values is greater than 3. Similarly, the expression $x/credits = $y/credits evaluates to true if any one of the values returned by the first expression is equal to any one of the values returned by the second expression. If this behavior is not appropriate, the operators eq, ne, lt, gt, le, ge can be used instead. These raise an error if either of their inputs is a sequence with multiple values.

#### 23.4.3.3 Nested Queries

XQuery FLWOR expressions can be nested in the **return** clause, in order to generate element nestings that do not appear in the source document. For instance, the XML structure shown in Figure 23.5, with course elements nested within department elements, can be generated from the structure in Figure 23.1 by the query shown in Figure 23.14.

The query also introduces the syntax $d/*, which refers to all the children of the node (or sequence of nodes) bound to the variable $d. Similarly, $d/text() gives the text content of an element, without the tags.

XQuery provides a variety of aggregate functions such as sum() and count() that can be applied on sequences of elements or values. The function distinct- values() applied on a sequence returns a sequence without duplication. The se- quence (collection) of values returned by a path expression may have some values repeated because they are repeated in the document, although an XPath expres-  



<university-1>

{ **for** $d **in** /university/department **return**

<department> { $d/* } { **for** $c **in** /university/course[dept name = $d/dept name]

**return** $c } </department>

} {

**for** $i **in** /university/instructor **return**

<instructor> { $i/* } { **for** $c **in** /university/teaches[IID = $i/IID]

**return** $c/course id } </instructor>

} </university-1>

**Figure 23.14** Creating nested structures in XQuery

sion result can contain at most one occurrence of each node in the document. XQuery supports many other functions; see the references in the bibliographical notes for more information. These functions are actually common to XPath 2.0 and XQuery, and can be used in any XPath path expression.

To avoid namespace conflicts, functions are associated with a namespace:

http://www.w3.org/2005/xpath-functions

which has a default namespace prefix of fn. Thus, these functions can be referred to unambiguously as fn:sum or fn:count.

While XQuery does not provide a **group by** construct, aggregate queries can be written by using the aggregate functions on path or FLWOR expressions nested within the **return** clause. For example, the following query on the university XML schema finds the total salary of all instructors in each department:

**for** $d **in** /university/department **return**

<department-total-salary>

<dept name> { $d/dept name } </dept name>

<total salary> { fn:sum( **for** $i **in** /university/instructor[dept name = $d/dept name] **return** $i/salary

) } </total salary>

</department-total-salary>  

**1006 Chapter 23 XML**

#### 23.4.3.4 Sorting of Results

Results can be sorted in XQuery by using the **order by** clause. For instance, this query outputs all instructor elements sorted by the name subelement:

**for** $i **in** /university/instructor **order by** $i/name **return** <instructor> { $i/* } </instructor>

To sort in descending order, we can use **order by** $i/name **descending**. Sorting can be done at multiple levels of nesting. For instance, we can get

a nested representation of university information with departments sorted in department name order, with courses sorted by course identifiers, as follows:

<university-1> { **for** $d **in** /university/department **order by** $d/dept name **return**

<department> { $d/* } { **for** $c **in** /university/course[dept name = $d/dept name] **order by** $c/course id **return** <course> { $c/* } </course> }_

</department> } </university-1>

#### 23.4.3.5 Functions and Types

XQuery provides a variety of built-in functions, such as numeric functions and string matching and manipulation functions. In addition, XQuery supports user- defined functions. The following user-defined function takes as input an instruc- tor identifier, and returns a list of all courses offered by the department to which the instructor belongs:

**declare function** local:dept courses($iid as xs:string) **as** element(course)\* { **for** $i **in** /university/instructor[IID = $iid],

$c **in** /university/courses[dept name = $i/dept name] **return** $c

}

The namespace prefix xs: used in the above example is predefined by XQuery to be associated with the XML Schema namespace, while the namespace local: is predefined to be associated with XQuery local functions.

The type specifications for function arguments and return values are optional, and may be omitted. XQuery uses the type system of XML Schema. The type element allows elements with any tag, while element(course) allows elements



with the tag course. Types can be suffixed with a \* to indicate a sequence of values of that type; for example, the definition of function dept courses specifies the return value as a sequence of course elements.

The following query, which illustrates function invocation, prints out the department courses for the instructor(s) named Srinivasan:

**for** $i **in** /university/instructor[name = “Srinivasan”], **return**local:inst dept courses($i/IID)

XQuery performs type conversion automatically whenever required. For ex- ample, if a numeric value represented by a string is compared to a numeric type, type conversion from string to the numeric type is done automatically. When an element is passed to a function that expects a string value, type conversion to a string is done by concatenating all the text values contained (nested) within the element. Thus, the function contains(a,b), which checks if string a contains string b, can be used with its first argument set to an element, in which case it checks if the element a contains the string b nested anywhere inside it. XQuery also provides functions to convert between types. For instance, number(x) converts a string to a number.

#### 23.4.3.6 Other Features

XQuery offers a variety of other features, such as if-then-else constructs that can be used within **return** clauses, and existential and universal quantification that can be used in predicates in **where** clauses. For example, existential quantification can be expressed in the **where** clause by using:

**some** $e **in** path **satisfies** P

where path is a path expression and P is a predicate that can use $e. Universal quantification can be expressed by using **every** in place of **some**.

For example, to find departments where every instructor has a salary greater than $50,000, we can use the following query:

**for** $d **in** /university/department **where every** $i **in** /university/instructor[dept name=$d/dept name]

**satisfies** $i/salary > 50000 **return** $d

Note, however, that if a department has no instructor, it will trivially satisfy the above condition. An extra clause:

**and** fn:exists(/university/instructor[dept name=$d/dept name]) 

can be used to ensure that there is at least one instructor in the department. The built-in function exists() used in the clause returns true if its input argument is nonempty.

The **XQJ** standard provides an API to submit XQuery queries to an XML database system and to retrieve the XML results. Its functionality is similar to the JDBC API.

