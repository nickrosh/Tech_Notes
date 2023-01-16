
## Deque Package

`collections.deque` is excellent because it is a data structure that is both a [Stack](../Data%20Structures/Stacks.md) and a [Queue](../Data%20Structures/Queues.md). This means you can use it for either reason, or even both if a problem needs popping from either side (like palindrome). The most important methods are the following:

- `deque.append(x)` : Add x to the right side of the deque
- `deque.appendleft(x)` : Add x to the left side of the deque
- `deque.pop()` : Remove and return an element from the right side of the deque
- `deque.popleft()` : Remove and return an element from the left side of the deque

To use it as a Stack, use `append` and `pop` (FIFO). To use it as a queue use `append` and `popleft` (FILO).


## Monotonic Stack / Queue

When we are tasked with identifying the *Next greater element* or lesser element, or anything where we have to record when a element is greater/less than some previous element, a monotonic stack can come in handy. Take LC 84 - Largest rectangle in histogram. The optimal way to solve the question is with a monotonically increasing stack. Record each element in the stack, if a new element is less that the top of the stack, keep popping and recording the area until the stack stays monotonic.

The implementation is simple: when you reach a new element, check it versus the top of the stack/queue. If it will break the monotonic condition, then pop from the stack (and process popped elements if needed) until you can successfully insert the new element.