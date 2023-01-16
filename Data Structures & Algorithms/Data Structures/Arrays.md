
The simplest data structure, yet also very powerful. An array is simply a collection of items stored at contiguous memory locations. The idea is to store multiple items of the same data type together, which makes it easier to calculate the position of each element by simply adding an offset to the base value. Because the array elements are tied to a location in memory, you can have constant time data access if you know the exact index you are looking for.

![](../../Attachments/Pasted%20image%2020220413233106.png)

#### Static Arrays

Normally, arrays have to have an allocated size when initialized. This means that the size of the array cannot change once declared and once its capacity is reached, it can store no more values. Here is a static int array in C initialized to a size of 10:

```C
int int_array[10];
```

#### Dynamic Arrays

Much more common are dynamic arrays which automatically resize based on the data inputs. This is the default array in Python and JavaScript, and the vector class in C++. When the user adds another element to a filled array, a new array of double size will be created and all values will be copied over. This operation is amortized to $O(1)$. This means the average time complexity, as whenever a new array needs to be created, it will take $O(n)$ time, but the average is constant.

![](../../Attachments/Pasted%20image%2020230114142926.png)


## Time Complexity

|Function | Complexity|
|---- | ---|
| Access | $O(1)$ |
| Search | $O(n)$ |
| Insert | $O(n)$ |
| Delete | $O(n)$ |
