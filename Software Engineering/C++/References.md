
In C++, a *reference* is an alias for an existing object. Once a reference has been defined, any operation on the reference is applied to the object being referenced. Once initialized, a reference cannot be *reseated* meaning it can not be changed to reference another object. A reference is a more tight less error-prone version of a [pointer](../C/Pointers.md). Much like pointers, if the object the reference is pointing to is destroyed, it becomes a *dangling reference*. A reference isn't an object. If possible, the compiler will optimize references away by replacing all occurrences of a reference with the object being referred to.


## Const Reference

`lvalue` references can only bind to a modifiable `lvalue`. This means the following is illegal:

```cpp
const int x { 5 }; // x is a non-modifiable (const) lvalue
int& ref { x }; // error: ref can not bind to non-modifiable lvalue
```

By using the `const` keyword when declaring a reference, we tell a reference to treat the object it is referencing as const. Such a reference is called a `lvalue` reference to a const value, or *const reference*.

```cpp
const int x { 5 };    // x is a non-modifiable lvalue
const int& ref { x }; // okay: ref is a an lvalue reference to a const value
```

Normal references can only bind to modifiable `lvalues`. When using a const reference, we can assign it to `modifiable lvalues`, non-modifiable `lvalues`, and `rvalues`. Favor const references to regular references unless you need to modify the object being referenced

#### Temporary Objects

Temporary objects are normally destroyed at the end of the expression in which they are created. However, with const references, we can set references to a temporary object. To avoid dangling references when the object is destroyed, C++ has a special rule: when a const reference is bound to a temporary object, the lifetime of the temporary object is extended to match the lifetime of the reference.


## Pass by Reference

Probably the most important and useful part of references is that they can be used to pass object by reference. When normally passing object by value to functions, the entire object must be copied, this can be very expensive for large objects and arrays. Instead, you can pass a reference to an object to a function, and this will be much more efficient. This also allows us to mutate arguments. You can't mutate objects passed by value.

```cpp
void addOne(int& y) // y is bound to the actual object x
{
    ++y; // this modifies the actual object x
}
```

As mentioned before, const references are ideal as we can also bind them to other types of values. Use pass by const reference unless the function needs to mutate the argument.

```cpp
void printValue(const int& y) // y is now a const reference
{
    std::cout << y << '\n';
}
```


## Pass by Value vs Pass by Reference

Not all objects need to be passed by reference. Here are two key points that will help us understand when we should pass by value vs pass by reference:
1. The cost of copying an object is generally proportional to two things:
	- The size of the object. Objects that use more memory take more time to copy.
	- Any additional setup costs. Some class types do additional setup when they are initialized (such as opening a file or database). These setup costs must be paid each time an object is copied
2. Accessing an object through reference is slightly more expensive than through a normal variable identifier. With a variable identifier, the running program can just go to the memory address assigned to that variable and access the value directly. With a reference, there usually is an extra step: the program must first access the reference to determine which object is being referenced, and only then can it go to that memory address for that object and access the value.

Prefer pass by value for objects that are cheap to copy, and pass by const reference for objects that are expensive to copy. If you are not sure if an object is cheap to copy, favor pass by const reference.