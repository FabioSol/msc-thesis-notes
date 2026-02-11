---
title: "KL Divergence Between Data and a Distribution"
date: 2026-01-20T18:28:37-06:00
draft: false
type: "document"
description: "Derivation of the Kullback-Leibler divergence between a data distribution and a model distribution"
showDescription: false
weight: 4
---


Consider the following scenario:

- **True distribution $P$**: The unknown distribution that generated our data
- **Observed data**: Samples $x_1, x_2, \ldots, x_n$ drawn i.i.d. from $P$
- **Parametric model $Q_\theta$**: A family of distributions parameterized by $\theta$

**Goal:** Find the parameter $\theta$ such that $Q_\theta$ best approximates $P$.

A natural objective is to minimize the KL divergence:

$$\theta^* = \arg\min_\theta D_{KL}(P \| Q_\theta)$$

Remembering the definition of KL divergence:

$$D_{KL}(P \| Q_\theta) = \mathbb{E}_{x \sim P}\left[\log \frac{P(x)}{Q_\theta(x)}\right]$$

we can rewrite this as:

$$D_{KL}(P \| Q_\theta) = \mathbb{E}_{x \sim P}[\log P(x)] - \mathbb{E}_{x \sim P}[\log Q_\theta(x)]$$

Then the optimization problem becomes:

$$\theta^* = \arg\min_\theta \left( \mathbb{E}_{x \sim P}[\log P(x)] - \mathbb{E}_{x \sim P}[\log Q_\theta(x)] \right)$$

The first term is the entropy of $P$, which does not depend on $\theta$. The second term is the cross-entropy between $P$ and $Q_\theta$ and it does depend on $\theta$.

This means that minimizing the KL divergence is equivalent to minimizing the cross-entropy:

$$\theta^* = \arg\min_\theta -\mathbb{E}_{x \sim P}[\log Q_\theta(x)]$$

an other way to express this is as the maximization of the negative cross-entropy also known as the log-likelihood:

$$\theta^* = \arg\max_\theta \mathbb{E}_{x \sim P}[\log Q_\theta(x)]$$

This is the same as maximizing the likelihood of the observed data under the model $Q_\theta$ since logarithm is a monotonic function.


