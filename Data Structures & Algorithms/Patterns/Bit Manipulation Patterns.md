
[Bitwise Operations](../../Software%20Engineering/Bitwise%20Operations.md) come up occasionally in [DS&A Coding Interviews](../DS&A%20Coding%20Interviews.md), although not often. This is because bit problems generally require a "trick" that you either know or don't know.\


## Counting Bits

An easy way to count the number of "1s" in the [Binary](../../Electrical%20Engineering/Digital/Binary.md) representation of a number is to continuously right shift and count the ones place until the value goes to zero. This is a $O(n\log{n})$ solution. We can use [Dynamic Programming](../Algorithms/Dynamic%20Programming.md) to speed this up. Notice how the pattern of ones and zeros for any particular number is just one more than the same number without the most significant bit, e.g. `bx1101 = 1 + bx101`. Thus, we just make a DP array and for each number, calculate `dp[n] = dp[n - most_significant_bit_offset]`.

## Reverse Bits

Let's say we are asked to reverse the bits of a 32 bit integer. Intuitively, we can iterate through each input bit shifting to the right, and if the place is a 1, we can logical OR it with a result integer that we shift to the left. This way, we are mirroring the ones as we iterate 32 times.

The more complex and efficient solution involves using bit masking and shifting. This method doesn't even require iteration, just bit manipulation magic. This runs in constant time and space. It works like this:
1. Break the original 32-bit into 2 blocks of 16 bits, and switch them
2. Then break the 16-bit blocks into 2 blocks of 8 bits, and switch them
3. Continue to break the blocks into smaller blocks, until you reach the level with a block of 1 bit
4. At each of the above steps, we merge the intermediate result into a single integer which serves as the input for the next step.

```python
def reverseBits(self, n):
	n = (n >> 16) | (n << 16)
	n = ((n & 0xff00ff00) >> 8) | ((n & 0x00ff00ff) << 8)
	n = ((n & 0xf0f0f0f0) >> 4) | ((n & 0x0f0f0f0f) << 4)
	n = ((n & 0xcccccccc) >> 2) | ((n & 0x33333333) << 2)
	n = ((n & 0xaaaaaaaa) >> 1) | ((n & 0x55555555) << 1)
	return n
```

## Sum of Two Integers  
  
How do you sum two integers without using the addition or subtraction operator? you can use alternating XOR and AND operators. First you XOR the two numbers, this will give you the remainder number. You also calculate the AND to get the carry bits, we then shift these once to the left as they are carries. We replace the first number with our XOR result and the second number with the carry result, and continue this process until the carry is zero. At this point, the first number is the result.  
  
In an [Interpreted](../../Systems%20Software/Interpreter.md) language like Python, integers grow limitlessly, and are not bound to set 32 bits, this becomes a problem when dealing with negative numbers (two's complement). Say we are adding -2 and 3, which = 1. In Python this would be (110 + 011 = 001). This seems fine but what happened to the extra carry bit at the front? Normally we should ignore it, but Python keeps it going. In order to fix this, we make a mask of 32 one's (8 F's in hex) and AND that with the carry whenever we are checking to end the loop. That way, we won't continue the operation if the carry is out of bounds  
  
  
## Reverse Integer  
  
To reverse an integer while staying in the 32 bit range, iterate through the int and sum up its digits backwards. You do this by modding the integer by 10 and then adding that to ten times the running sum. This way, you keep moving the digits up and add them modding through the entire input. To check for overflows, make sure the result does not go over or under the int MIN and MAX (+/- $2^{31}$). After each round of modding and adding, integer divide the input by 10, and continue until the input hits zero.  
  
Note that in [Python](../../Software%20Engineering/Python/Python%20101.md), the operators "%" and "//" have strange behavior with negative numbers. `-1 % 10 = 9` and `-1 // 10 = -1`. Instead of those, use the float versions `math.fmod = %` and `/ = //`, and then cast the values into integers.