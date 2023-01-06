
A [Transaction](Transactions.md) is a logical grouping of reads and writes in a database. ACID describes the attributes of a reliable database and ensures reliability that is key with important transactions like Bank transfers, imagine if a fault network caused your bank withdrawal to happen twice. An ACID transaction obeys the following:


## Atomicity

In the context of ACID, atomicity describes what happens when a client wants to make several writes, but a fault occurs after some of the writes have been processed - for example, a process crashes, a network connection is interrupted, a disk becomes full, or some integrity constraint is violated. If the writes are grouped together into an atomic transaction, and the transaction cannot be completed (*committed*) due to a fault, then the transaction is *aborted* and the database must discard or undo any writes it has made so far in that transaction. With this, you can be confident that the application did not partially apply changes and you can safely retry the entire transaction.

Note that the term atomicity comes up in other contexts such as in Multi-Threaded Programming, where no thread can see the half-finished result of the operation of another thread


## Consistency

Consistency here means that certain statements about your data (*invariants*) must always be true - for example, in an accounting system, credits and debits must always be balanced. If a transaction starts with a database that is valid according to these invariants, and any writes during the transaction preserve their validity, then you ca be sure that the invariants are always satisfied. 

However the definition of what is invariant is the application's responsibility. This is not something that the database can guarantee: if you write bad data that violates your invariants, the database can't stop you. Atomicity, Isolation, and Durability are properties of the database, whereas Consistency (in the ACID sense) is a property of the application, thus the letter C doesn't really belong in ACID!


## Isolation

Isolation means that concurrently executing transactions are isolated from each other: they cannot step on each other's toes. This issue comes up especially with distributed databases. Classically, this is formalized in textbooks as *serializability* however in practice serializable isolation is rarely used, because it carries a performance penalty.


## Durability

This is the promise that once a transaction has committed successfully, any data it has written will not be forgotten, even if there is a hardware fault or the database crashes. For a single-node system, this means the data has been written to non-volatile storage such as a hard drive or SSD. in a [replicated database](DB%20Replication.md), durability may mean that the data has been successfully copied to some number of nodes. In order to provide a durability guarantee, a database must wait until these writes or replications are complete before reporting a transaction as successfully committed.