
Boolean Algebra is computation where the values of variables are all True/False, and various operations with take the conjunction and intersection of these variables. We would know these better as AND, OR, NOT, and other operations. As it turns out, we have lightning fast electronic [switches](CMOS.md) that we can use to build [Logic Gates](Logic%20Gates.md) that will allow us to perform these operations. Now in this system, we represent everything as a "bit" which is just a '1' or '0'. The one means true and zero means false in Boolean terms. We want to represent all of our numbers in a base which is a power of 2 as all functions are based on this True/False calculation. Binary (base 2) is the obvious choice for digital electronics as it can be represented by High [Voltage](../Electricity.md) and Ground. The default decimal system is base 10, as in ten different numbers can be represented by a single digit. 

We can represent any number in binary by using each digit as a different power of two. For example, in the below example, we show how 183 is represented in binary. 

![](../../Attachments/Pasted%20image%2020230116230314.png)

Due to how many individual bits are used in the simplest calculations, we usually refer to "bytes" which just means 8 bits. In the above example that bit string `10110111` is a byte that represents decimal 183.

## Negative Numbers

To represent negative numbers in binary, we reserve the most significant bit as the *sign bit*. When it is 0, the number is positive. When it is 1, the number is negative. This cuts down our range of numbers by half, but allows us to represent negative numbers. This is called *signed-magnitude* representation of negative numbers. 

![](../../Attachments/Pasted%20image%2020230116234331.png)

There is also *Two's Complement* which is easier for machines to calculate with. In 2's complement the bits are wrapped around and are pretty much the opposite of signed-magnitude. 

![](../../Attachments/Pasted%20image%2020230116234911.png)


## Hexadecimal

If we wanted to represent all bytes by their binary representation, we could barely fit anything on the screen. So the most common representation to make binary more human readable is *Hexadecimal* format, base 16. the numbers that correspond to decimal 10-15 are represented by the letters A-F. To show that a number is hex, we prefix it with `0x`. For example 0x17 hex would be 23 in decimal. 

![](../../Attachments/Pasted%20image%2020230116221123.png)