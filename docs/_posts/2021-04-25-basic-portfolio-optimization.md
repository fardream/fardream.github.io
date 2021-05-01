---
layout: post
title:  "Portfolio Optimization Part 1: Basic Portfolio Optimization"
date:   2021-04-25 23:24:00 -0400
categories: portfolio optimization
---

## Conic Problem

In the end, all portfolio optimizations are reduced into a conic optimization problem like below:

$$\begin{align}
\operatorname{argmin}_{x}\quad& cx \\
\text{where} \quad & \mathbf{A} x \in B \\
\quad & \mathcal{Q}_i\left(x_{i,0},x_{i,1},\cdots\right), \mathcal{Q}_{r,i}\left(x_{i,0},x_{i,1},x_{i,2},\cdots\right)
\end{align}$$

where $$\mathcal{Q}\left(x_{0},x_{1},\cdots\right)$$ is a cone,

$$\begin{align}
x_0^2 \geq \sum_{i=1}^n x_i^2
\end{align}$$

and $$\mathcal{Q}_{r}\left(x_{0},x_{1},x_{2},\cdots\right)$$ is a rotated cone,

$$\begin{align}
2x_0x_1 \geq \sum_{i=2}^n x_i^2
\end{align}$$

It should be noted that the objective function is always linear, and the problem is minimizing - although it doesn't matter if the problem is minimizing or maximizing, sticking with one direction help consistency.

## Construct Conic Problem

The next natural step is to construct conic problems from various terms. This is not an exhaustive list.

### Linear

The simplest form of a term in the objective function is simply linear combination of some $$x$$. For example, alpha in the book is a linear term - do note, the weight before alpha should be negative.

### Single Side and Double Side

There are some objectives that only take non-zero values when the variables are positive or negative. For example, borrow costs for short positions are only in effect when positions are negative (short).

For positive site, the following transformation can be used:

$$\begin{align}
x^+ & \geq 0 \\
x^+ & \geq x
\end{align}$$

Similarly, for negative side, the following transformation can be used

$$\begin{align}
x^- & \geq 0 \\
x^- & \geq -x
\end{align}$$

Lastly, absolute values can be obtained by

$$\begin{align}
v & \geq 0 \\
v & \geq -x \\
v & \geq x \\
\end{align}$$

### Quadratic terms

Only minimization or upper bound can be put on for quadratic terms

$$\begin{align}
2cx_{\sigma^2} = \sigma^2 \geq\mathbf{x}^T\mathbf{A}\mathbf{x}
\end{align}$$

Where $$c$$ is a constant (generally $$1/2$$). $$\mathbf{A}$$ must be **positive semi-definite**.
Replace $$\mathbf{A}$$ with $$\mathbf{A}/2+\mathbf{A}^T/2$$ if it is not symmetric.

$$\mathbf{A}$$ can be Cholesky decomposed given it's positive semi-defnite and symmetric. Let $$\mathbf{A}=\mathbf{L}\mathbf{L}^T$$, and we can introduce $$\mathbf{l}$$

$$\begin{align}
\mathbf{l}=\mathbf{L}^T\mathbf{x}
\end{align}$$

Where $$\mathbf{l}$$ goes into the (rotated) cone.

### Three Halves and Five Thirds

Below are taken directly from [MOSEK documentation](https://www.mosek.com/documentation/).

#### Three Halves

Introducing following two rotated cones.

$$\begin{align}
\mathcal{Q}\left(m,s,x\right), \mathcal{Q}\left(1/8,x,s\right)
\end{align}$$

where $$m$$ is the three halves term.

#### Five Thirds

Introducing following three rotated cones.

$$\begin{align}
\mathcal{Q}\left(m,n,x\right), \mathcal{Q}\left(1/8,p,n\right), \mathcal{Q}\left(n,x,p\right)
\end{align}$$

Similary, $$m$$ is the five thirds term.

## Simple Mean Variance Optimization

For very simple mean variance optimization

$$\begin{align}
-\mathbf{\alpha}^T\mathbf{w} + \lambda \mathbf{w}^T\mathbf{\Sigma}\mathbf{w}
\end{align}$$

where

- $$\mathbf{\alpha}$$ is the alpha,
- $$\mathbf{w}$$ is dollar portfolio holdings,
- $$\mathbf{\Sigma}$$ is covariance matrix,
- $$\lambda$$ is the risk aversion coefficient.

Following the above formulations, the conic problem is

$$\begin{align}
\operatorname{argmin}_{x_\alpha,x_\sigma,\mathbf{l},\mathbf{w}} & -x_\alpha + \lambda x_\sigma \\
 & x_\alpha -\mathbf{\alpha}^T\mathbf{w} = 0 \\
 & \mathbf{l} - \mathbf{L}^T \mathbf{w} = \mathbf{0} \\
 & \mathcal{Q}_r\left(1/2, x_\sigma, \mathbf{l}\right)
\end{align}$$

where $$\mathbf{\Sigma}=\mathbf{L}\mathbf{L}^T$$.

Transaction cost can be introduced by adding additional terms and constraints.
