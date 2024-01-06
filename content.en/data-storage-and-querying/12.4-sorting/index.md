---
title: 12.4 Sorting
weight: 28
---

12.4 Sorting## 12.4 Sorting

Sorting of data plays an important role in database systems for two reasons. First, SQL queries can specify that the output be sorted. Second, and equally important for query processing, several of the relational operations, such as joins, can be implemented efficiently if the input relations are first sorted. Thus, we discuss sorting here before discussing the join operation in Section 12.5.

We can sort a relation by building an index on the sort key, and then using that index to read the relation in sorted order. However, such a process orders the relation only _logically_, through an index, rather than _physically_. Hence, the reading of tuples in the sorted order may lead to a disk access (disk seek plus block transfer) for each record, which can be very expensive, since the number of records can be much larger than the number of blocks. For this reason, it may be desirable to order the records physically.

The problem of sorting has been studied extensively, both for relations that fit entirely in main memory and for relations that are bigger than memory. In the first case, standard sorting techniques such as quick-sort can be used. Here, we discuss how to handle the second case.

### 12.4.1 External Sort-Merge Algorithm

Sorting of relations that do not fit in memory is called **external sorting**. The most commonly used technique for external sorting is the **external sort–merge** algorithm. We describe the external sort–merge algorithm next. Let _M_ denote the number of blocks in the main-memory buffer available for sorting, that is, the number of disk blocks whose contents can be buffered in available main memory.

**1\.** In the first stage, a number of sorted **runs** are created; each run is sorted, but contains only some of the records of the relation.

_i_ \= 0; 
**repeat**
    read _M_ blocks of the relation, or the rest of the relation,
    whichever is smaller;
    sort the in-memory part of the relation; 
    write the sorted data to run file _Ri_ ; 
    _i_ \= _i_ \+ 1;
**until** the end of the relation

**2\.** In the second stage, the runs are _merged_. Suppose, for now, that the total number of runs, _N_, is less than _M_, so that we can allocate one block to each run and have space left to hold one block of output. The merge stage operates as follows:

read one block of each of the _N_ files _Ri_ into a buffer block in memory; 
**repeat**
choose the first tuple (in sort order) among all buffer blocks; 
write the tuple to the output, and delete it from the buffer block; 
**if** the buffer block of any run _Ri_ is empty **and not** end-of-file(Ri )
        **then** read the next block of _Ri_ into the buffer block; 
**until** all input buffer blocks are empty

The output of the merge stage is the sorted relation. The output file is buffered to reduce the number of disk write operations. The preceding merge operation is a generalization of the two-way merge used by the standard in-memory sort– merge algorithm; it merges _N_ runs, so it is called an _N_**\-way merge**.

![Alt text](image-47.png)

In general, if the relation is much larger than memory, there may be _M_ or more runs generated in the first stage, and it is not possible to allocate a block for each run during the merge stage. In this case, the merge operation proceeds in multiple passes. Since there is enough memory for _M_ − 1 input buffer blocks, each merge can take _M_ − 1 runs as input.

The initial _pass_ functions in this way: It merges the first _M_ − 1 runs (as desc- ribed in item 2 above) to get a single run for the next pass. Then, it merges the next _M_ − 1 runs similarly, and so on, until it has processed all the initial runs. At this point, the number of runs has been reduced by a factor of _M_ − 1. If this reduced number of runs is still greater than or equal to _M_, another pass is made, with the runs created by the first pass as input. Each pass reduces the number of runs by a factor of _M_ − 1. The passes repeat as many times as required, until the number of runs is less than _M_; a final pass then generates the sorted output.

Figure 12.4 illustrates the steps of the external sort–merge for an example relation. For illustration purposes, we assume that only one tuple fits in a block ( _fr_ \= 1), and we assume that memory holds at most three blocks. During the merge stage, two blocks are used for input and one for output.

### 12.4.2 Cost Analysis of External Sort-Merge

We compute the disk-access cost for the external sort–merge in this way: Let _br_ denote the number of blocks containing records of relation _r_ . The first stage reads every block of the relation and writes them out again, giving a total of 2_br_ block transfers. The initial number of runs is L_br/M_⅃. Since the number of runs decreases by a factor of _M_ − 1 in each merge pass, the total number of merge passes required is Llog_M_−1(_br/M_)⅃. Each of these passes reads every block of the relation once and writes it out once, with two exceptions. First, the final pass can produce the sorted output without writing its result to disk. Second, there may be runs that are not read in or written out during a pass—for example, if there are _M_ runs to be merged in a pass, _M_ − 1 are read in and merged, and one run is not accessed during the pass. Ignoring the (relatively small) savings due to the latter effect, the total number of block transfers for external sorting of the relation is:

_br_ (2Llog_M_−1(_br/M_)⅃ + 1)

Applying this equation to the example in Figure 12.4, we get a total of 12 ∗ (4 + 1) = 60 block transfers, as you can verify from the figure. Note that the above numbers do not include the cost of writing out the final result.

We also need to add the disk-seek costs. Run generation requires seeks for reading data for each of the runs as well as for writing the runs. During the merge phase, if data are read _bb_ blocks at a time from each run (that is, _bb_ buffer blocks are allocated to each run), then each merge pass would require around L_br /bb_⅃ seeks for reading data.4 Although the output is written sequentially, if it is on the same disk as the input runs the head may have moved away between writes of consecutive blocks. Thus we would have to add a total of 2L_br /bb_⅃ seeks for each merge pass, except the final pass (since we assume the final result is not written back to disk). The total number of seeks is then:

2L_br/M_⅃ + L_br/bb_⅃(2Llog_M_−1(_br/M_)⅃ − 1)

Applying this equation to the example in Figure 12.4, we get a total of 8 + 12 ∗ (2 ∗ 2 − 1) = 44 disk seeks if we set the number of buffer blocks per run, _bb_ to 1.

