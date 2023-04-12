
There has been many new paradigms of database technology, these new ideas outside of the classic [Relational Databases](Relational%20Databases.md) are collectively called Not Only SQL, better known as NoSQL. The key issue that NoSQL is trying to solve is scalability. Traditional [DB Replication](DB%20Replication.md) and [DB Partitions](DB%20Partitions.md) are complex and lead to issues sometimes. NoSQL databases are designed with maximum horizontal scalability in mind.

## Scalability

NoSQL sacrifices [ACID](ACID.md) guarantees for the sake of better scalability. The key concept is Eventual [Consistency](../Distributed%20Systems/Consistency%20and%20Consensus.md), meaning that we are will to have temporarily inconsistent nodes in favor of maximum scalability. In SQL [Partitions](DB%20Partitions.md) the data might be split among many different nodes and may require many joins to service a request. In NoSQL, the data is already self-contained and can easily be partitioned to different machines without as much concern for how to access the data.

## NoSQL Paradigms

#### [Document Databases](Document%20Databases.md)
JSON-like document style storage, actually saved as [BSON](../Internet%20&%20Networking/Serialization.md) encoding that allows binary types. As there is no set schema, this is much more flexible and scalable than standard relational databases.
Example: **MongoDB**

#### Wide Column DB
Column storage database. These databases are designed for huge amounts of data, with particularly heavy write loads.
Example: **Cassandra**

#### Graph DB
Storing data as a [Graph](../Data%20Structures%20&%20Algorithms/Data%20Structures/Graphs.md). This database is built with object relationships in mind as the key feature. A social media site would have a huge web of followers and friends, and could be more optimally represented by a graph database.
Example: **Neo4J**

#### Key-Value DB
Storing data in key-value pairs, like a non-volatile [Hash Map](../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md). The benefit is that this type of DB is extremely fast, and many times are stored entirely in [Memory & Cache](../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) and used as a [Database Cache](Database%20Cache.md).
Example: **Redis**