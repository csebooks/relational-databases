---
title: Complex Data Types
weight: 2
---

Complex Data Types## Complex Data Types

Traditional database applications have conceptually simple data types. The basic data items are records that are fairly small and whose fields are atomic—that is, they are not further structured, and first normal form holds (see Chapter 8). Further, there are only a few record types.

In recent years, demand has grown for ways to deal with more complex data types. Consider, for example, addresses. While an entire address could be viewed as an atomic data item of type string, this view would hide details such as the street address, city, state, and postal code, which could be of interest to queries. On the other hand, if an address were represented by breaking it into the components (street address, city, state, and postal code), writing queries would be more complicated since they would have to mention each field. A better alternative is to allow structured data types that allow a type address with subparts street address, city, state, and postal code.

As another example, consider multivalued attributes from the E-R model. Such attributes are natural, for example, for representing phone numbers, since people  



|title | author\array| publisher| keyword\set |
| ------ | -------- | -------- | -------- |
| || |(name, branch)|| |
|Compilers| [Smith, Jones]| (McGraw-Hill, NewYork) |{parsing, analysis}| Networks \|[Jones, Frick] (Oxford, London) {Internet, Web}
| Netork | [Jones, Frick]|(Oxford, London) |{Internet, Web}|


**Figure 22.1** Non-1NF books relation, books.

may have more than one phone. The alternative of normalization by creating a new relation is expensive and artificial for this example.

With complex type systems we can represent E-R model concepts, such as composite attributes, multivalued attributes, generalization, and specialization directly, without a complex translation to the relational model.

In Chapter 8, we defined first normal form (1NF), which requires that all at- tributes have atomic domains. Recall that a domain is atomic if elements of the domain are considered to be indivisible units.

The assumption of 1NF is a natural one in the database application examples we have considered. However, not all applications are best modeled by 1NF relations. For example, rather than view a database as a set of records, users of certain applications view it as a set of objects (or entities). These objects may require several records for their representation. A simple, easy-to-use interface requires a one-to-one correspondence between the user’s intuitive notion of an object and the database system’s notion of a data item.

Consider, for example, a library application, and suppose we wish to store the following information for each book:

- Book title.

- List of authors.

- Publisher.

- Set of keywords.

We can see that, if we define a relation for the preceding information, several domains will be nonatomic.

- **Authors**. A book may have a list of authors, which we can represent as an array. Nevertheless, we may want to find all books of which Jones was one of the authors. Thus, we are interested in a subpart of the domain element “authors.”

- **Keywords**. If we store a set of keywords for a book, we expect to be able to retrieve all books whose keywords include one or more specified keywords. Thus, we view the domain of the set of keywords as nonatomic.

- **Publisher**. Unlike keywords and authors, publisher does not have a set-valued domain. However, we may view publisher as consisting of the subfields name and branch. This view makes the domain of publisher nonatomic.

Figure 22.1 shows an example relation, books.  

| title | author | position |
| ----------- | ----------- | -------|
| Compilers | Smith |    1  |
| Compilers | jones |  2 |
| Networks | jones |1|
| Networks | Frick |2|
                          author  

| title| keyword |
| ----------- | ----------- |
|  Compilers| parsing |
| Compilers | analysis |   
| Networks |Internet |
| Networks | Web |
              keywords

| title | pubname |pubbranch|
| ----------- | ----------- |-------|
| Header | Title | New Yock |
| Paragraph | Text | Londan|
                         books4

**Figure 22.2** 4NF version of the relation books.

For simplicity, we assume that the title of a book uniquely identifies the book.1 We can then represent the same information using the following schema, where the primary key attributes are underlined:

- authors(title, author, position)

- keywords(title, keyword)

- books4(title, pub name, pub branch)

The above schema satisfies 4NF. Figure 22.2 shows the normalized representation of the data from Figure 22.1.

Although our example book database can be adequately expressed without using nested relations, the use of nested relations leads to an easier-to-understand model. The typical user or programmer of an information-retrieval system thinks of the database in terms of books having sets of authors, as the non-1NF design models. The 4NF design requires queries to join multiple relations, whereas the non-1NF design makes many types of queries easier.

On the other hand, it may be better to use a first normal form representation in other situations. For instance, consider the takes relationship in our university example. The relationship is many-to-many between student and section. We could

1This assumption does not hold in the real world. Books are usually identified by a 10-digit ISBN number that uniquely identifies each published book.  


conceivably store a set of sections with each student, or a set of students with each section, or both. If we store both, we would have data redundancy (the relationship of a particular student to a particular section would be stored twice).

The ability to use complex data types such as sets and arrays can be useful in many applications but should be used with care.

