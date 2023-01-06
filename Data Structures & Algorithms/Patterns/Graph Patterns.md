
## Multi-Source BFS

When there are multiple sources to [Breadth First Search](Breadth%20First%20Search.md) from, like in a matrix, you can just load all of the sources in the BFS queue and iterate through the [Queue](Queues.md) like normal.


## Cycles in Directed Graphs

Cycles sometimes mean it is impossible to complete a certain computation like in LC 207 - Course Schedule. Simply create a new data structure to store which nodes have already been stored, usually a set. Then as you are traversing the graph, if you reach a node that is already in the Visited set, you know the graph has a cycle.

## Shortest Path Algorithm

When you have a weighted graph and you need to find the cost of going from point A to B, you can use Djikstra's. It is simply [Breadth First Search](Breadth%20First%20Search.md) with a [Heap(Priority Queue)](Heaps.md) instead of a regular [Queue](Queues.md) to store which nodes the visit. By always visiting the closest node, you will get to the destination in the fastest time.