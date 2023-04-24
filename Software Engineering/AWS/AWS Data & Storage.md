
# Data

## DynamoDB

*DynamoDB* is a [NoSQL](../../Databases/NoSQL.md) managed database. It can automatically scale with your read/write load and is highly [durable and available](../../Distributed%20Systems/Consistency%20and%20Consensus.md). Also all hardware scaling and [DB Partitions](../../Databases/DB%20Partitions.md) and [DB Replication](../../Databases/DB%20Replication.md) is taken care of under the hood. For this reason, dynamo is usually the preferred option for databases.

## RDS

*Relational Database Service* is the classic managed [Relational Database](../../Databases/Relational%20Databases.md) offered by AWS. This is essentially like installing Postgres or MySQL on an [EC2](AWS%20Compute.md) instance, except all the configuration and management overhead is taken care of. It looks a lot like classic database solutions and is a quick swap in for on-premise databases. You can have a maximum of 5 read replicas to increase read [throughput](../../Distributed%20Systems/System%20Design/Design%20Requirements.md).

#### Aurora

*Aurora* is a relational database service similar to RDS, except it is a higher level API. Aurora provides better performance than classic Postgres or MySQL  because it is split among specialty purpose machines. The data is stored on one type of machine and the compute is stored on another, compared to classic databases where everything is on the same node. This backend is not visible to the user: you only see the same database engine with better performance. With Aurora, you can have a maximum of 15 read replicas, replication across three Availability Zones, point-in-time recovery, and continuous backups to S3. 

## Elasticache

*Elasticache* is the AWS managed [Database Cache](../../Databases/Database%20Cache.md) service. It can be configured for use either Redis or Memcached. This is a managed cache that allows sub-millisecond latency responses to queries. Backups, failure recovery, monitoring, setup, etc. is taken care of by the service. Elasticache is also suitable for session storage, real-time analytics, and other tasks.

# Storage

## S3

*AWS S3* is the [Object Storage](../../Databases/Object%20Storage.md) service. It is one of the most popular services used. Files are uploaded to *buckets* and optional folders inside buckets. Every single file in S3 is globally unique, thus every bucket must be globally unique. The buckets and folders themselves are merely abstractions. In object storage, there is no folders, they are just there to prefix the file name to make it unique, and to help with organization. You simply upload objects to the bucket of choice and make sure the complete path name of the file is unique.

You can also allow unauthorized users  (with no AWS credentials) to Read and Write to S3 with presigned URLs. You set up [API Gateway](AWS%20Networking.md) to deal with the request itself and then return a presigned URL that the client can then use to access S3. This gives a safe way of letting unauthorized users to directly interact with the S3 [API](../../Internet%20&%20Networking/REST%20API.md). Presigned URLs also come with an expiration date and time.

#### S3 Glacier

There is also *glacier*, which is meant to be a data archive and long-term backup version of S3. This is offered with extreme durability and slow retrieval guarantees. The standard retrieval time is 3-4 hours, with the cheapest option being 5-12 hours.


## EBS

*Elastic Block Storage* is the generic long-term block storage for [EC2](AWS%20Compute.md) instances. It's designed for both throughput and transactional workloads and can scale to petabytes of data. You can choose different storage types with various throughput and latency suitable for your needs. The EBS replicates volumes within the Availability Zone, and you can use EBS snapshots to backup your volumes to S3.

## EFS

*Elastic File System* is a fully managed scalable NFS (Network File System). It grows and shrinks automatically, eliminating the need to provision and manually manage capacity. EFS is designed to provide massively parallel shared access to thousands of EC2 instances with a high level of aggregate throughput and IOPs, maintaining consistent latency. EFS should be used whenever you need a shared file storage option for multiple EC2 instances with automatic scaling. This makes it a great candidate for file storage for content management systems.