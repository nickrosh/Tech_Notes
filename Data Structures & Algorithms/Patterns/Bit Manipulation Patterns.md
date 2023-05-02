
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