
The Graph data structure is a collection of nodes and edges. A [Tree](Trees.md) is simply a special case of a graph.

![](../../Attachments/Pasted%20image%2020220413003926.png)


## Types of Edges

Directed Graphs only allow you to traverse through edges in the direction that is designated. Undirected graphs allow you to traverse the graph in any direction. There are also weighted graphs which have a value or cost associated with every edge. Specialty algorithms like Djikstra's and Prim's deal with weighted graphs

![](../../Attachments/Pasted%20image%2020220413004721.png)

Directed Graphs can also be described as **Acyclic** or **Cyclic**, which defines a graph that has a directed loop.

![](../../Attachments/Pasted%20image%2020220413005020.png)


A Directed Acyclic Graph (DAG) is commonly used in data processing to define a series of steps of data extraction and transformation, that ensures all data reaches the end due to the acyclic nature.


## Matrix

Quite often, graphs are represented as matrices. You can be given a n x m matrix and asked to perform an action. Most of the time, you can consider matrices as connected on four sides: above, below, left, and right. indexes beyond the boundary are not considered. Sometimes you can also traverse in 8 directions, including diagonal. Some values of the matrix can designate a path, and other values can designate a block in the path. In the below example, the algorithm can only traverse in "0" valued elements.

![](../../Attachments/Pasted%20image%2020230407142349.png)



## Adjacency List

The easiest way to define a graph in code is with an **Adjacency List** where you define each node by a corresponding list of edges.

![](../../Attachments/Pasted%20image%2020220413005220.png)

```python
graph = {'0': ['0', '1', '2', '3'],
         '1': ['0', '2'],
         '2': ['0', '1'],
         '3': ['0']
         }
```