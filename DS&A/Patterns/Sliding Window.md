

 In many problems dealing with and array (or linked list), we are asked to find or calculate something among all the subarrays (or sublists) of a given size. For example:

 >Given an array, find that average of all subarrays of 'k' contiguous elements in it.

  for instance for input
  `array = [1, 3, 2, 6, -1, 4, 1, 8, 2], k=5`

  The solution is:

  1. For the first 5 numbers (subarray from index 0-4), the average is: 
   $$
  \frac{(1+3+2+6-1)}{5} = 2.2
  $$
  2. For the next 5 numbers (subarray from index 1-5), the average is
  $$
  \frac{(3+2+6-1+4)}{5} = 2.8
  $$
  3. Continuing this process until the end, our output will be `output = [2.2, 2.8, 2.4, 3.6, 2.8]`  

  
## Brute Force

Naively, we can calculate the sum of every 5-element subarray of the given array and divide the sum by '5' to find the averages.

```python
def find_averages_of_subarrays(K, arr):
  result = []
  for i in range(len(arr)-K+1):
    # find sum of next 'K' elements
    _sum = 0.0
    for j in range(i, i+K):
      _sum += arr[j]
    result.append(_sum/K)  # calculate average

  return result
```

The time complexity for this will be $O(nk)$ where 'N' is the number of elements in the input array. The inefficiency here lies in the fact that for any two consecutive subarrays of 5 elements, we are calculating the overlapping part (4 elements) twice.

## Fixed Sliding Window: Average of Size K Subarrays

The efficient way to solve this would be to visualize each subarray as a sliding window of '5' elements. Instead of doing K calculations for every element in the array, all we have to do:

1. Load up first k elements into the running sum
2. Add the running sum to the results array
3. Subtract the last item of the window from the running sum
4. Add the next item in the array to the running sum, sliding the window forward
5. Repeat steps 2-4 until the end is reached.

This will save us from going through the whole subarray to find the `sum` and, as a result, the algorithm will reduce to $O(n)$. Just an add and subtract for every element in the array.

```python
def find_averages_of_subarrays(K, arr):
  result = []
  windowSum, windowStart = 0.0, 0
  for windowEnd in range(len(arr)):
    windowSum += arr[windowEnd]  # add the next element
    # slide the window, no need to slide if we've not hit the required window size of 'k'
    if windowEnd >= K - 1:
      result.append(windowSum / K)  # calculate the average
      windowSum -= arr[windowStart]  # subtract the element going out
      windowStart += 1  # slide the window ahead

  return result
```

## Variable Sliding Window: Smallest Substring with a Greater Sum

>Problem: Given an array of poistive numbers and a positive number 'S', find the length of the smallest contiguous subarray whose sum is greater than or equal to 'S'. Return 0 if no such subarray exists.

In this problem, our sliding window is not fixed, it will vary based on the array values. We can solve the problem with the following method:

1. Add up elements from the beginning of the array until the sum is greater than or equal to 'S'
2. These elements will constitute our sliding window. We will remember the length of this window as the smallest window so far.
3. After this, we will keep adding one element in the sliding window stepwise
4. In each step, we will also try to shrink the window from the beginning. We will shrink the window until the sum is smaller than 'S' again. Each shrinking step is as follows:
   
   1. Check if the current window length is the smallest so far, if so remember the length.
   2. Subtract the leftmost element of the element from the running sum.

Here's the code for this algorithm:

```python
import math


def smallest_subarray_sum(s, arr):
  window_sum = 0
  min_length = math.inf
  window_start = 0

  for window_end in range(0, len(arr)):
    window_sum += arr[window_end]  # add the next element
    # shrink the window as small as possible until the 'window_sum' is smaller than 's'
    while window_sum >= s:
      min_length = min(min_length, window_end - window_start + 1)
      window_sum -= arr[window_start]
      window_start += 1
  if min_length == math.inf:
    return 0
  return min_length
```

Time Complexity: $O(n + n)$ from the for loop and while loop, which is equivalent to Linear $O(n)$

Space Complexity: Constant $O(1)$

## Variable Sliding Window+HashMap: Longest Substring with K Distinct Characters

>Problem: Given a string, find the length of the longest substring in it with no more than `k` distinct characters. Assume `k` is <= string length

This maps closely to the previous case, except we will also use a **HashMap** to store the frequency of each character that shows up. Our Solution will be as follows:

1. First, insert characters into the window until we have `k` distinct characters in the **HashMap** 
2. We will remember the length of this window size as the longest so far
3. Keep adding one character in the sliding window stepwise.
4. In each step, shrink the window from the beginning if the count of distinct characters is greater than `k`
5. While shrinking, we decrement the frequency of the removed letter from the **HashMap**, if the frequency hits 0, we'll remove it from the HashMap
6. At the end of each step, we'll check if the current window length is the longest so far, if so remember the length

```python
def longest_substring_with_k_distinct(input_string, k):
  window_start = 0
  max_length = 0
  char_frequency = {}

  # in the following loop we'll try to extend the range [window_start, window_end]
  for window_end in range(len(input_string)):
    right_char = input_string[window_end]
    if right_char not in char_frequency:
      char_frequency[right_char] = 0
    char_frequency[right_char] += 1

    # shrink the sliding window, until we are left with 'k' distinct characters in 
    # the char_frequency
    while len(char_frequency) > k:
      left_char = input_string[window_start]
      char_frequency[left_char] -= 1
      if char_frequency[left_char] == 0:
        del char_frequency[left_char]
      window_start += 1  # shrink the window
    # remember the maximum length so far
    max_length = max(max_length, window_end - window_start + 1)
  return max_length
```

Time Complexity: $O(n+n)$ -> Linear $O(n)$

Space Complexity: Constant $O(1)$

## Longest Substring with Distinct Characters

>Given a string, find the **length of the longest substring**, which has all **distinct characters**.

```python
def non_repeat_substring(str1):
  window_start = 0
  max_length = 0
  char_index_map = {}

  # try to extend the range [windowStart, windowEnd]
  for window_end in range(len(str1)):
    right_char = str1[window_end]
    # if the map already contains the 'right_char', shrink the window from the beginning 
    # so that we have only one occurrence of 'right_char'
    if right_char in char_index_map:
      # this is tricky; if the current char we're checking has a previous 
      # index smaller than the currently set window_start, we'll just keep
      # the current window_start
      window_start = max(window_start, char_index_map[right_char] + 1)
    # insert the 'right_char' into the map. This also overwrites the previous
    # index if there was one
    char_index_map[right_char] = window_end
    # remember the maximum length so far
    max_length = max(max_length, window_end - window_start + 1)
  return max_length
```

## Longest Substring with Same Letters after Replacement

>Given a string with lowercase letters only, if you are allowed to **replace no more than `k` letters** with any letter, find the **length of the longest substring having the same letters** after replacement.

We can use a similar strategy to the **Longest Substring with Distinct Characters** problem.

1. Increment the right side of the window and add in letters until the substring isn't valid
2. set the result to the length of the subarray
3. while the substring isn't valid, shrink it and decrement the frequency of the letters that are getting removed


```python
def character_replacement(s: str, k: int) -> int:
    count = {}
    result = 0

    left = 0
    for right in range(len(s)):
        # increment frequency of right character, 1 if its the first instance
        count[s[right]] = 1 + count.get(s[right], 0)

       # while the substring isn't valid (# of non-max letters greater than k)
       # we get the max from the count table by do a scan of all values 
        while (right - left + 1) - max(count.values()) > k:
            count[s[left]] -= 1
            left += 1

        result = max(result, right - left + 1)
    return result
```

Now this above method requires a table scan of of the Count HashMap, which could possibly include all letters leading to a time complexity of $O(26n)$. This can be optimized with a clever change.

Instead of checking the entire count table every time for max frequency, we can just set a variable. And notably, it doesn't need to decrement if the actual character max frequency went down. The result will only ever be **K + Max Frequency** thus the max frequency will never need to decrement for the result which is the only thing we care about.

```python
def character_replacement(s: str, k: int) -> int:
    count = {}
    result = 0

    left = 0
    max_frequency = 0
    for right in range(len(s)):
        # increment frequency of right character, 1 if its the first instance
        count[s[right]] = 1 + count.get(s[right], 0)

        # check the max freqency whenever a character count is incremented
        max_frequency = max(max_frequency, count[s[right]])

       #  
        while (right - left + 1) - max_frequency > k:
            count[s[left]] -= 1
            left += 1

        result = max(result, right - left + 1)
    return result
```

Time Complexity: Linear $O(n)$

Space Complexity: Constant $O(1)$


## Permutations in a String


>Problem: Given a string and a pattern, find out if the string contains any permutation of the pattern.

## Solution:  


This problem is similar to Longest Substring with K Distinct Characters in that it needs a HashMap to store the frequency of characters. We can solve this problem as follows:

1. load up all the characters of the patters into a hashmap setting the frequency of each letter.
2. Iterate through the string, adding characters to the sliding window.
3. If a character being added matches a character in the **HashMap** then decrement its frequency in the map. If the frequency becomes zero, then we have a complete match.
4. If the number of characters matched is equal to the number of distinct characters in the pattern (i.e. total characters in the **HashMap**), we have our required permutation.
5. If the window size is greater than the length of the pattern, shrink the window. If a character leaving the **Window** was part of the pattern then increment its frequency in the **HashMap**.

```python
def find_permutation(str1, pattern):
    window_start, matched = 0, 0
    char_frequency = {}

    for character in pattern:
        if character not in char_frequency:
            char_frequency[character] = 0
        char_frequency[character] += 1

    # our goal is to match all the characters from the 'char_frequency' with the 
    # current window try to extend the range [window_start, window_end]
    for window_end in range(len(str1)):
        right_char = str1[window_end]
        if right_char in char_frequency:
            # decrement the frequency of matched character
            char_frequency[right_char] -= 1
            if char_frequency[right_char] == 0:
                matched += 1

        if matched == len(char_frequency):
            return True

        # shrink the window by one character
        if window_end >= len(pattern) - 1:
            left_char = str1[window_start]
            window_start += 1
            if left_char in char_frequency:
                if char_frequency[left_char] == 0:
                    matched -= 1
                char_frequency[left_char] += 1

    return False
```

Time Complexity: $O(N + M)$ where N is the length of the string, M is the length of the pattern, thus Linear $O(n)$

Space Complexity: $O(M)$ the length of the pattern



In a similar problem:

>Given a String and a Pattern, find all anagrams of the pattern in the given string. Return the starting indicies of the anagrams

## Solution:
we simple add a couple line to out previous code, where instead of returning a boolean, we append anagram locations to a list and return that list of indicies.

```python
def find_permutation(str1, pattern):
    window_start, matched = 0, 0
    char_frequency = {}

    for character in pattern:
        if character not in char_frequency:
            char_frequency[character] = 0
        char_frequency[character] += 1

    # Here we simple make another list to store the indicies
    result_indicies = []
    for window_end in range(len(str1)):
        right_char = str1[window_end]
        if right_char in char_frequency:
            # decrement the frequency of matched character
            char_frequency[right_char] -= 1
            if char_frequency[right_char] == 0:
                matched += 1

        if matched == len(char_frequency):
            # When we have a match (found an anagram) we append the start to the list
            result_indicies.append(window_start)

        # shrink the window by one character
        if window_end >= len(pattern) - 1:
            left_char = str1[window_start]
            window_start += 1
            if left_char in char_frequency:
                if char_frequency[left_char] == 0:
                    matched -= 1
                char_frequency[left_char] += 1

    return result_indicies
```

Time Complexity: Still $O(N + M)$ string and pattern length

Space Complexity: We add the length of the indicies list so $O(M + J)$