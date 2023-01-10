

## Scope

An identifier's *scope* determines where the identifier can be accessed within the source code.

Variables with **block (local) scope** can only be accessed within the block in which they are declared (including nested blocks). This includes:
- Local variables
- Function Parameters
- User-defined type definitions (such as enums and classes) declared inside a block

Variables and functions with **file (global) scope** can be accessed from the point of declaration until the end of the file. This includes:
- Global variables
- Functions
- User-defined type definitions (such as enums and classes) declared inside a namespace or in the global scope.

## Duration

A variable's *duration* determines when it is created and destroyed.

Variables with **automatic duration** are created at the point of definition, and destroyed when the block they are part of is exited. This includes:
- Local variables
- Function Parameters

Variables with **static duration** are created when the program begins and destroyed when the program ends. This includes:
- Global variables
- Static local variables

Variables with **dynamic duration** are created and destroyed by programmer request. This includes:
- Dynamically allocated variables

## Linkage

An identifier's *linkage* determines whether multiple declarations of an identifier refer to the same entity (object, function, reference, etc...) or not.

An identifier with **no linkage** means the identifier only refers to itself. This includes:
- Local variables
- User-defined type definitions (such as enums and classes) declared inside a block

An identifier with **internal linkage** can be accessed anywhere within the file it is declared. This includes:
- Static global variables (initialized or uninitialized)
- Static functions
- Const global variables
- Functions declared inside an unnamed namespace
- User-defined type definitions (such as enums and classes) declared inside an unnamed namespace

An identifier with **external linkage** can be accessed anywhere within the file it is declared, or other files (via a forward declaration). This includes:
- Functions
- Non-const global variables
- Extern const global variables
- Inline const global variables
- User-definer type definitions (such as enums and classes) declared inside a namespace or in the global scope

Identifiers with external linkage will generally cause a duplicate definition linker error if the definitions are compiled into more than one .cpp file (due to violating the one-definition rule). There are some exceptions to this rule (for types, templates, and inline functions and variables). Also note that functions have external linkage by default. They can be made internal by using the static keyword.

## Summary

|  Type | Example | Scope | Duration | Linkage | Notes |
|---|---|---|---|---|---|
|Local variable | `int x;`| Block | Automatic | None | |
|Static local variable | `static int x;`| Block | Static | None | |
|Dynamic variable | `int* x{new int{}};`| Block | Dynamic | None | |
|Function parameter | `void foo(int x)`| Block | Automatic | None | |
|External non-constant global variable | `int g_x;`| File | Static | External | Initialized or uninitialized |
|Internal non-constant global variable | `static int g_x;`| File | Static | Internal | Initialized or uninitialized |
|Internal constant global variable | `constexpr int g_x{1};`| File | Static | Internal | Must be initialized |
|External constant global variable | `extern const int g_x;`| File | Static | External | Must be initialized |
|Inline constant global variable | `inline constexpr int g_x;`| File | Static | External | Must be initialized |

