

  A Heap is a complete [Binary Tree](Trees.md) that ensures the max or min value can always be peeked easily. A Heap can either be a **Max Heap** which ensures the root is always the maximum value in the data structure (like the pic below) or a **Min Heap** where the root is always the minimum value in the data structure. For a **Max Heap**, it must simply satisfy the following Heap requirement at all times (for min heap it is reversed)
  
  **For every node X with parent  P,   P <= X**



![](Pasted%20image%2020220413001316.png)

In fact, we represent the heap as an array, as the tree will always be complete, with new nodes always adding to the first available open spot.

![](Pasted%20image%2020220413001437.png)

When you insert a new value, you append it to the end of the array and then _heapify_ it.

Percolate Up:
1. Compare vs. the Parent, if Current > Parent, then exchange the current and the parent
2. Set the current index to parent index
3. repeal until the current index is the root


Delete by removing the Root, like pop from a [Stack](Stacks.md), set the last value in the array as the root, and then _heapify_ it.

Percolate Down:
1. Compare vs. Both children, if the children < parent, then exchange the value with the lower value child node
2. set current index to min_child
3. repeat until current index is a leaf node.


### Heap Traversal

You can "traverse" the array with 3 simple algorithms.  From any arbitrary index:

- To get to the **Parent** = (index - 2) // 2
- To get to the **Left Child** = Index * 2 + 1
- To get to the **Right Child** = Index * 2 + 2

Note: if any of these functions returns an out-of-bounds value, then that node doesn't exist in the Heap.


### Time Complexity

|Function | Complexity|
|---- | ---|
| Peek | $O(1)$ |
| Insert | $O(log n)$ |
| Delete | $O(log n)$ |
| Heapify | $O(n)$ |
| HeapSort | $O(n log n)$ |
