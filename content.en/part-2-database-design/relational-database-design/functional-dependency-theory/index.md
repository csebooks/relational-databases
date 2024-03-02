---
title: 'Functional-Dependency Theory'
weight: 4
references:
    videos:
        - youtube:dR-jJimWWHA
        - youtube:Wd1IaVyl-bg
    links:
        - https://www.javatpoint.com/dbms-functional-dependency
        - https://www.geeksforgeeks.org/types-of-functional-dependencies-in-dbms/
    books:
        - b1:
            title: Dependency Theory
            url: https://www.google.co.in/books/edition/Dependency_Theory_Revisited/Q-KRAAAAIAAJ?hl=en&gbpv=0&bsq=Functional-Dependency%20Theory%20books
        - b2:
            title: Time-Dependent Density-Functional
            url: https://www.google.co.in/books/edition/Time_Dependent_Density_Functional_Theory/hCNNsC4sEtkC?hl=en&gbpv=0
---

# Functional-Dependency Theory

We have seen in our examples that it is useful to be able to reason systematically about functional dependencies as part of a process of testing schemas for BCNF or 3NF.

## Closure of a Set of Functional Dependencies

We shall see that, given a set _F_ of functional dependencies on a schema, we can prove that certain other functional dependencies also hold on the schema. We say that such functional dependencies are “logically implied” by _F_. When testing for normal forms, it is not sufficient to consider the given set of functional dependencies; rather, we need to consider _all_ functional dependencies that hold on the schema.

More formally, given a relational schema _r_ (_R_), a functional dependency _f_ on _R_ is **logically implied** by a set of functional dependencies _F_ on _r_ if every instance of _r_ (_R_) that satisfies _F_ also satisfies _f_ .

Suppose we are given a relation schema _r_ (_A_, _B_, _C_ , _G_, _H_, _I_ ) and the set of functional dependencies:

_A_ → _B 
A_ → _C 
CG_ → _H 
CG_ → _I 
B_ → _H_

The functional dependency:

_A_ → _H_

is logically implied. That is, we can show that, whenever a relation satisfies our given set of functional dependencies, _A_ → _H_ must also be satisfied by that relation. Suppose that t1 and t2 are tuples such that:

t1[_A_] = t2[_A_]

Since we are given that _A_ → _B_, it follows from the definition of functional depen- dency that:

t1[_B_] = t2[_B_]  


Then, since we are given that _B_ → _H_, it follows from the definition of functional dependency that:

t1[_H_] = t2[_H_]

Therefore, we have shown that, whenever t1 and t2 are tuples such that t1[_A_] = t2[_A_], it must be that t1[_H_] = t2[_H_]. But that is exactly the definition of _A_ → _H_.

Let _F_ be a set of functional dependencies. The **closure** of _F_, denoted by _F_ +, is the set of all functional dependencies logically implied by _F_. Given _F_, we can compute _F_ \+ directly from the formal definition of functional dependency. If _F_ were large, this process would be lengthy and difficult. Such a computation of _F_ \+ requires arguments of the type just used to show that _A_ → _H_ is in the closure of our example set of dependencies.

**Axioms**, or rules of inference, provide a simpler technique for reasoning about functional dependencies. In the rules that follow, we use Greek letters (α, β, γ , _. . ._ ) for sets of attributes, and uppercase Roman letters from the beginning of the alphabet for individual attributes. We use α to denote αβ ∪ β.

We can use the following three rules to find logically implied functional dependencies. By applying these rules _repeatedly_, we can find all of _F_ +, given _F_. This collection of rules is called **Armstrong’s axioms** in honor of the person who first proposed it.

• **Reflexivity rule**. If α is a set of attributes and β ⊆ α, then α → β holds.

• **Augmentation rule**. If α → β holds and γ is a set of attributes, then γα → γβ holds.

• **Transitivity rule**. If α → β holds and β → γ holds, then α → γ holds.

Armstrong’s axioms are **sound**, because they do not generate any incorrect functional dependencies. They are **complete**, because, for a given set _F_ of func- tional dependencies, they allow us to generate all _F_ +. The bibliographical notes provide references for proofs of soundness and completeness.

Although Armstrong’s axioms are complete, it is tiresome to use them directly for the computation of _F_ +. To simplify matters further, we list additional rules. It is possible to use Armstrong’s axioms to prove that these rules are sound (see Practice Exercises 8.4 and 8.5 and Exercise 8.26).

• **Union rule**. If α → β holds and α → γ holds, then α → βγ holds.

• **Decomposition rule**. If α → βγ holds, then α → β holds and α → γ holds.

• **Pseudotransitivity rule**. If α → β holds and γβ → 𝛿 holds, then αγ → 𝛿 holds.

Let us apply our rules to the example of schema _R_ \= (_A_, _B_, _C_ , _G_, _H_, _I_ ) and the set _F_ of functional dependencies {_A_ → _B_, _A_ → _C_ , _CG_ → _H_, _CG_ → _I_ , _B_ → _H_}. We list several members of _F_ \+ here:  

**340 Chapter 8 Relational Database Design**

• _A_ → _H_. Since _A_ → _B_ and _B_ → _H_ hold, we apply the transitivity rule. Observe that it was much easier to use Armstrong’s axioms to show that _A_ → _H_ holds than it was to argue directly from the definitions, as we did earlier in this section.

• _CG_ → _HI_ . Since _CG_ → _H_ and _CG_ → _I_ , the union rule implies that _CG_ → _HI_ .

• _AG_ → _I_ . Since _A_ → _C_ and _CG_ → _I_ , the pseudotransitivity rule implies that _AG_ → _I_ holds.

Another way of finding that _AG_ → _I_ holds is as follows: We use the augmentation rule on _A_ → _C_ to infer _AG_ → _CG_. Applying the transitivity rule to this dependency and _CG_ → _I_ , we infer _AG_ → _I_ .

Figure 8.7 shows a procedure that demonstrates formally how to use Arm- strong’s axioms to compute _F_ +. In this procedure, when a functional dependency is added to _F_ +, it may be already present, and in that case there is no change to _F_ +. We shall see an alternative way of computing _F_ \+ in Section 8.4.2.

The left-hand and right-hand sides of a functional dependency are both sub- sets of _R_. Since a set of size _n_ has 2_n_ subsets, there are a total of 2 ^n^ × 2 ^n^ \= 2^2n^
possible functional dependencies, where _n_ is the number of attributes in _R_. Each iteration of the repeat loop of the procedure, except the last iteration, adds at least one functional dependency to _F_ +. Thus, the procedure is guaranteed to terminate.

## Closure of Attribute Sets

We say that an attribute _B_ is **functionally determined** by α if α → _B_. To test whether a set α is a superkey, we must devise an algorithm for computing the set of attributes functionally determined by α. One way of doing this is to compute _F_ +, take all functional dependencies with α as the left-hand side, and take the union of the right-hand sides of all such dependencies. However, doing so can be expensive, since _F_ \+ can be large.

An efficient algorithm for computing the set of attributes functionally deter- mined by α is useful not only for testing whether α is a superkey, but also for several other tasks, as we shall see later in this section.
```
F + = F
repeat
    for each functional dependency f in F +
        apply reflexivity and augmentation rules on f
        add the resulting functional dependencies to F +
for each pair of functional dependencies f1 and f2 in F +
        if f1 and f2 can be combined using transitivity
            add the resulting functional dependency to F +
until F + does not change any further

```

**Figure 8.7** A procedure to compute _F_ +.  


```
result := α;
repeat
    for each functional dependency β → γ in F do
        begin
            if β ⊆ result then result := result ∪ γ;
        end
until (result does not change)
```
**Figure 8.8** An algorithm to compute α+, the closure of α under _F_.

Let α be a set of attributes. We call the set of all attributes functionally de- termined by α under a set _F_ of functional dependencies the **closure** of α under _F_; we denote it by α+. Figure 8.8 shows an algorithm, written in pseudocode, to compute α+. The input is a set _F_ of functional dependencies and the set α of attributes. The output is stored in the variable _result_.

To illustrate how the algorithm works, we shall use it to compute (_AG_)+ with the functional dependencies defined in Section 8.4.1. We start with _result_ \= _AG_ . The first time that we execute the **repeat** loop to test each functional dependency, we find that:

• _A_ → _B_ causes us to include _B_ in _result_. To see this fact, we observe that _A_ → _B_ is in _F_, _A_ ⊆ _result_ (which is _AG_), so _result_ := _result_ ∪_B_.

• _A_ → _C_ causes _result_ to become _ABCG_.

• _CG_ → _H_ causes _result_ to become _ABCGH_.

• _CG_ → _I_ causes _result_ to become _ABCGHI_.

The second time that we execute the **repeat** loop, no new attributes are added to _result_, and the algorithm terminates.

Let us see why the algorithm of Figure 8.8 is correct. The first step is correct, since α → α always holds (by the reflexivity rule). We claim that, for any subset β of _result_, α → β. Since we start the **repeat** loop with α → _result_ being true, we can add γ to _result_ only if β ⊆ _result_ and β → γ . But then _result_ → β by the reflexivity rule, so α → β by transitivity. Another application of transitivity shows that α → γ (using α → β and β → γ ). The union rule implies that α → _result_ ∪ γ , so α functionally determines any new result generated in the **repeat** loop. Thus, any attribute returned by the algorithm is in α+.

It is easy to see that the algorithm finds all of α+. If there is an attribute in α+ that is not yet in _result_ at any point during the execution, then there must be a functional dependency β → γ for which β ⊆ _result_, and at least one attribute in γ is not in _result_. When the algorithm terminates, all such functional dependencies have been processed, and the attributes in γ added to _result_; we can thus be sure that all attributes in α+ are in _result_.  

It turns out that, in the worst case, this algorithm may take an amount of time quadratic in the size of _F_. There is a faster (although slightly more complex) algorithm that runs in time linear in the size of _F_; that algorithm is presented as part of Practice Exercise 8.8.

There are several uses of the attribute closure algorithm:

• To test if α is a superkey, we compute α+, and check if α+ contains all attributes in _R_.

• We can check if a functional dependency α → β holds (or, in other words, is in _F_ +), by checking if β ⊆ α+. That is, we compute α+ by using attribute closure, and then check if it contains β. This test is particularly useful, as we shall see later in this chapter.

• It gives us an alternative way to compute _F_ +: For each γ ⊆ _R_, we find the closure γ+, and for each _S_ ⊆ γ+, we output a functional dependency γ → _S_.

## Canonical Cover

Suppose that we have a set of functional dependencies _F_ on a relation schema. Whenever a user performs an update on the relation, the database system must ensure that the update does not violate any functional dependencies, that is, all the functional dependencies in _F_ are satisfied in the new database state.

The system must roll back the update if it violates any functional dependen- cies in the set _F_ .

We can reduce the effort spent in checking for violations by testing a simplified set of functional dependencies that has the same closure as the given set. Any database that satisfies the simplified set of functional dependencies also satisfies the original set, and vice versa, since the two sets have the same closure. However, the simplified set is easier to test. We shall see how the simplified set can be constructed in a moment. First, we need some definitions.

An attribute of a functional dependency is said to be **extraneous** if we can remove it without changing the closure of the set of functional dependencies. The formal definition of **extraneous attributes** is as follows: Consider a set _F_ of functional dependencies and the functional dependency α → β in _F_.

• Attribute _A_ is extraneous in α if _A_ ∈ α, and _F_ logically implies (_F_ − {α → β}) ∪ {(α − _A_) → β}.

• Attribute _A_ is extraneous in β if _A_ ∈ β, and the set of functional dependencies (_F_ − _{_α → β_}_) ∪ {α → (β − _A_)} logically implies _F_.

For example, suppose we have the functional dependencies _AB_ → _C_ and _A_ → _C_ in _F_ . Then, _B_ is extraneous in _AB_ → _C_ . As another example, suppose we have the functional dependencies _AB_ → _C D_ and _A_ → _C_ in _F_ . Then _C_ would be extraneous in the right-hand side of _AB_ → _C D_.

Beware of the direction of the implications when using the definition of ex- traneous attributes: If you exchange the left-hand side with the right-hand side,  

```
Fc = F
repeat
    Use the union rule to replace any dependencies in Fc of the form
        α1 → β1 and α1 → β2 with α1 → β1 β2.
    Find a functional dependency α → β in Fc with an extraneous     
        attribute either in α or in β.
        /* Note: the test for extraneous attributes is done using Fc , not F */
    If an extraneous attribute is found, delete it from α → β in Fc .
until (Fc does not change)
```

**Figure 8.9** Computing canonical cover.

the implication will _always_ hold. That is, (_F_ − {α → β}) ∪ {(α − _A_) → β} al- ways logically implies _F_, and also _F_ always logically implies (_F_ − _{_α → β_}_) ∪ {α → (β − _A_)}.

Here is how we can test efficiently if an attribute is extraneous. Let _R_ be the relation schema, and let _F_ be the given set of functional dependencies that hold on _R_. Consider an attribute _A_ in a dependency α → β.

• If _A_ ∈ β, to check if _A_ is extraneous, consider the set

_F_ ′ = (_F_ − _{_α → β_}_) ∪ {α → (β − _A_)} and check if α → _A_can be inferred from _F_ ′. To do so, compute α+ (the closure of α) under _F_ ′; if α+ includes _A_, then _A_ is extraneous in β.

• If _A_ ∈ α, to check if _A_ is extraneous, let γ = α − {_A_}, and check if γ → β can be inferred from _F_ . To do so, compute γ+ (the closure of γ ) under _F_ ; if γ+ includes all attributes in β, then _A_ is extraneous in α.

For example, suppose _F_ contains _AB_ → _C D_, _A_ → _E_ , and _E_ → _C_ . To check if _C_ is extraneous in _AB_ → _C D_, we compute the attribute closure of _AB_ under _F_ ′ = {_AB_ → _D_, _A_ → _E_ , and _E_ → _C_}. The closure is _ABC DE_ , which includes _C D_, so we infer that _C_ is extraneous.

A **canonical cover** _Fc_ for _F_ is a set of dependencies such that _F_ logically implies all dependencies in _Fc_ , and _Fc_ logically implies all dependencies in _F_. Furthermore, _Fc_ must have the following properties:

• No functional dependency in _Fc_ contains an extraneous attribute.

• Each left side of a functional dependency in _Fc_ is unique. That is, there are no two dependencies α1 → β1 and α2 → β2 in _Fc_ such that α1 = α2.

A canonical cover for a set of functional dependencies _F_ can be computed as depicted in Figure 8.9. It is important to note that when checking if an attribute is extraneous, the check uses the dependencies in the current value of _Fc_ , and **not** the dependencies in _F_ . If a functional dependency contains only one attribute in its right-hand side, for example _A_ → _C_ , and that attribute is found to be extraneous, we would get a functional dependency with an empty right-hand side. Such functional dependencies should be deleted.

The canonical cover of _F_ , _Fc_ , can be shown to have the same closure as _F_ ; hence, testing whether _Fc_ is satisfied is equivalent to testing whether _F_ is satis- fied. However, _Fc_ is minimal in a certain sense—it does not contain extraneous attributes, and it combines functional dependencies with the same left side. It is cheaper to test _Fc_ than it is to test _F_ itself.

Consider the following set _F_ of functional dependencies on schema (_A, B, C_):

_A_ → _BC B_ → _C A_ → _B AB_ → _C_

Let us compute the canonical cover for _F_.

• There are two functional dependencies with the same set of attributes on the left side of the arrow:

_A_ → _BC A_ → _B_

We combine these functional dependencies into _A_ → _BC_.

• _A_ is extraneous in _AB_ → _C_ because _F_ logically implies (_F_ − {_AB_ → _C_}) ∪ {_B_ → _C_}. This assertion is true because _B_ → _C_ is already in our set of functional dependencies.

• _C_ is extraneous in _A_ → _BC_, since _A_ → _BC_ is logically implied by _A_ → _B_ and _B_ → _C_.

Thus, our canonical cover is:

_A_ → _B B_ → _C_

Given a set _F_ of functional dependencies, it may be that an entire functional dependency in the set is extraneous, in the sense that dropping it does not change the closure of _F_ . We can show that a canonical cover _Fc_ of _F_ contains no such extraneous functional dependency. Suppose that, to the contrary, there were such an extraneous functional dependency in _Fc_ . The right-side attributes of the de- pendency would then be extraneous, which is not possible by the definition of canonical covers.

A canonical cover might not be unique. For instance, consider the set of functional dependencies _F_ \= {_A_ → _BC_ , _B_ → _AC_ , and _C_ → _AB_}. If we apply the extraneity test to _A_ → _BC_ , we find that both _B_ and _C_ are extraneous under _F_ . However, it is incorrect to delete both! The algorithm for finding the canonical cover picks one of the two, and deletes it. Then,

**1\.** If _C_ is deleted, we get the set _F_ ′ = {_A_ → _B_, _B_ → _AC_ , and _C_ → _AB_}. Now, _B_ is not extraneous in the side of _A_ → _B_ under _F_ ′. Continuing the algorithm, we find _A_ and _B_ are extraneous in the right-hand side of _C_ → _AB_, leading to two canonical covers

_Fc_ \= {_A_ → _B_, _B_ → _C_ , _C_ → _A_} 
_Fc_ \= {_A_ → _B_, _B_ → _AC_ , _C_ → _B_}.

**2\.** If _B_ is deleted, we get the set {_A_ → _C_ , _B_ → _AC_ , and _C_ → _AB_}. This case is symmetrical to the previous case, leading to the canonical covers

_Fc_ \= {_A_ → _C_ , _C_ → _B_, and _B_ → _A_} 
_Fc_ \= {_A_ → _C_ , _B_ → _C_ , and _C_ → _AB_}.

As an exercise, can you find one more canonical cover for _F_ ?

## Lossless Decomposition

Let _r_ (_R_) be a relation schema, and let _F_ be a set of functional dependencies on _r_ (_R_). Let _R_1 and _R_2 form a decomposition of _R_. We say that the decomposition is a **lossless decomposition** if there is no loss of information by replacing _r_ (_R_) with two relation schemas _r_1(_R_1) and _r_2(_R_2). More precisely, we say the decomposition is lossless if, for all legal database instances (that is, database instances that satisfy the specified functional dependencies and other constraints), relation _r_ contains the same set of tuples as the result of the following SQL query:

**select** \* 
**from** (**select** _R_1 **from** r)
**natural join** 
(**select** _R_2 **from** r)

This is stated more succinctly in the relational algebra as:

αR1 (r) ✶ αR2 (r) = r

In other words, if we project _r_ onto _R_1 and _R_2, and compute the natural join of the projection results, we get back exactly _r_ . A decomposition that is not a lossless decomposition is called a **lossy decomposition**. The terms **lossless-join decomposition** and **lossy-join decomposition** are sometimes used in place of lossless decomposition and lossy decomposition.

As an example of a lossy decomposition, recall the decomposition of the _employee_ schema into:  

_employee1_ (_ID_, _name_) 
_employee2_ (_name_, _street_, _city_, _salary_)

that we saw earlier in Section 8.1.2. As we saw in Figure 8.3, the result of _employee1 α employee2_ is a superset of the original relation _employee_, but the decomposition is lossy since the join result has lost information about which employee identi- fiers correspond to which addresses and salaries, in the case where two or more employees have the same name.

We can use functional dependencies to show when certain decompositions are lossless. Let R, R1, R2, and F be as above. R1 and R2 form a lossless decomposition of R if at least one of the following functional dependencies is in F +:

• R1 ∩ R2 → R1

• R1 ∩ R2 → R2

In other words, if R1 ∩ R2 forms a superkey of either R1 or R2, the decomposition of R is a lossless decomposition. We can use attribute closure to test efficiently for superkeys, as we have seen earlier.

To illustrate this, consider the schema

_inst dept_ (_ID_, _name_, _salary_, _dept name_, _building_, _budget_)

that we decomposed in Section 8.1.2 into the _instructor_ and _department_ schemas:

_instructor_ (_ID_, _name_, _dept name_, _salary_) 
_department_ (_dept name_, _building_, _budget_)

Consider the intersection of these two schemas, which is _dept name_. We see that because _dept name_→ _dept name_, _building_, _budget_, the lossless-decomposition rule is satisfied.

For the general case of decomposition of a schema into multiple schemas at once, the test for lossless decomposition is more complicated. See the bibliograph- ical notes for references on the topic.

While the test for binary decomposition is clearly a sufficient condition for lossless decomposition, it is a necessary condition only if all constraints are func- tional dependencies. We shall see other types of constraints later (in particular, a type of constraint called multivalued dependencies discussed in Section 8.6.1), that can ensure that a decomposition is lossless even if no functional dependencies are present.

## Dependency Preservation

Using the theory of functional dependencies, it is easier to characterize depen- dency preservation than using the ad-hoc approach we took in Section 8.3.3.  

Let _F_ be a set of functional dependencies on a schema R, and let R1, R2, . . . , Rn be a decomposition of R. The **restriction** of F to Ri is the set Fi of all functional dependencies in F \+ that include only attributes of Ri . Since all functional depen- dencies in a restriction involve attributes of only one relation schema, it is possible to test such a dependency for satisfaction by checking only one relation.

Note that the definition of restriction uses all dependencies in F +, not just those in F . For instance, suppose F \= {A → B, B → C}, and we have a decompo- sition into AC and AB. The restriction of F to AC includes A → C , since A → C is in F +, even though it is not in F .

The set of restrictions F1, F2, . . . , Fn is the set of dependencies that can be checked efficiently. We now must ask whether testing only the restrictions is sufficient. Let F ′ = F1 ∪ F2 ∪ · · · ∪ Fn. F ′ is a set of functional dependencies on schema R, but, in general, F ′ β= F . However, even if F ′ β= F , it may be that F ′+ = F +. If the latter is true, then every dependency in F is logically implied by F ′, and, if we verify that F ′ is satisfied, we have verified that F is satisfied. We say that a decomposition having the property F ′+ = F \+ is a **dependency-preserving decomposition**.

Figure 8.10 shows an algorithm for testing dependency preservation. The input is a set _D_ \= _{R_1_, R_2_, . . . , Rn}_ of decomposed relation schemas, and a set _F_ of functional dependencies. This algorithm is expensive since it requires computation of _F_ +. Instead of applying the algorithm of Figure 8.10, we consider two alternatives.

First, note that if each member of _F_ can be tested on one of the relations of the decomposition, then the decomposition is dependency preserving. This is an easy way to show dependency preservation; however, it does not always work. There are cases where, even though the decomposition is dependency preserving, there is a dependency in _F_ that cannot be tested in any one relation in the decomposition. Thus, this alternative test can be used only as a sufficient condition that is easy

```
compute F +;
for each schema Ri in D do
    begin
        Fi : = the restriction of F + to Ri ;
    end
F' := ∅
for each restriction Fi do
    begin
        F' = F' ∪ Fi
    end
compute F'+;
if (F'+ = F +) then return (true)
                else return (false);

```
**Figure 8.10** Testing for dependency preservation.  

to check; if it fails we cannot conclude that the decomposition is not dependency preserving; instead we will have to apply the general test.

We now give a second alternative test for dependency preservation that avoids computing _F_ +. We explain the intuition behind the test after presenting the test. The test applies the following procedure to each α → β in _F_ .

```
result = α
repeat
    for each Ri in the decomposition
        t = (result ∩ Ri)+ ∩ Ri
        result = result ∪ t
until (result does not change)
```
The attribute closure here is under the set of functional dependencies _F_ . If _result_ contains all attributes in β, then the functional dependency α → β is preserved. The decomposition is dependency preserving if and only if the procedure shows that all the dependencies in _F_ are preserved.

The two key ideas behind the above test are as follows:

• The first idea is to test each functional dependency α → β in _F_ to see if it is preserved in _F_ ′ (where _F_ ′ is as defined in Figure 8.10). To do so, we compute the closure of α under _F_ ′; the dependency is preserved exactly when the closure includes β. The decomposition is dependency preserving if (and only if) all the dependencies in _F_ are found to be preserved.

• The second idea is to use a modified form of the attribute-closure algorithm to compute closure under _F_ ′, without actually first computing _F_ ′. We wish to avoid computing _F_ ′ since computing it is quite expensive. Note that _F_ ′ is the union of _Fi_ , where _Fi_ is the restriction of _F_ on _Ri_ . The algorithm computes the attribute closure of (_result_ ∩ _Ri_ ) with respect to _F_ , intersects the closure with _Ri_ , and adds the resultant set of attributes to _result_; this sequence of steps is equivalent to computing the closure of _result_ under _Fi_ . Repeating this step for each _i_ inside the while loop gives the closure of _result_ under _F_ ′.

To understand why this modified attribute-closure approach works cor- rectly, we note that for any γ ⊆ _Ri_ , γ → γ+ is a functional dependency in _F_ +, and γ → γ+ ∩ _Ri_ is a functional dependency that is in _Fi_ , the restriction of _F_ \+ to _Ri_ . Conversely, if γ → 𝛿 were in _Fi_ , then 𝛿 would be a subset of γ+ ∩ _Ri_ .

This test takes polynomial time, instead of the exponential time required to compute _F_ +.

