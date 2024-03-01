---
title: 'Transactions as SQL Statements'
weight: 10
references:
    videos:
        - youtube:20SXjcg6EIw
        - youtube:PhUgUTutiGk
    links:
        - https://www.geeksforgeeks.org/sql-transactions/
        - https://www.tutorialspoint.com/sql/sql-transactions.htm
    books:
        - b1:
            title: Databases and Transaction Processing
            url: https://www.google.co.in/books/edition/Databases_and_Transaction_Processing/o6ySAAAACAAJ?hl=en
        - b2:
            title: Learning SQL
            url: https://www.google.co.in/books/edition/Learning_SQL/FwKHDwAAQBAJ?hl=en&gbpv=0
---

#  Transactions as SQL Statements

In Section 4.3, we presented the SQL syntax for specifying the beginning and end of transactions. Now that we have seen some of the issues in ensuring the ACID properties for transactions, we are ready to consider how those properties are ensured when transactions are specified as a sequence of SQL statements rather than the restricted model of simple reads and writes that we considered up to this point.

In our simple model, we assumed a set of data items exists. While our simple model allowed data-item values to be changed, it did not allow data items to be created or deleted. In SQL, however, **insert** statements create new data and **delete** statements delete data. These two statements are, in effect, **write** opera- tions, since they change the database, but their interactions with the actions of other transactions are different from what we saw in our simple model. As an example, consider the following SQL query on our university database that finds all instructors who earn more than $90,000.

**select** _ID_, _name_ **from** _instructor_ **where** _salary >_ 90000;

Using our sample _instructor_ relation (Appendix A.3), we find that only Ein- stein and Brandt satisfy the condition. Now assume that around the same time we are running our query, another user inserts a new instructor named “James” whose salary is $100,000.

**insert into** _instructor_ **values** (’11111’, ’James’, ’Marketing’, 100000);

The result of our query will be different depending on whether this insert comes before or after our query is run. In a concurrent execution of these transactions, it is intuitively clear that they conflict, but this is a conflict not captured by our simple model. This situation is referred to as the **phantom phenomenon**, because a conflict may exist on “phantom” data.

Our simple model of transactions required that operations operate on a spe- cific data item given as an argument to the operation. In our simple model, we can look at the **read** and **write** steps to see which data items are referenced. But in an SQL statement, the specific data items (tuples) referenced may be determined by a **where** clause predicate. So the same transaction, if run more than once, might  


reference different data items each time it is run if the values in the database change between runs.

One way of dealing with the above problem is to recognize that it is not sufficient for concurrency control to consider only the tuples that are accessed by a transaction; the information used to find the tuples that are accessed by the transaction must also be considered for the purpose of concurrency control. The information used to find tuples could be updated by an insertion or deletion, or in the case of an index, even by an update to a search-key attribute. For example, if locking is used for concurrency control, the data structures that track the tuples in a relation, as well as index structures, must be appropriately locked. However, such locking can lead to poor concurrency in some situations; index-locking protocols which maximize concurrency, while ensuring serializability in spite of inserts, deletes, and predicates in queries, are discussed in Section 15.8.3.

Let us consider again the query:

**select** _ID_, _name_ **from** _instructor_ **where** _salary>_ 90000;

and the following SQL update:

**update** _instructor_ **set** _salary_ \= _salary_ \* 0.9 **where** _name_ \= ’Wu’;

We now face an interesting situation in determining whether our query conflicts with the update statement. If our query reads the entire _instructor_ relation, then it reads the tuple with Wu’s data and conflicts with the update. However, if an index were available that allowed our query direct access to those tuples with _salary >_ 90000, then our query would not have accessed Wu’s data at all because Wu’s salary is initially $90,000 in our example instructor relation, and reduces to $81,000 after the update.

However, using the above approach, it would appear that the existence of a conflict depends on a low-level query processing decision by the system that is unrelated to a user-level view of the meaning of the two SQL statements! An alternative approach to concurrency control treats an insert, delete or update as conflicting with a predicate on a relation, if it could affect the set of tuples selected by a predicate. In our example query above, the predicate is “_salary >_ 90000”, and an update of Wu’s salary from $90,000 to a value greater than $90,000, or an update of Einstein’s salary from a value greater that $90,000 to a value less than or equal to $90,000, would conflict with this predicate. Locking based on this idea is called **predicate locking**; however predicate locking is expensive, and not used in practice.