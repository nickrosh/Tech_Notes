 
## Classes

[Structs](../C/Structs.md) in C solve the issue of a custom user-defined object that contains different primitive types. However, with [Object Oriented Programming](../OOP/Object%20Oriented%20Programming.md), we create  a new paradigm where we combine state and functionality. This combination of state and function is called a *Class*. So we can make a circle class, with radius as the variable being stored. Instead of making [Functions](../C/Functions.md) that are simply associated to the class, we can make a function in the class itself called a method or member function. We can make a function that returns the area of the circle.

```C++
class Circle {
private:
	int radius;
public:
	int area() {
		const double pi = 3.14159
		return pi*radius*radius
	}
};
```

Encapsulation is a key tenet of object oriented programming. It means we must hide implementation details and objects away from the rest of the program. This prevents other developers from coupling to parts of the classes you didn't intend, and makes changing the class much more complicated. Instead we only expose what is needed in a public interface. In the Circle class, the radius variable is private while the area function is public. C++ has three access specifiers:
1. Public: Can be accessed by the entire program
2. Private: Can only be accessed within the class
3. Protected: Can only be accessed within the class, and by derived classes

A good rule of thumb is to make member variables private and member functions public and to not use protected. The last difference with structs is simple: structs default to public variables, classes default to private.

## Constructors and Destructors

A *Constructor* is a special type of class member function that is automatically called when an object of that class is created. Constructors are typically used to initialize member variables of the class to appropriate user-provided values, or to ado any setup steps necessary for the class to be used (e.g. open a file or database). Simply define a constructor by using just the name of the Class itself as a function, with no return type. C++ will create an implicit default constructor for you if you do not define one. It is best practice to explicitly define one like `Circle() = default;` to be clear.

```C++
class Something {
private:
	int value {};

public:
	// Constructor has the same name as the class
	Something() = default; // set the default constructor explicitly

	Something(int x) {
	// initialize things
	}
};
```

Initializing and assigning the member variables in the constructor can be problematic, so C++ provides a method for initializing class member variable via a *member initializer list* (often called member initialization list). This is done with adding a colon after the constructor and listing each of the member variables and what they should equal to, usually parameters to the constructor itself. This also lets us set default values.

```C++
class Circle {
private:
	int m_value1 {};
	double m_value2 {};
	char m_value3 {};

public:
	Circle(int value1, double value2, char value3='c') // directly initialize here
		: m_value1=value1, m_value2=value2, m_value3=value3 { 
		// No need for initialization here
		}
}
```

Note that variables in the initializer list are not initialized int he order that they are specified in the initializer list. Instead they are initialized in the order in which they are declared in the class. So if order of initialization matters, make sure to keep the same order as which the variables are defined in the class. Lastly you, can stack constructors to run after each other. For instance if you have default behavior you want for every instance, but only certain behavior to execute when integers are passed in, you simply call the first constructor in the member initializer list of the second one.

*Destructors* are the opposite of constructors. They run when an object is destroyed, and are intended to clean up any memory or resources being used by the object. They are a key component of [RAII](RAII.md). They are defined just like constructors, except with a "~" in front of the name.

```C++
class IntArray {
private:
	int* m_array{};
	int m_length{};

public:
	IntArray(int length) { // Constructor
	m_array = new int[static_cast<std::size_t>(length)]{};
	m_length = length;
	}

	~IntArray() { // Destructor
	delete[] m_array; // Dynamically delete the array we allocated before
	}
};

```


#### Friend Functions and Classes

Normally, when you define a variable or function as private, functions outside the class can't access them. We can define an outside function or class as a "friend" by putting a forward declaration in the public access with "friend" at the beginning. This will allow the friend to access private variables and functions. This allows us to keep variables private while exposing them for select applications. This is especially useful for [Overloading](Overloading.md) functions that you don't want to have to define as a member function. 


## Composition and Inheritance

A key concept in OOP is the relationship between objects. Composition is "has-a" relationships. This can be as simple as having a class for position on a grid. Any object on that grid can create an instance of the Position class as one of its member variables, this will allow it to have all the functionality without repeating code. Here is an example of composition:

```C++
class Creature
{
private:
    std::string m_name;
    Point2D m_location;

public:
    Creature(const std::string& name, const Point2D& location)
        : m_name{ name }, m_location{ location }
    {
    }

    void moveTo(int x, int y)
    {
        m_location.setPoint(x, y);
    }
};
```


[Inheritance](../OOP/Inheritance.md) on the other hand is a "is-a" relationship, and was and still is a big focus in academic circles and people with chronic Java disease. You should probably never use inheritance, definitely never more than one layer deep. When a class inherits from another base class, it inherits all of its code. In fact, the base class constructor is called first, and then propagates down until the derived class in constructed. Here we make a class that inherits from a base Person class, and inherits all of its functionality.

```cpp
class BaseballPlayer : public Person
{
public:
    double m_battingAverage{};
    int m_homeRuns{};

    BaseballPlayer(double battingAverage = 0.0, int homeRuns = 0)
       : m_battingAverage{battingAverage}, m_homeRuns{homeRuns}
    {
    }
};
```

You'll notice there's an access specifier when inheriting, pretty much only use *public* as private and protected make more rules against accessing base class functionality that only make the system more complex. 

The derived Class can redefine behavior from the base class. Note that you can change the access specifier of the base class methods: 

```cpp
class Base
{
private:
	void print() const
	{
		std::cout << "Base";
	}
};

class Derived : public Base
{
public:
	void print() const
	{
		std::cout << "Derived ";
	}
};
```

Sometimes, we don't want to completely replace a base class function, but instead want to add additional functionality to it. We can have the derived class call the base function of the same name  with a scope qualifier (class name with two colons) and then add additional behavior.

```cpp
class Derived: public Base
{
public:
    Derived(int value)
        : Base { value }
    {
    }

    int getValue() const  { return m_value; }

    void identify() const
    {
        Base::identify(); // call Base::identify() first
        std::cout << "I am a Derived\n"; // then identify ourselves
    }
};
```



## Virtual Functions


Let's say you have a function that takes in any Employee. You want to be able to put in any subclass to employee to this function and still have it work ([Liskov Substitution Principle](../OOP/SOLID%20Principles.md)), but when you pass a derived class, you see the function is still calling the base class functions!

This is due to the way inherited classes are built, the base classes are built first and contain all of their functionality, and then the derived classes are made. We can use *Virtual Functions* to tell the program to always go for the most derived version of a function.

In the base class we add *virtual* before all the functions we want to be passed over when derived classes are being used. In the derived class, we add *override* to the functions which are overriding the virtual functions. Note that the function signature must be the same as the Base functions in order to be an override.

```cpp
class A
{
public:
	virtual std::string_view getName1(int x) { return "A"; }
	virtual std::string_view getName2(int x) { return "A"; }
	virtual std::string_view getName3(int x) { return "A"; }
};

class B : public A
{
public:
	// compile error, function is not an override (short int)
	std::string_view getName1(short int x) override { return "B"; } 
	// compile error, function is not an override (const)
	std::string_view getName2(int x) const override { return "B"; } 
	// okay, function is an override of A::getName3(int)
	std::string_view getName3(int x) override { return "B"; } 

};
```


#### Pure Virtual Functions, Abstract Classes, and Interfaces

We can create *abstract functions*  in C++ with virtual functions defined without a body. Abstract functions have no implementation, they just exist as a contract that demands that all derived classes must implement the function. This is done by simply setting the assigning a virtual function the value of 0, this is also called a *pure virtual function*.

```cpp
class Base
{
public:
    std::string_view sayHi() const { return "Hi"; } // a normal non-virtual function

    virtual std::string_view getName() const { return "Base"; } // a normal virtual function

    virtual int getValue() const = 0; // a pure virtual function

    int doSomething() = 0; // Compile error: can not set non-virtual functions to 0
};
```

When using pure virtual functions, you have to keep a few things in mind. First, any class with one or more abstract functions becomes an *abstract base class*, which means that it can not be instantiated. Second, any derived class must define a body for this abstract function or that derived class will be considered an abstract base class as well.

An *Interface Class* is a class that has no member variables, and where *all* of the functions are abstract. In other words, the class is purely a definition, and acts more as a schema than a class.

```cpp
class IErrorLog
{
public:
    virtual bool openLog(std::string_view filename) = 0;
    virtual bool closeLog() = 0;

    virtual bool writeError(std::string_view errorMessage) = 0;

    virtual ~IErrorLog() {} // make a virtual destructor in case we delete an IErrorLog pointer, so the proper derived destructor is called
};
```

All in all, you should greatly prefer composition over inheritance, and use interfaces over abstract base classes.