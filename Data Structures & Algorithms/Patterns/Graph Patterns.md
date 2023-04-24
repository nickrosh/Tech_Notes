
## Matrix DFS

You can perform [Depth First Search](../Algorithms/Depth%20First%20Search.md) on [matrices](../Data%20Structures/Graphs.md). This is done by starting at any node, declaring the base case, and then calling the same DFS function to all neighbors, usually the four adjacent ones. This is very similar to [Backtracking](Backtracking%20Patterns.md) where you search through every single branch from the node. Except instead on a tree, this is on a matrix and thus in a n x m matrix, the [Time Complexity](../Time%20&%20Space%20Complexity.md) would be $O(4^{nm})$ (if you can only traverse in 4 directions). This is the algorithm to use when you want to do questions like "count the number of islands" or "max area of islands".

The common system for performing DFS in a matrix is to make a nested function that can be recursively called. Then calling its neighbors. Here is a generalized example:

```python
visited = set()
def dfs(row, column):
	if base_case or visited:
		return
	visited.add(graph[row][column])
	dfs(row + 1, column)
	dfs(row - 1, column)
	dfs(row, column + 1)
	dfs(row, column - 1)
```


## Matrix BFS

You can also perform [Breadth First Search](../Algorithms/Breadth%20First%20Search.md) on matrices. This is done by starting at a node, loading it into the queue, and then iteratively adding valid neighbors to the queue then processing them one at a time. This algorithm is better for pathfinding as it will won't go down every single branch to completion. When you have a question like "find the shortest path from top left to bottom right" in a matrix, you should use BFS. The time complexity would be $O(nm)$ which is better than the DFS complexity.


## Multi-Source BFS

When the matrix has multiple starting points that you must compute from, like "Rotting Oranges". You can just do [Breadth First Search](../Algorithms/Breadth%20First%20Search.md) and load every starting point into the [Queue](../Data%20Structures/Queues.md) and then proceed with BFS like normal.


## Cycles in Directed Graphs

Cycles sometimes mean it is impossible to complete a certain computation like in LC 207 - Course Schedule. Simply create a new data structure to store which nodes have already been stored, usually a set. Then as you are traversing the graph, if you reach a node that is already in the Visited set, you know the graph has a cycle.


## Djikstra's Algorithm

When you have a weighted graph and you need to find the cost of going from point A to B, you can use Djikstra's. It is simply [Breadth First Search](../Algorithms/Breadth%20First%20Search.md) with a [Heap(Priority Queue)](../Data%20Structures/Heaps.md) instead of a regular [Queue](../Data%20Structures/Queues.md) to store which nodes the visit. By always visiting the closest node, you will get to the destination in the fastest time. This is how to find the shortest path between two nodes with a weighted graph, by adding up the *total length* to each node and adding that to the heap. Then you, will get the actual minimum to each node in the graph.

Don't forget that when you load into a min heap, the value being minimized needs to go first. This is important in Djikstra's because you are usually loading multiple values into the min heap. The [Time Complexity](../Time%20&%20Space%20Complexity.md) is E times the log of E. As E is the same as V squared, this means the log of E is asymptotically the same as the log of V, thus the complexity is $O(E\log{V})$.

#### Bellman-Ford

Bellman-Ford is another shortest path algorithm similar to Djikstra's. You initiate a map of every node with an initial value of infinity, and the starting node itself to zero. This map represents the cost of going from the starting node to every other node. Begin [Breadth First Search](../Algorithms/Breadth%20First%20Search.md) at the starting node, until you exhaust all neighbors. For every neighbor in the BFS, compute the weight to go to that neighbor from the starting point you set and replace infinity with whatever value is found. When you have a path that is shorter than an existing one, you may replace it. The benefit to Bellman-Ford over Dijkstra is that it uses classic BFS and is thus computed in discrete steps. This works very well with questions like "Find Cheapest Price" where you can only use paths that are K maximum edges from the starting node.

## Minimum Spanning Tree

A tree is an Acyclic, Connected, Undirected Graph. With a weighted graph, a question that comes up is to get the minimum tree subset of a weighted graph that spans every node.

*Prim's Algorithm* is very similar to Djikstra's algorithm, except to find a minimum spanning tree. You start at an arbitrary node, and add it to a Min Heap as well as a *visited* hash set. You then add all of its neighbors to the Heap and iterate through the heap just like Djikstra's, except that instead of adding the total weight from the starting node, you just add the edge weight itself. Prim's is concerned with connections between nodes, not total path weight like Djikstra's. Continue to iterate through the heap until the number of visited nodes is the total number of nodes in the graph. The time complexity would be $O(E\log{V})$ just like Djikstra's

*Kruskal's Algorithm* is another minimum spanning tree algorithm. It makes use of [Union-Find](../Data%20Structures/Union-Find.md). You start by adding the minimum cost edge to the Union find, and the two nodes it touches. You then continue to iterate through every edge in ascending order, adding edges to the MST only when the Union Find operation is successful. If you are given an API to use a Union Find, this is pretty simple. If you have to code up Union Find, then it is probably easier to use Prim's in a [DS&A Coding Interview](../DS&A%20Coding%20Interviews.md).


## Alien Dictionary

Graphs excel when relationships between elements are important. In LC 269, you are asked to figure out the order of an alien alphabet by the order a list of words show up as. This problem can be turned into a [Topological Sort](../Algorithms/Topological%20Sort.md) problem by going through pairs of adjacent words and adding first differing letters as "edges" to then create a directed graph of which letters should show up before others. Once this is done, you generate an adjacency list from the edges and run the sort on it. Problems similar to this are like course schedule. The primary difficulty is recognizing the graph problem and converting the input into an adjacency list that you can perform graph algorithms on.