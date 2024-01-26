---
title: Bibliographical Notes
weight: 3
---

Bibliographical Notes## Bibliographical Notes

Gray and Reuter \[1993\] provides detailed textbook coverage of transaction- processing concepts, including concurrency-control concepts and implementa- tion details. Bernstein and Newcomer \[1997\] provides textbook coverage of vari- ous aspects of transaction processing including concurrency control.

The two-phase locking protocol was introduced by Eswaran et al. \[1976\]. The tree-locking protocol is from Silberschatz and Kedem \[1980\]. Other non- two-phase locking protocols that operate on more general graphs are described in Yannakakis et al. \[1979\], Kedem and Silberschatz \[1983\], and Buckley and Silberschatz \[1985\]. Korth \[1983\] explores various lock modes that can be obtained from the basic shared and exclusive lock modes.

Practice Exercise 15.4 is from Buckley and Silberschatz \[1984\]. Practice Ex- ercise 15.6 is from Kedem and Silberschatz \[1983\]. Practice Exercise 15.7 is from Kedem and Silberschatz \[1979\]. Practice Exercise 15.8 is from Yannakakis et al. \[1979\]. Practice Exercise 15.10 is from Korth \[1983\].

The locking protocol for multiple-granularity data items is from Gray et al. \[1975\]. A detailed description is presented by Gray et al. \[1976\]. Kedem and Silber- schatz \[1983\] formalizes multiple-granularity locking for an arbitrary collection of lock modes (allowing for more semantics than simply read and write). This approach includes a class of lock modes called _update_ modes to deal with lock con- version. Carey \[1983\] extends the multiple-granularity idea to timestamp-based concurrency control. An extension of the protocol to ensure deadlock freedom is presented by Korth \[1982\].

The timestamp-based concurrency-control scheme is from Reed \[1983\]. A timestamp algorithm that does not require any rollback to ensure serializability is presented by Buckley and Silberschatz \[1983\]. The validation concurrency-control scheme is from Kung and Robinson \[1981\].

Multiversion timestamp order was introduced in Reed \[1983\]. A multiversion tree-locking algorithm appears in Silberschatz \[1982\].

Degree-two consistency was introduced in Gray et al. \[1975\]. The levels of con- sistency—or isolation—offered in SQL are explained and critiqued in Berenson et al. \[1995\]. Many commercial database systems use version-based approaches in combination with locking. PostgreSQL, Oracle, and SQL Server all support forms of the snapshot isolation protocol mentioned in Section 15.6.2. Details can be found in Chapters 27, 28, and 30, respectively.

It should be noted that on PostgreSQL (as of version 8.1.4) and Oracle (as of version 10g), setting the isolation level to serializable results in the use of snapshot isolation, which does not guarantee serializability. Fekete et al. \[2005\] describes how to ensure serializable executions under snapshot isolation, by rewriting certain transactions to introduce conflicts; these conflicts ensure that the transactions cannot run concurrently under snapshot isolation; Jorwekar et al. \[2007\] describes an approach, that given a set of (parametrized) transactions running under snapshot isolation, can check if the transactions are vulnerability to nonserializability,  


Concurrency in B+-trees was studied by Bayer and Schkolnick \[1977\] and Johnson and Shasha \[1993\]. The techniques presented in Section 15.10 are based on Kung and Lehman \[1980\] and Lehman and Yao \[1981\]. The technique of key- value locking used in ARIES provides for very high concurrency on B+-tree access and is described in Mohan \[1990a\] and Mohan and Narang \[1992\]. Ellis \[1987\] presents a concurrency-control technique for linear hashing.  

  

