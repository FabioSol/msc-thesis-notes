---
title: "KL Divergence Between Two Poisson Distributions"
date: 2026-01-20T18:28:37-06:00
draft: false
type: "document"
description: "Derivation of the Kullback-Leibler divergence between two Poisson distributions"
showDescription: false
weight: 4
---

Given two Poisson distributions $P$ and $Q$ with parameters $\lambda_p$ and $\lambda_q$, respectively, the KL divergence from $P$ to $Q$ can be calculated as follows:

$$D_{KL}(P || Q) = \sum_{x \in \mathcal{X}} P(x) \log \frac{P(x)}{Q(x)}$$

Since $P$ and $Q$ are Poisson distributions, we have:

$$P(x)=\frac{e^{-\lambda_p} \lambda_p^x}{x!}$$
$$Q(x)=\frac{e^{-\lambda_q} \lambda_q^x}{x!}$$

for $x \in \{0, 1, 2, \ldots\}$

Substituting these into the KL divergence formula, we get:

$$D_{KL}(P || Q) = \sum_{x \in \mathcal{X}} P(x) \log \frac{P(x)}{Q(x)}$$

$$D_{KL}(P || Q) = \sum_{x \in \mathcal{X}} P(x) \log \frac{\frac{e^{-\lambda_p} \lambda_p^x}{x!}}{\frac{e^{-\lambda_q} \lambda_q^x}{x!}}$$

$$D_{KL}(P || Q) = \sum_{x \in \mathcal{X}} P(x) \left[\log \frac{e^{-\lambda_p} \lambda_p^x}{e^{-\lambda_q} \lambda_q^x}\right]$$

$$D_{KL}(P || Q) = \sum_{x \in \mathcal{X}} P(x) \left[\log e^{-\lambda_p} + \log \lambda_p^x - \log e^{-\lambda_q}-\log \lambda_q^x \right]$$

$$D_{KL}(P || Q) = \sum_{x \in \mathcal{X}} P(x) \left[-\lambda_p+ x \log \lambda_p + \lambda_q-x\log \lambda_q \right]$$

$$D_{KL}(P || Q) = -\sum_{x \in \mathcal{X}} P(x) \lambda_p+ \sum_{x \in \mathcal{X}} P(x)x \log \lambda_p +\sum_{x \in \mathcal{X}} P(x) \lambda_q-\sum_{x \in \mathcal{X}} P(x)x\log \lambda_q $$

$$D_{KL}(P || Q) = -\lambda_p\sum_{x \in \mathcal{X}} P(x) + \log \lambda_p \sum_{x \in \mathcal{X}} P(x)x +\lambda_q\sum_{x \in \mathcal{X}} P(x) -\log \lambda_q \sum_{x \in \mathcal{X}} P(x)x$$

since $\sum_{x \in \mathcal{X}} P(x) = 1$ and $\sum_{x \in \mathcal{X}} P(x)x = \lambda_p$, we have:

$$D_{KL}(P || Q) = -\lambda_p + \lambda_p\log \lambda_p  +\lambda_q -\lambda_p\log \lambda_q $$

$$D_{KL}(P || Q) = \lambda_q-\lambda_p + \lambda_p(\log \lambda_p  -\log \lambda_q )$$

$$D_{KL}(P || Q) = \lambda_q-\lambda_p + \lambda_p(\log \frac{\lambda_p}{\lambda_q} )$$
