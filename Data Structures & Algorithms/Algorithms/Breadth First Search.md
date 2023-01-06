

Breadth first search is a graph and tree search algorithm that searches level-by-level away from the search point of origin. The algorithm works as follows

1. Start at arbitrary root node
2. Scan all adjacent node to the root and add them to a queue
3. Search each item in the queue, for each item, scan for its own adjacent nodes and add those to the end of the queue
4. continue this process of searching nodes and adding their unsearched neighbors to the queue until the queue is empty



![](Pasted%20image%2020220413232230.png)


### Time Complexity

|Time | Space|
|--- | ---|
|$O(V + E)$ | $O(V)$|

Where V = number of Vertices, E = number of Edges


### Implementation

```python
import collections

# BFS algorithm
def bfs(graph, root):

    visited, queue = set(), collections.deque([root])
    visited.add(root)

    while queue:

        # Dequeue a vertex from queue
        vertex = queue.popleft()
        print(str(vertex) + " ", end="")

        # If not visited, mark it as visited, and
        # enqueue it
        for neighbour in graph[vertex]:
            if neighbour not in visited:
                visited.add(neighbour)
                queue.append(neighbour)
```