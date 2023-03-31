
There are many algorithms where using two [Pointers](../../Software%20Engineering/C/Pointers.md) can answer array questions where elements are compared against each other.


## Shrinking Window

Sometimes we need to shrink a window on either side until we've reached a condition. Like in 2Sum II, there's a sorted array and we need to find which elements sum to the target. Because it is sorted the solution is simple, Have a pointer at the beginning and a pointer at the end of the list. If the sum is too large, then bring the right pointer in (so it points to a smaller number). Vice versa if the sum is too small then push to left pointer right (so it points to a bigger number).

Then end condition of almost all of these are:  `while left < right:`


## Merge Lists

The way to merge two sorted lists (this is also used in [Merge Sort](../Algorithms/Merge%20Sort.md)) is to put a pointer at the start of each list, and a pointer at the output. For every iteration, the lesser value among the two lists is chosen and the lesser pointer is incremented. Once one of the pointers goes to the end of its list, the rest of the other list is appended to the output.


## Trapping Rain Water


In LC 42 *Trapping Rain Water*, we are tasked with calculating the amount of water that can be trapped by the array of "heights". This is a simple matter of 
1. Understanding the algorithm of how to calculate water at each element and 
2. construct a shrinking window to work through the array.

![](../../Attachments/Pasted%20image%2020230331171411.png)

We notice an important point, at every point, the amount of water that can be stored is constrained only by the max height on its left, or its right (whichever is lower). Thus, we don't need to save the heights at every location, we only need two pointers and variables that track the max left height and max right height. Simply move the pointers depending on which max is lower, then recalculate the max and take the difference of the max height and the height at that point:

```python
def trap(self, height: List[int]) -> int:
	if not height:
		return 0

	result = 0
	left, right = 0, len(height) - 1
	max_left, max_right = height[0], height[-1]

	while left < right:
		# If you put the result after the max calc, you don't have to check for zero or min the other max
		if max_left <= max_right:
			left += 1
			max_left = max(max_left, height[left])
			result += max_left - height[left]
		else:
			right -= 1
			max_right = max(max_right, height[right])
			result += max_right - height[right]
	return result
```