---
title: Review Terms
weight: 14
---

Review Terms## Review Terms

• Entity-relationship data model • Entity and entity set

    ◦ Attributes

    ◦ Domain

    ◦ Simple and composite attributes

    ◦ Single-valued and multivalued attributes

    ◦ Null value

    ◦ Derived attribute

    ◦ Superkey, candidate key, and primary key

• Relationship and relationship set

    ◦ Binary relationship set

    ◦ Degree of relationship set

    ◦ Descriptive attributes

    ◦ Superkey, candidate key, and primary key

    ◦ Role

    ◦ Recursive relationship set

• E-R diagram • Mapping cardinality:

    ◦ One-to-one relationship

    ◦ One-to-many relationship

    ◦ Many-to-one relationship

    ◦ Many-to-many relationship

• Participation

    ◦ Total participation

    ◦ Partial participation

• Weak entity sets and strong entity sets

    ◦ Discriminator attributes

    ◦ Identifying relationship

• Specialization and generalization

    ◦ Superclass and subclass

    ◦ Attribute inheritance

    ◦ Single and multiple inheritance

    ◦ Condition-defined and user- defined membership

    ◦ Disjoint and overlapping gener- alization

    ◦ Total and partial generalization

• Aggregation 

• UML

• UML class diagram

## Review Terms

• Application programs 
• Web interfaces to databases
• HyperText Markup Language (HTML)
• Hyperlinks
• Uniform resource locator (URL)
• Forms
• HyperText Transfer Protocol
(HTTP)
• Common Gateway Interface
(CGI)
• Connectionless protocols
• Cookie
• Session
• Servlets and Servlet sessions
• Server-side scripting
• JSP
• PHP
• ASP.NET
• Client-side scripting
• JavaScript
• Document Object Model (DOM)
• Applets
• Application architecture
• Presentation layer
• Model-view-controller (MVC)
architecture
• Business-logic layer
• Data-access layer
• Object-relational mapping
• Hibernate
• Web services
• RESTful services
• Rapid application development
• Web application frameworks
• Report generators
• Connection pooling
• Query result caching
• Application security
• SQL injection
• Cross-site scripting (XSS)
• Cross-site request forgery (XSRF)
• Authentication
• Two-factor authentication
• Man-in-the-middle attack
• Central authentication
• Single sign-on
• OpenID
• Virtual Private Database (VPD)
• Audit trail
• Encryption
• Symmetric-key encryption
• Public-key encryption
• Dictionary attack
• Challenge –response
• Digital signatures
• Digital certificates

**Practice Exercises**

**9.1** What is the main reason why servlets give better performance than pro- grams that use the common gateway interface (CGI), even though Java programs generally run slower than C or C++ programs?

**9.2** List some benefits and drawbacks of connectionless protocols over proto- cols that maintain connections.

**9.3** Consider a carelessly written Web application for an online-shopping site, which stores the price of each item as a hidden form variable in the Web page sent to the customer; when the customer submits the form, the information from the hidden form variable is used to compute the bill for the customer. What is the loophole in this scheme? (There was a real in- stance where the loophole was exploited by some customers of an online- shopping site, before the problem was detected and fixed.)

**9.4** Consider another carelessly written Web application, which uses a servlet that checks if there was an active session, but does not check if the user is authorized to access that page, instead depending on the fact that a link to the page is shown only to authorized users. What is the risk with this scheme? (There was a real instance where applicants to a college admissions site could, after logging into the Web site, exploit this loophole and view information they were not authorized to see; the unauthorized access was however detected, and those who accessed the information were punished by being denied admission.)

**9.5** List three ways in which caching can be used to speed up Web server performance.

**9.6** The netstat command (available on Linux and on Windows) shows the active network connections on a computer. Explain how this command can be used to find out if a particular Web page is not closing connections that it opened, or if connection pooling is used, not returning connections to the connection pool. You should account for the fact that with connection pooling, the connection may not get closed immediately.

**9.7** Testing for SQL-injection vulnerability:

a. Suggest an approach for testing an application to find if it is vulner- able to SQL injection attacks on text input.

b. Can SQL injection occur with other forms of input? If so, how would you test for vulnerability?

**9.8** A database relation may have the values of certain attributes encrypted for security. Why do database systems not support indexing on encrypted attributes? Using your answer to this question, explain why database sys- tems do not allow encryption of primary-key attributes.

**9.9** Exercise 9.8 addresses the problem of encryption of certain attributes. How- ever, some database systems support encryption of entire databases. Ex- plain how the problems raised in Exercise 9.8 are avoided when the entire database is encrypted.

**9.10** Suppose someone impersonates a company and gets a certificate from a certificate-issuing authority. What is the effect on things (such as purchase orders or programs) certified by the impersonated company, and on things certified by other companies?

**9.11** Perhaps the most important data items in any database system are the passwords that control access to the database. Suggest a scheme for the secure storage of passwords. Be sure that your scheme allows the system to test passwords supplied by users who are attempting to log into the system.

