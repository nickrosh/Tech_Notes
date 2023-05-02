
Distributed System Design is about tradeoffs. There are many factors to consider when designing a system. The classic rule of [CAP Theorem](CAP%20Theorem.md) speaks on availability versus consistency, but there are other system requirements that would need to be spelled out when designing a system.

## System Objectives

#### Availability

System availability in the context of system design can be used to define *Service Level Objectives*. The uptime is measured as a percent, almost always nines i.e. 99.9%, 99.99%, etc. We can say the SLO (objective) of our system is 99.99%. The *Service Level Agreement* also shows up as the contractual requirement. So you can have a SLO of 99.999% but a SLA of 99.9% that you would reimburse if the availability ever goes under the SLA.

#### Reliability, Fault Tolerance, Redundancy

Reliability is the probability that the system won't fail. By using [DB Replication](../Databases/DB%20Replication.md), we can increase the reliability of the system. This is also known as fault tolerance, which means the system is tolerance to [Distribution Problems](Distribution%20Problems.md) and network issues. Redundancy is similar and refers to having more resources than necessary in order to provide more reliability and a backup copy in the event of a failure.

#### Throughput

*Throughput* refers to amount of operations or data the system can handle at once. The throughput of a web server would be how many [HTTP](../Internet%20&%20Networking/HTTP.md) requests it can handle per second. This throughput can be scaled with creating more servers and spreading the load with a [Load Balancer](Load%20Balancer.md). A database throughput is usually measured in queries per second (QPS). A third measurement that comes up is  bytes per second, or any other unit of data per second. User interactions would be better measured with requests per second, but if we were looking at a data pipeline, then data per second might be more suitable.

#### Latency

*Latency* is just the amount of time it takes for an operation to complete, the end to end latency of the system. This is a combination of network travel time and system computation time. Reducing network latency is a primary motivator for [CDNs](CDN.md).


