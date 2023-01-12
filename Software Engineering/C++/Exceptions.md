
As a program executes, it will encounter errors. During runtime, these errors need to be dealt with or they could cause the system to crash. Exceptions are thrown in C++ with the *throw* keyword followed by the error. 

## Try Catch Block

Within the program, you can set a specific area to be on the lookout for exceptions, and then when one is detected, control is jumped to a *catch* block with the associated exception type where you can handle the exception in a number of ways. Typically catch block do one of the following:
- Print an error (either to the console or to a log file)
- Return a value or error code back to the caller
- Throw another exception. Because the catch block is outside of the try block, the newly thrown exception in this case is not handled by the preceding try block, it's handled by the next enclosing try block.

```cpp
try // Look for exceptions that occur within try block and route to attached catch block(s)
{
	// If the user entered a negative number, this is an error condition
	if (x < 0.0)
		throw "Can not take sqrt of negative number"; // throw exception of type const char*

	// Otherwise, print the answer
	std::cout << "The sqrt of " << x << " is " << std::sqrt(x) << '\n';
}
catch (const char* exception) // catch exceptions of type const char*
{
	std::cerr << "Error: " << exception << '\n';
}
```

Occasionally you may run into a case where you want to catch an exception, but not want to fully handle it at the point where you catch it. To rethrow the exception further up the chain, simply add `throw;` on its own in the catch block. C++ will automatically throw the exception up.

## Catch-All

Catch blocks operate on a type basis, meaning that a thrown exception can only go to a particular catch block if the type matches that of the catch block. When a function throws an exception that it does not handle itself, it is making the assumption that a [function](../C/Functions.md) up the [call stack](../C/The%20Stack%20&%20The%20Heap.md) will handle the exception. When an exception is thrown, the call stack is unwound until it is caught. If it is not caught, then the call stack may or may not be unwound and could cause a catastrophic failure of the program. Thus, we must make sure to handle all exceptions.

One solution is to use a catch-all catch block, which will accept any exception. Consider using a catch-all handler in main, to ensure orderly behavior when an unhandled exception occurs. Also consider disabling the catch-all handler for debug builds, to make it easier to identify how unhandled exceptions are occurring. If you have other catch block, put the catch-all at the end as it will capture everything.

```cpp
try
{
	throw 5; // throw an int exception
}
catch (double x)
{
	std::cout << "We caught an exception of type double: " << x << '\n';
}
catch (...) // catch-all handler
{
	std::cout << "We caught an exception of an undetermined type\n";
}
```


## Standard Exceptions

Many of the classes and operators in the standard library throw exception classes on failure. For example, [operator new](Smart%20Pointers.md) can throw `std::bad_alloc` if it is unable to [allocate enough memory](../C/Dynamic%20Memory%20Allocation.md). A failed `dynamic_cast` will throw `std::bad_cast` and so on. There are 28 different standard exceptions as of C++20. All standard exceptions derive from the base class `std::exception` (defined in the `<exception>` header). This base class is a small [interface class](C++%20OOP.md) designed to serve as a base class to any exception thrown by the [C++ standard library](C++%20STL.md). Simply catch a reference to the exception class and then get the actual error with `exception.what()`.

```cpp
try
{
	// Your code using standard library goes here
	// We'll trigger one of these exceptions intentionally for the sake of the example
	std::string s;
	s.resize(std::numeric_limits<std::size_t>::max()); // will trigger a std::length_error or allocation exception
}
// This handler will catch std::exception and all the derived exceptions too
catch (const std::exception& exception)
{
	std::cerr << "Standard exception: " << exception.what() << '\n';
}
```

We can use standard exceptions directly as well. `std::runtime_error` part of the `<stdexcept` header is a popular choice as it has a generic name, and its constructor takes a customizable message:

```cpp
try
{
	throw std::runtime_error("Bad things happened");
}
// This handler will catch std::exception and all the derived exceptions too
catch (const std::exception& exception)
{
	std::cerr << "Standard exception: " << exception.what() << '\n';
}
```

## Custom Exception Classes

You can make your own custom class as an exception, as long as you are catching the class in the catch block. Let's say you make a `SpecialException` class that you want thrown as an exception. Simply use it in the throw command, and then catch a reference to the class in the catch block

```cpp
try
{
	throw SpecialException();
}
catch (const SpecialException& special_exception)
{
	std::cerr << "caught something special";
}
```

Probably more usefully, we can derive from `std::exception` or `std::runtime_error` so that we can catch the custom exception as well as any standard exceptions coming from the standard library. You only need to override the virtual `what()` const member function in `std::exception` to derive from it.

```cpp
class ArrayException : public std::exception
{
private:
	std::string m_error{}; // handle our own string

public:
	ArrayException(std::string_view error)
		: m_error{error}
	{
	}

	// std::exception::what() returns a const char*, so we must as well
	const char* what() const noexcept override { return m_error.c_str(); }
};
```

## `noexcept` Specifier

In C++, all functions are classified as either *non-throwing* (does not throw exceptions) or *potentially throwing* (may throw an exception.) The `noexcept` specifier explicitly marks a function as non-throwing. Some functions are implicitly non-throwing, like destructors.

There are exception safety guarantees offered by each function and class. There are four levels of exception safety:
- **No guarantee**: There are no guarantees about what will happen if an exception is thrown (e.g. a class may be left in an unusable state)
- **Basic guarantee**: If an exception is thrown, no memory will be leaked and the object is still usable, but the program may be left in a modified state.
- **Strong guarantee**: If an exception is thrown, no memory will be leaked and the program state will not be changed. This means the function must either completely succeed or have no side effects if it fails. This is easy if the failure happens before anything is modified in the first place, but can also be achieved by rolling back any changes so the program is returned to the pre-failure state.
- **No throw / No fail**: The function will always succeed (no-fail) or fail without throwing an exception (no-throw)

You should err on the side of caution when using the `noexcept` specifier. There are two places that make sense to use `noexcept`
- On constructors and overloaded assignment operators that are no-throw (to take advantage of optimizations)
- On functions for which you want to express a no-throw or no-fail guarantee (e.g. to document that they can be safely called from destructors or other `noexcept` functions.)