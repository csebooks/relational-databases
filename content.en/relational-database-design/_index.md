---
title: 'Relational Database Design' 
categories:
weight: 8
--- 

# Relational Database Design

In this chapter, we consider the problem of designing a schema for a relational
database. Many of the issues in doing so are similar to design issues we considered
in Chapter 7 using the E-R model.
In general, the goal of relational database design is to generate a set of relation
schemas that allows us to store information without unnecessary redundancy, yet
also allows us to retrieve information easily. This is accomplished by designing
schemas that are in an appropriate normal form. To determine whether a relation
schema is in one of the desirable normal forms, we need information about
the real-world enterprise that we are modeling with the database. Some of this
information exists in a well-designed E-R diagram, but additional information
about the enterprise may be needed as well.
In this chapter, we introduce a formal approach to relational database design
based on the notion of functional dependencies. We then define normal forms
in terms of functional dependencies and other types of data dependencies. First,
however, we view the problem of relational design from the standpoint of the
schemas derived from a given entity-relationship design.