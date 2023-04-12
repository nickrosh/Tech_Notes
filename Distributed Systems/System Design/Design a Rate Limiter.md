
Imagine we have a system which is receiving a huge number of [requests](../../Internet%20&%20Networking/HTTP.md), but it can only serve a limited number of requests per second. To handle this problem, we need a throttling or rate limiting mechanism that would allow only a certain number of requests so our service can respond to all of them. A rate limiter, at a high-level, limits the number of events an entity (user, device, [IP](../../Internet%20&%20Networking/IP.md), etc.) can perform in a particular time window. For example:
- A user can only send one message per second
- A user is only allowed three failed credit card transactions per day
- A single IP can only create 20 accounts per day

Rate limiting helps to protect services against DDOS attacks, brute force password attempts, brute force credit card attempts, etc. Rate limiting is also used to limit revenue loss, to reduce infrastructure costs, etc. The following is a list of scenarios that can benefit from rate limiting:
- Misbehaving client/scripts: either intentionally or unintentionally, some entities can overwhelm a service with a huge number of requests
- Security: By limiting the number of the second-factor attempts (in 2 factor authentication)
- Preventing bad design: without API limits, client developers would use sloppy development tactics, not caching results and requesting duplicate information repeatedly
- To keep costs and resource usage under control: services are generally designed for normal input behavior
- Revenue: certain services may want to segment tier's of product to clients by limiting requests per purchased tier

## Requirements

**Functional Requirements:**
1. Limit the number of requests and entity can send to an [API](../../Internet%20&%20Networking/REST%20API.md) within a time window, e.g. 15 per second.
2. The APIs are accessible through a cluster, so the rate limit should be considered across different servers. The user should get an error message whenever the defined threshold is crossed within a single server or across a combination of servers.

**Non-functional Requirements**
1. The system should be highly [available](Design%20Requirements.md). The rate limiter should always be up as it protects from external attacks.
2. The rate limiter should not introduce substantial latencies affecting the user experience.


## Rate Limiting Definitions

Rate limiting is a process that is used to define the rate and speed at which consumers can access APIs. Throttling is the process of controlling the usage of the APIs by customers. When the throttle limit is crossed, the server returns [HTTP](../../Internet%20&%20Networking/HTTP.md) status "429 - Too many requests".

#### Different Type of Throttling

- **Hard Throttling**: The number of requests cannot exceed the throttle limit
- **Soft Throttling**: In this type, we can set the API request limit to exceed a certain percentage. For example, if we have a rate limit of 100 messages a minute and a 10% exceed limit, our rate limiter would allow up to 110 messages a minute.
- **Elastic or Dynamic Throttling**: Under elastic throttling, the number of requests can go beyond the threshold if the system has some resources available. For example, if a user is allowed only 100 messages a minute, we can let the user send more than 100 messages a minute when there are free resources available in the system.

#### Different Algorithms used for Rate Limiting

![](../../Attachments/Pasted%20image%2020230407010233.png)

There are two types of algorithms used for rate limiting:

- **Fixed Window Algorithm**: In this algorithm, the time window is considered from fixed timestamps, e.g. exactly 11:01, 11:02, etc. This is easy to implement as you simply have to look at the timestamp of the request, but this could also have problems. You could have a huge spike of requests right before the minute mark, and then another spike right after the minute mark, having effectively two times the rate limit in the matter of seconds
- **Sliding/Rolling Window Algorithm**: In this algorithm, the time window starts when the request is first received and then taken until the time limit of the window. 


## High Level Design

