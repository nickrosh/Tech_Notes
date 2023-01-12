
## Fundamental Algorithm for Trees

```python
def traverse(root):
	# Pre-order Traversal
	traverse(root.left)
	# In-order Traversal
	traverse(root.right)
	# Post-order Traversal
```

Almost every single algorithm about Trees, and really most data structures follow the above template. Every single [Depth First Search](../Algorithms/Depth%20First%20Search.md) question is just this with one or two changes to make it unique. You can look at [Graphs](../Data%20Structures/Graphs.md) as just a Tree with N-neighbors. Thus this gets even further reduced when talking about graphs with N connections

```python
def traverse(root):
	for neighbor in root.neighbors:
		traverse(neighbor)
```

When you look at things fundamentally, Trees  are the foundation of most algorithm questions. Graphs are just Trees with N branches. Dynamic Programming and Greedy Algorithms are just Tree questions that are looking to optimize a node or path or something. Backtracking is just gathering all nodes in a Tree that fulfill a condition.


## Invert a Binary Tree

You can invert a Binary Tree by simply swapping the left and right children in preorder traversal

```python
def invert_tree(root):
	root.left, root.right = root.right, root.left
	invert_tree(root.left)
	invert_tree(root.right)
	return root
```


## Tree Height

We can define the height of a node as the maximum height of it's left and right subtrees in post-order traversal

```python
def dfs(node):
	if not node:
		return -1
	left, right = dfs(node.left), dfs(node.right)
	return 1 + max(left, right)
```


## Order-Level Traversal

The tree equivalent of [Breadth First Search](../Algorithms/Breadth%20First%20Search.md) is to go through each level of the tree left to right iteratively. This is very useful in problems that require something special or a particular node from each level of the tree. 

```python
def level_order(root):
	result = []
	queue = collections.deque()
	queue.append(root)
	
	while queue:
		queue_length = len(queue)
		level = []
		for i in range(queue_length):
			node = queue.popleft()
			if node:
				level.append(node.val)
				queue.append(node.left)
				queue.append(node.right)
		if level:
			result.append(level)
```

This is a simple level-order traversal that returns a list of levels.
