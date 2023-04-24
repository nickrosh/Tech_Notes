
The biggest fear for most candidates is getting stuck during an interview. Fortunately there are structured ways to approach coding interview questions that will increase your chances of solving. When given a question, candidates should start by asking clarifying questions and discussing a few possible approaches. However this is where most candidates tend to get stuck. Note that not all techniques will apply to every interview, and you can also use multiple techniques on a single problem. As you apply them techniques during your practice, you will build the intuition for which technique will be useful for the problem at hand.

#### 1. Visualize the problem by drawing it out

Ever wondered why coding interviews are traditionally done on whiteboards? Whiteboards make it easy to draw diagrams which helps with problem solving! A huge part of coding is understanding how the interval state of a program changes and diagrams are super useful tools for representing the internal data structures state. If you are having a hard time understand how the solution is obtained, come up with a visual representation of the problem and if necessary, the internal state at each step.

For example, looking at *return all elements of a matrix in spiral order* helps you tremendously when drawn out.

#### 2. Think about how you would solve the problem by hand

Solving the problem by hand is about solving the problem without writing any code, like how a non-programmer would. This already happens naturally most of the time when you are trying to understand the example given to you.

What some people don't realize is that sometimes a working solutions is simply a code version of the manual approach. If you can come up with a concrete set of rules around the approach that works for every example, you can write the code for it. While you might not arrive at the most efficient solution by doing this, it's a start which will give you some credit.

For example. *Validate a Binary Search Tree* without any code is just looking at each subtree to verify its validity and going up the chain. You just take this intuition as a recursive solution.

#### 3. Come up with more examples

Coming up with more examples is something useful you can do regardless of whether you are stuck or not. It helps you to reinforce your understanding of the question, prevents your from prematurely jumping into code, helps you to identify a pattern which can be generalized to any input, which is the solution! Lastly, the multiple examples can be used as test cases at the end when verifying your solution.

#### 4. Break the problem down into smaller subproblems

If the problem is large, start with a high-level function and break it down into smaller constituting functions, solving each one separately. This prevents you from getting overwhelmed with the details of doing everything at once and keeps your thinking structured. Doing so makes it clear to the interviewer that you have an approach, even if you don't manage to finish coding all of the smaller functions.

#### 5. Apply common Data structures and algorithms at the problem

Unlike real-world software engineering where the problems are usually open-ended and might not have clear solutions, coding interview problems tend to be smaller in nature and are designed to be solvable within the duration of the interview. The number of common structures and algorithms is finite and a hacky approach which works is going through all the common data structures and applying them to the problem.

These are the common data structures to keep in mind and try, in order of frequency they appear in coding interview questions:
- **[Hash Maps](Data%20Structures/Hash%20Maps.md)**: Useful for making lookup efficient. This is the most common data structure used in interviews and you are guaranteed to have to use it
- **[Graphs](Data%20Structures/Graphs.md)**: If the data is presented to you as associations between entities, you might be able to model the question as a graph and use some common graph algorithm to solve the problem. 
- **[Stacks](Data%20Structures/Stacks.md) and [Queues](Data%20Structures/Queues.md)**: If you need to parse a string with nested properties (such as a mathematical equation), you will almost definitely need to use stacks
- **[Heaps](Data%20Structures/Heaps.md)**: Questions involving scheduling/ordering based on some priority. Also useful for finding the max K/min K/ median elements in a set.
- **[Trees](Data%20Structures/Trees.md)/[Tries](Data%20Structures/Tries.md)**: Do you need to store strings in a space-efficient manner and look for the existence of strings (or at least part of them) very quickly?


## How to Optimize your Time Complexity

After you've come up with an initial solution to the problem, you interviewer would most likely prompt you to optimize the solution by asking "Can we do better". The following techniques help you further optimize the time complexity of your solution.

#### 1. Identify the Best Theoretical Time Complexity of the problem

The Best Theoretical Time Complexity (BTTC) of a solution is a time complexity you know that you cannot beat. It's important to know BTTC so you don't go down the rabbit hole of trying to find a solution that is faster than the BTTC. The BTTC is not necessarily achievable in practice, it just means you can never find a real solution that is faster than it. Some people think the BTTC is simply the total number of elements in a data structure, because you need to go through each element once. This is **not always true**. The most famous example would be finding a number in a sorted array of numbers. The sorted property changes things:
- Finding a number would be $O(\log{n})$ since you can use [Binary Search](Algorithms/Binary%20Search.md).
- Finding the largest number would be $O(1)$ because it's the last value in the array.

With the correct BTTC determined, you now know the time complexity of the optimal solution lies between the brute force method and the BTTC. If your solution already has the BTTC and the interviewer is asking you to optimize further, there are usually two things they are looking out for:
- Do even less work. Your solution could be $O(n)$ but making two passes of the array and the interviewer is looking for the solution that uses a single pass
- Use less space. Refer to the section on space optimization

#### 2. Identify overlapping and repeated computation

A naive/brute force solution often executes the same operation over and over again. When the code is doing an expensive operation that has been done before, take a moment to step back and consider if you can reuse results from previous computations. [Dynamic Programming](Algorithms/Dynamic%20Programming.md) is the most obvious type of questions you can entirely leverage past computations. There are non-DP questions that can leverage this technique too, although not as straightforward and might require a preprocessing step.

#### 3. Try different data structures

Choice of data structures is key to coding interviews. It can help you to reach a solution for the problem, it can also help you to optimize your existing solution. Is lookup time slowing your algorithm down? You can use a Hash Map to make all lookups in constant time.

#### 4. Identify redundant work
Here are a few examples of doing redundant work. Although making these mistakes might not change the overall time complexity of your code, you are also evaluated on coding abilities, so it's important to write as efficient code as possible.
- **Don't check conditions unnecessarily**
- **Mind the order of checks**: in python checks evaluate from left to right in an IF statement
- **Don't invoke methods unnecessarily**: if you have to refer to a property multiple times in your function and that property has to be derived from a function call, cache the result as a variable
- **Early Termination**: Stop after you already have an answer and return the answer immediately. Terminate early from loops when possible
- **Minimize work inside loops**: don't re-compute the same values in every iteration of a loop. Anything that won't change can be computed outside of the loop to speed things up
- **Be Lazy**: Lazy evaluation is a strategy which delays the evaluation of an expression until its value is needed. Can be as simple as checking the input to not be empty.


## How to Optimize your Space Complexity

Most of the time, Time Complexity is more important than space complexity. But when you have already reached the optimal time complexity, the interviewer might ask you to optimize the space your solution is using (if it is using extra space). Here are some techniques you can use to improve the space complexity of you code.

#### 1. Changing data in-place/overwriting input data

If your solution contains code to create new data structures to do intermediate processing/caching, memory space is being allocated and can sometimes be seen as a negative. A trick to get around this is by overwriting values in the original input array so that you are not allocating any new space in your code. However, be careful not to destroy the input data if your need to use it in subsequent parts of your code.

Note in real engineering, this is generally frowned upon. This is an interview optimization.

#### 2. Change a data structure

You could be using a suboptimal data structure for the problem. For example if you want to only know what numbers exist in a list and not their frequencies, you should store this as a Hash Set instead of a array with duplicate values. Also [Tries](Data%20Structures/Tries.md) are very efficient for storing strings.