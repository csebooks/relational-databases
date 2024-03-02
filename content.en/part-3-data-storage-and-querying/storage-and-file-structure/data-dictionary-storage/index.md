---
title: 'Data-Dictionary Storage'
weight: 7
references:
    videos:
        - youtube:wWKUQz3giRI
        - youtube:nKochahg32Y
    links:
        - https://www.javatpoint.com/data-dictionary-storage
        - https://www.geeksforgeeks.org/short-note-on-data-dictionary/
    books:
        - b1:
            title: The Data Dictionary
            url: https://www.google.co.in/books/edition/The_Data_Dictionary/PN9QAAAAMAAJ?hl=en
        - b2:
            title: D.R.I.E. Data Dictionary Concepts, Services, and Facilities
            url: https://books.google.co.in/books?id=OGZ80AEACAAJ&newbks=0&hl=en&source=newbks_fb&redir_esc=y
---

# Data-Dictionary Storage

So far, we have considered only the representation of the relations themselves. A relational database system needs to maintain data _about_ the relations, such as the schema of the relations. In general, such “data about data” is referred to as **metadata**.

Relational schemas and other metadata about relations are stored in a struc- ture called the **data dictionary** or **system catalog**. Among the types of information that the system must store are these:

- Names of the relations.

- Names of the attributes of each relation.

- Domains and lengths of attributes.

- Names of views defined on the database, and definitions of those views.

- Integrity constraints (for example, key constraints).

In addition, many systems keep the following data on users of the system:

- Names of authorized users.

- Authorization and accounting information about users. 

- Passwords or other information used to authenticate users.

Further, the database may store statistical and descriptive data about the relations, such as:

- Number of tuples in each relation.

- Method of storage for each relation (for example, clustered or nonclustered).

The data dictionary may also note the storage organization (sequential, hash, or heap) of relations, and the location where each relation is stored:

- If relations are stored in operating system files, the dictionary would note the names of the file (or files) containing each relation.

- If the database stores all relations in a single file, the dictionary may note the blocks containing records of each relation in a data structure such as a linked list.

In Chapter 11, in which we study indices, we shall see a need to store information about each index on each of the relations:

- Name of the index.

- Name of the relation being indexed.

- Attributes on which the index is defined.

- Type of index formed.

All this metadata information constitutes, in effect, a miniature database. Some database systems store such metadata by using special-purpose data struc- tures and code. It is generally preferable to store the data about the database as relations in the database itself. By using database relations to store system meta- data, we simplify the overall structure of the system and harness the full power of the database for fast access to system data.

The exact choice of how to represent system metadata by relations must be made by the system designers. One possible representation, with primary keys underlined, is shown in Figure 10.16. In this representation, the attribute _index attributes_ of the relation _Index metadata_ is assumed to contain a list of one or more attributes, which can be represented by a character string such as “_dept name_, _building_”. The _Index metadata_ relation is thus not in first normal form; it can be normalized, but the above representation is likely to be more efficient to access. The data dictionary is often stored in a nonnormalized form to achieve fast access.

Whenever the database system needs to retrieve records from a relation, it must first consult the _Relation metadata_ relation to find the location and storage organization of the relation, and then fetch records using this information. How- ever, the storage organization and location of the _Relation metadata_ relation itself  

![Alt text](image-12.png)

must be recorded elsewhere (for example, in the database code itself, or in a fixed location in the database), since we need this information to find the contents of _Relation metadata_.

