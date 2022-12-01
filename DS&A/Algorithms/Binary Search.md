

Binary search allows you to search through a sorted array in $O(log n)$ time. This is much better than the time to search through an unsorted array $O(n)$. it involves searching midpoints, splitting the array in half until the element is found.

![](Pasted%20image%2020220413230533.png)

You simply compare the search term versus the midpoint continuously until the item is found. If the left pointer is greater than the right pointer, then the stop condition is reached and 


### Implementation (Iterative)

```python
def binarySearch(array, x, low, high):
    # Repeat until the pointers low and high meet each other
    while low <= high:
        mid = low + (high - low)//2
        if array[mid] == x:
            return mid
        elif array[mid] < x:
            low = mid + 1
        else:
            high = mid - 1
    return -1

```

### Implementation (Recursive)

```python
def binarySearch(array, x, low, high):
    if high >= low:
        mid = low + (high - low)//2
        # If found at mid, then return it
        if array[mid] == x:
            return mid
        # Search the left half
        elif array[mid] > x:
            return binarySearch(array, x, low, mid-1)
        # Search the right half
        else:
            return binarySearch(array, x, mid + 1, high)
    else:
        return -1
```