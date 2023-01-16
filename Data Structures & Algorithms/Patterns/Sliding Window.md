
*Sliding Window* can be seen as a special case of [Two Pointers](Two%20Pointers.md). Instead of two pointers are arbitrary locations, the window will always be a right pointer iterating through the array, and a left pointer that moves based on certain parameters according to the problem.

## Fixed Size Sliding Window

Say you need to find the number of subarrays of size *k* that fulfill some condition. This is a simple example of a fixed size sliding window. Simply iterate through the array with the right pointer, and then when the `right - left + 1` value is equal to or greater than *K*, you can advance the left pointer. You add elements as right advances, and remove elements as left advances. This way, your window will always be size K and you will be able to perform calculations based on the window.


## Variable Size Sliding Window

In problems like "longest substring without repeating characters", you are looking for a window of unknown size that matches the conditions. In this case we will use a variable size sliding window. We will iterate through the array with the *right* pointer like usual, but now we will make the movement of the left pointer contingent on the problems condition. Usually, we will make the movement of the left pointer a while loop, as the left pointer can move multiple elements, unlike the fixed sliding window.

In many of these types of problems, we will need a secondary data structure to store frequencies, occurrences, etc. The most common structure is a [Hash Map](../Data%20Structures/Hash%20Maps.md).