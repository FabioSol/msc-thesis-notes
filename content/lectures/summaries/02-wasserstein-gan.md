---
title: "Wasserstein GAN"
date: 2026-01-20T18:28:37-06:00
draft: false
type: "document"
description: "Wasserstein GAN Part-1(KL-Divergence Vs Jensen-Shannon Divergence Vs Wasserstein Distance)"
showDescription: true
weight: 1
---

[see resource](https://www.youtube.com/watch?v=4uEIMCa_BAo)

## GAN

Gan architecture has two important components, the generator and the discriminator. 
The generator create images from a random sample that aim to look real.
The discriminator tries to diferenciate between the actual real and the generated images.

In the past, cost functions like KL-divergence and Jensen shanon divergence have been used in GAN architectures but Wasswrstein distance have prevailed.

Suppose we consider the outcome of the generator a distribution $P$, and the real images a different distribution $Q$. Then the optimization problem becomes to approximate $P$ to $Q$.

But a problem arises, its very common that the overlap is 0, this is problematic for the reasons we will explore here.

lets calculate the different distances for the following distributions:

|   | P   | Q   |
|---|-----|-----|
| 1 | 0.4 | 0   |
| 2 | 0.6 | 0   |
| 3 | 0   | 0   |
| 4 | 0   | 0.5 |
| 5 | 0   | 0.5 |

### KL divergenge

$$D_{KL}(P||Q)=\sum_{x\in \mathcal{X}} P(x)\log(\frac{P(x)}{Q(x)})$$

$$=P(1)\log(\frac{P(1)}{Q(1)})+P(2)\log(\frac{P(2)}{Q(2)})+...$$

$$=0.4\log(\frac{0.4}{0})+0.6\log(\frac{0.6}{0})+0\log(\frac{0}{0})+0\log(\frac{0}{0.5})+0\log(\frac{0}{0.5})$$

you can see the problem, we have only indeterminations and zeros.

### Jennsen-Shannon divergence

$$D_{JSD}(P||Q)=\frac{1}{2}D_{KL}(P||M)+\frac{1}{2}D_{KL}(Q||M)$$
where $M=\frac{1}{2}(P+Q)$

so lets calculate it:

$$D_{KL}(P||M)=0.4\log(\frac{0.4}{0.2})+0.6\log(\frac{0.6}{0.3})+0\log(\frac{0}{0})+0\log(\frac{0}{0.25})+0\log(\frac{0}{0.25})$$

$$D_{KL}(P||M)=0.4\log(2)+0.6\log(2)$$

$$D_{KL}(Q||M)=0\log(\frac{0}{0.2})+0\log(\frac{0}{0.3})+0\log(\frac{0}{0})+0.5\log(\frac{0.5}{0.25})+0.5\log(\frac{0.5}{0.25})$$

$$D_{KL}(Q||M)=0.5\log(2)+0.5\log(2)$$

$$D_{JSD}(P||Q)=\frac{1}{2}(0.4\log(2)+0.6\log(2))+\frac{1}{2}(0.5\log(2)+0.5\log(2))$$

$$D_{JSD}(P||Q)=\log(2)$$

even ignoring the indetermination at the gap, this divergence fails to capture the divergence, for non overlapping distributions the divergence will always be $log(2)$

### Wasserstein Distance

$$w(P,Q)=\inf_{\gamma \in \Gamma(P,Q)}\mathbb{E}_{(x,y)\sim \gamma}[||x-y||]$$

The Wasserstein distance is the cost of transforming P into Q, for this we need an optimal transport plan. 

for this case this will be the movement sequence:

1. 0.4 pos(1) -> pos(4)
2. 0.1 pos(2) -> pos(4)
3. 0.5 pos(2) -> pos(5)

this means:

| step | mass | distance |
|-|-|-|
|1|0.4|3|
|2|0.1|2|
|3|0.5|3|


So the divergence is:

$$\sum_{i,j} \gamma_{ij}\, d(x_i, y_j)
=0.4*3+0.1*2+0.5*3=2.9$$

