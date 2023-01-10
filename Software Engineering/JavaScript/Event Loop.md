

The JavaScript event loop allows the [Asynchronous Execution](Asynchronous%20Execution.md) of tasks. It operates on a few principles

![](Pasted%20image%2020220414004459.png)

Generally, the event loop works as follows:

1. Functions are executed on the Call [Stack](The%20Stack%20&%20The%20Heap.md)
2. When an asynchronous task like a Web API is called, it is asynchronously executed off the main thread. When the IO task is done, its result is placed in the Callback [Queue](Queues.md).
3. When the Call Stack has completed its current execution and is empty, it adds the next function from the Callback Queue
4. It executed those functions, and then when it is complete, it repeats step 3 until every task has been completed in the Callback Queue.

Under this method of processing, long running tasks requiring network calls can be executed off the main thread to prevent blocking and pauses in user interface.