---
title: "Length-Limited Huffman Coding"
date: 2026-03-21T11:00:00-06:00
draft: false
type: "document"
description: "Huffman coding with a maximum codeword length constraint"
showDescription: false
weight: 2
---

## Motivation

In practice, many systems impose a **maximum codeword length**. For example:

- DEFLATE (used in gzip, PNG, ZIP) limits codewords to 15 bits.
- JPEG Huffman tables are limited to 16 bits.
- Hardware decoders often use fixed-width lookup tables of $L_{\max}$ bits.

Standard [Huffman coding](/lectures/compression/01-huffman-coding/) minimizes the expected codeword length without any constraint on individual codeword lengths. For a source with $n$ symbols, the Huffman tree can have depth up to $n - 1$, which may violate system constraints.

Length-limited Huffman coding solves the same optimality problem but with an additional hard constraint: no codeword may exceed a prescribed maximum length $L_{\max}$.

## Problem statement

Let $\mathcal{A} = \{a_1, a_2, \ldots, a_n\}$ be a finite alphabet with probability distribution $P$ where $p_i = P(a_i) > 0$ and $\sum_{i=1}^{n} p_i = 1$.

We seek codeword lengths $l_1, l_2, \ldots, l_n \in \mathbb{Z}_{>0}$ that solve:

$$\min_{l_1, \ldots, l_n} \sum_{i=1}^{n} p_i \, l_i$$

$$\text{s.t.} \quad \sum_{i=1}^{n} 2^{-l_i} \leq 1 \quad \text{(Kraft's inequality)}$$

$$l_i \leq L_{\max} \quad \forall \, i \in [n]$$

$$l_i \in \mathbb{Z}_{>0} \quad \forall \, i \in [n]$$

The first constraint ensures a prefix-free code exists with the chosen lengths. The second constraint enforces the length limit. The third restricts lengths to positive integers.

### Feasibility condition

A solution exists if and only if the alphabet can be encoded with codewords of length at most $L_{\max}$, which requires:

$$n \leq 2^{L_{\max}}$$

If $n > 2^{L_{\max}}$, there are not enough distinct binary strings of length $\leq L_{\max}$ to assign a unique codeword to each symbol, and the problem is infeasible.

## Why standard Huffman can violate the length limit

In unconstrained Huffman coding, the depth of the tree depends on the distribution. Consider an extreme case: a source with $n$ symbols where one symbol has probability close to $1$ and the rest share the remaining mass. The Huffman algorithm will repeatedly merge the low-probability symbols, potentially building a highly unbalanced tree of depth $n - 1$.

For example, with $n = 5$ and the distribution:

| Symbol | $p_i$ |
|:---:|:---:|
| $a_1$ | 0.70 |
| $a_2$ | 0.10 |
| $a_3$ | 0.08 |
| $a_4$ | 0.07 |
| $a_5$ | 0.05 |

Standard Huffman produces:

**Iteration 1:** Merge $a_5$ ($0.05$) and $a_4$ ($0.07$) $\to$ $N_1$ ($0.12$)

Queue: $\{a_3: 0.08,\; a_2: 0.10,\; N_1: 0.12,\; a_1: 0.70\}$

**Iteration 2:** Merge $a_3$ ($0.08$) and $a_2$ ($0.10$) $\to$ $N_2$ ($0.18$)

Queue: $\{N_1: 0.12,\; N_2: 0.18,\; a_1: 0.70\}$

**Iteration 3:** Merge $N_1$ ($0.12$) and $N_2$ ($0.18$) $\to$ $N_3$ ($0.30$)

Queue: $\{N_3: 0.30,\; a_1: 0.70\}$

**Iteration 4:** Merge $N_3$ ($0.30$) and $a_1$ ($0.70$) $\to$ Root ($1.00$)

```
         Root (1.00)
        /           \
     0 /             \ 1
      /               \
  a1 (0.70)        N3 (0.30)
                  /        \
               0 /          \ 1
                /            \
           N1 (0.12)      N2 (0.18)
           /     \         /     \
        0 /       \ 1   0 /       \ 1
         /         \     /         \
    a5 (0.05) a4 (0.07) a3 (0.08) a2 (0.10)
```

| Symbol | Codeword | Length |
|:---:|:---:|:---:|
| $a_1$ | `0` | 1 |
| $a_2$ | `111` | 3 |
| $a_3$ | `110` | 3 |
| $a_4$ | `101` | 3 |
| $a_5$ | `100` | 3 |

Here the maximum depth is $3$, which is manageable. But with more skewed distributions or larger alphabets, the tree depth can grow much larger. If $L_{\max} = 2$ were imposed, this code would be infeasible and a different assignment would be needed.

## The Package-Merge algorithm

The most well-known algorithm for length-limited Huffman coding is the **Package-Merge algorithm** due to Larmore and Hirschberg (1990). It reduces the problem to a variant of the **coin collector's problem**.

### The coin collector's problem

Imagine you must pay for an item of cost $n - 1$ using coins of denominations $2^{-1}, 2^{-2}, \ldots, 2^{-L_{\max}}$, where you have exactly $n$ coins of each denomination (one per symbol at each level). The goal is to select a minimum-cost subset of coins whose total value is at least $n - 1$, subject to a specific grouping constraint.

Each coin at denomination $2^{-j}$ corresponds to assigning one unit of depth $j$ in the tree. The total "value" of selected coins gives the Kraft sum, and the "cost" of each coin is the probability $p_i$ of the symbol it represents.

### Algorithm

**Input:** Symbols $a_1, \ldots, a_n$ with probabilities $p_1 \leq p_2 \leq \cdots \leq p_n$, and maximum length $L_{\max}$.

**Output:** Lengths $l_1, \ldots, l_n$ satisfying the length-limited optimality.

**Procedure:**

1. For each level $j = 1, 2, \ldots, L_{\max}$, create a list of $n$ **items**, one per symbol, each with cost $p_i$ and width $2^{-j}$.

2. Starting from level $j = L_{\max}$ (the deepest), process levels from bottom to top:
   - Sort all items at this level by cost.
   - Group consecutive pairs of items into **packages**. Each package has cost equal to the sum of its two items' costs and width equal to the sum of their widths.
   - Promote these packages to the next level $j - 1$, merging them with the items already there.

3. At level $j = 1$, sort all items (original and promoted packages) by cost. Greedily select the $2(n-1)$ cheapest items from the merged lists across all levels, collecting enough total width to satisfy Kraft's inequality with equality.

4. The codeword length $l_i$ for symbol $a_i$ equals the number of times $a_i$ appears in the selected items (counting appearances inside packages recursively).

### Complexity

The Package-Merge algorithm runs in $\mathcal{O}(n L_{\max})$ time and $\mathcal{O}(n L_{\max})$ space.

## Worked example

Consider $\mathcal{A} = \{A, B, C, D\}$ with distribution:

| Symbol | $p_i$ |
|:---:|:---:|
| A | 0.10 |
| B | 0.15 |
| C | 0.25 |
| D | 0.50 |

and $L_{\max} = 2$.

### Unconstrained Huffman

**Iteration 1:** Merge $A$ ($0.10$) and $B$ ($0.15$) $\to$ $N_1$ ($0.25$)

**Iteration 2:** Merge $C$ ($0.25$) and $N_1$ ($0.25$) $\to$ $N_2$ ($0.50$)

**Iteration 3:** Merge $D$ ($0.50$) and $N_2$ ($0.50$) $\to$ Root ($1.00$)

```
        Root (1.00)
       /           \
    0 /             \ 1
     /               \
  D (0.50)        N2 (0.50)
                 /        \
              0 /          \ 1
               /            \
          C (0.25)       N1 (0.25)
                         /     \
                      0 /       \ 1
                       /         \
                  A (0.10)    B (0.15)
```

| Symbol | Codeword | Length |
|:---:|:---:|:---:|
| D | `0` | 1 |
| C | `10` | 2 |
| A | `110` | 3 |
| B | `111` | 3 |

The maximum codeword length is $3$, which **violates** $L_{\max} = 2$.

Expected length: $\bar{L} = 0.50 \cdot 1 + 0.25 \cdot 2 + 0.10 \cdot 3 + 0.15 \cdot 3 = 1.75$ bits.

### Length-limited solution

With $L_{\max} = 2$, we have $4$ symbols and $2^2 = 4$ available codewords of length $\leq 2$: `00`, `01`, `10`, `11`.

Since we need exactly $4$ codewords and have exactly $4$ strings of length $2$, all codewords must have length $2$.

| Symbol | Codeword | Length |
|:---:|:---:|:---:|
| D | `00` | 2 |
| C | `01` | 2 |
| B | `10` | 2 |
| A | `11` | 2 |

**Kraft's inequality:**

$$\sum_{i=1}^{4} 2^{-l_i} = 4 \cdot 2^{-2} = 1.0 \leq 1 \quad \checkmark$$

**Expected length:**

$$\bar{L} = 0.50 \cdot 2 + 0.25 \cdot 2 + 0.15 \cdot 2 + 0.10 \cdot 2 = 2.00 \text{ bits}$$

**Cost of the length constraint:**

The length limit increases the expected length from $1.75$ to $2.00$, an overhead of $0.25$ bits per symbol. This is the price we pay for the hard constraint.

### A less trivial case: $L_{\max} = 3$

Consider instead $\mathcal{A} = \{A, B, C, D, E, F\}$ with:

| Symbol | $p_i$ |
|:---:|:---:|
| A | 0.03 |
| B | 0.05 |
| C | 0.07 |
| D | 0.15 |
| E | 0.25 |
| F | 0.45 |

and $L_{\max} = 3$.

**Unconstrained Huffman** would produce a tree of depth $5$, assigning very long codewords to $A$ and $B$. With $L_{\max} = 3$ we need all codewords to be at most $3$ bits.

Applying the Package-Merge algorithm:

**Level 3** ($2^{-3} = 0.125$ each): Items sorted by cost:

$$[A: 0.03,\; B: 0.05,\; C: 0.07,\; D: 0.15,\; E: 0.25,\; F: 0.45]$$

Package consecutive pairs:

$$[AB: 0.08,\; CD: 0.22,\; EF: 0.70]$$

**Level 2** ($2^{-2} = 0.25$ each): Merge level-2 items with promoted packages, sorted:

$$[A: 0.03,\; B: 0.05,\; C: 0.07,\; AB: 0.08,\; D: 0.15,\; CD: 0.22,\; E: 0.25,\; F: 0.45,\; EF: 0.70]$$

Package consecutive pairs:

$$[AB_2: 0.08,\; C{+}AB: 0.15,\; D{+}CD: 0.37,\; E{+}F: 0.70]$$

(the unpaired item $EF: 0.70$ is dropped)

**Level 1** ($2^{-1} = 0.5$ each): Merge level-1 items with promoted packages, sorted:

$$[A: 0.03,\; B: 0.05,\; C: 0.07,\; AB_2: 0.08,\; C{+}AB: 0.15,\; D: 0.15,\; E: 0.25,\; D{+}CD: 0.37,\; F: 0.45,\; E{+}F: 0.70]$$

We need to select the $2(n-1) = 10$ cheapest items across all levels. We select all items and count each symbol's appearances:

| Symbol | Appearances | $l_i$ |
|:---:|:---:|:---:|
| F | 2 | 2 |
| E | 2 | 2 |
| D | 3 | 3 |
| C | 3 | 3 |
| B | 3 | 3 |
| A | 3 | 3 |

The resulting length-limited code:

| Symbol | Length | Codeword |
|:---:|:---:|:---:|
| F | 2 | `00` |
| E | 2 | `01` |
| D | 3 | `100` |
| C | 3 | `101` |
| B | 3 | `110` |
| A | 3 | `111` |

**Kraft's inequality:**

$$2 \cdot 2^{-2} + 4 \cdot 2^{-3} = 0.5 + 0.5 = 1.0 \leq 1 \quad \checkmark$$

**Expected length:**

$$\bar{L} = 0.45 \cdot 2 + 0.25 \cdot 2 + 0.15 \cdot 3 + 0.07 \cdot 3 + 0.05 \cdot 3 + 0.03 \cdot 3 = 1.40 + 0.90 = 2.30 \text{ bits}$$

## Formulation as a constrained optimization problem

The length-limited Huffman coding problem can be stated as an integer optimization problem. Given a source with alphabet $\mathcal{A} = \{a_1, \ldots, a_n\}$ and probability distribution $P = (p_1, \ldots, p_n)$, and a maximum codeword length $L_{\max} \in \mathbb{Z}_{>0}$:

$$
\begin{array}{ll}
\min_{l_1, \ldots, l_n} & \displaystyle\sum_{i=1}^{n} p_i \, l_i \\\\
\text{s.t.} & \displaystyle\sum_{i=1}^{n} 2^{-l_i} \leq 1 & \text{(Kraft's inequality)} \\\\
& 1 \leq l_i \leq L_{\max} & \forall \, i \in [n] \quad \text{(length limit)} \\\\
& l_i \in \mathbb{Z} & \forall \, i \in [n] \quad \text{(integrality)}
\end{array}
$$

This is an **integer nonlinear program** (INLP). The objective is linear in the decision variables $l_i$, but the Kraft constraint $\sum_i 2^{-l_i} \leq 1$ is nonlinear (it is a sum of convex exponential terms in $-l_i$).

### Equivalent binary formulation

We can reformulate this as an integer linear program by introducing binary decision variables. Define $x_{ij} \in \{0,1\}$ to indicate whether symbol $a_i$ receives a codeword of length $j$:

$$x_{ij} = \begin{cases} 1 & \text{if } l_i = j \\ 0 & \text{otherwise} \end{cases}$$

for $i \in [n]$ and $j \in [L_{\max}]$.

The problem becomes:

$$
\begin{array}{lll}
\min_{x} & \displaystyle\sum_{i=1}^{n} \sum_{j=1}^{L_{\max}} p_i \, j \, x_{ij} \\\\
\text{s.t.} & \displaystyle\sum_{j=1}^{L_{\max}} x_{ij} = 1 & \forall \, i \in [n] \quad \text{(each symbol gets exactly one length)} \\\\
& \displaystyle\sum_{i=1}^{n} \sum_{j=1}^{L_{\max}} 2^{-j} \, x_{ij} \leq 1 & \text{(Kraft's inequality)} \\\\
& x_{ij} \in \{0, 1\} & \forall \, i \in [n], \, j \in [L_{\max}]
\end{array}
$$

In this formulation:
- The objective is linear in $x_{ij}$.
- The Kraft constraint is also linear in $x_{ij}$ (the $2^{-j}$ coefficients are constants).
- The assignment constraints ensure each symbol is assigned exactly one codeword length.

This is an **integer linear program** (ILP) with $n \cdot L_{\max}$ binary variables, $n$ assignment constraints, and one Kraft constraint. While ILPs are NP-hard in general, the special structure of this problem (the Kraft constraint has a totally unimodular-like structure when relaxed) allows the Package-Merge algorithm to solve it exactly in $\mathcal{O}(n L_{\max})$ time.

### Connection to resource allocation

The ILP formulation reveals that length-limited Huffman coding is a special case of the **minimum-cost resource allocation problem**: distribute a limited resource (the Kraft budget of $1$) among $n$ agents (symbols), where each agent must receive a resource share of the form $2^{-j}$ for some integer $j \leq L_{\max}$, and the cost of assigning share $2^{-j}$ to agent $i$ is $p_i \cdot j$.
