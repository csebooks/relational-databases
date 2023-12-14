---
title: 'TRANSACTION MANAGEMENT'
weight: 9
---


**PART 4**

# TRANSACTION MANAGEMENT

The term _transaction_ refers to a collection of operations that form a single logical unit of work. For instance, transfer of money from one account to another is a transaction consisting of two updates, one to each account.

It is important that either all actions of a transaction be executed completely, or, in case of some failure, partial effects of each incomplete transaction be un- done. This property is called _atomicity_. Further, once a transaction is successfully executed, its effects must persist in the database—a system failure should not result in the database forgetting about a transaction that successfully completed. This property is called _durability_.

In a database system where multiple transactions are executing concurrently, if updates to shared data are not controlled there is potential for transactions to see inconsistent intermediate states created by updates of other transactions. Such a situation can result in erroneous updates to data stored in the database. Thus, database systems must provide mechanisms to isolate transactions from the effects of other concurrently executing transactions. This property is called _isolation_.

Chapter 14 describes the concept of a transaction in detail, including the properties of atomicity, durability, isolation, and other properties provided by the transaction abstraction. In particular, the chapter makes precise the notion of isolation by means of a concept called serializability.

Chapter 15 describes several concurrency-control techniques that help im- plement the isolation property. Chapter 16 describes the recovery management component of a database, which implements the atomicity and durability prop- erties.

Taken as a whole, the transaction-management component of a database sys- tem allows application developers to focus on the implementation of individual transactions, ignoring the issues of concurrency and fault tolerance.

