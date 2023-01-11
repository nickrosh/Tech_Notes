
Instead of [Overloading](Overloading.md) a function for every possible type as input, we can simply define a *template*. This is how C++ handles generics, allowing any type of data as input to a function or class. Note that in order to instantiate a template, the compiler needs to see the full definition of the template. This means that if we want to use a function template in multiple code files, each file would need its own copy. For this reason, templates are typically written in header files, where they can be `#include` into any code file that wants to use them. Templates are not subject to the one-definition rule.

Programming with templates is sometimes called *generic programming*. Whereas C++ typically has a string focus on types and type checking, in contrast generic programming lets us focus on the logic of algorithms and design of data structures without having to worry so much about type information.

## Function Templates

To create a function template , we add a template parameter declaration above the function template definition.

```cpp
template <typename T> // this is the template parameter declaration
T max(T x, T y) // this is the function template definition for max<T>
{
    return (x > y) ? x : y;
}
```

The standard practice is to name the template type a single upper case letter starting at T (e.g. T, U, V, etc.). We can also use multiple template type parameters to handle when there are multiple types being inputted:

```cpp
template <typename T, typename U>
auto max(T x, U y)
{
    return (x > y) ? x : y;
}
```

But notice, we changed the return type to `auto`. This is because there is the danger of narrowing the return value if T was an integer and U was a float. By using `auto` we can return whatever is the actual type of the return value.

Additionally, in C++20, they added the ability to just use `auto` as a parameter type, which is really just syntactic sugar for a template. This is called an *abbreviated function template*. For example:

```cpp
auto max(auto x, auto y)
{
    return (x > y) ? x : y;
}
```

is just shorthand in C++20 for:

```cpp
template <typename T, typename U>
auto max(T x, U y)
{
    return (x > y) ? x : y;
}
```

Remember that there is a different template type parameter for each auto parameter, this can be easy to forget.

#### Function Template Specialization

Now let's say we have a function template that prints out the sum of two numbers entered. What if we wanted to print out doubles in scientific notation? We can use *specialization* to make special versions of the function for certain data types, we just set the template to `<>` and define the types explicitly in the return and parameters.

```cpp
template <typename T>
T max(T x, T y) {
    return x+y;
}

template<>
double max<double>(double x, double y) {
	return x+y;
}
```


## Class Templates

We can also make templates of [classes](C++%20OOP.md). Say we make a container class that makes an array of integers. We then want the ability to make an instance with doubles, or any other type. Instead of duplicating the class, we can use a class template to allow generic object instantiation.

```cpp
template <typename T> // class template parameter
class Array
{
private:
    int m_length{};
    T* m_data{}; // type of array set to T

public:

    Array(int length)
    {
        assert(length > 0);
        m_data = new T[length]{}; // allocated an array of objects of type T
        m_length = length;
    }
    
    T& operator[](int index) // now returns a T&
    {
        assert(index >= 0 && index < m_length);
        return m_data[index];
    }

    // templated getLength() function defined below
    int getLength() const;
};

// member functions defined outside the class need their own template declaration
template <typename T>
int Array<T>::getLength() const // note class name is Array<T>, not Array
{
  return m_length;
}
```

This is exactly how `std::vector<type>` works, it is just a class template that lets you choose what type you want to use. Class templates are a clean way of making generic objects, but they have some limitations like mentioned for declaration. Normally, we would declare a class in a header file, and then define all of its functions in the associated cpp file. But with templates, the entire definition must be in the header file, so you tend to see massive template classes in header files.

You can also add a non-type parameter to the class template to allow for more fixed usage like setting the length of an array. The non-type parameter must be a `constexpr` value passed in as an argument

```cpp
template <typename T, int size> // size is an integral non-type parameter
class StaticArray
{
private:
    // The non-type parameter controls the size of the array
    T m_array[size] {};

public:
    T* getArray();

    T& operator[](int index)
    {
        return m_array[index];
    }
};
```


#### Class Template Specialization

Like function templates, class templates can also specialize. You can define separate definitions for specific types. In the below example, we're specializing a template for bools to allow for space-efficient storage.

```cpp
template <> // the following is a template class with no templated parameters
class Storage8<bool> // we're specializing Storage8 for bool
{
// What follows is just standard class implementation details
private:
    std::uint8_t m_data{};

public:
    void set(int index, bool value)
    {
        // Figure out which bit we're setting/unsetting
        // This will put a 1 in the bit we're interested in turning on/off
        auto mask{ 1 << index };

        if (value)  // If we're setting a bit
            m_data |= mask;   // use bitwise-or to turn that bit on
        else  // if we're turning a bit off
            m_data &= ~mask;  // bitwise-and the inverse mask to turn that bit off
	}

    bool get(int index)
    {
        // Figure out which bit we're getting
        auto mask{ 1 << index };
        // bitwise-and to get the value of the bit we're interested in
        // Then implicit cast to boolean
        return (m_data & mask);
    }
};
```

Additionally we can make partial specializations: meaning that only some of the template parameter types are generic, and others are specialized. An example would be in the above `StaticArray` class, making a partial specialization for double arrays, but still allowing the user to enter their desired length.