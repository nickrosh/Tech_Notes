
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