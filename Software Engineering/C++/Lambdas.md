
*Lambdas* or anonymous functions allow us to define a function within another function. The nesting is important, as it allows us both to avoid namespace naming pollution, and to define the function as close to where it is used as possible (providing additional context). The syntax for lambdas are pretty alien:

`[captureClause] (parameters) -> returnType {statements}`

Now let's build some intuition for why lambdas are so useful. Take for example some code where we are iterating through an [array](../../Data%20Structures%20&%20Algorithms/Data%20Structures/Arrays.md):

```cpp
bool containsNut(std::string_view str)
{
    // std::string_view::find returns std::string_view::npos if it doesn't find
    // the substring. Otherwise it returns the index where the substring occurs
    // in str.
    return (str.find("nut") != std::string_view::npos);
}

int main()
{
    std::array<std::string_view, 4> arr{ "apple", "banana", "walnut", "lemon" };

    // Scan our array to see if any elements contain the "nut" substring
    auto found{ std::find_if(arr.begin(), arr.end(), containsNut) };
```

While this works, it could be improved. The root of the issue is that `std::find_if` requires that we pass it a [function pointer](../C/Functions.md). Because of that, we are forced to define a function that's only going to be used once, that must be given a name, and that must be put in the global scope (because functions can't be nested). The function is also short, it's almost easier to discern what it does from the one line of code than from the name and comments. Now rewriting the example with a lambda:

```cpp
constexpr std::array<std::string_view, 4> arr{ "apple", "banana", "walnut", "lemon" };

  // Define the function right where we use it.
const auto found{ std::find_if(arr.begin(), arr.end(),
					   [](std::string_view str) // here's our lambda, no capture clause
					   {
						 return (str.find("nut") != std::string_view::npos);
					   }) };
```

In the above example, we defined a lambda right where it was needed, which is sometimes called a *function literal*. However, to make things more clear, we can also assign a lambda to a variable, much like initializing a variable to a literal value. Here we make a variable named `isEven` to store the lambda and then use it in the `all_of` line.

```cpp
auto isEven{
  [](int i)
  {
    return ((i % 2) == 0);
  }
};

return std::all_of(array.begin(), array.end(), isEven);
```

The lambda has a unique type that is not revealed to the user, so we use either `auto` or `std::function` to deduce the type of the lambda. `auto` is preferable, but before C++20 it can't be used if you can't initialize the variable with the lambda. In that case, use `std::function`.

One thing to be aware of is that a unique lambda will be generated for each different type that `auto` resolves to. Most of the time this is inconsequential, but if you are using [static variables](Scope,%20Duration,%20&%20Linkage.md) in the lambda, keep in mind that if different lambdas are created, it might cause unintended behavior.

You can also explicitly define the return type with `-> type` after the parameters. This is better to avoid situations where the compiler might not be able to deduce the return type. The [standard library](C++%20STL.md) makes extensive use of lambdas combined with the algorithm library for powerful multi-threaded algorithms that end up looking more readable too

## Capture Clause

Unlike nested blocks, where any identifier defined in an outer block is accessible in the scope of the nested block, lambdas can only access specific kinds of identifiers: global identifiers, entities that are known at compile time (`constexpr`), and entities with static storage duration. Here is where the capture clause comes in.

The capture clause is used to (indirectly) give a lambda access to variables available in the surrounding scope that it normally would not have access to. All we need to do is list the entities we want to access from within the lambda as part of the capture clause.

```cpp
int health{ 33 };
int armor{ 100 };
std::vector<CEnemy> enemies{};

// Capture health and armor by value, and enemies by reference.
[health, armor, &enemies](){};
```

Just like functions, we can capture objects by value and by reference. Keep in mind that because captured variables are members of the lambda object, their values are persisted across multiple calls to the lambda. 

A *Default Capture* captures all variables that are mentioned in the lambda. Variables not mentioned in the lambda are not captured if a default capture is used. Just make sure the default capture is the first in the capture clause.
- To capture all used variables by value, use a capture value of `=`
- To capture all used variables by reference, use a capture values of `&`

So extending our previous example:

```cpp
int health{ 33 };
int armor{ 100 };
std::vector<CEnemy> enemies{};

// Capture health and armor by value, and enemies by reference.
[health, armor, &enemies](){};

// Capture enemies by reference and everything else by value.
[=, &enemies](){};

// Capture armor by value and everything else by reference.
[&, armor](){};
```

Be careful when you capture variables by reference, especially with a default reference capture. The captured variables must outlive the lambda. Try to avoid mutable lambdas. Non-mutable lambdas are easier to understand and don't suffer from mutation bugs and even more dangerous issues that arise when you add parallel execution.