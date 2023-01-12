
## Hash Indexes

The simplest way to store data is with [Hash Maps](../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md). These [Key-Value Stores](NoSQL.md) simply map values to hashable indexes. Let's say our data storage consists only of appending to a file, let's call this the log. Whenever you need to find an object, simply look for its index. A simplistic system like this is well suited for in-memory storage that is updated frequently with not too many distinct keys

If we keep just appending to the log, how do we avoid eventually running out of disk space? A good solution is to break the log into segments of a certain size by closing a segment file when it reaches a certain size, and making subsequent writes to a new segment file. We can then perform *compaction* on these segments, where we throw away duplicate keys in the log, and keep only the most recent update for each key (Last Write Wins). As we compact the segments and they become smaller, we can also *merge* the files together. Segments are never modified after they have been written, so the merged segment is written to a new file.

![](../Attachments/Pasted%20image%2020221227170759.png)

An append-only log seems wasteful but it has its benefits:
- Appending and segment merging are sequential writes, which are much faster than random writes, especially on Hard Drives, and SSDs to a lesser extent
- Concurrency and crash recovery are much simpler if segment files are append-only or immutable. For example, you don't have to worry about the case where a crash happened while a value was being overwritten, leaving with a file containing part of the old and part of the new value spliced together.
- Merging old segments avoids the problem of data files getting fragmented over time.

However there are also limitations:
- The Hash Table must fit in memory, so if you have a very large number of keys, you're out of luck. In principle, you could maintain a hash map on disk, but unfortunately it is difficult to make an on-disk hash map perform well.
- Range queries are not efficient, you can't easily scan over all keys between xyz000 - xyz999.


## SSTables and LSM-Trees

We make a small change to the format of our segment files: we require that the sequence of key-value pairs is *sorted by key*. This format is called *Stored String Tables* or *SSTables* for short. SSTables have several key advantages over log segments with hash indexes

1. Merging segments is simple and efficient, even if the files are bigger than the available memory. The approach is like the one used in the [Merge Sort](../Data%20Structures%20&%20Algorithms/Algorithms/Merge%20Sort.md) algorithm. You start looking at the input files, look at the first key in each file, copy the lowest key (according to the sort order) to the output file, and repeat. This produces a new merged segment file, also sorted by key. As each segment contains all the values written to the DB during some period of time , this means that all the values in one segment must be more recent than all the values in another segment. Thus, we don't have to worry about keys showing up in multiple segments.
2. In order to find a particular key in the file, you no longer need to keep an index of all the keys in memory. As everything is sorted, you only need a few sparse keys to point you in the right direction, and then you just need to scan a few kilobytes.
3. Since read requests need to scan over several key-value pairs in the requested range anyway, it is possible to group those records into a block and compress it before writing it to disk. This saves disk space and I/O bandwidth use.

How to actually get the data sorted by key in the first place? We maintain a balanced [Tree](../Data%20Structures%20&%20Algorithms/Data%20Structures/Trees.md) (like Red-Black) in memory that allow us to sort our data. Now our storage engine works like the following:
- When a write comes in, add it to an in-memory balanced tree data structure. This is sometimes called a *memtable*.
- When the memtable gets bigger than some threshold- typically a few megabytes- write it our to disk as an SSTable file. This can be done efficiently because the tree already maintains the key-value pairs sorted by key. The new SSTable is being written out to disk, writes can continue to a new memtable instance.
- In order to serve a read request, first try to find it in the memtable, then in the most recent segment on-disk, then look through older segments.
- From time to time, run a merge and compaction process in the background to combine segment files and to discard overwritten or deleted values

This system is also known as a *Log-Structured Merge-Tree* or LSM-Tree, it is used by many databases such as Cassandra, Lucene.


## B-Trees

The most widely used structure however, is the *B-Tree*. It is used in almost every [Relational Databases](Relational%20Databases.md) and many [NoSQL](NoSQL.md)  databases as well.

Like SSTables, B-Trees keep key-value pairs sorted by key, which allows efficient key value lookups and range queries. The log-structured systems we looked at earlier break down into megabyte sized segments, and write sequentially. B-Trees break the database down into fixed-size *blocks* or *pages*, traditionally 4KB in size (sometimes bigger), and read or write one page at a time. This design aligns more closely to the actual hardware as disk are also arranged in fixed-size blocks. 

![](../Attachments/Pasted%20image%2020221227174600.png)

One page is designated the *root* of the tree and contains several keys and references to child pages. Each child is responsible for a continuous range of keys, and the keys between references indicate where the boundaries between those ranges lie. Eventually we get down to a page containing individual keys (a *leaf* page) which either contains the values for each key inline or contains references to the pages where the values can be found.

If you want to update a value, you simply search for the leaf page containing that key, change the value in that page, and write the page back to disk. If you want to add a new key, you need to find that page whose range encompasses the new key and add it to that page. If there isn't enough free space in the page to accommodate the new key, it is split into two half-full pages, and the parent page is updated to account for  the new subdivision of key ranges. This ensures that the tree remains *balanced*: a B-Tree with n keys always has a depth of $O(logn)$ .

In order to make the database resilient to crashes, it is common for B-tree implementations to include an additional data structure on disk: a *write-ahead log* (WAL, also known as a redo log). This is an append-only file to which every B-Tree modification must be written before it can be applied to the pages of the tree itself. When the database comes back after a crash, the WAL is used to restore it to a consistent state.


## Comparing B-Trees and LSM-Trees

As a rule of thumb, B-Trees tend to be faster for reads, and LSM-Trees are faster for writes. Reads are typically slower on LSM-Trees because they have to check several different data structures and SSTables at different stages of compaction.

#### LSM-Tree Advantages

A B-Tree must write every piece of data at least twice: once to the write-ahead log, and once to the tree page itself (and perhaps again as pages are split). There is also overhead from having to write an entire page at a time, even if only a few bytes in that page changed. Log-structured indexes also rewrite data multiple times due to repeated compaction and merging of SSTables. This is of special concern to SSDs, which have a limited write lifetime before the charge trap memory degrades.

Additionally, LSM-Trees can be compress better, and thus often produce smaller files on disk than B-Trees. B-Tree storage engines leave some disk space unused due to fragmentation: when a page is split or when a row cannot fit into an existing page, some space in a page remains unused. Since LSM-Trees are not page-oriented and periodically rewrite SSTables to remove fragmentation, they have lower storage overheads, especially when using leveled compaction.

#### LSM-Tree Disadvantages

The compaction-merge process can sometimes interfere with the performance of ongoing reads and writes. Even though storage engines try to perform compaction incrementally and without affecting concurrent access, disks have limited resources, so it can easily happen that a request needs to wait while the disk finishes an expensive compaction operation. Another issue with compaction arises at high write throughput: the disk's finite write bandwidth needs to be shared between the initial write (logging and flushing a memtable to disk) and the compaction threads running in the background. When writing to an empty database, the full disk bandwidth can be used for the initial write, but the bigger the database gets, the more disk bandwidth is required for compaction.

An advantage of B-Trees is that each key exists in exactly one place in the index, whereas a log-structured storage engine may have multiple copies of the same key in different segments. This aspect makes B-trees attractive in databases that want to offer strong [transactional](Transactions.md) semantics.