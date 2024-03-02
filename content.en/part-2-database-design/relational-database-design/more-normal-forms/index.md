---
title: 'More Normal Forms'
weight: 7
references:
    videos:
        - youtube:IBEuwmYsMKI   
        - youtube:ABwD8IYByfk
    links:
        - https://www.geeksforgeeks.org/normal-forms-in-dbms/
        - https://www.tutorialspoint.com/normal-forms-in-dbms
    books:
        - b1:
            title: The Method of Normal Forms
            url: https://www.google.co.in/books/edition/The_Method_of_Normal_Forms/E3GvZ16s8AAC?hl=en&gbpv=0
        - b2:
            title: Poisson Structures and Their Normal Forms
            url: https://www.google.co.in/books/edition/Poisson_Structures_and_Their_Normal_Form/t8rHBAAAQBAJ?hl=en&gbpv=0
---

# More Normal Forms

The fourth normal form is by no means the “ultimate” normal form. As we saw earlier, multivalued dependencies help us understand and eliminate some forms of repetition of information that cannot be understood in terms of func- tional dependencies. There are types of constraints called **join dependencies** that generalize multivalued dependencies, and lead to another normal form called **project-join normal form (PJNF)** (PJNF is called **fifth normal form** in some books). There is a class of even more general constraints that leads to a normal form called **domain-key normal form (DKNF)**.  

A practical problem with the use of these generalized constraints is that they are not only hard to reason with, but there is also no set of sound and complete inference rules for reasoning about the constraints. Hence PJNF and DKNF are used quite rarely. Appendix C provides more details about these normal forms.

Conspicuous by its absence from our discussion of normal forms is **second normal form** (2NF). We have not discussed it, because it is of historical interest only. We simply define it, and let you experiment with it in Practice Exercise 8.17.

