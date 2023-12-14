---
title: 6.3 The Domain Relational Calculus
weight: 43
---

6.3 The Domain Relational Calculus## 6.3 The Domain Relational Calculus

A second form of relational calculus, called **domain relational calculus**, uses _domain_ variables that take on values from an attributes domain, rather than values for an entire tuple. The domain relational calculus, however, is closely related to the tuple relational calculus.

Domain relational calculus serves as the theoretical basis of the widely used QBE language (see Appendix B.1), just as relational algebra serves as the basis for the SQL language.

### 6.3.1 Formal Definition

An expression in the domain relational calculus is of the form

{_< x_1_, x_2_, . . . , xn >_ | _P_(_x_1_, x_2_, . . . , xn_)}

where _x_1_, x_2_, . . . , xn_ represent domain variables. _P_ represents a formula composed of atoms, as was the case in the tuple relational calculus. An atom in the domain relational calculus has one of the following forms:

• _< x_1_, x_2_, . . . , xn >_ ∈ _r,_ where _r_ is a relation on _n_ attributes and _x_1_, x_2_, . . . , xn_ are domain variables or domain constants.

• _x ρ y_, where _x_ and _y_ are domain variables and  is a comparison operator (_<_, ≤, =, =, _\>_, ≥). We require that attributes _x_ and _y_ have domains that can be compared by .

• _x  c_, where _x_ is a domain variable,  is a comparison operator, and _c_ is a constant in the domain of the attribute for which _x_ is a domain variable.

We build up formulae from atoms by using the following rules:

• An atom is a formula.

• If _P_1 is a formula, then so are ¬_P_1 and (_P_1).

• If _P_1 and _P_2 are formulae, then so are _P_1 ∨ _P_2, _P_1 ∧ _P_2, and _P_1 ⇒ _P_2.

• If _P_1(_x_) is a formula in _x_, where _x_ is a free domain variable, then

∃ _x_ (_P_1(_x_)) and ∀ _x_ (_P_1(_x_))

are also formulae.

As a notational shorthand, we write ∃ _a , b, c_ (_P_(_a , b, c_)) for ∃ _a_ (∃ _b_ (∃ _c_ (_P_(_a , b, c_)))).  

### 6.3.2 Example Queries

We now give domain-relational-calculus queries for the examples that we considered earlier. Note the similarity of these expressions and the corresponding tuple-relational-calculus expressions.

• Find the instructor _ID_, _name_, _dept name_, and _salary_ for instructors whose salary is greater than $80,000:

{_< i, n, d, s >_ | _< i, n, d, s >_ ∈ _instructor_ ∧ _s >_ 80000}

• Find all instructor _ID_ for instructors whose salary is greater than $80,000:

{_< n >_ | ∃ _i, d, s_ (_< i, n, d, s >_ ∈ _instructor_ ∧ _s >_ 80000)}

Although the second query appears similar to the one that we wrote for the tuple relational calculus, there is an important difference. In the tuple calculus, when we write ∃ _s_ for some tuple variable _s_, we bind it immediately to a relation by writing ∃ _s_ ∈ _r_ . However, when we write ∃ _n_ in the domain calculus, _n_ refers not to a tuple, but rather to a domain value. Thus, the domain of variable _n_ is unconstrained until the subformula _< i, n, d, s >_ ∈ _instructor_ constrains _n_ to instructor names that appear in the _instructor_ relation.

We now give several examples of queries in the domain relational calculus.

• Find the names of all instructors in the Physics department together with the _course id_ of all courses they teach:

{_< n, c >_ | ∃ _i, a_ (_< i, c, a , s, y >_ ∈ _teaches_ ∧ ∃ _d, s_ (_< i, n, d, s >_ ∈ _instructor_ ∧ _d_ \= “Physics”))}

• Find the set of all courses taught in the Fall 2009 semester, the Spring 2010 semester, or both:

{_< c >_ | ∃ _s_ (_< c, a , s, y, b, r, t >_∈ _section_ ∧ _s_ \= “Fall” ∧ _y_ \= “2009”

∨∃ _u_ (_< c, a , s, y, b, r, t >_∈ _section_ ∧ _s_ \= “Spring” ∧ _y_ \= “2010”

• Find all students who have taken all courses offered in the Biology department:

{_< i >_ | ∃ _n, d, t_ (_< i, n, d, t >_ ∈ _student_) ∧ ∀ _x, y, z, w_ (_< x, y, z, w >_ ∈ _course_ ∧ _z_ \= “Biology” ⇒ ∃ _a , b_ (_< a , x, b, r, p, q >_ ∈ _takes_ ∧ _< c, a >_ ∈ _depositor_ ))}  

Note that as was the case for tuple-relational-calculus, if no courses are offered in the Biology department, all students would be in the result.

### 6.3.3 Safety of Expressions

We noted that, in the tuple relational calculus (Section 6.2), it is possible to write expressions that may generate an infinite relation. That led us to define _safety_ for tuple-relational-calculus expressions. A similar situation arises for the domain relational calculus. An expression such as

{_< i, n, d, s >_ | ¬(_< i, n, d, s >_ ∈ _instructor_ )}

is unsafe, because it allows values in the result that are not in the domain of the expression.

For the domain relational calculus, we must be concerned also about the form of formulae within “there exists” and “for all” clauses. Consider the expression

{_< x >_ | ∃ _y_ (_< x, y >_∈ _r_ ) ∧ ∃ _z_ (¬(_< x, z >_∈ _r_ ) ∧ _P_(_x, z_))}

where _P_ is some formula involving _x_ and _z_. We can test the first part of the formula, ∃ _y_ (_< x, y >_ ∈ _r_ ), by considering only the values in _r_. However, to test the second part of the formula, ∃ _z_ (¬ (_< x, z >_ ∈ _r_ ) ∧ _P_(_x, z_)), we must consider values for _z_ that do not appear in _r_. Since all relations are finite, an infinite number of values do not appear in _r_. Thus, it is not possible, in general, to test the second part of the formula without considering an infinite number of potential values for _z_. Instead, we add restrictions to prohibit expressions such as the preceding one.

In the tuple relational calculus, we restricted any existentially quantified variable to range over a specific relation. Since we did not do so in the domain calculus, we add rules to the definition of safety to deal with cases like our example. We say that an expression

{_< x_1_, x_2_, . . . , xn >_ | _P_ (_x_1_, x_2_, . . . , xn_)}

is safe if all of the following hold:

**1\.** All values that appear in tuples of the expression are values from _dom_(_P_).

**2\.** For every “there exists” subformula of the form ∃ _x_ (_P_1(_x_)), the subformula is true if and only if there is a value _x_ in _dom_(_P_1) such that _P_1(_x_) is true.

**3\.** For every “for all” subformula of the form ∀_x_ (_P_1(_x_)), the subformula is true if and only if _P_1(_x_) is true for all values _x_ from _dom_(_P_1).

The purpose of the additional rules is to ensure that we can test “for all” and “there exists” subformulae without having to test infinitely many possibilities. Consider the second rule in the definition of safety. For ∃ _x_ (_P_1(_x_)) to be true,  

**248 Chapter 6 Formal Relational Query Languages**

we need to find only one _x_ for which _P_1(_x_) is true. In general, there would be infinitely many values to test. However, if the expression is safe, we know that we can restrict our attention to values from _dom_(_P_1). This restriction reduces to a finite number the tuples we must consider.

The situation for subformulae of the form ∀_x_ (_P_1(_x_)) is similar. To assert that ∀_x_ (_P_1(_x_)) is true, we must, in general, test all possible values, so we must examine infinitely many values. As before, if we know that the expression is safe, it is sufficient for us to test _P_1(_x_) for those values taken from _dom_(_P_1).

All the domain-relational-calculus expressions that we have written in the example queries of this section are safe, except for the example unsafe query we saw earlier.

### 6.3.4 Expressive Power of Languages

When the domain relational calculus is restricted to safe expressions, it is equivalent in expressive power to the tuple relational calculus restricted to safe expressions. Since we noted earlier that the restricted tuple relational calculus is equivalent to the relational algebra, all three of the following are equivalent:

• The basic relational algebra (without the extended relational-algebra operations)

• The tuple relational calculus restricted to safe expressions

• The domain relational calculus restricted to safe expressions

We note that the domain relational calculus also does not have any equivalent of the aggregate operation, but it can be extended to support aggregation, and extending it to handle arithmetic expressions is straightforward.

