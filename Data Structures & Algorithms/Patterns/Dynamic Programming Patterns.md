

## Fibonacci Sequence


## Shortest Path


## 0/1 Knapsack

Knapsack problems refer to the classic problem. Given N items each with a weight and value, what is the maximum value you can get while keeping weight under the value your bag can hold. This cannot be solved with [Greedy](Greedy%20Patterns.md) methods. This can be done with a decision tree where each decision is whether to add an item or not, similar to many [Backtracking](Backtracking%20Patterns.md) problems. This brute force method is done in $O(2^n)$ time. We can use top down Memoization to cache the results to bring the time complexity down to $O(nm)$ where n is the number of items and m is the capacity.

In 0/1 Knapsack, each item can only be used once in a solution. Thus, in the recursive solution, the decision tree will choose between using or skipping an element at each level. The prototypical example is where you have a series of items, each with a weight and value. Your goal is to maximize the value of items you can hold in a bag while staying under a set weight capacity. To accomplish this with Memoization, we create a decision tree, similar to backtracking problems, and go through the tree. At every single decision, we cache the value and current capacity, as this is the repeated work that comes up in other parts of the tree. This caching takes the complexity from $O(2^n)$ to $O(nm)$ where N is the number of items and M is the capacity. This problem can also be solved with true Dynamic Programming. You can create a 2-D DP grid where the Y axis is the item and the X axis is the weight/capacity. By going through every possible item and capacity from top to bottom, you can end up with the max given max capacity and all items. This can be optimized to only one array as the DP solution only depends on the array of prior items.

## Unbounded Knapsack

*Unbounded Knapsack* is very similar to 0/1 Knapsack, except the elements can be used an infinite number of times. The prototypical problem is Coin Change: with an array of coins, return the minimum number of coins needed to sum to a target number, and you can use each coin as many times as you like.

![](../../Attachments/Pasted%20image%2020230420191856.png)

Much like 0/1 Knapsack, we convert this problem into a Decision Tree. The difference is that for the left branch, we don't increment the index. So the left branch is now using the current element without incrementing the index and the right branch is incrementing the index without using the element. This way, we can get any number of duplicates for each element. When converted to the bottom up approach, you simply have a grid that can use previous answers on top of current ones.

## Longest Common Subsequence

[2D - Dynamic Programming](../Algorithms/Dynamic%20Programming.md) pattern where we are trying to find the longest subsequence that is shared by two strings. There are many problems that are similar to this. The key to this problem is that as we iterate through both strings and compare letters, the indexes at each string are the state variables for DP. Thus our recursion/DP Table is looking at both indexes and the longest subsequence up to that index. The solution to the overall problem is just the value at which both indexes reach the end of their respective strings.  
  
For example, in the prototypical example of LCS, our key decision is whether the letters at each index match, and what to do with the indexes. If they match, then we simply increment both indexes and add "1" to the result for a found match. If they don't match, then we have a split decision. We branch two different decisions of keeping one the same and incrementing the other. We then take the max of these two branches and return that. In a DP table, we would take the diagonal value and add 1 when there's a match. When there's not a match, we take the max of the above and left values, assuming we're going left to right top to bottom.

## Palindromes

Palindromes are another DP problem where we can optimize a problem by focusing on its subproblems. When we want to see if a string is a palindrome, we put [Two Pointers](Two%20Pointers.md) at either end and go in checking equality. In order to find palindromic substrings within the string, we need to iterate through every character, and do this same equality check going out from the iterated character. This means we do a palindrome sweep for every character. 

Also, checking either side of a single character will only give you the case for Odd length palindromes. To check for Even palindromes, at every index i, set the initial left pointer to "i" and the right pointer to "i+1". As soon as a pointer goes out of bound or the pointer's letters do not match, you can stop iterating.




