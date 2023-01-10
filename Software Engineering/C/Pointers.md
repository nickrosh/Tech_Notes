
Pointers are simply data structures that point to a particular address in memory. For 64-bit systems, this means an 8 byte memory address. Pointers are declared with an asterisk next to the type of the variable name. Conversely, if you use an asterisk before a pointer, it will dereference it and provide the value at that particular address. If you use an ampersand before a value, it will return the address of the value.

```c
int *ptr; // C style uninitialized pointer (garbage address)
int* ptr; // C++ style
int* ptr = NULL; // setting a null pointer, this is valid rather than not initializing

int x = 10;
int* ptr = &x; // pointer stores the address of variable x
printf("%d", *ptr); // print out the value being pointed to by ptr (x=10)
*ptr = 12; // We access the underlying object and change it. Thus x is also now 12
```

A *dangling pointer* is a pointer that is holding the address of an object that is no longer valid (e.g. because it has been destroyed). Dereferencing a dangling pointer will lead to undefined results. An uninitialized pointer is sometimes called a *wild pointer*. Always initialize pointers, they should either be set to an address or to null. 

In C, you can set the pointer equal to `NULL` to set it as a null pointer, this is simply a macro that equals zero. In C++11, and C23, you should set the pointer to `nullptr` to have the correct null pointer data type as well. Check for null pointers in code `if(!ptr) {}` to avoid dereferencing null pointers and causing a crash.

Instead of passing large objects by value, you can simply pass by address via a pointer. This pointer can then be used by the function and any changes to the underlying object will be maintained after the lifetime of the function, unlike pass by value.

```c
void swap(int* x, int* y) {
	int temp = *x;
	*x = *y;
	*y = temp;
	return;
}
```

The above function demonstrates how we use pass-by-reference to change variable values and persist the change as the original objects themselves are being changed, not copies.

## Pointers and Arrays

In C, when you initialize an [Array](Arrays.md), you are really just creating a sequential span of memory. Moreover, when you call an array, what you are really doing is just calling a pointer to the first address of the array. Each element in the array is just moving the pointer to the offset by the byte size of each element.

![](Pasted%20image%2020230109020508.png)

In the above image you can see, the array is nothing more than simply a pointer to the beginning of a sequence of memory, which can then be accessed as separate elements via offsets from the beginning memory location.