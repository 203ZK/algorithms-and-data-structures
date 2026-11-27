### Introduction

Recall that for binary search to work, the array must be sorted—but *how* does one sort an array? How long does it take (and on what kinds of inputs)? What are the properties of each sorting algorithm?

This (lengthy) chapter discusses four sorting algorithms:
- **Bubble sort**
- **Selection sort**
- **Insertion sort**
- **Merge sort**

A fifth sorting algorithm (**quicksort**) will be covered in its own chapter.

---
### Properties of Sorting Algorithms

The sorting problem is essentially the following task: Given an array `A[1..n]`, produce an array `S[1..n]` such that:
- `A` and `S` share the same elements (i.e., they are permutations of one another); and
- For all indices `i` in `[1..n-1]`, `S[i] <= S[i+1]`.

For a given sorting algorithm, we can examine the following properties:

1. Its **time complexity** (in the best/worst/average case)
2. Its **space complexity**
3. Whether it sorts the array **in-place** (i.e., does not require any additional memory)
4. Whether it is **stable** (a sorting algorithm is stable if it preserves the order of equal elements in `A`)

---
### Algorithm 1: Bubble sort

In bubble sort, elements are compared pairwise from left to right, and swapped if `A[j] > A[j+1]`. Here's the pseudocode:

```
1  BubbleSort(A, n):
2     for i = n-1 downto 1
3         for j = 1 to i
4             if A[j] > A[j+1]:
5                 Swap A[j] and A[j+1]
6
7     return A
```

Here's an example on `A = [3, 2, 5, 1, 4]` where `n = 5`:

| Iteration # | `i` | `j` | `A[j]` | `A[j+1]` | Swap? | Updated array     |
| ----------- | --- | --- | ------ | -------- | ----- | ----------------- |
| 1.1         | `4` | `1` | `3`    | `2`      | Yes   | `[2, 3, 5, 1, 4]` |
| 1.2         |     | `2` | `3`    | `5`      | No    | `[2, 3, 5, 1, 4]` |
| 1.3         |     | `3` | `5`    | `1`      | Yes   | `[2, 3, 1, 5, 4]` |
| 1.4         |     | `4` | `5`    | `4`      | Yes   | `[2, 3, 1, 4, 5]` |
| 2.1         | `3` | `1` | `2`    | `3`      | No    | `[2, 3, 1, 4, 5]` |
| 2.2         |     | `2` | `3`    | `1`      | Yes   | `[2, 1, 3, 4, 5]` |
| 2.3         |     | `3` | `3`    | `4`      | No    | `[2, 1, 3, 4, 5]` |
| 3.1         | `2` | `1` | `2`    | `1`      | Yes   | `[1, 2, 3, 4, 5]` |
| 3.2         |     | `2` | `2`    | `3`      | No    | `[1, 2, 3, 4, 5]` |
| 4.1         | `1` | `1` | `1`    | `2`      | No    | `[1, 2, 3, 4, 5]` |
The final sorted array is `[1, 2, 3, 4, 5]`.

Bubble sort gets its name from the behaviour that each iteration of the outer loop "bubbles" the largest element as far to the right as possible. Namely, we state the following invariant:

>[!quote] Loop invariant for `BubbleSort`
>By the end of the `k`-th iteration of the outer loop, the `k` largest elements of `A` are correctly sorted in the last `k` positions, i.e., `A[n-k+1..n]`.

>[!info]- Proof of loop invariant for `BubbleSort`
>1. **Initialisation**: Before the loop, we are at the end of the `0`-th iteration of the loop, so the `0` largest elements of `A` (i.e., no elements) are in the last `0` positions, which is the empty subarray `A[n-0+1..n] = A[n+1..n] = []`. Hence, the invariant holds.<br></br>
>2. **Maintenance**: Suppose that at the end of the `k`-th iteration, the `k` largest elements are in sorted order in `A[n-k+1..n]`. In the `k+1`-th iteration, `i` is at `n-(k+1) = n-k-1`, so `j` goes from `1` to `n-k-1`. We need to show that by the end of this iteration, the `k+1` largest elements are in sorted order in `A[n-k..n]`.
>	- After each `j`, `A[j+1]` holds the largest element seen so far in `A[1..j+1]`. (The base case when `j = 0` is true since the largest element seen in `A[1..j+1] = A[1..1]` is simply `A[1]` itself.)
>	- Then, with `j+1`, lines 4 and 5 ensure that the larger of `A[j+1]` (the largest element in `A[1..j+1]`) and `A[j+2]` is placed in `A[j+2]`, therefore placing the overall largest element in `A[1..j+2]` in `A[j+2]`.
>	- By the end of the inner loop, we have `j = i`, so the largest element in `A[1..i+1]` is in position `A[i+1]`.
>	- Since `i = n-k-1`, we have that the largest element in `A[1..i+1]` is in position `A[i+1] = A[n-k]`.
>	- By assumption, the `k` largest elements are already in sorted in `A[n-k+1..n]`, so placing the next largest element in `A[n-k]` would place the `k+1` largest elements in sorted order in `A[n-k..n]`, as desired.<br></br>
>3. **Termination**: Upon the termination of the outer loop, we have finished `n-1` iterations, which places the `n-1` largest elements in sorted order in the last `n-1` positions. All that remains is `A[1]`, which must be the smallest element in `A`. Thus, the entire array is now sorted.

In the example above:
- Iterations 1.1 to 1.4 "bubble" the largest element (`5`) to the last position;
- Iterations 2.1 to 2.3 "bubble" the second largest element (`4`) to the second last position;
- Iterations 3.1 to 3.2 "bubble" the third largest element (`3`) to the third last position;
- Iteration 4.1 "bubbles" the fourth largest element (`2`) to the fourth last position;
- The smallest element (`1`) is left in the first position.

#### Analysis of bubble sort

The inner loop runs `i` times for each `i`, each in $O(1)$ time. Meanwhile, `i` in the outer loop ranges from `n-1` to `1`. In total, we have:
$$T(n) = \sum_{i=1}^{n-1} i \cdot O(1) = O(1 + 2 + \cdots + (n-1)) = O(n(n-1)/2) = O(n^2)$$

For all inputs, all $O(n^2)$ comparisons need to be done (i.e., regardless of whether you swap `A[j]` and `A[j+1]`, you still need to perform the check `A[j] > A[j+1]`). It also *sorts in-place* since no auxiliary memory is required. 

Whether bubble sort is stable or not depends on the implementation—if the condition on line 4 were `if A[j] >= A[j+1]` instead, then elements that are the same will have their ordering swapped in line 5, so it will not be stable. As per the implementation given above, bubble sort is *stable*.

There is an optimisation for bubble sort such that a best-case input runs in merely $O(n)$ time:

```
1  BubbleSort(A, n):
2     for i = n-1 downto 1
3         swapped = false
4         for j = 1 to i
5             if A[j] > A[j+1]:
6                 Swap A[j] and A[j+1]
7                 swapped = true
8         if swapped == false:
9             break
10
11    return A
```

Then, on a sorted input, all `i` checks on line 5 fail and no swaps are performed, so `swapped` remains `false` for the entirety of the inner loop. Then, lines 8 and 9 causes the outer loop to terminate in a single iteration through all $n$ elements, resulting in a best-case running time of just $O(n)$ instead!

---
### Algorithm 2: Selection Sort

In selection sort, the smallest element in the remaining subarray `A[i..n]` is "selected", then inserted into its final position `A[i]`.

```
1  SelectionSort(A, n):
2      for i = 1 to n-1:
3          minNumIdx = i
4          for j = i to n:
5              if A[j] < A[minNumIdx]:
6                  minNumIdx = j
7          Swap A[i] and A[minNumIdx]
8
9      return A
```

Here's an example on `A = [3, 1, 4, 2]` where `n = 4`:

| Iteration # | `i` | `minNumIdx` | `A[minNumIdx]` | `j` | `A[j]` | Updated `minNumIdx`       |
| ----------- | --- | ----------- | -------------- | --- | ------ | ------------------------- |
| 1.1         | `1` | `1`         | `3`            | `1` | `3`    | `1`                       |
| 1.2         |     | `1`         | `3`            | `2` | `1`    | `2`                       |
| 1.3         |     | `2`         | `1`            | `3` | `4`    | `2`                       |
| 1.4         |     | `2`         | `1`            | `4` | `2`    | `2` (New: `[1, 3, 4, 2]`) |
| 2.1         | `2` | `2`         | `3`            | `2` | `3`    | `2`                       |
| 2.2         |     | `2`         | `3`            | `3` | `4`    | `2`                       |
| 2.3         |     | `2`         | `3`            | `4` | `2`    | `4` (New: `[1, 2, 4, 3]`) |
| 3.1         | `3` | `3`         | `4`            | `3` | `4`    | `3`                       |
| 3.2         |     | `3`         | `4`            | `4` | `3`    | `4` (New: `[1, 2, 3, 4]`) |
The final sorted array is `[1, 2, 3, 4]`.

Selection sort adheres to the following invariant:

>[!quote] Loop invariant for `SelectionSort`
>By the end of the `k`-th iteration of the outer loop, the `k` smallest elements of `A` are in sorted order in the first `k` positions, i.e., `A[1..k]`.

>[!info]- Proof of loop invariant for `SelectionSort`
>1. **Initialisation**: By the end of the `0`-th iteration of the outer loop, the `0` smallest elements of `A` (which is none of them) are necessarily "sorted" in the first `0` positions, `A[1..0] = []`.<br></br>
>2. **Maintenance**: Suppose that by the end of the `k`-th iteration of the outer loop, the `k` smallest elements are in sorted order in `A[1..k]`. In the `k+1`-th iteration, `minNumIdx` is initialised to `k+1`.
>	- We claim that `minNumIdx` is the (leftmost) index of the smallest element seen so far in `A[k+1..j]`.
>	- By the end of the first iteration of the inner loop, we must have `minNumIdx = k + 1`, which is indeed the index of the only element in `A[k+1..k+1] = A[k+1]`.
>	- For the remaining `j` from `k+2` to `n`, we examine `A[j]` and see if it is smaller than `A[minNumIdx]`. If it is, then `A[j]` must be smaller than all elements in `A[k+1..j-1]`, and so updating `minNumIdx = j` means that `minNumIdx` will still hold the index of the smallest element seen in `A[k+1..j]`. Otherwise, `A[j]` is not the smallest element so far, and `minNumIdx` remains the same.
>	- By the end of the inner loop, `j = n`, and so `minNumIdx` holds the index of the smallest element of `A[k+1..n]`, as desired.
>	- On line 7, swapping `A[k+1]` with `A[minNumIdx]` places the smallest element of `A[k+1..n]` into position `k+1`. By our inductive hypothesis, all elements in `A[k+1..n]` are larger than or equal to all elements already in `A[1..k]`, so we have `A[k] <= A[k+1]` in particular. Hence, the first `k+1` positions now hold the `k+1` smallest elements in sorted order.<br></br>
>3. **Termination**: By the end of the outer loop, `i = n-1`, so the `n-1` smallest elements are in sorted order in `A[1..n-1]`. This leaves the remaining largest element in the last position, which leaves the entirety of `A` in sorted order as well.

In the example above:
- Iterations 1.1 to 1.4 place the smallest element (`1`) in the first position;
- Iterations 2.1 to 2.3 place the second smallest element (`2`) in the second posiiton;
- Iterations 3.1 and 3.2 place the third smallest element (`3`) in the third position;
- The largest element (`4`) is left in the final position.

#### Analysis of selection sort

For each `i`, the inner loop executes `n-i+1` times, each taking $O(1)$ time (for the comparison and the swapping). Meanwhile, `i` in the outer loop ranges from `1` to `n-1`. All in all, we have: 
$$T(n) = \sum_{i=1}^{n-1} (n - i + 1) \cdot O(1) = O(2 + 3 + \cdots + n) = O(n^2)$$

Unlike bubble sort, even in the best case here the input is already sorted, the inner loop still has to iterate through the entirety of the remaining array to search for the smallest element, so selection sort takes $O(n^2)$ time for all inputs. It also *sorts in-place* since no auxiliary memory is required. 

Whether selection sort is stable or not depends on the implementation—if the condition on line 5 were `if A[j] <= A[minNumIdx]` instead, then `minNumIdx` will hold the *rightmost* index of the smallest element instead; the swap on line 7 will invert their positions, so it will not be stable. As per the implementation given above, selection sort is *stable*.

---
### Algorithm 3: Insertion Sort

Insertion sort works like how one would manually sorting a hand of cards: going from left to right, insert each card into its appropriate position into the cards that come before.

```
1  InsertionSort(A, n):
2      for i = 2 to n
3          key = A[i]
4          for j = i-1 downto 1:
5              if A[j] > key:
6                  Swap A[j] and A[j+1]
7              else:
8                   break
9
10     return A
```

Here's an example on `A = [5, 1, 4, 2, 3]` where `n = 5`:

| Iteration # | `i` | `key` | `j` | `A[j] > key`?       | Updated `A`                 |
| ----------- | --- | ----- | --- | ------------------- | --------------------------- |
| 1.1         | `2` | `1`   | `1` | `5 > 1`, so **yes** | `[1, 5, 4, 2, 3]`           |
| 2.1         | `3` | `4`   | `2` | `5 > 4`, so **yes** | `[1, 4, 5, 2, 3]`           |
| 2.2         |     |       | `1` | `1 <= 4`, so **no** | `[1, 4, 5, 2, 3]` (`break`) |
| 3.1         | `4` | `2`   | `3` | `5 > 2`, so **yes** | `[1, 4, 2, 5, 3]`           |
| 3.2         |     |       | `2` | `4 > 2`, so **yes** | `[1, 2, 4, 5, 3]`           |
| 3.3         |     |       | `1` | `1 <= 2`, so **no** | `[1, 2, 4, 5, 3]` (`break`) |
| 4.1         | `5` | `3`   | `4` | `5 > 3`, so **yes** | `[1, 2, 4, 3, 5]`           |
| 4.2         |     |       | `3` | `4 > 3`, so **yes** | `[1, 2, 3, 4, 5]`           |
| 4.3         |     |       | `2` | `2 <= 3`, so **no** | `[1, 2, 3, 4, 5]` (`break`) |
The final sorted array is `[1, 2, 3, 4, 5]`.

As would be perhaps the most intuitive sorting algorithms so far, at each iteration we try to make sure the elements from the left are always sorted:

>[!quote] Loop invariant for `InsertionSort`
>By the end of the `k`-th iteration of the outer loop, the first `k+1` (*not necessarily smallest elements*) elements of `A`, i.e., `A[1..k+1]`, are in sorted order.

>[!info]- Proof of loop invariant for `InsertionSort`
>1. **Initialisation**: By the end of the `0`-th iteration of the outer loop (before `i = 2`), `A[1..k+1] = A[1..1] = A[1]` contains just one element, so it is already sorted.<br></br>
>2. **Maintenance**: Suppose that by the end of the `k`-th iteration of the outer loop, `A[1..k+1]` is sorted; let's call this subarray `oldA`. Now, in the `k+1`-th iteration, `i = k+2`, so `key = A[k+2]`. `j` ranges from `i-1 = k+1` down to `1`.
>	- Now we prove that each insertion in the inner loop maintains the invariant that `A[j+1..i]` is sorted, with `key` at `A[j+1]`. Since we break from the inner loop the moment `A[j] <= key`, the subarray `A[1..j]` remains untouched. By the inductive hypothesis, we know that `A[1..j]` remains sorted, and since `A[j] <= key`, we know that `A[1..j+1]` is sorted as well.
>	- Before the start of the inner loop, `j = i-1`, so the subarray `A[j+1..i] = A[i..i] = [key]` has only one element and is already sorted. Also, `key` is at `A[i] = A[j+1]`.
>	- Suppose that for some `j`, `A[j+1..i]` is sorted, with `key` at `A[j+1]`. Let `A[j]` be called `old`, and suppose `old > key`. Then, the swap on line 6 affects only `A[j]` and `A[j+1]`; in particular, it puts `A[j]` and `A[j+1]` in sorted order.
>	- By our inductive hypothesis, we already have that `old` is smaller than or equal to all elements in `A[j+2..i]`, since we assumed that `oldA` was already in sorted order. Thus, we have `A[j] < A[j+1]`, and also that `A[j+1..i]` is in sorted order. Hence, with `j` now decremented, `A[(j-1)+1..i] = A[j..i]` is in sorted order as well.
>	- Combining this with the fact that `A[1..j]` remains sorted by the time the inner loop terminates, we have that `A[1..j..i] = A[1..i] = A[1..k+2]` is sorted.
>3. **Termination**: By the end of the outer loop, we have gone through `k = n-1` iterations, so the first `k+1 = n` elements, which is the entire array `A`, is in sorted order.

