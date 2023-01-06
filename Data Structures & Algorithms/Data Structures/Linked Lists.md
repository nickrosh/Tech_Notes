

Linked Lists are Linear Data Structures that simply consists of Nodes containing data, connected with pointers to the next node in the list. The first node in the list is referred to as the HEAD. If each node only contains a reference to the next node, it is called a **Singly-Linked List**. If there is both a reference to the next node and the previous node, it is called a **Doubly-Linked List**.

In a special case, the last node of the list, the **Tail** node, can point to the **Head**, this is called a **Circular Linked List**. Like how you can cycle through your programs in a loop with Alt + Tab.

Traversal through a singly-linked linked list is done with two pointers, one pointing to the current node, and one pointing to the previous node. The two pointers inch through the list one at a time until the search condition is met. This is akin to an inchworm moving along a branch. You insert and delete nodes by simply changing the pointers to point to a new node, or skip over a node for insertion and deletion, respectively.


![](Pasted%20image%2020220413230840.png)



### Time Complexity

Function | Complexity
--- | ---
Access | $O(n)$
Search | $O(n)$
Insertion | $O(1)$
Deletion | $O(1)$

### Implementation

```python
class NodeList:
    def __init__(self, value=None, next_node=None, prev_node=None):
        self.value = value
        # Just next for singly linked list
        self.next_node = next_node
        # Add in prev pointer for doubly linked list
        self.prev_node = prev_node
```