
Floating point uses an integer with fixed precision, scaled by an integer exponent of a fixed base. This is pretty much like scientific notation. This is required when dealing with [Binary](Binary.md) numbers as regular binary notation is incapable of describing values between discrete numbers. The term *floating point* refers to the fact that the numbers decimal point can "float" anywhere to the left, right, or between the significant digits of the number. This position is indicated by the exponent.

![](../../Attachments/Pasted%20image%2020230117004016.png)


## IEEE 754

The IEEE standardized the computer representation for binary floating-point numbers in standard *IEEE 754*. The standard describes three sections for the floating point number. The first bit is the sign bit, 0 is positive, 1 is negative. The next section is the exponent. Lastly is the Mantissa, otherwise referred to as the significand.

![](../../Attachments/Pasted%20image%2020230117004438.png)

The above image gives the bit layout for a single-precision 32 bit float. There is also a double precision float with 64 bits, and a half-precision float format used heavily in [machine learning](../../Machine%20Learning/Deep%20Learning/Neural%20Networks.md).

In the below image, we see an example of a IEEE 754 float and its representation of a decimal number. Notice how the exponent itself is negative. This is how we are able to show very small decimals. 
![](../../Attachments/Pasted%20image%2020230117004411.png)

While this system solves many problems, there are still some issues with floating point numbers in computing. There is the issue that very small rounding errors can impact logical comparisons. For example, you would expect the following expression to evaluate to True:
`0.1 + 0.1 + 0.1 == 0.3`

But many times, due to minute rounding differences, this will evaluate to False. A solution is to use ranges to look for approximate equality rather than absolute equal floating point numbers.

## Half Precision

The half-precision floating point is 16 bits and reduces the precision of the exponent term to 5 bits and the mantissa to 10 bits

![](../../Attachments/Pasted%20image%2020230310020346.png)

## bfloat16

The bfloat16 (Brain Floating Point) format occupies 16 bits. It has a greater exponent term than the Half Precision FP16 format. This is specifically to be used in [Neural Networks](../../Machine%20Learning/Deep%20Learning/Neural%20Networks.md) quantization.

![](../../Attachments/Pasted%20image%2020230310020513.png)