
Functions in C follow the simple template of type then function name then function parameters. When you call a function (other than main) a new frame is placed on [The Stack](The%20Stack%20&%20The%20Heap.md) and local variables and parameters are initialized.

```C
int add(int x, int y) {
return x + y;
}
```


#### Function Pointers

However, you can also have function [Pointers](Pointers.md) which point to the functions location in memory. Similarly to arrays, when you call a function, you are really just telling the computer to jump to the functions location in memory. We can save this location in a pointer and use the ampersand to get the address of the function, like we do with objects.

```C
int foo() {}
int goo() {}

int main() {
	int (*fcnPtr)() = &foo; // fcnPtr points to function foo
	fcnPtr = &goo; // fcnPtr now points to function goo
	fcnPtr = foo; // we can actully get rid of the &, but this isn't clear to people
}
```

remember not to do `fcnPtr = goo();` this tries to assign the return value from a call to the function (of type int) to the pointer which is expecting type `int(*)()` . We want the pointer set to the address of the function. Th power of function pointers is that you can make flexible functions and libraries that allow the programmer to choose behavior by passing function pointers as arguments, like selecting what function to use to compare two numbers. This is accomplished by [virtual functions](../C++/C++%20OOP.md) in C++.