
Greedy algorithms will always go for a hard rule to complete a question optimally. This is similar to Dynamic Programming, except that there is a "trick" that can allow you to get a better solution by being "greedy" and always selecting the best option at every moment, leading to the final answer. This will only lead to the answer in a particular subset of problems, but when it does, it is very efficient.  
  
  
## Jump Game  
  
Jump game asks you if you can jump to the end of the array, and each element of the array is how many places you can jump from that point. You could try DP and go through the entire decision tree of jumping to every possible position, and with caching, this would be $O(n^2)$. You can be Greedy and speed it up to $O(n)$. Instead of looking at the beginning and trying to get to the end, reverse the algorithm so that you work backwards from the end. The goal is to reach the last element, if you know which element would let you reach the end, then you can update the goal to be that element.  
  
Starting from the goalpost and moving backwards is a powerful pattern that can allow you to efficiently solve a normally recursive problem. Once the goalpost reaches the beginning or whatever target you want, you can return the result. Jump Game 2 is very similar, but you need to find the minimum number of jumps to the end. You move forwards and find the max length you can jump in K jumps.