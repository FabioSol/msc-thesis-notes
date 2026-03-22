---
title: "Distributionally Robust Huffman Coding"
date: 2026-03-21T12:00:00-06:00
draft: false
type: "document"
description: "Applying Wasserstein DRO to Huffman coding for soft codeword length control under distributional uncertainty"
showDescription: false
weight: 3
---

## Motivation

[Standard Huffman coding](/lectures/compression/01-huffman-coding/) assumes the source distribution $P$ is known exactly. In practice, we only have access to an empirical estimate $\hat{P}_N$ obtained from $N$ observed samples. If the true distribution $P$ differs from $\hat{P}_N$, the code optimized for $\hat{P}_N$ may perform poorly under $P$ — in particular, symbols that were estimated as rare (and assigned long codewords) might be more frequent than expected, inflating the actual expected codeword length.

[Length-limited Huffman coding](/lectures/compression/02-length-limited-huffman/) addresses this by imposing a hard constraint $l_i \leq L_{\max}$. But this approach requires choosing $L_{\max}$ a priori and does not account for the degree of uncertainty in $\hat{P}_N$.

Using the [Wasserstein DRO](/lectures/summaries/04-wasserstein-dro/) framework, we can instead optimize for the **worst-case** distribution within a neighborhood of $\hat{P}_N$. This produces a code that is robust to estimation errors, and the robustness parameter $\varepsilon$ provides a **soft control** over codeword lengths: no hard cap is imposed, but the optimization naturally discourages excessively long codewords because an adversary could exploit them by shifting mass to those symbols.

## Setup

Let $\mathcal{A} = \{a_1, \ldots, a_n\}$ be a finite alphabet. We observe $N$ i.i.d. samples from the unknown true distribution $P$ and form the empirical distribution:

$$\hat{P}_N = \frac{1}{N}\sum_{k=1}^{N} \delta_{\hat{\xi}_k}$$

which assigns probability $\hat{p}_i$ to each symbol $a_i$.

A prefix-free code $C$ assigns codeword lengths $l = (l_1, \ldots, l_n) \in \mathbb{Z}_{>0}^n$ satisfying Kraft's inequality. The expected codeword length under any distribution $Q$ is:

$$\mathcal{R}(Q, l) = \sum_{i=1}^{n} q_i \, l_i$$

where we use $\mathcal{R}$ to denote the risk, following the [DRO framework](/lectures/summaries/04-wasserstein-dro/) convention.

## Choice of metric: $W_1$ with the trivial metric

We equip the alphabet $\mathcal{A}$ with the **trivial (discrete) metric**:

$$d(a_i, a_j) = \mathbf{1}_{i \neq j} = \begin{cases} 0 & \text{if } i = j \\ 1 & \text{if } i \neq j \end{cases}$$

Under this ground metric, the type-1 Wasserstein distance between two distributions $Q, Q'$ on $\mathcal{A}$ equals the **total variation distance**:

$$W_1(Q, Q') = TV(Q, Q') = \frac{1}{2}\sum_{i=1}^{n} |q_i - q_i'|$$

This is the "purest" form of robustness: the adversary can move probability mass from any symbol to any other symbol with equal cost. We make no assumption about which estimation errors are more or less plausible — only that the total amount of redistributed mass is bounded.

## The Wasserstein ambiguity set

Following the DRO framework, we define the ambiguity set as the $W_1$-ball of radius $\varepsilon$ around $\hat{P}_N$:

$$\mathbb{B}_\varepsilon(\hat{P}_N) = \left\{ Q \in \mathcal{P}(\mathcal{A}) : W_1(Q, \hat{P}_N) \leq \varepsilon \right\}$$

This set contains all distributions obtainable from $\hat{P}_N$ by redistributing at most $\varepsilon$ total probability mass.

## The DRO-Huffman problem

For a fixed code with lengths $l$, the **worst-case risk** over the ambiguity set is:

$$\mathcal{R}_\varepsilon(\hat{P}_N, l) = \sup_{Q \in \mathbb{B}_\varepsilon(\hat{P}_N)} \sum_{i=1}^{n} q_i \, l_i$$

The **DRO-optimal code** minimizes this worst-case risk over all prefix-free codes:

$$l^* = \arg\min_{l \in \mathcal{L}} \; \mathcal{R}_\varepsilon(\hat{P}_N, l)$$

where $\mathcal{L} = \left\{ l \in \mathbb{Z}_{>0}^n : \sum_{i=1}^{n} 2^{-l_i} \leq 1 \right\}$ is the set of admissible codeword lengths.

Writing out the min-max:

$$\min_{l \in \mathcal{L}} \; \sup_{Q \in \mathbb{B}_\varepsilon(\hat{P}_N)} \; \sum_{i=1}^{n} q_i \, l_i$$

## Tractable reformulation via strong duality

Solving the inner supremum directly requires optimizing over the infinite-dimensional space of distributions. The strong duality result from the DRO framework transforms this into a finite minimization problem.

For $W_1$ with empirical $\hat{P}_N$, the worst-case risk of a loss function $\ell$ admits the dual:

$$\mathcal{R}_\varepsilon(\hat{P}_N, \ell) = \inf_{\lambda \geq 0} \left\{ \lambda \varepsilon + \sum_{i=1}^{n} \hat{p}_i \sup_{j \in [n]} \left\{ l_j - \lambda \, d(a_i, a_j) \right\} \right\}$$

The decision variable $\lambda \geq 0$ is the dual variable of the Wasserstein constraint and acts as a **penalty scaler**: we pay $\lambda \varepsilon$ for the right to "dampen" the adversary's power by $\lambda$ per unit of transport.

### Simplification under the trivial metric

With $d(a_i, a_j) = \mathbf{1}_{i \neq j}$, the inner supremum becomes:

$$\sup_{j \in [n]} \left\{ l_j - \lambda \, \mathbf{1}_{j \neq i} \right\} = \max\left( l_i, \; \max_{j \neq i}(l_j - \lambda) \right)$$

Define $M_i = \max_{j \neq i} l_j$ as the maximum codeword length **excluding** symbol $a_i$. Then:

$$\sup_{j \in [n]} \left\{ l_j - \lambda \, \mathbf{1}_{j \neq i} \right\} = \max(l_i, \; M_i - \lambda)$$

This expression has a clear interpretation:

- The adversary can either **keep** mass at symbol $a_i$, incurring loss $l_i$, or **move** it to the symbol with the longest codeword among $j \neq i$, incurring loss $M_i$ but paying transport cost $\lambda$.
- If $\lambda$ is large (transport is expensive), the adversary prefers to keep mass in place: the result is $l_i$.
- If $\lambda$ is small (transport is cheap), the adversary ships mass to the longest codeword: the result approaches $M_i$.

The transition occurs at $\lambda = M_i - l_i$:

$$\max(l_i, \; M_i - \lambda) = \begin{cases} M_i - \lambda & \text{if } \lambda < M_i - l_i \\ l_i & \text{if } \lambda \geq M_i - l_i \end{cases}$$

Note that when symbol $a_i$ already has the longest codeword ($l_i = l_{\max}$), then $M_i \leq l_i$, so $M_i - l_i \leq 0$, and the result is always $l_i$ for any $\lambda \geq 0$. The adversary has no incentive to move mass away from the symbol that already has the longest code.

### The full dual problem

Substituting back, the DRO-Huffman problem becomes:

$$\min_{l \in \mathcal{L}} \; \inf_{\lambda \geq 0} \left\{ \lambda \varepsilon + \sum_{i=1}^{n} \hat{p}_i \max(l_i, \; M_i - \lambda) \right\}$$

Since both minimizations are over the same direction (minimizing), we can combine them:

$$\min_{\substack{l \in \mathcal{L} \\ \lambda \geq 0}} \left\{ \lambda \varepsilon + \sum_{i=1}^{n} \hat{p}_i \max(l_i, \; M_i - \lambda) \right\}$$

For fixed $l$, the objective is a **piecewise linear convex function** of $\lambda$, with breakpoints at $\lambda_i^* = M_i - l_i$ for each symbol where $M_i > l_i$. The minimum over $\lambda$ can be found by evaluating the objective at each breakpoint.

## Worked example

Consider $\mathcal{A} = \{A, B, C, D\}$ with empirical distribution:

| Symbol $a_i$ | $\hat{p}_i$ |
|:---:|:---:|
| A | 0.50 |
| B | 0.25 |
| C | 0.15 |
| D | 0.10 |

There are two prefix-free codes with Kraft's inequality satisfied with equality for $n = 4$:

**Code 1** (standard Huffman): $l = (1, 2, 3, 3)$ assigning the shortest codeword to the most probable symbol.

**Code 2** (uniform): $l = (2, 2, 2, 2)$ assigning equal-length codewords to all symbols.

### Worst-case risk for Code 1: $l = (1, 2, 3, 3)$

Compute $M_i$ for each symbol:

| Symbol | $l_i$ | $M_i$ | $M_i - l_i$ |
|:---:|:---:|:---:|:---:|
| A | 1 | 3 | 2 |
| B | 2 | 3 | 1 |
| C | 3 | 3 | 0 |
| D | 3 | 3 | 0 |

The breakpoints are at $\lambda = 0, 1, 2$. We evaluate the objective $f(\lambda) = \lambda \varepsilon + \sum_i \hat{p}_i \max(l_i, M_i - \lambda)$ in each interval.

**Interval $\lambda \in [0, 1]$:** symbols C, D satisfy $\lambda \geq M_i - l_i = 0$, so their terms are $l_i = 3$. Symbols A, B have $\lambda < M_i - l_i$, so their terms are $M_i - \lambda = 3 - \lambda$.

$$f(\lambda) = \lambda\varepsilon + 0.50(3 - \lambda) + 0.25(3 - \lambda) + 0.15 \cdot 3 + 0.10 \cdot 3$$

$$= \lambda\varepsilon + 0.75(3 - \lambda) + 0.75 = 3 + \lambda(\varepsilon - 0.75)$$

This is decreasing when $\varepsilon < 0.75$.

**Interval $\lambda \in [1, 2]$:** symbol B also satisfies $\lambda \geq 1$, so its term becomes $l_B = 2$. Only symbol A still has $\lambda < 2$.

$$f(\lambda) = \lambda\varepsilon + 0.50(3 - \lambda) + 0.25 \cdot 2 + 0.15 \cdot 3 + 0.10 \cdot 3$$

$$= \lambda\varepsilon + 1.50 - 0.50\lambda + 0.50 + 0.75 = 2.75 + \lambda(\varepsilon - 0.50)$$

This is decreasing when $\varepsilon < 0.50$.

**Interval $\lambda \geq 2$:** all symbols satisfy $\lambda \geq M_i - l_i$, so every term reduces to $l_i$.

$$f(\lambda) = \lambda\varepsilon + 0.50 \cdot 1 + 0.25 \cdot 2 + 0.15 \cdot 3 + 0.10 \cdot 3 = 1.75 + \lambda\varepsilon$$

This is always increasing for $\varepsilon > 0$.

**Optimal $\lambda$** depends on $\varepsilon$:

$$\mathcal{R}_\varepsilon^{(1)} = \begin{cases} 1.75 + 2\varepsilon & \text{if } \varepsilon < 0.50 \quad (\lambda^* = 2)\\[4pt] 2.25 + \varepsilon & \text{if } 0.50 \leq \varepsilon < 0.75 \quad (\lambda^* = 1) \\[4pt] 3.00 & \text{if } \varepsilon \geq 0.75 \quad (\lambda^* = 0) \end{cases}$$

### Worst-case risk for Code 2: $l = (2, 2, 2, 2)$

Since all codeword lengths are equal, $M_i = 2$ and $M_i - l_i = 0$ for every symbol. The adversary gains nothing from moving mass — every symbol costs the same.

$$f(\lambda) = \lambda\varepsilon + \sum_i \hat{p}_i \cdot 2 = 2 + \lambda\varepsilon$$

The minimum is at $\lambda^* = 0$:

$$\mathcal{R}_\varepsilon^{(2)} = 2.00 \quad \text{for all } \varepsilon$$

### Comparison and crossover

| $\varepsilon$ | $\mathcal{R}_\varepsilon^{(1)}$ (Huffman) | $\mathcal{R}_\varepsilon^{(2)}$ (Uniform) | DRO-optimal |
|:---:|:---:|:---:|:---:|
| 0.00 | 1.75 | 2.00 | Huffman |
| 0.05 | 1.85 | 2.00 | Huffman |
| 0.10 | 1.95 | 2.00 | Huffman |
| **0.125** | **2.00** | **2.00** | **Tie** |
| 0.20 | 2.15 | 2.00 | Uniform |
| 0.50 | 2.75 | 2.00 | Uniform |
| 1.00 | 3.00 | 2.00 | Uniform |

The crossover occurs at $\varepsilon^* = 0.125$, where $1.75 + 2\varepsilon = 2.00$.

**Interpretation:** When the distributional uncertainty is small ($\varepsilon < 0.125$), the Huffman code is DRO-optimal — its efficiency gains from variable-length coding outweigh the vulnerability to adversarial mass shifting. When uncertainty is large ($\varepsilon > 0.125$), the uniform code wins — its immunity to distribution changes (the adversary cannot exploit any symbol) outweighs the loss in nominal efficiency.

## The soft length-limit mechanism

To see why DRO acts as a soft length limit, consider the Lipschitz regularization upper bound from the [DRO framework](/lectures/summaries/04-wasserstein-dro/#stability-theory):

$$\mathcal{R}_\varepsilon(\hat{P}_N, l) \leq \mathcal{R}(\hat{P}_N, l) + \varepsilon \cdot \text{Lip}(l)$$

where $\text{Lip}(l)$ is the Lipschitz constant of the loss function $\ell(a_i) = l_i$ with respect to the ground metric $d$. Under the trivial metric:

$$\text{Lip}(l) = \max_{i \neq j} \frac{|l_i - l_j|}{d(a_i, a_j)} = \max_{i \neq j} |l_i - l_j| = l_{\max} - l_{\min}$$

So:

$$\mathcal{R}_\varepsilon(\hat{P}_N, l) \leq \underbrace{\sum_{i=1}^{n} \hat{p}_i \, l_i}_{\text{nominal risk}} + \; \varepsilon \cdot \underbrace{(l_{\max} - l_{\min})}_{\text{length spread}}$$

The DRO objective penalizes the **spread** of codeword lengths: the wider the gap between the longest and shortest codewords, the higher the worst-case cost. This is the soft analog of the hard constraint $l_i \leq L_{\max}$:

| Approach | Mechanism | Control parameter |
|---|---|---|
| Hard limit | $l_i \leq L_{\max}$ for all $i$ | $L_{\max}$ |
| DRO soft limit | penalizes $l_{\max} - l_{\min}$ | $\varepsilon$ |

As $\varepsilon$ increases, the optimizer is forced to compress the length spread, eventually converging to a uniform-length code.

## Statistical guarantee

The parameter $\varepsilon$ is not arbitrary — it can be calibrated from the sample size $N$ and a desired confidence level $1 - \delta$.

By the Bretagnolle-Huber inequality for the total variation distance between the empirical and true distributions on a finite alphabet:

$$\mathbb{P}\left( W_1(\hat{P}_N, P) > \varepsilon \right) \leq 2 \exp(-2N\varepsilon^2)$$

Setting the right-hand side equal to $\delta$ and solving for $\varepsilon$:

$$\varepsilon_N(\delta) = \sqrt{\frac{\ln(2/\delta)}{2N}}$$

This gives the guarantee:

$$\mathbb{P}\left( P \in \mathbb{B}_{\varepsilon_N(\delta)}(\hat{P}_N) \right) \geq 1 - \delta$$

If we use $\varepsilon = \varepsilon_N(\delta)$ in the DRO-Huffman problem, the resulting code satisfies:

$$\mathbb{P}\left( \mathcal{R}(P, l^*) \leq \mathcal{R}_{\varepsilon}(\hat{P}_N, l^*) \right) \geq 1 - \delta$$

In words: with probability at least $1 - \delta$, the **actual** expected codeword length under the true distribution $P$ is at most the worst-case value we optimized for. The DRO code provides a **performance certificate** that holds with calibrated confidence.

### Scaling behavior

As $N \to \infty$, $\varepsilon_N(\delta) \to 0$, and the DRO-optimal code converges to the standard Huffman code. With finite samples:

| $N$ | $\varepsilon_{N}(0.05)$ | Regime |
|:---:|:---:|:---:|
| 10 | 0.430 | highly uncertain, favors uniform codes |
| 100 | 0.136 | moderate uncertainty, crossover region |
| 1000 | 0.043 | low uncertainty, close to standard Huffman |
| 10000 | 0.014 | near-certain, essentially standard Huffman |

## Full optimization problem

Combining all elements, the DRO-Huffman coding problem with statistical calibration is:

$$
\begin{array}{ll}
\displaystyle\min_{l, \lambda} & \displaystyle\lambda \varepsilon_N(\delta) + \sum_{i=1}^{n} \hat{p}_i \max\left(l_i, \; M_i - \lambda \right) \\\\
\text{s.t.} & \displaystyle\sum_{i=1}^{n} 2^{-l_i} \leq 1 & \text{(Kraft's inequality)} \\\\
& M_i = \max_{j \neq i} l_j & \forall \, i \in [n] \\\\
& \lambda \geq 0 \\\\
& l_i \in \mathbb{Z}_{>0} & \forall \, i \in [n]
\end{array}
$$

where $\varepsilon_N(\delta) = \sqrt{\frac{\ln(2/\delta)}{2N}}$.

The $\max(l_i, M_i - \lambda)$ terms can be linearized by introducing auxiliary variables $s_i \geq 0$:

$$
\begin{array}{ll}
\displaystyle\min_{l, \lambda, s} & \displaystyle\lambda \varepsilon + \sum_{i=1}^{n} \hat{p}_i \, s_i \\\\
\text{s.t.} & s_i \geq l_i & \forall \, i \in [n] \\\\
& s_i \geq M_i - \lambda & \forall \, i \in [n] \\\\
& \displaystyle\sum_{i=1}^{n} 2^{-l_i} \leq 1 \\\\
& \lambda \geq 0 \\\\
& l_i \in \mathbb{Z}_{>0} & \forall \, i \in [n]
\end{array}
$$

Furthermore, since $M_i = \max_{j \neq i} l_j$, we can replace the $M_i$ coupling with explicit constraints using additional variables $m_i$:

$$
\begin{array}{ll}
\displaystyle\min_{l, \lambda, s, m} & \displaystyle\lambda \varepsilon + \sum_{i=1}^{n} \hat{p}_i \, s_i \\\\
\text{s.t.} & s_i \geq l_i & \forall \, i \in [n] \\\\
& s_i \geq m_i - \lambda & \forall \, i \in [n] \\\\
& m_i \geq l_j & \forall \, i \in [n], \; \forall \, j \neq i \\\\
& \displaystyle\sum_{i=1}^{n} 2^{-l_i} \leq 1 \\\\
& \lambda \geq 0 \\\\
& l_i \in \mathbb{Z}_{>0} & \forall \, i \in [n]
\end{array}
$$

This is a **mixed-integer nonlinear program** (the Kraft constraint contains $2^{-l_i}$). Using the same binary variable reformulation as in the [length-limited formulation](/lectures/compression/02-length-limited-huffman/#equivalent-binary-formulation), introducing $x_{ij} \in \{0,1\}$ with $l_i = \sum_{j=1}^{L} j \, x_{ij}$, the problem can be converted into a **mixed-integer linear program** (MILP) that can be solved with standard solvers.

## Summary

| Property | Standard Huffman | Length-Limited Huffman | DRO Huffman |
|---|---|---|---|
| Assumes $P$ known | yes | yes | no |
| Length control | none | hard: $l_i \leq L_{\max}$ | soft: penalizes $l_{\max} - l_{\min}$ |
| Control parameter | — | $L_{\max}$ | $\varepsilon$ (or equivalently $N, \delta$) |
| Optimality | min $\bar{L}$ | min $\bar{L}$ s.t. $l_i \leq L_{\max}$ | min worst-case $\bar{L}$ over $\mathbb{B}_\varepsilon$ |
| Guarantee | exact if $P$ known | exact if $P$ known | probabilistic: holds with prob $\geq 1 - \delta$ |
| As $N \to \infty$ | — | — | recovers standard Huffman |
