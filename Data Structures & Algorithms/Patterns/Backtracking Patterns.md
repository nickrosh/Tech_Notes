
Backtracking is a technique to [Depth First Search](../Algorithms/Depth%20First%20Search.md) through an entire decision tree, and returning all nodes that satisfy some condition. This algorithm usually runs in $O(2^n)$ when it has to generate a binary tree, and $O(n)$ when it has to traverse a tree.

## Subsets

*Subsets* are a key type of backtracking problem where you are given an input and you must return all possible subsets of the input without duplicated results. This is done by making a decision tree, at every level you have the choice of appending the number at index i or not appending it. At every level you increment the index until the index is at the end of the input, then you add all of the nodes to the result.

```python
def subsets(self, nums: List[int]) -> List[List[int]]:
	result = []
	def dfs(i, current_set):
		if i >= len(nums):
			result.append(current_set.copy())
			return

		# Left Branch, add the number
		current_set.append(nums[i])
		dfs(i+1, current_set)
		current_set.pop()

		# Right Branch, don't add the number
		dfs(i+1, current_set)
	dfs(0, [])
	return result
```


## Combinations

*Combinations* are very similar to subsets. Instead of an input list, you will usually get a range of numbers up to N where you need to either get all unique combinations, or even combinations that fulfill some criteria like summing up to a target value. Below is combination sum:

```python
def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
	result = []
	def dfs(i, current_set, current_sum):
		if current_sum == target:
			result.append(current_set.copy())
			return
		if i >= len(candidates) or current_sum > target:
			return
			
		# Left Branch, continue with the same number
		current_set.append(candidates[i])
		current_sum += candidates[i]
		dfs(i, current_set, current_sum)
		current_set.pop()
		current_sum -= candidates[i]

		# Right Branch, don't choose same number, move onto the next one
		dfs(i+1, current_set, current_sum)
	dfs(0, [], 0)
	return result
```


## Permutations

*Permutations* are every single variation of an input. So with an input of `[0,1]` the output would be `[0,1], [1,0]`. This is a little more complex than combinations or subsets. The way to solve this is by adding one of the possible inputs into every index, and then going down the decision tree of adding the other inputs into the other indexes. This can be done by removing the chosen input from the list of possible options, until the list is complete. Another way is by incrementally adding inputs and then different options for every possible index could go, so with a current list of `[1,3,4]`, the last number "2" could either go in front, after the 1, after the 3, etc.

![](../../Attachments/Pasted%20image%2020230407225209.png)

If the number of elements we are provided is $n$, then at each element, we can have $n!$ permutations. However, inside each for loop, in both the solutions, we have another for loop that runs from 0 to each permutation's length, which is $n$ because we are using all of the elements. As a result the time complexity is $O(n^2n!)$.

## Duplicated Input

A lot of the time, we are given a twist on regular Backtracking where the input has duplicated values, but we are still only allowed to give unique responses. This comes up in Subsets II and Combination Sum II. The solution is to sort then input and proceed normally with the left branch of the tree, but for the right branch, instead of incrementing the index of the input by one, keep incrementing the index until the element selected is not a duplicate of the next selection. This way, all solutions in the right branch will exclude the duplicated element, and thus you will only have unique answers.

For permutations with duplicates, you need to load the inputs into a frequency counter [Hash Map](../Data%20Structures/Hash%20Maps.md). Then, for each branch, just iterate through the hash map until it is empty. For each node visited, decrement the hash map. Whenever the hash map is completely empty, that is a valid permutation. Then when you go back up the call stack, increment the hash map again.

## More than 2 Decision Tree

The classic in backtracking is the binary decision tree between "adding" an element and not adding one. A common change up is when there are multiple options that should be taken at each level. This is also a way to optimize certain problems like combinations. Below is an example of converting phone number digits to all possible letters, this would be much more difficult if each decision was binary.

```python
def letterCombinations(self, digits: str) -> List[str]:
	if not digits:
		return []
	number_map = {
		"2": ['a', 'b', 'c'],
		"3": ['d', 'e', 'f'],
		"4": ['g', 'h', 'i'],
		"5": ['j', 'k', 'l'],
		"6": ['m', 'n', 'o'],
		"7": ['p', 'q', 'r', 's'],
		"8": ['t', 'u', 'v'],
		"9": ['w', 'x', 'y', 'z'],
	}
	result = []
	def dfs(i, current_set):
		if i == len(digits):
			result.append(''.join(current_set.copy()))
			return
		if i > len(digits):
			return

		for letter in number_map[digits[i]]:
			current_set.append(letter)
			dfs(i+1, current_set)
			current_set.pop()
	dfs(0, [])
	return result
```