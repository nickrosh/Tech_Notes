
There are many algorithms where using two [Pointers](../../Software%20Engineering/C/Pointers.md) can answer array questions where elements are compared against each other.


## Shrinking Window

Sometimes we need to shrink a window on either side until we've reached a condition. Like in 2Sum II, there's a sorted array and we need to find which elements sum to the target. Because it is sorted the solution is simple, Have a pointer at the beginning and a pointer at the end of the list. If the sum is too large, then bring the right pointer in (so it points to a smaller number). Vice versa if the sum is too small then push to left pointer right (so it points to a bigger number).

Then end condition of almost all of these are:  `while left < right:`

In LC 42 *Trapping Rain Water*, we are tasked with calculating the amount of water that can be trapped by the array topology. This is a simple matter of 1. Understanding the algorithm of how to calculate water at each element and 2. construct a shrinking window to work through the array