---
title: Interoperation Parallelism
weight: 14
---

Interoperation Parallelism## Interoperation Parallelism

There are two forms of interoperation parallelism: pipelined parallelism and independent parallelism.

### Pipelined Parallelism

As discussed in Chapter 12, pipelining forms an important source of economy of computation for database query processing. Recall that, in pipelining, the output tuples of one operation, _A_, are consumed by a second operation, _B_, even before the first operation has produced the entire set of tuples in its output. The major advantage of pipelined execution in a sequential evaluation is that we can carry out a sequence of such operations without writing any of the intermediate results to disk.

Parallel systems use pipelining primarily for the same reason that sequential systems do. However, pipelines are a source of parallelism as well, in the same way that instruction pipelines are a source of parallelism in hardware design. It is possible to run operations _A_and _B_ simultaneously on different processors, so that _B_ consumes tuples in parallel with _A_ producing them. This form of parallelism is called **pipelined parallelism**.

Consider a join of four relations:

_r_~1~ ⨝ _r_~2~ ⨝ _r_~3~ ⨝ _r_~4~

We can set up a pipeline that allows the three joins to be computed in parallel. Suppose processor _P_~1~ is assigned the computation of _temp_~1~ ← _r_~1~ _⨝ r_~2~, and _P_~2~ is assigned the computation of _r_~3~  ⨝ temp~1~\. As _P_~1~ computes tuples in _r_~1~ _⨝ _r_~2~, it makes these tuples available to processor _P_~2~\. Thus, _P_~2~ has available to it some of the tuples in _r_~1~ _⨝ r_~2~ before _P_~1~ has finished its computation. _P_~2~ can use those tuples that are available to begin computation of _temp_~1~ _⨝ r_~3~, even before _r_~1~ _⨝ r_~2~ is fully computed by _P_~1~\. Likewise, as _P_~2~ computes tuples in (_r_~1~ _⨝ r_~2~) _⨝ r_~3~, it makes these tuples available to _P_~3~, which computes the join of these tuples with _r_~4~.

Pipelined parallelism is useful with a small number of processors, but does not scale up well. First, pipeline chains generally do not attain sufficient length to provide a high degree of parallelism. Second, it is not possible to pipeline rela- tional operators that do not produce output until all inputs have been accessed, such as the set-difference operation. Third, only marginal speedup is obtained for the frequent cases in which one operator’s execution cost is much higher than are those of the others.

All things considered, when the degree of parallelism is high, pipelining is a less important source of parallelism than partitioning. The real reason for using pipelining is that pipelined executions can avoid writing intermediate results to disk.  


### Independent Parallelism

Operations in a query expression that do not depend on one another can be executed in parallel. This form of parallelism is called **independent parallelism**.

Consider the join _r_~1~ _⨝ r_~2~ _⨝ r_~3~ _⨝ r_~4~\. Clearly, we can compute _temp_~1~ ← _r_~1~ _⨝ r_~2~ in parallel with temp~2~ ← _r_~3~_⨝ r_~4~\. When these two computations complete, we compute:

temp1 ⨝ temp2

To obtain further parallelism, we can pipeline the tuples in _temp_~1~ and _temp_~2~ into the computation of _temp_~1~ ⨝ _temp_~2~, which is itself carried out by a pipelined join (Section 12.7.2.2).

Like pipelined parallelism, independent parallelism does not provide a high degree of parallelism and is less useful in a highly parallel system, although it is useful with a lower degree of parallelism.

