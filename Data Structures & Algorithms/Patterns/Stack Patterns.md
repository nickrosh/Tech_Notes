
## Deque Package

`collections.deque` is excellent because it is a data structure that is both a [Stack](../Data%20Structures/Stacks.md) and a [Queue](../Data%20Structures/Queues.md). This means you can use it for either reason, or even both if a problem needs popping from either side (like palindrome). The most important methods are the following:

- `deque.append(x)` : Add x to the right side of the deque
- `deque.appendleft(x)` : Add x to the left side of the deque
- `deque.pop()` : Remove and return an element from the right side of the deque
- `dequq.popleft()` : Remove and return an element from the left side of the deque

To use it as a Stack, use `append` and `pop` (FIFO). To use it as a queue use `append` and `popleft` (FILO).

## Monotonically Decreasing Stack

In problems such as LC-739 Daily Temperatures, we must create a stack that has the condition that it is always decreasing as more elements are added to the stack. This is very useful in cases where we need to find when and where a higher element is found, as it can be compared with elements of the stack.