---
title: 13.3 Estimating Statistics of Expression Results
weight: 37
---

13.3 Estimating Statistics of Expression Results## 13.3 Estimating Statistics of Expression Results

The cost of an operation depends on the size and other statistics of its inputs. Given an expression such as _a_ (_b  c_) to estimate the cost of joining _a_ with (_b  c_), we need to have estimates of statistics such as the size of _b  c_.

In this section, we first list some statistics about database relations that are stored in database-system catalogs, and then show how to use the statistics to estimate statistics on the results of various relational operations.

One thing that will become clear later in this section is that the estimates are not very accurate, since they are based on assumptions that may not hold exactly. A query-evaluation plan that has the lowest estimated execution cost may therefore not actually have the lowest actual execution cost. However, real- world experience has shown that even if estimates are not precise, the plans with the lowest estimated costs usually have actual execution costs that are either the lowest actual execution costs, or are close to the lowest actual execution costs.

### 13.3.1 Catalog Information

The database-system catalog stores the following statistical information about database relations:  

- _nr_ , the number of tuples in the relation _r_.

- _br_ , the number of blocks containing tuples of relation _r_ .

- _lr_ , the size of a tuple of relation _r_ in bytes.

- _fr_ , the blocking factor of relation _r_ —that is, the number of tuples of relation _r_ that fit into one block.

- _V_(_A, r_ ), the number of distinct values that appear in the relation _r_ for attribute _A_. This value is the same as the size of _πA_(_r_ ). If _A_is a key for relation _r_ , _V_(_A, r_ ) is _nr_ .

The last statistic, _V_(_A, r_ ), can also be maintained for sets of attributes, if desired, instead of just for individual attributes. Thus, given a set of attributes, _π A_, _V_(_A, r_ ) is the size of _A_(_r_ ).

If we assume that the tuples of relation _r_ are stored together physically in a file, the following equation holds:

_br_ \= ⌈ _nr_/_fr_ ⌉

Statistics about indices, such as the heights of B+-tree indices and number of leaf pages in the indices, are also maintained in the catalog.

If we wish to maintain accurate statistics, then, every time a relation is modi- fied, we must also update the statistics. This update incurs a substantial amount of overhead. Therefore, most systems do not update the statistics on every mod- ification. Instead, they update the statistics during periods of light system load. As a result, the statistics used for choosing a query-processing strategy may not be completely accurate. However, if not too many updates occur in the intervals between the updates of the statistics, the statistics will be sufficiently accurate to provide a good estimation of the relative costs of the different plans.

The statistical information noted here is simplified. Real-world optimizers often maintain further statistical information to improve the accuracy of their cost estimates of evaluation plans. For instance, most databases store the distribution of values for each attribute as a **histogram**: in a histogram the values for the attribute are divided into a number of ranges, and with each range the histogram associates the number of tuples whose attribute value lies in that range. Figure 13.6 shows an example of a histogram for an integer-valued attribute that takes values in the range 1 to 25.

Histograms used in database systems usually record the number of distinct values in each range, in addition to the number of tuples with attribute values in that range.

As an example of a histogram, the range of values for an attribute _age_ of a re- lation _person_ could be divided into 0–9, 10–19, . . . , 90–99 (assuming a maximum age of 99). With each range we store a count of the number of _person_ tuples whose _age_ values lie in that range, and the number of distinct age values that lie in that  

![Alt text](image-55.png)

range. Without such histogram information, an optimizer would have to assume that the distribution of values is uniform; that is, each range has the same count.

A histogram takes up only a little space, so histograms on several different at- tributes can be stored in the system catalog. There are several types of histograms used in database systems. For example, an **equi-width histogram** divides the range of values into equal-sized ranges, whereas an **equi-depth** histogram ad- justs the boundaries of the ranges such that each range has the same number of values.

### 13.3.2 Selection Size Estimation

The size estimate of the result of a selection operation depends on the selection predicate. We first consider a single equality predicate, then a single comparison predicate, and finally combinations of predicates.

- σ _A_\= _a_ (_r_ ): If we assume uniform distribution of values (that is, each value ap- pears with equal probability), the selection result can be estimated to have _nr/V_(_A, r_ ) tuples, assuming that the value _a_ appears in attribute _A_ of some record of _r_ . The assumption that the value _a_ in the selection appears in some record is generally true, and cost estimates often make it implicitly. However, it is often not realistic to assume that each value appears with equal prob- ability. The _course id_ attribute in the _takes_ relation is an example where the assumption is not valid. It is reasonable to expect that a popular undergradu- ate course will have many more students than a smaller specialized graduate course. Therefore, certain _course id_ values appear with greater probability than do others. Despite the fact that the uniform-distribution assumption is often not correct, it is a reasonable approximation of reality in many cases, and it helps us to keep our presentation relatively simple.

If a histogram is available on attribute _A_, we can locate the range that contains the value _a_ , and modify the above-mentioned estimate _nr/V_(_A, r_ )  


**COMPUTING AND MAINTAINING STATISTICS**

Conceptually, statistics on relations can be thought of as materialized views, which should be automatically maintained when relations are modified. Unfor- tunately, keeping statistics up-to-date on every insert, delete or update to the database can be very expensive. On the other hand, optimizers generally do not need exact statistics: an error of a few percent may result in a plan that is not quite optimal being chosen, but the alternative plan chosen is likely to have a cost which is within a few percent of the optimal cost. Thus, it is acceptable to have statistics that are approximate.

Database systems reduce the cost of generating and maintaining statistics, as outlined below, by exploiting the fact that statistics can be approximate.

- Statistics are often computed from a sample of the underlying data, instead of examining the entire collection of data. For example, a fairly accurate histogram can be computed from a sample of a few thousand tuples, even on a relation that has millions, or hundreds of millions of records. However, the sample used must be a **random sample**; a sample that is not random may have an excessive representation of one part of the relation, and can give misleading results. For example, if we used a sample of instructors to compute a histogram on salaries, if the sample has an overrepresentation of lower-paid instructors the histogram would result in wrong estimates. Database systems today routinely use random sampling to create statistics. See the bibliographic notes for references on sampling.

- Statistics are not maintained on every update to the database. In fact, some database systems never update statistics automatically. They rely on database administrators periodically running a command to update statis- tics. Oracle and PostgreSQL provide an SQL command called **analyze** that generates statistics on specified relations, or on all relations. IBM DB2 sup- ports an equivalent command called **runstats**. See the system manuals for details. You should be aware that optimizers sometimes choose very bad plans due to incorrect statistics. Many database systems, such as IBM DB2, Oracle, and SQL Server, update statistics automatically at certain points of time. For example, the system can keep approximate track of how many tuples there are in a relation and recompute statistics if this number changes significantly. Another approach is to compare estimated cardinalities of a relation scan with actual cardinalities when a query is executed, and if they differ significantly, initiate an update of statistics for that relation.

by using the frequency count for that range instead of _nr_ , and the number of distinct values that occurs in that range instead of _V_(_A, r_ ).

- _A_≤_v_(_r_ ): Consider a selection of the form _A_≤_v_(_r_ ). If the actual value used in the comparison (_v_) is available at the time of cost estimation, a more accurate estimate can be made. The lowest and highest values (min(_A, r_ ) and max(_A, r_ )) for the attribute can be stored in the catalog. Assuming that values are uniformly distributed, we can estimate the number of records that will satisfy the condition _A_ ≤ _v_ as 0 if _v <_ min(_A, r_ ), as _nr_ if _v_ ≥ max(_A, r_ ), and:

$n_r = \frac{v - \min(A,r)}{\max(A,r) - \min(A,r)}$


otherwise. If a histogram is available on attribute _A_, we can get a more accurate estimate; we leave the details as an exercise for you. In some cases, such as when the query is part of a stored procedure, the value _v_ may not be available when the query is optimized. In such cases, we assume that approximately one-half the records will satisfy the comparison condition. That is, we assume the result has _nr/_2 tuples; the estimate may be very inaccurate, but is the best we can do without any further information.

- Complex selections:

◦ **Conjunction:** A _conjunctive selection_ is a selection of the form:

![Alt text](image-94.png)

We can estimate the result size of such a selection: For each _i_ , we estimate the size of the selection _i_ (_r_ ), denoted by _si_ , as described previously. Thus, the probability that a tuple in the relation satisfies selection condition _i_ is _si/nr_ .

The preceding probability is called the **selectivity** of the selection _i_ (_r_ ). Assuming that the conditions are _independent_ of each other, the probability that a tuple satisfies all the conditions is simply the product of all these probabilities. Thus, we estimate the number of tuples in the full selection as:

$$\frac{S_1 * S_2 * \cdots * S_n}{N_r * \frac{N_n}{N_r}}$$


◦ **Disjunction:** A _disjunctive selection_ is a selection of the form:

$\sigma_1 \vee \sigma_2 \vee \ldots \vee \sigma_n (r)$


A disjunctive condition is satisfied by the union of all records satisfying the individual, simple conditions _i_ .

As before, let _si/nr_ denote the probability that a tuple satisfies condition _i_ . The probability that the tuple will satisfy the disjunction is then 1 minus the probability that it will satisfy _none_ of the conditions:

1 - (1 - S~1~/N~1~) * (1 - S~2~/N~2~) * ... * (1 - S~n~/N~n~)


Multiplying this value by _nr_ gives us the estimated number of tuples that satisfy the selection.  

◦ **Negation:** In the absence of nulls, the result of a selection σ¬~θ~(_r_ ) is simply the tuples of _r_ that are not in σ~θ~(_r_ ). We already know how to estimate the number of tuples in σ~θ~(_r_ ). The number of tuples in σ¬~θ~(_r_ ) is therefore estimated to be _n_(_r_ ) minus the estimated number of tuples in σ~θ~(_r_ ).

We can account for nulls by estimating the number of tuples for which the condition θ would evaluate to _unknown_, and subtracting that number from the above estimate, ignoring nulls. Estimating that number would require extra statistics to be maintained in the catalog.

### 13.3.3 Join Size Estimation

In this section, we see how to estimate the size of the result of a join. The Cartesian product _r_ ×_s_ contains _nr_ ∗_ns_ tuples. Each tuple of _r_ ×_s_ occupies

_lr_ \+ _ls_ bytes, from which we can calculate the size of the Cartesian product. Estimating the size of a natural join is somewhat more complicated than

estimating the size of a selection or of a Cartesian product. Let _r_ (_R_) and _s_(_S_) be relations.

- If _R_ ∩ _S_ \= ∅—that is, the relations have no attribute in common—then _r  s_ is the same as _r_ × _s_, and we can use our estimation technique for Cartesian products.

- If _R_ ∩ _S_ is a key for _R_, then we know that a tuple of _s_ will join with at most one tuple from _r_ . Therefore, the number of tuples in _r  s_ is no greater than the number of tuples in _s_. The case where _R_ ∩ _S_ is a key for _S_ is symmetric to the case just described. If _R_ ∩ _S_ forms a foreign key of _S_, referencing _R_, the number of tuples in _r  s_ is exactly the same as the number of tuples in _s_.

- The most difficult case is when _R_ ∩ _S_ is a key for neither _R_ nor _S_. In this case, we assume, as we did for selections, that each value appears with equal probability. Consider a tuple _t_ of _r_ , and assume _R_ ∩ _S_ \= _{_A_}_. We estimate that tuple _t_ produces:

$$\frac{n_s}{{V(A,s)}}$$


tuples in _r  s_, since this number is the average number of tuples in _s_ with a given value for the attributes _A_. Considering all the tuples in _r_ , we estimate that there are:

$$\frac{n_r ∗ n_s}{{V(A,s)}}$$

tuples in _r  s_. Observe that, if we reverse the roles of _r_ and _s_ in the preceding estimate, we obtain an estimate of:

$$\frac{n_r ∗ n_s}{{V(A,r)}}$$

tuples in _r  s_. These two estimates differ if _V_(_A, r_ ) = _V_(_A, s_). If this situation occurs, there are likely to be dangling tuples that do not participate in the join. Thus, the lower of the two estimates is probably the more accurate one.

The preceding estimate of join size may be too high if the _V_(_A, r_ ) values for attribute _A_ in _r_ have few values in common with the _V_(_A, s_) values for attribute _A_ in _s_. However, this situation is unlikely to happen in the real world, since dangling tuples either do not exist or constitute only a small fraction of the tuples, in most real-world relations.

More important, the preceding estimate depends on the assumption that each value appears with equal probability. More sophisticated techniques for size estimation have to be used if this assumption does not hold. For example, if we have histograms on the join attributes of both relations, and both histograms have the same ranges, then we can use the above estimation technique within each range, using the number of rows with values in the range instead of _nr_ or _ns_ , and the number of distinct values in that range, instead of _V_(_A, r_ ) or _V_(_A, s_). We then add up the size estimates obtained for each range to get the overall size estimate. We leave the case where both relations have histograms on the join attribute, but the histograms have different ranges, as an exercise for you.

We can estimate the size of a theta join _r _ _s_ by rewriting the join as (_r_ × _s_), and using the size estimates for Cartesian products along with the size estimates for selections, which we saw in Section 13.3.2.

To illustrate all these ways of estimating join sizes, consider the expression:

_student  takes_

Assume the following catalog information about the two relations:

- _n~student~_ \= 5000.

- _f~student~_ \= 50, which implies that _b~student~_ \= 5000_/_50 = 100.

- _n~takes~_ \= 10000.

- _f~takes~_ \= 25, which implies that _b~takes~_ \= 10000_/_25 = 400.

- _V_(_ID, takes_) = 2500, which implies that only half the students have taken any course (this is unrealistic, but we use it to show that our size estimates are correct even in this case), and on average, each student who has taken a course has taken four courses.

The attribute _ID_ in _takes_ is a foreign key on _student_, and null values do not occur in _takes_._ID_, since _ID_ is part of the primary key of _takes_; thus, the size of _student  takes_ is exactly _n~takes~_ , which is 10000.

We now compute the size estimates for _student  takes_ without using infor- mation about foreign keys. Since _V_(_ID, takes_) = 2500 and _V_(_ID, student_) = 5000, the two estimates we get are 5000 ∗ 10000/2500 = 20000 and 5000 ∗ 10000/5000 = 10000, and we choose the lower one. In this case, the lower of these estimates is the same as that which we computed earlier from information about foreign keys. 

### 13.3.4 Size Estimation for Other Operations

We outline below how to estimate the sizes of the results of other relational-algebra operations.

- **Projection:** The estimated size (number of records or number of tuples) of a projection of the form _πA_(_r_ ) is _V_(_A, r_ ), since projection eliminates duplicates.

- **Aggregation:** The size of _~A~G~F~_ (_r_ ) is simply _V_(_A, r_ ), since there is one tuple in _~A~G~F~_ (_r_ ) for each distinct value of _A_.

- **Set operations:** If the two inputs to a set operation are selections on the same relation, we can rewrite the set operation as disjunctions, conjunctions, or negations. For example, σ1 (_r_ ) ∪ 2 (_r_ ) can be rewritten as 1∨2 (_r_ ). Simi- larly, we can rewrite intersections as conjunctions, and we can rewrite set difference by using negation, so long as the two relations participating in the set operations are selections on the same relation. We can then use the estimates for selections involving conjunctions, disjunctions, and negation in Section 13.3.2.

If the inputs are not selections on the same relation, we estimate the sizes this way: The estimated size of _r_ ∪ _s_ is the sum of the sizes of _r_ and _s_. The estimated size of _r_ ∩ _s_ is the minimum of the sizes of _r_ and _s_. The estimated size of _r_ − _s_ is the same size as _r_ . All three estimates may be inaccurate, but provide upper bounds on the sizes.

- **Outer join:** The estimated size of _r  s_ is the size of _r  s_ plus the size of _r_ ; that of _r  s_ is symmetric, while that of _r  s_ is the size of _r  s_ plus the sizes of _r_ and _s_. All three estimates may be inaccurate, but provide upper bounds on the sizes.

### 13.3.5 Estimation of Number of Distinct Values

For selections, the number of distinct values of an attribute (or set of attributes) _A_ in the result of a selection, _V_(_A,_ σθ(_r_ )), can be estimated in these ways:

- If the selection condition σθ forces _A_ to take on a specified value (e.g., _A_ \= 3), _V_(_A,_ σθ(_r_ )) = 1.

- If θ forces _A_ to take on one of a specified set of values (e.g., (_A_ \= 1 ∨ _A_ \= 3 ∨ _A_ \= 4)), then _V_(_A,_ σθ(_r_ )) is set to the number of specified values.

- If the selection condition θ is of the form _A op v_, where _op_ is a comparison operator, _V_(_A,_ σθ(_r_ )) is estimated to be _V_(_A, r_ ) ∗ _s_, where _s_ is the selectivity of the selection.

- In all other cases of selections, we assume that the distribution of _A_ values is independent of the distribution of the values on which selection conditions are specified, and we use an approximate estimate of min(_V_(_A, r_ )_, n_σθ(_r_ )). A more accurate estimate can be derived for this case using probability theory, but the above approximation works fairly well.  

For joins, the number of distinct values of an attribute (or set of attributes) _A_ in the result of a join, _V_(_A, r  s_), can be estimated in these ways:

- If all attributes in _A_ are from _r_ , _V_(_A, r  s_) is estimated as min(_V_(_A, r_ )_, nrs_), and similarly if all attributes in _A_ are from _s_, _V_(_A, r  s_) is estimated to be min(_V_(_A, s_)_, nrs_).

- If _A_contains attributes _A_1 from _r_ and _A_2 from _s_, then _V_(_A, r  s_) is estimated as:

min(V(A1),V(A2-A1,S),V(A1-A2,J),V(A2,S),1,Pk,S)


Note that some attributes may be in _A_1 as well as in _A_2, and _A_1 − _A_2 and _A_2− _A_1 denote, respectively, attributes in _A_that are only from _r_ and attributes in _A_ that are only from _s_. Again, more accurate estimates can be derived by using probability theory, but the above approximations work fairly well.

The estimates of distinct values are straightforward for projections: They are the same in _πA_(_r_ ) as in _r_ . The same holds for grouping attributes of aggregation. For results of **sum**, **count**, and **average**, we can assume, for simplicity, that all aggregate values are distinct. For **min**(_A_) and **max**(_A_), the number of distinct values can be estimated as min(_V_(_A, r_ )_, V_(_G, r_ )), where _G_ denotes the grouping attributes. We omit details of estimating distinct values for other operations.

