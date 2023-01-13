
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

The classic C-style strings are just arrays of chars ending with a null terminator `\0`. If you choose to use C style strings, you can find many functions that manipulate them in the `<cstring>` header. C style strings can be unsafe and inflexible. In C++, you should always just use `std::string` or `std::string_view`. Here is a simple example of what a C-string looks like:

```cpp
int main()
{
    char name[20]{ "Alex" }; // only use 5 characters (4 letters + null terminator)
    std::cout << "My name is: " << name << '\n';

    return 0;
}
```

#### `std::string`

The standard library provides a standard type for strings, `std::string`. This class lives in the `<string>` header. Unlike C-strings which operate like arrays, you can reassign `std::string` values just like other objects: 

```cpp
int main()
{
    std::string name { "Alex" }; // initialize name with string literal "Alex"
    name = "John";              // change name to "John"

    return 0;
}
```

These strings will also output to standard output just as you would expect. For string input however, you will want to use `std::getline()`. Normally, each whitespace (spaces, newlines, etc.) will be considered by the compiler to be a break and thus each word would be considered its own string. To read in spaces as you enter them, use `std::getline()`. Look at this example where we use get line, as well as `std::ws` to remove leading whitespace from the input:

```cpp
int main()
{
    std::cout << "Enter your full name: ";
    std::string name{};
    std::getline(std::cin >> std::ws, name); // read a full line of text into name

    std::cout << "Enter your age: ";
    std::string age{};
    std::getline(std::cin >> std::ws, age); // read a full line of text into age

    std::cout << "Your name is " << name << " and your age is " << age << '\n';

    return 0;
}
```


#### `std::string_view`

`std::string` is powerful, however it is expensive to copy. If you pass it to functions you will have a significant performance hit. This is where `std::string_view` comes in. Introduced in C++17, `std::string_view`, which lives in the `<string_view>` header,  provides read-only access to an *existing string* (a C-string, a `std::string`, or a char array) without making a copy. In this example, we initialize a string to `string_view` and pass it to a function.

```cpp
void printSV(std::string_view str) // now a std::string_view
{
    std::cout << str << '\n';
}

int main()
{
    std::string_view s{ "Hello, world!" }; // now a std::string_view
    printSV(s);

    return 0;
}
```

It is much less expensive to copy a `sting_view` so you should always prefer to use it over passing `std::string` to a function. In fact, string view is more like a [references](References.md) to the string. You can think of a const reference to `std::string` as similar to `std::string_view`. It is still preferable to use pass a string view by value rather than a reference to string, as string view also let's you use all of the string manipulation functions that a const reference wouldn't allow.

The following is the how to explicitly mark different literals as different string types. Regular double quotes defaults to C strings:
```cpp
int main()
{
    using namespace std::literals; // easiest way to access the s and sv suffixes

    std::cout << "foo\n";   // no suffix is a C-style string literal
    std::cout << "goo\n"s;  // s suffix is a std::string literal
    std::cout << "moo\n"sv; // sv suffix is a std::string_view literal

    return 0;
};
```


## Input / Output

All I/O in C++ is done using *streams*. Abstractly, a stream is just a sequence of bytes that can be accessed sequentially. Over time, a stream may produce or consume potentially unlimited amounts of data.

Typically we deal with two different types of streams. *Input Streams* are used to hold input from a data producer, such as a keyboard, a file, or a network. For example, the user may press a key on the keyboard while the program is currently not expecting any input. Rather than ignore the users keypress, the data is put into an input stream, where it will wait until the program is ready for it.

Conversely, *Output Streams* are used to hold output for a particular data consumer, such as a monitor, a file, or a printer. When writing data to an output device, the device may not be ready to accept that data yet. For example, the printer may still be warming up when the program writes data to its output stream. The data will sit in the output stream until the printer begins consuming it. Some devices, such as files and networks, are capable of being both input and output sources.

A *Standard Stream* is a pre-connected stream provided to a computer program by its environment. C++ comes with four predefined standard stream objects:
1. `cin` : an input stream objects tied to the standard input (typically the keyboard)
2. `cout` : an output stream tied to the standard output (typically the monitor)
3. `cerr` : an output stream tied to the standard error (typically the monitor) providing unbuffered output
4. `clog` : an output stream tied to the standard error (typically the monitor) providing buffered output

#### `<iostream>`

**Input**: we can use the `>>` extraction operator to read information from an input stream. When reading strings, one common problem with the extraction operator is how to keep the input from overflowing your buffer. One solution is to use *manipulators*, which are objects that are used to modify a stream when applied with the extraction (`>>`) operator.

```cpp
#include <iomanip>
char buf[10];
std::cin >> std::setw(10) >> buf;
```

This program will only read the first 9 characters out of the stream (leaving room for a terminator). Any remaining characters will be left in the stream until the next extraction. We can use `getline()` to read in a string with whitespaces and a newline character. This is how to do multiple lines read in

```cpp
int main()
{
    char strBuf[11];
    // Read up to 10 characters
    std::cin.getline(strBuf, 11);
    std::cout << strBuf << '\n';

    // Read up to 10 more characters
    std::cin.getline(strBuf, 11);
    std::cout << strBuf << '\n';
    return 0;
}
```

**Output**: we can use the `<<` insertion operator to put information into an output stream. We can manipulate output streams by setting formatting flags. You can set these flags with the `setf()` function, with the appropriate flag as a parameter. Here is an example of adding the positive sign to positive numbers, and make the scientific notation "e" uppercase:
```cpp
std::cout.setf(std::ios::showpos | std::ios::uppercase); // turn on the std::ios::showpos and std::ios::uppercase flag
std::cout << 1234567.89f << '\n';
// output: +1.23457E+06
```

You can turn off a flag with `unsetf()`. We can also use manipulators like inputs, which are much easier to use. Here is how to change decimal to hex with a manipulator:
```cpp
std::cout << std::hex << 27 << '\n'; // print 27 in hex
std::cout << 28 << '\n'; // we're still in hex
std::cout << std::dec << 29 << '\n'; // back to decimal
```


#### `<fstream>`

Just like how we interact with standard input and output, we can input and output to files using the  `<fstream>` header. The file stream has to be explicitly set by the developer. However this is simple, simply instantiate an object of the appropriate I/O file class, with the name of the file as a parameter. Then use the insertion (`<<`) or extraction (`>>`) operator to write to or read data from a file. Once you are done, close the file by explicitly calling the `close()` function, or just let the file I/O variable go out of scope (the file I/O class destructor will close the file for you).

File output is simple with using the `ofstream` class. In this example, we write to a text file in the local directory:
```cpp
int main()
{
    // ofstream is used for writing files
    // We'll make a file called Sample.txt
    std::ofstream outf{ "Sample.txt" };
    // If we couldn't open the output file stream for writing
    if (!outf) {
        // Print an error and exit
        std::cerr << "Uh oh, Sample.txt could not be opened for writing!\n";
        return 1;
    }
    // We'll write two lines into this file
    outf << "This is line 1\n";
    outf << "This is line 2\n";
    return 0;
    // When outf goes out of scope, the ofstream
    // destructor will close the file
}
```

Now we take the file we just wrote and read it back in from disk. Not that `ifstream` returns a 0 if we've reached the end of the file (EOF). We'll use this fact to determine how much to read. Remember we need to use  `getline()` otherwise each space will be considered a break:

```cpp
int main()
{
    // ifstream is used for reading files
    // We'll read from a file called Sample.txt
    std::ifstream inf{ "Sample.txt" };
    // If we couldn't open the input file stream for reading
    if (!inf) {
        // Print an error and exit
        std::cerr << "Uh oh, Sample.txt could not be opened for reading!\n";
        return 1;
    }
    // While there's still stuff left to read
    while (inf) {
        // read stuff from the file into a string and print it
        std::string strInput;
        std::getline(inf, strInput);
        std::cout << strInput << '\n';
    }
    return 0;
    // When inf goes out of scope, the ifstream
    // destructor will close the file
}
```

Output in C++ may be buffered. This means that anything that is output to a file stream may not be written to disk immediately. Instead, several output operations may be batched and handled together. This is done primarily for performance reasons. When a buffer is written to disk, this is called *flushing* the buffer. One way to cause the buffer to be flushed is to close the file, the contents of the buffer will be flushed to disk, and then the file will be closed.

Buffering is not usually a problem, but in certain circumstances, it can cause complications. The main culprit in this case is when there is data in the buffer, and the program terminates (either by crashing or calling `exit()`). The data is not written to disk and is then lost forever. This is why it is a good idea to `close()` all files before calling `exit()`.

The file modes available in C++ are as follows:
- `app`: opens the file in append mode
- `ate`: seeks to the end of the file before reading/writing
- `binary`: Opens the file in binary mode (instead of text mode)
- `in` : Opens the file in read mode (default for `ifstream`)
- `out`: Opens the file in write mode (default for `ofstream`)
- `trunc`: Erases the file if it already exists.

#### Random File I/O

By default, when opening a file for reading/writing, the file pointer is set the the beginning of the file. In append mode, the pointer is set to the end of the file. We can also do random file access. This is done by manipulating the file pointer using either `seekg()` function (for input) and `seekp()` function (for output). The "g" stands for "get" and the "p" stands for "put".

These functions take in two parameters, the first one is an offset that determines how many bytes to move the file pointer. The second parameter is an `ios` flag that specifies what the offset parameter should be offset from:
- `beg`: The offset is relative to the beginning of the file (default)
- `cur`: The offset is relative to the current location of the file pointer
- `end`: The offset is relative to the end of the file

```cpp
inf.seekg(14, std::ios::cur); // move forward 14 bytes
inf.seekg(-18, std::ios::cur); // move backwards 18 bytes
inf.seekg(22, std::ios::beg); // move to 22nd byte in file
inf.seekg(24); // move to 24th byte in file
inf.seekg(-28, std::ios::end); // move to the 28th byte before end of the file
```
