---
title: "Chapter 1: Asymptotic Notation"
---
### Introduction

Given an algorithm (on a particular data structure), we are interested in how "resource intensive" the algorithm is; analyses of this nature are referred to as **algorithmic analysis**. Even within this broad definition, there are several ways in which we can perform such analyses, for example:

- How fast does the algorithm run *on average*?
- How fast does the algorithm run *in the best/worst case*?
- Which *factors* (of the problem or of the data structure used) affect the running time of the algorithm, and how so?
- How much *additional* (i.e., *auxiliary*) space does the algorithm consume?

However, the absolute runtime of a process is machine-dependent. A faster CPU might execute the same program in a shorter amount of time, but that says nothing about the *algorithm* itself—the method in which the solution is computed—and so that would be and should be irrelevant to the analysis of the algorithm for our intents and purposes.

Instead, we use **asymptotic notation** to express (formally) how an algorithm performs *as the input grows*! For example, how does the running time of insertion sort change as the length of the input array doubles? And how does that compare with merge sort?

---
### First Attempt at Analysis

First, let's attempt to grasp an intuition of asymptotic complexity. Consider the following code snippet, which returns the maximum value in a non-empty array `arr`:

```
1  def findMax(arr):
2      maxNum = arr[0]
3
4      for num in arr:
5          if num >= maxNum:
6              maxNum = num
7
8      return maxNum
```

Before we try to analyse this, let's establish the following ground rules:

>[!info] Ground rules for analysis
>1. Primitive operations (e.g., assignments, mathematical operations, array indexing) take *constant time*. In layman terms, they always take "$1$ unit" of runtime, regardless of the input. For instance, no matter how large `i` is in `i += 1`, or no matter how large the array `arr` is in `temp = arr[i]`), these operations always take the same amount of time (i.e., $\Theta(1)$ time).<br></br>
>2. The total runtime of statements that run sequentially is the sum of their individual runtimes. For instance, suppose we have statements 1 and 2, that have individual runtimes of $T_1$ and $T_2$ respectively (e.g., lines 5 and 6 in the snippet above). Then, the total runtime of both statements is $T_1 + T_2$.

With this in mind, we can step through the code snippet on a sample array, `arr = [2, 1, 3]`:

- First, we initialise `maxNum = 2`. ($1$ unit of time)
- The $1^{\text{st}}$ iteration of the loop compares `2` with `maxNum`. Since `2 >= maxNum`, we assign `maxNum = 2`. ($2$ units of time — $1$ for comparing with `maxNum`, and $1$ for assigning)
- The $2^{\text{nd}}$ iteration of the loop compares `1` with `maxNum`. Since `1 < maxNum`, we do not perform the assignment. ($1$ unit of time)
- The $3^{\text{rd}}$ iteration of the loop compares `3` with `maxNum`. Since `3 >= maxNum`, we assign `maxNum = 3`. ($2$ units of time)
- Finally, we return `maxNum`. ($1$ unit of time)

In total, the algorithm takes $1 + 2 + 1 + 2 + 1 = 7$ units of time to execute. What about an array that's *twice* the size? Consider `arr = [2, 5, 8, 1, 9, 3]`:

- First, we initialise `maxNum = 2`. ($1$ unit of time)
- The $1^{\text{st}}$ iteration of the loop compares `2` with `maxNum`. Since `2 >= maxNum`, we assign `maxNum = 2`. ($2$ units of time)
- The $2^{\text{nd}}$ iteration of the loop compares `5` with `maxNum`. Since `5 >= maxNum`, we assign `maxNum = 5`. ($2$ units of time)
- (And so on...)
- Finally, we return `maxNum`. ($1$ unit of time)

In total, this takes $1 + 2 + 2 + 2 + 1 + 2 + 1 + 1 = 12$ units of time, which is approximately double the runtime of the previous array. Was that a coincidence?

Let's be pessimistic for the time being and assume that we want to find the *maximum* possible runtime on an array of size $n$; after all, we typically care about when things go wrong to make sure that we have sufficient resources for our programs! Then, we have the following tally:

- $1$ unit of time for the initialisation
- At most $2$ units of time for each of the $n$ elements
- $1$ unit of time for the return

This sums up to $T(n) \leq 1 + 2n + 1 = 2n + 2$ units of time, where $T(n)$ refers to the runtime of the algorithm on an input of size $n$. Congratulations, you have just performed your first algorithmic analysis!

---
### Asymptotic Notation

However, what we have accomplished there is merely half of the story; we still haven't expressed how the runtime differs when the input size *changes* (remember, we are always concern with the issue of *scale* here). To do this, we need a bit of math...

>[!info] Asymptotic notation
>Suppose we have two functions $\textcolor{blue}{T(n)}$ (e.g., the runtime of an algorithm) and $\textcolor{red}{f(n)}$.
>
>We say that $\textcolor{blue}{T(n)}$ is **big-O** of $\textcolor{red}{f(n)}$, denoted $\textcolor{blue}{T(n)} = O(\textcolor{red}{f(n)})$, if and only if the following holds: $$\exists c > 0, \exists n_0 > 0, \forall n \geq n_0 \ \big[\textcolor{blue}{T(n)} \leq c \cdot \textcolor{red}{f(n)} \big]$$
>
>We say that $\textcolor{blue}{T(n)}$ is **big-omega** of $\textcolor{red}{f(n)}$, denoted $\textcolor{blue}{T(n)} = \Omega(\textcolor{red}{f(n)})$, if and only if the following holds: $$\exists c > 0, \exists n_0 > 0, \forall n \geq n_0 \ \big[c \cdot \textcolor{red}{f(n)} \leq \textcolor{blue}{T(n)} \big]$$
>
>We say that $\textcolor{blue}{T(n)}$ is **big-theta** of $\textcolor{red}{f(n)}$, denoted $\textcolor{blue}{T(n)} = \Theta(\textcolor{red}{f(n)})$, if and only if the following holds: $$\exists c_1, c_2 > 0, \exists n_0 > 0, \forall n \geq n_0 \ \big[c_1 \cdot \textcolor{red}{f(n)} \leq \textcolor{blue}{T(n)} \leq c_2 \cdot \textcolor{red}{f(n)} \big]$$
>
>Equivalently, $\textcolor{blue}{T(n)} = \Theta(\textcolor{red}{f(n)})$ if and only if $\textcolor{blue}{T(n)} = O(\textcolor{red}{f(n)})$ and $\textcolor{blue}{T(n)} = \Omega(\textcolor{red}{f(n)})$.

Whew, that's a lot of math to digest! Let's break down the statements one by one.

![[big-O-1.png]]

In the diagram above, we have the functions $\textcolor{blue}{T(n) = 2n + 1}$ and $\textcolor{red}{f(n) = n^2}$. Observe that when $c = 1$, we have that $c \cdot \textcolor{red}{f(n)} = n^2$, and that past the point of $n_0 = 3$ (shaded in green), **all values** (i.e., $\forall n \geq n_0$) of $\textcolor{blue}{T(n)}$ are less than or equal to that of $c \cdot \textcolor{red}{f(n)} = n^2$. Hence, we have "upper-bounded" $\textcolor{blue}{T(n)}$ with $\textcolor{red}{n^2}$ up to a constant factor of $c = 1$, so we can say that $\textcolor{blue}{T(n)} = O(\textcolor{red}{n^2})$. In fact, this relationship holds true even for other values of $c$, such as $c = 1/2$:

![[big-O-2.png]]

In this case, we would need a larger value of $n_0$, say, $n_0 = 5$. Nonetheless, the existence of a single set of values of $c$ and $n_0$ would suffice. Intuitively, $\textcolor{red}{f(n) = n^2}$ grows *asymptotically faster* than $\textcolor{blue}{T(n) = 2n + 1}$.

An example of a function $\textcolor{red}{f(n)}$ that grows *asymptotically slower* than $\textcolor{blue}{T(n) = 2n + 1}$ would be $\textcolor{red}{f(n) = \log_2 n}$ (or otherwise written simply $\log n$, with the base of $2$ being implied by default):

![[big-omega.png]]

In fact, even with $c = 1$, a value of $n_0$ as small as $1$ would suffice that make $c \cdot \textcolor{red}{\log n} \leq \textcolor{blue}{2n + 1}$, so we can say that $\textcolor{blue}{T(n)} = \Omega(\textcolor{red}{\log n})$.

What about a function $\textcolor{red}{f(n)}$ that grows asymptotically as fast as $\textcolor{blue}{T(n)}$? Consider $\textcolor{red}{f(n) = n}$. Then, $\textcolor{blue}{T(n)} = \Omega(\textcolor{red}{n})$ is obvious, since for all values of $n \geq 0$, we have that $c \cdot \textcolor{red}{f(n)} = 1 \cdot \textcolor{red}{n} = n \leq \textcolor{blue}{2n + 1}$. But is $\textcolor{blue}{T(n)} = O(\textcolor{red}{n})$?

It is! Consider letting $c = 3$, such that $c \cdot \textcolor{red}{f(n)} = 3 \cdot \textcolor{red}{n} = 3n$. Then, for all $n \geq 1$, $\textcolor{blue}{2n + 1} \leq 3n$:

![[big-theta.png]]

Since $\textcolor{blue}{T(n)} = \Omega(\textcolor{red}{n})$ and also $\textcolor{blue}{T(n)} = O(\textcolor{red}{n})$, we have shown that $\textcolor{blue}{T(n)} = \Theta(\textcolor{red}{n})$.

Going back to our example of `findMax`, where $T(n) = 2n + 2$, we can likewise conclude that $T(n) = \Theta(n)$—which values of $c_1$ and $c_2$ would prove this?

#### Ordering of common asymptotic growths

In algorithmic analysis, certain functions tend to be used to characterise these asymptotic behaviours, and it is good to have a familiar grasp of how they compare with one another. Here are some of the common orders of growth, arranged from fastest to slowest:

1. Constant, $O(1)$ (e.g., array indexing, mathematical operations)
2. Double logarithmic, $O(\log \log n)^{\textcolor{red}{*}}$
3. Logarithmic, $O(\log n)$ (e.g., binary search, common heap operations)
4. Polylogarithmic, $O(\log^2 n)$
5. Linear, $O(n)$ (e.g., `findMax`, linked list reversal)
6. Log-linear, $O(n \log n)$ (e.g., merge sort, quicksort)
7. Polynomial, $O(n^k)$ for constant $k$ (e.g., Strassen's algorithm, Floyd-Warshall)
8. Exponential, $O(c^n)$ for constant $c$ (e.g., subset enumeration, naïve Fibonacci)
9. Factorial, $O(n!)$ (e.g., permutation enumeration, naïve Travelling Salesman)

$\textcolor{red}{^*}$ In computer science, "$\log n$" is defined to be "$\log_2 n$" by default, unless otherwise specified.

#### Working with big-O

Typically in an algorithm, we can find its overall running time by analysing separate "components" of it, finding their individual running times, and then summing them up to obtain the total running time of the entire algorithm. But how does one "add up" functions, as in "$O(\log n) + O(n)$"?

Here are some general rules when working with big-O:

>[!info] General rules with big-O
>1. If $c$ is a constant and $T(n) = O(c \cdot f(n))$, then $T(n) = O(f(n))$.<br></br>
>2. If $T(n)$ is a polynomial of degree $k$, then $T(n) = O(n^k)$ (i.e., where $k$ is the largest exponent). For example: $$T(n) = 10\textcolor{red}{n^5} - 50n^3 + 10n + 7 = O(\textcolor{red}{n^5})$$
>3. If $\textcolor{blue}{T_1(n)} = O(\textcolor{blue}{f_1(n)})$ and $\textcolor{red}{T_2(n)} = O(\textcolor{red}{f_2(n)})$, then $\textcolor{blue}{T_1(n)} + \textcolor{red}{T_2(n)} = O(\textcolor{blue}{f_1(n)} + \textcolor{red}{f_2(n)}) = O(f(n))$, where $f(n)$ is the asymptotically larger of $\textcolor{blue}{f_1(n)}$ and $\textcolor{red}{f_2(n)}$. For example, if $\textcolor{blue}{T_1(n)} = O(\textcolor{blue}{\log n})$ and $\textcolor{red}{T_2(n)} = O(\textcolor{red}{n})$, then: $$T(n) = \textcolor{blue}{T_1(n)} + \textcolor{red}{T_2(n)} = O(\textcolor{blue}{\log n} + \textcolor{red}{n}) = O(\textcolor{red}{n})$$ since $\textcolor{red}{\log n} = O(\textcolor{blue}{n})$ (from the list above).<br></br>
>4. If $\textcolor{blue}{T_1(n)} = O(\textcolor{blue}{f_1(n)})$ and $\textcolor{red}{T_2(n)} = O(\textcolor{red}{f_2(n)})$, then $\textcolor{blue}{T_1(n)} \cdot \textcolor{red}{T_2(n)} = O(\textcolor{blue}{f_1(n)} \cdot \textcolor{red}{f_2(n)})$. For example, if $\textcolor{blue}{T_1(n)} = O(\textcolor{blue}{\log n})$ and $\textcolor{red}{T_2(n)} = O(\textcolor{red}{n})$, then: $$T(n) = \textcolor{blue}{T_1(n)} \cdot \textcolor{red}{T_2(n)} = O(\textcolor{red}{n} \textcolor{blue}{\log n})$$

#### A word of caution

>[!warning] Case analysis and asymptotic notation
>**Do not conflate the concepts of worst-case analysis with big-O, best-case analysis with big-omega, and average-case analysis with big-theta.** They are different concepts and *cannot* be used interchangeably (nor would it makes sense to)! Simply put, "worst-/best-/average-case analysis" pertains to the *type of input* we are considering in our analysis, while big-O/big-omega/big-theta pertains to the *function of the running time*. 
>
>For instance, suppose we had an algorithm whose running time in the best case is (something like) $T_{best}(n) = 5n + 2$. Then, while it is true that $T_{best}(n) = O(n)$, we can also say that $T_{best}(n) = O(n^2)$ and $T_{best}(n) = \Omega(\sqrt{n})$; in fact, there are infinitely many expressions for these asymptotics for *any* function $T$!
>
>More importantly, we pay attention to the following for now:
>- How each of these algorithms work (and their rough implementations in, say, pseudocode)
>- How to analyse their running times (take worst-case analysis as the default by now)
>- The properties and invariants of the algorithms and data structures
>- Their use cases (perks and misses)

---
### Summary

In algorithmic analysis, we are interested in the *rate* at which resources (e.g., time, space) are consumed as inputs scale. To do so, we use asymptotic notation to express our analysis, namely via big-O, big-omega and big-theta.