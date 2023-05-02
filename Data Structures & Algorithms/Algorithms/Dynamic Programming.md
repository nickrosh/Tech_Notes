
Dynamic Programming is an optimization question. When you need to minimize and value or find the number of paths, or anything where you are searching a decision tree for the correct answers, DP will come into play.

DP at its core is the idea that you can break a problem down when a problem's subproblems each build towards the final solution. For example, in a college semester, to maximize your GPA, you would need to maximize the grade in each individual class. Because these classes can be individually optimized, we say this problem has an *optimal substructure*, where the min/max of subproblems add up to the min/max of the final problem. 

## Fibonacci

The simplest dynamic programming problem is the Fibonacci sequence. It is usually taught with recursion, but as we will see, the naïve recursive solution is very inefficient.

#### Top Down

The simple form of the Fibonacci is recursive as follows:

```python
def fib(n):
	if n <= 1:
		return 1
	return fib(n-1) + fib(n-2)
```

Suppose N=20, here's what the full recursive tree would look like

![](../../Attachments/Pasted%20image%2020221229165544.png)

There is a huge amount of repeated work here. The [Time Complexity](../Time%20&%20Space%20Complexity.md) for this is $O(2^n)$ (number of nodes in the tree is exponential). This brings up the first property in DP, *overlapping subproblems*. Now if we cached our calculations, we would eliminate all the repeated work

![](../../Attachments/Pasted%20image%2020221229165812.png)

This technique is called *Memoization*. We generally use a [Hash Map](../Data%20Structures/Hash%20Maps.md) to cache the calculated responses. Notice how we only calculate the left-most branch of the tree in this optimal method. The problem almost becomes like a [Linked List](../Data%20Structures/Linked%20Lists.md) when we only look at what we're really calculating

![](../../Attachments/Pasted%20image%2020221229170030.png)

Now you can see obviously we are only calculating for every value from zero to n, thus our time complexity becomes $O(n)$.


#### Bottom Up

Instead of starting at the top, the final value that we want to reach, the "true" Dynamic Programming solution starts at the bottom and build up to the target value. Instead of using a tree, we can simply define the above calculations as an array, starting at the base cases of f(0) = 0 and f(1) = 1

![](../../Attachments/Pasted%20image%2020221229172216.png)

We can then translate this to code

```python
def fib(n):
	dp = [0]*len(n+1)
	dp[1], dp[2] = 1, 1
	for i in range(3, len(n) + 1):
		dp[i] = dp[i-1] + dp[i-2]
	return dp[n]
```


The time efficiency is the same, but here we get to the real root of dynamic programming questions. We now have a *state transfer equation* that we define for any value of n:

$$
f(n) =
\begin{cases}
1  & \text{ if } n = 1,2 \cr
f(n-1) + f(n-2) & \text{ if } n \gt 2 \cr
\end{cases}
$$

This equation gives the answer to any possible input. Finding this *recurrence relation* is the key to solving a DP problem optimally, as we can actually optimize this problem further. As you can see, the equation only depends on two variables, the previous two values of f(n), thus we can optimize the code to only use two variables, bringing our space complexity to $O(1)$:

```python
def fib(n):
	one, two = 1, 1
	for i in range(3, len(n)):
	temp = one + two
	two = one
	one = temp
return one
```


## 2-D Dynamic Programming

The second style of dynamic programming is when performed on a grid, similar to a matrix [Graph](../Data%20Structures/Graphs.md). The classic question is LC 62 - *Unique Paths*. Starting from the top left of a m x n grid, how many unique paths are there to the bottom right? This can be decomposed into subproblems where you are computing the unique paths from each individual cell starting at the base cases, the bottom right. The entire bottom row will be zero, and all other cells will be a sum of the cell to the right, and below.

![](../../Attachments/Pasted%20image%2020230418004329.png)

This can be computed with Memoization, by caching the unique paths from every cell. Thus you can fetch pre-computed value rather than recalculating every single time. This would have a time and space complexity of $O(mn)$ . The bottom up true Dynamic Programming solution can be done by iterating from the bottom row, going up. With this technique, we can also optimize the space by only storing the previous row. Since every cell calculation only needs to right and below cells, we only need to save the most recently computed row at any time. This takes the memory complexity down to $O(n)$.


## Solution Framework

#### 1. Category

Most DP questions can be boiled down to a few [Dynamic Programming Patterns](../Patterns/Dynamic%20Programming%20Patterns.md). It's important to recognize these patterns as it allows us to frame a new question into an existing pattern that we already know. Each pattern also comes with some common variations. Like Target Sum is a 0/1 Knapsack problem, but has the requirement that EVERY item must be added to the solution. This is common variation that shows up in 0/1 problems.

#### 2. States

What variables do we need to keep track of in order to reach the optimal result? These state variables are what allow you to break down a problem into subproblems and cache them through Memoization, or use them in Tabulation. A 1-D DP problem will have one state variable, a 2-D problem will have at least two variables. For the classic Knapsack problem, the variables would be capacity and what items are available to use.

#### 3. Decisions

Dynamic programming at its core is about breaking a problem down to its subproblem in order to optimally make some decision, like picking the correct combination of items in the Knapsack. In order to make the optimal decision, we will have to try every decision first, like in [Backtracking](../Patterns/Backtracking%20Patterns.md). Decisions will have to bring us closer to the base case and lead us towards the question we want to answer. For example, in the Knapsack problem, the decision we are making at every step is: should we add item N or skip it and move onto the next item. We will create a decision tree that goes through every single possibility

#### 4. Base Case

Base cases need to relate directly to the conditions required by the answer we are seeking. This is why it is important for our decisions to work towards our base cases. Usually you will have two base cases, one valid and one invalid. Invalid would be like the index going past the end of the array. Valid would be like your running total equaling the target amount.

#### 5. Code

At this point, it should be trivial to code up the answer once you fully understand the question and all of the above components. Coding up the [Depth First Search](Depth%20First%20Search.md) Memoization solution requires correct recurrence and outputs. The Memoization solution will give you the optimal [Time Complexity](../Time%20&%20Space%20Complexity.md). Below is a rough template of how a Memoization solution will look.

```python
def dp(dp_state, cache):
	if dp_state in cache:
		return cache[dp_state]
	if dp_state is the base cases:
		return things like 0 or None

	result = calculate dp(dp_state) from dp(other_state)
	cache[dp_state] = result
	return dp[dp_state]
```

When customizing this template to your specific problem, there are four key items that need to be filled in:
1. What is `dp_state`?
2. What does the `dp` function return?
3. What is the base case(s)?
4. How to calculate `dp(dp_state)` from `dp(other_state)`?

#### 6. Optimize

Memoization should be optimal enough for most interviews, but you can further optimize the [Space Complexity](../Time%20&%20Space%20Complexity.md) by using a Bottom Up DP solution and minimizing the data structures to only what you need. For example, in a 2-D DP question like Coin Change, you don't actually need to save the entire DP grid into memory. Each cell's value depends only on the cells to its left and the cell above it. This means we can use a single DP array, and make the changes in-place. Keep iterating through the array and treat each cells current value like the value that would be above it in a full grid. This takes the memory complexity down to $O(N)$.


## Calculating Complexity

Calculating the Time complexity of a DP solution can get pretty complex. But the time complexity can be broken down to a simple formula:
$$\text{Time Complexity} = \text{Number of Function Calls}\times\text{Work Done per Function}$$
With Memoization and DP, we will avoid any repeated work, so the number of function calls will equal the number of unique states. Let's say we have 3 states: M, T, N. Thus we have $M \times T \times N$ function calls to calculate. Recursive DP calls can be treated as constant, so we only need to look at the work being done inside of a function. Let's say in each function, we are looping through one of the variables N, so the work done per function call would be $O(N)$. Thus our final complexity for the problem is $O(M \times T \times N^2)$.