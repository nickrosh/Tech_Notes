
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

You can determine a linked list is circular by simply saving the nodes in a [Hash Map](../Data%20Structures/Hash%20Maps.md), and then when  a new node is found to exist in a hash map, you know you have a circular list. This is enabled because you can use class instances as Dictionary Keys in Python. You can also use the Fast & Slow method.

## Fast and Slow Pointers

In problems like finding the middle node of a linked list, you can use one pointer to travel quickly to end, and then logically if a second pointer moves half as fast, it will end up in the middle.

This can also be done to detect cycles in constant space . If a slow pointer is moving one node at a time and a fast pointer is moving two at a time, if they ever meet before fast goes to NULL, then that means there is a loop in the Linked List.

## Find First Node of A Cycle

There is a simple algorithm called Floyd's loop detection algorithm to find the first node of a loop. 

![](../../Attachments/Pasted%20image%2020230126003719.png)

Look at the image above, "A" is the distance from the Head to the start of node. "B" is the intersection point of a Fast and Slow pointer. Mathematically, "C" the distance from the intersection point to the first node of the cycle is exactly equal to "A". This means once you have found the intersection (`fast == slow`), then make a new slow pointer at the head, lets say `slow2`. Now move slow and slow2 up one at a time, and the point at which they intersect (`slow == slow2`) is the first node of the loop.


## Dummy Node

Whenever you are making a new list, or even changing a list in place such a way that the head goes in different places, making a dummy node is helpful. The dummy node is an empty node that points to the beginning of the list, so that at the end of the function you simply `return dummy.next` to return the whole list. This simplifies the return process.