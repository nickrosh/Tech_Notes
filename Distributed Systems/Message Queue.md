

Message Queues are a design pattern that lets a publisher of any arbitrary message to asynchronously have the messages consumed by one or more subscribers. producers send message to a [Queue](../Data%20Structures%20&%20Algorithms/Data%20Structures/Queues.md) which then sends the message to a consumer. Message Queues are primarily used in [Stream Processing](Stream%20Processing.md) to handle asynchronous messages acting as either a load balancer with each message being consumed once, or in fan-out where each message is sent to every single consumer.

![](../Attachments/Pasted%20image%2020220415201844.png)

There are additional features that are commonly found in MQs that allow for greater durability. For example, in a pull system where the consumers must poll the queue for a message, the queue will not remove a message from its queue until the consumer has sent an acknowledgement message that it had successfully processed the message.