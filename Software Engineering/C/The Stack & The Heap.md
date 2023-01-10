
The memory that a program uses is typically divided into a few different areas, called segments:

- The code segment (also called text segment), where the compiled program sits in memory. The code segment is typically read-only.
- The bss segment (also called the uninitialized data segment), where zero-initialized global and static variables are stored.
- The data segment (also called the initialized data segment), where initialized global and static variables are stored.
- The heap, where dynamically allocated variables are allocated from.
- The call stack, where function parameters, local variables, and other function-related information are stored.

## Heap

The heap segment (also known as free-store) keeps track of memory used for dynamic memory allocation. This is done with `malloc`, and the address of this memory will be stored in a [Pointer](Pointers.md). You do not have to worry about the mechanics behind the process of how free memory is located and allocated to the user. However, it is worth knowing that sequential memory requests may not result in sequential memory addresses being allocated! 

When a dynamically allocated variable is deleted, the memory is "returned" to the heap and can then be reassigned as future allocation requests are received. Remember that deleting a pointer does not delete the variable, it just returns the memory at the associated address back to the operating system. The heap has the following advantages and disadvantages:

- Allocating memory on the heap is comparatively slow.
- Allocated memory stays allocated until it is specifically deallocated (beware memory leaks) or the application ends (at which point the OS should clean it up).
- Dynamically allocated memory must be accessed through a pointer. Dereferencing a pointer is slower than accessing a variable directly.
- Because the heap is a big pool of memory, large arrays, structures, or [classes](C++%20OOP.md) can be allocated here.


## Stack

The call stack (usually referred to as "The Stack") has a much more interesting role to play. The call stack keeps track of all the active functions (those that have been called but haver not yet terminated) from the start of the program top the current point of execution, and handles allocation of all function parameters and local variables. The call stack is simply a [Stack](Stacks.md) data structure and thus executes functions FIFO.

Here is a sequence of steps that takes place when a function is called:
1. The program encounters a function call.
2. A stack frame is constructed and pushed on the stack. The stack frame consists of:
- The address of the instruction beyond the function call (called the **return address**). This is how the CPU remembers whereto return to after the called function exits.
- All function arguments
- Memory for any local variables
- Saved copies of any registers modified by the function that need to be restored when the function returns
3. The CPU jumps to the function's start point
4. The instructions inside the function begin executing

When the function terminates, the following steps happen:
1. Registers are restored from the call stack
2. The stack frame is popped off the stack. This frees the memory for all local variables and arguments.
3. The return value is handled
4. The CPU resumes execution at the return address.

The stack has limited size. In fact, on Visual Studio its just 1MB, and on Linux and GCC/Clang it can be as large as 8MB. If the program tries to put tooo  much information on the stack, **stack overflow** will result. The stack has the following advantages and disadvantages:

- Allocating memory on the stack is comparatively fast
- Memory allocated on the stack stays in scope as long as it is on the stack. It is destroyed when it is popped off the stack.
- All memory allocated on the stack is known at compile time. Consequently, this memory can be accessed directly through a variable.
- Because the stack is relatively small, it is generally not a good idea to do anything that eats up lots of stack space. This includes passing by value or creating local variables of large arrays or other memory intensive structures.