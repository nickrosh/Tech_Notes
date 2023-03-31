

Depth First Search (DFS) is a graph and tree search algorithm that recursively goes down continuously until it reaches a stop condition, then backtracks to the next node search. This recursive function call stack is done as an implicit Stack. New nodes are added to the call stack, and then when they are read and completed they are removed from the top of the stack and new nodes are added


The algorithm works as follows:

1. start at one arbitrary node we call root
2. Search one neighbor node
3. Continually searching one unsearched neighbor node until you reach a node with no unsearched neighbors
4. backtrack to a node with unsearched neighbors and continue the previous steps.


You can see in the example, starting at Node _'S'_ the search goes until it reaches node _'B'_ which has no adjacent unsearched nodes, so it backtracks to _'G'_ and then goes down a new path until it finishes searching the entire graph.

![](../../Attachments/Pasted%20image%2020220413232525.png)


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


## Iterative DFS

Regular Recursive DFS is trivial. A more complex question is being asked to do DFS [Tree](../Data%20Structures/Trees.md) traversal iteratively. To do this, we must create an explicit [Stack](../Data%20Structures/Stacks.md) to function like the call stack created during recursion.

#### Iterative Inorder Traversal

With inorder traversal, we simply need to traverse downwards and to the left and append to the stack. Whenever we can't go left, we pop from the stack and keep on going until the stack is empty:
```python
def inorder(root):
    stack = []
    curr = root

    while curr or stack:
        if curr:
            stack.append(curr)
            curr = curr.left
        else:
            curr = stack.pop()
            print(curr.val)
            curr = curr.right
```

#### Iterative Preorder Traversal

Preorder is a little more complicated. In this traversal, as we traverse to the left, we check if there is a right child and append that to the stack. We also add the current node to the result first:
```python
def preorder(root):
    stack = []
    curr = root
    while curr or stack:
        if curr:
            print(curr.val)
            if curr.right:
                stack.append(curr.right)
            curr = curr.left
        else:
            curr = stack.pop()
```

#### Iterative Postorder Traversal

Postorder is more complicated than the previous two. We will be making use of two stacks. We will have a `visit` stack as well as the regular `stack`. The visit stack will always be the same size as the `stack` stack. Whenever we visit a node, we add it to the `stack` and add False to `visited`. Then when we need to pop, we take the popped node and add it back except this time we add True to `visited`, as well as adding the right child then the left child with both `visited=False`. If we pop a visited node we just add it to our result:
```python
def postorder(root):
    stack = [root]
    visit = [False]
    while stack:
        curr, visited = stack.pop(), visit.pop()
        if curr:
            if visited:
                print(curr.val)
            else:
                stack.append(curr)
                visit.append(True)
                stack.append(curr.right)
                visit.append(False)
                stack.append(curr.left)
                visit.append(False)

```

## Time Complexity

|Time | Space|
|--- | ---|
|$O(V + E)$ | $O(V)$|

Where V = number of Vertices, E = number of Edges