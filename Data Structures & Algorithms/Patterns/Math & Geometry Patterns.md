
Occasionally, there are math and geometry related questions in [DS&A Coding Interviews](../DS&A%20Coding%20Interviews.md). The challenge here is not in the coding, but in figuring out the math "trick". 


## Rotate Matrix

A classic question is how to rotate a square matrix 90 degrees clockwise, in-place. It would be trivial with more memory, but we must do creative value replacement if we are to do it in-place.

![](../../Attachments/Pasted%20image%2020230503002027.png)

The natural thing to try to do is to replace values going clockwise, in the direction of the rotation. However, this would require saving every single value being replaced to then place to the next element. Instead, if you save only the top left, and then go "backwards" by replacing it with the bottom left, then bottom right, etc. you will only have to save one value. Everything else will just being placed into the empty cell. You complete this as an array, shifting "top left" until you have the entire outer ring. Then you repeat the entire process inside the matrix until the entire matrix is rotated.

## Spiral Matrix

To iterate a matrix in a spiral fashion, simply set an index to left, right, top, and bottom. Then while left < right and top < bottom, iterate the four arrays starting at the top. When you finish an array, increment/decrement that array like top + 1. Eventually, this will lead you to the inside of the matrix and the end of the spiral.


## Fast Exponentiation

A trick question asks  to implement a function that simply takes a number "X" and exponent "N" and returns the power. The trick is that normal linear time multiplication is not good enough. The trick is to divide and conquer the multiplication. Instead of multiplying the base by itself N times, you square it N//2 times. At the end, you multiply the two halves with each other for the final result. Whenever the iterator (starting at N and going down by //2) is odd, multiply the result by the current sum. This is the "leftover" values that get reduced down by integer multiplication and will get added to the final answer. The running product will end up as the base to the power of a multiple of 2. When the iterator hits 1, then the product of the current result and the current running product will be the answer. This slight change takes it from $O(N)$ to $O(\log{n})$.

```python
if n < 0:
	x = 1/x
	n = -n
i = n
result = 1
current = x
while i > 0:
	if i % 2 == 1:
		result *= current
	current *= current
	i //= 2
return result
```
