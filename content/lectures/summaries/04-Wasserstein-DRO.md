---
title: "Wasserstein DRO"
date: 2026-01-20T18:28:37-06:00
draft: false
type: "document"
description: "Wasserstein Distributionally Robust Optimization, Theory and Applications in Machine Learning - 
Daniel Kuhn"
showDescription: true
weight: 1
---

[see resource](https://www.youtube.com/watch?v=Hhhtsi2tKlI)


## Decision-Making under Uncertainty

We are going to study a basic stochastic optimization problem where:

Each decision corresponds to a loss function $\ell(\xi)$.

Where $\xi$ is random vector that catches all problem dependent random variables and follows a distribution $\mathbb{P}$

And $\mathcal{L}$ is the set of all loss functions or admissible decisions.


Then we can define the Risk of a fixed loss function as the expected loss under the distribution $\mathbb{P}$:

$$\mathcal{R}(\mathbb{P},\ell) = \mathbb{E}^\mathbb{P}[\ell(\xi)]$$

And the Optimal Risk as the infimum of the risk over all admissible loss functions.

$$\mathcal{R}(\mathbb{P},\mathcal{L}) =\inf_{\ell \in \mathcal{L}} \mathbb{E}^\mathbb{P}[\ell(\xi)]$$

A very fundamental problem is that in most applications we are not given the distribution $P$ which we need to evaluate

So we are going to asume we are given limited structure and statistical information about this distribution $\mathbb{P}$.

Available Information:

- Structural: $\mathbb{P}$ supported on $\Xi \subseteq \mathbb{R}^m$
The set $\Xi$ that supports $\mathbb{P}$

- Statistical: $\hat{\xi_1}, \hat{\xi_2}, ... , \hat{\xi_N} \sim \mathbb{P}^N$
$N$ independent samples of $\mathbb{P}$ 

Nominal problem:

we can take the training samples $\hat{\xi_n}$ to estimate a nominal distribution $\hat{\mathbb{P}}$

and use it to calculate the Nominal $\mathcal{R}(\hat{\mathbb{P}},\ell)$ and the optimal nominal risk $\mathcal{R}(\hat{\mathbb{P}},\mathcal{L}) $

Non parametric estimators: 

Empirical distribution $\hat{\mathbb{P}}_\mathbb{N}=\frac{1}{N} \sum_{i = 1}^N \delta_{\hat{\xi}_i}$

in the absence of any structural information a very convinient chouce is to use empirical distributions

parametric estimators: 

Elliptical distribution $\mathbb{\hat{P}_N}=\mathcal{E}_g(\hat{\mu}_N,\hat{\Sigma}_N)$

if we have structural information is convenient to use elliptical distributions, that correspond to a density generator so the distribution is uniquely determined my the mean vector and the covariance matrix and this are the only  quantities that need to be estimated from the data. (with MLE for example)

even if we use the best estimators the nominal distribution will differ form the unkown true distribution. thus if we use it in the risk evaluation and decition problems we will inherit the estimation errors in the nominal distribution.

an importan question is how sensitive is the risk with respect to misspecifications of the distributions. to answer this we have to agree how to measure estimation errors in the input distribution. 

A good way to do this is by using the Wasserstein distance. there are diferent kinds of this wasserstine distance parametrized by a parameter $p \geq 1$

$$W_p(Q,Q')=(\inf_{\pi \in \Pi(Q,Q')}=\int_{\mathbb{R}^m\times \mathbb{R}^n}||\xi-\xi'||^p\Pi(d\xi, d\xi'))\frac{1}{p}$$

$\Pi(A, B)$ mass moved from source region $A$ to target region $B$

$||\xi-\xi'||^p$ price paid for moving mass from $\xi$ to $\xi'$


### Stability theory

Asume:

- $\ell(\xi)$ is Lipschitz continuous with $\text{Lip}(\ell)=L$
- the estimation error in $\mathbb{\hat{P}}_N$ is small, $W_1(\mathbb{\hat{P}}_N, \mathbb{P})\leq \varepsilon$

Question:

Whow big is the estimation error in $\mathcal{R}(\mathbb{\hat{P}}_N,\ell)$

K-R Theorem
$$|\mathcal{R}(\mathbb{\hat{P}}_N,\ell)-\mathcal{R}(\mathbb{P},\ell)|=L \cdot \left|\mathbb{E}^{\mathbb{\hat{P}}_N}\left[\frac{\ell(\xi)}{L}\right]-\mathbb{E}^{\mathbb{P}}\left[\frac{\ell(\xi)}{L}\right]\right|$$

$$\leq L \cdot W_1(\mathbb{\hat{P}}_N, \mathbb{P})\leq L \cdot \varepsilon$$

The estimation error art most is amplified by $L$

a very similar calculation can be applied to all admisible loss functions


Asume:

- $\ell(\xi)$ is Lipschitz continuous with $\text{Lip}(\ell)\leq L \forall \ell \in \mathcal{L}$
- the estimation error in $\mathbb{\hat{P}}_N$ is small, $W_1(\mathbb{\hat{P}}_N, \mathbb{P})\leq \varepsilon$

Question:

Whow big is the estimation error in $\mathcal{R}(\mathbb{\hat{P}}_N,\mathcal{L})$

$$|\mathcal{R}(\mathbb{\hat{P}}_N,\mathcal{L})-\mathcal{R}(\mathbb{P},\mathcal{L})|\leq L \cdot \varepsilon$$


## DRO

Nominal risk: $\mathcal{R}(\mathbb{\hat{P}}_N,\ell)$

Optimal nominal risk: $\mathcal{R}(\mathbb{\hat{P}}_N,\mathcal{L})$

if we want to have low estimation errors in the risk we should find estimators that have small wasserstein distance to the unknown true distribution.

Unfortunetely no estimator is perfect and given a finite sample we cant have perfect estimators due to the kramer rao bounds. but once we have exhausted the posibilities of choosing a good estimator we can still improve the out of sample performance of our decisions by changing the optimization problem itself. 
One way to do this is to rubustify the risk evaluation in decision problems with respect to distributional uncertainty. With respect to all distributions Q where our estimator has an estimation error that is at most epsilon

Definition

we start by defining an ambiguity set

$$\mathbb{B}_{\varepsilon, p}=\left\{\mathbb{Q}\in\mathcal{P}(\Xi): W_p(\mathbb{Q},\mathbb{\hat{P}}_N)\leq \varepsilon\right\}$$

this is a subset of all possible distributions, that has a wasserstein distance from our estimate of a most epsilon

This can be seen as all the distributions obtainable from reshaping our estimating distribution with a cost of at most $ \varepsilon $ 

then we have

Worst-case risk 

$\mathcal{R}_{\varepsilon, p}(\mathbb{\hat{P}}_N, \ell)=\sup_{\mathbb{Q}\in \mathbb{B}_{\varepsilon, p}}\mathbb{E}^\mathbb{Q} [ \ell(\xi) ]$

worst case optimal risk $\mathcal{R}_{\varepsilon, p}(\mathbb{\hat{P}}_N, \mathcal{L})= \inf_{\ell \in \mathcal{L}} \mathcal{R}_{\varepsilon, p}(\mathbb{\hat{P}}_N, \ell) $

The problem we are trying to solve id basically a zero sum game where the decision maker first picks a decision or loss function and then a nature chooses a distribution that will inflict the most damage to our decision. then epsilon is the maximum estimation error against wich we seek protection.

Three simple bounds on the worst case expectation

robust lower bound (any p)

this is an optimistic lowerbound

theorem if $\mathbb{\hat{P}}_N$ is the empirical distribution, then:

$$\mathcal{R}_{\varepsilon, p}(\mathbb{\hat{P}}_N, \ell) \geq \left\{                                                                                                              
\begin{array}{ll}
\sup & \frac{1}{N} \sum_{i=1}^{N} \ell(\hat{\xi}_i + \theta_i) \\                                                                                                                  
\text{s.t.} & \theta_i \in \mathbb{R}^m && \forall i \in [N] \\
 & \hat{\xi}_i+\theta_i \in \Xi && \forall i \in [N] \\
 & \frac{1}{N}\sum_{i=1}^N ||\theta_i||^p\leq \varepsilon^p
\end{array}
\right.$$

In this case we work with a discrete empirical distribution, we shift samples by a parameter theta

Lipschitz Regularizarion (any p)

this is the conservative upper bound

the worst case risk is bounded above by the lipschitz regularized nominal risk

$$\mathcal{R}_{\varepsilon, p}(\mathbb{\hat{P}}_N, \ell) \leq \mathcal{R}_{\varepsilon, p}(\mathbb{\hat{P}}_N, \ell)+\varepsilon \cdot \text{Lip}(\ell)$$

Gelbrich Bound (p=2)

annother conservative upperbound wich is more complex to construct.

it can be constructed by using lower bounds on the type 2 wasserstein distance and analytical lower bounds in the type 2 wasserstein distance between two distributions, which depend only on the parameters of the distribution.

Theorem: if $\mathbb{Q}\sim(\mu, \Sigma)$ and $\mathbb{Q}'\sim(\mu', \Sigma')$ then:

$$w_2(\mathbb{Q},\mathbb{Q}')\geq \sqrt{||\mu - \mu'||^2_2+\text{Tr}(\Sigma+\Sigma'-2(\Sigma^{\frac{1}{2}}\Sigma'\Sigma^{\frac{1}{2}})^\frac{1}{2})}$$

which we will denote:

$$d_G((\mu,\Sigma),(\mu',\Sigma'))$$

The bound is exact if $\mathbb{Q},\mathbb{Q}'$ are elliptical with the same generator. (like two normal distributions)

we can use this lower bound to build an upperbound in the worst case risk

first we introduce an uncertainty set in the space of mean covariance pairs.

$$\mathcal{U}_\varepsilon = \left\{(\mu, \Sigma) \in \mathbb{R}^m \times \mathbb{S}^m_+: d_G((\hat{\mu},\hat{\Sigma}),(\mu,\sigma)) \leq \varepsilon^2 \right\}$$

this uncertainty set is centered at the parameters of the nominal distribution.

we can use this uncertanty set to construct annother ambiguity set. 

$$T:\mathbb{Q}\mapsto (\mathbb{E}^\mathbb{Q}[\xi],\mathbb{E}^\mathbb{Q}[\xi\xi^T]-\mathbb{E}^\mathbb{Q}[\xi]\mathbb{E}^\mathbb{Q}[\xi]^T )$$

T is the projection that maps every distribution Q to it mean vector and its covariance matrix, this mapping because of geilbrich theoriem will map every distribution in $\mathbb{B}_{\varepsilon,2}(\hat{\mathbb{P}}_N)$ to $\mathcal{U}_\varepsilon(\hat{\mu},\hat{\Sigma})$ 

this means $T(\mathbb{B}_{\varepsilon,2}(\hat{\mathbb{P}}_N)) \subseteq \mathcal{U}_\varepsilon(\hat{\mu},\hat{\Sigma})$ and if $\hat{\mathbb{P}}_N$ is elliptical then $T(\mathbb{B}_{\varepsilon,2}(\hat{\mathbb{P}}_N)) = \mathcal{U}_\varepsilon(\hat{\mu},\hat{\Sigma})$ 

We are going to define a third ambiguity set, another family of distributions and call it **the Gelbrich hull**

$$\mathbb{G}_\varepsilon = T^{-1}(\mathcal{U}_\varepsilon(\hat{\mu},\hat{\Sigma}))$$

this is the preimage of the uncertainty set under the mean covariance projection T. this set contains all the distributions whose mean vector and covariance matrix resides within the uncertainty set.

its an outer aproximation of the wasertain bowl, if this hull contains some distribution Q then it will contain any distribution with the same  mean vector and covariance matrix as Q, so it can be much larger than the wasserstein bowl.

$\mathbb{B}_{\varepsilon,2}(\hat{\mathbb{P}}_N) \subseteq \mathbb{G}_\varepsilon(\hat{\mu},\hat{\Sigma})$ 

we can use this to construct an upperbound on the worst case risk just by replacing the waserstein bowl with the gelbrich hull in the definition of the worst case risk

theorem: if $\hat{\mathbb{P}}_N$ has mean $\hat{\mu}$ and covariance matrix $\hat{\Sigma}$, then

$$\mathcal{R}_{\varepsilon, 2}(\hat{\mathbb{P}}_N,\ell)\leq \sup_{\mathbb{Q}\in\mathbb{G}(\hat{\mu},\hat{\Sigma})}\mathbb{E^Q}[\ell(\xi)]$$

We will denote it as $\bar{\mathcal{R}}_\varepsilon(\hat{\mu},\hat{\Sigma}, \ell)$ for the rest of the document

### Efficient computation of the worst case risk

the main vehicle to derive tractable reformulations for the worst case risk evaluation problem is strong duality theorem which expreses the worst case risk (Wasserstein) as optimal value of a univariate convex minimization problem.

theorem: if  $\ell$ is u.s.c. and integratable under $\hat{\mathbb{P}}_N$, then 

$$\mathcal{R}_{\varepsilon, p}(\hat{\mathbb{P}}_N, \ell)=\inf_{\gamma \geq 0}\mathbb{E}^{\hat{\mathbb{P}}_N}[\ell_\gamma(\xi)] + \gamma\varepsilon^p$$

where $\ell_\gamma(\xi)=\sup_{z\in \Xi} \ell(z)-\gamma ||z-\xi||^p$

the decision variable $\gamma$ is the dual variable of the wasserstein constraint, and act as a scaler.

and this  $\sup_{z\in \Xi} \ell(z)-\gamma ||z-\xi||^p$ is called moreoau envelope. 

This result was first derived for $p=1$ and for descrete nominal distributions in finite dimentional spaces, but was later extented to arbitrary p and arbutrary nominal distributions. 

this can be use to find exact tractable reformulations of the worst case risk evaluation problem in certain setings for certain loss functions, 

one example is when the loss fonction is piecewise concave. that means when the loss function can be expressed as the poinwise maximum of finately many concave functions. 

$\ell(\xi)= \max_j\ell_j(\xi)$ where each $\ell_j$ is proper, concave and u.s.c.

this is a weak assumption, many functions can be written like this.

this leads us to a first result

theorem: if $\Xi$ is convex, $\hat{\mathbb{P}}_N$ is the empirical distribution and $\ell(\xi)$ is piecewise concave, then:

$$
\begin{array}{ll}
\mathcal{R}_{\varepsilon, p}(\hat{\mathbb{P}}_N, \ell)= \\
\inf \gamma \varepsilon^p + \frac{1}{N} \sum_{i=1}^N s_i\\
s.t. \gamma \in \mathbb{R_+}, s_i \in \mathbb{R}, u_{ij} \in \mathbb{R^m}, v_{ij} \in \mathbb{R^m} & \forall i,j \\
[-\ell_j]^*(u_{ij}-v_{ij})+\sigma_\Xi(v_{ij})-u_{ij}^T \hat{\xi}_i + \varphi(q)\gamma ||\frac{u_{ij}}{\gamma}||^q_* \leq s_i & \forall i,j
\end{array}
$$

where $\frac{1}{p}+\frac{1}{q}=1$ and $\varphi(q)=\frac{(q-1)^{q-1}}{q^q}$

this is an optimization problem that involves some ausiliary decision variables that weve just picked up by dualizing the mureau envelope.

this problem is convex and it involves the convex conjugate of $-\ell_j$, the support function of $\Xi$ and the perspective of $||\cdot||_*^p$

dualizing this problem we obtain:

theorem: if $\Xi$ is convex, $\hat{\mathbb{P}}_N$ is the empirical distribution and $\ell(\xi)$ is piecewise concave, then:

$$
\begin{array}{ll}
\mathcal{R}_{\varepsilon, p}(\hat{\mathbb{P}}_N, \ell)= &
\max & \frac{1}{N}\sum_{i=1}^N\sum_{j=1}^J\alpha_{ij}\ell_j(\hat{\xi}_i+\frac{\theta_{ij}}{\alpha_{ij}}) \\
& s.t. & \alpha_{ij} \in \mathbb{R}_+, \theta_{ij} \in \mathbb{R}^m & \forall i \in[N], \forall j \in [J] \\
& & \hat{\xi}_i+\frac{\theta_{ij}}{\alpha_{ij}} \in \Xi & \forall i \in[N], \forall j \in [J] \\
& & \sum_{j=1}^J \alpha_{ij}=1 & \forall i \in[N] \\
& & \frac{1}{N}\sum_{i=1}^N\sum_{j=1}^J\alpha_{ij}||\frac{\theta_{ij}}{\alpha_{ij}}||^p \leq \varepsilon^p
\end{array}
$$

an special case is that, if the loss function is concave (J is 1) then this exact reformulation reduces exactly to the robust lower bound.
 this shows the robust lb is exact when we have a loss function that is concave in the uncertainty.

one can show this problem is always solvable, even when we cannot obtain the worst case distribution we can use a solution of this problem to construct a sequence of discrete distributions that asymptotically obtain the worst case.

Main takeaways:

- if $\ell$ is piecewise concave and $\hat{\mathbb{P}}_N$ empirical, the worst-case risk can be computed by convex optimization for any $p\in[1,\infty]$

$\Longrightarrow$ generalizes early results for $p=1$

- if $\ell$ is concave and $\hat{\mathbb{P}}_N$ empirical, the robust lower bound is exact; the dual convex program:
  - provides a finite reduction of the worst-case risk problems
  - is always solvable
  - can be used to find a worst-case distribution (may not exist for $p=1$, always exists for $p>1$)