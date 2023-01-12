

Message Queues are a design pattern that lets a publisher of any arbitrary message to asynchronously have the messages consumed by one or more subscribers. producers send message to a [Queue](../Data%20Structures%20&%20Algorithms/Data%20Structures/Queues.md) which then sends the message to a consumer, you can have multiple consumers in which case the messages will be load balanced. This is used when you want each message to be processed once.

![](../Attachments/Pasted%20image%2020220415201844.png)