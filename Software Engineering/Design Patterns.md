

Some of the more common [Object Oriented Programming](C++/C++%20OOP.md) design patterns from the *Gang Of Four* book:

| Pattern | Description |
|---|---|
| Singleton | A class that only allows one instance to exist. This is often used in thread pools, logging, caching, and driver objects. Great for managing an expensive shard resources, like DB, or declaring a global state
| Factory | Lets you create an object without calling the class itself. You can add extra logic or declare different classes depending on the input |
| Decorator | Attaches new behavior to the objects without modifying their structure. This lets you add functionality without creating a subclass |
| Adapter | Allows incompatible classes to work with each other through an interface. This is like a language translator. |
| Observer | Notifies about events happening in other objects without coupling their classes. Like setting up subscribers in a [Pub-Sub Model](../Distributed%20Systems/Pub-Sub%20Model.md) |
| Iterator | Provide a clean way of traversing the collections in a class without the client needing to know its logic |
| State | helps an object alter its behavior in case its internal state changes. Useful when there is an enormous number of object states, and massive conditionals |
| Façade | Provide a simplified interface of an application to decrease complexity. Useful when working with complex libraries/APIs, or if you only need part of the functionality |
