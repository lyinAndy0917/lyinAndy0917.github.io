---
title: "Unconstrained Optimization"
description: "An introduction to unconstrained optimization."
slug: "Unconstrained Optimization"
categories:
    - Optimization
---

# Unconstrained Optimization
$$ \min_x \quad f(x) $$
Assumption:
- $f(x)$ is twice differentiable
- $f(x)$ is convex. The hessian of the function is positive semi-definite. (This means the function is bowl-shaped)
- optimal solution $x^*$ exists
- Optimal condition $\nabla f(x^*) = 0$. This mean the gradient of the function at the optimal solution is zero.

## Convex Quadratic Function
$$ \begin{align} \min_x \quad & \frac{1}{2} x^T P x + q^T x + r \\
    \quad & \nabla f(x)= P x + q = 0
    \end{align} $$

- P is the hessian of the function
- q is the gradient of the function
- r is the constant term


**Notation Explained**
- P is the hessian of the function
- q is the gradient of the function
- r is the constant term 

**Q:** How do we came up with this quadratic function?  
**A:** It's from second order Taylor expansion.  
$$ f(x) = f(x_0) + \nabla f(x_0)^T (x - x_0) + \frac{1}{2} (x - x_0)^T \nabla^2 f(x_0) (x - x_0) $$ 

We could solve this problem by setting the gradient to zero.
**Q:** Why do we set the gradient to zero?
**A:** Because the gradient is zero at the optimal solution.

## Method:
### Gradient Descent
$$ x_{k+1} = x_k + t_k \Delta x_k $$
$$ \Delta x_k = - \nabla f(x_k) $$

1. choose a starting point $x_0$
2. repeat until convergence:
    - calculate the gradient $\nabla f(x_k)$
    - line search: find the step size $t_k$ that minimizes $f(x_k + t_k \Delta x_k)$
    - update $x_{k+1} = x_k + t_k \Delta x_k$
3. Stop when L2 norm of the gradient is less than a threshold. 
   $$ \nabla f(x_k) < \text{threshold} $$







 

