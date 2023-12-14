---
title: 9.9 Summary
weight: 38
---

9.9 Summary## 9.9 Summary

• Application programs that use databases as back ends and interact with users have been around since the 1960s. Application architectures have evolved over this period. Today most applications use Web browsers as their front end, and a database as their back end, with an application server in between.

• HTML provides the ability to define interfaces that combine hyperlinks with forms facilities. Web browsers communicate with Web servers by the HTTP protocol. Web servers can pass on requests to application programs, and return the results to the browser.

• Web servers execute application programs to implement desired function- ality. Servlets are a widely used mechanism to write application programs that run as part of the Web server process, in order to reduce overhead. There are also many server-side scripting languages that are interpreted by the Web server and provide application-program functionality as part of the Web server.

• There are several client-side scripting languages—JavaScript is the most widely used—that provide richer user interaction at the browser end.

• Complex applications usually have a multilayer architecture, including a model implementing business logic, a controller, and a view mechanism to display results. They may also include a data access layer that implements an object-relational mapping. Many applications implement and use Web services, allowing functions to be invoked over HTTP.

• A number of tools have been developed for rapid application development, and in particular to reduce the effort required to build user interfaces.

• Techniques such as caching of various forms, including query result caching and connection pooling, and parallel processing are used to improve appli- cation performance.

• Application developers must pay careful attention to security, to prevent attacks such as SQL injection attacks and cross-site scripting attacks.

• SQL authorization mechanisms are coarse grained and of limited value to ap- plications that deal with large numbers of users. Today application programs implement fine-grained, tuple-level authorization, dealing with a large num- ber of application users, completely outside the database system. Database extensions to provide tuple-level access control and to deal with large num- bers of application users have been developed, but are not standard as yet.

• Protecting the privacy of data is an important task for database applications. Many countries have legal requirements on protection of certain kinds of data, such as credit-card information or medical data.

• Encryption plays a key role in protecting information and in authentication of users and Web sites. Symmetric-key encryption and public-key encryption are two contrasting but widely used approaches to encryption. Encryption of certain sensitive data stored in databases is a legal requirement in many countries and states.

• Encryption also plays a key role in authentication of users to applications, of Web sites to users, and for digital signatures.

