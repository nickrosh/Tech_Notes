
## Rotated Sorted Array

An ascending sorted [Array](../Data%20Structures/Arrays.md) can be rotated as the following:

This array is rotated at index = 4
(0, 1, 2, 3, 4, 5) -> (4, 5, 0, 1, 2, 3)

A common pattern is to do [Binary Search](../Algorithms/Binary%20Search.md) on this type of array. An important feature of this type of array is the following

`if nums[mid] >= nums[left]` then you know your midpoint is in the left sorted portion of the array. In the array above, that would be (4, 5). if `nums[mid] < nums[left` then you know your midpoint is in the right sorted portion of the array, which would be (0, 1, 2, 3) in the example