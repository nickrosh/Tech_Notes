
There are many algorithms where using two pointers can provide very powerful capabilities.




## Shrinking Window

Sometimes we need to shrink a window on either side until we've reached a condition. Like in 2Sum II, there's a sorted array and we need to find which elements sum to the target. Because it is sorted the solution is simple, Have a pointer at the beginning and a pointer at the end of the list. If the sum is too large, then bring the right pointer in (so it points to a smaller number). Vice versa if the sum is too small then push to left pointer right (so it points to a bigger number).

Then end condition of almost all of these are:  `while left < right:`