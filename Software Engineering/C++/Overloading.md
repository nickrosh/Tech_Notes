
## Function Overloading

Functions can be overloaded such that calling the same named function with different parameter types will call different functions, this is a form of polymorphism. This is a clean way to make a single function name handle different scenarios. However, if the operation will always be the same, you should probably use [Templates](Templates.md).

```c++
int add(int x, int y) { // Integer version
	return x + y;
}

double add(double x, double y) { // Floating Point version
	return x + y;
}
```


## Operator Overloading

We can also overload operators like plus(+) and minus(-) and even unary operators like subscript([ ]). This is very handy when creating a [class](C++%20OOP.md) that you want to exhibit natural behavior. For example, if  you make a container class, the user will expect to be able to subscript the object. Or if you have two objects, you can create a function that overloads what the multiplication of two objects would look like, and what exact member variables are multiplied.

```c++
Cents operator+(const Cents& c1, const Cents& c2) {
	return Cents{c1.getCents() + c2.getCents()};
}
```

In this example, we see how to overload The (+) symbol between two objects of our "Cents" class. We take in the references to the objects and return a new object that is the sum of their cent values. In this example we use the class's getter function to `.getCents()`, but we could have also used the private member variable for cents directly if this overload function was made a friend by the Cents class.

To do unary operator overloads like assignment (=) and subscript([ ]), the operator must be a member function of the class itself.

```c++
class IntList {
private:
	int m_list[10]{};
public
	int& operator[](int index) {
		return m_list[index];
	}
}
```

In this example, we see how you can also do an overload of a unary operator as a member function of a class. 

#### Rules of Thumb

The following rules of thumb can help you determine which form is beset for a given situation:

- If you're overloading assignment(=), subscript([]), function call(()), or a member selection(->), do so as a member function.
- If you're overloading a unary operator, do so as a member function.
- If you're overloading a binary operator that not modify its left operand (e.g. operator+), do so as a normal function (preferred) or friend function
- If you’re overloading a binary operator that modifies its left operand, but you can’t add members to the class definition of the left operand (e.g. operator<<, which has a left operand of type `ostream`), do so as a normal function (preferred) or friend function.
- If you’re overloading a binary operator that modifies its left operand (e.g. operator+=), and you can modify the definition of the left operand, do so as a member function.