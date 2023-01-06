

Depth First Search (DFS) is a graph and tree search algorithm that recursively goes down continuously until it reaches a stop condition, then backtracks to the next node search. This recursive function call stack is done as an implicit Stack. New nodes are added to the call stack, and then when they are read and completed they are removed from the top of the stack and new nodes are added


The algorithm works as follows:

1. start at one arbitrary node we call root
2. Search one neighbor node
3. Continually searching one unsearched neighbor node until you reach a node with no unsearched neighbors
4. backtrack to a node with unsearched neighbors and continue the previous steps.


You can see in the example, starting at Node _'S'_ the search goes until it reaches node _'B'_ which has no adjacent unsearched nodes, so it backtracks to _'G'_ and then goes down a new path until it finishes searching the entire graph.

![](Pasted%20image%2020220413232525.png)

### Time Complexity

|Time | Space|
|--- | ---|
|$O(V + E)$ | $O(V)$|

Where V = number of Vertices, E = number of Edges


### Implementation

```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()
    visited.add(start)

    print(start)

    for next in graph[start] - visited:
        dfs(graph, next, visited)
    return visited
```
