---
title: Motivation
weight: 7
---

Motivation## Motivation

Before we address each of the topics in detail, we summarize the motivation for, and some important issues in dealing with, each of these types of data.

- **Temporal data**. Most database systems model the current state of the world, for instance, current customers, current students, and courses currently be- ing offered. In many applications, it is very important to store and retrieve information about past states. Historical information can be incorporated manually into a schema design. However, the task is greatly simplified by database support for temporal data, which we study in Section 25.2.

- **Spatial data**. Spatial data include **geographic data**, such as maps and asso- ciated information, and **computer-aided-design data**, such as integrated- circuit designs or building designs. Applications of spatial data initially stored data as files in a file system, as did early-generation business ap- plications. But as the complexity and volume of the data, and the number of users, have grown, ad hoc approaches to storing and retrieving data in a file system have proved insufficient for the needs of many applications that use spatial data.

Spatial-data applications require facilities offered by a database system— in particular, the ability to store and query large amounts of data efficiently. Some applications may also require other database features, such as atomic updates to parts of the stored data, durability, and concurrency control. In Section 25.3, we study the extensions needed in traditional database systems to support spatial data.

- **Multimedia data**. In Section 25.4, we study the features required in database systems that store multimedia data such as image, video, and audio data. The main distinguishing feature of video and audio data is that the display of the data requires retrieval at a steady, predetermined rate; hence, such data are called **continuous-media data**.

- **Mobile databases**. In Section 25.5, we study the database requirements of mo- bile computing systems, such as laptop and netbook computers and high-end cell phones that are connected to base stations via wireless digital commu- nication networks. Such computers may need to be able to operate while disconnected from the network, unlike the distributed database systems dis- cussed in Chapter 19. They also have limited storage capacity, and thus require special techniques for memory management.

