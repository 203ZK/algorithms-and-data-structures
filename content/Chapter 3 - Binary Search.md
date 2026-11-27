### Introduction

A **search problem** is the task of looking for a particular item in a search space, which can be represented using data structures like trees or graphs. In its simplest form, we want to search for an element in an *array*. In the first half of this chapter, we consider searching for an element `key` in an **sorted** (non-decreasing) array `A[1..n]`, returning its (leftmost) index if it exists, and returning `-1` otherwise.

A naïve solution is known as **linear search**, which involves iterating through the array from start to end, stopping the moment the current element equals `key`, and returning `-1` by the end of the loop:

```
1  LinearSearch(A, n, key):
2      for i = 1 to n:
3          if A[i] == key:
4              return i
5      return -1
```

There can be at most $n$ iterations of the loop, where each iteration takes $O(1)$ time ($1$ comparison with `key`), thereby giving `LinearSearch` a worst-case running time of $T(n) = O(n)$. Can we do better?

---
### Implementing Binary Search

**Binary search** can be used to search for a particular element (called the `key`) in a sorted array `A`—the keyword "sorted" is crucial here! It works as follows:

1. Check whether the `key` we are looking for is smaller than or equal to the middle element, `A[n/2]`.
2. If it is, recursively check the lower half of `A`.
3. Otherwise, if the middle element is larger than `key`, recursively check the upper half of `A`.

The pseudocode is as such:

```
1  BinarySearch(A, n, key):
2      start = 1
3      end = n
4
5      while start < end:
6          mid = start + (end - start) // 2
7          if key <= A[mid]:
8              end = mid
9          else:
10             start = mid + 1
11
12     return (A[start] == key) ? start : -1 
```

Here's a run-through of binary search on the array `A = [2, 3, 5, 8, 9, 11, 14]` of length `n = 7`, searching for `key = 9`:

| Iteration # | `start` | `end` | `mid` | `key <= A[mid]`?        | New `start` | New `end` |
| ----------- | ------- | ----- | ----- | ----------------------- | ----------- | --------- |
| 1           | `1`     | `7`   | `4`   | `A[4] = 8`, so **no**   | `5`         | `7`       |
| 2           | `5`     | `7`   | `6`   | `A[6] = 11`, so **yes** | `5`         | `6`       |
| 3           | `5`     | `6`   | `5`   | `A[5] = 9`, so **yes**  | `5`         | `5`       |
Since `A[5] == 9`, we return `5` as the index.

This time, let's run through with the same array but with a non-existent `key = 1`:

| Iteration # | `start` | `end` | `mid` | `key <= A[mid]`?       | New `start` | New `end` |
| ----------- | ------- | ----- | ----- | ---------------------- | ----------- | --------- |
| 1           | `1`     | `7`   | `4`   | `A[4] = 8`, so **yes** | `1`         | `4`       |
| 2           | `1`     | `4`   | `2`   | `A[2] = 3`, so **yes** | `1`         | `2`       |
| 3           | `1`     | `2`   | `1`   | `A[1] = 2`, so **yes** | `1`         | `1`       |
Since `A[1] != 1`, we return `-1`, indicating that `key = 1` has not been found.

What invariant can we use to prove that our algorithm works as intended? Consider the following invariant:

>[!quote] Loop invariant for `BinarySearch`
>If `key` exists in `A`, then `key` exists in `A[start..end]`.

We verify this invariant using the three steps mentioned in the previous chapter:

1. **Initialisation**: Prior to the first iteration of the loop, we have `start = 1` and `end = n`. Since `A` is the same as `A[1..n]`, then if `key` exists in `A`, we already have that `key` exists in `A[start..end]`.

2. **Maintenance**: Suppose `key` exists in `A[start..end]`. Partitioning it into its two halves `A[start..mid]` and `A[mid+1..n]`, the `key` must exist in one of the halves:
	1. If `key <= A[mid]`, then it cannot exist in the upper half thanks to `A`'s being sorted. Therefore, `key` must exist in the lower half, in `A[start..mid]`. (`key` being at `A[mid]` itself is a possibility, so we should include `A[mid]` in this lower half.) By setting `end = mid` on line 8, we set up `start` and `end` to indicate this new range. 
	2. Otherwise, if `key > A[mid]`, then it cannot exist in the lower half, once again thanks to `A`'s being sorted. Therefore, `key` must exist in the upper half, in `A[mid+1..n]`. (This time, `key` being at `A[mid]` itself is *not* a possibility since the condition is a strict inequality, so we should *exclude* `A[mid]` in this upper half.) By setting `start = mid + 1` on line 10, we set up the appropriate bounds.

3. **Termination**: By the end of the loop, we have that `start == end`$\textcolor{red}{^*}$. Then, if `key` exists in `A[start..end]` (i.e., `key` equals `A[start]` itself), then we should return `start`; otherwise, we return `-1`, as per line 12.

$\textcolor{red}{^*}$ *Food for thought*: Why is it not possible for `start > end` upon termination?

#### Intricacies of binary search

Now that we've seen how binary search works, here's a challenge: try rewriting the algorithm by yourself in your preferred language and try running it. Did you get it completely bug-free the very first try? Likely not—but why? And how can invariants be used to help us write it correctly the first time round?

One major issue you might have encountered is that the algorithm fails to terminate, instead running into a loop. Likely, your binary search looks something like this

```python
# 0-indexing here!
1  def binarySearch(A, n, key): 
2  	   start, end = 0, n - 1
3
4      while start < end:
5          mid = start + (end - start) // 2
6          if key < A[mid]:
7              end = mid - 1  # Decrementing end...
8          else:
9              start = mid    # ...instead of incrementing start
10
11     return start if (A[start] == key) else -1
```

Try running this on `A = [3, 6, 8]` and `key = 6`:

| Iteration # | `start` | `end` | `mid` | `key < A[mid]`?                | New `start` | New `end` |
| ----------- | ------- | ----- | ----- | ------------------------------ | ----------- | --------- |
| 1           | `0`     | `2`   | `1`   | `A[1] = 6`, so **no**          | `1`         | `2`       |
| 2           | `1`     | `2`   | `1`   | `A[1] = 6`, so **no** (again!) | `1`         | `2`       |
... and the algorithm loops indefinitely!

The bug in this implementation lies in the fact that the computation of the middle index on line 5 *rounds down*. In other words, when the the subarray has an even number of elements (say, `start = 0`, `end = 1`, so number of elements is $1 - 0 + 1 = 2$), `mid` is computed to be the one on the left of the strict middle, i.e., index `0`. Hence, when there are only two elements to be considered and `key >= A[mid]`, then setting `start = mid` means that the left bound never changes, which produces the infinite loop.

Working backwards, we want to update the bounds as `start = mid + 1` and `end = mid`, instead of `start = mid` and `end = mid - 1`. So now we ask ourselves: when should we update `start = mid + 1`? Well, that should occur when we are certain that `A[mid]` cannot possibly be the `key` anymore, i.e., when `key` is *strictly* larger than `A[mid]`. This brings us back to the correct implementation in the first code snippet (in the `else` branch)!

Another safeguard that has been used so far is the way that `mid` is computed: `mid = start + (end - start) // 2`. Why not just do `mid = (start + end) // 2`? Well, if `A` has a huge number of elements, the addition could push the sum `start + end` beyond the maximum integer value, leading to a wrap-around in the computed value and providing an incorrect result for `mid`. Hence, we use `start + (end - start) // 2` to avoid that pothole.

---
### Analysis of Binary Search

After all that, what's the running time of binary search?

```
1  BinarySearch(A, n, key):
2      start = 1
3      end = n
4
5      while start < end:
6          mid = start + (end - start) // 2
7          if key <= A[mid]:
8              end = mid
9          else:
10             start = mid + 1
11
12     return (A[start] == key) ? start : -1 
```

First, we observe that in each iteration of the loop, there are a constant number of operations being performed ($2$ assignments of `mid` and `start`/`end`, plus $1$ comparison), so each iteration runs in $O(1)$ time. All we need now is to figure out how many times the loop executes.

Observe that after each iteration, the range `[start, end]` halves (approximately). For example, returning to the example of `A = [2, 3, 5, 8, 9, 11, 14]` and `key = 9`:

1. Iteration 1: Size of subarray `end - start + 1` is `7 - 1 + 1 = 7`
2. Iteration 2: Size of subarray `end - start + 1` is `7 - 5 + 1 = 3`
3. Iteration 3: Size of subarray `end - start + 1` is `6 - 5 + 1 = 2`

For an input size of $n = 7$, we had $k = 3$ iterations. What about a general input size $n$? 

Observe that at the start of iteration $k$, the size of the subarray in question is approximately $n/2^{k-1}$ (compare with the table above!), and the algorithm terminates when the subarray has length $1$, i.e., when $n/2^{k-1} = 1$. This gives us $n = 2^{k-1}$, which means that the number of iterations is roughly $k = 1 + \log n = O(\log n)$. Multiplying this by the $O(1)$ running time within each iteration gives us a total running time of $O(\log n)$ for binary search!

---
### Variants of binary search

Recall that in this first look at binary search, the algorithm works because of the sorted property of the input array. In fact, binary search works for inputs that possess a "monotonic" property!

Suppose we have a predicate `pred(n)` on each element of the array (i.e., `pred(A[i])` is either true or false), and we would like to find the largest `i` such that `pred(A[i])` is true but `pred(A[i+1])` is false. The "monotonicity" of the array refers to the property that for all $j \leq i$, we have that `pred(A[j])` is true, while for all $k > i$, `pred(A[k])` is false.

![[generic-binary-search.png]]

One possible way to do this is with the following modification:

```
1  GenericBinarySearch(A, n, pred):
2      start = 1
3      end = n + 1                            // Not n (with 1-indexing)
4
5      while start + 1 < end:                 // Not start < end!
6          mid = start + (end - start) // 2
7          if pred(A[mid]) == true:           // Test the predicate instead
8              start = mid
9          else:
10             end = mid
11
12     return pred(A[start]) ? start : -1
```

To prove that this works, we use the following invariant instead:

>[!quote] Loop invariant for `GenericBinarySearch`
>If `pred`  is true for some value in `A`, then the largest index `i` for which `pred(A[i])` is true exists in `A[start..end)` (`A[end]` excluded).

1. **Initialisation**: Prior to the first iteration of the loop, we have `start = 1` and `end = n + 1`. Since `A` is the same as `A[1..n+1)`, then if `pred` is true for some value in `A`, the largest index `i` for which `pred(A[i])` must be in `A[start..end)` as well.

2. **Maintenance**: Suppose that the start of an iteration, we have that the largest index `i` for which `pred(A[i])` is true is within `A[start..end)`. Partitioning it into two halves `A[start,mid)` and `A[mid,end)`, that element must exist in one of the halves:
	1. If `pred(A[mid])` is true, then by the monotonic property of the array, `i` must be greater than or equal to `mid`, so setting `start = mid` ensures that `i` is still within `[mid, end)`.
	2. If `pred(A[mid])` is false, then by the monotonic property of the array, `i` cannot be `mid` or above, so setting `end = mid` ensures that `i` is still within `[start, mid)`, and *not* including `A[mid]`.

3. **Termination**: By the end of the loop, we have that `start + 1 == end` (i.e., `start` and `end` are adjacent)$\textcolor{red}{^*}$. Since `A[start..end)`, which is simply `A[start]` itself, contains the largest index `i`, then if such an `i` exists, it must be equal to `start`. Testing `pred(A[start])` one last time performs this check, returning `start` if it evaluates to true, and returning `-1` if false.

$\textcolor{red}{^*}$ *Food for thought*: Why is it not possible for `start <= end` upon termination?

---
### Summary

Binary search allows us to search for an element in an array that has a monotonic property with respect to a predicate in $O(\log n)$ time. Its key invariant is that the element being searched for, if it exists, must exist within the range `A[start..end]`; this invariant can also be used to help derive the inequalities to be used in its implementation.