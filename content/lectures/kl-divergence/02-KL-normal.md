---
title: "KL Divergence Between Two Multivariate Normal Distributions"
date: 2026-01-20T18:28:37-06:00
draft: false
type: "document"
description: "Derivation of the Kullback-Leibler divergence between two multivariate normal distributions"
showDescription: false
weight: 2
---

Given two multivariate normal distributions in $\mathbb{R}^k$:

$$P = \mathcal{N}(\mu_0, \Sigma_0), Q = \mathcal{N}(\mu_1, \Sigma_1)$$

Where:

- $\mu_0, \mu_1 \in \mathbb{R}^k$ are the mean vectors
- $\Sigma_0, \Sigma_1 \in \mathbb{R}^{k \times k}$ are symmetric positive definite covariance matrices

The probability density functions are given by:

$$p(x) = \frac{1}{(2\pi)^{k/2} |\Sigma_0|^{1/2}} \exp\left( -\frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) \right)$$

$$q(x) = \frac{1}{(2\pi)^{k/2} |\Sigma_1|^{1/2}} \exp\left( -\frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1) \right)$$

We derive the Kullback-Leibler divergence $D_{KL}(P || Q)$ where $\mathcal{X} = \mathbb{R}^k$.

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} p(x) \ln \left( \frac{p(x)}{q(x)} \right) d\mu(x)$$

where $d\mu(x) = dx$ is the $k$-dimensional Lebesgue measure.

**Remark**: Throughout this derivation, $\ln$ denotes the natural logarithm (base $e$), which is standard in information theory and statistics. The resulting KL divergence is measured in nats. An alternative formulation using $\log_2$ would yield measurements in bits.

Using the logarithm property $\ln(a/b) = \ln(a) - \ln(b)$, we can split this into:

$$D_{KL}(P || Q) = \int_{x \in \mathcal{X}} p(x)\left( \ln \left( p(x) \right)-\ln \left( q(x) \right) \right) dx$$

## Step 1: Computing the log-densities

We compute $\ln \left( p(x) \right)$ and $\ln \left( q(x) \right)$.

**Finding $\ln \left( p(x) \right)$:**

$$\ln \left( p(x) \right)=\ln \left( \frac{1}{(2\pi)^{k/2} |\Sigma_0|^{1/2}} \exp\left( -\frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) \right) \right)$$

Applying the logarithm properties $\ln(ab) = \ln(a) + \ln(b)$ and $\ln(\exp(x)) = x$, we obtain:

$$\ln \left( p(x) \right)= -\frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) -\ln \left( (2\pi)^{k/2} |\Sigma_0|^{1/2} \right)$$

Applying $\ln(ab) = \ln(a) + \ln(b)$:

$$\ln \left( p(x) \right)= -\frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) -\ln \left( (2\pi)^{k/2}\right) -\ln \left(|\Sigma_0|^{1/2} \right)$$

Applying the logarithm property $\ln(a^b) = b\ln(a)$:

$$\ln \left( p(x) \right)= -\frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) -\frac{k}{2}\ln \left( 2\pi\right) -\frac{1}{2}\ln \left(|\Sigma_0| \right)$$

**Finding $\ln \left( q(x) \right)$:**

Following the same procedure with the density $q(x)$:

$$\ln \left( q(x) \right)= -\frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1) -\frac{k}{2}\ln \left( 2\pi\right) -\frac{1}{2}\ln \left(|\Sigma_1| \right)$$

## Step 2: Computing the log-ratio

We compute the difference $\ln \left( p(x) \right)-\ln \left( q(x) \right)$.

Substituting the expressions derived above:

$$
\begin{aligned}
\ln \left( p(x) \right)-\ln \left( q(x) \right) &= \left(-\frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) -\frac{k}{2}\ln \left( 2\pi\right) -\frac{1}{2}\ln \left(|\Sigma_0| \right)\right) \\\\
&- \left(-\frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1) -\frac{k}{2}\ln \left( 2\pi\right) -\frac{1}{2}\ln \left(|\Sigma_1| \right)\right)
\end{aligned}
$$

Distributing the negative sign:

$$
\begin{aligned}
\ln \left( p(x) \right)-\ln \left( q(x) \right) &= \left(-\frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) -\frac{k}{2}\ln \left( 2\pi\right) -\frac{1}{2}\ln \left(|\Sigma_0| \right)\right) \\\\
&+ \left(\frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1) +\frac{k}{2}\ln \left( 2\pi\right) +\frac{1}{2}\ln \left(|\Sigma_1| \right)\right)
\end{aligned}
$$

Observing that the $\frac{k}{2}\ln(2\pi)$ terms cancel, we obtain:

$$
\begin{aligned}
\ln \left( p(x) \right)-\ln \left( q(x) \right) = &-\frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) \\\\
&+\frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1) \\\\
&+\frac{1}{2}\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right)
\end{aligned}
$$

## Step 3: Substituting into the KL divergence integral

Substituting this expression into the definition of the KL divergence:

$$
\begin{aligned}
D_{KL}(P || Q) = \int_{x \in \mathcal{X}} p(x)\left( -\frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) \\\\
+\frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1) \\\\
+\frac{1}{2}\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right)\right) dx
\end{aligned}
$$

Distributing $p(x)$ inside:

$$
\begin{aligned}
D_{KL}(P || Q) = \int_{x \in \mathcal{X}}
&\quad - p(x) \frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) \\\\
&\quad + p(x) \frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1) \\\\
&\quad + p(x) \frac{1}{2}\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right) dx
\end{aligned}
$$

## Step 4: Decomposition into three integrals

By linearity of integration, we decompose this into three integrals:

$$
\begin{aligned}
D_{KL}(P || Q) =
&\quad - \int_{x \in \mathcal{X}} p(x) \frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) dx \\\\
&\quad + \int_{x \in \mathcal{X}} p(x) \frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1) dx \\\\
&\quad + \int_{x \in \mathcal{X}} p(x) \frac{1}{2}\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right) dx
\end{aligned}
$$

We define:
\begin{align*}
I_1 &= \int_{x \in \mathcal{X}} p(x) \frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) \, dx \\\\
I_2 &= \int_{x \in \mathcal{X}} p(x) \frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1) \, dx \\\\
I_3 &= \int_{x \in \mathcal{X}} p(x) \frac{1}{2}\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right) \, dx
\end{align*}

Thus, $D_{KL}(P || Q) = -I_1 + I_2 + I_3$.

We now evaluate each integral separately.

### Integral $I_1$: Expected quadratic form under $P$ with matrix $\Sigma_0^{-1}$

$$I_1 = \int_{x \in \mathcal{X}} p(x) \frac{1}{2} (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) dx$$

Factoring out the constant $\frac{1}{2}$:

$$I_1 = \frac{1}{2} \int_{x \in \mathcal{X}} p(x) (x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0) dx$$

**Application of the trace operator:** Since $(x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0)$ is a scalar, it equals its trace:

$$(x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0)=\text{tr}[(x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0)]$$

Using the cyclic property of trace, $\text{tr}(ABC) = \text{tr}(CAB)$:

$$(x - \mu_0)^T \Sigma_0^{-1} (x - \mu_0)=\text{tr}\left[\Sigma_0^{-1} (x - \mu_0)(x - \mu_0)^T\right]$$

Substituting back:

$$I_1 = \frac{1}{2} \int_{x \in \mathcal{X}} p(x) \text{tr}\left[\Sigma_0^{-1} (x - \mu_0)(x - \mu_0)^T\right] dx$$

Since $\Sigma_0^{-1}$ is independent of $x$, the linearity of the trace operator and Fubini's theorem allow us to interchange the trace and integral:

$$I_1 = \frac{1}{2} \text{tr}\left[\Sigma_0^{-1} \int_{x \in \mathcal{X}} p(x) (x - \mu_0)(x - \mu_0)^T dx\right]$$

The integral $\int_{x \in \mathcal{X}} p(x) (x - \mu_0)(x - \mu_0)^T \, dx$ is, by definition, the covariance matrix of $P$, which is $\Sigma_0$:

$$I_1 = \frac{1}{2} \text{tr}\left[\Sigma_0^{-1} \Sigma_0\right]$$

Since $\Sigma_0^{-1} \Sigma_0 = I_k$ (the identity matrix), and $\text{tr}(I_k) = k$:

$$I_1 = \frac{k}{2}$$

### Integral $I_2$: Expected quadratic form under $P$ with matrix $\Sigma_1^{-1}$


$$I_2 = \int_{x \in \mathbb{R}^k} \frac{1}{2} (x - \mu_1)^T \Sigma_1^{-1} (x - \mu_1)  p(x)  \, dx$$

This integral requires more careful treatment since the quadratic form is centered at $\mu_1$ while the probability measure is centered at $\mu_0$.

**Strategy:** We express $(x - \mu_1)$ in terms of $(x - \mu_0)$ and the difference $(\mu_0 - \mu_1)$:

$$x - \mu_1 = (x - \mu_0) + (\mu_0 - \mu_1)$$

Substituting:

$$I_2 = \int_{x \in \mathbb{R}^k} \frac{1}{2} ((x - \mu_0) + (\mu_0 - \mu_1))^T \Sigma_1^{-1} ((x - \mu_0) + (\mu_0 - \mu_1))  p(x)  dx$$

Expanding the quadratic form using the algebraic identity $(a+b)^T C (a+b) = a^T C a + a^T C b + b^T C a + b^T C b$ and noting that $a^T C b = b^T C a$ for symmetric matrices:

$$I_2 = \int_{x \in \mathbb{R}^k} p(x)  \frac{1}{2} \left( (x - \mu_0)^T \Sigma_1^{-1} (x - \mu_0) \\\\
+2(\mu_0 - \mu_1)^T \Sigma_1^{-1} (x - \mu_0) \\\\
+(\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1) \right)  dx$$

Using linearity of integration, we split this into three sub-integrals:
$$
\begin{aligned}
I_2 = \int_{x \in \mathbb{R}^k} p(x) \frac{1}{2}  (x - \mu_0)^T \Sigma_1^{-1} (x - \mu_0) dx \\\\
+\int_{x \in \mathbb{R}^k} p(x) \frac{1}{2} \cdot 2(\mu_0 - \mu_1)^T \Sigma_1^{-1} (x - \mu_0) dx \\\\
+\int_{x \in \mathbb{R}^k} p(x) \frac{1}{2} (\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1)  dx
\end{aligned}
$$

We define:
\begin{align*}
I_{2.1} &= \int_{x \in \mathbb{R}^k} p(x) \frac{1}{2}  (x - \mu_0)^T \Sigma_1^{-1} (x - \mu_0) \, dx \\\\
I_{2.2} &= \int_{x \in \mathbb{R}^k} p(x) \frac{1}{2} \cdot 2(\mu_0 - \mu_1)^T \Sigma_1^{-1} (x - \mu_0) \, dx \\\\
I_{2.3} &= \int_{x \in \mathbb{R}^k} p(x) \frac{1}{2} (\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1) \, dx
\end{align*}

Thus, $I_2 = I_{2.1} + I_{2.2} + I_{2.3}$.

**Sub-integral $I_{2.1}$:** Quadratic form with $\Sigma_1^{-1}$
$$I_{2.1} = \int_{x \in \mathbb{R}^k} p(x) \frac{1}{2}  (x - \mu_0)^T \Sigma_1^{-1} (x - \mu_0) dx $$

Applying the trace operator as in the evaluation of $I_1$:

$$ I_{2.1} = \frac{1}{2} \int_{x \in \mathbb{R}^k} p(x)  \text{tr}[ \Sigma_1^{-1} (x - \mu_0) (x - \mu_0)^T]dx $$

Interchanging the trace and integral:

$$ I_{2.1} = \frac{1}{2} \text{tr}\left[\Sigma_1^{-1} \int_{x \in \mathbb{R}^k} p(x) (x - \mu_0) (x - \mu_0)^T dx\right] $$

The integral is again the covariance $\Sigma_0$:

$$ I_{2.1} = \frac{1}{2} \text{tr}[\Sigma_1^{-1} \Sigma_0] $$

**Sub-integral $I_{2.2}$:** Cross term

$$I_{2.2} = \int_{x \in \mathbb{R}^k} p(x) \frac{1}{2} \cdot 2(\mu_0 - \mu_1)^T \Sigma_1^{-1} (x - \mu_0) dx $$

Factoring out terms independent of $x$:

$$I_{2.2} = (\mu_0 - \mu_1)^T \Sigma_1^{-1} \int_{x \in \mathbb{R}^k} p(x) (x - \mu_0) \, dx $$

Since $\mathbb{E}_P[x] = \mu_0$, 

We have $\int_{x \in \mathbb{R}^k} p(x) (x - \mu_0) \, dx = \mathbb{E}_P[x - \mu_0] = 0$. Therefore:

$$I_{2.2} = 0$$

**Sub-integral $I_{2.3}$:** Constant term

$$I_{2.3} = \int_{x \in \mathbb{R}^k} p(x) \frac{1}{2} (\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1)  dx $$

Since the integrand is independent of $x$, we have:

$$I_{2.3} = \frac{1}{2}(\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1) \int_{x \in \mathbb{R}^k} p(x) \, dx $$

As $p(x)$ is a probability density function, $\int_{x \in \mathbb{R}^k} p(x) \, dx = 1$, yielding:

$$I_{2.3} = \frac{1}{2}(\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1) $$

**Combining sub-integrals for $I_2$:**

Adding the three sub-integrals:

$$I_2 = I_{2.1} + I_{2.2} + I_{2.3}$$

$$I_2 = \frac{1}{2} \text{tr}[\Sigma_1^{-1} \Sigma_0] + 0 + \frac{1}{2}(\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1) $$

Therefore:

$$I_2 = \frac{1}{2} \text{tr}[\Sigma_1^{-1} \Sigma_0] + \frac{1}{2}(\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1) $$

### Integral $I_3$: Logarithm of determinant ratio


$$I_3 = \int_{x \in \mathcal{X}} p(x) \frac{1}{2}\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right) dx$$

Since the integrand is independent of $x$:

$$I_3 = \frac{1}{2}\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right)\int_{x \in \mathcal{X}} p(x) \, dx$$

As $\int_{x \in \mathcal{X}} p(x) \, dx = 1$:

$$I_3 = \frac{1}{2}\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right)$$

## Step 5: Final result

We combine $I_1$, $I_2$, and $I_3$ to obtain the final expression. Recall:

$$D_{KL}(P || Q) = -I_1 + I_2 + I_3$$

Substituting the values:

$$D_{KL}(P||Q)=-\frac{k}{2}+\frac{1}{2} \text{tr}[\Sigma_1^{-1} \Sigma_0] + \frac{1}{2}(\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1)+\frac{1}{2}\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right)$$

Factoring out $\frac{1}{2}$:

$$D_{KL}(P||Q)=\frac{1}{2}\left[-k+ \text{tr}[\Sigma_1^{-1} \Sigma_0] + (\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1)+\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right)\right]$$

Rearranging to obtain the standard form:

$$D_{KL}(P||Q)=\frac{1}{2}\left[\text{tr}[\Sigma_1^{-1} \Sigma_0] - k + (\mu_0 - \mu_1)^T \Sigma_1^{-1} (\mu_0 - \mu_1)+\ln \left(\frac{|\Sigma_1|}{|\Sigma_0|} \right)\right]$$
