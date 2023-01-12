
## Reverse Linked List

You can reverse a [Linked List](../Data%20Structures/Linked%20Lists.md) with the following simple method

```python
prev = None
current = head

while current:
	next_temp = current.next
	current.next = prev
	prev = current
	current = next_temp
return prev
```


This iterates through the LL and uses a temp variable to save the next pointer, and then changes everything to point backwards.

## Circular Linked List

You can determine a linked list is circular by simply saving the nodes in a [Hash Map](../Data%20Structures/Hash%20Maps.md), and then when  a new node is found to exist in a hash map, you know you have a circular list. This is enabled because you can use class instances as Dictionary Keys in Python.

## Fast and Slow Pointers

In problems like finding the middle node of a linked list, you can use one pointer to travel quickly to end, and then logically if a second pointer moves half as fast, it will end up in the middle.