
Suppose we were given a range of values. Then, given a left and a right pointer that defines the range, we want to be able to calculate the sum of the range. This is the motivation for *Segment Trees* which allow you to calculate intervals in $O(\log{n})$ [Time Complexity](../Time%20&%20Space%20Complexity.md). Not too dissimilar to [Merge Sort](../Algorithms/Merge%20Sort.md), the idea here is to break up an [Array](Arrays.md) into segments, by a branching factor of two, and have each node represent a progressively smaller range. We break up the array into two equal halves, and each node represents a range (which are basically indices of arrays). The below image shows how a segment tree is constructed using an array. The numbers in red represent the sum of the range at that given node. The numbers inside the node represent the range itself, inclusive of both numbers. The text in blue represents the formula for calculating the range.

![](../../Attachments/Pasted%20image%2020230331163750.png)

#### Building the Tree

Given an array `nums` and `L` and `R`, we can take a recursive approach. At each level, we calculate the midpoint, which splits the current range in half and builds the tree until we hit our base case, where `L == R`, i.e. the range gets exhausted.

```python
@staticmethod
def build(nums, L, R):
    if L == R:
        return SegmentTree(nums[L], L, R)

    M = (L + R) // 2
    root = SegmentTree(0, L, R)
    root.left = SegmentTree.build(nums, L, M)
    root.right = SegmentTree.build(nums, M + 1, R)
    root.sum = root.left.sum + root.right.sum
    return root
```

#### Update

If we want to update a node sum, we can take in the index that we want to update and the value we want to update it with. The implementation is not too dissimilar tot the `build()` function. Our base case is if we reach a leaf node, we have found our index and we can update the sum to the new value. Then we will calculate our midpoint value. If it is smaller than the index, we will recursively go down the right subtree and if it is greater than the index, we will recursively go down  the left subtree.

```python
def update(self, index, val):
    if self.L == self.R:
        self.sum = val
        return

    M = (self.L + self.R) // 2
    if index > M:
        self.right.update(index, val)
    else:
        self.left.update(index, val)
    self.sum = self.left.sum + self.right.sum
```

#### Range Query

This is the most important operation of a segment tree. If we are given a range that requires us to traverse the tree, we will follow the same recursive procedure. We will recurse down the tree, and calculate the midpoint. Given  `L` and `R`, if `L > M`, our range query lies on the right. If `R <= M`, our range lies on the left. And, this makes sense because when we calculated `M` in the `build()` function, the left sub-tree had indices `L,M` and the right sub-tree has indices `M+1, R`:

```python
def rangeQuery(self, L, R):
    if L == self.L and R == self.R:
        return self.sum

    M = (self.L + self.R) // 2
    if L > M:
        return self.right.rangeQuery(L, R)
    elif R <= M:
        return self.left.rangeQuery(L, R)
    else:
        return (self.left.rangeQuery(L, M) +
                self.right.rangeQuery(M + 1, R))
```


## Complexity

The time complexity of the three functions is as follows, where *n* is the number of nodes the tree contains:

| Function        | Complexity   |
| --------------- | ------------ |
| `build()`       | $O(n)$       |
| `update()`      | $O(\log{n})$ |
| `range_query()` | $O(\log{n})$             |
