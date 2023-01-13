
Memory management is difficult. There's many ways we can screw up and cause a memory leak. This is why there's such a strong emphasis on [RAII](RAII.md) in C++, to make sure that all memory we allocate on [The Heap](../C/The%20Stack%20&%20The%20Heap.md) is cleaned up when it is no longer needed.

## New & Delete

Unlike in C where we have to allocate memory by the exact byte size with [malloc and free](../C/Dynamic%20Memory%20Allocation.md), C++ gives us the keywords `new` to create a dynamically allocated object. This `new` keyword will automatically calculate the bytes needed for the object, so you don't have to put in the exact. To clean up a dynamic object in C++, use `delete` which functions the same as `free` in C, you just call it on the [pointer](../C/Pointers.md) of the dynamic memory.

```cpp
int main()
{
    int* ptr{ new int{} }; // dynamically allocate an integer
    int* otherPtr{ ptr }; // otherPtr is now pointed at that same memory location

    delete ptr; // return the memory to the operating system.  
    // ptr and otherPtr are now dangling pointers.
    ptr = nullptr; // ptr is now a nullptr
    // however, otherPtr is still a dangling pointer!
    return 0;
}
```

As in the example above, it is best practice to set deleted pointers to `nullptr` unless they are going out of scope immediately afterwards. Also keep in mind that if you try to `delete` a pointer that has already been deallocated, you will get undefined behavior.

Now consider the following example: what if you correctly delete a pointer, but actually return from the function before the data can be deallocated?

```cpp
void someFunction()
{
    Resource* ptr = new Resource();
    int x;
    std::cout << "Enter an integer: ";
    std::cin >> x;
    
    if (x == 0)
        return; // the function returns early, and ptr won’t be deleted!
    // do stuff with ptr here
    
    delete ptr;
}
```

In the above example, the memory allocated for variable `ptr` is now leaked due to the early return. These issues happen because pointers have no inherent mechanism to clean up after themselves. This is where *Smart Pointers* come to the rescue. They are essentially classes that "own" a pointer passed to it, and will guarantee to deallocate the pointer when the class object is destroyed via the [destructor](C++%20OOP.md). This is the heart of [RAII](RAII.md).

## Move Semantics

Normally, we implement copy and assignment with copy semantics, meaning a shallow copied object is created. This is wasteful and would produce duplicated pointers, and crashes. Plus assigning or initializing a dumb pointer doesn't copy the object being pointed to, so why would we expect smart pointers to behave differently?

The solution is *Move Semantics*. Instead of having our copy constructor and assignment operator copy the pointer (copy semantics) we instead transfer/move ownership of the pointer form the source to the destination object. `std::move` is a standard library function that casts (using static cast) its argument into a r - value reference, so that move semantics can be invoked. Thus we can use `std::move` to cast an l - value into a type that will prefer being moved over being coped. `std::move` is defined in the `<utility>` header.

```cpp
std::vector<std::string> v;

// We use std::string because it is movable (std::string_view is not)
std::string str { "Knock" };

std::cout << "Copying str\n";
v.push_back(str); // calls l-value version of push_back, which copies str into the array element

std::cout << "str: " << str << '\n';
std::cout << "vector: " << v[0] << '\n';

std::cout << "\nMoving str\n";

v.push_back(std::move(str)); // calls r-value version of push_back, which moves str into the array element

std::cout << "str: " << str << '\n'; // The result of this is indeterminate
std::cout << "vector:" << v[0] << ' ' << v[1] << '\n';
```

In the above example, we see two different ways of adding to a vector. The l - value version of pushing to the vector makes you copy the string into the array. The r - value version moves the string directly into the array. This is much more efficient, especially the larger an object gets.

#### `std::move_if_noexcept`

Consider the case where we are copying some object, and the copy fails for some reason In such a case, the object being copied in not harmed in any way, because the source object doesn't need to be modified to create a copy. We can discard the failed copy and move on. The [Strong Exception Guarantee](Exceptions.md)  is upheld. Now if it was a move operation, and it was interrupted, our source object will be left in a modified state. To give move constructors the *strong exception guarantee* we can either give it the `noexcept` specifier or we can use `std::move_if_noexcept()` to determine whether a copy should be performed. If the object passed won't throw an exception, it will operate exactly like `std::move` but otherwise `std::move_if_noexcept` will return a normal l-value reference to the object.


## Unique Pointer

`std::unique_ptr` is used to manage any dynamically allocated object that is not shared by multiple objects. That is, `std::unique_ptr` should completely own the object it manages, not share that ownership with other classes. `std::unique_ptr` lives in the `<memory>` header. Unique shared is easily the most popular smart pointer.

```cpp
class Resource
{
public:
	Resource() { std::cout << "Resource acquired\n"; }
	~Resource() { std::cout << "Resource destroyed\n"; }
};

int main()
{
	// allocate a Resource object and have it owned by std::unique_ptr
	std::unique_ptr<Resource> res{ new Resource() };

	return 0;
} // res goes out of scope here, and the allocated Resource is destroyed
```

To access the managed object, we just use the regular pointer operators `*` which returns a reference to the managed resource,  and `->` which returns a pointer.

Unique pointer is smart enough whether to use scalar delete or array delete, but still, for arrays and strings, it is best to just use the [standard library](C++%20STL.md) objects like `std::vector` and `std::array`.

#### `std::make_unique()`

You can also create a unique pointer with `std::make_unique()`. This [templated](Templates.md) function constructs an object of the template type and initializes it with the arguments passed into the function. Here we show make unique with an example "Fraction" class:

```cpp
// Create a single dynamically allocated Fraction with numerator 3 and denominator 5
// We can also use automatic type deduction to good effect here
auto f1{ std::make_unique<Fraction>(3, 5) };
std::cout << *f1 << '\n';

// Create a dynamically allocated array of Fractions of length 4
auto f2{ std::make_unique<Fraction[]>(4) };
std::cout << f2[0] << '\n';
```

`make_unique` is generally preferable than manually using `unique_ptr` and `new`. Some errors are avoided with `make_unique`.

#### Passing and Returning from a Function

`std::unique_ptr` can be safely returned from a function by value:

```cpp
std::unique_ptr<Resource> createResource()
{
     return std::make_unique<Resource>();
}
```

In general, you should not return `std::unique_ptr` by pointer or reference. The return value will be transferred 

Passing a `unique_ptr` to a function requires a little more thought. If you want the function to take ownership of the contents of the pointer, pass the `std::unique_ptr` by value. Note that because copy semantics have been disabled, you will need `std::move` to actually pass the variable in.

However, most of the time, you don't want the function to take ownership of the resource. Although you can pass a `std::uniquq_ptr` by reference (which will allow the function to use the object without assuming ownership), you should only do so when the called function might alter or change the object being managed. Instead it's much better to just pass the resource itself (by pointer or reference, depending on whether null is a valid argument). To get a raw pointer from a `std::unique_ptr` you can use the `get()` method.

```cpp
// The function only uses the resource, so we'll accept a pointer to the resource, not a reference to the whole std::unique_ptr<Resource>
void useResource(Resource* res)
{
	if (res)
		std::cout << *res << '\n';
	else
		std::cout << "No resource\n";
}

int main()
{
	auto ptr{ std::make_unique<Resource>() };

	useResource(ptr.get()); // note: get() used here to get a pointer to the Resource

	std::cout << "Ending program\n";

	return 0;
} // The Resource is destroyed here
```


## Shared Pointer

Unlike `std::unique_ptr` which is designed to singly own and manage a resource, `std::shared_ptr` is meant to solve the case where you need multiple smart pointers co-owning a resource. Internally, shared pointer keeps track of how many `std::shared_ptr` are sharing the resource. As long as at least one `std::shared_ptr` is pointing to the resource, the resource will not be deallocated. As soon as the last `std::shared_ptr` managing the resource goes out of scope or is reassigned, the resource will be deallocated. For the most part, people shy away from shared pointer in modern C++ as it causes a ton of complexity.

```cpp
int main()
{
	// allocate a Resource object and have it owned by std::shared_ptr
	Resource* res { new Resource };
	std::shared_ptr<Resource> ptr1{ res };
	{
		std::shared_ptr<Resource> ptr2 { ptr1 }; // make another std::shared_ptr pointing to the same thing

		std::cout << "Killing one shared pointer\n";
	} // ptr2 goes out of scope here, but nothing happens

	std::cout << "Killing another shared pointer\n";

	return 0;
} // ptr1 goes out of scope here, and the allocated Resource is destroyed
```

Due to the way `std::shared_ptr` internally tracks how many pointers there are left on a resource, you must make copies of existing `std::shared_ptr` if you need more than one. If you initialize a second `std::shared_ptr` at the initial object, bad things will happen.

#### `std::make_shared()`

Similar to `std::make_unique()` we can (and should) use `make_shared` to make `std::shared_ptr`. It is the simpler and safer way of making smart pointers. Again though, you must copy other shared pointers rather than pointing to the initial object more than once.

```cpp
int main()
{
	// allocate a Resource object and have it owned by std::shared_ptr
	auto ptr1 { std::make_shared<Resource>() };
	{
		auto ptr2 { ptr1 }; // create ptr2 using copy of ptr1

		std::cout << "Killing one shared pointer\n";
	} // ptr2 goes out of scope here, but nothing happens

	std::cout << "Killing another shared pointer\n";

	return 0;
} // ptr1 goes out of scope here, and the allocated Resource is destroyed
```


#### Weak Pointer

A *circular reference* is a series of references where each object references the next, and the last object references back to the first, causing a referential loop. When this happens with shared pointers, we can get into a situation where each object ends up keeping the next object alive. Thus, no objects in the series can be deallocated because they think some other object still needs it. `std::weak_ptr` was designed to solve this problem. A `std::weak_ptr` is an observer, it can observe and access the same object as a `std::shared_ptr` but it is not considered an owner. Thus it will not be counted when the last shared pointer is destroyed and wants to deallocate the object.

Even writing this makes me cringe. I hope I'll never have to use this, but we'll see.