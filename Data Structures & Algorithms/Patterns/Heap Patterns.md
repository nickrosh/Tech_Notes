
## Heapq Package

The Heapq package is in the Python Standard and is the main way you'll use heaps. Note that this package only implements a **MIN HEAP**.  The most important methods are the following:

- `heapq.heappush(heap, item)` : This method adds a new item to the min heap. **Note:** your item can be a tuple, this will let you have one item to be minimized, and a number of others. E.g. `heapq.heappush(heap, (item_being_sorted, item2, item3, etc...))`
- `heapq.heappop(heap)` : This methods pops the minimum value from the heap. 
- `heapq.heapify(list)` : This will turn the inputted list into a heap. To do heap sort, you would then just heap pop every element from the heap. **Note:** when you have an empty list, you can just heap push and heap pop into it.

If you want to do a **MAX HEAP** the workaround is to just set the items being inputted to heap push to negative. This way the "lowest" value comes out first, and you can just make it positive when you return it.


## Two Heaps

There are a class of problems where we are given a set of elements such that we can divide them into two parts. To solve, we need to know the smallest element in one part and the biggest element in the other part. You can use a **Min Heap** and **Max Heap** together