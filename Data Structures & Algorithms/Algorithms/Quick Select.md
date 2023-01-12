>Given an integer array, find the Kth largest element

If we [Sort](Quick%20Sort.md) the array, it will be in $O(nlogn)$ time.
If we use a [Priority Queue](Heaps.md) it will be in $O(nlogk)$ time.

We can actually do better. **Quick Select** is an algorithm that can select a value in $O(n)$ average time. 

It uses the same principle as [Quick Sort](Quick%20Sort.md) except instead of trying to get every single element in the right place, it only keeps iterating until it gets the element in the specific index trying to be found.

### Implementation

```python
k = len(nums) - k

def quick_select(left: int, right: int):
	# like Quick Sort, we set the pointer to the left index
	pointer = left
	# We set the pivot value as the rightmost value in the partition
	pivot = nums[right]


	# Go through each value in the partition
	for i in range(left, right):
		# When a value is less than the pivot switch it
		# with whatever pointer is currently pointing to
		if nums[i] <= pivot:

			nums[i], nums[pointer] = nums[pointer], nums[i]
			# Then advance the pointer by one
			pointer += 1
	# At the end, swap the value at the pointer and the pivot value
	nums[pointer], nums[right] = nums[right], nums[pointer]
	# Here is where Quick Select comes in, we want to recurse to
	# a smaller subset where we know K will be, and if we find K
	# we can simply return the value at that location
	if pointer > k:   return quick_select(left, pointer - 1)

	elif pointer < k: return quick_select(pointer + 1, right)

	else:             return nums[pointer]

return quick_select(0, len(nums) - 1)
```