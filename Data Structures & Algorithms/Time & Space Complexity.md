**Time Complexity** quantifies the amount of time taken by an algorithm to run as a function of the length of the input 'N'. It is assumed that constant time 'c' is taken to execute one operation. Thus if you had an [Array](Data%20Structures/Arrays.md) of size N and you iterated through the whole thing, the algorithm would be $O(n)$ time complexity. Note that we generally prefer better time complexity, even at the expense of space.

**Space Complexity** quantifies the space that the algorithm takes up to run as a function of the length of the input. For example, if you needed to save an array the same size as the input, then your space complexity would be $O(n)$.

### Big O Notation

Big O notation is the mathematical notation for the worst-case scenario of an algorithm. When the complexity for a calculation is a polynomial, it will reduce to the highest order term. Additionally, we do not include any coefficients.

$$O(3n^2 + n\log{n} + 2n ) = O(n^2)$$

The best complexity is constant time $O(1)$, then comes log time $O(\log{n})$ and then linear time $O(n)$. After this is $O(n\log{n})$ which is the best complexity for sorting, so it is commonly seen.


![](../Attachments/Pasted%20image%2020221220004601.png)

### Common Data Structures Operations

![](../Attachments/Pasted%20image%2020221220004814.png)



### Common Algorithm Complexities

![](../Attachments/Pasted%20image%2020221220005008.png)

Additionally for advanced algorithms, we commonly see the following complexities. But note that **these are not hard rules like the above are**, these are only common complexities

| Algorithm            | Time         | Space    |
| -------------------- | ------------ | -------- |
| Binary Search        | $O(\log{n})$ | $O(1)$   |
| Depth First Search   | $O(E + V)$   | $O(V)$   |
| Breadth First Search | $O(E + V)$   | $O(V)$   |
| Dynamic Programming  | $O(n^2)$     | $O(n^2)$ |
| Backtracking         | $O(2^n)$     | $O(2^n)$ |

