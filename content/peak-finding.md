---
title: "Chapter 4: Peak Finding"
---
### Introduction

In this chapter, we discuss the problem of finding a peak within an array (be it 1-dimensional or 2-dimensional). More precisely, given an array `A[1..n]`, we say that an element `A[i]` is a **peak** if it is greater than or equal to its adjacent neighbours, i.e., one of the following statements is true:

1. `i == 1` and `A[i] >= A[2]`
2. `i == n` and `A[n-1] <= A[i]`
3. `1 < i < n` and `A[i-1] <= A[i]` and `A[i] >= A[i+1]`

For instance, consider the array `A = [3, 2, 5, 6, 4, 7, 9]`. Then, the peaks are `3` (case 1), `6` (case 3) and `9` (case 2).

A naïve solution performs a linear search through `A`, stopping once an element that satisfies one of the above conditions has been found:

```
1  FindPeakLinear(A, n):
2      for i = 1 to n:
3          if i == 1 and A[i] >= A[2]:
4              return i
5          else if i == n and A[n-1] <= A[i]:
6              return i
7          else if A[i-1] <= A[i] and A[i] >= A[i+1]:
8              return i
```

Clearly, this takes $O(n)$ time, but can we do better? What about finding a peak in a 2-dimensional array?

---
### Peak Finding: A Divide-And-Conquer Approach

#### Existence of a peak

First, let's attempt to convince ourselves that in a non-empty array, a peak will definitely exist. For the sake of contradiction, let us suppose that there exists a non-empty array `A` of length `n` with no peaks, and let us attempt to construct it:

- Starting with `A[1]`, we must have that `A[1] < A[2]` in order case 1 not to hold.
- For `A[2]`, we cannot have that both `A[1] <= A[2]` and `A[2] >= A[3]` so that case 3 does not hold. Since we already have that `A[1] < A[2]`, we can only make the second inequality false by setting `A[2] < A[3]`.
- For each `3 <= i < n` , we can repeat the same argument as above and obtain that `A[i] < A[i+1]`. In particular, we have `A[n-1] < A[n]`.
- However, then we have `i == n` and `A[n-1] < A[n]`, which makes `A[n]` a peak by case 2.
- Hence, such an array `A` cannot exist, and every non-empty array must have a peak.

#### Algorithm for peak finding

With that out of the way, here's a **divide-and-conquer** approach to find a peak in a non-empty array `A`. Essentially, a divide-and-conquer algorithm works by splitting the sample space into partitions and using those partitions to solve the original problem, just like binary search does!

```
1  FindPeak(A, n, start, end):
2      A[0] = -MAX_INT   // Set up boundaries for easier checking
3      A[n+1] = -MAX_INT
4      
5      mid = start + (end - start) // 2
6      if A[mid] < A[mid+1]:                         // Branch 1
7          return FindPeak(A, n, mid + 1, end)
8      else if A[mid-1] > A[mid]:                    // Branch 2
9          return FindPeak(A, n, start, mid - 1)
10     else:                                         // Branch 3
11         return mid
```

$\textcolor{red}{^*}$ An alternative way of checking for peaks instead of relying on boundary values (as in lines 2 and 3) is to simply check the three conditions individually, perhaps using its own utility function.

Let's observe this algorithm on some examples. Consider calling `FindPeak(A, 8, 1, 8)` on `A = [2, 4, 5, 6, 8, 4, 7, 3]`:

| Iteration # | `start` | `end` | `mid` | `A[mid-1]` | `A[mid]` | `A[mid+1]` | Branch taken |
| ----------- | ------- | ----- | ----- | ---------- | -------- | ---------- | ------------ |
| 1           | `1`     | `8`   | `4`   | `5`        | `6`      | `8`        | 1            |
| 2           | `5`     | `8`   | `6`   | `8`        | `4`      | `7`        | 1            |
| 3           | `7`     | `8`   | `7`   | `4`        | `7`      | `3`        | 3            |
The algorithm terminates, returning index `7` as the peak (`A[7] = 7`).

Here's another example: `FindPeak(A, 9, 1, 9)` on `A = [10, 9, 7, 5, 3, 1, 8, 8, 12]`:

| Iteration # | `start` | `end` | `mid` | `A[mid-1]` | `A[mid]` | `A[mid+1]` | Branch taken |
| ----------- | ------- | ----- | ----- | ---------- | -------- | ---------- | ------------ |
| 1           | `1`     | `9`   | `5`   | `5`        | `3`      | `1`        | 2            |
| 2           | `1`     | `4`   | `2`   | `10`       | `9`      | `7`        | 2            |
| 3           | `1`     | `1`   | `1`   | `-MAX_INT` | `10`     | `9`        | 3            |
The algorithm terminates, returning index `1` as the peak (`A[1] = 10`).

Now, take a minute to think about what invariant we would need to prove the correctness of `FindPeak`! It sure looks a little like magic the first time you come across it—how do we *know* that recursing on a particular guarantees that a peak will be found?

Before we delve into the invariant itself, let's ponder a bit about the "magic" behind it. The most puzzling part about the algorithm is that it seems to be able to guarantee that when we recurse on the right half (without loss of generality here), then there exists a peak in the right half, and that it is also a peak of the *original array* `A`! Here's the pseudocode again for easier reference:

```
1  FindPeak(A, n, start, end):
2      A[0] = -MAX_INT   // Set up boundaries for easier checking
3      A[n+1] = -MAX_INT
4      
5      mid = start + (end - start) // 2
6      if A[mid] < A[mid+1]:                         // Branch 1
7          return FindPeak(A, n, mid + 1, end)
8      else if A[mid-1] > A[mid]:                    // Branch 2
9          return FindPeak(A, n, start, mid - 1)
10     else:                                         // Branch 3
11         return mid
```

First, let's think about *when* we would even recurse on the right half; this occurs when we are currently observing the subarray `A[start..end]` and we get that `A[mid] < A[mid+1]`, and afterwards we recurse on `A[mid+1..end]`. There are three cases here:

1. The peak that is found in `A[mid+1..end]` is somewhere in between the boundaries (i.e., one of `A[mid+2..end-1]`). Then, there is no issue since that peak will definitely be a peak of `A` itself.

2. The peak that is found in `A[mid+1..end]` is `A[mid+1]`.
	- Then, for the leftmost element `A[mid+1]` to be found in the right half, we must have that `A[mid+1] > A[mid+2]` (branch 2 taken).
	- On top of that, by the fact that we recursed on the right half, we must have had that `A[mid] < A[mid+1]` (condition for branch 1).
	- Hence, we have both `A[mid] < A[mid+1]` and `A[mid+1] > A[mid+2]`, which makes `A[mid+1]` a peak of `A` itself as well.

3. The peak that is found in `A[mid+1..end]` is `A[end]`. Then, either it is `A[n]` itself, or it is the result of being a left half of some previous call (when `end` was set to that call's `mid - 1`). In either case, to get to `A[end]` in the first place, we must have that `A[end-1] < A[end]` (via branch 1).
	- In the first case, since we already know that `A[n-1] < A[n]`, `A[n]` must be a peak.
	- In the second case, `end` must have been the rightmost boundary of some call's left half (i.e., reached via branch 2 when `end = M - 1` was set, where `M` is *that* call's middle index). Inductively, for that to happen, we must have that `A[M-1] > A[M]` (the condition for branch 2 to occur), so substituting `end = M - 1` gives us `A[end] > A[end+1]`. Then, combining that with `A[end-1] < A[end]` gives us `A[end-1] < A[end]` and `A[end] > A[end+1]`, which makes `A[end]` a peak of the original `A` as well.

In fact, what we have just derived is the invariant for `FindPeak`!

>[!quote] Invariant for `FindPeak`
> Recursing on the right half (without loss of generality) guarantees the existence of a peak of the original array in that right half.

To see this invariant more concretely, let's examine some actual arrays.

![[peak-in-middle.png]]

In the first case, we have `A = [2, 4, 5, 6, 8, 4, 7, 3]`. In the first iteration, `mid = 4`, and since `6 = A[mid] < A[mid+1] = 8`, we recurse on the right half, `[8, 4, 7, 3]`. We know that eventually the algorithm returns index `7`, which is an internal peak, sandwiched between `A[6] = 4` and `A[8] = 3`, so `A[7] = 7` is a peak of the original array `A` as well.

![[peak-in-left-half.png]]

In the second case, we have `A = [2, 4, 5, 6, 8, 4, 3, 3]`. In the first iteration, `mid = 4`, and since `6 = A[mid] < A[mid+1] = 8`, we recurse on the right half, `[8, 4, 3, 3]`. We know that eventually the algorithm returns index `5`, which is the leftmost element of this right half. In the next iteration, we recurse on the left half (on `[8]`) because `8 = A[mid+1] > A[mid+2] = 4`. Also, by virtue of recursing on `[8, 4, 3, 3]` in the first place, we know that `6 = A[mid] < A[mid+1] = 8`. Then, we have both `A[mid] < A[mid+1]` and `A[mid+1] > A[mid+2]`, which makes `A[mid+1]` a peak of the original array `A` as well.

![[peak-at-end.png]]

In the first scenario of the third case, we have `A = [2, 4, 5, 6, 8, 4, 7, 8]`. In the first iteration, `mid = 4`, and since `6 = A[mid] < A[mid+1] = 8`, we recurse on the right half, `[8, 4, 7, 8]`. We know that eventually the algorithm returns index `8`, which is `A[n]` itself. Then, we must have gotten to that index by recursing on the right half at each call, and at the very last call on `[7, 8]`, we have that `mid = 7`, and `7 = A[mid] < A[mid+1] = 8`, before finally returning `8` as the final output. Hence, we have `A[end-1] < A[end]`, as well as `A[end] > A[end+1] = -MAX_INT`, so `A[end] = 8` is a peak of the original array `A` as well.

![[peak-at-left-boundary.png]]

In the second scenario of the third case, we have `A = [1, 2, 4, 5, 6, 7, 8, 4, 3, 1]`. Two iterations in, we are looking at the subarray `A[start..end] = A[6..7] = [7, 8]`. We know that eventually the algorithm returns index `7`, which is not `A[n]` itself. In the final call, we recurse on the right half since `7 = A[end-1] < A[end] = 8`. 

Now, observe that we know that `A[end]` will be greater than the element to its right because we know (inductively) that `end = M - 1` due to recursion on the left half in some previous call. In this case, in the previous call on `[7, 8, 4, 3, 1]`, the middle index was `M = 8`, and we took the left half since `8 = A[M-1] > A[M] = 4`. Substituting `end = M - 1`, we have that `A[end] > A[end+1]`, as well as `A[end-1] < A[end]`, making `A[end] = 8` a peak in the original array `A` as well.

---
### Analysis of the Algorithm

Recall that in the analysis of binary search, we were a little informal about how we justified the relationship between the iteration number $k$ and the size of the array $n$, claiming that the size of the subarray at iteration $k$ is $n/2^{k-1}$, then using that to determine that binary search takes $O(\log n)$ time.

This time, we analyse the peak-finding algorithm a little more mathematically. Let $T(k)$ be the running time of `FindPeak` on a subarray of size $k$ (i.e., `k = end - start + 1`). Here's the pseudocode once more:

```
1  FindPeak(A, n, start, end):
2      A[0] = -MAX_INT
3      A[n+1] = -MAX_INT
4      
5      mid = start + (end - start) // 2
6      if A[mid] < A[mid+1]:
7          return FindPeak(A, n, mid + 1, end)
8      else if A[mid-1] > A[mid]:
9          return FindPeak(A, n, start, mid - 1)
10     else:
11         return mid
```

Lines 2, 3 and 5 take $O(1)$ time. Then, each recursive call is on half the array, so the input size to each of those calls is $k/2$ (strictly speaking, bounded by $\lceil k/2 \rceil$ but asymptotically they're equivalent), so the running time for each recursive call is $T(k/2)$. This gives us the recurrence: $$T(k) = T(k/2) + O(1)$$
Now, what we can do is "unroll" this recurrence in the following way:

$$
\begin{align*}
T(k) &= T(k/2) + O(1) \\
     &= T(k/4) + O(1) + O(1) \\ 
     &= T(k/8) + O(1) + O(1) + O(1) \\
     &= \dots \\
     &= O(1) + O(1) + \dots + O(1) \ \ \ \ \ \ \ \ \ \ \text{(How many times?)}
\end{align*}
$$

But how many times can we expand the first term (which is the same as the number of "$O(1)$" terms in the final sum)? In other words, how many times can we divide $k$ by $2$ before reaching $1$? That would be (approximately) $\log k$ times (more precisely speaking, $O(\log k)$ times), which means that we have $\log k$ terms of "$O(1)$" in the last line, giving a total running time of $T(n) = O(\log k) \cdot O(1) = O(\log k)$. You might also notice that the same recurrence can be applied to binary search as well!

>[!todo] Solving recurrence relations
>Yes, what we did there is still a little "hand-wavy", and there is indeed a more rigorous way to prove it. However, for now, we focus more on getting the final asymptotic running time instead of the mathematical intricacies of getting there!

---
### 2-Dimensional Peak Finding

Instead of 1-dimensional arrays, we can extend this approach to 2-dimensional arrays as well! Specifically, we are given a 2-dimensional array `A[1..R][1..C]`, where `R` and `C` are the number of rows and columns respectively, and our goal is to find a peak `(r, c)`, such that `A[r][c]` is greater than or equal to its four surrounding neighbours.

An intuitive approach might be to first find the maximum of each column (e.g., via linear search in $O(R)$ time each across $C$ columns), then finding the peak amongst those maxima, taking $O(RC + \log C)$ time in total. We know that this algorithm is correct:

- Let $m_c$ be the maximum of column $c$, for $1 \leq c \leq C$. Therefore, each $m_{c}$ is greater than or equal to its neighbours directly above and below in the same column. Now, we need to find the peak amongst $\{m_c : 1 \leq c \leq C\}$.
- Suppose the peak-finding algorithm settles on a peak $m_{c'}$, such that $m_{c'-1} \leq m_{c'} \leq m_{c'+1}$. Hence, we already know that $m_{c'}$ is greater than or equal to its left and right neighbours, so $m_{c'}$ is a peak of the entire 2-dimensional array `A`.

However, we can do better—finding the maximum of each column is far too slow! Instead, we swap the order and find peaks *first*, only looking for the maximum in each column when we examine columns at indices `mid - 1`, `mid` and `mid + 1`. In pseudocode:

```
1  FindPeak2D(A, R, C, start, end):
2      Pad with columns of -MAX_INT on column indices 0 and C + 1
3      
4      mid = start + (end - start) // 2
5      leftMax = Row index of maximum in column A[1..R][mid-1]
6      midMax = Row index of maximum in column A[1..R][mid]
7      rightMax = Row index of maximum in column A[1..R][mid+1]
8
9      if A[midMax][mid] < A[rightMax][mid+1]:
10         return FindPeak(A, R, C, mid + 1, end)
11     else if A[leftMax][mid-1] > A[midMax][mid]:
12         return FindPeak(A, R, C, start, mid - 1)
13     else:
14         return (midMax, mid)
```

Now, the algorithm only examines $O(\log C)$ values of `mid`, where each of those is compared to its two adjacent columns (total $3$ columns each time). For each of the $3$ columns, we take $O(R)$ time to find their maxima, giving `FindPeak2D` a total running time of $O(3\log C \cdot R) = O(3 \cdot R \log C) = O(R \log C)$.

---
### Summary

We can find a peak in a 1-dimension array of length $n$ in $O(\log n)$ time. To achieve that, we examine the middle element and compare it to its neighbours:
- If it is smaller than the element to its right, recurse on the right half.
- If it is smaller than the element to its left, recurse on the left half.
- Otherwise, that element is a peak.

We can extend this algorithm to search for a peak in a 2-dimensional array with $R$ rows and $C$ columns in $O(R \log C)$ time, by examining the middle column and comparing its maximum to the maxima of the two neighbouring columns.