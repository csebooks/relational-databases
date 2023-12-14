---
title: 12.8 Summary
weight: 32
---

12.8 Summary## 12.8 Summary

- The first action that the system must perform on a query is to translate the query into its internal form, which (for relational database systems) is usually based on the relational algebra. In the process of generating the internal form of the query, the parser checks the syntax of the user’s query, verifies that the relation names appearing in the query are names of relations in the database, and so on. If the query was expressed in terms of a view, the parser replaces all references to the view name with the relational-algebra expression to compute the view.

- Given a query, there are generally a variety of methods for computing the answer. It is the responsibility of the query optimizer to transform the query as entered by the user into an equivalent query that can be computed more efficiently. Chapter 13 covers query optimization.

- We can process simple selection operations by performing a linear scan, or by making use of indices. We can handle complex selections by computing unions and intersections of the results of simple selections.

- We can sort relations larger than memory by the external sort–merge algo- rithm.

- Queries involving a natural join may be processed in several ways, depending on the availability of indices and the form of physical storage for the relations.

    ◦ If the join result is almost as large as the Cartesian product of the two relations, a _block nested-loop_ join strategy may be advantageous.

    ◦ If indices are available, the _indexed nested-loop_ join can be used.

    ◦ If the relations are sorted, a _merge join_ may be desirable. It may be advan- tageous to sort a relation prior to join computation (so as to allow use of the merge-join strategy).

    ◦ The _hash-join_ algorithm partitions the relations into several pieces, such that each piece of one of the relations fits in memory. The partitioning is carried out with a hash function on the join attributes, so that correspond- ing pairs of partitions can be joined independently.

- Duplicate elimination, projection, set operations (union, intersection, and difference), and aggregation can be done by sorting or by hashing.

- Outer-join operations can be implemented by simple extensions of join algo- rithms.

- Hashing and sorting are dual, in the sense that any operation such as du- plicate elimination, projection, aggregation, join, and outer join that can be implemented by hashing can also be implemented by sorting, and vice versa; that is, any operation that can be implemented by sorting can also be imple- mented by hashing.

- An expression can be evaluated by means of materialization, where the sys- tem computes the result of each subexpression and stores it on disk, and then uses it to compute the result of the parent expression.

- Pipelining helps to avoid writing the results of many subexpressions to disk, by using the results in the parent expression even as they are being generated.

