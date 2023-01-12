

Quick sort is a sorting algorithm that takes advantage of the fact you can make sure one element is in the correct place, meaning all values greater are after it and all values lower are before it. This is referred to as the pivot point. quick sort iteratively does this pivot process to make smaller and smaller problems to solve for the algorithm. The algorithm works in $O(n log n)$ time but can be $O(n^2)$ in cases where the pivot point is poorly chosen every time (close to the edges of the array). Quick Sort has log $O(log n)$ space complexity.

Quick sort is not stable however, meaning that duplicate values can switch in order

Sorting the elements on the left of the pivot using recursion:
![](../../Attachments/Pasted%20image%2020220413231447.png)


Sorting the elements on the right of  the pivot using recursion:
![](../../Attachments/Pasted%20image%2020220413231529.png)





### Time Complexity

|Time Avg | Time Worst | Space|
|--- | --- | ---|
|$O(n log n)$ | $O(n^2)$ | $O(log n)$|


### Implementation

```python
# function to find the partition position
def partition(array, low, high):

  # choose the rightmost element as pivot
  pivot = array[high]

  # pointer for greater element
  i = low - 1

  # traverse through all elements
  # compare each element with pivot
  for j in range(low, high):
    if array[j] <= pivot:
      # if element smaller than pivot is found
      # swap it with the greater element pointed by i
      i = i + 1

      # swapping element at i with element at j
      (array[i], array[j]) = (array[j], array[i])

  # swap the pivot element with the greater element specified by i
  (array[i + 1], array[high]) = (array[high], array[i + 1])

  # return the position from where partition is done
  return i + 1

# function to perform quicksort
def quickSort(array, low, high):
  if low < high:

    # find pivot element such that
    # element smaller than pivot are on the left
    # element greater than pivot are on the right
    pi = partition(array, low, high)

    # recursive call on the left of pivot
    quickSort(array, low, pi - 1)

    # recursive call on the right of pivot
    quickSort(array, pi + 1, high)
```