

Document Style Databases are a NoSQL paradigm where you store documents ([JSON](../Internet%20&%20Networking/Serialization.md)-like objects) with no fixed schema like a [Relational Databases](Relational%20Databases.md). For example, in the most famous document store, **MongoDB**, data is stored as BSON format, binary JSON.

Document DBs do not have any set schema, and thus can store data in any format, however this presents problems when trying to query for data that is not in previous data. This however, prevents the need for database migrations and DDL commands. Because of its distributed nature without tables, Document DBs are very easy to partition, and thus can be easily scaled to many nodes.

![](../Attachments/Pasted%20image%2020220415191252.png)


### Comparison of Terms

|SQL | MongoDB |
|--- | --- |
|Database | Database|
| Table | Collection|
|Row | Document|
|Index | Index|
|Join | $Lookup |
|Foreign Key | Reference|

