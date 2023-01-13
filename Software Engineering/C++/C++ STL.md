
The standard library provides many powerful objects and algorithms that are fully fleshed out. Here is a selection of some of the most important

## Containers

- `std::vector<type>` : Auto-sized dynamically allocated [array](../../Data%20Structures%20&%20Algorithms/Data%20Structures/Arrays.md). Probably the most used standard container in C++. From `<vector>`.
- `std::array<type, size>`: Fixed statically allocated array. From `<array>`.
- `std::unordered_map<key_type, value_type>` :  [Hash Map](../../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md) with fixed type for key and value. From `<unordered_map>`.
- `std::unordered_set<key_type>` : Hash set with fixed type for keys. From `<unordered_set>`

And many many more...

## Iterators

An *iterator* is an object that can traverse a container class without the user having to know how the container is implemented. With many classes, iterators are the primary way elements of these classes are accessed.

An iterator is best visualized as a pointer to a given element in the container, with a set of overloaded operator to provide a set of well-defined functions:

- **Operator*** : Dereferences the iterator returns the element that the iterator is currently pointing at
- **Operator++** : Moves the iterator to the next element, most iterators also include Operator-- to go back
- **Operator== and Operator!=** : Basic comparison operators to determine if two iterators point to the same element. To compare the values that two iterators are pointing at, dereference the iterators first, and then use a comparison operator.
- **Operator=** : Assign the iterator to a new position (typically at the start or end of list).

Each container includes four basic member functions for use with Operator= :
- `begin()` returns an iterator representing the beginning of the container
- `end()` returns an iterator representing just past the end of the container
- `cbegin()` returns a const (read only) version of begin()
- `cend()` returns a const (read only) version of end()

```cpp
int main()
{
    std::vector<int> vect;
    for (int count=0; count < 6; ++count)
        vect.push_back(count);

    std::vector<int>::const_iterator it; // declare a read-only iterator
    it = vect.cbegin(); // assign it to the start of the vector
    while (it != vect.cend()) // while it hasn't reach the end
        {
        std::cout << *it << ' '; // print the value of the element it points to
        ++it; // and iterate to the next element
        }

    std::cout << '\n';
}
```


## Algorithms

Algorithms that can be performed on elements in container classes can be found in the `<algorithm>` header. These allow you to do things like search, sort, insert, reorder, remove, and copy elements of the container class. All algorithms are implemented as functions that operate using iterators

`std::min_element` and `std::max_element` as the name suggests:
```cpp
std::list<int> li(6);
// Fill li with numbers starting at 0.
std::iota(li.begin(), li.end(), 0);

std::cout << *std::min_element(li.begin(), li.end()) << ' '
		  << *std::max_element(li.begin(), li.end()) << '\n';
```

`std::find` to find a value in the list class, then use `list::insert()` to add a new value into the list at that point.
```cpp
std::list<int> li(6);
std::iota(li.begin(), li.end(), 0);

// Find the value 3 in the list
auto it{ std::find(li.begin(), li.end(), 3) };

// Insert 8 right before 3.
li.insert(it, 8);

for (int i : li) // for loop with iterators
	std::cout << i << ' ';
```

`std::sort` to sort a vector, then `std::reverse` to reverse the array
```cpp
std::vector<int> vect{ 7, -3, 6, 2, -5, 0, 4 };
// sort the vector
std::sort(vect.begin(), vect.end());

for (int i : vect) {
	std::cout << i << ' ';
}
std::cout << '\n';

// reverse the vector
std::reverse(vect.begin(), vect.end());

for (int i : vect) {
	std::cout << i << ' ';
}
```

## Strings


#### `std::string`


#### `std::string_view`


## Input / Output

