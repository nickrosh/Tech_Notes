
The *Union-Find* Data Structure is a useful tool for keeping track of nodes connected in a [Graph](Graphs.md) and detecting cycles. Union-Find operates on *Disjoint Sets*, which are sets that don't have any elements in common. Formally two disjoint sets are sets whose intersection is the empty set.  Union-Find is referred to as a "forest of [Trees](Trees.md)". Initially each vertex stands by itself, and for each vertex, we want to store the pointer to its parent. Since we have not connected them yet, each node is a parent to itself:

![](../../Attachments/Pasted%20image%2020230331162345.png)

Union-Find has two operations. The *Find* operation takes in a node and returns its parent. The *Union* operation takes two vertices and determines if a union can be performed. If the two vertices share the same parent, a union cannot be formed and we can return false. If the two vertices have different parents, we merge them so that they are pointing to the same parent. The tree with the higher rank will become the parent for optimal performance.

![](../../Attachments/Pasted%20image%2020230331162601.png)

The following is the implementation. Note that the find function performs path compression, which is setting the nodes parent to its grandparent, which improves complexity over time. Also the Union function does merging by rank:

```python
class UnionFind:
    def __init__(self, n):
        self.par = {}
        self.rank = {}

        for i in range(1, n + 1):
            self.par[i] = i
            self.rank[i] = 0
    
    # Find parent of n, with path compression.
    def find(self, n):
        p = self.par[n]
        while p != self.par[p]:
            self.par[p] = self.par[self.par[p]]
            p = self.par[p]
        return p

    # Union by height / rank.
    # Return false if already connected, true otherwise.
    def union(self, n1, n2):
        p1, p2 = self.find(n1), self.find(n2)
        if p1 == p2:
            return False
        
        if self.rank[p1] > self.rank[p2]:
            self.par[p2] = p1
        elif self.rank[p1] < self.rank[p2]:
            self.par[p1] = p2
        else:
            self.par[p1] = p2
            self.rank[p2] += 1
        return True
```

## Complexity

In the naive case, the find function will result in $O(n)$ [Time Complexity](../Time%20&%20Space%20Complexity.md) because it is possible that the tree is just a chain like a linked list and we have to traverse every node. With path compression we can reduce the complexity down to $O(\log{n})$. If we combine it with the union function, we get a time complexity called the *Inverse Ackerman*, $\alpha (n)$, which can be simplified to constant time $O(1)$. So, if *m* is the number of edges we have, then the time complexity of Union-Find is $O(m\log{n})$.