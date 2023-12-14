---
title: Bibliographical Notes
weight: 7
---

Bibliographical Notes## Bibliographical Notes

Hennessy et al.[2006\] provides an excellent introduction to the area of computer architecture. Abadi[2009\] provides an excellent introduction to cloud computing and the challenges of running database transactions in such an environment.

Gray and Reuter [1993\] provides a textbook description of transaction pro- cessing, including the architecture of client–server and distributed systems. The  bibliographical notes of Chapter 5 provide references to more information on ODBC, JDBC, and other database access APIs.

DeWitt and Gray [1992\] surveys parallel database systems, including their architecture and performance measures. A survey of parallel computer architec- tures is presented by Duncan [1990\]. Dubois and Thakkar [1992\] is a collection of papers on scalable shared-memory architectures. DEC clusters running Rdb were among the early commercial users of the shared-disk database architecture. Rdb is now owned by Oracle, and is called Oracle Rdb. The Teradata database machine was among the earliest commercial systems to use the shared-nothing database architecture. The Grace and the Gamma research prototypes also used shared-nothing architectures.

Ozsu and Valduriez [1999\] provides textbook coverage of distributed database systems. Further references pertaining to parallel and distributed database sys- tems appear in the bibliographical notes of Chapters 18 and 19, respectively.

Comer [2009\], Halsall [2006\], and Thomas [1996\] describe computer network- ing and the Internet. Tanenbaum [2002\], Kurose and Ross [2005\], and Peterson and Davie [2007\] provide general overviews of computer networks.   

# C H A P T E R 18 
# Parallel Databases

In this chapter, we discuss fundamental algorithms for parallel database systems that are based on the relational data model. In particular, we focus on the place- ment of data on multiple disks and the parallel evaluation of relational operations, both of which have been instrumental in the success of parallel databases.

