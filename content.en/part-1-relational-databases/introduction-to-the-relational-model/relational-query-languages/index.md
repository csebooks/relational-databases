---
title:  Relational Query Languages
weight: 5
references:
    videos:
        - youtube:KpbyPhJqIf0
        - youtube:0JAYah0MVno
    links:
        - https://www.tutorialspoint.com/what-is-a-query-language-in-dbms
        - https://www.i2tutorials.com/dbms-introduction/dbms-relational-query-languages/
    books:
        - b1:
            title: Understanding Relational Database Query Languages
            url: https://www.google.co.in/books/edition/Understanding_Relational_Database_Query/y3JnQgAACAAJ?hl=en
        - b2:
            title: Theory and Practice of Relational database
            url:   https://www.google.co.in/books/edition/Theory_and_Practice_of_Relational_Databa/T42BSf-1f5cC?hl=en&gbpv=1&dq=Relational+Query+language+books&printsec=frontcover
---

# Relational Query Languages

A **query language** is a language in which a user requests information from the database. These languages are usually on a level higher than that of a standard programming language. Query languages can be categorized as either procedural or nonprocedural. In a **procedural language**, the user instructs the system to perform a sequence of operations on the database to compute the desired result. In a **nonprocedural language**, the user describes the desired information without giving a specific procedure for obtaining that information. 

classroom(building, room number, capacity)
department(dept name, building, budget)
course(course id, title, dept name, credits)
instructor(ID, name, dept name, salary)
section(course id, sec id, semester, year, building, room number, time slot id)
teaches(ID, course id, sec id, semester, year)
student(ID, name, dept name, tot cred)
takes(ID, course id, sec id, semester, year, grade)
advisor(s ID, i ID)
time slot(time slot id, day, start time, end time)
prereq(course id, prereq id)

**Figure 2.9** Schema of the university database.

Query languages used in practice include elements of both the procedural and the nonprocedural approaches. We study the very widely used query language SQL in Chapters 3 through 5.

There are a number of “pure” query languages: The relational algebra is procedural, whereas the tuple relational calculus and domain relational calculus are nonprocedural. These query languages are terse and formal, lacking the “syntactic sugar” of commercial languages, but they illustrate the fundamental techniques for extracting data from the database. In Chapter 6, we examine in detail the relational algebra and the two versions of the relational calculus, the tuple relational calculus and domain relational calculus. The relational algebra consists of a set of operations that take one or two relations as input and produce a new relation as their result. The relational calculus uses predicate logic to define the result desired without giving any specific algebraic procedure for obtaining that result.

