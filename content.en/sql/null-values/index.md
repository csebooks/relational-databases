---
title: Null Values
weight: 6
---

# Null Values

Null values present special problems in relational operations, including arithmetic operations, comparison operations, and set operations.

The result of an arithmetic expression (involving, for example +, −, ∗, or _/_) is null if any of the input values is null. For example, if a query has an expression _r.A_\+ 5, and _r.A_ is null for a particular tuple, then the expression result must also be null for that tuple.

Comparisons involving nulls are more of a problem. For example, consider the comparison “1 _<_ **null**”. It would be wrong to say this is true since we do not know what the null value represents. But it would likewise be wrong to claim this expression is false; if we did, “**not** (1 _<_ **null**)” would evaluate to true, which does not make sense. SQL therefore treats as **unknown** the result of any comparison involving a _null_ value (other than predicates **is null** and **is not null**, which are described later in this section). This creates a third logical value in addition to _true_ and _false_.

Since the predicate in a **where** clause can involve Boolean operations such as **and**, **or**, and **not** on the results of comparisons, the definitions of the Boolean operations are extended to deal with the value **unknown**.

• **and**: The result of _true_ **and** _unknown_ is _unknown_, _false_ **and** _unknown_ is _false_, while _unknown_ **and** _unknown_ is _unknown_.

• **or**: The result of _true_ **or** _unknown_ is _true_, _false_ **or** _unknown_ is _unknown_, while _unknown_ **or** _unknown_ is _unknown_.

• **not**: The result of **not** _unknown_ is _unknown_.

You can verify that if _r.A_ is null, then “1 _< r.A_” as well as “**not** (1 _< r.A_)” evaluate to unknown.  

If the **where** clause predicate evaluates to either **false** or **unknown** for a tuple, that tuple is not added to the result.

SQL uses the special keyword **null** in a predicate to test for a null value. Thus, to find all instructors who appear in the _instructor_ relation with null values for _salary_, we write:

**select** _name_ 
**from** _instructor_ 
**where** _salary_ **is null**;

The predicate **is not null** succeeds if the value on which it is applied is not null. Some implementations of SQL also allow us to test whether the result of a com-

parison is unknown, rather than true or false, by using the clauses **is unknown** and **is not unknown**.

When a query uses the **select distinct** clause, duplicate tuples must be eliminated. For this purpose, when comparing values of corresponding attributes from two tuples, the values are treated as identical if either both are non-null and equal in value, or both are null. Thus two copies of a tuple, such as _{_(’A’,null), (’A’,null)_}_, are treated as being identical, even if some of the attributes have a null value. Using the **distinct** clause then retains only one copy of such identical tuples. Note that the treatment of null above is different from the way nulls are treated in predicates, where a comparison “null=null” would return unknown, rather than true.

The above approach of treating tuples as identical if they have the same values for all attributes, even if some of the values are null, is also used for the set operations union, intersection and except.

