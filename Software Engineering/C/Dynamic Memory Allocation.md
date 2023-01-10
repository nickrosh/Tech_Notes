
As the Stack only has very limited memory, we have to put all large data structures on [The Stack & The Heap](The%20Stack%20&%20The%20Heap.md). To do so, we much use Dynamic Memory Allocation. When memory is allocated on the heap, we must use pointers to track this data. Once we are done with the memory, we must free it, otherwise it becomes a memory leak. All the functions for dynamic memory are in the `<stdlib.h>` header file.

## Malloc

`pointer = (cast-type*)malloc(byte-size)`

![](Pasted%20image%2020230110010218.png)

Malloc is the straightforward memory allocation. You must manually give how many bytes that need to be allocated. You can do this by simply getting the data type in question and multiplying it by however many elements you need. If the malloc fails, it will return a [null pointer](Pointers.md) Here is an example of allocating memory for a 10 element int array:

```C
int main() {
	ptr = (int*)malloc(10 * sizeof(int));
	if (ptr == NULL) {
		exit(0); // Allocation failed, exit out
	}
	
	free(ptr); // free the memory after you are finished
}
```



## Calloc

`pointer = (cast-type*)calloc(n, element-size)`


![](Pasted%20image%2020230110010334.png)

Use Calloc (or contiguous allocation) when you want to initialize all the elements in memory. There is additional overhead to doing this, so only do it if necessary. Calloc will initialize each block to with a default value of '0'.


## Free

`free(pointer)`

![](Pasted%20image%2020230110010417.png)

All dynamically allocated memory must be freed, or it will become a memory leak. Simply call `free` on the pointer and the [Operating System](Operating%20System.md) will take back control of the memory.


## Realloc

`pointer = realloc(pointer, new byte-size)`


![](Pasted%20image%2020230110010938.png)

The final method reallocated memory in an existing memory allocation. simply define the new byte-size desired.