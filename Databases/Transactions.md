Simply put, a transaction is a way for an application to group several reads and writes together in a logical unit. Conceptually, all of the reads and writes in a transaction are executed as one operation: either the entire transaction succeeds (*commit*) or it fails (*abort, rollback*). If it fails, the application can safely retry. This system is much safer as we will never have partial failures, cases where some operations are successful and others fail.

The safety guarantees provided by transactions are often described by the well known acronym [ACID](ACID.md), which stands for *Atomicity, Consistency, Isolation, and Durability*. If we only used a single node database, we could easily ensure ACID, the difficulty comes with concurrency and distributed systems, where we have issues with the usual ACID guarantees, namely *Isolation*.


## Weak Isolation Levels

If two transactions don't touch the same data, they can safely be run in parallel, because neither depends on the other. Concurrency issues (race conditions) only come into play when one transaction reads data that is concurrently modified by another transaction, or when two transactions try to simultaneously modify the same data

*Serializable Isolation* means the DB guarantees that transactions have the same effect as if they ran *serially*. In practice, it is not that simple as this system has a significant performance cost, and most databases don't want to pay the price. Thus, most databases use Weak Isolation levels which only protect against some concurrency bugs.

#### Read Committed

The most basic level of transaction isolation is *read committed*. It makes two guarantees:
1. When reading from a database, you will only see data that has been committed (no dirty reads)
2. When writing to the database, you will only overwrite data that has been committed (no dirty writes)

**No Dirty Reads**: Imagine a transaction has written some data to the database, but the transaction has not yet committed or aborted. If another transaction can you that uncommitted data, it is a *dirty read*.

**No Dirty Writes**: What if two transactions concurrently try to update the same object in a database. We don't know the correct order the writes will happens, but we normally assume that the later write overwrites the earlier write. Now if the earlier write is part of a transaction that has not yet committed, and it was overwritten, then it is a *dirty write*.

Read committed is a very popular isolation level. It is the default setting in Oracle, Postgres, and many others. Commonly databases prevent dirty writes by using row-level locks: when a transaction wants to modify a particular object (row or document), it must first acquire a lock on that object. It must then hold that lock for any given object; if another transaction wants to writes to the same object, it must wait until the first one has committed or aborted before it can acquire the lock and continue.

To prevent dirty reads, Read Committed DBs simply save the old version of committed data and provide that to all reads until a new value has been committed.

#### Snapshot Isolation


![](../Attachments/Pasted%20image%2020221227002641.png)

While it might seem like *Read Committed* hits everything we need, look at the above picture for a bug called *Read Skew* where Alice observes the database in an inconsistent state. One of her reads is looking at the old version of the data, the second read is looking at the updated data. This bug is not a huge deal for many applications as you can just refresh the data you are looking at and get the right data, but for certain applications this is unacceptable.
- Backups: Taking a backup requires making a copy of the entire database, which may take hours on a large database., During the time that the backup process is running, writes will continue to be made to the database.
- Analytics and Integrity Checks: Sometimes you may want to run a query that scans over large parts of the database. Such queries are common in analytics, or may be a periodic integrity check.

The most common solution to this problem is *Snapshot Isolation*. The idea is that each transaction reads from a *consistent snapshot* of the database- that is, the transaction sees all the data that was committed in the database at the start of the transaction. Even if the data is subsequently changed by another transaction, each transaction sees only the old data from that particular point in time. This is a boon for long-running, read-only queries. The key principle of Snapshot Isolation is *readers never block writers, and writers never block readers*.

**Visibility rules for observing a consistent state:**
1. At the start of every transaction, the DB makes a list of all the other transactions that are in progress (not yet committed or aborted) at that time. Any writes that those transactions have made are ignored, even if the transactions subsequently commit.
2. Any writes made by aborted transactions are ignored.
3. Any writes made by transactions with a later transaction ID (i.e. which started after the current transaction started) are ignored, regardless of whether those transactions have committed.
4. All other writes are visible to the application's queries.


#### Preventing Lost Updates

The isolation levels described primarily protect against dirty reads in the case of concurrent writes. There are several other race conditions that can occur between concurrent writes. Lost Updates can occur if an application reads some value from the database, modifies it, and writes back the modified value. If two transactions do this concurrently, one of th3e modifications can be lost, because the second write does not include the first modification. This is a very common problem, and several solutions have been developed.

- **Atomic Write Operations**: Many databases provide atomic update operations, which removes the need to implement read-modify-write cycles in application code, such as simple as 
`UPDATE counters SET value = value + 1 WHERE key = 'foo';`
- **Explicit Locking**: the application can perform a transaction, and if any other transaction tries to concurrently read the same object, it is forced to wait until the first transaction has finished.
- **Automatically Detecting Lost Updates**: Simply detect when an update has been lost, and retry that specific transaction. Many DBs offering Snapshot Isolation also detect and fix lost updates. This is less error prone than the other options.
- **Compare-and-Set**: Only allow updates if the data has not been changed from the time the transaction started. This is a rudimentary method to prevent lost updates by refusing them entirely if the data being updated has been changed.


#### Write Skew and Phantoms

Another type of race condition is *Write Skew* when two transactions read the same objects and then update some of those objects (different transactions may update different objects). In the special case where different transactions update the same object, you get a dirty write or lost update anomaly (depending on the timing). 

![](../Attachments/Pasted%20image%2020221227013633.png)

Here you can see an example where one doctor has to always be on-call. Both doctors concurrently read the DB and see there is another doctor on-call so they both leave, even though this shouldn't be allowed. Unlike Lost Updates, our previous solutions will not work. For something like two people trying to register the same Username at the same time, add a UNIQUE constraint to the database. This will not always work as like in the example above.

When the write of one transaction changes the results of a search query in another transaction, it is called a *Phantom*. Snapshot Isolation avoids phantoms in read-only queries, but in read-write transactions like the doctors example, phantoms can lead to particularly tricky cases of write skew.


## Serializability

As we've seen, certain bugs can't be caught using Read Committed or Snapshot Isolation levels. The only way to solve these sneaky bugs is the simplest solution of all- *serializable* isolation! This isolations level is generally regarded as the strongest isolation level. It guarantees that even though transactions may execute in parallel, the end result is the same as if they had executed one at a time, *serially*, without any concurrency. In other words, the database prevents *all* possible race conditions. If it's so great, then why isn't this used more? Well there are tradeoffs, and we'll see how each option tries to mitigate these costs.

#### Actual Serial Execution

The simplest way of avoiding concurrency problems is to remove the concurrency entirely: to execute only one transaction at a time, in serial order, on a single thread. By doing so, we completely sidestep the problem of detecting and preventing conflicts between transactions: the resulting isolation is by definition serializable. This approach limits the throughput of a DB to that of a single CPU core on a single machine. In order to make the most of that single thread, transactions need to be structured differently from their traditional form. Additionally this method is limited to cases where the active dataset can fit in memory. Rarely accessed data could potentially be moved to disk, but if it needed to be accessed in a single-treaded transaction, the system would get very slow.

**Stored Procedures**: Imagine if you wanted to book an airline ticket. You look through all the tickets and spend a few minutes deciding which one to buy. Under a Single-Threaded system, you would have locked the entire database while your transaction was executing, hence interactive multi-statement transactions are not allowing in single-threaded systems. Instead the application must submit the entire transaction code to the database ahead of time as a *stored procedure*.

**Partitions**: In order to scale such a system, you can partition the data such that each CPU core gets its own partition. This allows us to scale machines, but brings significant overhead compared to single-threaded transactions. Also, any transaction that has to access multiple partitions will face significant potential slowdown. This partitioning is best used with [Key-Value Stores](NoSQL.md) like Redis, as you don't have to worry about accessing multiple partitions, and the partitioning process itself is very simple.


#### Two-Phase Locking (2PL)

We saw previously that locks are often used to prevent dirty writes: if two transactions concurrently try to write to the same object, the lock ensures that the second writer must wait until the first one has finished its transaction (aborted or committed) before it may continue.

Two-Phase Locking is similar, but makes the lock requirements much stronger. Several transactions are allowed to concurrently read the same object as long as nobody is writing to it. As soon as someone wants to write an object, exclusive access access is required:
- If transaction A has read on an object and transaction B wants to write to that object, B must wait until A commits or aborts before it can continue. (This ensures that B can't change the object unexpectedly behind A's back.
- If transaction A has written an object and transaction B wants to read that object, B must wait until A commits or aborts before it can continue (Reading an old version of the object is not acceptable in 2PL)

In 2PL, writers don't just block other writers; they also block readers and vice versa. Snapshot Isolation has the mantra *readers never block writers, writers never block readers*, on the other hand 2PL provides serializability and prevents all race conditions.

The blocking of readers and writers is implemented by having a lock on each object in the database. The lock can either be in *shared mode* or in *exclusive mode*.
- If a transaction wants to read an object, it must first acquire the lock in shared mode. Multiple transactions are allowed to hold the lock in shared mode simultaneously, but if another transaction already has an exclusive lock on the object, the other transaction must wait.
- If a transaction wants to write to an object, it must first acquire the lock in exclusive mode. No other transaction may hold the lock at the same time (either in shared or exclusive), so if there is any existing lock on the object, the transaction must wait.
- If a transaction first reads then writes to an object, it may upgrade its shared lock to an exclusive lock, with the same system as getting an exclusive lock directly
- After a transaction has acquired the lock, it must continue to hold the lock until the end of the transaction (commit or abort). This is where the name "**two-phase**" comes from: the first phase (while the transaction is executing) is when the locks are acquired, the second phase (at the end of the transaction) is when all the locks are released.

**Deadlocks**: when two transactions are waiting on the other one to finish. The database automatically detects deadlocks and aborts one of the transactions so that the other one can make progress. This process can seriously slowdown performance as all the aborted transactions need to be retried, along with the overhead with detecting and aborting deadlocked transactions.

**Predicate Locks**: How does 2PL prevents Phantoms as described above? We can apply a *predicate lock* to the exclusive/shared lock, but rather than belonging to a particular object (e.g. one row in a table), it belongs to all objects that match some search condition. These types of locks perform very poorly in high-throughput DBs, checking for matching locks becomes time-consuming.

**Index-Range Locks**: A simplified version of predicate locks by making it match a greater set of objects. Approximating the search condition is good enough and much more performant, hence why most databases with 2PL actually implement *index-range locking*


#### Serializable Snapshot Isolation (SSI)

2PL is a so called *Pessimistic* control system as is believes that if anything might possibly go wrong, it's better to wait until the situation is safe again before doing anything. By contrast, *serializable snapshot isolation* is an *Optimistic* system. Instead of blocking if something dangerous could happen, transactions continue anyway. When a transaction want to commit, the database check whether anything bad happened (i.e. whether isolation was violated); if so, the transaction is aborted and has to be retried. Only transactions that executed in series are allowed to commit.

Queries based on a false premise are the root of many errors. To provide serializable isolation, the database must detect situations in which a transaction may have acted on an outdated premise and abort the transaction in that case.

**Detecting Stale Reads**: Just like Snapshot Isolation, the transaction can see if any other transaction are currently trying to write to the same object. When the transaction tries to commit, the database checks whether any of the ignored writes have now been committed. If so, the transaction must be aborted. By waiting until committing and avoiding unnecessary aborts, SSI preserves snapshot isolation's support for long-running reads from a consistent snapshot.

**Detecting Writes That Affect Prior Reads**: The second case to consider is when another transaction modifies data after it has been read. In the context of 2PL, we discussed index-range locks, which allow the DB to lock access to all rows matching some search query. SSI does something similar except that SSI locks don't block other transactions. When a transaction writes to the DB, it must look in the indexers for any other transactions that have recently read the affected data. This process is similar to acquiring a write lock on the affected key range, but rather than blocking until the readers have committed, the lock acts as a tripwire: it simply notifies the transactions that the data they read may not longer be up to date. See below.

![](../Attachments/Pasted%20image%2020221227161631.png)

SSI is the best method of Serializable Isolation Level, as it can be on multiple CPU cores, and it won't lock down objects whenever one transaction wants to access it.