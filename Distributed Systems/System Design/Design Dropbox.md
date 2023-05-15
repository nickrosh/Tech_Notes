
Cloud file storage services like Dropbox or Google Drive enable users to store their data on remote servers. Usually, these servers are maintained by cloud storage providers and made available to users over a network (typically through the internet). Users pay for their cloud data storage on a monthly basis. Cloud storage is very popular due to its many benefits over local storage

- **Availability**: The motto of cloud storage services is to have data availability anywhere, anytime. Users can access their files/photos from any device whenever and wherever they like
- **Reliability and Durability**: Another benefit of cloud storage is that it offers 100% reliability and durability of data. Cloud storage ensures that users will never lose their data by keeping multiple copies of the data stored on different geographically located servers.
- **Scalability**: Users will never have to worry about getting out of storage space. With cloud storage you have unlimited storage as long as you are ready to pay for it.

## Requirements
What do we wish to achieve from a cloud storage system? Here are the top level requirements

#### Functional Requirements
1. Users should be able to upload and download their files/photos from any device.
2. Users should be able to share files or folders with other users.
3. Our service should support automatic synchronization between devices, i.e. after updating a file on one device, it should get synchronized on all devices
4. The system should support storing large files up to 1GB
5. Our system should support offline editing. Users should be able to add/delete/modify files while offline, and as soon as they come online, all their changes should be synced to the remote servers and other devices.

#### Non-Functional Requirements
1. Our service needs to be highly available
2. Our service needs to be highly reliable, any uploaded file cannot be lost or deleted.
3. There should never be storage issues, users will never worry about running out of space unexpectedly

#### Extended Requirements
The system should support snapshotting of the data, so that users can go back to any version of the files.

## Some Design Considerations

- We should expect huge read and write volumes
- Read to write ratio is expected to be nearly the same
- Internally, files can be stored in small parts or chunks (say 4MB), this can provide a lot of benefits i.e. all failed operations shall only be retried for smaller parts of a file. If a user fails to upload a file, then only the failing chunk will be retried.
- We can reduce the amount of data exchange by transferring updated chunks only
- By removing duplicate chunks, we can save storage space and bandwidth usage.
- Keeping a local copy of the metadata (file name, size, etc.) with the client can save us a lot of round trips to the server.
- For small changes, clients can intelligently upload the diffs instead of the while chunk.

## Capacity Estimation and Constraints
- Let's assume we have 500M total users, and 100M daily active users (DAU).
- Let's assume that on average, each user connects from three different devices
- On average if a user has 200 files/photos, we will have 100 billion total files
- Let's assume the average file size is 100KB, this would give us 10 petabytes of total storage
- Let's also assume that we have one million active connections per minute.

## High Level Design

The user will specify a folder as the workspace on their device. Any file/photo/folder placed in this folder will be uploaded to the cloud, and whenever a file is modified or deleted, it will be reflected in the same way in the cloud storage. The user can specify similar workspaces on all their devices and any modification done on one device will be propagated to all other devices to have the same view of the workspace everywhere.

At a high level, we need to store files and their metadata information like File Name, File Size, Directory, etc. and who the file is shared with. So, we need some servers that can help the clients to upload/download files to Cloud Storage and some servers that can facilitate updating metadata about files and users. We also need some mechanism to notify all clients whenever an update happens so they can synchronize their files.

![](../../Attachments/Pasted%20image%2020230509193909.png)

## Component Design

Let's go through the major components one by one:

#### Client
The Client Application monitors the workspace folder on the user's machine and syncs all files/folders in it with the remote Cloud Storage. The client application will work with the storage servers to upload, download, and modify actual files to the backend Cloud Storage. The client also interacts with the remote Synchronization Service to handle any file metadata updates, e.g. change in file name, size, modification date, etc. Here are some essential operations for the client:

1. Upload and download files
2. Detect file changes in the workspace folder
3. Handle conflict dur to offline or concurrent updates

**How do we handle file transfer effiicently? **As mentioned above, we can break each file into smaller chunks so that we transfer only those chunks that are modified and not the whole file. Let's say we divide each file into dixed sizes of 4MB chunks. We can statically calculate what could be an optimal chunk size based on: 
1. storage devices we use in the cloud to optimize space utilization and input/output operation per second (IOPS) 
2. Network bandwidth 
3. Average file size in the storage etc. In our metadata, we should also keep a record of each file and the chunks that constitute it.

**Should we keep a copy of metadata with Clients?** Keeping a local copy of metadata not only enables us to do offline updates but also saves a lot of round trips to update remote metadata.

**How can clients efficiently listen to changes happening with other clients?** One solution could be that the clients periodically check with the server if there are any changes. The problem with this approach is that we will have a delay in reflecting changes locally as clients will be checking for changes periodically compared to a server notifying whenever there is some change. If the client frequently checks the server for changes, it will not only be wasting bandwidth, as the server has to return an empty response most of the time, but will also be keeping the server busy. Pulling information in this manner is not scalable.

A solution to the above problem could be to use [HTTP](../../Internet%20&%20Networking/HTTP.md) long polling. With long polling, the client requests information from the server with the expectation that the server may not respond immediately. If the server has no new data for the client when the poll is received, instead of sending an empty response, the server holds the request open and waits for response information to become available. Once it does have new information, the server immediately sends an HTTP response to the client, completing the open request. Upon receipt of the server response, the client can immediately issue another server request for future updates.

Based on the above considerations, we can divide our client into four parts:

1. **Internal Metadata Database** will keep track of all the files, chunks, their versions, and their locations in the file system.
2. **Chunker** will split the files into smaller pieces called chunks. It will also be responsible for reconstructing a file from its chunks. Our chunking algorithm will detect the parts of the files that have been modified by the user and only transfer those parts to the Cloud Storage, this will save us bandwidth and synchronization time.
3. **Watcher** will monitor the local workspace folders and notify the Indexer (below) of any action performed by the users e.g. when users create, delete, or update files or folders. Watcher also listens to any changes happening on other clients that are broadcasted by Synchronization service.
4. **Indexer** will process the events received from the Watcher and update the internal metadata database with information about the chunks of the modified files. Once the chunks are successfully submitted/downloaded to the Cloud Storage, the Indexer will communicate with the remote Synchronization Service to broadcast changes to other clients and update the remote metadata database.

![](../../Attachments/Pasted%20image%2020230509193946.png)

How should clients handle slow servers? Clients should exponentially back-off if the server is busy/not-responding. Meaning, if a server is too slow to respond, clients should delay their retires, and this delay should increase exponentially.

#### Metadata Database

The Metadata Database is responsible for maintaining the versioning and metadata information about files/chunks, users, and workspaces. The Metadata Database can be a [Relational Databases](../../Databases/Relational%20Databases.md) such as MySQL or a [NoSQL](../../Databases/NoSQL.md) database service such as DynamoDB. Regardless of the type of database, the Synchronization Service should be able to provide a consistent view of the files using a database, especially if more than one user is working with the same file simultaneously. Since NoSQL data stores so not support [ACID](../../Databases/ACID.md) properties in favor of scalability and performance, we need to incorporate the support for ACID properties programmatically in the logic of our Synchronization Service in case we opt for this kind of database. However, using a relational database can simplify the implementation of the Synchronization Service as they natively support ACID properties.

The metadata Database should be storing information about the following objects:
1. Chunks
2. Files
3. User
4. Devices
5. Workspace (sync folder)

#### Synchronization Service

The Synchronization Service is the component that processes file updates made by a client and applies these changes to other subscribed clients. It also synchronizes clients' local databases with the information stored in the remote Metadata DB. The Synchronization Service is the most important part of the system architecture due to its critical role in managing the metadata and synchronizing users' files. Desktop clients communicate with the Synchronization Service to either obtain updates from the Cloud Storage or send files and updates to the Cloud Storage and, potentially, other users. If a client was offline for a period, it polls the system for new updates as soon as they come online. When the Synchronization Service receives an update request, it checks with the Metadata Database for consistency and then proceeds with the update. Subsequently, a notification is sent to all subscribed users or devices to report the file update.

The Synchronization Service should be designed to transmit less data between clients and the Cloud Storage to achieve a better response time. To meet this design goal, the Synchronization Service can employ a differencing algorithm to reduce the amount of data that needs to be synchronized. Instead of transmitting entire files from clients to the server or vice versa, we can just transmit the difference between two versions of a file. Therefore, only the part of the file that has been changed is transmitted. This also decreases bandwidth consumption and cloud data storage for the end-user. As described above, we will be dividing our files into 4MB chunks and will be transferring modified chunks only. Server and clients can calculate a hash (e.g. SHA-256) to see whether to update the local copy of a chunk or not. On the server, if we already have a chunk with a similar hash (even from another user), we don't need to create another copy, we can use the same chunk. This is discussed in detail later under Data Deduplication.

To be able to provide an efficient and scalable synchronization protocol, we can consider using a communication middleware between clients and the Synchronization Service. The messaging middleware should provide scalable message queuing and change notifications to support a high number of clients using pull or push strategies. This way, multiple Synchronization Service instances can receive requests from a global [Message Queue](../Message%20Queue.md), and the communication middleware will be able to balance its load.

#### Message Queuing Service

An important part of our architecture is a messaging middleware that should be able to handle a substantial number of requests. A scalable Message Queuing Service that supports asynchronous message-based communication between clients and the Synchronization Service best fits the requirements of our application. The Message Queuing Service supports asynchronous and loosely coupled message-based communication between distributed components of the system. The Message Queuing Service should be able to efficiently store any number of messages in a highly available, reliable, and scalable queue.

The Message Queuing Service will implement two types of queues in our system. The Request Queue is a global queue and all clients will share it. Clients' requests to update the Metadata Database will be sent to the Request Queue first. From there, the Synchronization Service will take it to update metadata. The Response Queues that correspond to individual subscribed clients are responsible for delivering the update messages to each client. Since a message will be deleted from the queue once received by a client, we need to create separate Response Queues for each subscribed client to share update messages.

![](../../Attachments/Pasted%20image%2020230509194114.png)

#### Cloud/Block Storage

Cloud/Block Storage stores chunks of files uploaded by the users. Clients directly interact with the storage to send and receive objects from it. Separation of the metadata from storage enables us to use any storage either in the cloud or in-house.

![](../../Attachments/Pasted%20image%2020230509194120.png)

## File Processing Window

The sequence below shows the interaction between the components of the application in a scenario when Client A updates a file that is shared with Client B and C, so they should receive the update too. If the other clients are not online at the time of the update, the Message Queuing Service keeps the update notifications in separate response queues for them until they come online later.
1. Client A uploads chunks to cloud storage.
2. Client A updates metadata and commits changes.
3. Client A gets confirmation and notifications are sent to Clients B and C about the changes.
4. Client B and C receive metadata changes and download updated chunks.

## Data Deduplication

Data deduplication is a technique used for eliminating duplicate copies of data to improve storage utilization. It can also be applied to network data transfers to reduce the number of bytes that must be sent. Fore each new incoming chunk, we can calculate a hash of it and compare that hash with all the hashes of the existing chunks to see if we already have the same chunk present in our storage. 

We can implement deduplication in two ways:

#### Post-Process Deduplication

With post-process deduplication, new chunks are first stored on the storage device and later some process analyzes the data looking for duplication. The benefit is that clients will not need to wait for the hash calculation or lookup to complete before storing the data, thereby ensuring that there is not degradation in storage performance. Drawbacks of this approach are:
1. We will unnecessarily be storing duplicate data, though for a short time
2. Duplicate data will be transferred consuming bandwidth

#### In-Line Deduplication

Alternatively, deduplication hash calculations can be done in real-time as the clients are entering data on their device. If our system identifies a chunk that it has already stored, only a reference to the existing chunk will be added int he metadata, rather than a full copy of the chunk. This approach will give us optimal network and storage usage.

## Metadata Partitioning

To scale out the metadata DB, we need to [Partition](../../Databases/DB%20Partitions.md) it so that it can store information about millions of users and billions of files/chunks. We need to come up with a partitioning scheme that would divide and store our data in different DB servers.

#### Vertical Partitioning
We can partition our database in such a way that we store tables related to on particular feature on one server. For example, we can store all the user-related tables in one database and all files/chunks related table in another database. Although this approach is straightforward to implement it has some issues:
1. Will we still have scale issues? What if we have trillions of chunks to be stored and our database cannot support storing such a huge number of records? How would we further partition such tables?
2. Joining two tables in two separate databases can cause performance and consistency issues. How frequently do we have to join user and file tables?

#### Range Based Partitioning
What if we store files/chunks in separate partitions based on the first letter of the file path? In that case, we save all the files starting with the letter "A" in one partition and those that start with the letter "B" in another partition and so on. This approach is called range-based partitioning. We can even combine certain less frequently occurring letters into one database partition. We should come up with this partitioning scheme statically so that we can always store/find a file in a predictable manner.

The main problem with this approach is that it can lead to unbalanced servers. For example, if we decide to put all files starting with the letter "E" into a DB partition, and later we realize that we have too many files that start with the letter "E", to such an extent that we cannot fit them into one DB partition.

#### Hash Based Partition

In this scheme we take a hash of the object we are storing and based on this hash we figure our the DB partition to which this object should go. In our case, we can take the hash of the `FileID` of the File object we are storing to determine which partition the file will be stored in. Our hashing function will randomly distribute objects into different partitions, e.g. our hashing functions can always map any ID to a number between (1...256), and this number would be the partition we will store our object.

This approach can still lead to overloaded partitions, which can be solved by using [Consistent Hashing](../Consistent%20Hashing.md)

## Caching

We can have two kinds of [cache](../../Databases/Database%20Cache.md) in our system. To deal with hot files/chunks we can introduce a cache for block storage. We can use an off-the-shelf solution like Memcached that can store whole chunks with its respective IDs/Hashes and Block servers before hitting Block Storage to quickly check if the cache has the desired chunk. Based on clients' usage patterns we can determine how many cache servers we need. A high-end commercial server can have 144GB of memory, thus one such server can cache 36K chunks.

**Which eviction policy would best fit our needs?** When the cache is full, Least Recently Used (LRU) can be a reasonable policy for our system. Under this policy, we discard the least recently used chunk first. Similarly, we can have a cache for the Metadata Database.

## Load Balancer

We can add a [Load Balancer](../Load%20Balancer.md) to two places in our system:
1. Between Clients and Block Servers
2. Between Clients and Metadata servers

Initially, a simple round robin approach can be adopted that distributes incoming requests equally among backend servers. This LB is simple to implement and does not introduce any overhead. Another benefit of this approach is if a server is dead, LB will take it out of the rotation and will stop sending any traffic to it. A problem with round robin is that it doesn't take server load into account at all. We can thus use a weighted round robin that checks server capacity and distributes requests accordingly.

## Security, Permissions, and File Sharing

One of the primary concerns users will have while storing their files in the cloud is the privacy and security of their data, especially since in our system users can share their files with other users or even make them public to share them with everyone. To handle this, we will be storing the permissions of each file in our metadata DB to reflect what files are visible or modifiable by any user.