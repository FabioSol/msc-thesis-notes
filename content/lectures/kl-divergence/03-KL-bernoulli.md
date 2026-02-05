---
title: "KL Divergence Between Two Bernoulli Distributions"
date: 2026-01-20T18:28:37-06:00
draft: false
type: "document"
description: "Derivation of the Kullback-Leibler divergence between two Bernoulli distributions"
showDescription: false
weight: 3
---

Given two Bernoulli distributions $P$ and $Q$ with parameters $p$ and $q$, respectively, the KL divergence from $P$ to $Q$ can be calculated as follows:

$$D_{KL}(P || Q) = \sum_{x \in \mathcal{X}} P(x) \log \frac{P(x)}{Q(x)}$$

Since $P$ and $Q$ are Bernoulli distributions, we have:

$$P(x)=p^x (1-p)^{1-x}$$
$$Q(x)=q^x (1-q)^{1-x}$$

for $x \in \{0, 1\}$.

Substituting these into the KL divergence formula, we get:

$$D_{KL}(P || Q) = \sum_{x \in \mathcal{X}} P(x) \log \frac{P(x)}{Q(x)}$$

$$D_{KL}(P || Q) = \sum_{x = 0}^1 (p^x (1-p)^{1-x}) \log \frac{p^x (1-p)^{1-x}}{q^x (1-q)^{1-x}}$$

$$D_{KL}(P || Q) =  (p^0 (1-p)^{1-0}) \log \frac{p^0 (1-p)^{1-0}}{q^0 (1-q)^{1-0}}+(p^1 (1-p)^{1-1}) \log \frac{p^1 (1-p)^{1-1}}{q^1 (1-q)^{1-1}}$$

$$D_{KL}(P || Q) =  (1-p) \log \frac{1-p}{1-q}+p \log \frac{p}{q}$$

$$D_{KL}(P || Q) = \log \frac{1-p}{1-q} - p \log \frac{1-p}{1-q}+p \log \frac{p}{q}$$

$$D_{KL}(P || Q) = \log \frac{1-p}{1-q} + p \log \frac{1-q}{1-p}+p \log \frac{p}{q}$$

$$D_{KL}(P || Q) = \log \frac{1-p}{1-q} + p (\log \frac{1-q}{1-p}+ \log \frac{p}{q})$$

$$D_{KL}(P || Q) = \log \frac{1-p}{1-q} + p \log \frac{p(1-q)}{q(1-p)}$$
