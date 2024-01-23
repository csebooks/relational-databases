---
title: Storage and File Structure
weight: 1
---

## Storage and File Structure

In preceding chapters, we have emphasized the higher-level models of a database. For example, at the _conceptual_ or _logical_ level, we viewed the database, in the re- lational model, as a collection of tables. Indeed, the logical model of the database is the correct level for database _users_ to focus on. This is because the goal of a database system is to simplify and facilitate access to data; users of the system should not be burdened unnecessarily with the physical details of the implemen- tation of the system.

In this chapter, however, as well as in Chapters 11, 12, and 13, we probe be- low the higher levels as we describe various methods for implementing the data models and languages presented in preceding chapters. We start with character- istics of the underlying storage media, such as disk and tape systems. We then define various data structures that allow fast access to data. We consider several alternative structures, each best suited to a different kind of access to data. The final choice of data structure needs to be made on the basis of the expected use of the system and of the physical characteristics of the specific machine.

