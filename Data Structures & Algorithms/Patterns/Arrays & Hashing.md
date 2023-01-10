
One of the simplest patterns that takes advantage of [Arrays](Arrays.md) and [Hash Maps](Hash%20Maps.md). 

## Anagrams

An *Anagram* is defined as a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once. E.g. "rat" and "tar". We can define two words as being anagrams if they have identical letter counts

One method is a frequency [Hash Map](Hash%20Maps.md), another is an array with 26 elements (for each letter in the alphabet). The benefit of the array is that it can be converted into a tuple and used as a Hash Key itself

## Top K Frequent Elements

You can get the top k frequent elements in an array in linear time by doing something similar to bucket sort

![](../../Attachments/Pasted%20image%2020221207171047.png)

Normally, you would make a [Hash Map](Hash%20Maps.md) and put the element-value as the key and the frequency as the value. But this would mean the key space would be unbounded, and you would also be unable to know which keys correspond to the highest frequency values. 
Instead, flip the reasoning, so frequency will be the key, and value will be a list of elements that correspond to the frequency. The length of the array will be N + 1, the number of elements in the input + 1 (for zero frequency).

## Kadane's Algorithm

