

Asynchronous execution refers to programs that can execute out of order and not require the main running thread to stop while waiting for execution. This is especially useful in Single Threaded languages like JavaScript and Python on IO bound tasks. Asynchronous tasks are taken off the main execution thread and put on a secondary stack, in a process from the [Event Loop](Event%20Loop.md). This allows you to execute many different tasks




This is especially important in Web tasks, that usually take a long to execute API fetches. If all web code was synchronous, the screen would freeze while you wait for the [HTTP](HTTP.md) request to make it's complete round trip.

![](Pasted%20image%2020220414000656.png)