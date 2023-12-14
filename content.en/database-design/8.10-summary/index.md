---
title: 8.10 Summary
weight: 28
---

8.10 Summary## 8.10 Summary

• We showed pitfalls in database design, and how to systematically design a database schema that avoids the pitfalls. The pitfalls included repeated information and inability to represent some information.

• We showed the development of a relational database design from an E-R design, when schemas may be combined safely, and when a schema should be decomposed. All valid decompositions must be lossless.

• We described the assumptions of atomic domains and first normal form.

• We introduced the concept of functional dependencies, and used it to present two normal forms, Boyce–Codd normal form (BCNF) and third normal form (3NF).

• If the decomposition is dependency preserving, given a database update, all functional dependencies can be verifiable from individual relations, without computing a join of relations in the decomposition.

• We showed how to reason with functional dependencies. We placed special emphasis on what dependencies are logically implied by a set of dependen- cies. We also defined the notion of a canonical cover, which is a minimal set of functional dependencies equivalent to a given set of functional dependencies.

• We outlined an algorithm for decomposing relations into BCNF. There are relations for which there is no dependency-preserving BCNF decomposition.

• We used the canonical covers to decompose a relation into 3NF, which is a small relaxation of the BCNF condition. Relations in 3NF may have some redundancy, but there is always a dependency-preserving decomposition into 3NF. 

• We presented the notion of multivalued dependencies, which specify con- straints that cannot be specified with functional dependencies alone. We de- fined fourth normal form (4NF) with multivalued dependencies. Appendix C.1.1 gives details on reasoning about multivalued dependencies.

• Other normal forms, such as PJNF and DKNF, eliminate more subtle forms of redundancy. However, these are hard to work with and are rarely used. Appendix C gives details on these normal forms.

• In reviewing the issues in this chapter, note that the reason we could define rigorous approaches to relational database design is that the relational data model rests on a firm mathematical foundation. That is one of the primary advantages of the relational model compared with the other data models that we have studied.

**Review Terms**

• E-R model and normalization 
• Decomposition 
• Functional dependencies 
• Lossless decomposition 
• Atomic domains 
• First normal form (1NF) 
• Legal relations 
• Superkey 
• _R_ satisfies _F_
• _F_ holds on _R_
• Boyce–Codd normal form (BCNF)
• Dependency preservation 
• Third normal form (3NF) 
• Trivial functional dependencies 
• Closure of a set of functional dependencies
• Armstrong’s axioms 
• Closure of attribute sets 
• Restriction of _F_ to _Ri_
• Canonical cover 
• Extraneous attributes 
• BCNF decomposition algorithm 
• 3NF decomposition algorithm 
• Multivalued dependencies 
• Fourth normal form (4NF) 
• Restriction of a multivalued dependency 
• Project-join normal form (PJNF) 
• Domain-key normal form (DKNF) 
• Universal relation 
• Unique-role assumption 
• Denormalization

