---
title: 'Advanced Aggregation Features'
weight: 5
references:
    videos:
        - youtube:nNrgRVIzeHg
        - youtube:nNrgRVIzeHg
    links:
        - https://mode.com/sql-tutorial/sql-aggregate-functions
        - https://www.sqlcourse.com/advanced-course/aggregate-functions/
    books:
        - b1:
            title: Mastering Oracle SQL 
            url: https://www.google.co.in/books/edition/Mastering_Oracle_SQL/Rl2_4LhDjZkC?hl=en&gbpv=1&dq=advanced+aggregation+features+in+sql&printsec=frontcover
        - b2:
            title: Oracle SLQ by Example 
            url: https://www.google.co.in/books/edition/Oracle_SQL_By_Example/8Kk05fYPs7AC?hl=en&gbpv=1&dq=advanced+aggregation+features+in+sql&pg=PT352&printsec=frontcover
---

# Advanced Aggregation Features

The aggregation support in SQL, which we have seen earlier, is quite powerful, and handles most common tasks with ease. However, there are some tasks that are hard to implement efficiently with the basic aggregation features. In this section, we study features that were added to SQL to handle some such tasks.

## Ranking

Finding the position of a value in a larger set is a common operation. For instance, we may wish to assign students a rank in class based on their grade-point average (GPA), with the rank 1 going to the student with the highest GPA, the rank 2 to the student with the next highest GPA, and so on. A related type of query is to find the percentile in which a value in a (multi)set belongs, for example, the bottom third, middle third, or top third. While such queries can be expressed using the SQL constructs we have seen so far, they are difficult to express and inefficient to evaluate. Programmers may resort to writing the query partly in SQL and partly in a programming language. We study SQL support for direct expression of these types of queries here.

In our university example, the _takes_ relation shows the grade each student earned in each course taken. To illustrate ranking, let us assume we have a view _student grades_ (_ID, GPA_) giving the grade-point average of each student.8

Ranking is done with an **order by** specification. The following query gives the rank of each student:

**select** _ID_, **rank**() **over** (**order by** (_GPA_) **desc**) **as** _s rank_ 
**from** _student grades_;

Note that the order of tuples in the output is not defined, so they may not be sorted by rank. An extra **order by** clause is needed to get them in sorted order, as shown below.

**select** _ID_, **rank** () **over** (**order by** (_GPA_) **desc**) **as** _s rank_ 
**from** _student grades_ 
**order by** _s rank_;

A basic issue with ranking is how to deal with the case of multiple tuples that are the same on the ordering attribute(s). In our example, this means deciding what to do if there are two students with the same GPA. The **rank** function gives the same rank to all tuples that are equal on the **order by** attributes. For instance, if the highest GPA is shared by two students, both would get rank 1. The next rank given would be 3, not 2, so if three students get the next highest GPA, they would all get rank 3, and the next student(s) would get rank 6, and so on. There is also a **dense rank** function that does not create gaps in the ordering. In the above example, the tuples with the second highest value all get rank 2, and tuples with the third highest value get rank 3, and so on.

It is possible to express the above query with the basic SQL aggregation functions, using the following query:

**select** _ID_, (1 + (**select count**(\*) **from** _student grades B_ **where** _B_._GPA_ \> _A_._GPA_)) **as** _s rank_
**from** _student grades A_ 
**order by** _s rank_;

It should be clear that the rank of a student is merely 1 plus the number of students with a higher _GPA_, which is exactly what the above query specifies. However, this computation of each student’s rank takes time linear in the size of the relation, leading to an overall time quadratic in the size of the relation. On large relations, the above query could take a very long time to execute. In contrast, the system’s implementation of the **rank** clause can sort the relation and compute the rank in much less time.

Ranking can be done within partitions of the data. For instance, suppose we wish to rank students by department rather than across the entire university. Assume that a view is defined like _student grades_ but including the department name: _dept grades_(_ID, dept name, GPA_). The following query then gives the rank of students within each section: 

**select** _ID_, _dept name_, 
**rank** () **over** (**partition by** _dept name_ **order by** _GPA_ **desc**) **as** _dept rank_
**from** _dept grades_ 
**order by** _dept name_, _dept rank_;

The outer **order by** clause orders the result tuples by department name, and within each department by the rank.

Multiple **rank** expressions can be used within a single **select** statement; thus we can obtain the overall rank and the rank within the department by using two **rank** expressions in the same **select** clause. When ranking (possibly with partitioning) occurs along with a **group by** clause, the **group by** clause is applied first, and partitioning and ranking are done on the results of the group by. Thus aggregate values can then be used for ranking. We could have written our ranking over the _student grades_ view without using the view, using a single **select** clause. We leave details as an exercise for you.

The ranking functions can be used to find the top _n_ tuples by embedding a ranking query within an outer-level query; we leave details as an exercise. Note that the bottom _n_ is simply the same as the top _n_ with a reverse sorting order. Several database systems provide nonstandard SQL extensions to specify directly that only the top _n_ results are required; such extensions do not require the rank function and simplify the job of the optimizer. For example, some databases allow a clause **limit** _n_ to be added at the end of an SQL query to specify that only the first _n_ tuples should be output; this clause is used in conjunction with an **order by** clause to fetch the top _n_ tuples, as illustrated by the following query, which retrieves the IDs and GPAs of the top 10 students in order of GPA:

**select** _ID_, _GPA_) 
**from** _student grades_ 
**order by** _GPA_ 
**limit** 10;

However, the **limit** clause does not support partitioning, so we cannot get the top _n_ within each partition without performing ranking; further, if more than one student gets the same GPA, it is possible that one is included in the top 10, while another is excluded.

Several other functions can be used in place of **rank**. For instance, **per- cent rank** of a tuple gives the rank of the tuple as a fraction. If there are _n_ tuples in the partition9 and the rank of the tuple is _r_ , then its percent rank is defined as (_r_ − 1)_/_(_n_ − 1) (and as _null_ if there is only one tuple in the partition). The function **cume dist**, short for cumulative distribution, for a tuple is defined as _p/n_ where _p_ is the number of tuples in the partition with ordering values preceding or equal to the ordering value of the tuple and _n_ is the number of tuples in the partition. The function **row number** sorts the rows and gives each row a unique number corresponding to its position in the sort order; different rows with the same ordering value would get different row numbers, in a nondeterministic fashion.

Finally, for a given constant _n_, the ranking function **ntile**(_n_) takes the tuples in each partition in the specified order and divides them into _n_ buckets with equal numbers of tuples.10 For each tuple, **ntile**(_n_) then gives the number of the bucket in which it is placed, with bucket numbers starting with 1. This function is particularly useful for constructing histograms based on percentiles. We can show the quartile into which each student falls based on GPA by the following query:

**select** _ID_, **ntile**(4) **over** (**order by** (_GPA_ **desc**)) **as** _quartile_ **from** _student grades_;

The presence of null values can complicate the definition of rank, since it is not clear where they should occur first in the sort order. SQL permits the user to specify where they should occur by using **nulls first** or **nulls last**, for instance:

**select** _ID_, **rank** () **over** (**order by** _GPA_ **desc nulls last**) **as** _s rank_ **from** _student grades_;

## Windowing

Window queries compute an aggregate function over ranges of tuples. This is useful, for example, to compute an aggregate of a fixed range of time; the time range is called a _window_. Windows may overlap, in which case a tuple may contribute to more than one window. This is unlike the partitions we saw earlier, where a tuple could contribute to only one partition.

An example of the use of windowing is trend analysis. Consider our earlier sales example. Sales may fluctuate widely from day to day based on factors like weather (for example a snowstorm, flood, hurricane, or earthquake might reduce sales for a period of time). However, over a sufficiently long period of time, fluctuations might be less (continuing the example, sales may “make up” for weather-related downturns). Stock market trend analysis is another example of the use of the windowing concept. Various “moving averages” are found on business and investment Web sites.

It is relatively easy to write an SQL query using those features we have already studied to compute an aggregate over one window, for example, sales over a fixed 3-day period. However, if we want to do this for _every_ 3-day period, the query becomes cumbersome.

SQL provides a windowing feature to support such queries. Suppose we are given a view _tot credits_ (_year, num credits_) giving the total number of credits take by students in each year.11 Note that this relation can contain at most one tuple for each year. Consider the following query:

**select** _year_, **avg**(_num credits_) 
**over** (**order by** _year_ **rows** 3 **preceding**) **as** _avg total credits_
**from** _tot credits_;

This query computes averages over the 3 _preceding_ tuples in the specified sort order. Thus, for 2009, if tuples for years 2008 and 2007 are present in the relation _tot credits_, with each year represented by only one tuple, the result of the window definition is the average of the values for years 2007, 2008, and 2009. The averages each year would be computed in a similar manner. For the earliest year in the relation _tot credits_, the average would be over only that year itself, while for the next year, the average would be over two years. Note that if the relation _tot credits_ has more than one tuple for a specific year, there may be multiple possible

orderings of tuples, that are sorted by year. In this case, the definition of preceding tuples is based on the implementation dependent sort order, and is not uniquely defined.

Suppose that instead of going back a fixed number of tuples, we want the window to consist of all prior years. That means the number of prior years considered is not fixed. To get the average total credits over all prior years we write:

**select** _year_, **avg**(_num credits_) 
**over** (**order by** _year_ **rows unbounded preceding**) 
**as** _avg total credits_
**from** _tot credits_;

It is possible to use the keyword **following** in place of **preceding**. If we did this in our example the _year_ value specifies the beginning of the window instead of the end. Similarly, we can specify a window beginning before the current tuple and ending after it:

**select** _year_, **avg**(_num credits_) 
**over** (**order by** _year_ **rows between** 3 **preceding and** 2 **following**) 
**as** _avg total credits_
**from** _tot credits_;

Instead of a specific count of tuples, we can specify a range based on the value of the **order by** attribute. To specify a range going back 4 years and including the current year, we write:

We leave the definition of this view in terms of our university example as an exercise.  

**select** _year_, **avg**(_num credits_) 
**over** (**order by** _year_ **range between** _year_ \- 4 **and** _year_) 
**as** _avg total credits_
**from** _tot credits_;

Be sure to note the use of the keyword **range** in the above example. For the year 2010, data for years 2006 to 2010 inclusive would be included regardless of how many tuples actually exist for that range.

In our example, all tuples pertain to the entire university. Suppose instead, we have credit data for each department in a view _tot credits dept_ (_dept name, year, num credits_) giving the total number of credits students took with the particular department in the specified year. (Again, we leave writing this view definition as an exercise.) We can write windowing queries that treat each department separately by partitioning by _dept name_:

**select** _dept name, year_, **avg**(_num credits_) 
**over** (**partition by** _dept name_**order by** _year_ **rows between** 3 **preceding and current row**) 
**as** _avg total credits_
**from** _tot credits dept_;
