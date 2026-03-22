---
title: "DRO-Huffman Coding with Ordered Ground Metric"
date: 2026-03-21T14:00:00-06:00
draft: false
type: "document"
description: "DRO-Huffman coding using a rank-based ground metric for more realistic distributional uncertainty"
showDescription: false
weight: 4
---

## Motivation

In the [previous formulation](/lectures/compression/03-dro-huffman/), we equipped the alphabet with the **trivial metric** $d(a_i, a_j) = \mathbf{1}_{i \neq j}$, which treats all symbol confusions as equally costly. This is the most conservative choice: the adversary can shift mass from any symbol to any other symbol at unit cost.

In practice, estimation errors are not uniform. If we observe a text corpus and estimate letter frequencies, the symbols most likely to be confused are those with **similar frequencies**. It is far more plausible that our estimate of $P(\text{E})$ and $P(\text{T})$ are swapped (both are common letters) than that $P(\text{E})$ and $P(\text{Z})$ are swapped (one is 181 times more frequent than the other).

This motivates a **rank-based ground metric**: index symbols by their position in the sorted empirical distribution $\hat{P}$ and define the distance between two symbols as the gap between their ranks.

## Setup

Let $\mathcal{A} = \{a_1, a_2, \ldots, a_n\}$ be a finite alphabet with empirical distribution $\hat{P}_N$ where the symbols are sorted in decreasing probability order:

$$\hat{p}_1 \geq \hat{p}_2 \geq \cdots \geq \hat{p}_n > 0$$

The index $i$ is the **frequency rank** of symbol $a_i$: rank 1 is the most common, rank $n$ is the rarest.

## The ordered ground metric

We define the ground metric as the **rank distance**:

$$d(a_i, a_j) = |i - j|$$

Under this metric, the type-1 Wasserstein distance between two distributions $Q$ and $Q'$ on $\mathcal{A}$ is:

$$W_1(Q, Q') = \inf_{\gamma \in \Pi(Q, Q')} \sum_{i,j} |i - j| \, \gamma_{ij}$$

where $\Pi(Q, Q')$ is the set of all couplings with marginals $Q$ and $Q'$.

This metric captures the intuition that:

- **Nearby ranks** (similar frequencies): mass can be exchanged cheaply. Confusing $a_3$ and $a_4$ costs 1.
- **Distant ranks** (very different frequencies): mass transport is expensive. Confusing $a_1$ and $a_{26}$ costs 25.
- **The Wasserstein ball is elongated** along the frequency axis, not spherical as with the trivial metric.

## Ambiguity set

The Wasserstein ball under the ordered metric is:

$$\mathbb{B}_\varepsilon^{\text{ord}}(\hat{P}_N) = \left\{ Q \in \mathcal{P}(\mathcal{A}) : W_1^{\text{ord}}(Q, \hat{P}_N) \leq \varepsilon \right\}$$

Compared to the trivial-metric ball $\mathbb{B}_\varepsilon^{\text{triv}}$, the ordered-metric ball contains distributions that are **locally perturbed** — mass is redistributed among symbols with similar frequencies. For the same $\varepsilon$, the ordered ball is **larger** in the total variation sense (it allows more redistribution between nearby symbols) but **smaller** in terms of long-range perturbations.

## DRO-Huffman with ordered metric

The worst-case risk for a code with lengths $l = (l_1, \ldots, l_n)$ is:

$$\mathcal{R}_\varepsilon^{\text{ord}}(\hat{P}_N, l) = \sup_{Q \in \mathbb{B}_\varepsilon^{\text{ord}}(\hat{P}_N)} \sum_{i=1}^{n} q_i \, l_i$$

And the DRO-optimal code is:

$$l^* = \arg\min_{l \in \mathcal{L}} \; \mathcal{R}_\varepsilon^{\text{ord}}(\hat{P}_N, l)$$

## Tractable reformulation

Applying the strong duality result from the [DRO framework](/lectures/summaries/04-wasserstein-dro/) with $p = 1$:

$$\mathcal{R}_\varepsilon^{\text{ord}}(\hat{P}_N, l) = \inf_{\lambda \geq 0} \left\{ \lambda \varepsilon + \sum_{i=1}^{n} \hat{p}_i \sup_{j \in [n]} \left\{ l_j - \lambda |i - j| \right\} \right\}$$

### The inner supremum

For each symbol $a_i$ at rank $i$, the adversary solves:

$$\phi_\lambda(i) = \sup_{j \in [n]} \left\{ l_j - \lambda |i - j| \right\}$$

This has a geometric interpretation. Consider plotting $(j, l_j)$ for all symbols: the codeword length as a function of rank. For a given $\lambda$, the adversary places a **cone** of slope $\lambda$ centered at position $i$ and finds the symbol $j$ whose length $l_j$ protrudes the most above the cone.

Formally:

- If $j > i$ (moving mass to a rarer symbol): gain is $l_j - \lambda(j - i)$. The adversary wants $j$ where $l_j$ increases faster than $\lambda$ per rank.
- If $j < i$ (moving mass to a more common symbol): gain is $l_j - \lambda(i - j)$. Less useful since common symbols have shorter codes.
- If $j = i$ (no transport): gain is $l_i$ at zero cost.

For non-decreasing lengths (as in any optimal code for sorted probabilities), the adversary typically targets symbols to the **right** of $i$ (rarer symbols with longer codewords), but only if the length increase outpaces the transport cost $\lambda$ per rank.

### Contrast with the trivial metric

Under the trivial metric ($d = \mathbf{1}_{i \neq j}$), the inner supremum was:

$$\max(l_i, \; M_i - \lambda)$$

where $M_i = \max_{j \neq i} l_j$. The adversary could jump to the **longest** codeword at cost $\lambda$, regardless of how far away it is.

Under the ordered metric, the adversary pays **proportionally** to rank distance. A rare symbol with a very long codeword is only exploitable by nearby symbols — distant common symbols would have to pay too much transport cost to reach it.

## Lipschitz regularization: slope vs spread

The [Lipschitz upper bound](/lectures/compression/03-dro-huffman/#the-soft-length-limit-mechanism) takes a different form under the ordered metric. The Lipschitz constant of the loss $\ell(a_i) = l_i$ with respect to $d(a_i, a_j) = |i - j|$ is:

$$\text{Lip}^{\text{ord}}(\ell) = \max_{i \neq j} \frac{|l_i - l_j|}{|i - j|}$$

For non-decreasing lengths this simplifies to the maximum **slope** of the length function between any pair of consecutive ranks where the length changes:

$$\text{Lip}^{\text{ord}}(\ell) = \max_{i} (l_{i+1} - l_i)$$

The upper bound becomes:

$$\mathcal{R}_\varepsilon^{\text{ord}}(\hat{P}_N, l) \leq \sum_{i=1}^{n} \hat{p}_i \, l_i + \varepsilon \cdot \max_{i}(l_{i+1} - l_i)$$

| Metric | Lipschitz constant | Penalizes |
|---|---|---|
| Trivial: $d = \mathbf{1}_{i \neq j}$ | $l_{\max} - l_{\min}$ | total spread of lengths |
| Ordered: $d = \|i - j\|$ | $\max_i(l_{i+1} - l_i)$ | sharpest jump between adjacent ranks |

The ordered metric penalizes **abrupt transitions** in codeword lengths. A code that gradually increases lengths from 3 to 7 over 26 symbols has a small Lipschitz constant ($\approx 1$), even though the spread is 4. A code that jumps from 3 to 7 between two adjacent ranks has Lipschitz constant 4.

This means the DRO-optimal code under the ordered metric prefers **smooth length gradients** over abrupt boundaries, while still allowing a reasonable total spread if the transition is gradual.

## Worked example

Consider $\mathcal{A} = \{a_1, a_2, a_3, a_4, a_5, a_6\}$ with sorted probabilities:

| Rank $i$ | $\hat{p}_i$ |
|:---:|:---:|
| 1 | 0.35 |
| 2 | 0.25 |
| 3 | 0.15 |
| 4 | 0.12 |
| 5 | 0.08 |
| 6 | 0.05 |

Shannon entropy: $H \approx 2.29$ bits.

### Candidate codes

**Code A** (Huffman-like): $l = (2, 2, 3, 3, 4, 4)$

- Spread: $4 - 2 = 2$
- Max slope: $\max(0, 1, 0, 1, 0) = 1$
- $\text{Lip}^{\text{triv}} = 2$, $\text{Lip}^{\text{ord}} = 1$

**Code B** (abrupt jump): $l = (2, 2, 2, 4, 4, 4)$

- Spread: $4 - 2 = 2$
- Max slope: $\max(0, 0, 2, 0, 0) = 2$
- $\text{Lip}^{\text{triv}} = 2$, $\text{Lip}^{\text{ord}} = 2$

**Code C** (uniform): $l = (3, 3, 3, 3, 3, 3)$

- Spread: $0$
- Max slope: $0$
- $\text{Lip}^{\text{triv}} = 0$, $\text{Lip}^{\text{ord}} = 0$

Under the **trivial metric**, Codes A and B have the same Lipschitz constant (2), so the DRO sees them as equally vulnerable. Under the **ordered metric**, Code A ($\text{Lip}^{\text{ord}} = 1$) is preferred over Code B ($\text{Lip}^{\text{ord}} = 2$) because its gradual length increase is harder for the adversary to exploit.

### Computing $\phi_\lambda(i)$ for Code A under the ordered metric

$l = (2, 2, 3, 3, 4, 4)$ with $\lambda = 1$:

For $i = 1$: $\phi_1(1) = \max(2,\; 2-1,\; 3-2,\; 3-3,\; 4-4,\; 4-5) = \max(2, 1, 1, 0, 0, -1) = 2$

For $i = 2$: $\phi_1(2) = \max(2-1,\; 2,\; 3-1,\; 3-2,\; 4-3,\; 4-4) = \max(1, 2, 2, 1, 1, 0) = 2$

For $i = 3$: $\phi_1(3) = \max(2-2,\; 2-1,\; 3,\; 3-1,\; 4-2,\; 4-3) = \max(0, 1, 3, 2, 2, 1) = 3$

For $i = 4$: $\phi_1(4) = \max(2-3,\; 2-2,\; 3-1,\; 3,\; 4-1,\; 4-2) = \max(-1, 0, 2, 3, 3, 2) = 3$

For $i = 5$: $\phi_1(5) = \max(2-4,\; 2-3,\; 3-2,\; 3-1,\; 4,\; 4-1) = \max(-2, -1, 1, 2, 4, 3) = 4$

For $i = 6$: $\phi_1(6) = \max(2-5,\; 2-4,\; 3-3,\; 3-2,\; 4-1,\; 4) = \max(-3, -2, 0, 1, 3, 4) = 4$

Worst-case risk at $\lambda = 1$:

$$f(1) = 1 \cdot \varepsilon + 0.35 \cdot 2 + 0.25 \cdot 2 + 0.15 \cdot 3 + 0.12 \cdot 3 + 0.08 \cdot 4 + 0.05 \cdot 4$$

$$= \varepsilon + 0.70 + 0.50 + 0.45 + 0.36 + 0.32 + 0.20 = \varepsilon + 2.53$$

For $\lambda = 0$ (no transport penalty): all $\phi_0(i) = l_{\max} = 4$, so $f(0) = 0 + 4.0 = 4.0$.

For $\lambda = 2$ (expensive transport): each $\phi_2(i) = l_i$ (the adversary cannot profitably move any mass), so $f(2) = 2\varepsilon + \sum \hat{p}_i l_i = 2\varepsilon + 2.53$.

The optimal $\lambda$ for Code A depends on $\varepsilon$ and balances the transport penalty against the adversary's ability to exploit nearby symbols with longer codewords.

### Computing $\phi_\lambda(i)$ for Code B under the ordered metric

$l = (2, 2, 2, 4, 4, 4)$ with $\lambda = 1$:

For $i = 3$: $\phi_1(3) = \max(2-2, 2-1, 2, 4-1, 4-2, 4-3) = \max(0, 1, 2, 3, 2, 1) = 3$

For $i = 2$: $\phi_1(2) = \max(2-1, 2, 2-1, 4-2, 4-3, 4-4) = \max(1, 2, 1, 2, 1, 0) = 2$

The abrupt jump from $l_3 = 2$ to $l_4 = 4$ means that symbol $a_3$ can see $l_4 = 4$ at cost only $\lambda \cdot 1 = 1$, giving a net gain of $4 - 1 = 3 > l_3 = 2$. This is the vulnerability the ordered metric detects but the trivial metric does not.

## Full optimization problem

$$
\begin{array}{ll}
\displaystyle\min_{l, \lambda, s} & \displaystyle\lambda \varepsilon + \sum_{i=1}^{n} \hat{p}_i \, s_i \\\\
\text{s.t.} & s_i \geq l_j - \lambda |i - j| & \forall \, i, j \in [n] \\\\
& \displaystyle\sum_{i=1}^{n} 2^{-l_i} \leq 1 & \text{(Kraft's inequality)} \\\\
& \lambda \geq 0 \\\\
& l_i \in \mathbb{Z}_{>0} & \forall \, i \in [n]
\end{array}
$$

The auxiliary variables $s_i$ represent $\phi_\lambda(i) = \sup_j \{l_j - \lambda|i-j|\}$. The constraints $s_i \geq l_j - \lambda|i-j|$ for all $j$ linearize the supremum.

This problem has $n^2$ linear constraints linking $s_i$ to each $l_j$ (one for every pair $(i, j)$), compared to only $2n$ constraints in the trivial-metric formulation. The richer structure of the ordered metric makes the problem larger but also gives the optimizer more nuance in balancing codeword lengths.

### Binary variable reformulation

As with the [length-limited formulation](/lectures/compression/02-length-limited-huffman/#equivalent-binary-formulation), introducing $x_{ik} \in \{0,1\}$ with $l_i = \sum_{k=1}^{L} k \, x_{ik}$ converts the Kraft constraint into a linear one, yielding a **mixed-integer linear program** (MILP):

$$
\begin{array}{ll}
\displaystyle\min_{x, \lambda, s} & \displaystyle\lambda \varepsilon + \sum_{i=1}^{n} \hat{p}_i \, s_i \\\\
\text{s.t.} & \displaystyle s_i \geq \sum_{k=1}^{L} k \, x_{jk} - \lambda |i - j| & \forall \, i, j \in [n] \\\\
& \displaystyle\sum_{k=1}^{L} x_{ik} = 1 & \forall \, i \in [n] \\\\
& \displaystyle\sum_{i=1}^{n} \sum_{k=1}^{L} 2^{-k} x_{ik} \leq 1 \\\\
& \lambda \geq 0, \quad x_{ik} \in \{0,1\}
\end{array}
$$

## Summary: trivial vs ordered metric

| Aspect | Trivial metric | Ordered metric |
|---|---|---|
| Ground metric | $d(a_i, a_j) = \mathbf{1}_{i \neq j}$ | $d(a_i, a_j) = \|i - j\|$ |
| Adversary cost | uniform: any swap costs $\lambda$ | proportional: distant swaps cost more |
| $W_1$ equals | total variation | earth mover's on rank axis |
| Lipschitz penalty | $l_{\max} - l_{\min}$ (spread) | $\max_i(l_{i+1} - l_i)$ (max slope) |
| DRO effect | flattens all lengths uniformly | smooths length gradients |
| Inner sup size | $O(1)$ per symbol | $O(n)$ per symbol |
| Constraint count | $2n$ | $n^2$ |
| Realism | all confusions equally likely | nearby-frequency confusions more likely |
