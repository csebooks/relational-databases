---
title: 'Algorithms for Decomposition'
weight: 5
---

# Algorithms for Decomposition

Real-world database schemas are much larger than the examples that fit in the pages of a book. For this reason, we need algorithms for the generation of designs that are in appropriate normal form. In this section, we present algorithms for BCNF and 3NF.

## BCNF Decomposition

The definition of BCNF can be used directly to test if a relation is in BCNF. However, computation of _F_ \+ can be a tedious task. We first describe below simplified tests for verifying if a relation is in BCNF. If a relation is not in BCNF, it can be decomposed to create relations that are in BCNF. Later in this section, we describe an algorithm to create a lossless decomposition of a relation, such that the decomposition is in BCNF.

###  Testing for BCNF

Testing of a relation schema _R_ to see if it satisfies BCNF can be simplified in some cases:

• To check if a nontrivial dependency α → β causes a violation of BCNF, compute α+ (the attribute closure of α), and verify that it includes all attributes of _R_; that is, it is a superkey of _R_.

• To check if a relation schema _R_ is in BCNF, it suffices to check only the dependencies in the given set _F_ for violation of BCNF, rather than check all dependencies in _F_ +.

We can show that if none of the dependencies in _F_ causes a violation of BCNF, then none of the dependencies in _F_ \+ will cause a violation of BCNF, either.

Unfortunately, the latter procedure does not work when a relation is decomposed. That is, it does not suffice to use F when we test a relation Ri , in a decomposition of R, for violation of BCNF. For example, consider relation schema R (A, B, C, D, E), with functional dependencies F containing A → B and BC → D. Suppose this were decomposed into R1(A, B) and R2(A, C, D, E). Now, neither of the dependencies in F contains only attributes from (A, C, D, E) so we might be misled into thinking R2 satisfies BCNF. In fact, there is a dependency AC → D in F \+ (which can be inferred using the pseudotransitivity rule from the two dependencies in F ) that shows that R2 is not in BCNF. Thus, we may need a dependency that is in _F_ +, but is not in _F_ , to show that a decomposed relation is not in BCNF.

An alternative BCNF test is sometimes easier than computing every depen- dency in _F_ +. To check if a relation _Ri_ in a decomposition of _R_ is in BCNF, we apply this test:

• For every subset α of attributes in _Ri_ , check that α+ (the attribute closure of α under _F_ ) either includes no attribute of _Ri_ − α, or includes all attributes of _Ri_ .  

```
result := {R};
done := false;
compute F +;
while (not done) do
    if (there is a schema Ri in result that is not in BCNF)
        then begin
            let α → β be a nontrivial functional dependency that holds
            on Ri such that α → Ri is not in F +, and α ∩ β = ∅ ;
            result := (result − Ri) ∪ (Ri − β) ∪ ( α, β);
        end
else done := true;

```
**Figure 8.11** BCNF decomposition algorithm.

If the condition is violated by some set of attributes α in _Ri_ , consider the following functional dependency, which can be shown to be present in _F_ +:

α → (α+ − α) ∩ _Ri_ .

The above dependency shows that _Ri_ violates BCNF.

### BCNF Decomposition Algorithm

We are now able to state a general method to decompose a relation schema so as to satisfy BCNF. Figure 8.11 shows an algorithm for this task. If _R_ is not in BCNF, we can decompose _R_ into a collection of BCNF schemas _R_1_, R_2_, . . . , Rn_ by the algorithm. The algorithm uses dependencies that demonstrate violation of BCNF to perform the decomposition.

The decomposition that the algorithm generates is not only in BCNF, but is also a lossless decomposition. To see why our algorithm generates only lossless decompositions, we note that, when we replace a schema _Ri_ with (_Ri_ − β) and (α_,_ β), the dependency α → β holds, and (_Ri_ − β) ∩ (α_,_ β) = α.

If we did not require α ∩ β = ∅, then those attributes in α ∩ β would not appear in the schema (_Ri_ − β) and the dependency α → β would no longer hold.

It is easy to see that our decomposition of _inst dept_ in Section 8.3.2 would result from applying the algorithm. The functional dependency _dept name_ → _building_, _budget_ satisfies the α ∩ β = ∅ condition and would therefore be chosen to decompose the schema.

The BCNF decomposition algorithm takes time exponential in the size of the initial schema, since the algorithm for checking if a relation in the decomposition satisfies BCNF can take exponential time. The bibliographical notes provide ref- erences to an algorithm that can compute a BCNF decomposition in polynomial time. However, the algorithm may “overnormalize,” that is, decompose a relation unnecessarily.

As a longer example of the use of the BCNF decomposition algorithm, suppose we have a database design using the _class_ schema below:  


_class_ (_course id_, _title_, _dept name_, _credits_, _sec id_, _semester_, _year_, _building_, _room number_, _capacity_, _time slot id_)

The set of functional dependencies that we require to hold on _class_ are:

_course id_ → _title_, _dept name_, _credits 
building_, _room number_ → _capacity
course id_, _sec id_, _semester_,_year_→ _building_, _room number_,_time slot id_

A candidate key for this schema is _{course id_, _sec id_, _semester_, _year}_. We can apply the algorithm of Figure 8.11 to the _class_ example as follows:

• The functional dependency:

_course id_ → _title_, _dept name_, _credits_

holds, but _course id_ is not a superkey. Thus, _class_ is not in BCNF. We replace _class_ by:

_course_(_course id_, _title_, _dept name_, _credits_) 
_class-1_ (_course id_, _sec id_,_semester_, _year_, _building_,room number_

_capacity_, _time slot id_)

The only nontrivial functional dependencies that hold on _course_ include _course id_ on the left side of the arrow. Since _course id_ is a key for _course_, the relation

_course_ is in BCNF.

• A candidate key for _class-1_ is _{course id_, _sec id_, _semester_, _year}_. The functional dependency:

_building_, _room number_ → capacity_

holds on _class-1_, but _{building_, _room number}_ is not a superkey for _class-1_. We replace _class-1_ by:

_classroom_ (_building_, _room number_, _capacity_) 
_section_ (_course id_, _sec id_, _semester_, _year_,_building_, _room number_, _time slot id_)

_classroom_ and _section_ are in BCNF.

Thus, the decomposition of _class_ results in the three relation schemas _course_, _class- room_, and _section_, each of which is in BCNF. These correspond to the schemas that we have used in this, and previous, chapters. You can verify that the decomposi- tion is lossless and dependency preserving.  

```
let Fc be a canonical cover for F;
i := 0;
for each functional dependency α → β in Fc
i := i + 1;
Ri := α β;
if none of the schemas Rj, j = 1, 2,...,i contains a candidate key for R
then
i := i + 1;
Ri := any candidate key for R;
/* Optionally, remove redundant relations */
repeat
if any schema Rj is contained in another schema Rk
then
/* Delete Rj */
Rj := Ri ;
i := i - 1;
until no more Rj s can be deleted
return (R1, R2,..., Ri)
```

**Figure 8.12** Dependency-preserving, lossless decomposition into 3NF.

## 3NF Decomposition

Figure 8.12 shows an algorithm for finding a dependency-preserving, lossless decomposition into 3NF. The set of dependencies Fc used in the algorithm is a canonical cover for F. Note that the algorithm considers the set of schemas Rj , j \= 1, 2, . . . , i ; initially i \= 0, and in this case the set is empty.

Let us apply this algorithm to our example of Section 8.3.4, where we showed that:

dept advisor (s ID, i ID, dept name)

is in 3NF even though it is not in BCNF. The algorithm uses the following functional dependencies in F :

f1: i_ID → dept name 
f2: s_ID, dept name → i_ID

There are no extraneous attributes in any of the functional dependencies in F , so Fc contains f1 and f2\. The algorithm then generates as R1 the schema, (i ID dept name), and as R2 the schema (s ID, dept name, i ID). The algorithm then finds that R2 contains a candidate key, so no further relation schema is created.

The resultant set of schemas can contain redundant schemas, with one schema Rk containing all the attributes of another schema Rj . For example, R2 above contains all the attributes from R1\. The algorithm deletes all such schemas that are contained in another schema. Any dependencies that could be tested on an _Rj_ that is deleted can also be tested on the corresponding relation _Rk_ , and the decomposition is lossless even if _Rj_ is deleted.

Now let us consider again the _class_ schema of Section 8.5.1.2 and apply the 3NF decomposition algorithm. The set of functional dependencies we listed there happen to be a canonical cover. As a result, the algorithm gives us the same three schemas _course, classroom_, and _section_.

The above example illustrates an interesting property of the 3NF algorithm. Sometimes, the result is not only in 3NF, but also in BCNF. This suggests an alternative method of generating a BCNF design. First use the 3NF algorithm. Then, for any schema in the 3NF design that is not in BCNF, decompose using the BCNF algorithm. If the result is not dependency-preserving, revert to the 3NF design.

## Correctness of the 3NF Algorithm

The 3NF algorithm ensures the preservation of dependencies by explicitly building a schema for each dependency in a canonical cover. It ensures that the decomposi- tion is a lossless decomposition by guaranteeing that at least one schema contains a candidate key for the schema being decomposed. Practice Exercise 8.14 provides some insight into the proof that this suffices to guarantee a lossless decomposition.

This algorithm is also called the **3NF synthesis algorithm**, since it takes a set of dependencies and adds one schema at a time, instead of decomposing the initial schema repeatedly. The result is not uniquely defined, since a set of functional dependencies can have more than one canonical cover, and, further, in some cases, the result of the algorithm depends on the order in which it considers the dependencies in _Fc_ . The algorithm may decompose a relation even if it is already in 3NF; however, the decomposition is still guaranteed to be in 3NF.

If a relation _Ri_ is in the decomposition generated by the synthesis algorithm, then _Ri_ is in 3NF. Recall that when we test for 3NF it suffices to consider functional dependencies whose right-hand side is a single attribute. Therefore, to see that _Ri_ is in 3NF you must convince yourself that any functional dependency γ → _B_ that holds on _Ri_ satisfies the definition of 3NF. Assume that the dependency that generated _Ri_ in the synthesis algorithm is α → β. Now, _B_ must be in α or β, since _B_ is in _Ri_ and α → β generated _Ri_ . Let us consider the three possible cases:

• _B_ is in both α and β. In this case, the dependency α → β would not have been in _Fc_ since _B_ would be extraneous in β. Thus, this case cannot hold.

• _B_ is in β but not α. Consider two cases:

◦ γ is a superkey. The second condition of 3NF is satisfied.

◦ γ is not a superkey. Then α must contain some attribute not in γ . Now, since γ → _B_ is in _F_ +, it must be derivable from _Fc_ by using the attribute closure algorithm on γ . The derivation could not have used α → β— if it had been used, α must be contained in the attribute closure of γ , which is not possible, since we assumed γ is not a superkey. Now, using α → (β − {_B_}) and γ → _B_, we can derive α → _B_ (since γ ⊆ αβ, and γ 
cannot contain _B_ because γ → _B_ is nontrivial). This would imply that _B_ is extraneous in the right-hand side of α → β, which is not possible since α → β is in the canonical cover _Fc_ . Thus, if _B_ is in β, then γ must be a superkey, and the second condition of 3NF must be satisfied.

• _B_ is in α but not β. Since α is a candidate key, the third alternative in the definition of 3NF is satisfied.

Interestingly, the algorithm we described for decomposition into 3NF can be implemented in polynomial time, even though testing a given relation to see if it satisfies 3NF is NP-hard (which means that it is very unlikely that a polynomial- time algorithm will ever be invented for this task).

## Comparison of BCNF and NF

Of the two normal forms for relational database schemas, 3NF and BCNF there are advantages to 3NF in that we know that it is always possible to obtain a 3NF design without sacrificing losslessness or dependency preservation. Nevertheless, there are disadvantages to 3NF: We may have to use null values to represent some of the possible meaningful relationships among data items, and there is the problem of repetition of information.

Our goals of database design with functional dependencies are:

**1\.** BCNF.

**2\.** Losslessness.

**3\.** Dependency preservation.

Since it is not always possible to satisfy all three, we may be forced to choose between BCNF and dependency preservation with 3NF.

It is worth noting that SQL does not provide a way of specifying functional dependencies, except for the special case of declaring superkeys by using the **primary key** or **unique** constraints. It is possible, although a little complicated, to write assertions that enforce a functional dependency (see Practice Exercise 8.9); unfortunately, currently no database system supports the complex assertions that are required to enforce a functional dependency, and the assertions would be expensive to test. Thus even if we had a dependency-preserving decomposition, if we use standard SQL we can test efficiently only those functional dependencies whose left-hand side is a key.

Although testing functional dependencies may involve a join if the decom- position is not dependency preserving, we could in principle reduce the cost by using materialized views, which many database systems support, provided the database system supports primary key constraints on materialized views. Given a BCNF decomposition that is not dependency preserving, we consider each de- pendency in a canonical cover _Fc_ that is not preserved in the decomposition. For each such dependency α → β, we define a materialized view that computes a join of all relations in the decomposition, and projects the result on αβ. The func- tional dependency can be tested easily on the materialized view, using one of the constraints **unique** (α) or **primary key** (α).

On the negative side, there is a space and time overhead due to the material- ized view, but on the positive side, the application programmer need not worry about writing code to keep redundant data consistent on updates; it is the job of the database system to maintain the materialized view, that is, keep it up to date when the database is updated. (Later in the book, in Section 13.5, we outline how a database system can perform materialized view maintenance efficiently.)

Unfortunately, most current database systems do not support constraints on materialized views. Although the Oracle database does support constraints on materialized views, by default it performs view maintenance when the view is accessed, not when the underlying relation is updated;7 as a result a constraint violation may get detected well after the update has been performed, which makes the detection useless.

Thus, in case we are not able to get a dependency-preserving BCNF decom- position, it is generally preferable to opt for BCNF, since checking functional dependencies other than primary key constraints is difficult in SQL.

