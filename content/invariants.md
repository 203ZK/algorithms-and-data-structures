---
title: "Chapter 2: Invariants"
---
### Introduction

Before an algorithm or a data structure (DS) is efficient, it should **work**; *correctness* should be the first order of business. But how do we do that? This short chapter introduces the concept of an **invariant**—a "principle" that an algorithm or a DS should always adhere to so that we can *prove* that it behaves as intended.

---
### Loop Invariants

A **loop invariant** is a statement that verifies that a loop preserves certain properties that we decide on; we can check that our invariant is maintained in each of the following three steps to assert that the loop achieves our intended goal upon exiting. These three checks are:

1. **Initialisation**: Prior to the loop, prove that the invariant holds.
2. **Maintenance**: At the end of each iteration of the loop, prove that the invariant *still* holds.
3. **Termination**: By the time the loop finishes, the invariant helps us to prove that our goal has been achieved.

Let's revisit our `findMax` function from the previous chapter (this time in pseudocode, and with $1$-indexing):

```
1  FindMax(A, n):
2      maxNum = A[1]
3      
4      for i = 2 to n:
5          if A[i] >= maxNum:
6              maxNum = A[i]
7
8      return maxNum
```

We can use the following loop invariant on lines 4 to 6:

>[!quote] Loop invariant for `FindMax`
>At the start of the `k`-th iteration of the for loop, `maxNum` holds the largest value of the subarray `A[1..k]`.

Now, we can perform the three checks on our invariant:

1. **Initialisation**: Prior to the first iteration of the loop, `maxNum` is set to `A[1]`. Since there is only one element in the subarray `A[1..1]`, `maxNum` holds the largest (and only) value of that subarray.<br>
2. **Maintenance**: Suppose that at the start of the `k`-th iteration, `maxNum` holds the largest value of the subarray `A[1..k]`. Now we want to show that by the end of the `k`-th iteration (i.e., by the start of the `k+1`-th iteration), `maxNum` holds the largest value of the subarray `A[1..k+1]`. There are two cases here:
	- If `A[k+1]` is indeed the largest element seen so far, then the comparison on line 5 is true, and `maxNum` is updated to hold `A[k+1]` instead.
	- Otherwise, the largest element of `A[1..k+1]` is in `A[1..k]` already, and by our assumption (i.e., the inductive hypothesis), we already have that `maxNum` holds that value.
	- In either case, `maxNum` has been shown to hold the largest value of the subarray by the end of the `k`-th iteration.<br>
3. **Termination**: By the end of the `n-1`-th iteration (since the loop starts from `i = 2`), `maxNum` holds the largest value of the subarray `A[1..n]`, which is the original array `A` itself. Thus, our invariant has ensured that `FindMax` indeed does what we want it to do.

The point of this example is to illustrate how a loop invariant can be used to prove (or at least provide a sanity check) that our algorithm works *correctly*. We will encounter more complicated invariants in the upcoming chapters, so be sure that you have a firm grasp of this notion first!

---
### Recursive Algorithms

Another type of invariant can be applied to recursion algorithms. Consider the following alternative to `FindMax`:

```
1  FindMaxRec(A, start, end):
2      if start == end:
3          return A[start]
4      
5      mid = start + (end - start) // 2
6      leftMax = FindMaxRec(A, start, mid)
7      rightMax = FindMaxRec(A, mid + 1, end)
8      
9      return max(leftMax, rightMax)
```

Since there is no loop, we cannot check that the invariant holds at each *iteration*; we can, however, verify that it is held at (i) the base case(s), and (ii) at each *recursive call*.

In this example, we can use the following invariant:

>[!quote] Invariant for `FindMaxRec`
>Each call to `FindMaxRec(A, start, end)` returns the largest value in the subarray `A[start..end]`.

1. **Base case(s)**: The invariant is held at the base case when `start == end`, since the subarray `A[start..end]` contains a single element which is `A[start]`, so it is also the largest element of that subarray.<br>
2. **Recursive calls**: The recursive calls on lines 6 and 7 partition `A[start..end]` into two subarrays, `A[start..mid]` and `A[mid+1..end]`. Since each element in `A[start..end]` must belong to either of these new subarrays, then in particular, the largest element must also be in either one of them. By the inductive hypothesis, `leftMax` and `rightMax` contain the largest elements in their respective subarrays, at least one of which must be the overall largest element. By taking the greater of them on line 9, the original call returns that element.

---
### Invariants of Data Structures

Yet another category of invariants are those enforced by data structures themselves. you might be familiar with the concept of **binary search trees (BSTs)**, which store values in nodes and each node points to a left and a right subtree, where all elements in the left subtree are smaller than the value at the root, and all elements in the right subtree are larger than the value at the root.

In these data structures, the correctness of algorithms that operate on them hinge on these inherent invariants being held at each step of the algorithm. This is important to ensure that our algorithm does not break the data structure along the way! They can also help us prove certain behaviours of these algorithms; we'll see more concrete examples of these claims as we go along.

---
### Summary

Invariants come in several forms, namely: loop invariants, invariants in recursive algorithms, and invariants pertaining to specific data structures. Invariants can be used to prove the correctness of an algorithm by ensuring that certain properties are maintained at each iteration/call, thereby producing useful results upon the termination of the algorithm.