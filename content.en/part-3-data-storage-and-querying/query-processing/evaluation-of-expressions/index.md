---
title: 'Evaluation of Expressions'
weight: 7
references:
    videos:
        - youtube:HBN-RL7W8uM
        - youtube:84BsI5VJPq4
    links:
        - https://www.geeksforgeeks.org/expression-evaluation/
        - https://www.javatpoint.com/evaluation-of-expressions
    books:
        - b1:
            title: Query Processing in Database Systems
            url: https://www.google.co.in/books/edition/Query_Processing_in_Database_Systems/AOGpCAAAQBAJ?hl=en&gbpv=0
        - b2:
            title: Query Processing in Data-warehousing Environments
            url: https://www.google.co.in/books/edition/Query_Processing_in_Data_warehousing_Env/_rJEAQAAIAAJ?hl=en&gbpv=0&bsq=query%20processing%20Evaluation%20of%20Expressions%20books
---

# Evaluation of Expressions

So far, we have studied how individual relational operations are carried out. Now we consider how to evaluate an expression containing multiple operations. The obvious way to evaluate an expression is simply to evaluate one operation at a time, in an appropriate order. The result of each evaluation is **materialized** in a temporary relation for subsequent use. A disadvantage to this approach is the need to construct the temporary relations, which (unless they are small) must be written to disk. An alternative approach is to evaluate several operations simultaneously in a **pipeline**, with the results of one operation passed on to the next, without the need to store a temporary relation.

In Sections 12.7.1 and 12.7.2, we consider both the _materialization_ approach and the _pipelining_ approach. We shall see that the costs of these approaches can differ substantially, but also that there are cases where only the materialization approach is feasible.

## Materialization

It is easiest to understand intuitively how to evaluate an expression by looking at a pictorial representation of the expression in an **operator tree**. Consider the expression:

_π ~name~_(σ _~building~_ \= ~“Watson”~(_department_)  ✶ _instructor_ )

in Figure 12.11. 
If we apply the materialization approach, we start from the lowest-level operations in the expression (at the bottom of the tree). In our example, there is only one such operation: the selection operation on _department_. The inputs to the lowest-level operations are relations in the database. We execute these operations by the algorithms that we studied earlier, and we store the results in temporary relations. We can use these temporary relations to execute the operations at the next level up in the tree, where the inputs now are either temporary relations or relations stored in the database. In our example, the inputs to the join are the _in- structor_ relation and the temporary relation created by the selection on _department_. The join can now be evaluated, creating another temporary relation.  

![Alt text](image-50.png)

By repeating the process, we will eventually evaluate the operation at the root of the tree, giving the final result of the expression. In our example, we get the final result by executing the projection operation at the root of the tree, using as input the temporary relation created by the join.

Evaluation as just described is called **materialized evaluation**, since the re- sults of each intermediate operation are created (materialized) and then are used for evaluation of the next-level operations.

The cost of a materialized evaluation is not simply the sum of the costs of the operations involved. When we computed the cost estimates of algorithms, we ignored the cost of writing the result of the operation to disk. To compute the cost of evaluating an expression as done here, we have to add the costs of all the operations, as well as the cost of writing the intermediate results to disk. We assume that the records of the result accumulate in a buffer, and, when the buffer is full, they are written to disk. The number of blocks written out, _br_ , can be estimated as _nr/ fr_ , where _nr_ is the estimated number of tuples in the result relation _r_ , and _fr_ is the _blocking factor_ of the result relation, that is, the number of records of _r_ that will fit in a block. In addition to the transfer time, some disk seeks may be required, since the disk head may have moved between successive writes. The number of seeks can be estimated as L_br/bb_⅃ where _bb_ is the size of the output buffer (measured in blocks).

**Double buffering** (using two buffers, with one continuing execution of the algorithm while the other is being written out) allows the algorithm to execute more quickly by performing CPU activity in parallel with I/O activity. The number of seeks can be reduced by allocating extra blocks to the output buffer, and writing out multiple blocks at once.

## Pipelining

We can improve query-evaluation efficiency by reducing the number of tem- porary files that are produced. We achieve this reduction by combining several relational operations into a _pipeline_ of operations, in which the results of one operation are passed along to the next operation in the pipeline. Evaluation as just described is called **pipelined evaluation**.

For example, consider the expression (_π_~a1~,a~2~(_r  s_)). If materialization were applied, evaluation would involve creating a temporary relation to hold the result of the join, and then reading back in the result to perform the projection. These operations can be combined: When the join operation generates a tuple of its result, it passes that tuple immediately to the project operation for processing. By combining the join and the projection, we avoid creating the intermediate result, and instead create the final result directly.

Creating a pipeline of operations can provide two benefits:

**1\.** It eliminates the cost of reading and writing temporary relations, reducing the cost of query evaluation.

**2\.** It can start generating query results quickly, if the root operator of a query- evaluation plan is combined in a pipeline with its inputs. This can be quite useful if the results are displayed to a user as they are generated, since otherwise there may be a long delay before the user sees any query results.

### Implementation of Pipelining

We can implement a pipeline by constructing a single, complex operation that combines the operations that constitute the pipeline. Although this approach may be feasible for some frequently occurring situations, it is desirable in general to reuse the code for individual operations in the construction of a pipeline.

In the example of Figure 12.11, all three operations can be placed in a pipeline, which passes the results of the selection to the join as they are generated. In turn, it passes the results of the join to the projection as they are generated. The memory requirements are low, since results of an operation are not stored for long. However, as a result of pipelining, the inputs to the operations are not available all at once for processing.

Pipelines can be executed in either of two ways:

**1\.** In a **demand-driven pipeline**, the system makes repeated requests for tuples from the operation at the top of the pipeline. Each time that an operation receives a request for tuples, it computes the next tuple (or tuples) to be returned, and then returns that tuple. If the inputs of the operation are not pipelined, the next tuple(s) to be returned can be computed from the input relations, while the system keeps track of what has been returned so far. If it has some pipelined inputs, the operation also makes requests for tuples from its pipelined inputs. Using the tuples received from its pipelined inputs, the operation computes tuples for its output, and passes them up to its parent.

**2\.** In a **producer-driven pipeline**, operations do not wait for requests to pro- duce tuples, but instead generate the tuples **eagerly**. Each operation in a producer-driven pipeline is modeled as a separate process or thread within the system that takes a stream of tuples from its pipelined inputs and gen- erates a stream of tuples for its output.

We describe below how demand-driven and producer-driven pipelines can be implemented.

Each operation in a demand-driven pipeline can be implemented as an **iterator** that provides the following functions: _open_(), _next_(), and _close_(). After a call to _open_(), each call to _next_() returns the next output tuple of the operation. The implementation of the operation in turn calls _open_() and _next_() on its inputs, to get its input tuples when required. The function _close_() tells an iterator that no more tuples are required. The iterator maintains the **state** of its execution in between calls, so that successive _next_() requests receive successive result tuples.

For example, for an iterator implementing the select operation using linear search, the _open_() operation starts a file scan, and the iterator’s state records the point to which the file has been scanned. When the _next_() function is called, the file scan continues from after the previous point; when the next tuple satisfying the selection is found by scanning the file, the tuple is returned after storing the point where it was found in the iterator state. A merge-join iterator’s _open_() operation would open its inputs, and if they are not already sorted, it would also sort the inputs. On calls to _next_(), it would return the next pair of matching tuples. The state information would consist of up to where each input had been scanned. Details of the implementation of iterators are left for you to complete in Practice Exercise 12.7.

Producer-driven pipelines, on the other hand, are implemented in a different manner. For each pair of adjacent operations in a producer-driven pipeline, the system creates a buffer to hold tuples being passed from one operation to the next. The processes or threads corresponding to different operations execute concurrently. Each operation at the bottom of a pipeline continually generates output tuples, and puts them in its output buffer, until the buffer is full. An operation at any other level of a pipeline generates output tuples when it gets input tuples from lower down in the pipeline, until its output buffer is full. Once the operation uses a tuple from a pipelined input, it removes the tuple from its input buffer. In either case, once the output buffer is full, the operation waits until its parent operation removes tuples from the buffer, so that the buffer has space for more tuples. At this point, the operation generates more tuples, until the buffer is full again. The operation repeats this process until all the output tuples have been generated.

It is necessary for the system to switch between operations only when an output buffer is full, or an input buffer is empty and more input tuples are needed to generate any more output tuples. In a parallel-processing system, operations in a pipeline may be run concurrently on distinct processors (see Chapter 18).

Using producer-driven pipelining can be thought of as **pushing** data up an operation tree from below, whereas using demand-driven pipelining can be thought of as **pulling** data up an operation tree from the top. Whereas tuples are generated _eagerly_ in producer-driven pipelining, they are generated **lazily**, on demand, in demand-driven pipelining. Demand-driven pipelining is used more commonly than producer-driven pipelining, because it is easier to imple- ment. However, producer-driven pipelining is very useful in parallel processing systems.

### Evaluation Algorithms for Pipelining

Some operations, such as sorting, are inherently **blocking operations**, that is, they may not be able to output any results until all tuples from their inputs have been examined.5

Other operations, such as join, are not inherently blocking, but specific eval- uation algorithms may be blocking. For example, the hash-join algorithm is a blocking operation, since it requires both its inputs to be fully retrieved and parti- tioned, before it outputs any tuples. On the other hand, the indexed nested loops join algorithm can output result tuples as it gets tuples for the outer relation. It is therefore said to be **pipelined** on its outer (left-hand side) relation, although it is blocking on its indexed (right-hand side) input, since the index must be fully constructed before the indexed nested-loop join algorithm can execute.

Hybrid hash join can be viewed as partially pipelined on the probe relation, since it can output tuples from the first partition as tuples are received for the probe relation. However, tuples that are not in the first partition will be output only after the entire pipelined input relation is received. Hybrid hash join thus provides pipelined evaluation on its probe input if the build input fits entirely in memory, or nearly pipelined evaluation if most of the build input fits in memory.

If both inputs are sorted on the join attribute, and the join condition is an equi-join, merge join can be used, with both its inputs pipelined.

However, in the more common case that the two inputs that we desire to pipeline into the join are not already sorted, another alternative is the **double- pipelined join** technique, shown in Figure 12.12. The algorithm assumes that the input tuples for both input relations, _r_ and _s_, are pipelined. Tuples made available for both relations are queued for processing in a single queue. Special queue entries, called _Endr_ and _Ends_ , which serve as end-of-file markers, are inserted in the queue after all tuples from _r_ and _s_ (respectively) have been generated. For efficient evaluation, appropriate indices should be built on the relations _r_ and _s_. As tuples are added to _r_ and _s_, the indices must be kept up to date. When hash indices are used on _r_ and _s_, the resultant algorithm is called the **double-pipelined hash-join** technique.

The double-pipelined join algorithm in Figure 12.12 assumes that both inputs fit in memory. In case the two inputs are larger than memory, it is still possible to use the double-pipelined join technique as usual until available memory is full. When available memory becomes full, _r_ and _s_ tuples that have arrived up to that point can be treated as being in partition _r_ 0 and _s_ 0, respectively. Tuples for _r_ and _s_ that arrive subsequently are assigned to partitions _r_ 1 and _s_ 1, respectively, which 

```
doner := false;
dones := false;
r := ∅;
s := ∅;
result := ∅;
while not doner or not dones do
    begin
        if queue is empty, then wait until queue is not empty;
        t := top entry in queue;
        if t = Endr then doner := true
            else if t = Ends then dones := true
                else if t is from input r
                    then
                        begin
                            r := r ∪ {t};
                            result := result ∪ ({t} ✶ s);
                        end
                    else /* t is from input s */
                        begin
                            s := s ∪ {t};
                            result := result ∪ (r ✶ {t});
                        end
    end

```
**Figure 12.12** Double-pipelined join algorithm.

are written to disk, and are not added to the in-memory index. However, tuples assigned to _r_1 and _s_1 are used to probe _s_0 and _r_0, respectively, before they are written to disk. Thus, the join of _r_1 with _s_0, and _s_0 with _r_1, is also carried out in a pipelined fashion. After _r_ and _s_ have been fully processed, the join of _r_1 tuples with _s_1 tuples must be carried out, to complete the join; any of the join techniques we have seen earlier can be used to join _r_1 with _s_1.

