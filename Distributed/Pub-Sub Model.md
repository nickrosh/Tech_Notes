

The request-response model of a [REST API](REST%20API.md) is simple and powerful, but breaks down when there are multiple receivers. Additionally request-response creates tight coupling, which introduces problems whenever something like the schema is changed for example.

Pub-Sub is a system that uses [Message Queues](Message%20Queue.md) to asynchronously deliver a message from the client or _publisher_ to one or more consumer or _subscriber_. A message is sent to a topic/channel/queue (different brokers have different names). Every message is loaded into a message queue for every single subscriber, so all subscribers will see every message.

This scales very well with multiple receivers, and is used heavily in microservices applications. Additionally it is a loosely coupled system that works when the client isn't even running.

On the downside there is the byzantine generals problems where it is very difficult to know whether another system has actually consumed the message. It is complex to implement, and causes network saturation for the message brokers that send messages via long polling

![](Pasted%20image%2020220415202355.png)


### Message Brokers

Brokers refer to the middleman software that handles this messaging system. Different message brokers deal with this problem differently, but the two most common programs are:

**RabbitMQ** : the channel pushes messages to subscribers. This is used for intermittent, larger messages.

**Kafka** : Subscribers continuously poll the queue, waiting for new messages. This is used for streaming data