
One of the simplest patterns that takes advantage of [Arrays](../Data%20Structures/Arrays.md) and [Hash Maps](../Data%20Structures/Hash%20Maps.md). 

## Anagrams

An *Anagram* is defined as a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once. E.g. "rat" and "tar". We can define two words as being anagrams if they have identical letter counts

One method is a frequency [Hash Map](../Data%20Structures/Hash%20Maps.md), another is an array with 26 elements (for each letter in the alphabet). The benefit of the array is that it can be converted into a tuple and used as a Hash Key itself

## Top K Frequent Elements

You can get the top k frequent elements in an array in linear time by doing something similar to [Bucket Sort](../Algorithms/Bucket%20Sort.md).

![](../../Attachments/Pasted%20image%2020221207171047.png)

Normally, you would make a [Hash Map](../Data%20Structures/Hash%20Maps.md) and put the element-value as the key and the frequency as the value. But this would mean the key space would be unbounded, and you would also be unable to know which keys correspond to the highest frequency values. 
Instead, flip the reasoning, so frequency will be the key, and value will be a list of elements that correspond to the frequency. The length of the array will be N + 1, the number of elements in the input + 1 (for zero frequency).

## Kadane's Algorithm

To find a non-empty subarray with the largest sum, the optimal solution is with *Kadane's Algorithm*. That is, we keep going through the array and compute a rolling sum. As soon as we reach a negative sum for a section, we know that that section can **never** contribute to the max subarray. Thus we can ignore that section and reset the rolling sum to zero. This can also be done as a [Sliding Window](Sliding%20Window.md) where we have a variable sliding window. As soon as the window sum is less than zero, move the left pointer up to the right pointer.

![](../../Attachments/Pasted%20image%2020230114150322.png)


## Prefix Sum

When you take the rolling sum of an array, you are calculating the *prefix sum*:

![](../../Attachments/Pasted%20image%2020230114175802.png)

This prefix calculation is actually very helpful in many types of problems that ask for subarrays that sum to a specific number, or questions about finding the sum of a specific subarray. The reason being, let's say you want to find the sum of elements of index n to n+5, if you calculated that sum by iterating the subarray, and then needed to do that n times, you would have a quadratic [Time Complexity](../Time%20&%20Space%20Complexity.md). If you just take the Prefix sum at index n+10 and subtract the prefix sum at index n, you would have the sum in one operation. This is going off a similar idea as Kadane's, we can take the total running sum, and chop off the parts we don't want

There is also postfix sum, which is the same idea in the opposite direction. Starting at the end of the array, get the rolling sum back backwards to the start of the array. Combined with prefix sum, this lets us answer questions that ask about getting subarrays from either side of the array.


## Counting Letters

A nice compact trick for detecting anagram and permutations of words is the fact that there are a limited number of letters. This coupled with the fact that we can take the *ordinal* value of a letter (e.g. 'a' = 0, 'b' = 1, etc.) we can actually store the count of each letter in an array of size 26. We can then convert this array to a tuple so that it can be hashed, and that can be the key for a python dictionary!

This is very handy and efficient when we are trying to group things like permutations where the relative location of letters don't matter, just the letter frequency. Here is an example of grouping anagrams by using the count array, converted to a tuple (only immutable types can be hashable), as the key of a dictionary to group equal frequency lists:
```python
def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
	anagrams = {}
	for word in strs:
		count = [0] * 26
		for letter in word:
			position = ord(letter) - ord('a') # This gets the index a=0, b=1, etc.
			count[position] += 1
		count = tuple(count)
		if count not in anagrams:
			anagrams[count] = []
		anagrams[count].append(word)
	return list(anagrams.values())
```


## Any Hashable Key

Remember in python, **ANY** hashable object (non-mutable) can be turned into a hash key. This includes Class instances, Tuples (not arrays!), strings, etc.


## Key-Object HashMap

One key doesn't have to map to one primitive, it can map to any object. This includes connecting the values to [Linked Lists](../Data%20Structures/Linked%20Lists.md) nodes (this is how LRU cache is implemented).