

Hash maps are an extremely powerful data structure that allow for **constant time lookup and access**. This is done with the underlying concept of the data structure, the Hash Function.

Simply put, each key is fed through the hash function, and reveals the location of the corresponding value to the key. It can be as simple as taking the modulo of the key number, and then that modulo value is the index of the value. Things can be much more complex than that but that is the underlying idea. Feeding key-value pairs through a Hash Function that will let you get the location in memory with a single calculation.

This data structure is so powerful, hash maps are commonly combined with other algorithms as a frequency counter, result store, or anything that benefits from constant time access.

## Dealing with Collisions

Collisions are when the Hash function puts value in the same location as another value. So if the keys 'Apple' and 'Banana" both get hash values for the array index of 1. There are two general ways of dealing with this problem.

### Chaining

Chaining is when collisions are added as nodes of a [Linked List](Linked%20Lists.md) to the location in memory. So wherever multiple keys point to the same place, the yare placed in a linked list data Structure.  

### Open Addressing

The more popular way to handle collisions is with open addressing, finding a new location to put the data when there is a collision. This can be as simple as computing the Hash Function again and adding a bias like +3, multiplying by 3 or something else. This linear checking of open memory locations is called **linear probing**.  


Example of a Hash Map using chaining to deal with collisions.
![](Pasted%20image%2020220413231644.png)


## Time Complexity

|Function | Complexity|
|--- | ---|
|Access | $O(1)$|
|Search | $O(1)$|
|Insertion | $O(1)$|
|Deletion | $O(1)$|

Note: in poorly designed Hash Maps with many collisions, access and search can approach $O(n)$, but this can be avoiding with properly handling collisions.


### Implementation

The Python built-in dictionary data structure is a great hash map. Here is an example of a simple hash table with 10 slots, it uses modulo of the numeric key to determine the location of the value.

```python
TABLE_SIZE = 10
hashTable = [[],] * TABLE_SIZE

def checkPrime(n):
    if n == 1 or n == 0:
        return 0
    for i in range(2, n//2):
        if n % i == 0:
            return 0
    return 1

def getPrime(n):
    if n % 2 == 0:
        n += 1 # turn even to odd
    while not checkPrime(n):
        n += 2 # cycle through odds until you get prime
    return n

def hashFunction(key):
    capacity = getPrime(TABLE_SIZE)
    return key % capacity

def insertData(key, data):
    index = hashFunction(key)
    hashTable[index] = [key, data]

def removeData(key):
    index = hashFunction(key)
    hashTable[index] = 0
```
