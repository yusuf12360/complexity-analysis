# Time Complexity Proof of the Hopcroft-Karp Algorithm

The Hopcroft-Karp algorithm is a robust algorithm designed to solve the Maximum Cardinality Bipartite Matching (MCBM) problem. It augments along a maximal set of shortest augmenting paths in each phase to ensure $O(\sqrt{V})$ phases.

## Terminology

- **Path:** A set of edges forming a line graph.
- **Matching:** A set of edges such that no two distinct edges share a common vertex. In other words, every vertex is connected to at most one edge in the matching. An edge is called a matched edge if and only if it exists in the current matching.
- **Alternating Path (w.r.t. Matching $M$):** A path that alternates between an edge that exists in Matching $M$ and an edge that does not.
- **Augmenting Path (w.r.t. Matching $M$):** An alternating path that starts and ends with vertices that are not covered by Matching $M$.
- **$\oplus$ (Symmetric Difference):** An operator taking two sets as operands that results in a set containing all elements that exist in exactly one of the two operands (but not both).
- **Augment:** To augment a path w.r.t. matching $M$ means to toggle the status of its edges: matched edges become unmatched, and unmatched edges become matched. This will automatically change the matching $M$.
- **Vertex-Disjoint:** A property of a collection of paths where no two paths share any common vertex.
- **Maximal Set:** A set of vertex-disjoint augmenting paths with the shortest length such that every other augmenting path of the same length in the graph shares at least one vertex with at least one path already in the set.

## Algorithm

1. Set `ans = 0`.
2. Check if there exists an augmenting path w.r.t. the current matching. While such a path exists, perform the following phase:
   - Augment all vertex-disjoint augmenting paths in the maximal set.
   - Add the number of augmented paths in this phase to `ans`.
3. Return `ans` as the MCBM (Maximum Cardinality Bipartite Matching).

The C++ implementation of the algorithm is available [here](https://github.com/yusuf12360/cp-library/blob/main/graphs/hopcroft-karp.cpp)

## Lemmas

The proof of the $O(E \sqrt{V})$ time complexity relies heavily on the fact that there are at most $O(\sqrt{V})$ phases in this algorithm, where $V$ is the number of vertices and $E$ is the number of edges. Each phase has a time complexity of $O(E)$ (BFS for searching for the shortest augmenting paths and DFS for augmenting them). First, let us prove several lemmas.

### Lemma 1: If there are 2 matchings $M_1$ and $M_2$ such that $|M_2| - |M_1| = k$, then $M_1 \oplus M_2$ contains at least $k$ vertex-disjoint augmenting paths w.r.t. $M_1$.

Consider the graph $G = M_1 \oplus M_2$. Each vertex in this graph must be connected by at most 2 edges. If a vertex were connected by 3 edges or more, then either $M_1$ or $M_2$ (or both) would contain a vertex connected by 2 or more edges, which violates the definition of a matching.

Since every vertex in $G$ is connected by at most 2 edges, every connected component in $G$ must form a **simple path** or a **simple cycle**, with alternating edges in each component. This means that an edge from $M_1$ will be followed by an edge from $M_2$ in this connected component and vice versa. This is because a vertex in $G$ cannot have 2 edges from the same matching ($M_1$ or $M_2$). Otherwise, this would again violate the matching definition.

In each **cycle**, the number of edges from $M_1$ and the number of edges from $M_2$ must be equal because the edges alternate and cycles must have an even length.  
In each **path**, one matching must contribute exactly one more edge to the path than the other matching, or both matchings contribute the same number of edges. 

Let:
- $a$ = the number of cycles.
- $b$ = the number of paths where $M_2$ contributes the same number of edges as $M_1$.
- $c$ = the number of paths where $M_2$ contributes exactly one more edge than $M_1$.
- $d$ = the number of paths where $M_1$ contributes exactly one more edge than $M_2$.

Note that a path in category $c$ starts and ends with an edge from $M_2$. Since edges alternate, this path is an **augmenting path w.r.t. $M_1$**.

We can define the sizes based on these components:
- The difference $|M_2| - |M_1|$ is determined only by paths of type $c$ and $d$, because $a$ and $b$ contribute equal edges.
- Specifically, each path in $c$ adds $1$ to the count, and each path in $d$ subtracts $1$.

Therefore:

$$
|M_2| - |M_1| = c - d
$$

$$
c - d = k \implies c = k + d
$$

$$
c \geq k
$$

**Conclusion:** There are at least $k$ paths of type $c$. Thus, $M_1 \oplus M_2$ contains at least $k$ vertex-disjoint augmenting paths w.r.t. $M_1$.

### Lemma 2: The length of the shortest augmenting path strictly increases from phase to phase.

Let:
- **$M$:** The latest matching before the $i$-th phase.
- **$k$:** The length of the shortest augmenting path found in phase $i$.
- **$n$:** The number of vertex-disjoint augmenting paths with length $k$ found in phase $i$.
- **$\{ P_1, P_2, \dots, P_n \}$:** A maximal set of $n$ vertex-disjoint augmenting paths of length $k$ found in phase $i$.
- **$P =$** $P_1 \oplus P_2 \oplus \dots \oplus P_n$.
- **$M'$:** The matching after we augment all paths in $P$ from matching $M$. ($M' = M \oplus P$)
- **$Q$:** The shortest augmenting path found in phase $i + 1$.
- **$M''$:** The matching after we augment path $Q$ from matching $M'$. ($M'' = M' \oplus Q$)

Our goal is to prove that $|Q| > k$. We will prove this by contradiction by first assuming that $|Q| \leq k$.

First, consider the symmetric difference of the matchings:

$$
M'' \oplus M' = Q
$$

$$
M'' \oplus (M \oplus P) = Q
$$

$$
M'' \oplus M = P \oplus Q
$$

Now, let's look at the size of the matchings:
- Augmenting $P$ (which has $n$ paths) increases the matching size by $n$:

$$
|M'| = |M| + n
$$

- Augmenting $Q$ (which is 1 path) increases the matching size by 1:

$$
|M''| = |M'| + 1
$$

- Therefore:

$$
|M''| - |M| = n + 1
$$

According to **Lemma 1**, since $|M''| - |M| = n + 1$, the graph $M'' \oplus M$ must contain at least **$n + 1$ vertex-disjoint augmenting paths w.r.t. $M$**. Let's call the set of these paths $R$.

We know that in phase $i$, the length of the shortest augmenting path was $k$. Therefore, every augmenting path w.r.t. $M$ must have a length of at least $k$. Since $M \oplus M'' = P \oplus Q$, the total number of edges in $P \oplus Q$ is the sum of the lengths of these paths in $R$.

$$
|P \oplus Q| \geq (\text{number of paths}) \times (\text{minimum length})
$$

$$
|P \oplus Q| \geq (n + 1) \cdot k \ \dots \ (1)
$$

Now, let's calculate the size of $|P \oplus Q|$ using set theory:

$$
|P \oplus Q| = |P| + |Q| - 2|P \cap Q|
$$

We know that $|P| = n \cdot k$ (since $P$ consists of $n$ paths of length $k$). Using our contradiction assumption $|Q| \leq k$:

$$
|P \oplus Q| \leq n \cdot k + k - 2|P \cap Q|
$$

$$
|P \oplus Q| \leq (n + 1) \cdot k - 2|P \cap Q| \ \dots \ (2)
$$

Combining inequalities (1) and (2):

$$
(n + 1) k \leq |P \oplus Q| \leq (n + 1) k - 2|P \cap Q|
$$

$$
(n + 1) k \leq (n + 1) k - 2|P \cap Q|
$$

$$
0 \leq -2|P \cap Q|
$$

$$
|P \cap Q| \leq 0
$$

Since the size of a set cannot be negative, we must have:
- $|P \cap Q| = 0$ (They are edge-disjoint).
- $(n+1)k \le |P \oplus Q| \le (n+1)k - 2(0) \implies |P \oplus Q| = (n+1)k$.
- $|P \oplus Q| = |P| + |Q| - 2|P \cap Q| \implies |Q| = k$.

This leaves us with the case where $|Q| = k$ and $P$ shares no edges with $Q$. We must now check if they share vertices. 

After augmenting $P$, every end-vertex in every connected component in $P$ becomes matched in $M'$, and specifically, it is covered by an edge belonging to $P$. If $Q$ were to share any vertex with $P$, it would have to traverse the matched edge connected to an end-vertex in any connected component in $P$. Since that matched edge belongs to $P$, $Q$ cannot share any vertex with $P$. Therefore $Q$ is an augmenting path of length $k$ w.r.t. $M$ that was vertex-disjoint from $P$ but was not included in $P$. This directly contradicts the fact that $P$ is a **maximal set**.

**Conclusion:** The assumption $|Q| \leq k$ leads to a contradiction. Thus, $|Q| > k$.

## The Proof

Let:
- **$M$:** The matching after $\sqrt{V}$ phases. 
- **$M_{\text{max}}$:** The MCBM (Maximum Cardinality Bipartite Matching).
- **$c = $** $|M_{\text{max}}| - |M|$ 

According to **Lemma 1**, $M \oplus M_{\text{max}}$ contains at least $c$ vertex-disjoint augmenting paths w.r.t. $M$. Let $m \geq c$ be the number of such augmenting paths and $Q_1, Q_2, \dots, Q_m$ be these paths.

According to **Lemma 2**, the length of the shortest augmenting path will be greater than 
$
\sqrt{V}$ w.r.t. $M$. Thus, $|Q_i| > \sqrt{V} \ \ \forall \ \ 1 \leq i \leq m
$
. In other words:

$$
m \cdot \sqrt{V} < \sum_{i = 1}^{m} |Q_i|
$$

Consider the total vertices used in $Q$. Then: 

$$
\displaystyle \sum_{i = 1}^{m} |Q_i| \leq V
$$

Hence:

$$
m \sqrt{V} < V
$$

$$
m < \frac{V}{\sqrt{V}}
$$

We know that $|M_{\text{max}}| - |M| = c$ and $c \leq m$. Therefore: 

$$
|M_{\text{max}}| - |M| < \sqrt{V}
$$

**Conclusion:** After $\sqrt{V}$ phases, we only need fewer than $\sqrt{V}$ additional phases to reach MCBM, leading to a maximum of $2\sqrt{V}$ phases in total, with $O(E)$ time spent on BFS and DFS in each phase. Thus, the time complexity of the Hopcroft-Karp Algorithm is $O(E \sqrt{V})$.