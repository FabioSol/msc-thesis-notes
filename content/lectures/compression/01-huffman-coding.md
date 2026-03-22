---
title: "Huffman Coding"
date: 2026-03-21T10:00:00-06:00
draft: false
type: "document"
description: "Huffman coding: a prefix-free optimal variable-length coding algorithm"
showDescription: false
weight: 1
---

## Motivation

Given a source that emits symbols from a finite alphabet, we want to encode each symbol as a binary string (a codeword) such that the **expected length** of the encoded message is minimized.

A naive approach assigns fixed-length codewords: for an alphabet of $n$ symbols we need $\lceil \log_2 n \rceil$ bits per symbol regardless of how often each symbol appears. Huffman coding exploits the frequency distribution of symbols to assign **shorter codewords to more frequent symbols** and longer codewords to less frequent ones.

## Prefix-free codes

A binary code $C: \mathcal{A} \to \{0,1\}^*$ is **prefix-free** (or instantaneously decodable) if no codeword is a prefix of another codeword. That is, for any two distinct symbols $a_i, a_j \in \mathcal{A}$:

$$C(a_i) \text{ is not a prefix of } C(a_j)$$

This property guarantees that a stream of bits can be decoded unambiguously from left to right without lookahead.

### Kraft's inequality

A necessary and sufficient condition for the existence of a prefix-free code with codeword lengths $l_1, l_2, \ldots, l_n$ is:

$$\sum_{i=1}^{n} 2^{-l_i} \leq 1$$

where $l_i = |C(a_i)|$ is the length (in bits) of the codeword assigned to symbol $a_i$.

## Problem formulation

Let $\mathcal{A} = \{a_1, a_2, \ldots, a_n\}$ be a finite alphabet with probability distribution $P$ where $p_i = P(a_i) > 0$ and $\sum_{i=1}^{n} p_i = 1$.

A binary code $C$ assigns to each symbol $a_i$ a codeword $C(a_i) \in \{0,1\}^*$ of length $l_i = |C(a_i)|$.

The **expected codeword length** under $P$ is:

$$\bar{L}(C, P) = \sum_{i=1}^{n} p_i \, l_i = \mathbb{E}_P[l(a)]$$

The goal is to find a prefix-free code $C^*$ that minimizes the expected codeword length:

$$C^* = \arg\min_{C \in \mathcal{C}_{\text{prefix}}} \bar{L}(C, P)$$

where $\mathcal{C}_{\text{prefix}}$ is the set of all prefix-free binary codes on $\mathcal{A}$.

### Entropy as a lower bound

The [entropy](/lectures/kl-divergence/08-adjacent-concepts/#entropy) of the source provides a fundamental lower bound on the expected codeword length of any uniquely decodable code:

$$H(P) \leq \bar{L}(C, P)$$

where $H(P) = -\sum_{i=1}^{n} p_i \log_2 p_i$ is the Shannon entropy measured in bits.

Furthermore, for any source $P$ there exists a prefix-free code with:

$$H(P) \leq \bar{L}(C, P) < H(P) + 1$$

Huffman coding achieves the minimum expected length among all prefix-free codes, making it **optimal** in this class.

## The Huffman algorithm

**Input:** Alphabet $\mathcal{A} = \{a_1, \ldots, a_n\}$ with probabilities $p_1, \ldots, p_n > 0$.

**Output:** A prefix-free binary code $C^*$ with minimum expected codeword length.

**Procedure:**

1. Create a leaf node for each symbol $a_i$ with weight $p_i$. Insert all leaf nodes into a priority queue $\mathcal{Q}$ ordered by ascending weight.

2. While $|\mathcal{Q}| > 1$:
   - Extract the two nodes $u, v$ with the smallest weights from $\mathcal{Q}$.
   - Create a new internal node $w$ with:
     - left child $u$, right child $v$
     - weight $p_w = p_u + p_v$
   - Insert $w$ into $\mathcal{Q}$.

3. The remaining node in $\mathcal{Q}$ is the root of the Huffman tree $\mathcal{T}$.

4. Assign codewords by traversing $\mathcal{T}$ from the root: append $0$ when descending to the left child and $1$ when descending to the right child. The codeword of a symbol is the binary string accumulated along the path from the root to its leaf.

### Formal description

Let $\mathcal{T} = (V, E)$ be the resulting binary tree. For each leaf node corresponding to symbol $a_i$, define the codeword length as:

$$l_i = \text{depth}_\mathcal{T}(a_i)$$

where $\text{depth}_\mathcal{T}(a_i)$ is the number of edges on the path from the root to the leaf of $a_i$.

The code constructed by this algorithm satisfies:

$$\bar{L}(C^*, P) = \sum_{i=1}^{n} p_i \, \text{depth}_\mathcal{T}(a_i)$$

which is equal to the **weighted external path length** of the tree.

## Worked example

Consider the alphabet $\mathcal{A} = \{A, B, C, D, E\}$ with the following probability distribution:

| Symbol $a_i$ | $p_i$ |
|:---:|:---:|
| A | 0.40 |
| B | 0.20 |
| C | 0.15 |
| D | 0.15 |
| E | 0.10 |

**Shannon entropy of the source:**

$$H(P) = -\sum_{i=1}^{5} p_i \log_2 p_i$$

$$= -(0.40 \log_2 0.40 + 0.20 \log_2 0.20 + 0.15 \log_2 0.15 + 0.15 \log_2 0.15 + 0.10 \log_2 0.10)$$

$$= -(0.40 \cdot (-1.322) + 0.20 \cdot (-2.322) + 0.15 \cdot (-2.737) + 0.15 \cdot (-2.737) + 0.10 \cdot (-3.322))$$

$$= -(- 0.529 - 0.464 - 0.411 - 0.411 - 0.332)$$

$$= 2.147 \text{ bits}$$

### Step-by-step construction

**Iteration 1:** Extract the two lowest-weight nodes: $E$ ($0.10$) and $C$ ($0.15$).

Create internal node $N_1$ with weight $0.10 + 0.15 = 0.25$.

Queue: $\{D: 0.15,\; B: 0.20,\; N_1: 0.25,\; A: 0.40\}$

**Iteration 2:** Extract $D$ ($0.15$) and $B$ ($0.20$).

Create internal node $N_2$ with weight $0.15 + 0.20 = 0.35$.

Queue: $\{N_1: 0.25,\; N_2: 0.35,\; A: 0.40\}$

**Iteration 3:** Extract $N_1$ ($0.25$) and $N_2$ ($0.35$).

Create internal node $N_3$ with weight $0.25 + 0.35 = 0.60$.

Queue: $\{A: 0.40,\; N_3: 0.60\}$

**Iteration 4:** Extract $A$ ($0.40$) and $N_3$ ($0.60$).

Create root node $R$ with weight $0.40 + 0.60 = 1.00$.

### Resulting Huffman tree

```
          R (1.00)
         /        \
      0 /          \ 1
       /            \
    A (0.40)      N3 (0.60)
                 /        \
              0 /          \ 1
               /            \
           N1 (0.25)     N2 (0.35)
           /     \        /     \
        0 /       \ 1  0 /       \ 1
         /         \    /         \
     E (0.10)  C (0.15) D (0.15)  B (0.20)
```

### Codeword assignment

Reading the bit labels from root to leaf:

| Symbol | Probability | Codeword | Length $l_i$ |
|:---:|:---:|:---:|:---:|
| A | 0.40 | `0` | 1 |
| B | 0.20 | `111` | 3 |
| C | 0.15 | `101` | 3 |
| D | 0.15 | `110` | 3 |
| E | 0.10 | `100` | 3 |

### Verification

**Prefix-free check:** No codeword is a prefix of another. The codeword `0` (for A) is not a prefix of any of `100`, `101`, `110`, `111` since they all start with `1`.

**Kraft's inequality:**

$$\sum_{i=1}^{5} 2^{-l_i} = 2^{-1} + 2^{-3} + 2^{-3} + 2^{-3} + 2^{-3} = 0.5 + 0.125 \cdot 4 = 1.0 \leq 1 \quad \checkmark$$

**Expected codeword length:**

$$\bar{L}(C^*, P) = 0.40 \cdot 1 + 0.20 \cdot 3 + 0.15 \cdot 3 + 0.15 \cdot 3 + 0.10 \cdot 3$$

$$= 0.40 + 0.60 + 0.45 + 0.45 + 0.30 = 2.20 \text{ bits}$$

**Comparison with entropy:**

$$H(P) = 2.147 \leq \bar{L}(C^*, P) = 2.20 < H(P) + 1 = 3.147 \quad \checkmark$$

The Huffman code achieves an expected length only $0.053$ bits above the entropy, which represents a **redundancy** of:

$$r = \bar{L}(C^*, P) - H(P) = 2.20 - 2.147 = 0.053 \text{ bits}$$

Compared to a fixed-length code which would require $\lceil \log_2 5 \rceil = 3$ bits per symbol, the Huffman code saves $3 - 2.20 = 0.80$ bits per symbol on average, a $26.7\%$ reduction.

## Optimality of Huffman coding

**Theorem:** Among all prefix-free binary codes for a source with distribution $P$, the Huffman code minimizes the expected codeword length.

**Proof sketch (by induction on the alphabet size $n$):**

**Base case** ($n = 2$): With two symbols, the optimal prefix-free code assigns codewords `0` and `1`. This is exactly what Huffman produces.

**Inductive step:** Assume the Huffman algorithm produces an optimal code for any alphabet of size $n - 1$. We show it is optimal for size $n$.

Two key lemmas:

**Lemma 1:** In an optimal prefix-free code, if $p_i > p_j$ then $l_i \leq l_j$.

*Proof:* Suppose for contradiction that $p_i > p_j$ but $l_i > l_j$. Swapping the codewords of $a_i$ and $a_j$ would change the expected length by:

$$\Delta \bar{L} = (p_i - p_j)(l_j - l_i) < 0$$

since $p_i - p_j > 0$ and $l_j - l_i < 0$. This contradicts optimality.

**Lemma 2:** There exists an optimal code in which the two least probable symbols are siblings (share the same parent) and have the longest codewords.

*Proof:* By Lemma 1, the two least probable symbols $a_{n-1}, a_n$ have the longest codewords in any optimal code. If they are not siblings, we can swap one of them with a sibling of the other without increasing the expected length (since they have equal depth), obtaining an optimal code where they are siblings.

**Inductive argument:** Let $a_{n-1}$ and $a_n$ be the two least probable symbols. By Lemma 2, there is an optimal code where they are siblings at maximum depth. We can replace them by a single merged symbol $a'$ with $p' = p_{n-1} + p_n$, reducing the alphabet to $n - 1$ symbols. By the inductive hypothesis, Huffman produces an optimal code for this reduced alphabet. Restoring the split of $a'$ back into $a_{n-1}$ and $a_n$ (by appending `0` and `1`) yields the Huffman code for the original alphabet, and its expected length equals the optimal for the reduced problem plus $p'$. Since any code for the original alphabet can be similarly reduced, and the reduced Huffman code is optimal, the original Huffman code is optimal. $\square$

## Connection to information theory

The redundancy of Huffman coding can be understood through the lens of [KL divergence](/lectures/kl-divergence/01-introduction/). Define the **implicit distribution** $Q$ induced by a code with lengths $l_1, \ldots, l_n$:

$$q_i = \frac{2^{-l_i}}{\sum_{j=1}^{n} 2^{-l_j}}$$

If the code satisfies Kraft's inequality with equality (as Huffman codes typically do), then $q_i = 2^{-l_i}$ and:

$$\bar{L}(C, P) = \sum_{i=1}^{n} p_i \, l_i = -\sum_{i=1}^{n} p_i \log_2 q_i$$

This is the **cross-entropy** $H(P, Q) = -\sum_i p_i \log_2 q_i$, and the redundancy is:

$$r = \bar{L}(C, P) - H(P) = H(P, Q) - H(P) = D_{KL}(P \| Q)$$

The redundancy of any prefix-free code is exactly the KL divergence from the true distribution $P$ to the implicit distribution $Q$ of the code. Huffman coding minimizes this KL divergence over all prefix-free codes.
