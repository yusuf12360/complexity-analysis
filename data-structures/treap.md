# Proof of the Expected Height of a Treap

Treap is a versatile data structure that combines Tree (specifically, BST) properties and Heap properties to ensure an expected height of $O(\log N)$.

## Terminology

- **Height of a Rooted Tree:** The distance from the root to its furthest leaf.
- **Random Variable:** A variable representing numerical outcomes of a random phenomenon. Usually written as a capital letter (e.g., $X$).
- **Expected Value:** The mean/average of all possible numerical outcomes of a random phenomenon. The expected value of a random variable $X$ is denoted as $E[X]$.
- **Jensen's Inequality:** Given a convex function $f(x)$ and a random variable $X$, the following inequality is always true:

$$
f(E[X]) \leq E[f(X)]
$$


- **$H_n$:** A random variable representing the height of a Treap with $n$ nodes.

## Implementation

By utilizing the expected height of the Treap, we can confidently do many operations in this Treap such as splitting the tree, merging two trees, inserting a node into the tree, deleting it, and many others. Most of its operations are done in $O(\text{height of the Treap})$, which is $O(\log n)$ on average. It is trivial that the probability of a Treap with $n$ nodes (for large $n$) having a height of $\leq 1$ is astronomically small, meaning that the probability of a Treap having a height of $\geq 2 \cdot E[H_n]$ is even smaller, nearly impossible. This is why Treaps are reliable in terms of execution time.

The C++ implementation of this data structure is available [here](https://github.com/yusuf12360/cp-library/blob/main/data-structures/implicit-treaps.cpp).

## The Proof

Let us formulate $H_n$:

$$
H_n = 1 + \max(H_L, H_R)
$$

where $L$ is the number of nodes in the left child's subtree and $R$ is the number of nodes in the right child's subtree.

Let's define another random variable $X_n = 2^{H_n}$. This means:

$$
X_n = 2^{1 + \max(H_L, H_R)}
$$

$$
X_n = 2 \cdot \max(X_L, X_R)
$$

$$
X_n \leq 2(X_L + X_R)
$$

Imagine there are $n$ nodes whose keys are sorted from left to right. If we assign a random priority to each node, assuming all priorities are distinct, the probability of any specific node being the root is exactly $\frac{1}{n}$. Based on this probability, using the basic expected value formula, we get:

$$
E[X_n] \leq \frac{1}{n} \sum_{i = 0}^{n - 1} E[2(X_i + X_{n - 1 - i})]
$$

By the linearity of expectation:

$$
E[X_n] \leq \frac{2}{n} \sum_{i = 0}^{n - 1} (E[X_i] + E[X_{n - 1 - i}])
$$

Note that each $E[X_i]$ for all $i$ contributes exactly twice to the right-hand side:

$$
E[X_n] \leq \frac{4}{n} \sum_{i = 0}^{n - 1} E[X_i]
$$

Let's show that $E[X_n]$ is bounded by $O(n^3)$. Firstly, we claim that $E[X_n] \leq n^3$. We will prove this by mathematical induction. For $n = 1$, there is only one node and the height is obviously 0. Thus:

$$
E[X_1] = 2^0 = 1 \leq 1^3
$$

Base case proven. Now, we move on to the transition. Let $p_n$ be the statement that $E[X_n] \leq n^3$. Assume $p_0, p_1, \dots, p_{n - 1}$ are all true. Using the lastest formula of $E[X_n]$:

$$
E[X_n] \leq \frac{4}{n} \sum_{i = 0}^{n - 1} E[X_i]
$$

Note that we have assumed $p_0, p_1, \dots, p_{n - 1}$ to be true:

$$
E[X_n] \leq \frac{4}{n} \sum_{i = 0}^{n - 1} i^3
$$

Using a well-known bounding rule from calculus:

$$
\sum_{i = 0}^{n - 1} i^3 \leq \int_{0}^{n} x^3 \ dx = \frac{n^4}{4}
$$

Using this to continue the transition:

$$
E[X_n] \leq \frac{4}{n} \sum_{i = 0}^{n - 1} i^3 \leq \frac{4}{n} \cdot \frac{n^4}{4}
$$

$$
E[X_n] \leq n^3
$$

Thus, if $p_0, p_1, \dots, p_{n - 1}$ are true, then $p_n$ is also true. Because $p_1$ is true, $p_n$ is always true for $n \geq 1$. From this, we can continue to calculate the expected value:

$$
E[2^{H_n}] \leq n^3
$$

Because $f(x) = 2^x$ is a convex function, we can apply Jensen's inequality:

$$
2^{E[H_n]} \leq E[2^{H_n}]
$$

Combining the last two inequalities:

$$
2^{E[H_n]} \leq n^3
$$

$$
E[H_n] \leq 3 \log_2 n
$$

Since $3$ is a constant:

$$
E[H_n] = O(\log n)
$$

**Conclusion:** The expected height of a Treap with $n$ nodes by assigning a random priority to its nodes is $O(\log n)$.