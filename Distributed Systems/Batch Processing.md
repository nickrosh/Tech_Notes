
*Batch Processing* is the act of processing a large quantity of bounded data at one time. Traditionally, this can be done with [Unix Tools](../Software%20Engineering/Common%20Linux%20Commands.md) like grep and the pipeline. The Unix philosophy believes that data and I/O should flow from one application to another in pipelines, like connecting different things with a garden hose. Part of what makes Unix Tools so successful is that they can easily see what's going on:
- The input files to Unix commands are normally treated as immutable. This means you can run the commands as often as you want, trying various options without damaging the input files
- You can end the pipeline at any point, pipe the output into `less`, and look at it to see if it has the expected form. This inspection is very useful for debugging
- You can write the output of one pipeline stage to a file and use that file as input to the next stage. This allows you to restart the later stage without rerunning the entire pipeline.

Here's an example of finding the 5 most popular pages on your website:

```shell
cat /var/log/nginx/access.log |
awk '{print $7}' |
sort |
uniq -c |
sort -r -n |
head -n -5
```

1. Read the log file
2. `awk` to split each line into fields by whitespace, and output only by the seventh such field from each line, which happens to be the requested URL
3. Alphabetically sort the list of requested UIRLs
4. filter out repeated lines in sorted URL list. the `-c` flag adds a counter of how many repeats were found for each URL
5. The second sort sorts by the number `-n` at the start of each line, which is the counter we got from `uniq`
6. Finally, `head` prints out the top five results, the top five most visited pages


## MapReduce and Distributed Filesystems

*MapReduce* is a bit like Unix tools, but distributed across potentially thousands of machines. While Unix tools use `stdin` and `stdout` as input and output, MapReduce jobs read and write files on a distributed filesystem. In Hadoop's implementation of MapReduce, that filesystem is called HDFS (Hadoop Distributed File System), an open source reimplementation of the Google File System. Object storage systems like S3 are also similar, except that computing tasks are done on separate machines, unlike in HFDS.

HFDS consists of a daemon process running on each machine, exposing a network service that allows other nodes to access files stored on that machine. A central server called the *NameNode* keeps track of which file blocks are stored on which machine. Thus, HFDS conceptually creates one big filesystem that can use the space on the disks of all machines running the daemon. Data replication is also done in a system similar to RAID to keep the filesystem fault resistant to any disk failures.

#### MapReduce Job Execution

MapReduce is a programming framework with which you can write code to process large datasets in a distributed filesystem like HDFS. The easiest way of understanding it is by referring back to the web server log analysis above. The pattern of data processing in MapReduce is very similar to this example:
1. Read a set of input files, and break it up into *records*. In the web server example, each record is one line in the log
2. Call the mapper function to extract a key and value from each input record. In the preceding example, the mapper function is `awk '{print $7}'` , it extracts the URL ($7) as the key, and leaves the value empty.
3. Sort all of the key-value pairs by key. In the log example, this is done by the first `sort` command.
4. Call the reducer function to iterate over the sorted key-value pairs. If there are multiple occurrences of the same key, the sorting has made them adjacent in the list so it is easy to combine them without having to keep state memory. This is done above with `uniq -c`.

These four steps can be performed by one MapReduce job. Steps 2 (map) and 4 (reduce) are where you write your custom data processing code. Step 1 (breaking files into records) is handled by the input format parser. Step 3, the sort step, is implicit in MapReduce, you don't have to write it, because the output from the mapper is always sorted before it is given to the reducer.
- **Mapper**: The mapper is called once for every input record, and its job is to extract the key and value from the input record. For each input, it may generate any number of key-value pairs (including none). It does not keep any state from one input record to the next, so each record is handled independently.
- **Reducer**: The MapReduce framework takes the key-value pairs produced by the mappers, collects all the values belonging to the same key, and calls the reducer with an iterator over that collection of values. The reducer can produce output records (such as the number of occurrences of the same URL)

![](../Attachments/Pasted%20image%2020230131154305.png)

The main difference between MapReduce and traditional Unix tools is that MapReduce can be parallelized across many machines. The above image demonstrates the dataflow in a parallel MapReduce job. Its parallelization is based on [partitioning](../Databases/DB%20Partitions.md): the input to a job is typically a directory in HDFS, and each file or file block within the input directory is considered to be a separate partition that can be processed by a separate map task. Each file is typically hundreds of MBs in size. The scheduler tries to run each mapper on one of the machines that stores a replica of the input file, this principle is known as *putting the computation near the data*, and saves copying the input file over the network.

The key-value pairs must be sorted, but the dataset is likely too [large](../Data%20Structures%20&%20Algorithms/Time%20&%20Space%20Complexity.md) to sorted with a conventional sorting algorithm on a single machine. Instead the sorting is performed in stages. First, each map task partitions its output by reducer, based on the [hash](../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md) of the key. Each of these partitions is written to a sorted file on the mapper's local disk, similar to how [SSTables and LSM Trees are made](../Databases/Underlying%20DB%20Data%20Structures.md). When the mapper finishes reading its input file and writing its sorted output files, the scheduler notifies the reducers that they can start fetching the output files from that mapper. This process os partitioning by reducer, sorting, and copying data partitions from mappers to reducers is known as *shuffling*.

A single MapReduce job cannot do much, so we typically chain multiple jobs together in a [Directed Acyclic Graph](../Data%20Structures%20&%20Algorithms/Data%20Structures/Graphs.md) workflow, using scheduling software like *Airflow*.


## Beyond MapReduce

Although MapReduce has become popular and easy to understand, in practice it is not simple. Implementing a complex processing job using the raw MapReduce API is quite hard and labor intensive, and you would need to implement JOINs from scratch. In response to this difficulty various abstraction models like Hive, Pig, and Crunch were created to create high-level constructs on top of MapReduce. However, there are also problems with MapReduce itself, namely the fact that it runs slowly and mostly on disk.

#### Dataflow Engines

Several new execution engines have sprung up to fix the problems of batch execution. The most popular being *Spark*. The biggest difference being that they handle an entire workflow  as one job, rather than breaking it up into independent sub-jobs. Since they explicitly model the flow of data through several processing stages, these systems are known as *dataflow engines*. Like MapReduce, they work by repeatedly calling a user-defined function to process one record at a rime on a single [thread](../Systems%20Software/Processes%20&%20Threads.md). They parallelize work by partitioning inputs, and they copy the output of once function over the network to become the input to another function.

Unlike MapReduce, these functions need not take the strict role of alternating map and reduce, but instead can be assembled in more flexible ways. We call these functions *operators*, and the dataflow engine provides several different options for connecting one operator's output to another's input:
- One option is to repartition and sort record by key, like in the shuffle stage of MapReduce. This feature enables sort-merge joins and grouping in the same way as in MapReduce.
- Another possibility is to take several inputs and to partition them in the same way, but skip the sorting. This saves effort on partitioned hash joining, where the partitioning of records is important but the order is irrelevant because building the hash table randomizes the order anyway.
- For broadcast hash joins, the same output from one operator can be sent to all partitions of the join operator.

These systems generally outperform MapReduce for a number of reasons:
- Expensive work such as sorting need only be performed in places where it is required, rather than always happening by default between every map and reduce stage
- There are no unnecessary map tasks, since the work done by a mapper can often be incorporated into the preceding reduce operator
- Because all joins and data dependencies in a workflow are explicitly declared, the scheduler has an overview of what data is required where, so it can make locality optimizations. It will try to put compute and memory on the same machine
- It is usually sufficient for intermediate state between operators to be kept in memory or written to local disk, which requires less I/O than writing it to HDFS. MapReduce already performs this optimization for the mapper stage, but dataflow engines generalize this to all intermediate state.
- Operators can start executing as soon as their input is ready; there is no need to wait for the entire preceding stage to finish before the next one starts.
- Existing JVM processes can be reused to run new operators, reducing startup overheads comparted to MapReduce (which launches a new JVM for each task)

These engines also have systems to account for fault tolerance. Spark uses the *Resilient Distributed Dataset* (RDD) abstraction for tracking the ancestry of data. Thus the absolute input and output are deterministic, just like MapReduce. Only the intermediate stages are optimized.

#### High Level APIs and Languages

The ability to run arbitrary code has long been one of the biggest benefits of batch processing over Analytics Data Warehouses. Spark jobs are natively written in *Scala* which is a high-level data processing language similar to Java. There is also a Python client library called *PySpark*. However there are also pushes to bringing more of a declarative [SQL](../Databases/Relational%20Databases.md) style to batch processing, which makes it look more like the analytics warehouses.