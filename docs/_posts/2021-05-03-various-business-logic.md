---
layout: post
title:  "Portfolio Optimization Part 6: Various Business Logics"
date:   2021-05-03 16:51:00 -0400
categories: portfolio optimization
---

Now let's turn attention to various business logics.

## Share Count

Uptil now, the discussion has been focusing on dollar values. The share count solution can be easily computed by dividing the dollar value with a price. There are several corner cases:

- **Consistent Price**. It is important to have a consistent price to avoid situations where dollar trade is positive but trade count is negative. For example, the price of the initial positions can be saved and used as the only price.
  - It also needs to be special careful around share split/reverse split, and/or spin offs. This problem is avoided when we optimize on dollar values instead of share counts.
- **Rounding**. The optimized positions may not be whole share positions, and rounding is needed to remove fractional shares.
- **Multiperiod price**. If there are predicted alpha in multiperiod set up, the price needs to be adjusted by the alpha.

## Small Trades/Positions

This is briefly touched on in integer programming related disucssions. To make the optimization simpler, the smaller positions and trades can be filtered out **after** the optimization. Both shares count and dollar value can be used as criteria. Be careful with following

- Small positions still need to be included in the reporting - filtering them out from optimization doesn't mean the positions are not there anymore.
- It may still be wise to let the small trades go through if they are fully liquidating positions.

## Minimum Volatility

Many portfolios are managed by targeting a specific risk or volatility number. However, it is impossible to make this a convex problem. One way to do this is adjust risk aversion coefficient $$\lambda$$ to get the desired number, however, it may be impossible to do if many constraints are present.

## Minimum Gross Market Value

Similarly many portfolios are managed by targeting a specific gross market value. This constraint is generally not a convex constraint either. However, it can be convex constraint if all the positions' directions are known before optimization, which basically reduces it to a linear constraint. However, it can also be achieved similarly through adjusting risk aversion coefficient.

## Soft Constraint

There may be a situation where some constraints are preferred to be met, but can be breached a little if the solution is infeasible. For example, the GMV of the book may not exceed 1 billion dollars - but 1.001 billion dollars GMV may also be fine. For a constraint on variable $$x \in \left[l,u\right]$$, define variable $$d$$, such that

$$\begin{align}
d & \geq 0 \\
d & \geq l - x \\
d & \geq x - u
\end{align}$$

The variable $$d$$ can be considered the out-of-bound value of $$x$$. Now $$d$$ can be used to used in objective terms to control the constraint.

The order of $$d$$ in objective term will determine how far out of bound $$x$$ can be. For example, linear objective term will grow slower than quadratic objective, and quadraple order may be as effective as a hard constraint.