---
title: Exercises
weight: 16
---

Exercises## Exercises

**7.14** Explain the distinctions among the terms primary key, candidate key, and superkey.

**7.15** Construct an E-R diagram for a hospital with a set of patients and a set of medical doctors. Associate with each patient a log of the various tests and examinations conducted.

**7.16** Construct appropriate relation schemas for each of the E-R diagrams in Practice Exercises 7.1 to 7.3.

**7.17** Extend the E-R diagram of Practice Exercise 7.3 to track the same informa- tion for all teams in a league.

**7.18** Explain the difference between a weak and a strong entity set.

**7.19** We can convert any weak entity set to a strong entity set by simply adding appropriate attributes. Why, then, do we have weak entity sets?

**7.20** Consider the E-R diagram in Figure 7.29, which models an online bookstore.

a. List the entity sets and their primary keys.

b. Suppose the bookstore adds Blu-ray discs and downloadable video to its collection. The same item may be present in one or both formats, with differing prices. Extend the E-R diagram to model this addition, ignoring the effect on shopping baskets.

c. Now extend the E-R diagram, using generalization, to model the case where a shopping basket may contain any combination of books, Blu-ray discs, or downloadable video.

**7.21** Design a database for an automobile company to provide to its dealers to assist them in maintaining customer records and dealer inventory and to assist sales staff in ordering cars.

Each vehicle is identified by a vehicle identification number (VIN). Each individual vehicle is a particular model of a particular brand offered by the company (e.g., the XF is a model of the car brand Jaguar of Tata Motors). Each model can be offered with a variety of options, but an individual car may have only some (or none) of the available options. The database needs to store information about models, brands, and options, as well as information about individual dealers, customers, and cars.

Your design should include an E-R diagram, a set of relational schemas, and a list of constraints, including primary-key and foreign-key constraints.

**7.22** Design a database for a world-wide package delivery company (e.g., DHL or FedEX). The database must be able to keep track of customers (who ship items) and customers (who receive items); some customers may do both.  

![Alt text](image-27.png)

Each package must be identifiable and trackable, so the database must be able to store the location of the package and its history of locations. Locations include trucks, planes, airports, and warehouses.

Your design should include an E-R diagram, a set of relational schemas, and a list of constraints, including primary-key and foreign-key constraints.

**7.23** Design a database for an airline. The database must keep track of cus- tomers and their reservations, flights and their status, seat assignments on individual flights, and the schedule and routing of future flights.

Your design should include an E-R diagram, a set of relational schemas, and a list of constraints, including primary-key and foreign-key constraints.

**7.24** In Section 7.7.3, we represented a ternary relationship (repeated in Fig- ure 7.27a) using binary relationships, as shown in Figure 7.27b. Consider the alternative shown in Figure 7.27c. Discuss the relative merits of these two alternative representations of a ternary relationship by binary relation- ships.  

**7.25** Consider the relation schemas shown in Section 7.6, which were generated from the E-R diagram in Figure 7.15. For each schema, specify what foreign- key constraints, if any, should be created.

**7.26** Design a generalization–specialization hierarchy for a motor vehicle sales company. The company sells motorcycles, passenger cars, vans, and buses. Justify your placement of attributes at each level of the hierarchy. Explain why they should not be placed at a higher or lower level.

**7.27** Explain the distinction between condition-defined and user-defined con- straints. Which of these constraints can the system check automatically? Explain your answer.

**7.28** Explain the distinction between disjoint and overlapping constraints.

**7.29** Explain the distinction between total and partial constraints.

## Exercises

**9.12** Write a servlet and associated HTML code for the following very simple application: A user is allowed to submit a form containing a value, say _n_, and should get a response containing _n_ “\*” symbols.

**9.13** Write a servlet and associated HTML code for the following simple appli- cation: A user is allowed to submit a form containing a number, say _n_, and should get a response saying how many times the value _n_ has been submitted previously. The number of times each value has been submitted previously should be stored in a database.

**9.14** Write a servlet that authenticates a user (based on user names and pass- words stored in a database relation), and sets a session variable called _userid_ after authentication.

**9.15** What is an SQL injection attack? Explain how it works, and what precau- tions must be taken to prevent SQL injection attacks.

**9.16** Write pseudocode to manage a connection pool. Your pseudocode must include a function to create a pool (providing a database connection string, database user name, and password as parameters), a function to request a connection from the pool, a connection to release a connection to the pool, and a function to close the connection pool.

**9.17** Explain the terms CRUD and REST.

**9.18** Many Web sites today provide rich user-interfaces using Ajax. List two features each of which reveals if a site uses Ajax, without having to look at the source code. Using the above features, find three sites which use Ajax; you can view the HTML source of the page to check if the site is actually using Ajax.

**9.19** XSS attacks:

a. What is an XSS attack?

b. How can the referer field be used to detect some XSS attacks?

**9.20** What is multi-factor authentication? How does it help safeguard against stolen passwords?

**9.21** Consider the Oracle **Virtual Private Database** (**VPD**) feature described in Section 9.7.5, and an application based on our university schema.

a. What predicate (using a subquery) should be generated to allow each faculty member to see only _takes_ tuples corresponding to course sections that they have taught?  

b. Give an SQL query such that the query with the predicate added gives a result that is a subset of the original query result without the added predicate.

c. Give an SQL query such that the query with the predicate added gives a result containing a tuple that is not in the result of the original query without the added predicate.

**9.22** What are two advantages of encrypting data stored in the database?

**9.23** Suppose you wish to create an audit trail of changes to the _takes_ relation.

a. Define triggers to create an audit trail, logging the information into a relation called, for example, _takes trail_. The logged information should include the user-id (assume a function _user id_() provides this informa- tion) and a timestamp, in addition to old and new values. You must also provide the schema of the _takes trail_ relation.

b. Can the above implementation guarantee that updates made by a malicious database administrator (or someone who manages to get the administrator’s password) will be in the audit trail? Explain your answer.

**9.24** Hackers may be able to fool you into believing that their Web site is actually a Web site (such as a bank or credit card Web site) that you trust. This may be done by misleading email, or even by breaking into the network infrastructure and rerouting network traffic destined for, say mybank.com, to the hacker’s site. If you enter your user name and password on the hacker’s site, the site can record it, and use it later to break into your account at the real site. When you use a URL such as https://mybank.com, the HTTPS protocol is used to prevent such attacks. Explain how the protocol might use digital certificates to verify authenticity of the site.

**9.25** Explain what is a challenge–response system for authentication. Why is it more secure than a traditional password-based system?

