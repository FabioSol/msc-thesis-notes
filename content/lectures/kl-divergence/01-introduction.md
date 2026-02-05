---
title: "Introduction"
date: 2026-01-20T18:28:37-06:00
draft: false
type: "document"
description: "Introduction to Kullback–Leibler divergence"
showDescription: false
weight: 1
---

## What is Kullback–Leibler divergence?

Is the expected excess [surprisal](/lectures/kl-divergence/05-adjacent-concepts/#suprisal) from using the approximation $Q$ instead of $P$ when the actual is $P$

Also called relative entropy and I-divergence, Kullback–Leibler divergence is a measure of how much an approximating probability distribution is different from a true probability distribution.

## Properties

- **Non-negativity**: $D_{KL}(P || Q) \geq 0$ for all probability distributions $P$ and $Q$. Equality holds if and only if $P = Q$ almost everywhere.
- **Asymmetry**: $D_{KL}(P || Q) \neq D_{KL}(Q || P)$ in general. This means that the divergence from $P$ to $Q$ is not the same as from $Q$ to $P$.
- **Not a true metric**: Due to its asymmetry and failure to satisfy the triangle inequality, KL divergence is not a true distance metric.


## Mathematical Definition

It is denoted as $D_{KL}(P || Q)$.

It is defined as:

### Discrete case:

$$D_{KL}(P || Q) = \sum_{x \in \mathcal{X}} P(x) \log \frac{P(x)}{Q(x)}$$

where $\mathcal{X}$ is the set of all possible outcomes.

$P$ is the true distribution, $Q$ is the approximating distribution, and they are both defined on the same sample space $\mathcal{X}$.

another way of expressing it is:

$$D_{KL}(P || Q) = \left(-\sum_{x \in \mathcal{X}} P(x) \log Q(x) \right) - \left(-\sum_{x \in \mathcal{X}} P(x) \log P(x) \right)$$

It is defined this way if for all $x$ in $\mathcal{X}$, $Q(x) = 0$ implies $P(x) = 0$. If there exists an $x$ such that $Q(x) = 0$ and $P(x) > 0$, then $D_{KL}(P || Q) = +\infty$. 

When $P(x) = 0$, the corresponding term in the sum is defined to be zero, since:

$$\lim_{x \to 0^+} x \log x = 0$$

### Continuous case:

$$D_{KL}(P || Q) = \int_{-\infty}^{\infty} p(x) \log \frac{p(x)}{q(x)} dx$$

where $p(x)$ and $q(x)$ are the probability densities of $P$ and $Q$, respectively.

more generally, if $P$ and $Q$ are defined on a measurable space $\mathcal{X}$ and $P$ is absolutely continuous with respect to $Q$ ($P$ does not assign probabilities to something that is impossible for $Q$), then the KL divergence can be defined using the Radon-Nikodym derivative $\frac{dP}{dQ}$:

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} \log \left( \frac{dP(x)}{dQ(x)} \right) dP(x)$$

therefore, the KL divergence can be expressed in terms of the Radon-Nikodym derivative as:

$$dP(x)=r(x)dQ(x)$$

Where $r(x)$ is the density of $P$ with respect to $Q$. (This is only true if $P$ is absolutely continuous with respect to $Q$.)

Equivalently, we can express the KL divergence as:

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} \log \left( \frac{dP(x)}{dQ(x)} \right) dP(x)$$

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} \log \left( \frac{dP(x)}{dQ(x)} \right) r(x)dQ(x)$$

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} \log \left( \frac{dP(x)}{dQ(x)} \right) \frac{dP(x)}{dQ(x)}dQ(x)$$

2) $$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} \frac{dP(x)}{dQ(x)} \log \left( \frac{dP(x)}{dQ(x)} \right) dQ(x)$$

Which is the [entropy](/lectures/kl-divergence/05-adjacent-concepts/#entropy) of $P$ with respect to $Q$.

## Generalization to densities with respect to a common measure

If both $P$ and $Q$ can be described using ordinary densities with respect to the same underlying [measure](http://localhost:1313/lectures/kl-divergence/05-adjacent-concepts/#measure-measure-theory) $\mu$, is to say,

$$dP(x)=p(x)d\mu(x)$$ 
$$dQ(x)=q(x)d\mu(x)$$

then the KL divergence can be expressed as 3.

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} \frac{dP(x)}{dQ(x)} \log \left( \frac{dP(x)}{dQ(x)} \right) dQ(x)$$

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} dP(x) \log \left( \frac{dP(x)}{dQ(x)} \right)$$

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} p(x)d\mu(x) \log \left( \frac{p(x)d\mu(x)}{q(x)d\mu(x)} \right)$$

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} p(x)d\mu(x) \log \left( \frac{p(x)}{q(x)} \right)$$


3) $$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} p(x) \log \left( \frac{p(x)}{q(x)} \right) d\mu(x)$$







