
With [Batch Processing](Batch%20Processing.md), the input is bounded, so the batch process will know when it's reached the end of the input data. But what if the input in unbounded? In reality, a lot of data is unbounded because it arrives gradually over time: your users produced data yesterday and today, and they will continue to produce more data tomorrow. Thus batch processes artificially bound inputs by day, hour, etc. If we processed the data as it came in, continuously, that is called *stream processing*.

## Transmitting Event Streams

In the batch processing world, the inputs and outputs of a job are files (perhaps on a distributed file system). What does the streaming equivalent look like? When the input is a file, the first step generally is to parse it into a series of records. In stream processing, a record is more commonly known as an *event*, a small self-contained, immutable object containing the details of something that happened at some point in time. An event generally contains a timestamp of when it was recorded.

In batch processing, a file is written once, and then potentially read by multiple jobs. Analogously, in streaming terminology, an event is generated once by a *producer* (also known as *publisher* or *sender*), and then potentially processed by multiple *consumers* (or *subscribers* or *recipients*). In a filesystem, a filename identifies a set of related records; in a streaming system, related events are usually grouped together into a *topic* or *stream*. In principle, a file or database is sufficient to connect producers and consumers. The producer writes every event that it generates to the datastore, and each consumer periodically polls the datastore to check for events that have appeared since it last ran. 

However, when moving towards continual processing with low delays, polling becomes expensive if the datastore is not designed for this kind of usage. The more often you poll, the lower the percentage of requests that return new events, and thus the higher the overheads become. Instead, it is better for consumers to be notified when new events appear.

#### Messaging Systems

A common approach for notifying consumers about new events is to use a *messaging system*: a producer sends a message containing the event, which is then pushed to consumers. Within this [Pub-Sub Model](Pub-Sub%20Model.md), different systems take a wide range of approaches, and there is no one right answer for all purposes. To differentiate systems, it is important to ask the following two questions:
1. *What happens if the producers send messages faster than the consumers can process them?* Broadly speaking, there are three options: the system can drop messages, buffer messages in a queue, or apply *backpressure* (blocking the producer from sending more messages). For example, Unix Pipes and [TCP](../Internet%20&%20Networking/TCP.md) use backpressure: there is a small fixed-size buffer, and if it fills up, the sender is blocked until the recipient takes data out of the buffer. If the data is buffered in a [Message Queue](Message%20Queue.md), it is important to know what happens as the queue grows. Does it crash if the queue can't fit in memory?
2. *What happens if nodes crash or temporarily go offline, are any messages lost?* As with databases, durability may require some combination of writing to disk and/or [Replication](../Databases/DB%20Replication.md), which has a cost. If you can afford to lose some messages, you can probably get higher throughput and lower latency on the same hardware.

A number of messaging systems use direct network communication between producers and consumers without going via intermediary nodes. However, these are quite prone to message loss, so they are only used in low latency or low simple systems.

#### Message Brokers

A widely used alternative is to send messages via a *message broker* (also known as a *message queue*), which is essentially a kind of database that is optimized for handling message streams. It runs as a server, with producers and consumers connecting to it as clients. Producers write messages to the broker, and consumers receive them by reading them from the broker. By centralizing data in the broker, these systems can more easily tolerate clients that come and go (connect, disconnect, crash), and the question of durability is moved to the broker instead. Faced with slow consumers, they generally allow unbounded queueing, as opposed to dropping messages or backpressure.

Some message brokers even participate in [two-phase commit](Consistency%20and%20Consensus.md) protocols using XA or JTA. This makes them quite similar to databases, although there are still important practical differences:
- Databases usually keep data until it is explicitly deleted, whereas most message brokers automatically delete a message when it has been successfully delivered to its consumers. Such message brokers are not suitable for long term data storage
- Since they quickly delete messages, most message brokers assume that their working set is fairly small i.e. the [Queues](../Data%20Structures%20&%20Algorithms/Data%20Structures/Queues.md) are short. If the broker needs to buffer a lot of messages because the consumers are slow, each individual message takes longer to process, and the overall throughput may degrade.
- Databases often support secondary indexes and various ways of searching for data, while message brokers often support some way of subscribing to a subset of topics matching some pattern. Both are essentially ways for clients to select specific portions of data.
- When querying a database, the result is typically based on a point-in-time snapshot of the data; if another client subsequently writes something to the database that changes the query result, the first client does not find out that its prior result is now outdated (unless it repeats the query or polls for changes). By contrast, message brokers do not support arbitrary queries, but they do notify clients when data changes (i.e. when new messages become available).

This is the traditional view of message brokers, which is encapsulated in standards like JMS and AMQP and implemented in software like *RabbitMQ* and *Google Pub/Sub*.

![](../Attachments/Pasted%20image%2020230201131824.png)

When multiple consumers read messages in the same topic, two patterns emerge:
- **Load Balancing**: Each message is delivered to *one* of the consumers, so the consumers can share the work of processing the messages in the topic. The broker may assign messages to consumers arbitrarily. This pattern is useful when the messages are expensive to process, and so you want to be able to add consumers to parallelize the processing.
- **Fan-out**: Each message is delivered to *all* of the consumers. Fan-out allows several independent consumers to each "tune in" to the same broadcast of messages, without affecting each other; the streaming equivalent of having several batch jobs that read the same input file.

Consumers may crash at any time, so message brokers use *acknowledgements*: a client must explicitly tell the broker when it has finished processing a message so that the broker can remove it from the queue.

#### Partitioned Logs

Sending a packet over a network or writing a message to a broker are considered transient operations. They will eventually be deleted or discarded. Databases take the opposite approach of permanently recording everything written to the database. If you add consumers to a queue, it will only start receiving data from the time it was added, you can't look back in time. There is actually a hybrid approach that allows the best of both worlds called *log-based message brokers*.

A log is simply an append-only structure of records on disk. The same structure can be used to implement a message broker: a producer sends a message by appending it to the end of the log, and a consumer receives messages by reading the log sequentially. If a consumer reaches the end of the log, it waits for a notification that a new message has been appended. In order to scale this to higher throughput, the log can be [partitioned](../Databases/DB%20Partitions.md). This is the system implemented by *Apache Kafka*.

![](../Attachments/Pasted%20image%2020230201133811.png)

This approach can implement fan out trivially as consumers can independently read the log without affecting each other, reading a message from the log does not delete it. Load balancing can be done if the broker assigns entire partitions to nodes in the consumer group.

Each client consumes *all* the messages in the partition it has been assigned. Typically, when a consumer has been assigned a log partition, it read the messages in the partition sequentially, in a straightforward manner. This can cause problems with head-of-line blocking and limited number of partitions. Thus, if you want to parallelize processing on a message-by-message basis, and where messaging ordering is not important, the Message Broker is preferable. On the other hand, in situations with high message throughput, where each message is fast to process and where message ordering is important, the log-based approach works very well.


## Databases and Streams

There is a fundamental connection between databases and streams. In fact, a [Replication Log](../Databases/DB%20Replication.md) is a stream of database write events, produced by the leader as it processes transactions. The followers apply that stream of writes to their own copy of the database and thus end up with an accurate copy of the data. The events in the replication log describe the data changes that occurred.

There is no single system that can satisfy all data storage, querying, and processing needs. In practice, most non-trivial applications need to combine several different technologies. As the same or related data appears in several different places, the y need to be kept in sync with one another: if an item is updated in the database, it also needs to be updated in the [Cache](../Databases/Database%20Cache.md), search indexes, and data warehouse. Data warehouse synchronization is usually done with *ETL* (extract, transform, load integration process), which is a batch process. If periodic full database dumps are too slow, an alternative that is sometimes used is *dual writes*, in which the application code explicitly writes to each of the systems when data changes:

![](../Attachments/Pasted%20image%2020230201142716.png)

If one write fails while the other succeeds, then your dataset will be inconsistent. You will need [atomic commit](Consistency%20and%20Consensus.md) guarantees, which is expensive.

#### Change Data Capture

What if we used a "Leader" and replicated its copy like in [single-leader replication](../Databases/DB%20Replication.md) instead of writing multiple parallel writes? There is a process called *Change Data Capture* (CDC) which is the process of of observing all data changes written to a database and extracting them in a form in which they can be replicated to other systems. CDC is especially interesting if the changes are made available as a stream:

![](../Attachments/Pasted%20image%2020230201153044.png)

Essentially, CDC makes one database the leader and turns the other ones into followers. A log-based message broker is well suited for transporting the change events from the source database, since it preserves the ordering of messages. Database triggers can be used to implement DCD, by registering triggers that observe all changes to the data tables and add corresponding entries to a changelog table. However, they tend to be more fragile. Parsing the replication log itself can be more robust, although it comes with challenges such as handling schema changes.

Like message brokers, Change Data Capture is usually asynchronous: the system of record database does not wait for the change to be applied to consumers before committing it. If you want to build a new full-text database for example, you will need to entire history and may need to load a [consistent snapshot](../Databases/DB%20Replication.md) initially to the CDC. Also, we can be more judicious with storage by using [log compaction](../Databases/Underlying%20DB%20Data%20Structures.md) which is the same process used by SSTables and LSM Trees. *Event Sourcing* is a software architectural strategy of keeping all changes to application state in an ordered event stream. This strategy is enabled by CDC.

#### State, Streams, and Immutability

[Batch Processing](Batch%20Processing.md) benefits from the immutability of its input files, so you can run experimental processing jobs on existing input files without damaging them. This principle of immutability is also what makes Change Data Capture so powerful. Whenever you have state that changes, that state is the result of the events that mutated it over time. The log of all changes, the *changelog*, represents the evolution of state over time. A simple mathematical equivalent to this relationship is shown below:


$$\text{state(now)} = \int_{t=0}^{\text{now}}\text{stream(t)}dt$$
$$\text{stream(t)} = \frac{d \text{state(t)}}{dt}$$

Immutable logs provide many benefits. they are entirely auditable as to how the current application state has been reached. Storing data is normally quite straightforward if you don't have to worry about how it is going to be queried and accessed. Most of the complexity comes from wanting to support complex queries and access patterns. You gain a lot of flexibility by separating the form in which data is written from the form in which it is read, and by allowing several different read views. This idea is known as *command query responsibility principle* (CQRS).

## Processing Streams

There are three things you can do with a stream:
1. You can take the data in the events and write it to a database, cache, search index, or similar storage system, from where it can then be queried by other clients. This is a good way of keeping a database in sync with changes happening in other parts of the system - especially if the stream consumer is the only client writing to the database.
2. You can push the events to users in some way, for example by sending email alerts or push notifications, or by streaming the events to a real-time dashboard where they are visualized. In this case, a human is the ultimate consumer of the stream.
3. You can process one or more input streams to produce one or more output streams. Streams may go through a pipeline consisting of several such processing stages before they eventually end up at an output (option 1 or 2)

The rest of this article discusses option 3, known as a *job*. The patterns for partitioning and parallelization is very similar to MapReduce and Dataflow engines used in [Batch Processing](Batch%20Processing.md), so they won't be repeated here. The one crucial difference with batch jobs is that a stream never ends.

#### Uses of Stream Processing

**Complex Event Processing** (CEP) is an approach for analyzing event streams, especially geared towards the kind of application that requires searching for certain event patterns. In these systems the relationship between queries and data is reversed compared to regular databases. Queries are saved as *complex events* while regular events are transient.

**Stream Analytics** tends to be less interested in specific events than CEP. Analytics is more geared towards aggregations and statistical metrics over a large number of events. Such statistics are usually computed over fixed [time intervals](Distribution%20Problems.md). For example, you might want to know the average number of queries per second to a service over the last 5 minutes.

A stream of changes can be used to keep all derived datasets to the main one up to date. These are use cases of **Materialized Views** which look at the entire history of the stream, not just a time interval.

**Message Passing** is an alternative to RPC. Although one may not think of these systems as stream processors, there are some similarities.

#### Reasoning About Time

Many stream processing frameworks use the local system clock on the processing machine to determine time windows. This approach has the advantage of being simple, and it is reasonable if the delay between event creation and processing is negligibly short. However, it breaks down if there is any significant processing lag, i.e.  if the processing may happen noticeably later than the time at which the event actually occurred. There are many reasons why be delayed: queueing, [network faults](Distribution%20Problems.md), a performance issue leading to contention in the message broker or processor, a restart of the stream's consumer, or reprocessing of past events while recovering from a fault or after fixing a bug.

![](../Attachments/Pasted%20image%2020230201165222.png)

A tricky problem when defining windows in terms of event time is that you can never be sure when you have received all of the events for a particular window, or whether there are some events still to come. You can time out and declare a window ready after you have not seen any new events for a while, but it could still happen that some events were buffered on another machine somewhere, delayed due to a network interruption. You need to handle these *straggler* events that arrive after the window has closed. Broadly, you have two options:
1. Ignore the straggler events, as they are probably a small percentage of events in normal circumstances. You can track the number of dropped events as a  metric, and alert if you start dropping a significant amount of data.
2. Publish a *correction*, an updated value for the window with stragglers included. You may also need to retract the previous output.

Assigning timestamps to events is even more difficult when events can be buffered at several points in the system. You will encounter incorrect device clocks. You can trust the server clock as it is under your control, but users can alter their own clocks. To adjust for incorrect clocks, one approach is to log three timestamps:
- The time at which the event occurred, according to the device clock
- The time at which the event was sent to the server, according to the device clock
- The time at which the event was received by the server, according to the server clock

By subtracting the second time from the third, you can estimate the offset between the device clock and server clock. You can then apply that offset to the event timestamp, and thus estimate the true time at which the event actually occurred. 

Once you know how the timestamp of an event should be determined, the next step is to decide how windows over time periods should be defined. The window can then be used for aggregations, for example to count events, or to calculate average values. Several types of common windows are in use:
- **Tumbling window**: a tumbling window has a fixed length, and every event belongs to exactly one window. For example, if you have a 1-minute window, all the events with timestamps between 10:03:00 and 10:03:59 are in the same window.
- **Hopping window**: a hopping window also has a fixed length, but allows windows to overlap in order to provide some smoothing. For example, a 5-minute window with a hop size of 1 minute would contain the events between 10:03:00 and 10:07:59, then the next window would cover events between 10:04:00 and 10:08:59
- **Sliding window**: a sliding window contains all the events that occur within some interval of each other. For example, a 5-minute sliding window would cover events at 10:03:39 and 10:08:12 as they are less than 5 minutes apart. Tumbling and hopping windows would not contain these two events as they use fixed boundaries.
- **Session window**: Unlike the other window types, a session window has no fixed duration. Instead, it is defined by grouping together all events for the same user that occur closely together in time, and the window ends when the user has been inactive for some time (for example if there has been no events for 30 minutes).


#### Stream Joins

Joins are a little more challenging when working with streams. There are three types of joins: *stream-stream*, *steam-table*, and *table-table*.

*Stream-stream* joins (window joins) are when you want to combine two streams. You must choose a suitable window for both streams. To implement this join, a stream processor needs to maintain *state*: for example, all the events that occurred in the last hour, indexed by session ID. Whenever a search event or click event occurs, it is added to the appropriate index, and the stream processor also checks the other index to see if another index for the same session ID has already arrived. If there is a matching event, you emit an event saying which search result was clicked. If the search event expires without you seeing a matching click event, you emit an event saying which search results were not clicked.

*Stream-table* joins (stream enrichment) is when you combine a stream with a batched table to "enrich" the stream. Likewise we can also do a *table-table* join which is used for materialized view maintenance, to maintain a cache of data from the streams.

#### Fault Tolerance

Batch processing can handle faults relatively easily. In particular, the batch approach to fault tolerance ensure that the output of the batch job is the same as if nothing had gone wrong, even if in fact some tasks did fail. It appears as though every input record was processed exactly once, no records are skipped, and none are processed twice. Although restarting tasks means that records may in fact be processed multiple times, the visible effect int he output is as if they had only been processed once. This principle is known as *exactly-once semantics*, although *effectively-once* would  be a more descriptive name.

One solution is to break the stream into small blocks, and treat each block like a miniature batch process. This approach is called *micro-batching*, and it is used in *Spark Streaming*. The batch size is typically around one second, to compromise between the coordination overhead of smaller batches and the longer delay of larger batches. We can also use distributed [transactions](Consistency%20and%20Consensus.md) to ensure that all the outputs and side effects of processing an event takes place *if and only if* the processing is successful. Lastly, we can use *idempotent writes*. An idempotent operation is one that you can perform multiple times, and it has the same effect as if you performed it only once.