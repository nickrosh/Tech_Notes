
## Heapq Package

The Heapq package is in the Python Standard and is the main way you'll use [Heaps](../Data%20Structures/Heaps.md). Note that this package only implements a **MIN HEAP**.  The most important methods are the following:

- `heapq.heappush(heap, item)` : This method adds a new item to the min heap. **Note:** your item can be a tuple, this will let you have one item to be minimized, and a number of others. E.g. `heapq.heappush(heap, (item_being_sorted, item2, item3, etc...))`
- `heapq.heappop(heap)` : This methods pops the minimum value from the heap. 
- `heapq.heapify(list)` : This will turn the inputted list into a heap. To do heap sort, you would then just heap pop every element from the heap. **Note:** when you have an empty list, you can just heap push and heap pop into it.

If you want to do a **MAX HEAP** the workaround is to just set the items being inputted to heap push to negative. This way the "lowest" value comes out first, and you can just make it positive when you return it.


## Heap of Lists

When you run `sort` on a list of lists, it will sort by the first value in each list. Heaps work the same way. You can push lists like `[val1, val2, val3]` and it will be heapsorted by the first value. This can come in handy when you want to do things like find the most recent of a entry. You can have the timestamp as the first value, and the rest of the data following it. The heap will then return the most recent (or whatever you want) when you pop from the heap.


## Two Heaps

There are a class of problems where we are given a set of elements such that we can divide them into two parts. To solve, we need to know the smallest element in one part and the biggest element in the other part. You can use a **Min Heap** and **Max Heap** together

LC502-IPO is an interesting problem that asks you to get K different elements that would maximize their value (profit). However, you can only select elements that you have the resources for (capital). This is two different objectives, you are trying to maximize profit while only looking at the projects you have capital for. The solution is to have a max heap for the project profits, and a min heap of all the capital requirements and their related profits. You can pop from the min heap as long as you have the capital needed, and insert those values into the max heap of the profit. Then you pop K times from the profit max heap and you will maximize your profit requirements.


## Median of a Data Stream

The classic use of Two Heaps is finding the median in a stream of data. A key property of median values motivates this: the median will always be at the boundary of the lower and upper half of values. In other words, we can put the lower half of all values into a Max Heap and the upper half of all values into a Min Heap. The key is:
1. to make sure that they are always balanced, small heap should always be equal or one value larger than the large heap
2. Make sure that every value in the small heap is lesser than every value in the large heap. This can be done easily every time you add a new value to the stream by then popping from both heaps and pushing the lower value of the two into the small heap and the larger one in the large heap.

![](../../Attachments/Pasted%20image%2020230331184942.png)