---
layout: post
title:  "Portfolio Optimization Part 5: Integer Programming Related Problems"
date:   2021-05-02 20:15:00 -0400
categories: portfolio optimization
---

## Integer Programming Based Problems

There are two types of problems that can be solved by integer programming:

1. Max number of non-zero variables - this can be either number of trades, or number of positions.
2. Minimum variable absolute value - this can be either trade or positions.

## Max Number of Non-zero Variables

This may be an often requested feature - many portfolio managers may not want the number of trades excessive.

Let $$v$$ be an indicator variable, which can only be either 0 or 1. In optimizers such as MOSEK, this type of variables is obtained by setting a variable to be integer and constrain its range to be $$\left[0,1\right]$$. Let $$x$$ be the variable that we want to control, and let $$L$$ be a sufficiently large constant value.

$$\begin{align}
x & \leq Lv \\
x & \geq -Lv
\end{align}$$

The number of non-zero variables is the summation of $$n=\sum_iv_i$$. Now $$n$$ can either be constrained on the upper bound or minimized.

## Minimum Variable Size

There may be a desire to avoid small trades or small positions (for example, it doesn't make much difference if two shares of 10 dollar stocks are traded for a billion dollar portfolio). This can be obtained by trimming the trades or positions after the optimization, which will be covered in a later part. However, it can be solved by optimizer.

Let $$a$$ be an indicator variable for $$x$$ being positive or not, let $$b$$ be an indicator variable for $$x$$ being negative or not. Let $$d$$ be the minimal absolute value, and $$L$$ be a sufficiently large constant, we have

$$\begin{align}
x & \geq da -bL \\
x & \leq -db + aL \\
& a + b   \leq 1 \\
& a + b  \geq 0
\end{align}$$

It can be seen from the constraint that $$a+b$$ is also an indicator variable, indicating if $$x$$ is non-zero or not.

## Avoid Integer Programming

The integer problems are theoretically solvable, but please try and avoid them as much as possible:

1. The above formulation will work for small number of variables, and the optimizer tends to be very slow to converge for large number of integer variables. You can adjust the converging criteria to help at the cost of accuracy.
1. It may be difficult to explain or pinpoint why optimizer chooses certain variables to be non-zero or not.
1. The solution may be non-unique or not very stable.