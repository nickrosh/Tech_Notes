


Trees are a non-linear data structure that consists of a cascading number of nodes and child nodes. The head node is called the **root** and its subsequent nodes are its **children**. When a node doesn't have any children, it is referred to as a **lead node**. Trees are a special case of [Graphs](Graphs.md) which are acyclic and sometimes directed.
![](../../Attachments/Pasted%20image%2020220412234227.png)

### Binary Search Trees

When we talk about trees, we are almost always talking about binary search trees, where every node can have one or two children. The left child value will be lower and the right child value will be higher. Insertion then just becomes continually comparing node values and traversing down until you find the right location.


### Balanced Trees

When a tree has an equal number of nodes on it's left and right subtrees, we call it balanced. When the tree is unbalanced, it actually impacts the time complexity performance of search and insertion functions. When a tree is balanced the time complexity to search/insert is **$O(log n)$** which comes from $O(H)$ where H is the height of the tree. When a tree is unbalanced, the worst case scenario is that you have to traverse every single node, so the complexity becomes $O(n)$.

There are multiple methods to design trees that balance themselves when there is an imbalance. This includes **AVL Trees** and **Red-Black Trees**. Also, [B-Trees](../../Databases/Underlying%20DB%20Data%20Structures.md) are used as the underlying data structure of most databases.

### Tree Traversal

There are four ways to traverse a tree, using either [Depth First Search](../Algorithms/Depth%20First%20Search.md) or [Breadth First Search](../Algorithms/Breadth%20First%20Search.md). 

![](../../Attachments/Pasted%20image%2020220412235157.png)

1. DFS Postorder: Left Child -> Right Child -> Root
2. DFS Preorder: Root -> Left Child -> Right Child
3. DFS Inorder: Left Child -> Root -> Right Child
4. BFS Level Order:  Traverse level by level, left to right

### Time Complexity (Balanced Tree)

|Function | Complexity|
|---- | ---|
| Access | $O(log n)$ |
| Search | $O(log n)$ |
| Insert | $O(log n)$ |
| Delete | $O(log n)$ |