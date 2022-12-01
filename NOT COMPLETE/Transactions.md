

A *Transaction* is a single indivisible unit of work in a Database. ACID ensures reliability that is key with important transactions like Bank transfers, imagine if a fault network caused your bank withdrawal to happen twice. An ACID transaction obeys the following:


### Atomic
All changes to the data must be performed successfully or not at all.


### Consistent
Data must be in a valid state before and after the transaction.


### Isolated
No other process can change the data while the transaction is running


### Durable
The changes made by a transaction must persist.