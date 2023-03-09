

## Binary Search Range

A lot of the time, you won't just look for a target number. The question will disguise that it is asking for binary search. In normal binary search, you shift the pointers when the midpoint is less than or greater than the target. In this twist, you compare the midpoint to the value of some other function  or calculation. This is how you can find the optimal input to a calculation in log time. 

Famous example is Koko eating bananas (LC-875) where you are trying to find the minimum rate of banana consumption. You simply binary search through rates of consumption and return the minimum value.

## Rotated Sorted Array

An ascending sorted [Array](../Data%20Structures/Arrays.md) can be rotated as the following:

This array is rotated at index = 4
(0, 1, 2, 3, 4, 5) -> (4, 5, 0, 1, 2, 3)

A common pattern is to do [Binary Search](../Algorithms/Binary%20Search.md) on this type of array. An important feature of this type of array is the following

`if nums[mid] >= nums[left]` then you know your midpoint is in the left sorted portion of the array. In the array above, that would be (4, 5). if `nums[mid] < nums[left` then you know your midpoint is in the right sorted portion of the array, which would be (0, 1, 2, 3) in the example