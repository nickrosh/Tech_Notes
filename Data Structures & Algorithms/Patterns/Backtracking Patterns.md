
## Subsets



## Duplicated Input

a lot of the time, we are given a twist on regular Backtracking where the input has duplicated values, but we are still only allowed to give unique responses. This comes up in Subsets II and Combination Sum II. The solution is to proceed normally with the left branch of the tree, but for the right branch, instead of incrementing the index of the input by one, keep incrementing the index until the element selected is not a duplicate of the previous selection. This way, all solutions in the right branch will exclude the duplicated element, and thus you will only have unique answers.