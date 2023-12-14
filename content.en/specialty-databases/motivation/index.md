---
title: Motivation
weight: 13
---

Motivation## Motivation

To understand XML, it is important to understand its roots as a document markup language. The term **markup** refers to anything in a document that is not intended to be part of the printed output. For example, a writer creating text that will eventually be typeset in a magazine may want to make notes about how the typesetting should be done. It would be important to type these notes in a way so that they could be distinguished from the actual content, so that a note like “set this word in large size, bold font” or “insert a line break here” does not end up printed in the magazine. Such notes convey extra information about the text. In electronic document processing, a **markup language** is a formal description of what part of the document is content, what part is markup, and what the markup means.

Just as database systems evolved from physical file processing to provide a separate logical view, markup languages evolved from specifying instructions for how to print parts of the document to specifying the function of the content. For instance, with functional markup, text representing section headings (for this section, the word “Motivation”) would be marked up as being a section heading, instead of being marked up as text to be printed in large size, bold font. From the viewpoint of typesetting, such functional markup allows the document to be
 


formatted differently in different situations. It also helps different parts of a large document, or different pages in a large Web site, to be formatted in a uniform manner. More importantly, functional markup also helps record what each part of the text represents semantically, and correspondingly helps automate extraction of key parts of documents.

For the family of markup languages that includes HTML, SGML, and XML, the markup takes the form of **tags** enclosed in angle brackets, <>_. Tags are used in pairs, with <tag> and </tag> delimiting the beginning and the end of the portion of the document to which the tag refers. For example, the title of a document might be marked up as follows:
```sql
<title>Database System Concepts</title>
```
Unlike HTML, XML does not prescribe the set of tags allowed, and the set may be chosen as needed by each application. This feature is the key to XML’s major role in data representation and exchange, whereas HTML is used primarily for document formatting.
```sql
<university>

<department> <dept name> Comp. Sci. </dept name>

    <building> Taylor </building>

    <budget> 100000 </budget>
</department> <department>

<dept name> Biology </dept name>

<building> Watson </building>

<budget> 90000 </budget> </department> 
<course>

<course id> CS-101 </course id>

<title> Intro. to Computer Science </title>

<dept name> Comp. Sci </dept name>

<credits> 4 </credits>

</course>

<course>

<course id> BIO-301 </course id>

<title> Genetics </title>

<dept name> Biology </dept name>

<credits> 4 </credits>

</course>
```
continued in Figure 23.2

**Figure 23.1** XML representation of (part of) university information.  

```sql
<instructor> <IID> 10101 </IID>

<name> Srinivasan </name>

<dept name> Comp. Sci. </dept name>

<salary> 65000 </salary>

</instructor> <instructor>

<IID> 83821 </IID>

<name> Brandt </name>

<dept name> Comp. Sci. </dept name>

<salary> 92000 </salary>

</instructor> <instructor>

<IID> 76766 </IID>

<name> Crick </name>

<dept name> Biology </dept name>

<salary> 72000 </salary>

</instructor> <teaches>

<IID> 10101 </IID>

<course id> CS-101 </course id>

</teaches>

<teaches>

<IID> 83821 </IID>

<course id> CS-101 </course id>

</teaches>

<teaches>

<IID> 76766 </IID>

<course id> BIO-301 </course id>

</teaches>

</university>
```


For example, in our running university application, department, course and instructor information can be represented as part of an XML document as in Fig- ures 23.1 and 23.2. Observe the use of tags such as department, course, instructor, and teaches. To keep the example short, we use a simplified version of the uni- versity schema that ignores section information for courses. We have also used the tag IID to denote the identifier of the instructor, for reasons we shall see later.

These tags provide context for each value and allow the semantics of the value to be identified. For this example, the XML data representation does not provide any significant benefit over the traditional relational data representation; however, we use this example as our running example because of its simplicity.

```sql

<purchase order> <identifier>_ P-101 </identifier> <purchaser>

<name> Cray Z. Coyote </name>

<address> Mesa Flats, Route 66, Arizona 12345, USA </address>

</purchaser> <supplier>

<name> Acme Supplies </name>

<address> 1 Broadway, New York, NY, USA </address>

</supplier> <itemlist>

<item>

<identifier>_ RS1 </identifier> <description> Atom powered rocket sled </description>

<quantity> 2 </quantity>

<price> 199.95 </price>

</item>

<item>

<identifier>_ SG2 </identifier> <description> Superb glue </description>

<quantity> 1 </quantity>

<unit-of-measure> liter </unit-of-measure>

<price> 29.95 </price>

</item>

</itemlist> <total cost> 429.85 </total cost> <payment terms> Cash-on-delivery </payment terms>

<shipping mode> 1-second-delivery </shipping mode>

</purchaseorder>
```

Figure 23.3, which shows how information about a purchase order can be represented in XML, illustrates a more realistic use of XML. Purchase orders are typically generated by one organization and sent to another. Traditionally they were printed on paper by the purchaser and sent to the supplier; the data would be manually re-entered into a computer system by the supplier. This slow process can be greatly sped up by sending the information electronically between the purchaser and supplier. The nested representation allows all information in a purchase order to be represented naturally in a single document. (Real purchase orders have considerably more information than that depicted in this simplified example.) XML provides a standard way of tagging the data; the two organizations must of course agree on what tags appear in the purchase order, and what they mean.  



Compared to storage of data in a relational database, the XML representa- tion may be inefficient, since tag names are repeated throughout the document. However, in spite of this disadvantage, an XML representation has significant ad- vantages when it is used to exchange data between organizations, and for storing complex structured information in files:

- First, the presence of the tags makes the message **self-documenting**; that is, a schema need not be consulted to understand the meaning of the text. We can readily read the fragment above, for example.

- Second, the format of the document is not rigid. For example, if some sender adds additional information, such as a tag last accessed noting the last date on which an account was accessed, the recipient of the XML data may simply ignore the tag. As another example, in Figure 23.3, the item with identifier SG2 has a tag called unit-of-measure specified, which the first item does not. The tag is required for items that are ordered by weight or volume, and may be omitted for items that are simply ordered by number.

The ability to recognize and ignore unexpected tags allows the format of the data to evolve over time, without invalidating existing applications. Similarly, the ability to have multiple occurrences of the same tag makes it easy to represent multivalued attributes.

- Third, XML allows nested structures. The purchase order shown in Figure 23.3 illustrates the benefits of having a nested structure. Each purchase order has a purchaser and a list of items as two of its nested structures. Each item in turn has an item identifier, description and a price nested within it, while the purchaser has a name and address nested within it.

Such information would have been split into multiple relations in a relational schema. Item information would have been stored in one relation, purchaser information in a second relation, purchase orders in a third, and the relationship between purchase orders, purchasers, and items would have been stored in a fourth relation.

The relational representation helps to avoid redundancy; for example, item descriptions would be stored only once for each item identifier in a normalized relational schema. In the XML purchase order, however, the de- scriptions may be repeated in multiple purchase orders that order the same item. However, gathering all information related to a purchase order into a single nested structure, even at the cost of redundancy, is attractive when information has to be exchanged with external parties.

- Finally, since the XML format is widely accepted, a wide variety of tools are available to assist in its processing, including programming language APIs to create and to read XML data, browser software, and database tools.

We describe several applications for XML data later, in Section 23.7. Just as SQL is the dominant language for querying relational data, XML has become the dominant format for data exchange.  



