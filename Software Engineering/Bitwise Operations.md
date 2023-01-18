
We can take advantage of fast, simple higher-level arithmetic operations directly on [bits](../Electrical%20Engineering/Digital/Binary.md). Using special tricks like shifting bits to the left, we can take advantage of binary arithmetic to calculate operations faster than traditional addition, multiply, divide, etc. Using operators like [AND](../Electrical%20Engineering/Digital/Logic%20Gates.md) and others, we can check for 0 and other unexpected calculations.

In C, C++, and Python the following are the bitwise operators:

| Symbol | Operator    |
| ------ | ----------- |
| `&`    | bitwise AND |
|  \|   | bitwise OR  |
| `^`    | bitwise XOR |
| `<<`   | left shift  |
| `>>`   | right shift |
| `~`    | bitwise NOT |

## Bit Shifting

Left shifting a number by `n` is the exact same as multiplying it by $2^n$ for both positive and negative values.
![](../Attachments/Pasted%20image%2020230116233656.png)

Right shifting a positive number by `n` is the exact same as integer division (round to zero) by $2^n$. This is called the Logical shift

![](../Attachments/Pasted%20image%2020230116233741.png)

Right shifting a negative number by `n` is the same as division rounded to negative infinity (always round to lower value) by $2^n$. This is called the Arithmetic shift.

![](../Attachments/Pasted%20image%2020230116233757.png)


## Bitwise Operations

Assume every variable is 32 bits.

**AND**:
- x & 0 = 0
- x & x = x
- x & 0xFF = x
- x & y = y & x

**OR**:
- x | 0 = x
- x | x = x
- x | 0xFF = 0xFF
- x | y = y | x

**XOR**:
- x ^ 0 = x
- x ^ x = 0
- x ^ 0xFF = ~x
- x ^ y = y ^ x
- x ^ y ^ y = x


To set bit N to 1:
`x |= (0x1 << N)`

To clear bit N to 0:
`x &= ~(0x1 << N)`

To toggle bit N:
`x ^= (0x1 << N)`


## Bit Masking

A bit mask is a binary pattern used to modify another binary pattern. Say you just want to keep the lower half of a 32 bit integer. You could just AND the integer with 0x0000FFFF. This keeps the original bottom half while clearing out the top half.