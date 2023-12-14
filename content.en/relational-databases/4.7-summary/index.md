---
title: 4.7 Summary
weight: 32
---

4.7 Summary## 4.7 Summary

• SQL supports several types of joins including inner and outer joins and several types of join conditions.

• View relations can be defined as relations containing the result of queries. Views are useful for hiding unneeded information, and for collecting together information from more than one relation into a single view.  

• Transactions are a sequence of queries and updates that together carry out a task. Transactions can be committed, or rolled back; when a transaction is rolled back, the effects of all updates performed by the transaction are undone.

• Integrity constraints ensure that changes made to the database by authorized users do not result in a loss of data consistency.

• Referential-integrity constraints ensure that a value that appears in one relation for a given set of attributes also appears for a certain set of attributes in another relation.

• Domain constraints specify the set of possible values that may be associated with an attribute. Such constraints may also prohibit the use of null values for particular attributes.

• Assertions are declarative expressions that state predicates that we require always to be true.

• The SQL data-definition language provides support for defining built-in domain types such as **date** and **time**, as well as user-defined domain types.

• SQL authorization mechanisms allow one to differentiate among the users of the database as far as the type of access they are permitted on various data values in the database.

• A user who has been granted some form of authority may be allowed to pass on this authority to other users. However, we must be careful about how authorization can be passed among users if we are to ensure that such authorization can be revoked at some future time.

• Roles help to assign a set of privileges to a user according to the role that the user plays in the organization.

**Review Terms**

• Join types

◦ Inner and outer join

◦ Left, right and full outer join

◦ Natural, using, and on

• View definition 
• Materialized views 
• View update 
• Transactions

◦ Commit work

◦ Rollback work

◦ Atomic transaction

• Integrity constraints 
• Domain constraints 
• Unique constraint 
• Check clause 
• Referential integrity

◦ Cascading deletes

◦ Cascading updates  

**152 Chapter 4 Intermediate SQL**

• Assertions 
• Date and time types 
• Default values 
• Indices 
• Large objects 
• User-defined types 
• Domains 
• Catalogs 
• Schemas 
• Authorization 
• Privileges

◦ **select**

◦ **insert**

◦ **update**

◦ **all privileges**

◦ Granting of privileges

◦ Revoking of privileges

◦ Privilege to grant privileges

◦ Grant option

• Roles 
• Authorization on views
• Execute authorization 
• Invoker privileges 
• Row-level authorization

