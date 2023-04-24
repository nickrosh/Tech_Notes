
*Topological Sort* is a way of sorting a [Directed Acyclic Graph](../Data%20Structures/Graphs.md) (DAG) such that each node comes before its dependent nodes. A simple example of this is university courses. There are some courses that can be taken without any pre-requisites and then there are those that have pre-requisites. This ordering is called the *Topological Ordering*.  The graph below has two connected components and one possible valid ordering could be `[A,B,C,D,E,F,G,H]`:

![](../../Attachments/Pasted%20image%2020230410191014.png)

## Implementation

The algorithm for creating the topological ordering can be done with either [Breadth First Search](Breadth%20First%20Search.md) or [Depth First Search](Depth%20First%20Search.md). This is the most straightforward method with DFS. We run a [post-order traversal](../Data%20Structures/Trees.md) which will first return all descendent nodes before returning the ancestors. Remember that post-order traversal is: `left, right, root`.

![](../../Attachments/Pasted%20image%2020230415125502.png)

The order in which you traverse nodes does not matter either. You can iterate through every node and run the post-order traversal. We can keep track of a `visited` set to let us know when we have already processed a node. Then we can easily DFS the entire list of nodes.

```python
# Given a directed acyclical graph, return a valid
# topological ordering of the graph.
def topologicalSort(edges, n):
    adj = {}
    for i in range(1, n + 1):
        adj[i] = []
    for src, dst in edges:
        adj[src].append(dst)
    topSort = []
    visit = set()
    for i in range(1, n + 1):
        dfs(i, adj, visit, topSort)
    topSort.reverse()
    return topSort

def dfs(src, adj, visit, topSort):
    if src in visit:
        return
    visit.add(src)
    for neighbor in adj[src]:
        dfs(neighbor, adj, visit, topSort)
    topSort.append(src)
```

#### Cyclic Path

What if we were not guaranteed that the graph was acyclic? We can declare another [Hash set](../Data%20Structures/Hash%20Maps.md) `path`, which will keep track of the current path in our DFS. Just like `visit`, we will add nodes to our path. Then, once we pop from the recursion stack of a path in the graph, we will also pop from our `path`. If we visit the same node along a path twice, we can return false since this means our graph has a cycle.

```python
# Given a directed acyclical graph, return a valid
# topological ordering of the graph.
def topologicalSort(edges, n):
    adj = {}
    for i in range(1, n + 1):
        adj[i] = []
    for src, dst in edges:
        adj[src].append(dst)
    topSort = []
    path = set()
    visit = set()
    for i in range(1, n + 1):
        res = dfs(i, adj, visit, path, topSort)
        if not res: # If the node is in the current path, then there is no ordering
            return []
    topSort.reverse()
    return topSort

def dfs(src, adj, visit, path, topSort):
    if src in path:
        return False
    if src in visit:
        return True
    visit.add(src)
    path.add(src)
    for neighbor in adj[src]:
        dfs(neighbor, adj, visit, path, topSort)
    path.remove(src)
    topSort.append(src)
    return True
```
