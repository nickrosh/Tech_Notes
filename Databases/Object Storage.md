
*Object Storage* is a data storage system optimized for large files that manages data as objects, as opposed to file systems which manage data as a file hierarchy. Each object typically includes the data itself, its metadata, and a globally unique identifier. All data is stored structurally in a flat environment, and is callable by the globally unique identifiers, similar to a [Hash Map](../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md). Users can access objects using [HTTP](../Internet%20&%20Networking/HTTP.md) to query object metadata. Since the objects are stored in a global storage pool, retrieval is fast. And the flat storage structure allows for easy scalability.

![](../Attachments/Pasted%20image%2020230401152838.png)

Object Storage is based on BLOB (*Binary Large Object*) storage, and is optimized for large files like images and videos. Data can only be created and deleted, you cannot update objects. Also, due to the unique global identifier, there cannot be duplicates.